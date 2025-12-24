# Rust Copilot Instruction File - Planning Document

**Date**: December 24, 2025  
**Purpose**: Create comprehensive copilot instruction file for Rust mono-workspace template targeting Azure SDK development

---

## Architectural Decisions

### Core Decisions Made
1. **Service Scope**: Service-agnostic (not tied to specific Azure services)
2. **Async Runtime**: Tokio (required for Azure SDK compatibility)
3. **Error Handling**: Standard library `Result` type with `?` operator
4. **Authentication**: `ChainedTokenCredential` for Azure authentication
5. **Workspace Type**: Mono-workspace (virtual workspace pattern)
6. **Documentation Style**: Include code examples for 80% use case coverage
7. **CI/CD**: Specify GitHub Actions workflows with rust-cache, clippy, rustfmt

### Rationale

#### Why Tokio?
- Azure SDK for Rust is built on tokio runtime
- Industry standard for async Rust applications
- Excellent ecosystem support and documentation
- Performance and reliability proven at scale

#### Why Standard Result?
- Built-in, zero-dependency error handling
- Idiomatic Rust approach
- Works seamlessly with `?` operator
- Avoids external error crate dependencies (anyhow, thiserror) which can be added per-project need

#### Why ChainedTokenCredential?
- Supports multiple authentication methods in priority order
- Development: Azure CLI → Production: Managed Identity → Service Principal
- Single pattern works across environments
- Follows Azure SDK best practices

#### Why Mono-Workspace?
- Single `Cargo.lock` for consistent dependencies
- Shared build cache improves compile times
- Centralized dependency management in root `Cargo.toml`
- Enables code sharing between crates
- Industry standard for multi-crate Rust projects

#### Why Code Examples?
- Reduces ambiguity for 80% of users
- Provides copy-paste starting points
- Frontend Vibes template demonstrated effectiveness of concrete examples
- Faster onboarding for developers new to Rust or Azure SDK

---

## Source Analysis

### Frontend Vibes Template Analysis

**Structure**: 93 lines, very detailed and prescriptive

**Key Strengths**:
- Specific technology stack declaration
- Concrete file path examples
- Tool recommendations with IDs
- Architecture patterns with code snippets
- Performance optimization guidance
- Planning workflow (todo lists)

**Sections**:
1. Stack - Tech specifications
2. Development Server - Setup details
3. Architecture Patterns - Component organization, responsive design, color system
4. Planning - Todo list usage
5. Error Monitoring Integration
6. Code Style and Structure - Functional patterns, naming
7. Naming Conventions - File/directory standards
8. Recommended Tools - Browser tools, Perplexity, library docs
9. UI and Styling - Shadcn UI, Lucide icons, Tailwind
10. Syntax and Formatting - Function syntax, JSX
11. Performance Optimization - RSC, Suspense, lazy loading
12. Key Conventions - LLM integration, 'use client' limits

**Tone**: Direct, prescriptive, technical

**Applicability to Rust**: Architecture patterns, planning workflow, tool recommendations, performance optimization mindset

---

### Azure SDK Rust Analysis

**Structure**: 52 lines, concise and focused

**Key Strengths**:
- Clear role definition ("expert Rust programmer")
- Comprehensive Rust style conventions
- Specific workspace dependency patterns
- Test generation guidelines
- Multi-line search awareness (rustfmt)
- Generated code protection rules

**Sections**:
1. Instructions - Role definition, tone guidance
2. Prerequisites - PowerShell for Azure SDK MCP tools
3. Code Generation - Comprehensive Rust coding standards
   - Naming conventions (PascalCase, snake_case, UPPER_SNAKE_CASE)
   - `use` directive patterns
   - Safety, efficiency, correctness priorities
   - Ownership and borrowing
   - Error handling with Result and `?`
   - Workspace dependency management
   - Documentation syntax (`///`)
   - Clippy validation
   - TODO comment usage
   - No wildcard imports
   - Generated code protection
4. Test Generation - Module structure, placement, naming

**Tone**: Informal, direct, "not overly apologetic"

**Applicability to Rust Template**: Nearly everything - this is Rust-specific guidance

---

### Rust API Guidelines Summary

**Source**: https://rust-lang.github.io/api-guidelines/

**Key Principles**:
- Naming conventions (C-CASE family)
- Type safety and API design
- Predictability and ergonomics
- Documentation standards
- Interoperability
- Flexibility and future-proofing

**Relevance**: Comprehensive API design patterns, should reference this resource in instruction file

