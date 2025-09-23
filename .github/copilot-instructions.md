# SmartStore.NET to React Migration Project

**ALWAYS follow these instructions first and fallback to additional search and context gathering only when the information here is incomplete or found to be in error.**

This repository contains a comprehensive migration project from Legacy .NET MVC 4.7 SmartStore.NET to React + .NET 8 API architecture. The project is currently in the planning/documentation phase with extensive migration analysis complete.

## Repository Structure Overview

```
/
├── Legacy-Source/           # Original .NET MVC 4.7 SmartStore.NET code (Windows-only)
├── Migration/              # Migration planning and documentation
│   └── Docs/              # Comprehensive migration analysis and plans
├── README.md              # Basic repository description
└── .github/               # GitHub configuration and this instructions file
```

## Current Project Status

- **Phase**: Planning and Documentation Complete
- **Legacy Code**: Cannot build on Linux (requires Windows/.NET Framework 4.7.2)
- **Target System**: React 18+ frontend with .NET 8 API backend (not yet implemented)
- **Migration Strategy**: Phased migration with parallel systems over 18 months

## Working Effectively

### Build Status and Limitations

**CRITICAL: Legacy .NET Framework code cannot be built on Linux systems.**

- **DO NOT** attempt to build Legacy-Source/ projects on Linux
- **Legacy system requires**: Windows + Visual Studio + .NET Framework 4.7.2
- **Error expected**: "The reference assemblies for .NETFramework,Version=v4.7.2 were not found"
- **Mono installation**: Does not resolve the build issues
- **Timeline impact**: Legacy build issues do not affect migration timeline

### Repository Analysis and Navigation

When analyzing the codebase:

1. **Start with migration documentation**:
   ```bash
   # View migration overview
   cat Migration/Docs/Migration-Implementation-Phase-Prompts_Version2.md
   
   # Review legacy analysis
   find Migration/Docs/01-Legacy-Analysis -name "*.md" | head -5
   
   # Check migration roadmap
   cat Migration/Docs/04-Migration-Strategy/Migration-Roadmap-Plan.md
   ```

2. **Understand legacy architecture** (analysis only):
   ```bash
   # View legacy solution structure
   find Legacy-Source -name "*.sln" -o -name "*.csproj" | head -10
   
   # Examine key legacy components
   ls Legacy-Source/Libraries/
   ls Legacy-Source/Presentation/
   ls Legacy-Source/Tests/
   ```

3. **NEVER** run these commands (they will fail on Linux):
   - `dotnet build SmartStoreNET.sln`
   - `msbuild SmartStoreNET.sln`
   - Any build commands in Legacy-Source/

### Future Development Environment Setup

**When React/Node.js implementation begins**, prepare the environment:

1. **Prerequisites validation**:
   ```bash
   # Verify Node.js (should be v18.0.0+)
   node --version
   
   # Verify npm (should be v9.0.0+)
   npm --version
   
   # Verify .NET 8 (for future API development)
   dotnet --version
   ```

2. **Project initialization** (when migration implementation starts):
   ```bash
   # Create modern project structure
   mkdir -p frontend backend shared docs
   
   # Initialize React frontend (TypeScript)
   cd frontend
   npx create-react-app . --template typescript
   npm install
   # Build takes ~3-5 minutes. NEVER CANCEL. Set timeout to 10+ minutes.
   npm run build
   
   # Initialize .NET 8 API backend
   cd ../backend
   dotnet new webapi -n SmartStore.API
   cd SmartStore.API
   dotnet build
   # Build takes ~1-2 minutes. Set timeout to 5+ minutes.
   ```

3. **Development workflow** (future):
   ```bash
   # Frontend development
   cd frontend
   npm run start  # Development server on http://localhost:3000
   
   # Backend development
   cd backend/SmartStore.API
   dotnet run     # API server on https://localhost:7000
   
   # Run tests
   npm run test   # Frontend tests
   dotnet test    # Backend tests
   ```

