# SmartStore.NET - Business Rules Catalog

## Overview
This document catalogs the business rules, validation logic, and constraints identified in the SmartStore.NET system. These rules must be preserved and reimplemented in the React migration.

---

## 1. Order Management Rules

### 1.1 Order Placement Rules

#### R-ORD-001: Minimum Order Placement Interval
```csharp
public virtual bool IsMinimumOrderPlacementIntervalValid(Customer customer, Store store)
{
    var lastOrderTime = customer.LastOrderTime;
    if (lastOrderTime.HasValue)
    {
        var interval = DateTime.UtcNow - lastOrderTime.Value;
        return interval.TotalSeconds >= _orderSettings.MinimumOrderPlacementInterval;
    }
    return true;
}
```
**Business Impact**: Prevents rapid-fire order submissions for fraud prevention
**Migration Note**: Implement as API-level validation with client-side feedback

#### R-ORD-002: Order Status State Machine
```csharp
public enum OrderStatus
{
    Pending = 10,
    Processing = 20,
    Complete = 30,
    Cancelled = 40
}

// Valid transitions
Pending → Processing → Complete
Pending → Cancelled
Processing → Cancelled
```
**Business Impact**: Ensures order lifecycle integrity
**Migration Note**: Implement state machine pattern in React state management

#### R-ORD-003: Payment Status Validation
```csharp
// Capture validation
public virtual bool CanCapture(Order order)
{
    return (order.OrderStatus != OrderStatus.Cancelled && 
            order.OrderStatus != OrderStatus.Pending) &&
           (order.PaymentStatus == PaymentStatus.Authorized) &&
           _paymentService.SupportCapture(order.PaymentMethodSystemName);
}
```
**Business Impact**: Prevents invalid payment operations
**Migration Note**: API validation with UI state reflection

#### R-ORD-004: Recurring Order Validation
```csharp
if (isRecurringShoppingCart)
{
    recurringPaymentType = _paymentService.GetRecurringPaymentType(processPaymentRequest.PaymentMethodSystemName);
    switch (recurringPaymentType.Value)
    {
        case RecurringPaymentType.NotSupported:
            warnings.Add(T("Payment.RecurringPaymentNotSupported"));
            break;
        case RecurringPaymentType.Manual:
        case RecurringPaymentType.Automatic:
            break;
        default:
            warnings.Add(T("Payment.RecurringPaymentTypeUnknown"));
            break;
    }
}
```
**Business Impact**: Ensures payment method supports recurring billing
**Migration Note**: Payment method capability validation

---

## 2. Customer Management Rules

### 2.1 Registration Rules

#### R-CUS-001: Email Uniqueness Validation
```csharp
public virtual void InsertCustomer(Customer customer)
{
    if (customer.Email.HasValue() && GetCustomerByEmail(customer.Email) != null)
    {
        throw new SmartException(T("Account.Register.Errors.EmailAlreadyExists"));
    }
}
```
**Business Impact**: Prevents duplicate email registrations
**Migration Note**: Real-time API validation with debounced checking

#### R-CUS-002: Username Uniqueness Validation
```csharp
if (customer.Username.HasValue() &&
    _customerSettings.CustomerLoginType != CustomerLoginType.Email &&
    GetCustomerByUsername(customer.Username) != null)
{
    throw new SmartException(T("Account.Register.Errors.UsernameAlreadyExists"));
}
```
**Business Impact**: Prevents duplicate usernames when username login is enabled
**Migration Note**: Conditional validation based on login type settings

#### R-CUS-003: System Account Protection
```csharp
if (request.Customer.IsSearchEngineAccount())
{
    result.AddError(T("Account.Register.Errors.CannotRegisterSearchEngine"));
    return result;
}

if (request.Customer.IsBackgroundTaskAccount())
{
    result.AddError(T("Account.Register.Errors.CannotRegisterTaskAccount"));
    return result;
}
```
**Business Impact**: Prevents registration of system-reserved accounts
**Migration Note**: Server-side validation only (security rule)

#### R-CUS-004: Already Registered Check
```csharp
if (request.Customer.IsRegistered())
{
    result.AddError(T("Account.Register.Errors.AlreadyRegistered"));
    return result;
}
```
**Business Impact**: Prevents re-registration of existing customers
**Migration Note**: Session state validation

