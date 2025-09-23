# Technology Stack Inventory - SmartStore.NET Legacy

## Core Framework Stack

### .NET Platform
- **.NET Framework**: 4.7.2 (Legacy, Microsoft support ends 2027)
- **C# Language Version**: 7.3 (based on .NET Framework 4.7.2)
- **ASP.NET**: MVC 5.2.7 (Legacy version)
- **Entity Framework**: 6.4.4 (Legacy ORM)
- **Visual Studio**: Compatible with VS 2017-2022

### Web Framework
- **ASP.NET MVC**: 5.2.7
- **Razor View Engine**: 3.2.7
- **Web API**: Limited implementation
- **Web Pages**: 3.2.7
- **Authentication**: Forms Authentication + Custom providers

## Database Technologies

### Primary Database
- **SQL Server**: Full edition support (2012+)
- **SQL Server Compact**: 4.0.8876.1 (for development/small deployments)
- **Entity Framework**: 6.4.4 Code First approach
- **Migrations**: Automated schema versioning

### Database Providers
- **SqlServerDataProvider**: Primary production provider
- **SqlCeDataProvider**: Development/testing provider
- **Connection Pooling**: Built-in .NET connection management

## Dependency Injection & IoC

### Autofac Framework
- **Autofac Core**: 5.2.0
- **Autofac.Mvc5**: 5.0.0 (MVC integration)
- **Autofac.WebApi2**: 5.0.0 (Web API integration)
- **Registration Pattern**: Module-based dependency registration
- **Lifetime Scope**: Per-request and singleton scopes

## Frontend Technologies

### JavaScript Libraries
- **jQuery**: Core DOM manipulation (legacy version)
- **jQuery Validation**: Form validation framework
- **Underscore.js**: Utility library
- **Globalize**: Internationalization support

### UI Frameworks & Styling
- **Bootstrap**: CSS framework (older version)
- **SASS/SCSS**: CSS preprocessing
- **Custom SmartStore JS**: Proprietary UI components
  - `smartstore.common.js`
  - `smartstore.gallery.js`
  - `smartstore.megamenu.js`
  - `smartstore.instantsearch.js`

### Build Tools & Asset Processing
- **Bundle Transformer**: Asset bundling and minification
  - **Core**: 1.10.0
  - **SASS**: 1.12.1 for SCSS compilation
  - **Autoprefixer**: 1.12.1 for CSS vendor prefixes
  - **NUglify**: 1.12.16 for JS/CSS minification
- **LibSassHost**: 1.2.6 (Native SASS compilation)
- **WebGrease**: 1.6.0 (Legacy asset optimization)

## Business Logic Libraries

### Payment Processing
- **AmazonPay**: 3.5.0
- **DotNetOpenAuth**: 4.3.4.13329 (OAuth/OpenID)
- **Payment Provider Pattern**: Extensible payment gateway system

### Data Processing
- **EPPlus**: 4.5.3.1 (Excel file processing)
- **LumenWorksCsvReader**: 4.0.0 (CSV data import)
- **CronExpressionDescriptor**: 2.15.0 (Task scheduling)

### Media & Content Processing
- **ImageProcessor**: 2.8.0 (Image manipulation)
- **ImageProcessor.Plugins.WebP**: 1.3.0 (WebP support)
- **AngleSharp**: 0.9.11 (HTML parsing)
- **HtmlSanitizer**: 4.0.205 (XSS protection)
- **DotLiquid**: 2.0.254 (Template engine)

### Localization & Content
- **FluentValidation**: 7.4.0 (Model validation)
- **FluentValidation.Mvc5**: 7.4.0 (MVC integration)
- **Multi-language Support**: Built-in localization system

## Logging & Monitoring

### Logging Framework
- **log4net**: 2.0.8 (Comprehensive logging)
- **Configuration**: XML-based log4net.config
- **Appenders**: File, Database, Email logging support
- **Log Levels**: Debug, Info, Warn, Error, Fatal

## Security Libraries

### Authentication & Authorization
- **Custom Authentication**: Forms-based with extensions
- **Password Hashing**: Custom implementation with salt
- **CSRF Protection**: Anti-forgery tokens
- **XSS Protection**: HTML sanitization

### Data Protection
- **Input Validation**: FluentValidation + custom validators
- **SQL Injection Prevention**: EF parameterized queries
- **File Upload Security**: MIME type validation and scanning

