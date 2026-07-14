# Ecosystem-Specific Upgrade Guide

## Python Ecosystem

### Python Version Migration

| From → To   | Key Breaking Changes                                                                       | Migration Effort |
|-------------|--------------------------------------------------------------------------------------------|------------------|
| 3.8 → 3.9   | `dict` merge operators, `str.removeprefix()`/`removesuffix()`, typing improvements         | Low              |
| 3.9 → 3.10  | Pattern matching (`match/case`), `Union` via `X \| Y`, better error messages               | Low              |
| 3.10 → 3.11 | Exception groups, `TaskGroup`, `tomllib`, significant performance boost                    | Low              |
| 3.11 → 3.12 | Type parameter syntax, `type` statement, f-string improvements, removed `distutils`        | Medium           |
| 3.12 → 3.13 | Free-threaded mode (experimental), JIT compiler (experimental), removed `tkinter` optional | Medium           |
| 3.13 → 3.14 | `typing` improvements, `dbm.sqlite3`, enhanced error messages, performance optimizations   | Low              |

**Python migration checklist:**
- Check `distutils` removal (3.12+) — use `setuptools` instead
- Check `asyncio` API changes across versions
- Verify `typing` module compatibility (deprecated aliases removed in 3.10+)
- Check C extension compatibility (ABI changes)
- Run `python -W all` to catch deprecation warnings before upgrading
- Python 3.14+ requires checking free-threaded mode compatibility if using experimental features

### Django Upgrade Guide

| From → To | Key Breaking Changes                                                                                              |
|-----------|-------------------------------------------------------------------------------------------------------------------|
| 3.2 → 4.0 | Removed `ugettext*`, `url()` → `re_path()`, `default_app_config` removed, `USE_L10N` deprecated                   |
| 4.0 → 4.1 | `django.contrib.postgres.fields.JSONField` removed (use `models.JSONField`), `CSRF_COOKIE_MASKED` default changed |
| 4.1 → 4.2 | STORAGES setting replaces DEFAULT_FILE_STORAGE/MEDIA_URL, `FormMixin.get_prefix()` removed                        |
| 4.2 → 5.0 | **Python 3.10+ required**, MariaDB 10.4+ required, `django.utils.timezone.utc` deprecated                         |
| 5.0 → 5.1 | `django.contrib.admin` widget changes, database backend API changes                                               |
| 5.1 → 5.2 | `django.contrib.postgres` operator changes, `QuerySet.aiterator()` improvements, `STORAGES` changes finalized     |
| 5.2 → 6.0 | **Python 3.10+ required**, `django.utils.timezone.utc` removed, `USE_TZ` default changed, admin UI overhaul       |

**Django migration strategy:**
1. Upgrade one minor version at a time (3.2 → 4.0 → 4.1 → 4.2 → 5.0 → 5.1 → 5.2 → 6.0)
2. Fix all deprecation warnings before each upgrade
3. Run `python manage.py check --deploy` after each upgrade
4. Check DRF compatibility: DRF 3.14+ for Django 4.x, DRF 3.15+ for Django 5.x, DRF 4.0+ for Django 6.x
5. Check Celery compatibility: Celery 5.3+ for Django 4.x, Celery 5.4+ for Django 5.x/6.x

### Flask Upgrade Guide

| From → To | Key Breaking Changes                                                                                                           |
|-----------|--------------------------------------------------------------------------------------------------------------------------------|
| 1.x → 2.x | `Flask.run()` defaults changed, JSON provider API changed, `json` module deprecated                                            |
| 2.x → 3.x | **Python 3.8+ required**, removed deprecated `flask.json.htmlsafe_dump`, `send_file` changes, `Blueprint` registration changes |

### FastAPI Upgrade Guide

| From → To     | Key Breaking Changes                                                   |
|---------------|------------------------------------------------------------------------|
| 0.8x → 0.9x   | Pydantic v1 → v2 migration required, `response_model` behavior changes |
| 0.9x → 0.10x+ | Starlette 0.28+, Pydantic v2 required, `jsonable_encoder` changes      |

**Key concern**: Pydantic v1 → v2 is a major breaking change affecting FastAPI projects. Check model validation, `Config` class → `model_config`, `@validator` → `@field_validator`.

---

## Vue / Frontend Ecosystem

### Vue 2 → Vue 3 Migration (Cross-Major)

**This is the most complex frontend migration. Requires significant code changes.**

