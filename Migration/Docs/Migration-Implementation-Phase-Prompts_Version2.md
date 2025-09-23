# 📋 NEXT STEPS: Implementation Phase Prompts

## 🚀 Migration Implementation Overview
**Purpose**: Step-by-step AI Agent prompts to execute the actual migration from Legacy .NET MVC 4.7 to React + Node.js  
**Context**: Using all Migration\Docs\ analysis files as reference  
**Approach**: Progressive implementation with validation at each step  

---

## 📁 STEP 1: Local Repository Setup and Environment Preparation

### 🟦 DEVELOPMENT ENVIRONMENT SETUP

**PROMPT FOR AI AGENT:**

```
I have a legacy .NET MVC 4.7 project that I want to migrate to React + Node.js. I've completed the analysis phase and have comprehensive documentation in Migration\Docs\ folder.

Please help me:
1. Create a proper folder structure for the new React + Node.js project alongside my legacy project
2. Set up the development environment with all necessary tools
3. Initialize Git repository with proper .gitignore files
4. Set up package.json for both frontend and backend
5. Configure development tools (ESLint, Prettier, Jest, etc.)

**Context Files to Reference:**
- Migration\Docs\05-Target-Architecture\Target-Architecture-Design.md
- Migration\Docs\12-Code-Standards\Migration-Code-Rules-Standards.md
- Migration\Docs\11-Implementation-Prompts\Step-by-Step-Implementation-Prompts.md

**Requirements:**
- Clean Architecture pattern for Node.js backend
- React 18+ for frontend with TypeScript
- Modern tooling and best practices
- Separate frontend/backend folders but single repository
- Development and production environment configurations
```

**EXPECTED OUTPUT:**
- Complete folder structure
- Package.json files with all dependencies
- Configuration files (tsconfig, eslint, prettier, jest)
- Development scripts and commands
- Environment setup instructions

---

## 📁 STEP 2: Database Analysis and Target Database Setup

### 🟦 DATABASE MIGRATION PREPARATION

**PROMPT FOR AI AGENT:**

```
Based on my legacy system analysis, I need to set up the target database for migration.

Please help me:
1. Analyze the existing SQL Server database schema from Migration\Docs\01-Legacy-Analysis\Database-Schema-Analysis.md
2. Recommend the best target database (PostgreSQL/MySQL/MongoDB) based on my data patterns
3. Create database migration scripts to transform the schema
4. Set up database connection and ORM configuration
5. Create initial database models/entities for the new system

**Context Files to Reference:**
- Migration\Docs\01-Legacy-Analysis\Database-Schema-Analysis.md
- Migration\Docs\06-Database-Migration\Database-Migration-Plan.md
- Migration\Docs\05-Target-Architecture\Data-Layer-Design.md

**Provide:**
- Database selection rationale
- Schema transformation scripts
- ORM setup (Prisma/TypeORM/Sequelize)
- Initial model definitions
- Database seeding scripts for development
```

**EXPECTED OUTPUT:**
- Target database recommendation with justification
- Complete schema migration scripts
- ORM configuration and models
- Database connection setup
- Development data seeding

---

## 📁 STEP 3: Authentication and Security Foundation

### 🟦 SECURITY INFRASTRUCTURE SETUP

**PROMPT FOR AI AGENT:**

```
I need to implement the authentication and security layer as the foundation of my new system.

Please help me:
1. Analyze current authentication patterns from my legacy system
2. Implement JWT-based authentication system
3. Set up role-based access control (RBAC)
4. Create authentication middleware and guards
5. Implement input validation and security headers
6. Set up password hashing and session management

**Context Files to Reference:**
- Migration\Docs\01-Legacy-Analysis\Security-Analysis.md
- Migration\Docs\05-Target-Architecture\Security-Architecture-Design.md
- Migration\Docs\12-Code-Standards\Security-Implementation-Standards.md

**Requirements:**
- JWT token implementation
- Role-based permissions matching legacy system
- Secure password handling
- API security middleware
- Frontend authentication context
```