### 2.2 Authentication Rules

#### R-CUS-005: Customer Login Type Validation
```csharp
public virtual Customer GetAuthenticatedCustomerFromTicket(FormsAuthenticationTicket ticket)
{
    List<Func<string, Customer>> customerResolvers = new List<Func<string, Customer>>(2);

    if (_customerSettings.CustomerLoginType == CustomerLoginType.Email)
    {
        customerResolvers.Add(_customerService.GetCustomerByEmail);
    }
    else if (_customerSettings.CustomerLoginType == CustomerLoginType.Username)
    {
        customerResolvers.Add(_customerService.GetCustomerByUsername);
    }
    else // UsernameOrEmail
    {
        customerResolvers.Add(_customerService.GetCustomerByUsername);
        var mayBeEmail = usernameOrEmail.IndexOf('@') > -1;
        if (mayBeEmail)
        {
            customerResolvers.Insert(0, _customerService.GetCustomerByEmail);
        }
    }
}
```
**Business Impact**: Flexible login credential handling
**Migration Note**: Form validation logic adaptation

#### R-CUS-006: Password Validation
```csharp
public virtual bool ValidateCustomer(string usernameOrEmail, string password)
{
    var customer = /* resolve customer */;
    
    switch (customer.PasswordFormat)
    {
        case PasswordFormat.Clear:
            pwd = password;
            break;
        case PasswordFormat.Encrypted:
            pwd = _encryptionService.DecryptText(customer.Password);
            break;
        case PasswordFormat.Hashed:
            pwd = _encryptionService.CreatePasswordHash(password, customer.PasswordSalt, _customerSettings.HashedPasswordFormat);
            break;
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
**Business Impact**: Secure password verification with format flexibility
**Migration Note**: API-only validation (security-sensitive)

### 2.3 Role Management Rules

#### R-CUS-007: Default Role Assignment
```csharp
public virtual CustomerRegistrationResult RegisterCustomer(CustomerRegistrationRequest request)
{
    var registeredRole = _customerService.GetCustomerRoleBySystemName(SystemCustomerRoleNames.Registered);
    if (registeredRole == null)
    {
        throw new SmartException(T("Admin.Customers.CustomerRoles.CannotFoundRole", "Registered"));
    }

    // Add to 'Registered' role
    _customerService.InsertCustomerRoleMapping(new CustomerRoleMapping { CustomerId = request.Customer.Id, CustomerRoleId = registeredRole.Id });

    // Remove from 'Guests' role
    var mappings = request.Customer.CustomerRoleMappings.Where(x => !x.IsSystemMapping && x.CustomerRole.SystemName == SystemCustomerRoleNames.Guests).ToList();
    mappings.Each(x => _customerService.DeleteCustomerRoleMapping(x));
}
```
**Business Impact**: Proper role transitions during registration
**Migration Note**: User role state management in React

---

## 3. Shopping Cart Rules

### 3.1 Cart Item Management Rules

#### R-CAR-001: Maximum Cart Items Validation
```csharp
if (cartType == ShoppingCartType.ShoppingCart && cart.Count >= _shoppingCartSettings.MaximumShoppingCartItems)
{
    warnings.Add(T("ShoppingCart.MaximumShoppingCartItems"));
    return warnings;
}
else if (cartType == ShoppingCartType.Wishlist && cart.Count >= _shoppingCartSettings.MaximumWishlistItems)
{
    warnings.Add(T("ShoppingCart.MaximumWishlistItems"));
    return warnings;
}
```
**Business Impact**: Prevents cart abuse and performance issues
**Migration Note**: Client-side validation with configurable limits

#### R-CAR-002: Quantity Validation
```csharp
if (quantity <= 0)
{
    warnings.Add(T("ShoppingCart.QuantityShouldPositive"));
    return warnings;
}
```
**Business Impact**: Ensures valid product quantities
**Migration Note**: Form validation with immediate feedback

#### R-CAR-003: Permission-Based Cart Access
```csharp
if (cartType == ShoppingCartType.ShoppingCart && 
    !_permissionService.Authorize(Permissions.Cart.AccessShoppingCart, customer))
{
    warnings.Add(T("ShoppingCart.IsDisabled"));
    return warnings;
}