## Geographic & Location Services

### Geographic Data
- **MaxMind.GeoIP2**: 3.2.0 (IP geolocation)
- **MaxMind.Db**: 2.6.1 (Database reader)
- **Country/Region**: Built-in geographic data management

## Plugin Architecture

### Plugin System
- **Hot-swappable Plugins**: Dynamic assembly loading
- **Plugin Discovery**: Automatic plugin detection
- **Provider Patterns**: Payment, shipping, tax providers
- **Theme System**: UI theme inheritance and customization

### Supported Plugin Types
- **Payment Providers**: Gateway integrations
- **Shipping Providers**: Carrier rate calculations
- **Tax Providers**: Tax calculation engines
- **Authentication Providers**: External auth (Google, Facebook, etc.)
- **Widget Providers**: UI component plugins

## Development Tools

### Build & Compilation
- **MSBuild**: Standard .NET build system
- **NuGet Package Management**: Dependencies via packages.config
- **Assembly Versioning**: Centralized version management
- **Configuration Transforms**: Environment-specific configs

### Testing Framework
- **Test Projects**: 6 dedicated test assemblies
- **Testing Pattern**: Service layer unit testing
- **Mocking**: Dependency injection enables testability

## Caching Technologies

### Multi-Tier Caching
- **L1 Cache**: In-memory caching (MemoryCache)
- **L2 Cache**: Distributed caching support
- **Redis Support**: Available through plugins
- **Output Caching**: HTTP response caching
- **Cache Invalidation**: Event-driven cache management

## Email & Communication

### Email Processing
- **SMTP Support**: Built-in email infrastructure
- **Email Templates**: Liquid template engine
- **Queue Management**: Background email processing
- **Attachment Support**: File attachment handling

## File & Media Management

### File Storage
- **Local File System**: Default file storage
- **Cloud Storage**: Extensible provider pattern
- **Image Processing**: Automated thumbnail generation
- **CDN Support**: Content delivery network integration

## Version Compatibility Matrix

| Component | Current Version | Latest Available | Migration Path |
|-----------|----------------|------------------|----------------|
| .NET Framework | 4.7.2 | 4.8.1 | .NET 6/8 |
| ASP.NET MVC | 5.2.7 | 5.2.9 | ASP.NET Core |
| Entity Framework | 6.4.4 | 6.4.4 | EF Core |
| jQuery | Legacy | 3.7.1 | React/Modern JS |
| Autofac | 5.2.0 | 7.1.0 | Built-in DI |
| Bootstrap | Legacy | 5.3.2 | Tailwind/Modern CSS |

## Security Assessment

### Current Security Posture
- **Framework Security**: Dependent on .NET Framework patches
- **Known Vulnerabilities**: Some dependencies may have CVEs
- **Security Updates**: Limited by .NET Framework lifecycle
- **Best Practices**: Generally follows secure coding practices

### Security Recommendations
1. **Dependency Updates**: Update all NuGet packages to latest versions
2. **Framework Migration**: Move to .NET 6/8 for continued security support
3. **Vulnerability Scanning**: Regular dependency vulnerability assessment
4. **Security Headers**: Implement modern security headers

## Performance Characteristics

### Current Performance Stack
- **Caching**: Multi-tier caching strategy
- **Database**: EF 6.x with optimization patterns
- **Assets**: Bundle optimization and minification
- **Images**: Automated image optimization

### Performance Limitations
- **JavaScript**: jQuery performance limitations
- **Rendering**: Server-side rendering only
- **Bundle Size**: Large JavaScript bundles
- **Mobile**: Limited mobile optimization

## Migration Impact Assessment

### High Impact Changes Required
1. **Frontend Framework**: Complete JavaScript rewrite to React
2. **Backend Framework**: .NET Framework to .NET Core/8
3. **ORM**: Entity Framework 6 to EF Core
4. **Authentication**: Modern authentication patterns
5. **API Layer**: RESTful API implementation

### Medium Impact Changes
1. **Dependency Injection**: Autofac to built-in DI
2. **Configuration**: appsettings.json pattern
3. **Logging**: ILogger interface adoption
4. **Testing**: Modern testing frameworks

### Low Impact (Preservable)
1. **Domain Models**: Core business entities
2. **Business Logic**: Service layer patterns
3. **Database Schema**: Existing data structure
4. **Plugin Architecture**: Concept can be modernized