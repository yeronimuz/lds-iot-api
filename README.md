# OpenAPI generated API stub

Spring Framework stub

## Prerequisites (TypeScript/Angular client packaging)
The Gradle tasks `openApiGenerateTypescript`, `npmInstallTypescript`, and `npmPackTypescript` generate and pack an interface for an Angular 22-compatible client.

Use a Node.js version compatible with Angular CLI 22:
- `node`: `^22.22.3` (recommended), `^24.15.0`, or `>=26.0.0`
- `npm`: `>=8.0.0` (npm 10+ recommended)

Quick check:
```bash
node -v
npm -v
```

## Angular 22 compatibility steps applied in this build
To make packaging the generated client work reliably with Angular 22, these build changes were required:


1. Normalize generated npm metadata before install (`normalizeTypescriptPackageForNg` task):
   - force `devDependencies.ng-packagr` to `^22.0.0`
   - force `devDependencies.typescript` to `>=6.0.0 <6.1.0`
2. Remove stale lock files in generated client folder before `npm install`:
   - `package-lock.json`
   - `npm-shrinkwrap.json`
3. Keep generated outputs isolated to avoid Gradle validation conflicts:
   - server generator output goes to `src/gen` (not project root)

Typical build command:
```bash
./gradlew build
```

Optional overrides:
```bash
./gradlew build -PngVersion=22.0.1
./gradlew build -PnpmExecutable=/absolute/path/to/npm
```

## Overview
This project generates a Spring API stub and a TypeScript Angular client from `src/main/resources/openapi.yaml`.

The generated Spring interfaces and models are written to `src/gen` and can be implemented in your application controllers.
For example, implement the generated `SiteApi` contract in your own controller:
```java
@Controller
public class SiteController implements SiteApi {
    // implement all SiteApi methods
}
```

The generated Angular client (services/models for Site, Device, Sensor, and User endpoints) is built and packed as an npm package via Gradle.

You can also use the generated interfaces to create Spring Cloud OpenFeign clients. Example:
```java
@FeignClient(name = "site", url = "http://localhost:8080")
public interface SiteClient extends SiteApi {
}
```