| Area                      | Vue 2                         | Vue 3                                          | Migration Action                       |
|---------------------------|-------------------------------|------------------------------------------------|----------------------------------------|
| **Entry**                 | `new Vue()`                   | `createApp()`                                  | Rewrite app initialization             |
| **Composition API**       | Not available (or via plugin) | Built-in                                       | Optional but recommended               |
| **Global API**            | `Vue.use()`, `Vue.mixin()`    | `app.use()`, `app.mixin()`                     | Refactor plugin/mixin registration     |
| **Event Bus**             | `$on`, `$off`, `$once`        | Removed                                        | Use `mitt` or `tiny-emitter`           |
| **Filters**               | Supported                     | Removed                                        | Replace with computed/methods          |
| **`$children`**           | Available                     | Removed                                        | Use `ref` or provide/inject            |
| **`$listeners`**          | Available                     | Removed                                        | Merged into `$attrs`                   |
| **`$destroy`**            | Available                     | Removed                                        | Use `app.unmount()`                    |
| **Transition classes**    | `v-enter`, `v-leave`          | `v-enter-from`, `v-leave-from`                 | Rename CSS classes                     |
| **`key` on conditionals** | Not required                  | Required for `v-if/v-for` on same element      | Add explicit keys                      |
| **`v-model`**             | Single prop+event             | Multiple `v-model` bindings, `modelValue` prop | Update component props                 |
| **Teleport**              | Not available                 | `<Teleport>`                                   | Use for modals/popups                  |
| **Fragments**             | Not available                 | Supported                                      | Remove single root element requirement |
| **`<script setup>`**      | Not available                 | Supported                                      | Optional but recommended               |

**Vue ecosystem library migration:**

| Vue 2 Library          | Vue 3 Equivalent              | Notes                                                |
|------------------------|-------------------------------|------------------------------------------------------|
| Vuetify 2.x            | Vuetify 3.x                   | Complete rewrite, different component API            |
| Element UI             | Element Plus                  | Different import paths, some API changes             |
| Vue Router 3.x         | Vue Router 4.x                | `createRouter()`, `createWebHistory()`, `useRoute()` |
| Vuex 3.x               | Pinia (recommended) or Vuex 4 | Pinia is now the official recommendation             |
| Vue CLI                | Vite                          | Vue CLI is in maintenance mode                       |
| `@vue/composition-api` | Built-in                      | Remove plugin, use native API                        |

**Migration tool**: Use `@vue/compat` (build with compat mode) for gradual migration, then remove compat layer.

### React Major Version Upgrade

| From → To | Key Breaking Changes                                                                                          |
|-----------|---------------------------------------------------------------------------------------------------------------|
| 16 → 17   | No new features, but changed event delegation (no longer on `document`), effect cleanup timing                |
| 17 → 18   | `ReactDOM.render()` → `createRoot()`, automatic batching, `useId`, Suspense for SSR, concurrent features      |
| 18 → 19   | `ReactDOM.render` removed, React Compiler (opt-in), Server Components, Actions API, `use()` hook, ref as prop |

**React ecosystem library compatibility:**

| Library      | React 17 | React 18  | React 19 |
|--------------|----------|-----------|----------|
| Next.js      | 12.x     | 13.x-14.x | 15.x     |
| React Router | v5/v6    | v6        | v6/v7    |
| Redux        | 4.x      | 4.x/5.x   | 5.x      |
| Material UI  | v4/v5    | v5        | v5/v6    |
| Ant Design   | v4       | v4/v5     | v5       |

### Node.js Version Upgrade

| From → To | Key Breaking Changes                                                                    |
|-----------|-----------------------------------------------------------------------------------------|
| 16 → 18   | `fetch` available globally, `--experimental-fetch` removed, Web Crypto API, OpenSSL 3.0 |
| 18 → 20   | Permission model, single executable applications, stable test runner, V8 11.3           |
| 20 → 22   | `require()` for ESM (experimental), WebSocket client, V8 12.x                           |
| 22 → 24   | `node:sqlite` built-in module, permission model v2, `navigator.credentials` stable      |
| 24 → 26   | V8 14.x, enhanced `fetch` API, improved test runner, `require()` ESM improvements       |

**Node.js migration checklist:**
- Check native addon compatibility (node-gyp rebuild)
- Check `crypto` API changes (OpenSSL version bumps)
- Verify `http`/`https` default behavior changes
- Check `fs` API changes (recursive options, etc.)
- Test with `--pending-deprecation` flag first
- Note: Node.js 20 reached EOL 2026-04, Node.js 24 is current Active LTS (Krypton)
- From Node.js 27, release cycle changes to annual with all versions entering LTS

