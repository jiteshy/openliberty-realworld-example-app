# OpenLiberty RealWorld Example App Documentation

## 1. Project Overview

**Purpose**: Backend REST service implementing the RealWorld API specification for a social blogging platform.

**Domain**: Social media and content management platform for article publishing and community interaction.

**Target Users**: 
- **Content Creators**: Writers who publish articles with rich content and categorization
- **Community Members**: Readers who interact through comments, follows, and favorites
- **Frontend Developers**: Client application developers consuming the REST API

**Core Value Proposition**: Provides a complete backend foundation for building social blogging platforms with user management, content publishing, and community features.

## 2. Functional Documentation

### 2.1 Feature Inventory

#### User Management
- **User role**: All users
- **Primary use case**: Account creation, authentication, and profile management
- **Key user flows**:
  1. Register → Provide username, email, password → Receive JWT token
  2. Login → Provide email, password → Receive JWT token
  3. Update profile → Modify bio, image, or credentials → Save changes
  4. View profile → Access public profile information

#### Article Management
- **User role**: Authenticated users (authors)
- **Primary use case**: Creating, editing, and managing blog articles
- **Key user flows**:
  1. Create article → Provide title, description, body, tags → Publish with unique slug
  2. Edit article → Update content or metadata → Save changes
  3. Delete article → Remove article and associated comments
  4. Browse articles → Filter by tag, author, or favorites → View paginated results
  5. View article feed → Get articles from followed authors

#### Social Features
- **User role**: Authenticated users
- **Primary use case**: Community interaction and engagement
- **Key user flows**:
  1. Follow user → View profile → Follow to receive articles in feed
  2. Favorite article → Mark article as favorite → Increase favorites count
  3. Comment on article → Add comment → Engage in discussion
  4. Delete comment → Remove own comment from article

#### Tag System
- **User role**: All users (read), authenticated users (write)
- **Primary use case**: Content categorization and discovery
- **Key user flows**:
  1. Tag article → Add tags during article creation/editing
  2. Browse by tag → Filter articles by specific tags
  3. Discover tags → View all available tags

### 2.2 User Journey Maps

#### Authentication Flow
1. **Registration**: POST `/api/users` → Validate input → Create user → Generate JWT
2. **Login**: POST `/api/users/login` → Validate credentials → Generate JWT
3. **Authenticated requests**: Include JWT token in Authorization header as `Token {jwt}`

#### Core User Workflows
1. **Article Publishing**: Login → Create article → Add tags → Publish → Share slug
2. **Community Engagement**: Browse articles → Follow authors → Comment → Favorite articles
3. **Content Discovery**: Browse tags → Filter articles → Follow interesting authors

#### Permission Levels and Access Control
- **Public access**: Article listing, individual article view, tag listing, user profiles
- **Authenticated access**: Article creation/editing, commenting, following, favoriting, user updates
- **Owner-only access**: Edit/delete own articles, edit/delete own comments, update own profile

### 2.3 Business Logic Rules

#### Data Validation Rules
- **User registration**: Username, email, and password are required and non-empty
- **Article creation**: Title, description, and body are required and non-empty
- **Comment creation**: Body is required and non-empty
- **Email format**: No specific email format validation implemented
- **Username uniqueness**: Enforced at database level
- **Email uniqueness**: Enforced at database level

#### Business Constraints
- **Article slugs**: Auto-generated from title, must be unique
- **User roles**: Single role "users" for all authenticated users
- **Ownership**: Users can only edit/delete their own articles and comments
- **Following**: Users cannot follow themselves (not explicitly prevented)
- **Favoriting**: Users can favorite/unfavorite articles multiple times

#### Workflow Dependencies
- **Comments require articles**: Comments are linked to existing articles
- **Favorites require authentication**: Only authenticated users can favorite articles
- **Following requires authentication**: Only authenticated users can follow others
- **Article editing requires ownership**: Only article authors can edit their articles

#### Integration Points with External Systems
- **JWT authentication**: Uses OpenLiberty JWT implementation
- **Database**: Derby embedded database for persistence
- **CORS**: Configured for cross-origin requests from `http://localhost:4100`

## 3. Technical Documentation

### 3.1 Architecture Overview

**Technology Stack**:
- **Runtime**: OpenLiberty 20.0.0.4
- **Java Version**: Java 8
- **Framework**: MicroProfile 3.0
- **Build Tool**: Maven 3.x
- **Database**: Derby 10.14.2.0 (embedded)
- **ORM**: JPA 2.2 with EclipseLink
- **Authentication**: JWT (MicroProfile JWT)
- **JSON Processing**: org.json library
- **HTTP Client**: Jersey 2.30.1
- **Testing**: JUnit 4.12