if (cartType == ShoppingCartType.Wishlist && 
    !_permissionService.Authorize(Permissions.Cart.AccessWishlist, customer))
{
    warnings.Add(T("Wishlist.IsDisabled"));
    return warnings;
}
```
**Business Impact**: Role-based cart feature access
**Migration Note**: Permission-based UI component rendering

#### R-CAR-004: Cart Item Consolidation
```csharp
var existingCartItem = FindShoppingCartItemInTheCart(cart, cartType, product, selectedAttributes, customerEnteredPrice);

if (existingCartItem != null && !_shoppingCartSettings.AddProductsToBasketInSinglePositions)
{
    // Update existing shopping cart item
    int newQuantity = existingCartItem.Item.Quantity + quantity;
    warnings.AddRange(GetShoppingCartItemWarnings(customer, cartType, product, storeId, selectedAttributes, customerEnteredPrice, newQuantity));
    
    if (warnings.Count == 0)
    {
        existingCartItem.Item.AttributesXml = selectedAttributes;
        existingCartItem.Item.Quantity = newQuantity;
        existingCartItem.Item.UpdatedOnUtc = DateTime.UtcNow;
    }
}
```
**Business Impact**: Configurable cart item consolidation behavior
**Migration Note**: Settings-driven cart behavior

### 3.2 Cart Calculation Rules

#### R-CAR-005: Price Calculation with Discounts
```csharp
public virtual decimal GetUnitPrice(OrganizedShoppingCartItem shoppingCartItem, bool includeDiscounts)
{
    var customer = shoppingCartItem.Item.Customer;
    return GetUnitPrice(shoppingCartItem.Item.Product, customer, ShoppingCartType.ShoppingCart,
        shoppingCartItem.Item.Quantity, shoppingCartItem.Item.AttributesXml, 
        shoppingCartItem.Item.CustomerEnteredPrice, includeDiscounts);
}
```
**Business Impact**: Consistent pricing across cart and checkout
**Migration Note**: Real-time price calculation with discount application

#### R-CAR-006: Attribute Price Adjustments
```csharp
public virtual decimal GetDiscountAmount(OrganizedShoppingCartItem shoppingCartItem, out Discount appliedDiscount)
{
    var attributesTotalPrice = decimal.Zero;
    var pvaValues = _productAttributeParser.ParseProductVariantAttributeValues(shoppingCartItem.Item.AttributesXml).ToList();

    foreach (var pvaValue in pvaValues)
    {
        attributesTotalPrice += GetProductVariantAttributeValuePriceAdjustment(pvaValue, product, customer, null, quantity);
    }

    var productDiscountAmount = GetDiscountAmount(product, customer, attributesTotalPrice, quantity, out appliedDiscount);
    totalDiscountAmount = productDiscountAmount * quantity;
}
```
**Business Impact**: Accurate pricing with product attribute considerations
**Migration Note**: Complex price calculation logic preservation

---

## 4. Product Catalog Rules

### 4.1 Product Availability Rules

#### R-PRD-001: Stock Quantity Validation
```csharp
public virtual List<string> GetShoppingCartItemWarnings(Customer customer, ShoppingCartType shoppingCartType, Product product, int storeId, string selectedAttributes, decimal customerEnteredPrice, int quantity, bool automaticallyAddRequiredProductsIfEnabled, bool getStandardWarnings = true, bool getAttributesWarnings = true, bool getGiftCardWarnings = true, bool getRequiredProductWarnings = true, bool getRentalWarnings = true)
{
    if (product.ManageInventoryMethod == ManageInventoryMethod.ManageStock)
    {
        if (product.BackorderMode == BackorderMode.NoBackorders && product.StockQuantity < quantity)
        {
            int maximumQuantityCanBeAdded = product.StockQuantity;
            if (maximumQuantityCanBeAdded <= 0)
                warnings.Add(T("ShoppingCart.OutOfStock"));
            else
                warnings.Add(T("ShoppingCart.QuantityExceedsStock", maximumQuantityCanBeAdded));
        }
    }
}
```
**Business Impact**: Prevents overselling and stock discrepancies
**Migration Note**: Real-time inventory validation

#### R-PRD-002: Product Visibility Rules
```csharp
// Category browsing
searchQuery.VisibleIndividuallyOnly = true;
searchQuery.StoreId = _storeContext.CurrentStore.Id;

