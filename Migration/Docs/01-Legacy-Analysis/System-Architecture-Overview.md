# System Architecture Overview - SmartStore.NET Legacy

## Executive Summary
SmartStore.NET is a comprehensive e-commerce platform built on **ASP.NET MVC 5.2.7** with **.NET Framework 4.7.2**. The system follows a **Domain-Driven Design (DDD)** architecture with clean separation of concerns across multiple layers.

## Architecture Layers

### 1. Presentation Layer (`SmartStore.Web`)
- **Pattern**: ASP.NET MVC 5.2.7 with Razor View Engine
- **Controllers**: 32 controllers handling various business domains
- **Views**: Multi-theme support (Flex, FlexBlack, FlexBlue themes)
- **Models**: ViewModels for data transfer to views
- **Framework Integration**: Custom base controllers with shared functionality

**Key Controllers:**
- `ProductController` - Product catalog and details
- `CatalogController` - Category browsing and search
- `ShoppingCartController` - Cart management
- `CheckoutController` - Order processing
- `CustomerController` - User account management
- `HomeController` - Landing pages and main navigation

### 2. Framework Layer (`SmartStore.Web.Framework`)
- **Authentication**: Forms-based authentication with custom membership
- **Authorization**: Role-based access control with ACL support
- **Filters**: Custom action filters for security and validation
- **Base Controllers**: `PublicControllerBase` for common functionality
- **UI Helpers**: HTML helpers and UI components
- **Routing**: Custom route handlers and URL generation

### 3. Service Layer (`SmartStore.Services`)
- **Pattern**: Service-oriented architecture with interface contracts
- **Dependency Injection**: Autofac container for IoC
- **Business Logic**: 47 service domains covering all business functions
- **Transaction Management**: Unit of Work pattern with Entity Framework

**Key Service Domains:**
- **Catalog Services**: Product, Category, Manufacturer management
- **Customer Services**: Registration, authentication, profiles
- **Order Services**: Order processing, payment, shipping
- **Media Services**: File management, image processing
- **Security Services**: Authentication, authorization, encryption
- **Localization Services**: Multi-language support
- **Search Services**: Product search and indexing
- **Tax/Shipping Services**: Complex business rule engines

### 4. Core Layer (`SmartStore.Core`)
- **Domain Models**: Rich domain entities with business logic
- **Base Classes**: Abstract base classes and interfaces
- **Infrastructure**: Core services and utilities
- **Configuration**: Settings management and validation
- **Events**: Event-driven architecture support
- **Caching**: Multi-tier caching strategy
- **Extensions**: Extension methods and utilities

### 5. Data Access Layer (`SmartStore.Data`)
- **ORM**: Entity Framework 6.4.4 Code First
- **Database Context**: `SmartObjectContext` with automated migrations
- **Repository Pattern**: Generic repository with `EfRepository<T>`
- **Mapping**: Fluent API configurations for all entities
- **Providers**: Support for SQL Server and SQL Server Compact
- **Migrations**: Automated database schema versioning

## Domain Model Structure

### Core Entities (87 total domain entities identified)
- **Product Catalog**: Product, Category, Manufacturer, ProductAttribute
- **Customer Management**: Customer, CustomerRole, Address
- **Order Management**: Order, OrderItem, ShoppingCartItem
- **Content Management**: Blog, News, Topic, Poll
- **Media Management**: MediaFile, MediaFolder, MediaTrack
- **Localization**: Language, LocalizedProperty, LocaleStringResource
- **Security**: PermissionRecord, AclRecord, CustomerRole
- **Configuration**: Setting, Store, Theme

### Data Relationships
- **Complex Many-to-Many**: Products ↔ Categories, Products ↔ Manufacturers
- **Hierarchical Data**: Category trees, Menu structures
- **Localization Support**: All content entities support multi-language
- **Multi-Store**: Tenant isolation for multi-store scenarios
- **Audit Trail**: Created/Modified timestamps on all entities

## Technology Integration Patterns

### Dependency Injection (Autofac)
```csharp
// Service registration pattern
public class DependencyRegistrar : IDependencyRegistrar
{
    public void Register(ContainerBuilder builder, ITypeFinder typeFinder)
    {
        builder.RegisterType<ProductService>().As<IProductService>().InstancePerLifetimeScope();
    }
}
```

### Entity Framework Code First
```csharp
// Configuration pattern
public class ProductMap : EntityTypeConfiguration<Product>
{
    public ProductMap()
    {
        ToTable("Product");
        HasKey(p => p.Id);
        Property(p => p.Name).IsRequired().HasMaxLength(400);
    }
}
```

### Plugin Architecture
- **Hot-swappable Plugins**: Dynamic loading of external assemblies
- **Provider Pattern**: Payment, shipping, tax providers
- **Theme System**: Customizable UI themes with inheritance
- **Widget System**: Embeddable UI components

## Performance Architecture

### Caching Strategy
- **L1 Cache**: In-memory caching with MemoryCache
- **L2 Cache**: Distributed caching support (Redis compatible)
- **HTTP Caching**: Output caching for static content
- **Cache Invalidation**: Event-driven cache busting

### Database Optimization
- **Lazy Loading**: EF proxy-based lazy loading
- **Projection Queries**: DTO projections for performance
- **Connection Management**: Optimized connection pooling
- **Index Strategy**: Comprehensive database indexing

## Security Architecture

### Authentication
- **Forms Authentication**: Cookie-based session management
- **Password Hashing**: BCrypt/PBKDF2 implementation
- **External Auth**: OAuth providers (Google, Facebook, etc.)
- **Session Management**: Configurable timeout and security

### Authorization
- **Role-Based**: Hierarchical role permissions
- **ACL Support**: Fine-grained access control lists
- **Store Mapping**: Multi-tenant authorization
- **CSRF Protection**: Anti-forgery token validation

### Data Protection
- **Input Validation**: FluentValidation framework
- **HTML Sanitization**: HtmlSanitizer for content
- **SQL Injection**: EF parameterized queries
- **XSS Protection**: Automatic encoding in Razor

## Configuration Management

### Settings Architecture
- **Typed Settings**: Strongly-typed configuration classes
- **Multi-Store**: Store-specific setting overrides
- **Runtime Changes**: Hot-reload of configuration changes
- **Validation**: Built-in setting validation

### File Configuration
- **Web.config**: Core application settings
- **Connection Strings**: Database configuration
- **Bundle Configuration**: Asset optimization
- **Log4Net**: Comprehensive logging configuration

## Legacy Strengths
1. **Mature Architecture**: Well-established patterns and practices
2. **Rich Domain Model**: Comprehensive e-commerce entities
3. **Plugin System**: Extensible architecture
4. **Multi-Store/Multi-Language**: Enterprise-ready features
5. **Performance Optimization**: Multi-tier caching and optimization
6. **Security**: Comprehensive security implementation

## Architectural Concerns
1. **Framework Age**: .NET Framework 4.7.2 reaching end-of-life
2. **MVC Version**: ASP.NET MVC 5.2.7 is legacy
3. **JavaScript**: jQuery-heavy frontend (no modern JS framework)
4. **Monolithic Structure**: Tightly coupled components
5. **EF Version**: Entity Framework 6.x is legacy
6. **No API-First**: Limited REST API support

## Migration Complexity Assessment
- **High Complexity**: Deep architectural changes required
- **Domain Logic**: Business logic can be preserved
- **Database Schema**: Can be migrated with minimal changes
- **UI Layer**: Complete rewrite required for React
- **Service Layer**: Significant refactoring needed for modern patterns