**Project Structure**:
```
src/main/java/
├── application/
│   ├── rest/           # JAX-RS REST endpoints
│   └── errors/         # Error handling and validation messages
├── core/               # Domain entities and business models
│   ├── user/           # User, Profile, CreateUser models
│   ├── article/        # Article, CreateArticle models
│   └── comments/       # Comment, CreateComment models
├── dao/                # Data Access Objects
└── security/           # JWT token generation
```

**Design Patterns**:
- **Layered Architecture**: REST → Business Logic → Data Access → Persistence
- **Data Access Object (DAO)**: Centralized data access logic
- **Entity-Relationship Mapping**: JPA entities with relationships
- **Request/Response DTOs**: Separate create/update objects for API requests

**Data Flow**:
1. **HTTP Request** → JAX-RS Resource
2. **Authentication** → JWT validation (if required)
3. **Business Logic** → Service layer processing
4. **Data Access** → DAO layer with JPA EntityManager
5. **Database** → Derby persistence layer
6. **Response** → JSON serialization back to client

### 3.2 Development Environment

**Prerequisites**:
- Java 8 or higher
- Maven 3.x
- Git for version control

**Installation Steps**:
```bash
# Clone the repository
git clone https://github.com/jiteshy/openliberty-realworld-example-app.git
cd openliberty-realworld-example-app

# Build the project
mvn clean install

# Start the server
mvn liberty:start

# Start the server in foreground
mvn liberty:run
```

**Configuration**:
- **Server Configuration**: `src/main/liberty/config/server.xml`
- **JPA Configuration**: `src/main/resources/META-INF/persistence.xml`
- **Database**: Embedded Derby, no additional setup required
- **Port Configuration**: HTTP 9080, HTTPS 9443 (configurable via Maven properties)

**Common Commands**:
```bash
# Development commands
mvn clean compile          # Compile source code
mvn clean install          # Build and install artifacts
mvn liberty:start          # Start server in background
mvn liberty:run           # Start server in foreground (for development)
mvn liberty:stop          # Stop background server

# Testing commands
mvn test                  # Run unit tests
mvn integration-test      # Run integration tests
mvn verify                  # Run all tests
```

### 3.3 Application Configuration

**Required Configuration**:
- **Database Connection**: Configured in `server.xml`, uses embedded Derby
- **JWT Issuer**: Set to `https://192.168.1.15:9443/jwt/defaultJWT` in server configuration
- **CORS Origins**: Configured for `http://localhost:4100`

**Optional Configuration**:
- **HTTP Ports**: Default 9080 (HTTP), 9443 (HTTPS)
- **Application Context**: `/realworld` (configurable via Maven)
- **Database Name**: `UserDB` (Derby embedded)

**Configuration Sources**:
- **Maven Properties**: `pom.xml` defines port and context settings
- **Server Configuration**: `src/main/liberty/config/server.xml`
- **JPA Configuration**: `src/main/resources/META-INF/persistence.xml`

**Environment Variables**: No environment variables are used for configuration.

**Configuration Examples**:
```xml
<!-- server.xml configuration -->
<httpEndpoint httpPort="9080" httpsPort="9443" id="defaultHttpEndpoint" host="*"/>
<application name="RealWorld" location="realworld-liberty-1.0-SNAPSHOT.war" type="war" context-root="/"/>
<dataSource id="RealWorldSource" jndiName="jdbc/RealWorldSource">
    <properties.derby.embedded databaseName="UserDB" createDatabase="create" />
</dataSource>
```

**Configuration Validation**:
- **Startup Validation**: OpenLiberty validates configuration on startup
- **Error Messages**: Configuration errors are logged to Liberty logs
- **Database Creation**: Derby database is created automatically if it doesn't exist

### 3.4 Code Organization

**Directory Structure**:
- **`application/rest/`**: JAX-RS REST endpoints and API controllers
- **`application/errors/`**: Centralized error handling and validation messages
- **`core/`**: Domain entities, business models, and DTOs
- **`dao/`**: Data Access Objects for database operations
- **`security/`**: JWT token generation and security utilities

**File Naming Conventions**:
- **`*API.java`**: REST endpoint controllers (UsersAPI, ArticlesAPI)
- **`*.java`**: Domain entities (User, Article, Comment)
- **`Create*.java`**: Data Transfer Objects for API requests
- **`*Dao.java`**: Data Access Objects
- **`*Messages.java`**: Error and validation message constants