---

### Cargo Workspace Documentation Summary

**Source**: https://doc.rust-lang.org/cargo/reference/workspaces.html

**Key Patterns**:

#### Virtual Workspace Structure:
```toml
# Root Cargo.toml
[workspace]
members = ["crates/*"]
resolver = "2"

[workspace.package]
version = "0.1.0"
edition = "2021"

[workspace.dependencies]
tokio = { version = "1.35", features = ["full"] }
```

#### Member Inheritance:
```toml
# Member Cargo.toml
[package]
version.workspace = true
edition.workspace = true

[dependencies]
tokio.workspace = true
```

**Key Benefits**:
- Shared `Cargo.lock`
- Common output directory (`target/`)
- Centralized dependency versions
- Package metadata inheritance
- Lints inheritance

---

## Workspace Member Naming Patterns

### Recommended Structure (80% Use Case)

```
my-project/
├── Cargo.toml                    # Virtual workspace root
├── Cargo.lock                    # Shared lock file
├── .github/
│   ├── copilot-instructions.md
│   └── workflows/
│       ├── ci.yml
│       └── release.yml
├── crates/
│   ├── my-project-core/          # Shared business logic, domain models
│   │   ├── Cargo.toml
│   │   └── src/
│   ├── my-project-api/           # Azure SDK integrations, API clients
│   │   ├── Cargo.toml
│   │   └── src/
│   ├── my-project-cli/           # CLI application (clap-based)
│   │   ├── Cargo.toml
│   │   └── src/
│   └── my-project-web/           # Web service (axum/actix-web)
│       ├── Cargo.toml
│       └── src/
└── README.md
```

### Naming Convention Rationale

1. **`{project}-core`**: Shared business logic, domain models, utilities
   - No external service dependencies
   - Pure Rust logic
   - Testable in isolation

2. **`{project}-api`**: Azure SDK integration layer
   - Azure service clients
   - Authentication setup
   - Service-specific logic

3. **`{project}-cli`**: Command-line interface
   - Uses `clap` or `structopt`
   - Depends on `-core` and `-api`
   - Entry point: `src/main.rs`

4. **`{project}-web`**: Web service
   - Uses `axum`, `actix-web`, or `rocket`
   - REST or GraphQL APIs
   - Depends on `-core` and `-api`

### Alternative Patterns

- **Flat structure** (smaller projects): `src/core/`, `src/api/`, `src/cli/`
- **Feature-based**: `{project}-storage`, `{project}-compute`, `{project}-messaging`
- **Layer-based**: `{project}-domain`, `{project}-infrastructure`, `{project}-presentation`

---

## Content Sources for Instruction File

### Primary Sources
1. ✅ **Azure SDK for Rust** - `.github/copilot-instructions.md`
   - Rust-specific conventions
   - Workspace patterns
   - Test generation
   - Azure SDK MCP prerequisites

2. ✅ **Frontend Vibes Template** - `.github/copilot-instructions.md`
   - Structure and organization approach
   - Tool recommendations pattern
   - Planning workflow (todo lists)
   - Example code inclusion strategy

3. ✅ **Rust API Guidelines** - https://rust-lang.github.io/api-guidelines/
   - Naming conventions checklist
   - API design principles
   - Documentation standards

4. ✅ **Cargo Workspace Docs** - https://doc.rust-lang.org/cargo/reference/workspaces.html
   - Virtual workspace configuration
   - Dependency inheritance patterns
   - Workspace member management

### Additional Sources to Reference

5. **Tokio Documentation** - https://tokio.rs/
   - Async runtime best practices
   - Common patterns (`#[tokio::main]`, `#[tokio::test]`)
   - Runtime configuration

6. **Azure Identity for Rust** - Azure SDK GitHub
   - ChainedTokenCredential usage
   - DefaultAzureCredential patterns
   - Authentication examples

7. **Clippy Lint Groups** - https://rust-lang.github.io/rust-clippy/
   - Recommended lints for workspace
   - Lint configuration in `Cargo.toml`

8. **GitHub Actions for Rust** - https://github.com/actions-rs
   - `actions-rs/toolchain`
   - `actions-rs/cargo`
   - `Swatinem/rust-cache`

---

## Instruction File Structure

### Planned Sections

1. **Instructions** (Header)
   - Role definition: "expert Rust programmer specializing in Azure SDK development"
   - Tone guidance: informal, helpful, not overly apologetic
   - Confidence requirement: ask for clarification if uncertain

