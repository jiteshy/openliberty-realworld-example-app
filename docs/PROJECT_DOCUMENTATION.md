# OpenLiberty RealWorld Example App Documentation

## 1. Project Overview

**Purpose**: A comprehensive backend REST API service implementing the RealWorld specification for a social blogging platform similar to Medium.

**Domain**: Social content publishing platform serving writers, readers, and content consumers who want to create, discover, and engage with articles and authors.

**Target Users**: 
- Content creators who write and publish articles with tags
- Readers who discover, favorite, and comment on articles
- Social users who follow authors and build personalized content feeds

**Core Value Proposition**: Provides a production-ready, fully-featured backend implementation demonstrating real-world enterprise patterns including CRUD operations, JWT authentication, role-based authorization, social features, and complex database relationships using modern Java technologies.

## 2. Functional Documentation

### 2.1 Feature Inventory

**User Authentication & Management**
- User role: Any visitor (registration), Authenticated users (management)
- Primary use case: Account lifecycle and security management
- Key user flows:
  1. Register new account with email, username, password
  2. Login with credentials to receive JWT token
  3. Update profile information (bio, image, email, password)
  4. Retrieve current user profile and authentication status

**Article Content Management**
- User role: Authenticated users (authors)
- Primary use case: Content creation and lifecycle management
- Key user flows:
  1. Create articles with title, description, body content, and tags
  2. Update existing articles (author-only permissions)
  3. Delete articles (author-only permissions)
  4. Generate unique URL slugs automatically from article titles

**Content Discovery & Browsing**
- User role: Any user (authenticated or anonymous)
- Primary use case: Content exploration and consumption
- Key user flows:
  1. Browse all articles with pagination support
  2. Filter articles by tag, author, or favorited status
  3. Access personalized feed of articles from followed authors
  4. View individual articles by unique slug identifier

**Social Engagement Features**
- User role: Authenticated users
- Primary use case: Community interaction and content curation
- Key user flows:
  1. Follow and unfollow other authors
  2. Favorite and unfavorite articles for bookmarking
  3. View public user profiles with following status
  4. Build personalized feeds based on followed authors

**Article Discussion System**
- User role: Authenticated users for creation, Any user for viewing
- Primary use case: Community engagement and article feedback
- Key user flows:
  1. Add comments to articles with structured discussion
  2. Delete own comments (author-only permissions)
  3. View all comments on articles in threaded format

**Content Organization**
- User role: Any user
- Primary use case: Content categorization and discoverability
- Key user flows:
  1. View comprehensive list of all available tags in the system

### 2.2 User Journey Maps

**Authentication & Onboarding Flow**:
1. New user registers with email/username/password → System validates uniqueness and creates account → JWT token generated and returned
2. Returning user logs in with email/password → System validates credentials → JWT token generated for session
3. Authenticated user includes JWT token in Authorization header as "Token {jwt}" → Server validates token and extracts user claims
4. Protected endpoints verify JWT and user permissions before processing requests

**Content Creation & Publishing Workflow**:
1. Authenticated user creates article with metadata → System validates required fields and generates unique slug
2. Article becomes immediately available in public listings and discoverable by filters
3. Other users can engage through favorites, comments, and author following
4. Author retains exclusive edit/delete permissions for content lifecycle management

**Social Discovery & Engagement Flow**:
1. Users discover content through public article listings, tag filtering, or personalized feeds
2. Users can favorite articles for personal bookmarking and follow authors for ongoing content
3. Following relationships populate personalized feeds with articles from followed authors
4. Comment system enables structured discussions and community engagement around articles

### 2.3 Business Logic Rules

**Data Validation & Integrity Rules**:
- User registration requires non-empty email, username, and password fields
- Email addresses must be unique across all users in the system
- Usernames must be unique across all users in the system
- Article creation requires non-empty title, description, and body content
- Article slugs must be unique system-wide (auto-generated from titles with conflict resolution)

**Authorization & Permissions Constraints**:
- Only article authors can update or delete their own articles
- Only comment authors can delete their own comments
- Article favoriting and following actions require user authentication
- User profile updates restricted to account owner only
- Personalized feeds available only to authenticated users