**EXPECTED OUTPUT:**
- Complete authentication system
- Authorization middleware
- Security configuration
- Frontend auth components
- Security testing setup

---

## 📁 STEP 4: API Layer Development

### 🟦 REST API IMPLEMENTATION

**PROMPT FOR AI AGENT:**

```
Now I need to create the API layer that will replace my legacy MVC controllers.

Please help me:
1. Convert legacy MVC controllers to REST API endpoints
2. Implement clean architecture layers (Controller → Service → Repository)
3. Set up API routing and middleware
4. Implement error handling and logging
5. Create API documentation (OpenAPI/Swagger)
6. Set up API validation and serialization

**Context Files to Reference:**
- Migration\Docs\08-Module-Analysis\Module-Discovery-Analysis.md
- Migration\Docs\09-Pseudo-Code\Business-Logic-Pseudo-Code.md
- Migration\Docs\05-Target-Architecture\API-Design-Specification.md
- Migration\Docs\10-Call-Hierarchy\Call-Hierarchy-Flow-Maps.md

**For each legacy controller, provide:**
- REST endpoint design
- Request/response models
- Service layer implementation
- Repository pattern implementation
- Error handling
- Unit tests
```

**EXPECTED OUTPUT:**
- Complete REST API structure
- Service and repository layers
- API documentation
- Error handling system
- Comprehensive API tests

---

## 📁 STEP 5: Business Logic Migration

### 🟦 CORE BUSINESS LOGIC IMPLEMENTATION

**PROMPT FOR AI AGENT:**

```
I need to migrate all business logic from my legacy system to the new Node.js backend.

Please help me:
1. Convert pseudo code from analysis into actual TypeScript/JavaScript
2. Implement all business rules and calculations
3. Migrate workflow logic and decision trees
4. Convert data validation and transformation logic
5. Implement background jobs and scheduled tasks
6. Set up business logic unit tests

**Context Files to Reference:**
- Migration\Docs\09-Pseudo-Code\Business-Logic-Pseudo-Code.md
- Migration\Docs\02-Business-Processes\Business-Process-Documentation.md
- Migration\Docs\08-Module-Analysis\Business-Logic-Extraction.md

**For each business process:**
- Service implementation
- Business rule validation
- Data transformation logic
- Error handling and recovery
- Unit test coverage
- Integration with API layer
```

**EXPECTED OUTPUT:**
- Complete business logic services
- Validation and transformation functions
- Background job implementations
- Comprehensive unit tests
- Business logic documentation

---

## 📁 STEP 6: Data Migration and Synchronization

### 🟦 DATA MIGRATION EXECUTION

**PROMPT FOR AI AGENT:**

```
I need to migrate all data from my legacy SQL Server database to the new target database.

Please help me:
1. Create ETL scripts for data migration
2. Implement data validation and integrity checks
3. Set up data synchronization during transition period
4. Create rollback procedures for data recovery
5. Migrate stored procedure logic to application layer
6. Validate migrated data against business rules

**Context Files to Reference:**
- Migration\Docs\06-Database-Migration\Database-Migration-Plan.md
- Migration\Docs\01-Legacy-Analysis\Database-Schema-Analysis.md
- Migration\Docs\03-Risk-Assessment\Risk-Assessment-Report.md

**Provide:**
- Complete ETL scripts
- Data validation procedures
- Synchronization strategies
- Rollback mechanisms
- Performance optimization
- Migration progress tracking
```

**EXPECTED OUTPUT:**
- ETL migration scripts
- Data validation tools
- Synchronization mechanisms
- Rollback procedures
- Migration monitoring

---

## 📁 STEP 7: Frontend Component Development

### 🟦 REACT FRONTEND IMPLEMENTATION

**PROMPT FOR AI AGENT:**

