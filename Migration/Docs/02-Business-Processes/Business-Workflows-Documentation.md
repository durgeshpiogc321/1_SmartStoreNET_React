# SmartStore.NET - Business Workflows Documentation

## Overview
This document details the core business workflows identified in the SmartStore.NET e-commerce platform, serving as a foundation for React migration planning.

---

## 1. Order Processing Workflow

### 1.1 Order Placement Process
**Controllers**: `CheckoutController`, `OrderProcessingService`
**Key Components**: 
- `PlaceOrderResult PlaceOrder(ProcessPaymentRequest, Dictionary<string, string>)`
- Order validation and warnings system
- Payment workflow integration

**Workflow Steps**:
1. **Pre-Order Validation**
   - Customer authentication check
   - Shopping cart validation
   - Minimum order placement interval validation (`IsMinimumOrderPlacementIntervalValid`)
   - Order placement warnings collection (`GetOrderPlacementWarnings`)

2. **Order Details Calculation**
   ```csharp
   // Currency handling
   customerCurrencyCode = customer.GetAttribute<string>(SystemCustomerAttributeNames.CurrencyId);
   customerCurrencyRate = _currencyService.GetCurrencyById(currencyId).Rate;
   
   // Shipping calculations
   orderShippingTotalInclTax = _orderTotalCalculationService.GetShoppingCartShippingTotal(cart, true);
   orderShippingTotalExclTax = _orderTotalCalculationService.GetShoppingCartShippingTotal(cart, false);
   
   // Tax calculations
   orderTaxTotal = _orderTotalCalculationService.GetTaxTotal(cart, out taxRatesDictionary);
   ```

3. **Payment Processing**
   - Pre-payment workflow (`PreProcessPayment`)
   - Payment method validation
   - Payment processing (`ProcessPayment`)
   - Post-payment handling (`PostProcessPayment`)

4. **Order Creation**
   - Order entity creation with calculated totals
   - Order item creation for each cart item
   - Order notes and status assignment
   - Event publishing (`OrderPlacedEvent`)

### 1.2 Order Status Management
**Business Rules**:
- **Payment Status Flow**: `Pending → Authorized → Paid → Refunded/Voided`
- **Order Status Flow**: `Pending → Processing → Complete → Cancelled`
- **Shipping Status Flow**: `NotYetShipped → Shipped → Delivered`

**State Validation Logic**:
```csharp
// Capture validation
public virtual bool CanCapture(Order order)
{
    return (order.OrderStatus != OrderStatus.Cancelled && 
            order.OrderStatus != OrderStatus.Pending) &&
           (order.PaymentStatus == PaymentStatus.Authorized) &&
           _paymentService.SupportCapture(order.PaymentMethodSystemName);
}

// Payment marking validation
public virtual bool CanMarkOrderAsPaid(Order order)
{
    return order.OrderStatus != OrderStatus.Cancelled &&
           (order.PaymentStatus != PaymentStatus.Paid && 
            order.PaymentStatus != PaymentStatus.Refunded && 
            order.PaymentStatus != PaymentStatus.Voided);
}
```

### 1.3 Recurring Orders
**Components**: 
- Recurring payment validation
- Cycle calculation (`GetRecurringCycleInfo`)
- Automatic payment processing

---

## 2. Customer Management Workflow

### 2.1 Customer Registration Process
**Controllers**: `CustomerController`, `CustomerRegistrationService`
**Key Methods**: `RegisterCustomer(CustomerRegistrationRequest)`

**Registration Flow**:
1. **Input Validation**
   ```csharp
   if (request.Customer.IsSearchEngineAccount())
       result.AddError(T("Account.Register.Errors.CannotRegisterSearchEngine"));
   
   if (!request.Email.IsEmail())
       result.AddError(T("Account.Register.Errors.EmailIsNotValid"));
   ```

2. **Uniqueness Validation**
   - Email uniqueness check
   - Username uniqueness check (if applicable)
   - System account prevention

3. **Account Creation**
   ```csharp
   // Password handling
   switch (_customerSettings.DefaultPasswordFormat)
   {
       case PasswordFormat.Clear:
           request.Customer.Password = request.Password;
           break;
       case PasswordFormat.Encrypted:
           request.Customer.Password = _encryptionService.EncryptText(request.Password);
           break;
       case PasswordFormat.Hashed:
           var saltKey = _encryptionService.CreateSaltKey(5);
           request.Customer.PasswordSalt = saltKey;
           request.Customer.Password = _encryptionService.CreatePasswordHash(request.Password, saltKey);
           break;
   }
   ```