**Content & Workflow Dependencies**:
- Article creation and management requires valid user authentication
- Comment creation requires both valid authentication and existing target article
- User following requires valid target user existence in system
- Article favoriting requires valid target article existence in system
- Feed generation depends on established following relationships

**External System Integration Points**:
- Derby embedded database for all persistent data storage and retrieval
- JWT token generation and validation for stateless authentication
- GitHub Slugify library for consistent URL slug generation from article titles
- Jersey JAX-RS implementation for REST API processing and JSON serialization

## 3. Technical Documentation

### 3.1 Architecture Overview

**Technology Stack**:
- Java 8 with Maven 3.x for build management and dependency resolution
- Open Liberty 20.0.0.4 application server with MicroProfile 3.0 specifications
- JAX-RS 2.0.1 with Jersey 2.30.1 implementation providing REST API services, JSON binding, client framework, and HK2 dependency injection
- JPA 2.2 features with EclipseLink ORM and javax.persistence 2.1.0 API (Liberty provides 2.2 runtime capabilities through 2.1.0 API compatibility)
- Derby 10.14.2.0 embedded database with test scope dependency that is copied to Liberty shared resources for both development runtime and test execution environments
- MicroProfile JWT 1.0 for stateless authentication and authorization
- JSON processing with org.json library for request/response serialization

**Project Structure**:
```
src/main/java/
├── application/
│   ├── errors/          # Centralized validation error messages and formatting
│   └── rest/            # JAX-RS REST endpoint implementations
├── core/                # Domain models, entities, and data transfer objects
│   ├── article/         # Article-related entities and DTOs
│   ├── comments/        # Comment entities and creation DTOs
│   └── user/           # User entities, profiles, and DTOs
├── dao/                # Data Access Objects for database operations
└── security/           # JWT token generation and security utilities
```

**Design Patterns & Architecture**:
- **Layered Architecture**: Clear separation between REST controllers, business logic, and data access layers
- **Data Access Object (DAO) Pattern**: Centralized database operations in specialized DAO classes (UserDao, ArticleDao)
- **Request-Response DTO Pattern**: Dedicated data transfer objects (CreateUser, CreateArticle, CreateComment) for API boundaries
- **Entity-JSON Serialization**: Custom toJson() methods on entities for consistent API response formatting
- **Dependency Injection**: CDI-based injection for service and DAO layer dependencies

**Data Flow Architecture**:
1. HTTP requests received by JAX-RS endpoints with automatic JSON deserialization to DTO objects
2. JWT tokens validated for authentication and user identity extraction
3. Business logic processed in DAO layer with transaction management
4. JPA entities queried, created, or updated through EntityManager operations
5. Entity objects converted to JSON responses through custom serialization methods
6. HTTP responses returned to clients with appropriate status codes and error handling

### 3.2 Development Environment

**Prerequisites**:
- Java 8 JDK (minimum requirement for compilation and runtime)
- Maven 3.x for dependency management and build execution
- Open Liberty runtime automatically managed through Maven plugin

**Installation Steps**:
1. Clone the repository from GitHub source
2. Navigate to the project root directory in terminal
3. Execute `mvn clean install` to download dependencies and build the project
4. Run `mvn liberty:start` to start the server in background mode
5. Application becomes available at `http://localhost:9080` with API base at `/api`

**Configuration Management**:
- Server configuration: `src/main/liberty/config/server.xml` (Liberty features, datasources, security)
- Database configuration: Derby embedded database (UserDB) serving both development runtime and test environments
- JWT configuration: MicroProfile JWT with hardcoded issuer requiring environment-specific updates
- CORS configuration: Explicitly allows `http://localhost:4100` origin for frontend integration
- Build configuration: Maven POM with Liberty plugin, dependency management, and test execution

**Development Commands**:
- **Build Project**: `mvn clean install` (full build with dependency resolution and testing)
- **Start Server**: `mvn liberty:start` (background server process)
- **Run Server**: `mvn liberty:run` (foreground server with console output)
- **Stop Server**: `mvn liberty:stop` (graceful shutdown of background server)
- **Execute Tests**: `mvn test` (unit test execution)
- **Integration Tests**: `mvn integration-test` (full integration test suite)
- **Package WAR**: `mvn package` (create deployable WAR artifact)