### Migration Documentation Workflow

**Always reference the migration documentation for context**:

1. **Before making any changes**:
   ```bash
   # Review relevant migration phase
   cat Migration/Docs/Migration-Implementation-Phase-Prompts_Version2.md
   
   # Check business requirements
   find Migration/Docs/02-Business-Processes -name "*.md" | xargs cat
   
   # Validate against migration roadmap
   grep -n "STEP\|Phase\|Timeline" Migration/Docs/04-Migration-Strategy/Migration-Roadmap-Plan.md
   ```

2. **Common migration reference files**:
   - `Migration/Docs/04-Migration-Strategy/Migration-Roadmap-Plan.md` - Overall strategy and timeline
   - `Migration/Docs/Migration-Implementation-Phase-Prompts_Version2.md` - Step-by-step implementation guides
   - `Legacy-NET-MVC-Migration-Analysis-Prompts (2).md` - Migration analysis framework

## Validation Scenarios

### Current Phase Validation

**Since no React/Node.js code exists yet**, validation focuses on documentation and planning:

1. **Migration documentation completeness**:
   ```bash
   # Verify all migration docs exist
   find Migration/Docs -type f -name "*.md" | wc -l
   # Should return 44+ documentation files
   
   # Check for key strategy documents
   test -f Migration/Docs/04-Migration-Strategy/Migration-Roadmap-Plan.md && echo "✅ Migration roadmap exists"
   test -f Migration/Docs/Migration-Implementation-Phase-Prompts_Version2.md && echo "✅ Implementation prompts exist"
   ```

2. **Legacy system analysis validation**:
   ```bash
   # Verify legacy solution structure
   find Legacy-Source -name "*.sln" | head -1 | xargs test -f && echo "✅ Legacy solution found"
   
   # Confirm project count matches analysis
   find Legacy-Source -name "*.csproj" | wc -l
   # Should match project count in migration docs
   ```

### Future Implementation Validation

**When React/Node.js implementation begins**, always validate:

1. **Complete development workflow**:
   ```bash
   # Frontend validation scenario
   cd frontend
   npm install
   npm run build  # NEVER CANCEL: Takes 3-5 minutes
   npm run test   # NEVER CANCEL: Takes 2-4 minutes
   npm run start &  # Start dev server
   
   # Test frontend loads successfully
   sleep 10
   curl -f http://localhost:3000 || echo "❌ Frontend not responding"
   
   # Backend validation scenario  
   cd ../backend/SmartStore.API
   dotnet restore
   dotnet build   # NEVER CANCEL: Takes 1-2 minutes
   dotnet test    # NEVER CANCEL: Takes 2-3 minutes
   dotnet run &   # Start API server
   
   # Test API health endpoint
   sleep 5
   curl -f https://localhost:7000/health || echo "❌ API not responding"
   ```

2. **Integration validation**:
   ```bash
   # Test frontend can connect to API
   curl -f http://localhost:3000/api/health || echo "❌ Frontend-API integration failed"
   
   # Verify database connection (when implemented)
   cd backend/SmartStore.API
   dotnet ef database update  # Apply migrations
   ```

## Build and Test Requirements

### Current Phase (Documentation Only)

- **No builds required** - repository is in planning phase
- **Linting**: Use markdown linters for documentation
  ```bash
  # Install markdown linter (if not present)
  npm install -g markdownlint-cli
  
  # Lint migration documentation
  markdownlint Migration/Docs/**/*.md
  markdownlint *.md
  ```

### Future Phase Build Requirements

**NEVER CANCEL builds or tests**. All timing estimates include 50% buffer:

1. **Frontend builds**:
   - `npm install`: 2-3 minutes (timeout: 5 minutes)
   - `npm run build`: 3-5 minutes (timeout: 10 minutes)
   - `npm run test`: 2-4 minutes (timeout: 8 minutes)

