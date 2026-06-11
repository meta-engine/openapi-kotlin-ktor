# Changelog

See the [Maven Central version history](https://central.sonatype.com/artifact/eu.metaengine/metaengine-openapi-kotlin-ktor-maven-plugin/versions) for all published versions.

## 1.0.0

Initial release — generate Kotlin Ktor HTTP client services and models from OpenAPI specifications.

- **Ktor HTTP client services** — coroutine-based `suspend fun` clients with a constructor-injected base URL
- **Models** — `@Serializable` Kotlin data classes generated from OpenAPI schemas, with null safety (required fields non-nullable, optional fields nullable)
- **Authentication** — `bearerAuth`, `bearerAuthHeader`, `basicAuth`, and repeatable `customHeaders` from environment variables
- **Resilience** — `timeout` (or split `timeoutConnect` / `timeoutRead` / `timeoutWrite`) and `retry` with exponential backoff
- **Error handling** — `errorHandling` enables smart error handling on the Ktor client
- **KDoc** — `documentation` emits doc comments from OpenAPI descriptions and examples
- **Validation annotations** — `validationAnnotations` emits Jakarta Bean Validation annotations from spec constraints
- **Strict enums** — `strictEnums` opts out of the synthetic `UNKNOWN` enum fallback
- **Tag filtering** — `includeTags` generates only the operations you need
- **Options-object pattern** — `optionsThreshold` collapses long parameter lists into an options object
- **Strict validation** — `strictValidation` for stricter OpenAPI spec checking
- **Output management** — `clean` and configurable `outputDirectory`