### 3.3 Code Organization

**Directory Structure & Responsibilities**:
- **`application/rest/`**: JAX-RS REST endpoint implementations with HTTP method handlers
- **`core/`**: Domain models, JPA entities, and data transfer objects for API boundaries
- **`dao/`**: Data access layer with EntityManager operations and custom queries
- **`security/`**: JWT token generation, validation, and security utility classes
- **`src/main/liberty/config/`**: Open Liberty server configuration and feature definitions
- **`src/main/resources/META-INF/`**: JPA persistence configuration and database connection settings

**File Naming Conventions**:
- **`*API.java`**: JAX-RS REST endpoint controllers (UsersAPI, ArticlesAPI, ProfilesAPI, TagsAPI)
- **`*.java`**: Domain entities and core business objects (User, Article, Comment, Profile)
- **`Create*.java`**: Data transfer objects for request payloads (CreateUser, CreateArticle, CreateComment)
- **`*Dao.java`**: Data access objects for database operations (UserDao, ArticleDao)
- **`*IT.java`**: Integration test classes (EndpointIT)

**Import Organization & Dependencies**:
- Standard Java libraries (javax.* packages for JAX-RS, JPA, CDI)
- MicroProfile specifications (org.eclipse.microprofile.jwt)
- Open Liberty specific classes (com.ibm.websphere.security.jwt)
- Third-party libraries (org.json for JSON processing, com.github.slugify for URL generation)
- Application internal dependencies following package hierarchy

**Module Architecture & Connections**:
- **REST Layer**: JAX-RS endpoints inject DAO and security components via CDI
- **DAO Layer**: Handles all database operations with JPA EntityManager injection
- **Entity Layer**: JPA entities with custom JSON serialization methods
- **Security Layer**: JWT token generation and validation utilities
- **Error Handling**: Centralized validation message formatting and HTTP status management

### 3.4 API Documentation

**Authentication Mechanism**:
- **JWT Token-based Authentication**: MicroProfile JWT 1.0 with stateless token validation
- **Authorization Header Format**: `Authorization: Token {jwt_token}`
- **Token Generation**: Custom JwtGenerator class creates tokens with user ID and username claims
- **Protected Endpoints**: Most endpoints require `@RolesAllowed("users")` with specific exceptions for public access

**REST API Endpoint Specification**:

**User Management Endpoints**:
- `POST /api/users` - User registration (public access)
  - Request: `{"user": {"username": "string", "email": "string", "password": "string"}}`
  - Response: User object with JWT token
  - Validation: Username and email uniqueness, required field validation

- `POST /api/users/login` - User authentication (public access)
  - Request: `{"user": {"email": "string", "password": "string"}}`
  - Response: User object with JWT token
  - Validation: Credential verification, email existence

- `GET /api/user` - Current user profile (authenticated)
  - Response: Current user profile with token refresh
  - Authorization: JWT token required

- `PUT /api/user` - Update user profile (authenticated)
  - Request: `{"user": {"email": "string", "username": "string", "bio": "string", "image": "string"}}`
  - Response: Updated user object with refreshed token
  - Authorization: User can only update own profile

**Article Management Endpoints**:
- `GET /api/articles` - List articles with filtering (public access)
  - Query Parameters: `tag`, `author`, `favorited`, `limit` (default: 20), `offset` (default: 0)
  - Response: Array of articles with metadata and pagination info
  - Features: Tag-based filtering, author filtering, favorited-by filtering

- `GET /api/articles/feed` - Personalized article feed (authenticated)
  - Query Parameters: `limit` (default: 20), `offset` (default: 0)
  - Response: Articles from followed authors with pagination
  - Authorization: JWT token required for feed generation

- `GET /api/articles/{slug}` - Single article retrieval (public access)
  - Response: Complete article object with author profile and engagement data
  - URL Parameter: Article slug (auto-generated from title)

- `POST /api/articles` - Create new article (authenticated)
  - Request: `{"article": {"title": "string", "description": "string", "body": "string", "tagList": ["string"]}}`
  - Response: Created article with generated slug and metadata
  - Authorization: JWT token required, auto-assignment of author