```
I need to recreate my legacy MVC views as React components while preserving the existing UI/UX.

Please help me:
1. Convert Razor views to React components
2. Migrate existing CSS and Bootstrap styles
3. Implement state management (Redux/Context API)
4. Set up routing and navigation
5. Create reusable UI components
6. Implement form handling and validation

**Context Files to Reference:**
- Migration\Docs\01-Legacy-Analysis\UI-Component-Inventory.md
- Migration\Docs\05-Target-Architecture\Frontend-Architecture-Design.md
- Migration\Docs\02-Business-Processes\User-Journey-Maps.md
- Migration\Docs\12-Code-Standards\Frontend-Coding-Standards.md

**For each legacy view:**
- React component structure
- State management setup
- CSS/Bootstrap migration
- Form validation
- API integration
- Component tests
```

**EXPECTED OUTPUT:**
- Complete React component library
- State management setup
- CSS/Bootstrap integration
- Form handling system
- Component test suite

---

## 📁 STEP 8: Integration and Third-Party Services

### 🟦 EXTERNAL INTEGRATION IMPLEMENTATION

**PROMPT FOR AI AGENT:**

```
I need to migrate all external integrations and third-party service connections.

Please help me:
1. Migrate external API integrations
2. Convert file system operations to cloud storage
3. Implement email service integration
4. Set up logging and monitoring services
5. Migrate reporting and export functionality
6. Implement caching layer (Redis)

**Context Files to Reference:**
- Migration\Docs\08-Module-Analysis\External-Integration-Points.md
- Migration\Docs\05-Target-Architecture\System-Architecture-Overview.md
- Migration\Docs\09-Pseudo-Code\Integration-Logic-Pseudo-Code.md

**For each integration:**
- Service connection setup
- Error handling and retry logic
- Authentication and authorization
- Data transformation
- Monitoring and logging
- Integration tests
```

**EXPECTED OUTPUT:**
- External service integrations
- File handling system
- Email service setup
- Caching implementation
- Monitoring configuration

---

## 📁 STEP 9: Testing Implementation

### 🟦 COMPREHENSIVE TESTING SETUP

**PROMPT FOR AI AGENT:**

```
I need to implement comprehensive testing to ensure migration quality and reliability.

Please help me:
1. Set up unit testing for all components and services
2. Implement integration tests for API endpoints
3. Create end-to-end tests for critical user workflows
4. Set up performance and load testing
5. Implement security testing suite
6. Create data validation and migration tests

**Context Files to Reference:**
- Migration\Docs\07-Testing-Strategy\Testing-Strategy-Framework.md
- Migration\Docs\02-Business-Processes\User-Journey-Maps.md
- Migration\Docs\12-Code-Standards\Testing-Standards-Guidelines.md

**Testing Requirements:**
- 80%+ code coverage for unit tests
- API endpoint integration tests
- E2E tests for all user journeys
- Performance benchmarking
- Security vulnerability scanning
- Automated test execution in CI/CD
```

**EXPECTED OUTPUT:**
- Complete test suite setup
- Automated testing pipeline
- Performance testing framework
- Security testing tools
- Test coverage reporting

---

## 📁 STEP 10: Performance Optimization and Monitoring

### 🟦 PERFORMANCE AND MONITORING SETUP

**PROMPT FOR AI AGENT:**

```
I need to optimize performance and set up monitoring to ensure the new system meets or exceeds legacy performance.

Please help me:
1. Implement performance monitoring and metrics
2. Set up application logging and error tracking
3. Optimize database queries and connections
4. Implement caching strategies
5. Set up API rate limiting and throttling
6. Configure production monitoring and alerting

**Context Files to Reference:**
- Migration\Docs\01-Legacy-Analysis\Performance-Baseline.md
- Migration\Docs\05-Target-Architecture\System-Architecture-Overview.md
- Migration\Docs\12-Code-Standards\Performance-Optimization-Standards.md

**Performance Goals:**
- Response times ≤ legacy system
- 99.9% uptime target
- Scalable architecture
- Efficient resource utilization
- Real-time monitoring
- Proactive alerting
```

**EXPECTED OUTPUT:**
- Performance monitoring setup
- Logging and error tracking
- Caching implementation
- Rate limiting configuration
- Production monitoring