4. **Role Assignment**
   - Remove from 'Guests' role
   - Add to 'Registered' role
   - Apply custom registration role if configured

5. **Post-Registration Actions**
   - Reward points assignment
   - Newsletter subscription (if opted in)
   - Account activation email (if required)
   - Event publishing (`CustomerRegisteredEvent`)

### 2.2 Authentication Workflow
**Components**: `FormsAuthenticationService`, `CustomerRegistrationService.ValidateCustomer`

**Login Process**:
1. **Credential Validation**
   ```csharp
   public virtual bool ValidateCustomer(string usernameOrEmail, string password)
   {
       var customer = _customerSettings.CustomerLoginType == CustomerLoginType.Email 
           ? _customerService.GetCustomerByEmail(usernameOrEmail)
           : _customerService.GetCustomerByUsername(usernameOrEmail);
           
       // Password verification based on format
       switch (customer.PasswordFormat)
       {
           case PasswordFormat.Hashed:
               pwd = _encryptionService.CreatePasswordHash(password, customer.PasswordSalt);
               break;
           // ... other formats
       }
       
       bool isValid = pwd == customer.Password;
       if (isValid)
       {
           customer.LastLoginDateUtc = DateTime.UtcNow;
           _customerService.UpdateCustomer(customer);
       }
       return isValid;
   }
   ```

2. **Session Management**
   - Shopping cart migration from anonymous to registered
   - Authentication cookie creation
   - Activity logging
   - Event publishing (`CustomerLoggedInEvent`)

### 2.3 Account Activation
**Process**: Token-based email verification
```csharp
public ActionResult AccountActivation(string token, string email)
{
    var customer = _customerService.GetCustomerByEmail(email);
    var cToken = customer.GetAttribute<string>(SystemCustomerAttributeNames.AccountActivationToken);
    
    if (cToken.Equals(token, StringComparison.InvariantCultureIgnoreCase))
    {
        customer.Active = true;
        _customerService.UpdateCustomer(customer);
        _genericAttributeService.SaveAttribute(customer, SystemCustomerAttributeNames.AccountActivationToken, "");
        Services.MessageFactory.SendCustomerWelcomeMessage(customer, _workContext.WorkingLanguage.Id);
    }
}
```

---

## 3. Shopping Cart Workflow

### 3.1 Cart Management Process
**Controllers**: `ShoppingCartController`, `ShoppingCartService`
**Key Methods**: `AddToCart`, `UpdateShoppingCartItem`, `DeleteShoppingCartItem`

**Add to Cart Flow**:
1. **Pre-Addition Validation**
   ```csharp
   // Quantity validation
   if (quantity <= 0)
       warnings.Add(T("ShoppingCart.QuantityShouldPositive"));
   
   // Permission checks
   if (cartType == ShoppingCartType.ShoppingCart && 
       !_permissionService.Authorize(Permissions.Cart.AccessShoppingCart, customer))
       warnings.Add(T("ShoppingCart.IsDisabled"));
   
   // Maximum items validation
   if (cartType == ShoppingCartType.ShoppingCart && 
       cart.Count >= _shoppingCartSettings.MaximumShoppingCartItems)
       warnings.Add(T("ShoppingCart.MaximumShoppingCartItems"));
   ```

2. **Product Validation**
   - Product availability check
   - Stock quantity validation
   - Product attributes validation
   - Price validation (for customer-entered prices)

3. **Cart Item Processing**
   ```csharp
   // Check for existing item
   var existingCartItem = FindShoppingCartItemInTheCart(cart, cartType, product, selectedAttributes);
   
   if (existingCartItem != null && !_shoppingCartSettings.AddProductsToBasketInSinglePositions)
   {
       // Update existing item quantity
       int newQuantity = existingCartItem.Item.Quantity + quantity;
       existingCartItem.Item.Quantity = newQuantity;
       existingCartItem.Item.UpdatedOnUtc = DateTime.UtcNow;
   }
   else
   {
       // Create new cart item
       var cartItem = new ShoppingCartItem
       {
           ShoppingCartType = cartType,
           StoreId = storeId,
           Product = product,
           AttributesXml = selectedAttributes,
           CustomerEnteredPrice = customerEnteredPrice,
           Quantity = quantity,
           ParentItemId = null
       };
       customer.ShoppingCartItems.Add(cartItem);
   }
   ```