- `PUT /api/articles/{slug}` - Update article (authenticated, author-only)
  - Request: `{"article": {"title": "string", "description": "string", "body": "string"}}`
  - Response: Updated article with refreshed slug if title changed
  - Authorization: Only article author can update

- `DELETE /api/articles/{slug}` - Delete article (authenticated, author-only)
  - Response: Empty response with 200 status
  - Authorization: Only article author can delete

**Article Engagement Endpoints**:
- `POST /api/articles/{slug}/favorite` - Favorite article (authenticated)
  - Response: Article object with updated favorites count
  - Authorization: JWT token required

- `DELETE /api/articles/{slug}/favorite` - Unfavorite article (authenticated)
  - Response: Article object with updated favorites count
  - Authorization: JWT token required

**Comment System Endpoints**:
- `GET /api/articles/{slug}/comments` - List article comments (public access)
  - Response: Array of comments with author information
  - Features: Comments include author profiles and timestamps

- `POST /api/articles/{slug}/comments` - Add comment (authenticated)
  - Request: `{"comment": {"body": "string"}}`
  - Response: Created comment object with author profile
  - Authorization: JWT token required

- `DELETE /api/articles/{slug}/comments/{id}` - Delete comment (authenticated, author-only)
  - Response: Empty response with 200 status
  - Authorization: Only comment author can delete

**Profile & Social Endpoints**:
- Profile endpoints implemented in ProfilesAPI (following/unfollowing functionality)
- Tag listing endpoint in TagsAPI for content categorization

**Health Monitoring Endpoints**:
- `GET /health` - MicroProfile Health endpoint returning service status with UP/DOWN state (public access)
  - Implemented via `HealthEndpoint.java` class implementing MicroProfile `HealthCheck` interface
  - Auto-registered by MicroProfile 3.0 feature for application health monitoring
  - Returns JSON response with service availability status for load balancer integration

**Error Response Format**:
- Standard HTTP status codes: 200 (success), 201 (created), 404 (not found), 422 (validation error), 403 (forbidden)
- Consistent error response structure with validation messages
- Centralized error handling through ValidationMessages utility class

**Data Model Schemas**:

**User Entity Structure**:
```json
{
  "user": {
    "email": "string",
    "username": "string", 
    "bio": "string|null",
    "image": "string|null",
    "token": "jwt_string"
  }
}
```

**Article Entity Structure**:
```json
{
  "article": {
    "slug": "string",
    "title": "string",
    "description": "string", 
    "body": "string",
    "tagList": ["string"],
    "createdAt": "ISO_datetime",
    "updatedAt": "ISO_datetime",
    "favoritesCount": "integer",
    "favorited": "boolean",
    "author": "profile_object"
  }
}
```

### 3.5 Data Management

**Database Schema & Design**:
- **Primary Database**: Derby 10.14.2.0 embedded database with automatic table creation
- **Persistence Framework**: JPA 2.2 with EclipseLink ORM for entity management
- **Connection Management**: JTA datasource configured in Liberty server with automatic connection pooling
- **Schema Generation**: EclipseLink DDL generation creates tables automatically from entity annotations

**Core Entity Relationships**:
- **User → Article**: One-to-many relationship through Profile entity serving as author
- **Article → Comment**: One-to-many relationship with cascade delete operations
- **User ↔ User**: Following relationships for social feed generation
- **User ↔ Article**: Favoriting relationships for bookmark functionality

**Data Access Patterns**:
- **Repository Pattern**: DAO classes (UserDao, ArticleDao) encapsulate all database operations
- **Entity Manager Integration**: CDI injection of EntityManager for JPA operations
- **Transaction Management**: Method-level `@Transactional` annotations for atomic operations
- **Custom Query Implementation**: JPQL queries for complex filtering and relationship traversal

**Data Validation & Constraints**:
- **Entity-Level Validation**: JPA annotations for null constraints and column specifications
- **Business Logic Validation**: Custom validation in API endpoints for business rules
- **Unique Constraint Enforcement**: Email and username uniqueness validation at application level
- **Referential Integrity**: JPA cascade operations for related entity management

