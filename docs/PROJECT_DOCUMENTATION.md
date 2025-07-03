# OpenLiberty RealWorld Example App Documentation

## 1. Project Overview

**Purpose**: A fully featured backend REST API service implementing the RealWorld specification for a blogging platform similar to Medium.

**Domain**: Social blogging platform serving content creators and readers who want to publish, discover, and interact with articles.

**Target Users**: 
- Content creators who write and publish articles
- Readers who discover, favorite, and comment on articles
- Developers who follow and interact with other users

**Core Value Proposition**: Provides a complete, production-ready backend implementation demonstrating real-world patterns including CRUD operations, authentication, authorization, social features, and advanced database relationships.

## 2. Functional Documentation

### 2.1 Feature Inventory

**User Management**
- User role: Any registered user
- Primary use case: Account management and authentication
- Key user flows:
  1. Register with email, username, and password
  2. Login with email and password to receive JWT token
  3. Update profile information (email, username, bio, image, password)
  4. View current user profile with authentication status

**Article Management**
- User role: Authenticated users (authors)
- Primary use case: Content creation and management
- Key user flows:
  1. Create new article with title, description, body, and optional tags
  2. Update existing articles (author only)
  3. Delete articles (author only)
  4. View individual articles by slug

**Article Discovery**
- User role: Any user (authenticated or anonymous)
- Primary use case: Content discovery and filtering
- Key user flows:
  1. List all articles with pagination (limit/offset)
  2. Filter articles by tag, author, or favorited user
  3. Get personalized feed of articles from followed users (authenticated only)

**Social Features**
- User role: Authenticated users
- Primary use case: Social interaction and engagement
- Key user flows:
  1. Follow/unfollow other users
  2. Favorite/unfavorite articles
  3. View user profiles and following status

**Commenting System**
- User role: Authenticated users
- Primary use case: Article discussion and engagement
- Key user flows:
  1. Add comments to articles
  2. Delete own comments
  3. View all comments on an article

**Content Organization**
- User role: Any user
- Primary use case: Content categorization and discovery
- Key user flows:
  1. View all available tags in the system

### 2.2 User Journey Maps

**Authentication Flow**:
1. User registers with email/username/password → Receives JWT token
2. User logs in with email/password → Receives JWT token
3. User includes JWT token in Authorization header as "Token {jwt}"
4. Server validates JWT for protected endpoints

**Core Content Workflow**:
1. User authenticates and receives token
2. User creates article with title, description, body, tags
3. System generates unique slug from title
4. Article becomes discoverable in public listings
5. Other users can favorite, comment, or follow the author

**Social Interaction Flow**:
1. User discovers articles through listing or feed
2. User can favorite articles to bookmark them
3. User can follow article authors
4. User's feed shows articles from followed authors
5. User can comment on articles for discussion

### 2.3 Business Logic Rules

**Data Validation Rules**:
- User registration requires non-empty email, username, and password
- Email must be unique across all users
- Username must be unique across all users
- Article creation requires non-empty title, description, and body
- Article slugs must be unique (generated from title)

**Business Constraints**:
- Only article authors can update or delete their articles
- Only comment authors can delete their comments
- Users cannot favorite their own articles (not enforced in code)
- Article feed only shows articles from followed users

**Workflow Dependencies**:
- Article creation requires authenticated user
- Comments require existing article
- Following requires existing target user
- Favoriting requires existing article

**Integration Points**:
- Derby embedded database for data persistence
- JWT token generation and validation
- Slug generation using GitHub Slugify library

## 3. Technical Documentation

### 3.1 Architecture Overview

**Technology Stack**:
- Java 8
- Open Liberty 20.0.0.4
- MicroProfile 3.0 (includes Health, JWT, and other specifications)
- JAX-RS 2.0.1 with Jersey 2.30.1 implementation
- JPA 2.2 features with javax.persistence 2.1.0 API compatibility
- Derby 10.14.2.0 embedded database (development and test)
- JWT 1.0 for authentication
- Maven for build management
- JSON processing with org.json library

**Project Structure**:
```
src/main/java/
├── application/
│   ├── errors/          # Validation error messages
│   └── rest/            # JAX-RS REST endpoints
├── core/                # Domain models and DTOs
│   ├── article/         # Article entities
│   ├── comments/        # Comment entities
│   └── user/           # User entities and profiles
├── dao/                # Data Access Objects
└── security/           # JWT token generation
```

**Design Patterns**:
- **Layered Architecture**: Clear separation between REST, business logic, and data access layers
- **Data Access Object (DAO)**: Centralized database operations in UserDao and ArticleDao
- **Request-Response DTOs**: CreateUser, CreateArticle, CreateComment for API requests
- **Entity-JSON Serialization**: Custom toJson() methods for API responses