4. **Post-Addition Processing**
   - Checkout data reset
   - Required products addition (if enabled)
   - Bundle item handling

### 3.2 Cart Calculations
**Price Calculation Components**:
```csharp
// Unit price calculation
public virtual decimal GetUnitPrice(OrganizedShoppingCartItem shoppingCartItem, bool includeDiscounts)
{
    var customer = shoppingCartItem.Item.Customer;
    return GetUnitPrice(shoppingCartItem.Item.Product, customer, ShoppingCartType.ShoppingCart,
        shoppingCartItem.Item.Quantity, shoppingCartItem.Item.AttributesXml, 
        shoppingCartItem.Item.CustomerEnteredPrice, includeDiscounts);
}

// Discount calculation
public virtual decimal GetDiscountAmount(OrganizedShoppingCartItem shoppingCartItem, out Discount appliedDiscount)
{
    var product = shoppingCartItem.Item.Product;
    var quantity = shoppingCartItem.Item.Quantity;
    var customer = shoppingCartItem.Item.Customer;
    
    var attributesTotalPrice = decimal.Zero;
    var pvaValues = _productAttributeParser.ParseProductVariantAttributeValues(shoppingCartItem.Item.AttributesXml);
    
    foreach (var pvaValue in pvaValues)
    {
        attributesTotalPrice += GetProductVariantAttributeValuePriceAdjustment(pvaValue, product, customer, null, quantity);
    }
    
    var productDiscountAmount = GetDiscountAmount(product, customer, attributesTotalPrice, quantity, out appliedDiscount);
    totalDiscountAmount = productDiscountAmount * quantity;
}
```

### 3.3 Wishlist Management
**Components**: Wishlist-specific cart operations
- Move between cart and wishlist
- Wishlist sharing via email
- Guest wishlist handling

---

## 4. Product Catalog Workflow

### 4.1 Product Browsing Process
**Controllers**: `CatalogController`, `ProductController`
**Key Components**: `CatalogHelper`, `ProductSummaryMappingSettings`

**Category Browsing Flow**:
1. **Category Loading**
   - Category hierarchy resolution
   - ACL and store mapping validation
   - Category template selection

2. **Product Filtering**
   ```csharp
   // Search query building
   var searchQuery = _catalogSearchQueryFactory.Current;
   searchQuery.CategoryIds = new[] { category.Id };
   searchQuery.StoreId = _storeContext.CurrentStore.Id;
   searchQuery.VisibleIndividuallyOnly = true;
   
   // Apply filters (price, attributes, etc.)
   if (model.PriceRangeFilter.Enabled)
   {
       searchQuery.PriceMin = model.PriceRangeFilter.Min;
       searchQuery.PriceMax = model.PriceRangeFilter.Max;
   }
   ```

3. **Product List Generation**
   - Search execution
   - Product summary mapping
   - Pagination handling
   - Sorting options application

### 4.2 Product Details Workflow
**Product Loading Process**:
1. **Product Resolution**
   - SEO name to product mapping
   - Availability validation
   - ACL and store validation

2. **Product Data Preparation**
   ```csharp
   // Price calculation
   model.ProductPrice.Price = _priceCalculationService.GetFinalPrice(product, customer, includeDiscounts: true);
   
   // Stock information
   model.StockAvailability = product.FormatStockMessage(_localizationService);
   
   // Product attributes
   var productVariantAttributes = _productAttributeService.GetProductVariantAttributesByProductId(product.Id);
   foreach (var attribute in productVariantAttributes)
   {
       var attributeModel = new ProductDetailsModel.ProductVariantAttributeModel();
       // ... attribute mapping
   }
   ```

3. **Related Data Loading**
   - Product images and media
   - Related/associated products
   - Product reviews and ratings
   - Cross-sell suggestions

---

## 5. Discount and Promotion Workflow

### 5.1 Coupon Code Application
**Controllers**: `ShoppingCartController.ApplyDiscountCoupon`