2. **Prerequisites**
   - Rust toolchain (rustc, cargo, rustfmt, clippy)
   - Azure CLI for local development authentication
   - PowerShell (for Azure SDK MCP tools)
   - IDE restart recommendation after tooling installation

3. **Workspace Architecture**
   - Virtual workspace pattern with example `Cargo.toml`
   - Recommended member structure (`{project}-core`, `-api`, `-cli`, `-web`)
   - Dependency inheritance pattern
   - Shared `Cargo.lock` and `target/` directory benefits

4. **Code Generation**
   - Rust naming conventions (PascalCase, snake_case, UPPER_SNAKE_CASE)
   - `use` directive organization and patterns
   - Safety → Efficiency → Correctness priority
   - Ownership and borrowing rules
   - Short, descriptive names
   - Explicit imports (no wildcards)
   - Generated code protection (`generated/` subdirectories)

5. **Azure Integration**
   - ChainedTokenCredential setup example
   - Service-agnostic SDK patterns
   - Authentication best practices
   - Environment variable configuration

6. **Async Patterns**
   - Tokio runtime as standard
   - `#[tokio::main]` for entry points
   - `#[tokio::test]` for async tests
   - Async function signatures
   - `.await` usage patterns

7. **Error Handling**
   - Standard `Result<T, E>` type
   - `?` operator for error propagation
   - Parent function must return `Result`
   - Error context via wrapping

8. **Testing Strategy**
   - `#[cfg(test)]` module at file bottom
   - Import from `super` in tests
   - Test function naming (no "test" prefix unless disambiguating)
   - `#[tokio::test]` for async tests
   - Integration tests in `tests/` directory

9. **Documentation Standards**
   - `///` for public API documentation
   - Format: concise summary, blank line, detailed explanation
   - Markdown support in doc comments
   - Code examples in doc comments
   - `#[doc]` attributes for advanced scenarios

10. **CI/CD with GitHub Actions**
    - Workflow structure example
    - `actions-rs/toolchain` for Rust setup
    - `Swatinem/rust-cache` for caching
    - Clippy checks with `--all-targets --all-features`
    - Rustfmt validation
    - Test execution with `cargo test --workspace`
    - Build validation with `cargo build --workspace`

11. **Validation and Linting**
    - Clippy for lint-free code
    - Rustfmt for consistent formatting
    - `TODO` comments when stuck
    - Multi-line search awareness for method chains

12. **Planning and Workflow**
    - Use todo lists for complex tasks
    - Break down large features into testable units
    - Document architectural decisions

---

## Code Examples to Include

### 1. Workspace Root `Cargo.toml`
```toml
[workspace]
members = [
    "crates/my-project-core",
    "crates/my-project-api",
    "crates/my-project-cli",
]
resolver = "2"

[workspace.package]
version = "0.1.0"
edition = "2021"
authors = ["Your Name <you@example.com>"]
license = "MIT OR Apache-2.0"

[workspace.dependencies]
tokio = { version = "1.35", features = ["full"] }
azure_identity = "0.20"
azure_core = "0.20"
anyhow = "1.0"
clap = { version = "4.4", features = ["derive"] }
serde = { version = "1.0", features = ["derive"] }
tracing = "0.1"
tracing-subscriber = "0.3"
```

### 2. Member Crate `Cargo.toml`
```toml
[package]
name = "my-project-api"
version.workspace = true
edition.workspace = true
authors.workspace = true
license.workspace = true

[dependencies]
tokio.workspace = true
azure_identity.workspace = true
azure_core.workspace = true
my-project-core = { path = "../my-project-core" }
```

### 3. ChainedTokenCredential Example
```rust
use azure_identity::{ChainedTokenCredential, AzureCliCredential, DefaultAzureCredential};
use azure_core::auth::TokenCredential;
use std::sync::Arc;

pub fn create_credential() -> Arc<dyn TokenCredential> {
    Arc::new(
        ChainedTokenCredential::new(vec![
            Arc::new(AzureCliCredential::new()),
            Arc::new(DefaultAzureCredential::default()),
        ])
    )
}
```

### 4. Async Main Function
```rust
#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    // Initialize tracing
    tracing_subscriber::fmt::init();
    
    // Your async code here
    
    Ok(())
}
```

### 5. Test Module Structure
```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn basic_test() {
        assert_eq!(2 + 2, 4);
    }

    #[tokio::test]
    async fn async_test() {
        let result = async_function().await;
        assert!(result.is_ok());
    }
}
```