// ACL validation
if (!_aclService.Authorize(product))
{
    // Product not accessible to current customer
}

// Store mapping validation
if (!_storeMappingService.Authorize(product))
{
    // Product not available in current store
}
```
**Business Impact**: Ensures proper product visibility and access control
**Migration Note**: Server-side filtering with client-side state management

#### R-PRD-003: Product Attribute Validation
```csharp
public virtual List<string> GetShoppingCartItemAttributeWarnings(Customer customer, ShoppingCartType shoppingCartType, Product product, string selectedAttributes)
{
    var warnings = new List<string>();
    
    // Check required attributes
    var productVariantAttributes = _productAttributeService.GetProductVariantAttributesByProductId(product.Id);
    foreach (var pva in productVariantAttributes)
    {
        if (pva.IsRequired)
        {
            var pvaValues = _productAttributeParser.ParseProductVariantAttributeValues(selectedAttributes, pva.Id);
            if (!pvaValues.Any())
            {
                warnings.Add(T("ShoppingCart.SelectAttribute", pva.ProductAttribute.GetLocalized(x => x.Name)));
            }
        }
    }
    
    return warnings;
}
```
**Business Impact**: Ensures required product options are selected
**Migration Note**: Form validation with dynamic attribute requirements

### 4.2 Pricing Rules

#### R-PRD-004: Customer-Specific Pricing
```csharp
public virtual decimal GetFinalPrice(Product product, Customer customer, decimal additionalCharge = decimal.Zero, bool includeDiscounts = true, int quantity = 1)
{
    // Check for customer-entered price
    if (product.CustomerEntersPrice)
    {
        return additionalCharge;
    }
    
    // Check for tier prices
    var tierPrice = product.TierPrices
        .OrderBy(tp => tp.Quantity)
        .LastOrDefault(tp => quantity >= tp.Quantity && 
                       (tp.CustomerRoleId == null || customer.CustomerRoles.Any(cr => cr.Id == tp.CustomerRoleId)));
    
    if (tierPrice != null)
    {
        return tierPrice.Price + additionalCharge;
    }
    
    return product.Price + additionalCharge;
}
```
**Business Impact**: Dynamic pricing based on customer and quantity
**Migration Note**: Context-aware price display

---

## 5. Discount and Promotion Rules

### 5.1 Coupon Code Rules

#### R-DIS-001: Coupon Code Validation
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
        
        if (discountApplied)
        {
            model.DiscountBox.Message = T("ShoppingCart.DiscountCouponCode.Applied");
        }
        else
        {
            model.DiscountBox.Message = T("ShoppingCart.DiscountCouponCode.NoMoreDiscount");
        }
    }
    else
    {
        model.DiscountBox.Message = T("ShoppingCart.DiscountCouponCode.WrongDiscount");
    }
}
```
**Business Impact**: Proper coupon validation and feedback
**Migration Note**: Async validation with immediate UI feedback

#### R-DIS-002: Discount Applicability Rules
```csharp
public virtual bool IsDiscountValid(Discount discount, Customer customer, string couponCodeToValidate)
{
    // Check if discount is active
    if (!discount.IsActive())
        return false;
    
    // Check date range
    if (discount.StartDateUtc.HasValue && discount.StartDateUtc.Value > DateTime.UtcNow)
        return false;
    if (discount.EndDateUtc.HasValue && discount.EndDateUtc.Value < DateTime.UtcNow)
        return false;
    
    // Check usage limits
    if (discount.LimitationTimes > 0 && discount.DiscountUsageHistory.Count >= discount.LimitationTimes)
        return false;
    
    // Check customer-specific usage limits
    if (discount.LimitationTimes > 0)
    {
        var customerUsageCount = discount.DiscountUsageHistory.Count(x => x.CustomerId == customer.Id);
        if (customerUsageCount >= discount.LimitationTimes)
            return false;
    }
    
    return true;
}
```
**Business Impact**: Comprehensive discount validation logic
**Migration Note**: Complex validation rule implementation

---

## 6. Payment Processing Rules

### 6.1 Payment Method Rules