### npm/yarn/pnpm Migration

| Change                  | Impact                                | Action                                           |
|-------------------------|---------------------------------------|--------------------------------------------------|
| npm 6 → 7+              | Auto peer dependency installation     | May cause install failures if peer deps conflict |
| npm 7 → 8+              | `--legacy-peer-deps` behavior changes | Check peer dependency resolution                 |
| yarn v1 → v2/v3 (Berry) | PnP by default, no `node_modules`     | Major migration, check `.pnp.cjs` compatibility  |
| pnpm 7 → 8+             | Stricter dependency resolution        | Check phantom dependency issues                  |

---

## Java Ecosystem

### Java Version Migration

| From → To | Key Breaking Changes                                                                                                              | Migration Effort                                                 |
|-----------|-----------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------|
| 8 → 11    | Removed Java EE modules (`javax.xml.bind`, `javax.annotation`, etc.), removed `javah`, HTTP Client API, module system enforcement | **High** — need to add explicit dependencies for removed modules |
| 11 → 17   | Sealed classes, pattern matching, records, text blocks, `SecurityManager` deprecated                                              | Medium                                                           |
| 17 → 21   | Virtual threads, pattern matching for switch, sequenced collections, `SecurityManager` removed                                    | Medium                                                           |

**Java 8 → 11+ critical migration items:**

| Removed Module            | Replacement Dependency                                                      |
|---------------------------|-----------------------------------------------------------------------------|
| `javax.xml.bind` (JAXB)   | `jakarta.xml.bind:jakarta.xml.bind-api` + `org.glassfish.jaxb:jaxb-runtime` |
| `javax.annotation`        | `jakarta.annotation:jakarta.annotation-api`                                 |
| `javax.activation`        | `jakarta.activation:jakarta.activation-api`                                 |
| `java.xml.ws` (JAX-WS)    | `jakarta.xml.ws:jakarta.xml.ws-api`                                         |
| `java.xml.bind`           | `jakarta.xml.bind:jakarta.xml.bind-api`                                     |
| `java.corba`              | Use GlassFish CORBA ORB                                                     |
| `javax.transaction` (JTA) | `jakarta.transaction:jakarta.transaction-api`                               |

**javax → jakarta namespace migration:**
- Java EE 8 (`javax.*`) → Jakarta EE 9+ (`jakarta.*`)
- This is required for Spring Boot 3.x, Tomcat 10+, and other Jakarta EE 9+ compatible servers
- Package rename is mechanical but extensive — affects imports across the entire codebase

### Maven/Gradle Plugin Compatibility

| Tool                  | Upgrade Concern                                                                |
|-----------------------|--------------------------------------------------------------------------------|
| Maven Compiler Plugin | Must set `<release>` or `<source>/<target>` for new Java version               |
| Maven Surefire Plugin | Version 3.x required for Java 16+ (module access)                              |
| Gradle                | Gradle 7.3+ for Java 17, Gradle 8.5+ for Java 21                               |
| Lombok                | Must upgrade Lombok for each Java version (annotation processor compatibility) |
| MapStruct             | Version compatibility with Java version and annotation processor               |

---

## Spring Ecosystem

### Spring Boot 2.x → 3.x Migration (Major)

**This is the most impactful Spring upgrade in recent years. Requires Java 17+ and Jakarta EE 9+.**

| Area                                      | Spring Boot 2.x       | Spring Boot 3.x          | Action Required                                         |
|-------------------------------------------|-----------------------|--------------------------|---------------------------------------------------------|
| **Java Version**                          | 8+                    | **17+**                  | Upgrade JDK                                             |
| **Namespace**                             | `javax.*`             | `jakarta.*`              | Rename all imports                                      |
| **Spring Framework**                      | 5.3.x                 | 6.0.x+                   | Major framework upgrade                                 |
| **Tomcat (embedded)**                     | 9.0.x                 | 10.1.x                   | Jakarta EE 9+ servlet API                               |
| **Spring Security**                       | 5.x                   | 6.x                      | `WebSecurityConfigurerAdapter` removed                  |
| **`spring.config.use-legacy-processing`** | Available             | Removed                  | Must use new config processing                          |
| **`spring.data.jpa.enable-transactions`** | Default true          | Default true             | No change                                               |
| **Actuator**                              | `/actuator`           | `/actuator`              | Endpoint structure mostly same, security config changed |
| **`@ConstructorBinding`**                 | On class level        | On constructor level     | Move annotation                                         |
| **`SpringApplication.setBannerMode()`**   | Accepts `Banner.Mode` | Accepts `ResourceLoader` | API change                                              |