**Import/Export Patterns**:
- **JAX-RS annotations**: `@Path`, `@GET`, `@POST`, `@PUT`, `@DELETE`
- **CDI injection**: `@Inject` for dependency injection
- **JPA annotations**: `@Entity`, `@Table`, `@Column`, `@Id`
- **Security annotations**: `@RolesAllowed`, `@PermitAll`

**State Management**: 
- **Stateless services**: All REST endpoints are stateless
- **Database persistence**: JPA entities manage state in database
- **JWT tokens**: Carry user identity and claims across requests

### 3.5 API Documentation

**Base URL**: `http://localhost:9080/api`

**Authentication**: JWT token required for protected endpoints
- **Header**: `Authorization: Token {jwt_token}`
- **Token generation**: Obtained from login or registration endpoints

**Endpoints**:

#### User Management
- **`POST /api/users`** - Register new user
  - **Request**: `{"user": {"username": "john", "email": "john@example.com", "password": "password"}}`
  - **Response**: `{"user": {"email": "john@example.com", "username": "john", "bio": null, "image": null, "token": "jwt_token"}}`

- **`POST /api/users/login`** - Authenticate user
  - **Request**: `{"user": {"email": "john@example.com", "password": "password"}}`
  - **Response**: `{"user": {"email": "john@example.com", "username": "john", "bio": null, "image": null, "token": "jwt_token"}}`

- **`GET /api/user`** - Get current user (requires authentication)
  - **Response**: `{"user": {"email": "john@example.com", "username": "john", "bio": null, "image": null, "token": "jwt_token"}}`

- **`PUT /api/user`** - Update user (requires authentication)
  - **Request**: `{"user": {"email": "john@example.com", "bio": "Updated bio", "image": "http://example.com/image.jpg"}}`

#### Article Management
- **`GET /api/articles`** - List articles (optional query parameters: tag, author, favorited, limit, offset)
  - **Response**: `{"articles": [...], "articlesCount": 0}`

- **`GET /api/articles/feed`** - Get article feed (requires authentication)
  - **Response**: `{"articles": [...], "articlesCount": 0}`

- **`GET /api/articles/{slug}`** - Get article by slug
  - **Response**: `{"article": {"slug": "article-slug", "title": "Article Title", ...}}`

- **`POST /api/articles`** - Create article (requires authentication)
  - **Request**: `{"article": {"title": "Article Title", "description": "Description", "body": "Content", "tagList": ["tag1", "tag2"]}}`

- **`PUT /api/articles/{slug}`** - Update article (requires authentication, owner only)
- **`DELETE /api/articles/{slug}`** - Delete article (requires authentication, owner only)

#### Comment Management
- **`GET /api/articles/{slug}/comments`** - Get comments for article
- **`POST /api/articles/{slug}/comments`** - Add comment to article (requires authentication)
- **`DELETE /api/articles/{slug}/comments/{id}`** - Delete comment (requires authentication, owner only)

#### Social Features
- **`POST /api/articles/{slug}/favorite`** - Favorite article (requires authentication)
- **`DELETE /api/articles/{slug}/favorite`** - Unfavorite article (requires authentication)

#### Profile Management
- **`GET /api/profiles/{username}`** - Get user profile
- **`POST /api/profiles/{username}/follow`** - Follow user (requires authentication)
- **`DELETE /api/profiles/{username}/follow`** - Unfollow user (requires authentication)

#### Tag System
- **`GET /api/tags`** - Get all tags
  - **Response**: `{"tags": ["tag1", "tag2", ...]}`

#### Health Check
- **`GET /health`** - Health check endpoint
  - **Response**: `{"status": "UP"}`

**Error Handling**:
- **HTTP 422**: Validation errors with detailed messages
- **HTTP 404**: Resource not found
- **HTTP 403**: Forbidden (insufficient permissions)
- **HTTP 401**: Unauthorized (invalid or missing authentication)

**Response Format**: All responses use JSON format with consistent structure

### 3.6 Data Management

**Database Schema**:
- **USER_TABLE**: Users and profiles (shared table with entity inheritance)
- **Article_Table**: Articles with metadata, timestamps, and relationships
- **Comment_Table**: Comments linked to articles and authors
- **FOLLOWED_BY**: Many-to-many relationship for user following
- **Favorited**: Many-to-many relationship for article favorites

**Core Entities**:
- **User/Profile**: Abstract base class with specialized implementations
- **Article**: Main content entity with slug, title, description, body, tags
- **Comment**: User-generated content linked to articles
- **Relationships**: Following, favoriting, article-comment associations