#### R-PAY-001: Payment Method Availability
```csharp
public virtual bool IsPaymentMethodActive(Provider<IPaymentMethod> paymentMethod, Customer customer, IList<OrganizedShoppingCartItem> cart, int storeId)
{
    if (!paymentMethod.IsPaymentMethodActive(_paymentSettings))
        return false;
    
    if (!_paymentService.IsPaymentMethodAllowedByRestrictions(paymentMethod.Metadata, customer, cart, storeId))
        return false;
    
    return true;
}
```
**Business Impact**: Dynamic payment method filtering
**Migration Note**: Context-aware payment options

#### R-PAY-002: Payment Validation
```csharp
public virtual ProcessPaymentResult ProcessPayment(ProcessPaymentRequest processPaymentRequest)
{
    if (processPaymentRequest.OrderTotal == decimal.Zero)
    {
        var result = new ProcessPaymentResult()
        {
            NewPaymentStatus = PaymentStatus.Paid
        };
        return result;
    }
    
    // Clean credit card number
    if (!String.IsNullOrWhiteSpace(processPaymentRequest.CreditCardNumber))
    {
        processPaymentRequest.CreditCardNumber = processPaymentRequest.CreditCardNumber.Replace(" ", "");
        processPaymentRequest.CreditCardNumber = processPaymentRequest.CreditCardNumber.Replace("-", "");
    }
    
    var paymentMethod = LoadPaymentMethodBySystemName(processPaymentRequest.PaymentMethodSystemName);
    if (paymentMethod == null)
        throw new SmartException(T("Payment.CouldNotLoadMethod"));

    return paymentMethod.Value.ProcessPayment(processPaymentRequest);
}
```
**Business Impact**: Payment processing validation and formatting
**Migration Note**: Payment form validation and processing

---

## 7. Security Rules

### 7.1 Access Control Rules

#### R-SEC-001: Permission-Based Access
```csharp
public virtual bool Authorize(string permissionSystemName, Customer customer = null)
{
    if (String.IsNullOrEmpty(permissionSystemName))
        return false;

    customer = customer ?? _workContext.CurrentCustomer;
    
    var customerRoles = customer.CustomerRoles.Where(cr => cr.Active);
    foreach (var role in customerRoles)
    {
        if (Authorize(permissionSystemName, role))
            return true;
    }
    
    return false;
}
```
**Business Impact**: Role-based feature access control
**Migration Note**: Component-level permission checking

#### R-SEC-002: Store-Specific Access
```csharp
public virtual bool Authorize<T>(T entity) where T : BaseEntity, IStoreMappingSupported
{
    if (entity == null)
        return false;

    if (!entity.LimitedToStores)
        return true;

    var currentStoreId = _storeContext.CurrentStore.Id;
    var mappings = _storeMappingService.GetStoreMappings(entity);
    
    return mappings.Any(x => x.StoreId == currentStoreId);
}
```
**Business Impact**: Multi-store content visibility
**Migration Note**: Store context validation

---

## 8. Data Integrity Rules

### 8.1 Entity Validation Rules

#### R-DAT-001: Required Field Validation
```csharp
// Email validation
if (!request.Email.HasValue())
{
    result.AddError(T("Account.Register.Errors.EmailIsNotProvided"));
    return result;
}

if (!request.Email.IsEmail())
{
    result.AddError(T("Account.Register.Errors.EmailIsNotValid"));
    return result;
}
```
**Business Impact**: Data quality and consistency
**Migration Note**: Form validation with proper error messaging

#### R-DAT-002: Referential Integrity
```csharp
public virtual void DeleteCustomer(Customer customer)
{
    if (customer.IsSystemAccount)
        throw new SmartException(T("Admin.Customers.Customers.CannotDeleteSystemAccount"));
    
    // Check for dependencies
    var orders = _orderService.SearchOrders(customerId: customer.Id, pageSize: 1);
    if (orders.TotalCount > 0)
        throw new SmartException(T("Admin.Customers.Customers.CannotDeleteWithOrders"));
}
```
**Business Impact**: Prevents orphaned data and maintains relationships
**Migration Note**: Cascade deletion rules and dependency checking

---

## 9. Configuration Rules

### 9.1 Setting-Dependent Behavior