2. **Backend builds**:
   - `dotnet restore`: 30-60 seconds (timeout: 3 minutes)
   - `dotnet build`: 1-2 minutes (timeout: 5 minutes) 
   - `dotnet test`: 2-3 minutes (timeout: 6 minutes)

3. **Full integration tests**: 5-10 minutes (timeout: 20 minutes)

## Migration Implementation Guidelines

### Development Approach

1. **Always follow the migration phases**:
   - Foundation Phase (Months 1-3): Infrastructure and tooling
   - Core Migration Phase (Months 4-12): React frontend + .NET 8 API
   - Integration Phase (Months 13-15): Third-party integrations
   - Transition Phase (Months 16-18): Production deployment

2. **Implementation validation**:
   - **ALWAYS** implement according to Migration/Docs specifications
   - **ALWAYS** validate against legacy business rules
   - **ALWAYS** test complete user scenarios end-to-end
   - **NEVER** skip validation steps due to time constraints

### Key Technology Decisions

Based on migration analysis:

- **Frontend**: React 18+ with TypeScript
- **Backend**: .NET 8 Web API with Clean Architecture
- **Database**: SQL Server (maintain compatibility)
- **Authentication**: JWT-based with role-based access control
- **Testing**: Jest (frontend), xUnit (.NET), Playwright (E2E)

### Critical Validation Points

1. **Business Logic Preservation**:
   ```bash
   # When implementing, always cross-reference with legacy
   grep -r "business rule\|validation\|calculation" Migration/Docs/02-Business-Processes/
   ```

2. **Security Implementation**:
   ```bash
   # Validate security requirements
   cat Migration/Docs/03-Risk-Assessment/*.md | grep -i "security\|authentication\|authorization"
   ```

3. **Performance Benchmarks**:
   - Legacy system response time baselines in migration docs
   - New system must meet or exceed legacy performance
   - Load testing required before each phase completion

## Common Tasks and Reference

### Migration Documentation Navigation

```bash
# Repository root structure
ls -la /home/runner/work/1_SmartStoreNET_React/1_SmartStoreNET_React/
# Output: Legacy-Source/ Migration/ README.md .github/ .gitignore .vscode/

# Migration documentation overview  
find Migration/Docs -maxdepth 1 -type d
# Output: 01-Legacy-Analysis/ 02-Business-Processes/ 03-Risk-Assessment/ 04-Migration-Strategy/

# Legacy project structure
find Legacy-Source -maxdepth 2 -type d | grep -E "(Libraries|Presentation|Tests)"
# Output: Libraries/SmartStore.Core/ Presentation/SmartStore.Web/ Tests/ etc.
```

### Migration Phase Checklist

Before implementing any migration phase:

- [ ] Read relevant Migration/Docs phase documentation
- [ ] Review legacy system analysis for the component
- [ ] Understand business rules and validation requirements  
- [ ] Plan validation scenarios before coding
- [ ] Set appropriate timeouts for all build/test commands
- [ ] Test complete user workflows, not just technical functionality
- [ ] Document any deviations from migration plan
- [ ] Validate against performance and security requirements

## Error Handling and Known Issues

### Expected Failures

1. **Legacy build failures on Linux** - This is normal and expected
2. **Missing .NET Framework references** - Cannot be resolved on Linux
3. **Web application targets not found** - Windows/Visual Studio specific

### DO NOT attempt to fix:
- .NET Framework 4.7.2 build issues on Linux
- Missing Visual Studio web application targets
- Windows-specific package restore problems

### When to seek help:
- Migration documentation inconsistencies
- Business rule interpretation conflicts  
- Technology stack decision conflicts with migration analysis
- Timeline or resource constraint impacts on quality

---

**Remember**: This is a comprehensive 18-month, $3.2M migration project. Every implementation decision should align with the documented migration strategy and business requirements. Never compromise on validation or skip testing phases to meet artificial deadlines.