**Database Configuration**:
- **Maven Dependency**: Derby defined with `test` scope in pom.xml but copied to Liberty shared resources during build
- **Runtime Configuration**: Derby JDBC driver configured in server.xml with embedded database properties
```xml
<dataSource id="RealWorldSource" jndiName="jdbc/RealWorldSource">
    <jdbcDriver libraryRef="derbyJDBCLib" />
    <properties.derby.embedded databaseName="UserDB" createDatabase="create" />
</dataSource>
```
- **Build Integration**: Maven dependency plugin copies Derby JAR to `target/liberty/wlp/usr/shared/resources/` for runtime access

**Migration Strategy**: 
- EclipseLink automatic DDL generation handles schema creation and updates
- Database file persisted locally for development consistency
- Derby embedded mode eliminates external database dependencies

## 4. Development Guidelines

### 4.1 Code Standards

**Java Coding Style**:
- **Language Version**: Java 8 compatibility throughout codebase
- **Naming Conventions**: CamelCase for variables/methods, PascalCase for classes
- **Package Organization**: Domain-driven package structure with clear layer separation
- **Annotation Usage**: JAX-RS, JPA, and CDI annotations following framework conventions

**Architectural Standards**:
- **Dependency Injection**: CDI-based injection for all service and DAO dependencies
- **Transaction Management**: JTA transactions with method-level annotation control
- **Error Handling**: Centralized error response formatting with consistent HTTP status codes
- **JSON Serialization**: Custom toJson() methods on entities for API response control

**Security Practices**:
- **Authentication**: JWT-based stateless authentication with MicroProfile JWT
- **Authorization**: Role-based access control with method-level security annotations
- **Input Validation**: Required field validation and business rule enforcement
- **Password Security**: Secure password handling in authentication flows

**Performance Considerations**:
- **Database Access**: Efficient JPA queries with relationship loading optimization
- **Connection Pooling**: Liberty-managed connection pooling for database efficiency
- **Caching Strategy**: No explicit caching implemented (relies on JPA first-level cache)
- **Resource Management**: Proper EntityManager lifecycle management through CDI

### 4.2 Testing Strategy

**Integration Testing**: Test files exist in `src/test/java/it/` directory with `EndpointIT.java` implementing basic endpoint validation

**Test Framework Configuration**:
- **JUnit 4.12**: Primary testing framework for test execution
- **Derby Test Database**: Embedded Derby database used for test execution isolation
- **Jersey Test Framework**: Jersey test framework with JDK HTTP provider for endpoint testing
- **Maven Test Integration**: Surefire plugin for unit tests, Failsafe plugin for integration tests

**Current Test Coverage**:
- **Health Endpoint Validation**: Basic health check endpoint verification
- **Article Listing Test**: Empty article list retrieval validation
- **Test Environment**: Isolated test execution with dedicated Derby database instance

**Test Execution Commands**:
- **Unit Tests**: `mvn test` (excludes integration tests)
- **Integration Tests**: `mvn integration-test` (includes endpoint testing)
- **Complete Test Suite**: `mvn verify` (runs both unit and integration tests)

### 4.3 Performance Considerations

**Database Performance**:
- **Embedded Derby**: Optimized for development and testing environments
- **JPA Query Optimization**: Entity relationships configured with appropriate fetch strategies
- **Connection Efficiency**: Liberty-managed connection pooling reduces connection overhead

**API Performance**:
- **JSON Processing**: Efficient JSON serialization through custom entity methods
- **Response Optimization**: Minimal data transfer with focused entity serialization
- **Pagination Support**: Built-in pagination for article listings to manage large datasets

**Resource Management**:
- **Memory Usage**: Entity lifecycle managed through JPA and CDI containers
- **CPU Optimization**: Efficient slug generation and JWT token processing
- **Network Efficiency**: RESTful API design with appropriate HTTP methods and status codes

## 5. Deployment & Operations

### 5.1 Build Process

**Maven Build Configuration**:
- **Project Packaging**: WAR (Web Archive) packaging for Liberty deployment
- **Dependency Management**: Maven coordinates with version management through parent POM
- **Artifact Generation**: `realworld-liberty-1.0-SNAPSHOT.war` as deployable artifact
- **Liberty Integration**: Liberty Maven plugin manages server lifecycle and deployment