**Spring Security 5 → 6 breaking changes:**

| Change                            | Details                                                             |
|-----------------------------------|---------------------------------------------------------------------|
| `WebSecurityConfigurerAdapter`    | **Removed** — use `SecurityFilterChain` bean + `@EnableWebSecurity` |
| `antMatchers()` / `mvcMatchers()` | Replaced by `requestMatchers()`                                     |
| `authorizeRequests()`             | Replaced by `authorizeHttpRequests()`                               |
| CSRF defaults                     | `CookieCsrfTokenRepository` default changed                         |
| CORS                              | Stricter default configuration                                      |
| `@EnableGlobalMethodSecurity`     | Replaced by `@EnableMethodSecurity`                                 |

**Spring Boot version mapping (for coordinated upgrades):**

| Spring Boot | Spring Framework | Spring Security | Embedded Tomcat | Java Min |
|-------------|------------------|-----------------|-----------------|----------|
| 2.6.x       | 5.3.x            | 5.6.x           | 9.0.x           | 8        |
| 2.7.x       | 5.3.x            | 5.7.x           | 9.0.x           | 8        |
| 3.0.x       | 6.0.x            | 6.0.x           | 10.1.x          | 17       |
| 3.1.x       | 6.0.x            | 6.1.x           | 10.1.x          | 17       |
| 3.2.x       | 6.1.x            | 6.2.x           | 10.1.x          | 17       |
| 3.3.x       | 6.1.x            | 6.3.x           | 10.1.x          | 17       |
| 3.4.x       | 6.2.x            | 6.4.x           | 10.1.x          | 17       |
| 3.5.x       | 6.2.x            | 6.4.x           | 10.1.x          | 17       |
| 4.0.x       | 7.0.x            | 7.0.x           | 11.0.x          | 17       |
| 4.1.x       | 7.0.x            | 7.0.x           | 11.0.x          | 17       |

### Spring Boot 3.x → 4.x Migration (Major)

**Spring Boot 4.0 (released Nov 2025) is a major upgrade. Requires Java 17+ and Jakarta EE 11+.**

| Area                     | Spring Boot 3.x | Spring Boot 4.x     | Action Required                                |
|--------------------------|-----------------|---------------------|------------------------------------------------|
| **Jakarta EE**           | Jakarta EE 9/10 | **Jakarta EE 11**   | Check Jakarta EE 11 API changes                |
| **Jackson**              | 2.x             | **3.x**             | Check Jackson 3 API changes                    |
| **Embedded Tomcat**      | 10.1.x          | **11.0.x**          | Servlet 6.1 API changes                        |
| **Undertow**             | Supported       | **Removed**         | Migrate to Tomcat or Jetty                     |
| **Auto-config modules**  | Single JAR      | **Split modules**   | Check dependency changes                       |
| **JSpecify null-safety** | Not available   | **Built-in**        | Optional but recommended                       |
| **Gradle**               | 7.6.4+/8.4+     | **8.14+/9.x**       | Upgrade Gradle version                         |
| **Spring gRPC**          | Not built-in    | **Built-in (4.1+)** | Use native auto-config instead of manual setup |

### Spring Cloud Version Alignment

| Spring Boot | Spring Cloud | Spring Cloud Gateway | Spring Cloud Config |
|-------------|--------------|----------------------|---------------------|
| 2.6.x       | 2021.0.x     | 3.1.x                | 3.1.x               |
| 2.7.x       | 2021.0.x     | 3.1.x                | 3.1.x               |
| 3.0.x       | 2022.0.x     | 4.0.x                | 4.0.x               |
| 3.1.x       | 2022.0.x     | 4.0.x                | 4.0.x               |
| 3.2.x       | 2023.0.x     | 4.1.x                | 4.1.x               |
| 3.3.x       | 2023.0.x     | 4.1.x                | 4.1.x               |
| 3.4.x       | 2024.0.x     | 4.2.x                | 4.2.x               |
| 4.0.x       | 2025.0.x     | 4.3.x                | 4.3.x               |

---