**Data Flow**:
1. JAX-RS endpoint receives HTTP request
2. Request deserialized to DTO objects
3. JWT token validated for authentication
4. DAO layer performs database operations
5. Entity objects converted to JSON responses
6. HTTP response returned to client

### 3.2 Development Environment

**Prerequisites**:
- Java 8 JDK
- Maven 3.x
- Open Liberty runtime (handled by Maven plugin)

**Installation Steps**:
1. Clone the repository
2. Navigate to project root directory
3. Run `mvn clean install` to build the project
4. Run `mvn liberty:start` to start the server
5. Server will be available at `http://localhost:9080`

**Configuration**:
- Server configuration: `src/main/liberty/config/server.xml`
- Database configuration: Derby embedded database (UserDB) - serves both development runtime and test environments
- JWT configuration: MicroProfile JWT with hardcoded issuer (requires environment-specific configuration)
- CORS configuration: Allows `http://localhost:4100` origin

**Common Commands**:
- **Build**: `mvn clean install`
- **Start server**: `mvn liberty:start`
- **Run server in foreground**: `mvn liberty:run`
- **Stop server**: `mvn liberty:stop`
- **Run tests**: `mvn test`
- **Run integration tests**: `mvn integration-test`
- **Package WAR**: `mvn package`

### 3.3 Code Organization

**Directory Structure**:
- **`application/rest/`**: JAX-RS REST endpoint classes
- **`core/`**: Domain models, entities, and data transfer objects
- **`dao/`**: Data access layer with database operations
- **`security/`**: JWT token generation and validation
- **`src/main/liberty/config/`**: Open Liberty server configuration
- **`src/main/resources/META-INF/`**: JPA persistence configuration

**File Naming Conventions**:
- `*API.java` for REST endpoint classes (UsersAPI, ArticlesAPI)
- `*Dao.java` for Data Access Objects (UserDao, ArticleDao)
- `Create*.java` for request DTOs (CreateUser, CreateArticle)
- Entity classes use domain names (User, Article, Comment)

**Import/Export Patterns**:
- CDI `@Inject` for dependency injection
- JPA entities for database mapping
- JAX-RS annotations for REST endpoint definitions
- MicroProfile JWT for token handling

**State Management**:
- Database persistence through JPA entities
- Stateless REST endpoints with `@RequestScoped`
- JWT tokens for user session management

### 3.4 API Documentation

**Base URL**: `http://localhost:9080/api`

**Authentication**: 
- JWT tokens using Authorization header: `Authorization: Token {jwt}`
- Tokens obtained from `/api/users` (register) or `/api/users/login` endpoints
- Protected endpoints require `@RolesAllowed("users")` annotation

**Endpoints**:

**User Management**:
- `POST /api/users` - User registration (public)
- `POST /api/users/login` - User login (public)
- `GET /api/user` - Get current user (authenticated)
- `PUT /api/user` - Update user profile (authenticated)

**Profile Management**:
- `GET /api/profiles/{username}` - Get user profile (public)
- `POST /api/profiles/{username}/follow` - Follow user (authenticated)
- `DELETE /api/profiles/{username}/follow` - Unfollow user (authenticated)

**Article Management**:
- `GET /api/articles` - List articles with filtering (public)
- `GET /api/articles/feed` - Get personalized feed (authenticated)
- `GET /api/articles/{slug}` - Get single article (public)
- `POST /api/articles` - Create article (authenticated)
- `PUT /api/articles/{slug}` - Update article (authenticated, author only)
- `DELETE /api/articles/{slug}` - Delete article (authenticated, author only)

**Article Engagement**:
- `POST /api/articles/{slug}/favorite` - Favorite article (authenticated)
- `DELETE /api/articles/{slug}/favorite` - Unfavorite article (authenticated)

**Comments**:
- `GET /api/articles/{slug}/comments` - Get article comments (public)
- `POST /api/articles/{slug}/comments` - Add comment (authenticated)
- `DELETE /api/articles/{slug}/comments/{id}` - Delete comment (authenticated, author only)

**Tags**:
- `GET /api/tags` - Get all tags (public)

**Health Monitoring**:
- `GET /health` - MicroProfile Health endpoint (public)
  - Returns JSON status with service availability
  - Used for health checks and monitoring

**Data Models**:

**User Registration/Login**:
```json
{
  "user": {
    "email": "user@example.com",
    "username": "username",
    "password": "password"
  }
}
```

**Article Creation**:
```json
{
  "article": {
    "title": "Article Title",
    "description": "Article description",
    "body": "Article content",
    "tagList": ["tag1", "tag2"]
  }
}
```

**Comment Creation**:
```json
{
  "comment": {
    "body": "Comment text"
  }
}
```

