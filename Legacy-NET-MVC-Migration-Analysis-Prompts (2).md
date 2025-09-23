# Legacy .NET MVC to React/Node.js Migration Analysis Prompts

## 🚀 Migration Project Overview
**Source**: Legacy .NET MVC 4.7 with SQL Database  
**Target**: React Frontend + Node.js Backend (Clean Architecture)  
**Preservation**: CSS, Bootstrap UI, Business Logic  

---

## 📋 PHASE 1: Legacy Analysis & Discovery

### PROMPT 1: .NET MVC Legacy Project Migration Analysis

🟦 **LEGACY SYSTEM COMPREHENSIVE ANALYSIS**

Perform deep analysis of the existing .NET MVC 4.7 system:

**INPUT**: Complete legacy codebase + database schema

**ANALYZE**:
- **ARCHITECTURE LAYERS**: Controllers, Models, Views, Services, Data Access
- **CODE COMPLEXITY**: Cyclomatic complexity, technical debt assessment
- **DEPENDENCIES**: NuGet packages, external libraries, third-party integrations
- **DATABASE SCHEMA**: Tables, relationships, constraints, indexes, stored procedures
- **SECURITY PATTERNS**: Authentication, authorization, data validation
- **PERFORMANCE BOTTLENECKS**: Slow queries, memory usage, processing intensive operations
- **UI COMPONENTS**: Razor views, partial views, HTML helpers, JavaScript libraries
- **CONFIGURATION**: Web.config, connection strings, app settings