**Migration Strategy**: Database tables are created automatically using JPA DDL generation
- **Property**: `eclipselink.ddl-generation=create-tables`
- **Mode**: Tables created on application startup if they don't exist

**Data Access Patterns**:
- **JPA EntityManager**: Direct entity management with JPQL queries
- **DAO Pattern**: Centralized data access with `UserDao`, `ArticleDao`, `UserContext`
- **Connection Pooling**: Managed by OpenLiberty datasource configuration
- **Transaction Management**: JTA transactions with `@Transactional` annotation

**Caching Strategy**: No explicit caching implemented, relies on JPA first-level cache

**Data Validation**:
- **Entity-level**: JPA validation with `nullable` constraints
- **Application-level**: Custom validation in REST endpoints
- **Database-level**: Unique constraints on username and email

## 4. Development Guidelines

### 4.1 Code Standards

**Coding Style**:
- **Java 8 conventions**: Standard Oracle Java coding standards
- **Package organization**: Logical separation by layer (rest, core, dao, security)
- **Naming conventions**: CamelCase for classes, methods; descriptive names
- **JSON handling**: org.json library for JSON parsing and generation

**Architecture Patterns**:
- **Layered architecture**: Clear separation of concerns
- **Dependency injection**: CDI with `@Inject` annotations
- **REST design**: JAX-RS with proper HTTP methods and status codes
- **Entity mapping**: JPA annotations for database relationships

### 4.2 Testing Strategy

**Test Files Found**:
- **Integration Tests**: `src/test/java/it/EndpointIT.java`
- **Test Framework**: JUnit 4.12 with JAX-RS test client
- **Test Coverage**: Basic health check and article listing endpoints

**Test Structure**:
- **Integration Tests**: Test actual HTTP endpoints with Jersey test client
- **Test Data**: Uses embedded Derby database for testing
- **Test Execution**: Maven Surefire (unit tests) and Failsafe (integration tests)

**Test Commands**:
```bash
mvn test                    # Run unit tests
mvn integration-test        # Run integration tests
mvn verify                  # Run all tests
```

### 4.3 Performance Considerations

**Database Performance**:
- **Embedded Derby**: Suitable for development, not production-scale
- **Connection pooling**: Managed by OpenLiberty datasource
- **Query optimization**: Basic JPQL queries, no complex optimizations

**API Performance**:
- **Stateless design**: No server-side session state
- **JSON serialization**: Direct entity-to-JSON conversion
- **Pagination**: Implemented for article listing (limit/offset)

## 5. Deployment & Operations

### 5.1 Build Process

**Maven Build Configuration**:
- **Parent POM**: `liberty-maven-app-parent` version 2.7
- **Packaging**: WAR file deployment
- **Artifact**: `realworld-liberty-1.0-SNAPSHOT.war`
- **Dependencies**: Managed through Maven with Liberty features

**Build Commands**:
```bash
mvn clean compile           # Compile source code
mvn clean package           # Create WAR file
mvn clean install           # Build and install to local repository
mvn liberty:package         # Create Liberty server package
```

**Build Artifacts**:
- **WAR file**: `target/realworld-liberty.war`
- **Liberty server**: Complete server package with application
- **Derby database**: Copied to Liberty shared resources

### 5.2 Deployment Process

**Local Development**:
```bash
mvn liberty:start           # Start server in background
mvn liberty:run            # Start server in foreground
mvn liberty:stop           # Stop server
```

**Server Configuration**:
- **Liberty server**: Configured in `src/main/liberty/config/server.xml`
- **Application deployment**: WAR file deployed to Liberty runtime
- **Database setup**: Derby database created automatically

**Environment Configuration**:
- **Development**: Default configuration suitable for local development
- **Production**: Requires external database configuration and security hardening

### 5.3 Monitoring & Debugging

**Application Logging**:
- **Liberty logs**: Standard Liberty server logging
- **Application logs**: Exception stack traces printed to console
- **Database logs**: Derby database logs

**Health Monitoring**:
- **Health endpoint**: `/health` returns simple UP status
- **MicroProfile Health**: Basic health check implementation

**Debugging**:
- **Liberty development mode**: Use `mvn liberty:run` for development
- **Database inspection**: Derby can be accessed via Derby tools
- **API testing**: Use curl or Postman for endpoint testing

## 6. Quick Start Guides

### 6.1 For New Developers

**5-Step Onboarding Process**:
1. **Clone repository**: `git clone https://github.com/jiteshy/openliberty-realworld-example-app.git`
2. **Install prerequisites**: Ensure Java 8+ and Maven 3.x are installed
3. **Build project**: Run `mvn clean install`
4. **Start server**: Run `mvn liberty:run`
5. **Test API**: Access `http://localhost:9080/api/articles` to verify