**Build Commands & Outputs**:
- **Clean Build**: `mvn clean install` - Complete build with dependency resolution and testing
- **Package Creation**: `mvn package` - Generates WAR file in `target/` directory
- **Dependency Resolution**: Maven automatically downloads and manages library dependencies
- **Test Execution**: Integrated test execution during build process with reporting

**Build Artifact Structure**:
- **WAR Contents**: Compiled classes, dependency libraries, Liberty configuration, static resources
- **Deployment Location**: Liberty server deploys WAR to `/realworld` context path
- **Resource Management**: Derby database JAR copied to Liberty shared resources directory

### 5.2 Deployment Process

**Liberty Server Deployment**:
- **Server Management**: Liberty Maven plugin handles server lifecycle (start, stop, deploy)
- **Configuration**: Server configuration in `src/main/liberty/config/server.xml`
- **Application Context**: Deployed to root context (`/`) with API base at `/api`
- **Port Configuration**: HTTP on 9080, HTTPS on 9443 (configurable via Maven properties)

**Database Deployment**:
- **Embedded Derby**: Database files created automatically in Liberty server directory
- **Schema Creation**: EclipseLink DDL generation creates tables on first deployment
- **Data Persistence**: Database files persist between server restarts for data continuity

**Environment Configuration**:
- **CORS Settings**: Configured for `http://localhost:4100` frontend integration
- **JWT Configuration**: Hardcoded issuer requires environment-specific updates for production
- **Database Path**: Derby database location managed by Liberty server configuration

**Deployment Commands**:
- **Server Start**: `mvn liberty:start` (background process)
- **Server Stop**: `mvn liberty:stop` (graceful shutdown)
- **Development Mode**: `mvn liberty:run` (foreground with console output)
- **Hot Deployment**: File changes trigger automatic redeployment during development

### 5.3 Monitoring & Debugging

**Application Logging**:
- **Liberty Logging**: Standard Liberty server logging with configurable levels
- **Application Logs**: Custom logging available through java.util.logging framework
- **Console Output**: Real-time logging available through `mvn liberty:run` foreground mode
- **Log Location**: Liberty server logs stored in server directory structure

**Health Monitoring**:
- **Health Endpoint**: Basic health check available at `/health` endpoint
- **Endpoint Testing**: Integration tests verify health endpoint functionality
- **Server Status**: Liberty server provides runtime status information
- **Database Health**: Derby embedded database health monitored through connection status

**Development Debugging**:
- **Hot Reload**: Liberty development mode supports automatic redeployment
- **Debug Mode**: Standard Java debugging support through IDE integration
- **Error Responses**: Comprehensive error responses with HTTP status codes and messages
- **Console Debugging**: Real-time console output during development execution

**Performance Monitoring**:
- **Database Performance**: Derby embedded database performance suitable for development
- **API Response Times**: RESTful endpoint performance monitoring through integration tests
- **Resource Usage**: Java application resource monitoring through standard JVM tools
- **Connection Monitoring**: Liberty connection pool monitoring and management

## 6. Quick Start Guides

### 6.1 For New Developers

**5-Step Onboarding Process**:
1. **Environment Setup**: Install Java 8 JDK and verify Maven installation
2. **Project Setup**: Clone repository and run `mvn clean install` to verify build
3. **Server Launch**: Execute `mvn liberty:start` and verify server at `http://localhost:9080`
4. **API Testing**: Test health endpoint with `curl http://localhost:9080/health`
5. **Code Exploration**: Examine `UsersAPI.java` and `ArticlesAPI.java` for REST endpoint patterns

**First Feature Implementation**:
Implement a new article tag counting endpoint:
1. Create method in `TagsAPI.java` with `@GET @Path("/count")` annotation
2. Inject `ArticleDao` and implement tag counting logic using existing methods
3. Return JSON response with tag statistics in format `{"tagCount": integer}`
4. Test endpoint with curl and verify JSON response format
5. Add basic integration test in `EndpointIT.java` following existing patterns

