# MetaEngine OpenAPI Kotlin Ktor

[![Maven Central](https://img.shields.io/maven-central/v/eu.metaengine/metaengine-openapi-kotlin-ktor-maven-plugin.svg)](https://central.sonatype.com/artifact/eu.metaengine/metaengine-openapi-kotlin-ktor-maven-plugin)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

**Generate Kotlin Ktor HTTP client services and models from OpenAPI/Swagger specifications.**

A Maven plugin that produces coroutine-based Ktor `HttpClient` service clients and `kotlinx.serialization` data classes from an OpenAPI spec, with built-in authentication, retries, timeouts, and error handling — straight into your build at `generate-sources`.

---

## Quick Links

- **Maven Central**: [eu.metaengine:metaengine-openapi-kotlin-ktor-maven-plugin](https://central.sonatype.com/artifact/eu.metaengine/metaengine-openapi-kotlin-ktor-maven-plugin)
- **NuGet Package**: [MetaEngine.Kotlin.OpenApi.Ktor](https://www.nuget.org/packages/MetaEngine.Kotlin.OpenApi.Ktor)
- **Website**: [metaengine.eu](https://www.metaengine.eu)

---

## Features

- ✅ **Ktor HTTP client** - Coroutine-based `suspend fun` service clients with a constructor-injected base URL
- ✅ **kotlinx.serialization models** - Kotlin `data class` types with `@Serializable`
- ✅ **Authentication** - Bearer token, basic auth, and custom headers from environment variables
- ✅ **Retries** - Exponential-backoff retries with a configurable max attempt count
- ✅ **Timeouts** - Single timeout, or split connect / read / write timeouts
- ✅ **Error Handling** - Smart error handling on the Ktor client
- ✅ **KDoc** - Optional doc-comment generation from OpenAPI descriptions and examples
- ✅ **Validation Annotations** - Optional Jakarta Bean Validation annotations from spec constraints
- ✅ **Null safety** - Required fields non-nullable, optional fields nullable (`?`)
- ✅ **Tag Filtering** - Generate only the operations you need
- ✅ **Options Object** - Collapse long parameter lists into an options object past a threshold

---

## Requirements

- **JDK 11** or later
- **Maven 3.6** or later
- **.NET 8.0 runtime** ([Download](https://dotnet.microsoft.com/download)) — the plugin runs the bundled MetaEngine generator via `dotnet`, so the runtime must be on your `PATH`

---

## Quick Start

Add the plugin to your `pom.xml` and bind it to the `generate-sources` phase:

```xml
<build>
  <plugins>
    <plugin>
      <groupId>eu.metaengine</groupId>
      <artifactId>metaengine-openapi-kotlin-ktor-maven-plugin</artifactId>
      <version>1.0.0</version>
      <executions>
        <execution>
          <goals><goal>generate</goal></goals>
          <configuration>
            <inputSpec>src/main/resources/openapi.json</inputSpec>
            <packageName>com.example.api</packageName>
            <documentation>true</documentation>
          </configuration>
        </execution>
      </executions>
    </plugin>
  </plugins>
</build>
```

Then generate (the output is added to your compile source roots automatically):

```bash
mvn generate-sources
```

The plugin can also be run directly without a lifecycle binding:

```bash
mvn eu.metaengine:metaengine-openapi-kotlin-ktor-maven-plugin:generate \
  -Dmetaengine.inputSpec=src/main/resources/openapi.json \
  -Dmetaengine.packageName=com.example.api
```

---

## Configuration Options

Every option is a `<configuration>` element, also settable on the command line via its `metaengine.*` property.

| Element | Property | Description | Default |
|---|---|---|---|
| `inputSpec` *(required)* | `metaengine.inputSpec` | OpenAPI spec file path or URL | - |
| `packageName` *(required)* | `metaengine.packageName` | Kotlin package for generated code | - |
| `outputDirectory` | `metaengine.outputDirectory` | Output directory | `${project.build.directory}/generated-sources/metaengine` |
| `includeTags` | `metaengine.includeTags` | Only generate operations with these tags (comma-separated) | - |
| `documentation` | `metaengine.documentation` | Generate KDoc comments | `false` |
| `bearerAuth` | `metaengine.bearerAuth` | Env var name holding the bearer token | - |
| `bearerAuthHeader` | `metaengine.bearerAuthHeader` | Custom header name for bearer auth | `Authorization` |
| `basicAuth` | `metaengine.basicAuth` | Env var names for basic auth (`USER_VAR,PASS_VAR`) | - |
| `customHeaders` | `metaengine.customHeaders` | Custom headers from env vars (`Header-Name:ENV_VAR`, repeatable) | - |
| `timeout` | `metaengine.timeout` | Request timeout in seconds (connect + read + write) | - |
| `timeoutConnect` | `metaengine.timeoutConnect` | Connect timeout in seconds | - |
| `timeoutRead` | `metaengine.timeoutRead` | Read timeout in seconds (request timeout) | - |
| `timeoutWrite` | `metaengine.timeoutWrite` | Write timeout in seconds (socket I/O) | - |
| `retry` | `metaengine.retry` | Enable retries with exponential backoff (max attempts) | `3` when enabled |
| `errorHandling` | `metaengine.errorHandling` | Enable smart error handling on the Ktor client | `false` |
| `validationAnnotations` | `metaengine.validationAnnotations` | Emit Jakarta Bean Validation annotations on data classes | `false` |
| `strictEnums` | `metaengine.strictEnums` | Strict string enums — no synthetic `UNKNOWN` fallback for unrecognized wire values | `false` |
| `optionsThreshold` | `metaengine.optionsThreshold` | Parameter count that triggers the options-object pattern | `4` |
| `strictValidation` | `metaengine.strictValidation` | Enable strict OpenAPI validation | `false` |
| `clean` | `metaengine.clean` | Clean the output directory before generation | `false` |
| `verbose` | `metaengine.verbose` | Enable verbose logging | `false` |

---

## Production-Ready Configuration

```xml
<configuration>
  <inputSpec>src/main/resources/openapi.json</inputSpec>
  <packageName>com.example.api</packageName>
  <documentation>true</documentation>

  <!-- Auth from environment variables -->
  <bearerAuth>API_TOKEN</bearerAuth>

  <!-- Resilience -->
  <timeout>30</timeout>
  <retry>3</retry>
  <errorHandling>true</errorHandling>

  <!-- Generate only what you need -->
  <includeTags>pets,store</includeTags>
</configuration>
```

Authentication values are read from environment variables at runtime — secrets never end up in your generated source or your `pom.xml`.

---

## Generated Code Structure

```
target/generated-sources/metaengine/
  └── com/example/api/
      ├── model/          # One file per schema (@Serializable data classes)
      │   ├── Pet.kt
      │   ├── Category.kt
      │   └── ...
      └── client/         # One Ktor client per tag (HttpClient-based, suspend funs)
          ├── PetClient.kt
          └── StoreClient.kt
```

Each client is a plain Kotlin class — construct it with a Ktor `HttpClient` and a base URL (`PetClient(httpClient, baseUrl)`) and call its coroutine `suspend fun` operations. Models are `@Serializable` data classes ready for `kotlinx.serialization`.

---

## Support

- **Issues**: [GitHub Issues](https://github.com/meta-engine/openapi-kotlin-ktor/issues)
- **Email**: info@metaengine.eu
- **Website**: [metaengine.eu](https://www.metaengine.eu)

---

## License

MIT License - see [LICENSE](./LICENSE) file for details.

---

## About This Repository

This is the **documentation and issue tracking repository** for MetaEngine OpenAPI Kotlin Ktor. The plugin is published to [Maven Central](https://central.sonatype.com/artifact/eu.metaengine/metaengine-openapi-kotlin-ktor-maven-plugin).

Source code is proprietary, but the plugin is free to use under the MIT license.