**Error Handling**:
- **422 Unprocessable Entity**: Validation errors, duplicate data
- **404 Not Found**: Resource not found
- **403 Forbidden**: Insufficient permissions
- **401 Unauthorized**: Authentication required

**Error Response Format**:
```json
{
  "errors": {
    "body": ["error message"]
  }
}
```

**Rate Limiting**: Not implemented
**API Versioning**: Not implemented

### 3.5 Data Management

**Database Schema**:
- **USER_TABLE**: User accounts with authentication and profile data
- **Article_Table**: Articles with content, metadata, and author relationships
- **Comments**: Article comments with author relationships
- **FOLLOWED_BY**: Many-to-many relationship for user following
- **Favorited Articles**: One-to-many relationship for user favorites

**Core Entities**:
- **User**: email, username, password, bio, image, followers, favorites
- **Article**: slug, title, description, body, tags, timestamps, author, favorites count
- **Comment**: body, timestamps, author, article relationship
- **Profile**: Separate JPA entity for public user profile display (related to User)

**Migration Strategy**: 
- EclipseLink DDL generation with `create-tables` strategy
- Database schema created automatically on startup
- No explicit migration files (development setup)

**Data Access Patterns**:
- JPA Entity Manager for database operations
- Custom DAO classes for complex queries
- CDI injection for database resource management
- Transaction boundaries using `@Transactional`

**Caching Strategy**: No caching implemented

**Data Validation**:
- Bean validation for required fields (manual checks in API layer)
- Unique constraints on email and username
- Database-level constraints through JPA annotations
- Article slug uniqueness validation

## 4. Development Guidelines

### 4.1 Code Standards

**Coding Style**:
- Java 8 language features
- 4-space indentation
- Descriptive method and variable names
- PascalCase for class names, camelCase for methods/variables

**Framework Conventions**:
- JAX-RS annotations for REST endpoints
- CDI `@Inject` for dependency injection
- JPA annotations for entity mapping
- MicroProfile annotations for JWT handling

**Architecture Standards**:
- Request-scoped REST endpoints
- Transactional DAO operations
- Proper exception handling with custom error responses
- JSON serialization through custom toJson() methods

### 4.2 Testing Strategy

**Testing Framework**: JUnit 4.12 is configured with integration test support.

**Existing Tests**:
- **Integration Tests**: `src/test/java/it/EndpointIT.java`
  - Health endpoint verification (`/health`) - verifies MicroProfile Health endpoint returns UP status
  - Basic articles endpoint test (`/api/articles`) - verifies empty articles list response
  - Uses JAX-RS client for HTTP testing

**Test Configuration**:
- Maven Surefire plugin for unit tests
- Maven Failsafe plugin for integration tests
- Derby database for test scope
- Test server runs on ports 9080 (HTTP) and 9443 (HTTPS)

**Test Categories**:
- **Integration Tests**: API endpoint testing with actual HTTP requests
- **Database Tests**: Configured with Derby test database
- **No Unit Tests**: No service-level or DAO-level unit tests currently implemented

### 4.3 Performance Considerations

**Known Performance Characteristics**:
- Embedded Derby database suitable for development, not production
- No connection pooling configuration
- No caching layer implemented
- Default pagination limits (20 items per page)

**Optimization Opportunities**:
- Replace Derby with production database (PostgreSQL, MySQL)
- Implement database connection pooling
- Add caching for frequently accessed data
- Optimize N+1 queries in article listings with followers/favorites

## 5. Deployment & Operations

### 5.1 Build Process

**Maven Build Configuration**:
- **Compile**: `mvn clean compile` - Compiles Java sources
- **Package**: `mvn package` - Creates WAR artifact (`realworld-liberty.war`)
- **Install**: `mvn clean install` - Full build with tests
- **Dependencies**: Maven automatically resolves and packages dependencies

**Build Artifacts**:
- WAR file: `target/realworld-liberty.war`
- Liberty server package: `target/liberty/wlp/`
- Derby database files: Embedded in shared resources

**Environment Configuration**:
- Server ports configurable via Maven properties
- Database configuration in `server.xml`
- JWT issuer configuration in `server.xml`

### 5.2 Deployment Process

**Local Development**:
- `mvn liberty:run` - Starts server in foreground with live reload
- `mvn liberty:start` - Starts server as background process
- `mvn liberty:stop` - Stops background server process

**Server Configuration**:
- Open Liberty runtime version 20.0.0.4
- MicroProfile 3.0 and JPA 2.2 features enabled
- Derby database auto-created on first startup
- CORS configured for frontend integration

**Environment Variables**:
- `${default.http.port}` - HTTP port (default: 9080)
- `${default.https.port}` - HTTPS port (default: 9443)
- `${appLocation}` - WAR file location
- `${warContext}` - Application context (realworld)