**Common Development Pitfalls**:
- **JWT Configuration**: Remember to include `Authorization: Token {jwt}` header for authenticated endpoints
- **Database State**: Derby database persists between server restarts; clean database by deleting server directory
- **Maven Dependencies**: Always run `mvn clean install` after dependency changes
- **Port Conflicts**: Ensure ports 9080 and 9443 are available before starting Liberty server
- **JSON Format**: Follow exact JSON structure expected by RealWorld API specification

### 6.2 For QA Engineers

**Integration Test Execution**:
- **Full Test Suite**: `mvn verify` runs complete test suite with integration tests
- **Quick Validation**: `mvn test` executes unit tests for rapid feedback
- **Server Testing**: `mvn integration-test` runs endpoint validation tests
- **Test Reports**: Test results available in `target/test-reports/` directory

**API Testing & Validation**:
- **Postman Collection**: `utility/Conduit.postman_collection.json` provides comprehensive API test suite
- **Manual Testing**: Use curl or Postman for manual endpoint validation
- **Authentication Testing**: Test JWT token generation and validation workflows
- **Error Scenario Testing**: Validate error responses and HTTP status codes

**Key Testing Focus Areas**:
- **User Registration & Authentication**: Validate user creation, login, and profile management
- **Article CRUD Operations**: Test article creation, updates, deletion, and retrieval
- **Social Features**: Verify following, favoriting, and personalized feed functionality
- **Data Validation**: Test required field validation and business rule enforcement
- **Authorization**: Confirm proper access control and permission enforcement

**Bug Reporting Guidelines**:
- **API Endpoint**: Specify exact endpoint URL and HTTP method
- **Request Payload**: Include complete request body and headers
- **Expected vs Actual**: Document expected behavior vs actual response
- **Environment**: Note Java version, Maven version, and server configuration
- **Reproduction Steps**: Provide step-by-step reproduction instructions with specific data

## 7. Troubleshooting

### Common Issues & Solutions

**Server Startup Problems**:
- **Port Conflicts**: Liberty server requires ports 9080 and 9443; verify availability with `netstat -an | grep 9080`
- **Java Version**: Ensure Java 8 JDK is installed and JAVA_HOME environment variable is set correctly
- **Maven Issues**: Verify Maven installation with `mvn -version` and ensure M2_HOME is configured
- **Permission Errors**: Ensure write permissions for Liberty server directory creation

**Database Connection Errors**:
- **Derby Initialization**: Database creates automatically on first run; delete server directory to reset
- **Connection Pool**: Liberty manages Derby connections automatically; restart server if connection issues occur
- **Schema Problems**: EclipseLink creates tables automatically; check logs for DDL generation errors
- **Data Corruption**: Remove Derby database files in Liberty server directory to start fresh

**Authentication & Authorization Issues**:
- **JWT Token Format**: Ensure Authorization header uses exact format `Token {jwt_token}` (not Bearer)
- **Token Expiration**: JWT tokens may expire; re-authenticate to obtain fresh token
- **User Permissions**: Verify user has appropriate role assignments for endpoint access
- **CORS Problems**: Frontend requests from origins other than `localhost:4100` will be blocked

**API Response Errors**:
- **JSON Format**: Verify request payloads match exact structure expected by endpoints
- **Required Fields**: Check that all required fields are present and non-empty
- **Validation Errors**: Review validation messages for business rule violations
- **HTTP Status Codes**: Use appropriate HTTP methods and expect correct status codes

**Build & Deployment Issues**:
- **Maven Dependencies**: Run `mvn clean install` after any POM changes
- **Compilation Errors**: Verify Java 8 source compatibility and resolve import issues
- **WAR Deployment**: Check Liberty server logs for deployment errors and configuration issues
- **Hot Reload Problems**: Restart server if automatic redeployment fails during development

**Performance Issues**:
- **Slow API Responses**: Derby embedded database may be slow for large datasets; consider external database for production
- **Memory Usage**: Monitor JVM memory usage during development and testing
- **Database Query Performance**: Review JPA queries and consider adding database indexes for production use

**Testing & Integration Problems**:
- **Test Execution Failures**: Ensure test database is isolated and clean between test runs
- **Integration Test Timeouts**: Verify server starts successfully before integration test execution
- **Postman Collection Issues**: Update collection environment variables to match local server configuration