### 6. Documentation Example
```rust
/// Fetches data from Azure Storage.
///
/// This function retrieves a blob from the specified container using
/// the provided credential. Returns the blob content as bytes.
///
/// # Arguments
///
/// * `credential` - Azure authentication credential
/// * `container` - Container name
/// * `blob_name` - Name of the blob to fetch
///
/// # Example
///
/// ```no_run
/// use my_project_api::fetch_blob;
///
/// #[tokio::main]
/// async fn main() -> Result<(), Box<dyn std::error::Error>> {
///     let cred = create_credential();
///     let data = fetch_blob(cred, "my-container", "file.txt").await?;
///     Ok(())
/// }
/// ```
pub async fn fetch_blob(
    credential: Arc<dyn TokenCredential>,
    container: &str,
    blob_name: &str,
) -> Result<Vec<u8>, Box<dyn std::error::Error>> {
    // Implementation
    Ok(vec![])
}
```

### 7. GitHub Actions Workflow
```yaml
name: CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

env:
  CARGO_TERM_COLOR: always

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - uses: actions-rs/toolchain@v1
        with:
          toolchain: stable
          components: rustfmt, clippy
          
      - uses: Swatinem/rust-cache@v2
      
      - name: Check formatting
        run: cargo fmt --all -- --check
        
      - name: Clippy
        run: cargo clippy --workspace --all-targets --all-features -- -D warnings
        
      - name: Run tests
        run: cargo test --workspace
        
      - name: Build
        run: cargo build --workspace --release
```

---

## Target Audience (80% Use Case)

### Primary User Profile
- Rust developers building Azure-integrated applications
- Teams working on multi-crate projects
- Developers familiar with async Rust but new to Azure SDK
- Projects requiring service-agnostic Azure patterns
- CI/CD pipelines using GitHub Actions

### Not Covered (20% Edge Cases)
- Non-tokio async runtimes (async-std, smol)
- Complex error handling crates (thiserror, eyre)
- Specialized Azure services requiring unique patterns
- Non-GitHub CI/CD platforms (GitLab CI, Jenkins)
- Embedded or no_std Rust projects

---

## Implementation Checklist

- [ ] Create `.github/copilot-instructions.md` file
- [ ] Add Instructions header with role definition
- [ ] Add Prerequisites section
- [ ] Add Workspace Architecture with example
- [ ] Add Code Generation section with Rust conventions
- [ ] Add Azure Integration with ChainedTokenCredential example
- [ ] Add Async Patterns with tokio examples
- [ ] Add Error Handling patterns
- [ ] Add Testing Strategy section
- [ ] Add Documentation Standards with examples
- [ ] Add CI/CD section with GitHub Actions workflow
- [ ] Add Validation and Linting guidance
- [ ] Add Planning and Workflow section
- [ ] Review completeness against both source templates
- [ ] Validate all code examples compile
- [ ] Ensure 80% use case coverage

---

## Key Differentiators from Source Templates

### vs. Frontend Vibes
- Rust-specific instead of TypeScript/React
- Workspace architecture instead of Next.js structure
- Azure SDK focus instead of web framework
- System programming patterns instead of UI/UX
- Tokio async instead of React hooks

### vs. Azure SDK Rust
- Template-focused instead of SDK development
- More prescriptive workspace structure
- Code examples included throughout
- CI/CD guidance included
- Authentication patterns specified (ChainedTokenCredential)
- More comprehensive for beginners while maintaining expert guidance

---

## Success Criteria

1. **Completeness**: Covers Rust conventions, Azure patterns, workspace structure, testing, docs, CI/CD
2. **Clarity**: Code examples for all major patterns
3. **Actionable**: 80% of users can start building without additional questions
4. **Idiomatic**: Follows Rust API guidelines and community standards
5. **Concise**: Balanced detail (target ~100-120 lines like Frontend Vibes)
6. **Future-proof**: Patterns remain valid as Azure SDK evolves

---

## Notes and Considerations

- Keep tone informal and helpful (like Azure SDK Rust example)
- Avoid being overly apologetic
- Prioritize clarity over brevity
- Reference external documentation where appropriate
- Focus on patterns, not specific Azure service implementations
- Ensure examples are copy-paste ready
- Consider maintenance burden of keeping examples updated
- Link to Rust API Guidelines for comprehensive reference

---

**Status**: Planning complete, ready for implementation  
**Next Steps**: Create `.github/copilot-instructions.md` based on this plan