## Standalone Components

### Apache Tomcat

| From → To   | Key Breaking Changes                                                                       |
|-------------|--------------------------------------------------------------------------------------------|
| 8.5 → 9.0   | Servlet 4.0, JSP 2.3, EL 3.0, WebSocket 1.1, minimum Java 7                                |
| 9.0 → 10.1  | **Jakarta EE 9+ (javax → jakarta)**, Servlet 5.0/6.0, minimum Java 11, HTTP/2 improvements |
| 10.1 → 11.0 | Servlet 6.1, Jakarta EE 11, minimum Java 17, virtual threads support                       |

**Tomcat migration checklist:**
- 9 → 10.1: Full `javax.*` → `jakarta.*` package rename in all servlets, filters, listeners
- Check `web.xml` schema version update
- Verify `server.xml` connector configuration compatibility
- Check custom Valve/Realm/LifecycleListener API changes
- Update `context.xml` if using custom resources

### Nginx

| From → To   | Key Breaking Changes                                                |
|-------------|---------------------------------------------------------------------|
| 1.18 → 1.20 | HTTP/2 defaults changes, `ssl_protocols` defaults updated           |
| 1.20 → 1.22 | `map` directive changes, stream module improvements                 |
| 1.22 → 1.24 | `ssl_session_ticket` key changes, QUIC/HTTP3 support (experimental) |
| 1.24 → 1.26 | Enhanced HTTP/3, improved proxy protocol handling                   |
| 1.26 → 1.28 | Stable HTTP/3, improved QUIC, `proxy_protocol` enhancements         |
| 1.28 → 1.30 | Security hardening, njs 1.0 (QuickJS-based), CVE fixes              |

**Nginx migration checklist:**
- Review `ssl_protocols` and `ssl_ciphers` defaults (older protocols removed)
- Check `proxy_pass` behavior changes
- Verify custom module compatibility (third-party modules)
- Review `nginx.conf` directive deprecations
- Test `location` block matching behavior
- Note: Nginx 1.30 is current stable, 1.31 is mainline (as of Jul 2026)
- Check CVE-2026-42530 (HTTP/3 UAF) and CVE-2026-42055 (buffer overflow) if using 1.31.x

### MySQL

| From → To | Key Breaking Changes                                                                                                                                                              |
|-----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 5.7 → 8.0 | Default auth plugin changed (`caching_sha2_password`), `utf8mb4` default charset, reserved keywords (`RANK`, `ROW_NUMBER`), `GROUP BY` no longer implicit sort, JSON improvements |
| 8.0 → 8.4 | New authentication options, deprecation of `mysql_native_password`, optimizer changes                                                                                             |
| 8.4 → 9.0 | JSON enhancements, improved replication, performance optimizations (expected 2026+)                                                                                               |

**MySQL migration checklist:**
- Check `sql_mode` changes (especially `ONLY_FULL_GROUP_BY`)
- Verify reserved keyword conflicts in table/column names
- Test authentication plugin compatibility with application drivers
- Check character set migration (`utf8` → `utf8mb4`)
- Review deprecated features removed in target version
- Run `mysqlcheck` after upgrade
- Test replication compatibility if using master-slave setup
- Note: MySQL 8.4 LTS is recommended for new projects; 8.0 is in maintenance mode

### PostgreSQL

| From → To | Key Breaking Changes                                                                                                                                     |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| 12 → 13   | `default_statistics_target` changes, `enable_incremental_sort` default on, `jit` default on, `wal_level` minimum `replica`, removed `default_with_oids`  |
| 13 → 14   | `search_path` no longer includes `$user` implicitly in some contexts, `SET` requires privilege, `pg_stat_activity` column changes, `VACUUM` improvements |
| 14 → 15   | `wal_level` minimum `replica`, `SET ROLE` behavior changes, removed `CREATE RULE` privilege, `jsonb` path changes, `GSSAPI` encryption default on        |
| 15 → 16   | `pg_hba.conf` changes, `SCRAM-SHA-256` default auth, `SQL/JSON` enhancements, logical replication improvements, `VACUUM FREEZE` improvements             |
| 16 → 17   | `pg_createsubscriber` tool, JSON table constructs, `SQL_STANDARD` date style, logical replication failover improvements                                  |
| 17 → 18   | Native vector search, improved JSON processing, S3 table queries, `MERGE` enhancements, `pg_createsubscriber` improvements                               |