**Important Configuration Notes**:
- JWT issuer is hardcoded to `https://192.168.1.15:9443/jwt/defaultJWT` in both `server.xml` and `JwtGenerator.java`
- This requires environment-specific configuration changes for different deployment environments
- Derby database files are automatically created in `target/liberty/wlp/usr/shared/resources/`

**Health Checks**:
- Health endpoint available at `/health`
- Returns JSON status with service availability

### 5.3 Monitoring & Debugging

**Application Logging**:
- Default Java logging through Open Liberty
- Console output includes server startup, errors, and request logging
- No structured logging framework configured

**Development Debugging**:
- Java debugging port can be enabled in Liberty configuration
- JAX-RS exception mappers for consistent error responses
- Derby database can be accessed via network for debugging

**Performance Monitoring**:
- MicroProfile Metrics available but not configured
- No APM tools integrated
- Basic request logging through Liberty access logs

## 6. Quick Start Guides

### 6.1 For New Developers

**5-Step Onboarding**:
1. Install Java 8 JDK and Maven
2. Clone repository: `git clone [repository-url]`
3. Build project: `mvn clean install`
4. Start server: `mvn liberty:run`
5. Test API: `curl http://localhost:9080/api/articles`

**First Feature Implementation**:
1. **Create a new validation rule**: Add validation logic in `ValidationMessages.java`
2. **Add validation to API**: Implement validation in relevant `*API.java` class
3. **Test the validation**: Add test case in `EndpointIT.java`
4. **Update error responses**: Ensure proper HTTP status codes returned

**Common Pitfalls**:
- **Derby Database Locking**: Stop server properly with `mvn liberty:stop` to avoid database locks
- **JWT Token Format**: Use `Authorization: Token {jwt}` not `Bearer {jwt}`
- **Slug Conflicts**: Article titles must generate unique slugs
- **Transaction Boundaries**: Use `@Transactional` for data modifications

### 6.2 For QA Engineers

**Running Tests**:
- **Unit Tests**: `mvn test` (currently only basic tests exist)
- **Integration Tests**: `mvn integration-test` (tests API endpoints)
- **Full Test Suite**: `mvn clean install` (runs all tests)

**Test Data Management**:
- Database resets on each server restart
- Use API endpoints to create test data
- Derby database files in `target/liberty/wlp/usr/shared/resources/`

**Key Testing Areas**:
- **Authentication Flow**: Registration, login, JWT token validation
- **Article CRUD**: Creation, reading, updating, deletion with proper authorization
- **Social Features**: Following users, favoriting articles, commenting
- **Data Validation**: Required fields, unique constraints, proper error responses
- **Authorization**: Ensuring users can only modify their own content

**Bug Reporting Guidelines**:
- Include curl commands or API request details
- Check server logs in console output
- Verify JWT token format and expiration
- Test with clean database state

## 7. Troubleshooting

### Common Issues

**Database Connection Problems**:
- **Issue**: Derby database locked
- **Solution**: Run `mvn liberty:stop` to properly shutdown server
- **Prevention**: Always stop server cleanly, avoid force-killing processes

**Authentication Failures**:
- **Issue**: JWT token not recognized
- **Solution**: Ensure token format is `Authorization: Token {jwt}`, not `Bearer`
- **Check**: Verify token is not expired and user exists

**Article Slug Conflicts**:
- **Issue**: Article creation fails with slug already exists
- **Solution**: Modify article title to generate unique slug
- **Check**: Slugs are generated from title using slugify library

### Environment Issues

**Maven Build Failures**:
- **Issue**: Compilation errors or dependency conflicts
- **Solution**: Run `mvn clean install` to rebuild from scratch
- **Check**: Ensure Java 8 JDK is installed and JAVA_HOME set

**Server Startup Problems**:
- **Issue**: Port already in use (9080/9443)
- **Solution**: Stop other processes or change ports in `pom.xml`
- **Check**: `netstat -an | grep 9080` to verify port availability

### Runtime Errors

**404 Not Found Errors**:
- **Common Cause**: Incorrect API paths or missing resources
- **Debug**: Check JAX-RS `@Path` annotations and URL construction
- **Verify**: Ensure server started successfully and APIs are deployed

**422 Validation Errors**:
- **Common Cause**: Missing required fields in API requests
- **Debug**: Check request JSON structure matches expected DTOs
- **Verify**: Review `ValidationMessages.java` for specific error conditions

### Performance Issues

**Slow Database Operations**:
- **Cause**: Derby embedded database not optimized for production
- **Solution**: Consider upgrading to PostgreSQL or MySQL for production
- **Temporary**: Restart server to clear Derby locks and optimize

**Memory Usage**:
- **Monitor**: Java heap space usage during development
- **Solution**: Increase JVM heap size if needed: `-Xmx512m`
- **Check**: Derby database file size growth over time