---

## 📁 STEP 11: Deployment and DevOps Setup

### 🟦 PRODUCTION DEPLOYMENT PREPARATION

**PROMPT FOR AI AGENT:**

```
I need to prepare for production deployment with proper DevOps practices.

Please help me:
1. Set up CI/CD pipeline for automated deployment
2. Configure containerization (Docker) for both frontend and backend
3. Set up environment management (dev, staging, production)
4. Implement database migration scripts for production
5. Configure SSL/TLS and security headers
6. Set up backup and disaster recovery procedures

**Context Files to Reference:**
- Migration\Docs\04-Migration-Strategy\Migration-Strategy-Roadmap.md
- Migration\Docs\03-Risk-Assessment\Rollback-Strategy.md
- Migration\Docs\12-Code-Standards\Version-Control-Standards.md

**Deployment Requirements:**
- Zero-downtime deployment
- Automated rollback capability
- Environment-specific configurations
- Security hardening
- Monitoring and alerting
- Backup and recovery procedures
```

**EXPECTED OUTPUT:**
- CI/CD pipeline configuration
- Docker containers setup
- Environment configurations
- Deployment scripts
- Backup and recovery procedures

---

## 📁 STEP 12: User Acceptance and Go-Live

### 🟦 FINAL MIGRATION EXECUTION

**PROMPT FOR AI AGENT:**

```
I'm ready for the final migration phase and user acceptance testing.

Please help me:
1. Create user acceptance testing scenarios
2. Set up production data migration procedures
3. Implement feature flags for gradual rollout
4. Prepare user training materials and documentation
5. Set up production monitoring and support procedures
6. Create go-live checklist and rollback procedures

**Context Files to Reference:**
- Migration\Docs\04-Migration-Strategy\Success-Criteria.md
- Migration\Docs\07-Testing-Strategy\User-Acceptance-Testing.md
- Migration\Docs\03-Risk-Assessment\Risk-Assessment-Report.md

**Go-Live Requirements:**
- All UAT scenarios passed
- Performance benchmarks met
- Security requirements validated
- User training completed
- Support procedures in place
- Rollback plan tested
```

**EXPECTED OUTPUT:**
- UAT test scenarios and results
- Production migration procedures
- Feature flag implementation
- User training materials
- Go-live checklist and procedures

---

## 🎯 Implementation Execution Order

**STEP 1** → Environment Setup  
**STEP 2** → Database Setup  
**STEP 3** → Authentication Foundation  
**STEP 4** → API Layer Development  
**STEP 5** → Business Logic Migration  
**STEP 6** → Data Migration  
**STEP 7** → Frontend Development  
**STEP 8** → External Integrations  
**STEP 9** → Testing Implementation  
**STEP 10** → Performance & Monitoring  
**STEP 11** → Deployment Setup  
**STEP 12** → Go-Live Execution  

---

## 📊 Success Validation Criteria

- ✅ **Functional Parity**: All legacy features working in new system
- ✅ **Performance**: New system meets or exceeds legacy performance benchmarks
- ✅ **Security**: Enhanced security posture with modern authentication
- ✅ **User Experience**: UI/UX preserved with improved responsiveness
- ✅ **Data Integrity**: All data migrated successfully with validation
- ✅ **Test Coverage**: Comprehensive test suite with 80%+ coverage
- ✅ **Documentation**: Complete technical and user documentation
- ✅ **Monitoring**: Production monitoring and alerting in place
- ✅ **Support**: User training and support procedures established
- ✅ **Rollback**: Tested rollback procedures for emergency scenarios

---

## 🔄 Iterative Development Notes

Each step should be:
1. **Implemented incrementally** with frequent validation
2. **Tested thoroughly** before moving to next step
3. **Documented** with progress updates
4. **Reviewed** against original requirements
5. **Validated** with stakeholders when appropriate

**Remember**: Reference the Migration\Docs\ folder for all context and requirements throughout implementation.