**First Feature Implementation**:
1. **Add new validation**: Modify `ValidationMessages.java` to add new error message
2. **Update entity**: Add new field to `User` or `Article` entity
3. **Update DAO**: Add new method to appropriate DAO class
4. **Update API**: Add new endpoint or modify existing endpoint
5. **Test change**: Use integration test or manual testing

**Common Pitfalls**:
- **JWT configuration**: Ensure JWT issuer matches server configuration
- **Database location**: Derby database is created in project directory
- **CORS errors**: Update CORS configuration in `server.xml` for different origins

### 6.2 For QA Engineers

**Test Execution**:
```bash
mvn test                    # Run unit tests
mvn integration-test        # Run integration tests
mvn verify                  # Run all tests and verify build
```

**Test Data Management**:
- **Database reset**: Derby database is recreated on each server restart
- **Test isolation**: Each test should be independent
- **API testing**: Use tools like Postman or curl for manual testing

**Key Testing Areas**:
- **Authentication flow**: Registration, login, JWT token handling
- **CRUD operations**: Article creation, editing, deletion
- **Social features**: Following, favoriting, commenting
- **Data validation**: Required fields, unique constraints, error handling
- **Authorization**: Ensure users can only modify their own content

**API Testing Examples**:
```bash
# Register user
curl -X POST http://localhost:9080/api/users \
  -H "Content-Type: application/json" \
  -d '{"user": {"username": "test", "email": "test@example.com", "password": "password"}}'

# Login user
curl -X POST http://localhost:9080/api/users/login \
  -H "Content-Type: application/json" \
  -d '{"user": {"email": "test@example.com", "password": "password"}}'

# List articles
curl http://localhost:9080/api/articles

# Create article (requires authentication)
curl -X POST http://localhost:9080/api/articles \
  -H "Content-Type: application/json" \
  -H "Authorization: Token {jwt_token}" \
  -d '{"article": {"title": "Test Article", "description": "Test", "body": "Content"}}'
```

## 7. Troubleshooting

### Common Issues

**Server Won't Start**:
- **Port conflict**: Change ports in `pom.xml` if 9080/9443 are in use
- **Java version**: Ensure Java 8+ is installed and JAVA_HOME is set
- **Maven issues**: Run `mvn clean install` to resolve dependency problems

**Database Issues**:
- **Derby conflicts**: Delete `UserDB` directory and restart server
- **Connection errors**: Check datasource configuration in `server.xml`
- **JPA errors**: Verify persistence.xml configuration

**Authentication Issues**:
- **JWT validation**: Ensure Authorization header format is `Token {jwt_token}`
- **Token expiration**: Tokens don't expire by default, check JWT configuration
- **CORS errors**: Update allowed origins in `server.xml`

### Environment Issues

**Maven Build Failures**:
- **Clean and rebuild**: Run `mvn clean compile` to resolve build issues
- **Dependency resolution**: Check internet connection and Maven repository access
- **Test failures**: Run `mvn test -X` for detailed error information

**Liberty Server Issues**:
- **Server not stopping**: Use `mvn liberty:stop` or kill process manually
- **Configuration errors**: Check Liberty logs for detailed error messages
- **Feature conflicts**: Ensure feature compatibility in `server.xml`

### Runtime Errors

**404 Errors**:
- **Incorrect URLs**: Verify API base path is `/api`
- **Context root**: Check application context-root in `server.xml`
- **Routing issues**: Ensure JAX-RS application path is correct

**500 Internal Server Error**:
- **Database connectivity**: Check Derby database configuration
- **JWT issues**: Verify JWT configuration and token format
- **Entity mapping**: Check JPA entity annotations and relationships

**Authentication/Authorization Errors**:
- **401 Unauthorized**: Verify JWT token is valid and properly formatted
- **403 Forbidden**: Check user permissions and resource ownership
- **422 Validation**: Review input validation and required fields

### Performance Issues

**Slow Response Times**:
- **Database queries**: Review JPQL queries for optimization opportunities
- **Connection pooling**: Adjust datasource configuration if needed
- **JSON serialization**: Consider optimizing entity-to-JSON conversion

**Memory Usage**:
- **JPA cache**: Monitor JPA first-level cache usage
- **Connection leaks**: Ensure proper connection management
- **Liberty heap**: Adjust JVM heap settings if needed

This documentation provides a comprehensive guide for understanding, developing, and maintaining the OpenLiberty RealWorld Example App. The application implements a complete social blogging platform backend with modern Java enterprise technologies. 