**Coupon Validation Process**:
```csharp
public ActionResult ApplyDiscountCoupon(string discountcouponcode, ProductVariantQuery query)
{
    var discount = _discountService.GetDiscountByCouponCode(discountcouponcode);
    var isDiscountValid = discount != null &&
                         discount.RequiresCouponCode &&
                         _discountService.IsDiscountValid(discount, customer, discountcouponcode);
    
    if (isDiscountValid)
    {
        var oldCartTotal = _orderTotalCalculationService.GetShoppingCartTotal(cart);
        _genericAttributeService.SaveAttribute(customer, SystemCustomerAttributeNames.DiscountCouponCode, discountcouponcode);
        var newCartTotal = _orderTotalCalculationService.GetShoppingCartTotal(cart);
        
        discountApplied = oldCartTotal != newCartTotal;
    }
}
```

### 5.2 Automatic Discount Application
**Components**: Rules engine integration
- Customer-based discounts
- Order total discounts
- Product-specific discounts
- Category-based promotions

---

## 6. Payment Processing Workflow

### 6.1 Payment Method Selection
**Controllers**: `CheckoutController.PaymentMethod`
**Process**:
1. Available payment methods filtering
2. Payment method validation
3. Payment info collection
4. Payment method storage in session

### 6.2 Payment Processing Pipeline
**Components**: `PaymentService`, `IPaymentMethod` implementations

**Payment Flow**:
```csharp
// Pre-process payment
var preProcessResult = _paymentService.PreProcessPayment(processPaymentRequest);

// Process payment
var processResult = _paymentService.ProcessPayment(processPaymentRequest);

// Post-process payment (redirects, confirmations)
_paymentService.PostProcessPayment(postProcessPaymentRequest);
```

**Payment State Management**:
- `Pending` → `Authorized` → `Paid`
- Capture and void operations
- Refund processing
- Recurring payment handling

---

## 7. User Journey Analysis

### 7.1 Guest Customer Journey
1. **Browse Products** → **Add to Cart** → **Guest Checkout** → **Order Completion**
2. **Registration Prompts** at checkout
3. **Anonymous cart** → **Registered cart migration**

### 7.2 Registered Customer Journey
1. **Login/Registration** → **Profile Management** → **Shopping** → **Checkout** → **Order History**
2. **Wishlist Management**
3. **Repeat Order Functionality**
4. **Account Dashboard Access**

### 7.3 Administrative Journey
1. **Product Management** → **Category Organization** → **Inventory Control**
2. **Order Processing** → **Customer Service** → **Reporting**
3. **Configuration Management** → **System Administration**

---

## 8. Integration Points

### 8.1 External Systems
- **Payment Gateways**: Plugin-based architecture
- **Shipping Providers**: Rate calculation and tracking
- **Tax Services**: Tax calculation and validation
- **Email Services**: Transactional email handling

### 8.2 Internal Components
- **Search Engine**: Product and content search
- **Media Management**: Image and file handling
- **Localization**: Multi-language and currency support
- **Caching**: Performance optimization layer

---

## 9. Business Rules Summary

### 9.1 Order Management Rules
- Minimum order intervals for fraud prevention
- Order status state machine enforcement
- Payment method availability based on order characteristics
- Shipping method filtering based on products and geography

### 9.2 Customer Management Rules
- Registration validation and uniqueness enforcement
- Role-based access control
- Account activation workflows
- Password security requirements

### 9.3 Inventory Management Rules
- Stock level validation during cart operations
- Backorder and preorder handling
- Product availability scheduling
- Inventory allocation for pending orders

### 9.4 Pricing and Discount Rules
- Tiered pricing based on customer groups
- Volume discounts and promotional pricing
- Coupon code validation and usage limits
- Tax calculation based on customer and product attributes

---

## 10. Migration Considerations

### 10.1 State Management Migration
- **Session-based data** → **Redux/Context state management**
- **Server-side validation** → **Client-side + API validation**
- **Postback model** → **SPA navigation**

### 10.2 Workflow Adaptation
- **Multi-step forms** → **React form management (Formik/React Hook Form)**
- **Server-side rendering** → **Client-side rendering with SSR/SSG**
- **Synchronous operations** → **Asynchronous API calls**

### 10.3 Business Logic Preservation
- **Core validation rules** must be maintained in API layer
- **State transitions** need equivalent implementation
- **Integration patterns** require API gateway design
- **Security workflows** need JWT/token-based equivalents

---

*This document serves as the foundation for understanding SmartStore.NET business processes and will guide the React migration architecture decisions.*