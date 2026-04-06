# Changelog
## 2026-04-06
### Fixed application startup configuration
- Updated `src/main/resources/application.properties` to make datasource config resilient when deployment environment variables are not present.
- Changed:
  - `spring.datasource.url` from `${JDBC_DATABASE_URL}` to `${JDBC_DATABASE_URL:jdbc:h2:mem:outfitdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE}`
  - `spring.datasource.username` from `${JDBC_DATABASE_USERNAME}` to `${JDBC_DATABASE_USERNAME:sa}`
  - `spring.datasource.password` from `${JDBC_DATABASE_PASSWORD}` to `${JDBC_DATABASE_PASSWORD:}`
- Changed:
  - `spring.jpa.hibernate.ddl-auto` from `none` to `${SPRING_JPA_HIBERNATE_DDL_AUTO:update}`
- Added:
  - `spring.h2.console.enabled=true`
  - `spring.h2.console.path=/h2`

### Verification
- Ran the application with `./mvnw spring-boot:run`.
- Confirmed successful startup:
  - Build reported `BUILD SUCCESS`
  - Embedded Tomcat started on port `8080`
  - `Started OutfitApplication` appeared in logs
  - H2 console available at `/h2`
### Configuration profile split (dev/prod)
- Refactored Spring config into profile-based files for cleaner local vs deployment separation.
- Updated `src/main/resources/application.properties` to keep only shared settings and default profile activation:
  - `spring.profiles.active=${SPRING_PROFILES_ACTIVE:dev}`
  - Shared cloud/weather/JPA-validation settings
- Added `src/main/resources/application-dev.properties` for local development:
  - H2 datasource settings
  - `spring.jpa.hibernate.ddl-auto=update`
  - H2 console enabled at `/h2`
- Added `src/main/resources/application-prod.properties` for deployment:
  - Datasource values from environment variables only
  - `spring.jpa.hibernate.ddl-auto=${SPRING_JPA_HIBERNATE_DDL_AUTO:none}`
  - SQL logging disabled