**OUTPUT**: `Migration\Docs\01-Legacy-Analysis\` folder with:
- `System-Architecture-Assessment.md` - System structure, layers, and architectural patterns
- `Technology-Stack-Audit.md` - Complete technology inventory and dependencies
- `Code-Quality-Report.md` - Technical debt, complexity metrics, and quality assessment
- `Database-Schema-Documentation.md` - Database structure, relationships, and stored procedures
- `UI-Component-Catalog.md` - Frontend components, JavaScript libraries, and CSS frameworks

**NEXT DEPENDENCY**: Feeds into Business Process Documentation

---

### PROMPT 2: Business Process Documentation

🟦 **BUSINESS PROCESS EXTRACTION**

Extract and document all business processes from the legacy system:

**INPUT**: Legacy system analysis results + codebase

**EXTRACT**:
- **BUSINESS WORKFLOWS**: End-to-end process flows for each module
- **DECISION POINTS**: Business rules embedded in code
- **DATA TRANSFORMATIONS**: Transaction processing logic
- **VALIDATION REQUIREMENTS**: Input validation and business rule validation
- **EXCEPTION HANDLING**: Error scenarios and recovery processes
- **USER INTERACTIONS**: Input/output touchpoints
- **INTEGRATION PATTERNS**: External system communications
- **DATABASE INTERACTIONS**: CRUD operations, stored procedures

**OUTPUT**: `Migration\Docs\02-Business-Processes\` folder with:
- `Business-Workflows-Documentation.md` - Complete workflow diagrams and process flows
- `Business-Rules-Catalog.md` - Extracted business rules and decision logic
- `User-Journey-Analysis.md` - End-to-end user experiences and interaction patterns

**NEXT DEPENDENCY**: Feeds into Risk Assessment

---

### PROMPT 3: Risk and Compliance Assessment

🟦 **MIGRATION RISK ANALYSIS**

Identify and assess all migration risks and compliance requirements:

**INPUT**: Legacy analysis + business process documentation

**ASSESS**:
- **TECHNICAL RISKS**: Data loss, functionality gaps, integration failures
- **BUSINESS RISKS**: Downtime impact, user adoption challenges, training needs
- **COMPLIANCE REQUIREMENTS**: Data protection, industry regulations, audit trails
- **SECURITY VULNERABILITIES**: Authentication changes, data exposure risks
- **DATA INTEGRITY RISKS**: Migration data corruption, validation failures
- **PERFORMANCE RISKS**: System slowdowns, scalability issues
- **INTEGRATION RISKS**: Third-party service compatibility, API changes
- **ROLLBACK SCENARIOS**: Failure recovery plans, data restoration procedures

**OUTPUT**: `Migration\Docs\03-Risk-Assessment\` folder with:
- `Risk-Analysis-Report.md` - Comprehensive risk catalog with impact assessment and mitigation strategies
- `Compliance-Security-Assessment.md` - Regulatory requirements and security vulnerability analysis
- `Rollback-Recovery-Plan.md` - Emergency procedures and data recovery strategies

**NEXT DEPENDENCY**: Feeds into Migration Strategy Development

---

## 📋 PHASE 2: Migration Strategy & Architecture Design

### PROMPT 4: Migration Strategy Development

🟦 **STRATEGIC MIGRATION PLANNING**

Develop comprehensive migration strategy and roadmap:

**INPUT**: Risk assessment + legacy analysis + business processes

**DESIGN**:
- **MIGRATION APPROACH**: Big-bang vs phased migration strategy
- **PRIORITY MODULES**: Critical path analysis and module sequencing
- **DATA MIGRATION STRATEGY**: ETL processes, data validation, synchronization
- **CUTOVER PLANNING**: Go-live strategy, rollback triggers, success criteria
- **RESOURCE ALLOCATION**: Team structure, skill requirements, timeline
- **TESTING STRATEGY**: Unit, integration, user acceptance, performance testing
- **TRAINING REQUIREMENTS**: User training, administrator training, support documentation
- **COMMUNICATION PLAN**: Stakeholder updates, change management, user notifications

**OUTPUT**: `Migration\Docs\04-Migration-Strategy\` folder with:
- `Migration-Roadmap-Plan.md` - Detailed timeline, milestones, and delivery approach
- `Resource-Team-Planning.md` - Team structure, skills matrix, and resource allocation
- `Data-Migration-Strategy.md` - ETL processes, validation, and synchronization approach
- `Communication-Change-Management.md` - Stakeholder engagement and change management strategy

**NEXT DEPENDENCY**: Feeds into Target Architecture Design

---

### PROMPT 5: Target Architecture Design

🟦 **CLEAN ARCHITECTURE DESIGN (REACT + NODE.JS)**

Design the target system architecture using clean architecture principles:

**INPUT**: Migration strategy + legacy system analysis

**DESIGN**:
- **FRONTEND ARCHITECTURE**: React components, state management (Redux/Context), routing
- **BACKEND ARCHITECTURE**: Node.js with Express/Fastify, clean architecture layers
- **API DESIGN**: RESTful APIs, GraphQL endpoints, authentication/authorization
- **DATA LAYER**: Database abstraction, repository patterns, ORM/ODM selection
- **SECURITY ARCHITECTURE**: JWT tokens, RBAC, input validation, CORS policies
- **CACHING STRATEGY**: Redis/Memory caching, API response caching
- **ERROR HANDLING**: Global error handlers, logging strategy, monitoring
- **CONFIGURATION MANAGEMENT**: Environment variables, secrets management

**OUTPUT**: `Migration\Docs\05-Target-Architecture\` folder with:
- `System-Architecture-Design.md` - High-level architecture blueprint and component interactions
- `Frontend-Architecture-Specification.md` - React application structure, state management, and routing
- `Backend-API-Architecture.md` - Node.js clean architecture layers and API design
- `Security-Data-Architecture.md` - Authentication, authorization, database design, and security patterns
- `Technology-Stack-Selection.md` - Selected technologies with versions and justification

**NEXT DEPENDENCY**: Feeds into Technical Planning

---

## 📋 PHASE 3: Technical Planning & Implementation Preparation

### PROMPT 6: Database Migration Planning

🟦 **DATABASE TRANSFORMATION STRATEGY**

Plan the complete database migration from SQL Server to target database:

**INPUT**: Database schema analysis + target architecture design

**PLAN**:
- **TARGET DATABASE SELECTION**: PostgreSQL/MySQL/MongoDB evaluation and selection
- **SCHEMA MIGRATION**: Table structure, relationships, constraints transformation
- **DATA MIGRATION SCRIPTS**: ETL processes, data validation, integrity checks
- **STORED PROCEDURE CONVERSION**: Business logic migration to application layer
- **INDEX OPTIMIZATION**: Performance tuning for new database platform
- **BACKUP STRATEGIES**: Migration rollback, data recovery procedures
- **SYNCHRONIZATION METHODS**: Dual-write strategies, data consistency maintenance
- **PERFORMANCE OPTIMIZATION**: Query optimization, connection pooling, caching

**OUTPUT**: `Migration\Docs\06-Database-Migration\` folder with:
- `Database-Migration-Plan.md` - Complete database migration strategy and timeline
- `Schema-Data-Transformation.md` - Table mapping, ETL scripts, and data validation procedures
- `Performance-Optimization-Strategy.md` - Indexing, query optimization, and performance tuning

**NEXT DEPENDENCY**: Feeds into Testing Strategy

---

### PROMPT 7: Testing Strategy & Quality Assurance

🟦 **COMPREHENSIVE TESTING FRAMEWORK**

Develop end-to-end testing strategy for migration validation:

**INPUT**: Target architecture + database migration plan + business processes

**DESIGN**:
- **UNIT TESTING**: Component testing for React, API endpoint testing for Node.js
- **INTEGRATION TESTING**: API integration, database connectivity, third-party services
- **UI TESTING**: Component rendering, user interaction flows, cross-browser compatibility
- **PERFORMANCE TESTING**: Load testing, stress testing, database performance
- **SECURITY TESTING**: Authentication, authorization, input validation, SQL injection
- **DATA VALIDATION**: Migration data integrity, business rule validation
- **USER ACCEPTANCE TESTING**: Business workflow validation, user experience testing
- **REGRESSION TESTING**: Automated test suites, continuous integration testing

**OUTPUT**: `Migration\Docs\07-Testing-Strategy\` folder with:
- `Testing-Framework-Strategy.md` - Complete testing approach and methodologies
- `Automated-Testing-Setup.md` - Unit, integration, and UI testing configuration
- `Performance-Security-Testing.md` - Load testing, security validation, and data integrity testing
- `User-Acceptance-Testing-Plan.md` - UAT scenarios, criteria, and business validation

**NEXT DEPENDENCY**: Feeds into Legacy System Reverse Engineering

---

## 📋 PHASE 4: Legacy System Reverse Engineering

### PROMPT 8: Module Discovery and Analysis

🟦 **LEGACY MODULE DECOMPOSITION**

Analyze and document each module in the legacy system:

**INPUT**: Legacy system codebase + business process documentation

**ANALYZE**:
- **MODULE IDENTIFICATION**: Controllers, services, models, utilities by functional area
- **DEPENDENCY MAPPING**: Inter-module dependencies, shared components, utilities
- **BUSINESS LOGIC EXTRACTION**: Core business rules, calculations, workflows
- **DATA FLOW ANALYSIS**: Input/output patterns, data transformations, persistence
- **UI COMPONENT ANALYSIS**: Views, partial views, JavaScript components, CSS classes
- **INTEGRATION POINTS**: External APIs, file system access, email services
- **CONFIGURATION DEPENDENCIES**: App settings, connection strings, environment variables
- **ERROR HANDLING PATTERNS**: Exception handling, logging, user notifications

**OUTPUT**: `Migration\Docs\08-Module-Analysis\` folder with:
- `Module-Inventory-Analysis.md` - Complete module catalog and functional analysis
- `Dependency-Integration-Mapping.md` - Inter-module dependencies and external integrations
- `Business-Logic-Extraction.md` - Core business rules and processing logic documentation

**NEXT DEPENDENCY**: Feeds into Pseudo Code Generation

---

### PROMPT 9: Pseudo Code Generation

🟦 **BUSINESS LOGIC PSEUDO CODE EXTRACTION**

Convert legacy business logic into technology-agnostic pseudo code:

**INPUT**: Module analysis + business logic catalog + legacy codebase

**GENERATE**:
- **ALGORITHM PSEUDO CODE**: Core business calculations and processing logic
- **WORKFLOW PSEUDO CODE**: Step-by-step process flows and decision trees
- **VALIDATION PSEUDO CODE**: Input validation rules and business rule checks
- **DATA TRANSFORMATION PSEUDO CODE**: Data mapping, formatting, and conversion logic
- **INTEGRATION PSEUDO CODE**: External service calls and data exchange patterns
- **ERROR HANDLING PSEUDO CODE**: Exception scenarios and recovery procedures
- **AUTHORIZATION PSEUDO CODE**: Access control and permission checking logic
- **REPORTING PSEUDO CODE**: Data aggregation and report generation logic

**OUTPUT**: `Migration\Docs\09-Pseudo-Code\` folder with:
- `Business-Logic-Pseudo-Code.md` - Core algorithms and business processing logic
- `Workflow-Validation-Pseudo-Code.md` - Process flows, decision trees, and validation rules
- `Integration-Error-Handling-Pseudo-Code.md` - External integrations and error management logic

**NEXT DEPENDENCY**: Feeds into Call Hierarchy Mapping

---

### PROMPT 10: Call Hierarchy Mapping

🟦 **SYSTEM CALL FLOW ANALYSIS**

Map the complete call hierarchy and execution flows:

**INPUT**: Module analysis + pseudo code + legacy codebase

**MAP**:
- **EXECUTION PATHS**: User action to database interaction complete flows
- **METHOD CALL CHAINS**: Controller → Service → Repository → Database patterns
- **EVENT HANDLING FLOWS**: User interactions, system events, scheduled tasks
- **DATA FLOW DIAGRAMS**: Data movement through system layers
- **ASYNC OPERATION FLOWS**: Background tasks, queued operations, scheduled jobs
- **ERROR PROPAGATION PATHS**: Exception handling and error recovery flows
- **TRANSACTION BOUNDARIES**: Database transaction scopes and rollback scenarios
- **CACHING INTERACTION FLOWS**: Cache read/write patterns and invalidation

**OUTPUT**: `Migration\Docs\10-Call-Hierarchy\` folder with:
- `System-Execution-Flow-Maps.md` - Complete execution paths and method call chains
- `Data-Event-Flow-Analysis.md` - Data movement, event handling, and async operation flows
- `Transaction-Error-Flow-Mapping.md` - Transaction boundaries and error propagation patterns

**NEXT DEPENDENCY**: Feeds into Migration Implementation Prompts

---

## 📋 PHASE 5: Step-by-Step Migration Implementation

### PROMPT 11: Migration Implementation Prompts

🟦 **PROGRESSIVE MIGRATION EXECUTION**

Create step-by-step implementation prompts for systematic migration:

**INPUT**: All previous analysis + target architecture + testing strategy

**CREATE**:
- **INFRASTRUCTURE SETUP PROMPTS**: Development environment, database setup, CI/CD pipeline
- **FOUNDATION LAYER PROMPTS**: Authentication, authorization, logging, error handling
- **DATA LAYER PROMPTS**: Database models, repositories, data access patterns
- **API LAYER PROMPTS**: REST endpoints, GraphQL resolvers, middleware
- **BUSINESS LOGIC PROMPTS**: Service layer implementation, business rule enforcement
- **FRONTEND COMPONENT PROMPTS**: React components, state management, routing
- **INTEGRATION PROMPTS**: External service integrations, third-party APIs
- **TESTING IMPLEMENTATION PROMPTS**: Unit tests, integration tests, E2E tests

**OUTPUT**: `Migration\Docs\11-Implementation-Guide\` folder with:
- `Infrastructure-Foundation-Setup-Prompts.md` - Environment setup, authentication, and foundational layer implementation
- `Backend-API-Implementation-Prompts.md` - Data layer, API development, and business logic implementation
- `Frontend-UI-Migration-Prompts.md` - React components, state management, and UI migration
- `Integration-Testing-Implementation-Prompts.md` - External integrations and comprehensive testing implementation

**NEXT DEPENDENCY**: Feeds into Code Rules Definition

---

## 📋 PHASE 6: Code Standards & Guidelines

### PROMPT 12: Migration Code Rules & Standards

🟦 **DEVELOPMENT STANDARDS DEFINITION**

Establish comprehensive coding standards and guidelines for the migration:

**INPUT**: Target architecture + implementation prompts + best practices

**DEFINE**:
- **REACT CODING STANDARDS**: Component structure, hooks usage, state management patterns
- **NODE.JS CODING STANDARDS**: Module structure, async/await patterns, error handling
- **API DESIGN STANDARDS**: REST conventions, response formats, error codes
- **DATABASE STANDARDS**: Naming conventions, query optimization, migration scripts
- **SECURITY STANDARDS**: Input validation, authentication patterns, data encryption
- **TESTING STANDARDS**: Test structure, coverage requirements, mocking patterns
- **DOCUMENTATION STANDARDS**: Code comments, API documentation, README formats
- **GIT WORKFLOW STANDARDS**: Branch naming, commit messages, pull request guidelines

**OUTPUT**: `Migration\Docs\12-Code-Standards\` folder with:
- `Frontend-Backend-Coding-Standards.md` - React and Node.js development guidelines and patterns
- `API-Database-Standards.md` - API design principles and database development standards
- `Security-Testing-Standards.md` - Security implementation and testing guidelines
- `Documentation-Workflow-Standards.md` - Code documentation and Git workflow standards

**FINAL OUTPUT**: Complete migration framework ready for implementation

---

## 🎯 Execution Sequence

**Phase 1 → Phase 2 → Phase 3 → Phase 4 → Phase 5 → Phase 6**

Each phase builds upon the previous phase outputs, creating a comprehensive migration framework that ensures systematic, risk-aware, and quality-focused migration from legacy .NET MVC to modern React/Node.js architecture.

---

## 📊 Success Metrics

- **Functional Parity**: 100% business functionality preserved
- **Performance**: Target system meets or exceeds legacy performance
- **Security**: Enhanced security posture with modern authentication
- **Maintainability**: Clean architecture with comprehensive documentation
- **User Experience**: Preserved UI/UX with modern responsiveness

---

## 📁 Optimized Documentation Structure

```
Migration/
└── Docs/
    ├── 01-Legacy-Analysis/ (5 files)
    ├── 02-Business-Processes/ (3 files)
    ├── 03-Risk-Assessment/ (3 files)
    ├── 04-Migration-Strategy/ (4 files)
    ├── 05-Target-Architecture/ (5 files)
    ├── 06-Database-Migration/ (3 files)
    ├── 07-Testing-Strategy/ (4 files)
    ├── 08-Module-Analysis/ (3 files)
    ├── 09-Pseudo-Code/ (3 files)
    ├── 10-Call-Hierarchy/ (3 files)
    ├── 11-Implementation-Guide/ (4 files)
    └── 12-Code-Standards/ (4 files)
```

**Total: 44 well-organized, comprehensive documentation files across 12 phases**

This optimized structure provides:
- **Manageable file count**: 44 focused files instead of 100+
- **Comprehensive coverage**: All aspects covered without overwhelming detail
- **Easy navigation**: Clear structure with logical grouping
- **Practical implementation**: Each file serves a specific, actionable purpose
- **Better collaboration**: Easier for teams to work with and maintain