#### R-CFG-001: Registration Type Enforcement
```csharp
public virtual AuthorizationResult Authorize(OpenAuthenticationParameters parameters)
{
    if (AccountDoesNotExistAndUserIsNotLoggedOn(userFound, userLoggedIn))
    {
        if (AutoRegistrationIsEnabled())
        {
            // Auto-register user
            return AutoRegister(parameters);
        }
        else if (RegistrationIsEnabled())
        {
            // Redirect to registration
            return new AuthorizationResult(OpenAuthenticationStatus.AssociateOnLogOn);
        }
        else
        {
            // Registration disabled
            return new AuthorizationResult(OpenAuthenticationStatus.Error);
        }
    }
}

private bool RegistrationIsEnabled()
{
    return _customerSettings.UserRegistrationType != UserRegistrationType.Disabled && 
           !_externalAuthenticationSettings.AutoRegisterEnabled;
}
```
**Business Impact**: Configurable user registration behavior
**Migration Note**: Settings-driven UI behavior

#### R-CFG-002: Feature Toggle Rules
```csharp
// Wishlist feature
if (!_permissionService.Authorize(Permissions.Cart.AccessWishlist))
{
    // Hide wishlist functionality
}

// Reviews feature
if (!_catalogSettings.ShowProductReviewsOnProductPage)
{
    // Hide review section
}
```
**Business Impact**: Feature availability based on configuration
**Migration Note**: Conditional component rendering

---

## 10. Localization Rules

### 10.1 Multi-Language Support

#### R-LOC-001: Content Localization
```csharp
public virtual string GetLocalized<T>(T entity, Expression<Func<T, string>> keySelector, int languageId = 0, bool returnDefaultValue = true) where T : BaseEntity, ILocalizedEntity
{
    if (languageId == 0)
        languageId = _workContext.WorkingLanguage.Id;
    
    var localizedProperty = GetLocalizedProperty(entity.Id, keySelector.GetPropertyName(), typeof(T).Name, languageId);
    
    if (localizedProperty != null && !string.IsNullOrEmpty(localizedProperty.LocaleValue))
        return localizedProperty.LocaleValue;
    
    if (returnDefaultValue)
    {
        var compiledExpression = keySelector.Compile();
        return compiledExpression(entity);
    }
    
    return string.Empty;
}
```
**Business Impact**: Dynamic content localization
**Migration Note**: i18n implementation with dynamic loading

#### R-LOC-002: Currency Conversion
```csharp
public virtual decimal ConvertFromPrimaryStoreCurrency(decimal amount, Currency targetCurrencyCode)
{
    var primaryStoreCurrency = _currencyService.GetCurrencyById(_currencySettings.PrimaryStoreCurrencyId);
    var targetCurrency = _currencyService.GetCurrencyByCode(targetCurrencyCode.CurrencyCode);
    
    if (primaryStoreCurrency.Id == targetCurrency.Id)
        return amount;
    
    var result = amount * targetCurrency.Rate / primaryStoreCurrency.Rate;
    return Math.Round(result, targetCurrency.DisplayOrder);
}
```
**Business Impact**: Accurate multi-currency pricing
**Migration Note**: Currency context and conversion logic

---

## Migration Priority Matrix

| Rule Category | Business Criticality | Technical Complexity | Migration Priority |
|---------------|---------------------|---------------------|-------------------|
| Order Management | High | Medium | P1 |
| Customer Management | High | Medium | P1 |
| Payment Processing | High | High | P1 |
| Shopping Cart | High | Medium | P1 |
| Security Rules | High | High | P1 |
| Product Catalog | Medium | Medium | P2 |
| Discount Rules | Medium | High | P2 |
| Data Integrity | Medium | Low | P2 |
| Configuration | Low | Low | P3 |
| Localization | Low | Medium | P3 |

---

## Implementation Strategy

### Phase 1: Core Business Rules (P1)
1. **API Layer Implementation**: Server-side validation and business logic
2. **Client-Side Validation**: Form validation and immediate feedback
3. **State Management**: React state for business rule enforcement

### Phase 2: Extended Business Rules (P2)
1. **Complex Workflow Logic**: Multi-step processes and advanced validation
2. **Integration Rules**: External system interaction rules
3. **Performance Rules**: Caching and optimization rules

### Phase 3: Configuration and Localization (P3)
1. **Setting-Driven Behavior**: Dynamic feature toggling
2. **Internationalization**: Multi-language and currency support
3. **Administrative Rules**: Backend management and configuration

---

*This business rules catalog serves as the definitive reference for maintaining business logic integrity during the React migration process.*