**PostgreSQL migration checklist:**
- Check `wal_level` compatibility (minimum raised to `replica` in 15)
- Verify authentication plugin changes (`md5` → `scram-sha-256` default in 16)
- Review `pg_hba.conf` rules for compatibility with new auth defaults
- Check deprecated/removed configuration parameters
- Test extensions compatibility (`pg_upgrade --check` first)
- Review `search_path` behavior changes (14+)
- Check `jsonb` operator and function changes
- Test replication setup compatibility if using streaming replication
- Run `pg_upgrade --check` before actual upgrade
- Check client driver compatibility (psycopg2/psycopg3, JDBC, node-postgres)
- Review SQL syntax deprecations in target version
- Note: PostgreSQL 13 reached EOL (Nov 2025), PostgreSQL 18 is latest (Nov 2025)

### Redis

| From → To | Key Breaking Changes                                                                                                   |
|-----------|------------------------------------------------------------------------------------------------------------------------|
| 5 → 6     | ACL users, SSL/TLS support, client-side caching, RDB version 10                                                        |
| 6 → 7     | Functions replace Lua scripts, multi-part AOF, `SHUTDOWN` changes, `COPY` command                                      |
| 7 → 8     | **AGPLv3 license**, 9 new data types (Vector Set, JSON, Time Series, etc.), I/O multi-threading, 36% latency reduction |

**Redis migration checklist:**
- Check `requirepass` → ACL user authentication migration
- Verify RDB file format compatibility
- Check deprecated command removals
- Test client library compatibility
- Note: Redis 8.0 returned to open source (AGPLv3); check license implications
- Redis 8.x introduces Vector Set for AI/RAG workloads, native JSON, Time Series
- Performance improvement: ~36% latency reduction vs 7.x

### RabbitMQ

| From → To   | Key Breaking Changes                                                        |
|-------------|-----------------------------------------------------------------------------|
| 3.8 → 3.9   | Feature flags required, Erlang/OTP 23+ required, classic queue improvements |
| 3.9 → 3.10  | Quorum queues improvements, Erlang/OTP 24+ required                         |
| 3.10 → 3.11 | Stream queues, Erlang/OTP 25+ required                                      |
| 3.11 → 3.12 | `rabbitmqadmin` changes, feature flag v2                                    |
| 3.12 → 3.13 | Stream filtering, Erlang/OTP 26+ required                                   |
| 3.13 → 4.0  | Khepri metadata store, Erlang/OTP 27+ required, AMQP 1.0 improvements       |
| 4.0 → 4.2   | Stream improvements, CVE fixes (CVE-2026-57217 topic auth bypass)           |

**RabbitMQ migration checklist:**
- Check Erlang/OTP version compatibility (must upgrade Erlang first)
- Verify feature flags are enabled before upgrade
- Test queue type compatibility (classic → quorum migration)
- Check client library compatibility (AMQP 0-9-1 vs 1.0)
- Review plugin compatibility
- Note: RabbitMQ 4.x requires Khepri metadata store; plan migration from Mnesia
- RabbitMQ 3.13 still supported for security patches; 4.2 is latest stable

---

## Cross-Cutting: Common Upgrade Pitfalls by Tech Stack

| Pitfall                        | Python                | Vue/JS                             | Java/Spring                                     |
|--------------------------------|-----------------------|------------------------------------|-------------------------------------------------|
| **Namespace rename**           | N/A                   | N/A                                | `javax` → `jakarta` (massive)                   |
| **Config format change**       | Django settings       | webpack → vite config              | `application.properties` keys renamed           |
| **Default behavior change**    | `datetime` timezone   | npm peer deps                      | Jackson serialization, Spring Security defaults |
| **Build tool incompatibility** | `distutils` removed   | webpack 4→5 config changes         | Maven plugin Java version requirements          |
| **Auth mechanism change**      | `passlib` algorithms  | JWT library versions               | Spring Security filter chain rewrite            |
| **Database driver change**     | `mysqlclient` version | N/A                                | JDBC driver version, Hibernate dialect          |
| **Serialization format**       | `pickle` protocol     | JSON handling                      | Jackson/Gson/Fastjson version-specific behavior |
| **Async model change**         | `asyncio` API         | Callbacks → Promises → async/await | Virtual threads (Java 21+)                      |
| **Package ecosystem shift**    | pip → poetry/uv       | npm → pnpm, Vue CLI → Vite         | Maven → Gradle, Spring Initializr templates     |
