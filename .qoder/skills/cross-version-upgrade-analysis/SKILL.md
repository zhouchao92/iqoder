---
name: cross-version-upgrade-analysis
description: Analyze cross-version upgrades for project dependencies or standalone components, identifying breaking changes, compatibility risks, migration steps, and deprecations. Use when the user asks to upgrade a framework/library/runtime across major or minor versions, plan a migration, check upgrade compatibility, or generate an upgrade impact report. Supports package-based ecosystems (npm/pip/maven/go/cargo/nuget/ruby) and standalone components (Tomcat, Nginx, MySQL, Node.js, Java, Python, Spring Boot, etc.).
---

# Cross-Version Upgrade Analysis

Analyze the impact of upgrading a project or component from one version to another. Identify breaking changes, deprecated APIs, compatibility risks, and generate a step-by-step migration plan with a structured report.

## Supported Targets

### Package-Based (via lock files / manifests)

| Ecosystem           | Lock File / Manifest                         | Package Source        |
|---------------------|----------------------------------------------|-----------------------|
| npm / yarn / pnpm   | package-lock.json, yarn.lock, pnpm-lock.yaml | registry.npmjs.org    |
| Python (pip/poetry) | requirements.txt, Pipfile.lock, poetry.lock  | pypi.org              |
| Maven / Gradle      | pom.xml, build.gradle                        | repo.maven.apache.org |
| Go                  | go.sum                                       | pkg.go.dev            |
| Rust (Cargo)        | Cargo.lock                                   | crates.io             |
| NuGet               | packages.lock.json, *.csproj                 | nuget.org             |
| Ruby                | Gemfile.lock                                 | rubygems.org          |

### Standalone Components (specified by name + version)

| Component Type | Examples                          | Primary Info Source                                       |
|----------------|-----------------------------------|-----------------------------------------------------------|
| Web Servers    | Tomcat, Nginx, Apache HTTPD       | Vendor migration guide & release notes                    |
| Databases      | MySQL, PostgreSQL, Redis, MongoDB | Vendor upgrade documentation                              |
| Middleware     | RabbitMQ, Kafka, Elasticsearch    | Vendor upgrade guides                                     |
| Runtimes       | Node.js, Python, Java, .NET       | Vendor release notes & migration guides                   |
| Frameworks     | Spring Boot, Django, Vue, React   | Official migration/upgrade guides                         |

## Workflow

Copy this checklist and track progress:

```
Task Progress:
- [ ] Step 1: Identify upgrade scope (current version → target version)
- [ ] Step 2: Collect version change data (release notes, changelogs, migration guides)
- [ ] Step 3: Analyze breaking changes and deprecated APIs
- [ ] Step 4: Assess dependency compatibility matrix
- [ ] Step 5: Evaluate configuration and behavior changes
- [ ] Step 6: Generate structured upgrade analysis report
- [ ] Step 7: Save report to /output/reports/upgrade/<ecosystem>/<yyyyMMdd>-<component>.md
```

### Step 1: Identify Upgrade Scope

Determine the upgrade type and scope:

**Mode A: Project-wide scan** — Detect current versions from lock files/manifests, then determine target versions.

**Mode B: Specific component** — User specifies component, current version, and target version (e.g., `Spring Boot 2.7 → 3.2`, `Vue 2 → Vue 3`, `Python 3.10 → 3.12`).

**Upgrade classification:**

| Upgrade Type | Example                  | Risk Level | Typical Effort     |
|--------------|--------------------------|------------|--------------------|
| Patch        | 3.2.1 → 3.2.5           | Low        | Minimal            |
| Minor        | 3.2 → 3.3               | Medium     | Moderate           |
| Major        | 2.x → 3.x               | High       | Significant        |
| Cross-major  | Python 2 → 3, Vue 2 → 3 | Critical   | Extensive rewrite  |

### Step 2: Collect Version Change Data

Use **WebSearch** and **WebFetch** to gather authoritative upgrade information. Query in priority order.

#### Priority 0: Official Migration Guides (most authoritative)

| Component          | Migration Guide URL                                                  |
|--------------------|----------------------------------------------------------------------|
| Spring Boot        | https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-3.0-Migration-Guide |
| Spring Framework   | https://github.com/spring-projects/spring-framework/wiki/Spring-Framework-6.0-Release-Notes |
| Vue.js             | https://v3-migration.vuejs.org/                                      |
| React              | https://react.dev/blog (release posts)                               |
| Django             | https://docs.djangoproject.com/en/stable/releases/                   |
| Node.js            | https://nodejs.org/en/blog/release                                   |
| Python             | https://docs.python.org/3/whatsnew/                                  |
| Java               | https://www.oracle.com/java/technologies/javase/jdk-relnotes-index.html |
| Next.js            | https://nextjs.org/docs/app/building-your-application/upgrading       |
| Nuxt               | https://nuxt.com/docs/migration                                      |

**Key searches:**
- `WebSearch "<component> <from_version> to <to_version> migration guide"`
- `WebSearch "<component> <to_version> breaking changes"`
- `WebFetch` on the official migration guide URL

#### Priority 1: Release Notes & Changelogs

- `WebSearch "<component> <version> release notes changelog"`
- `WebFetch` on GitHub releases page: `https://github.com/<org>/<repo>/releases`
- Check each intermediate major/minor version's release notes for cumulative changes

#### Priority 2: Community & Ecosystem Resources

- `WebSearch "<component> upgrade issues <from_version> to <to_version>"`
- Search Stack Overflow: `WebSearch "site:stackoverflow.com <component> upgrade <version>"`
- Check GitHub Issues for known upgrade problems: `WebSearch "site:github.com/<org>/<repo>/issues upgrade <version>"`

#### Priority 3: Ecosystem Compatibility

For framework-based projects, check the entire dependency chain:
- Spring Boot version → compatible Spring Framework, Spring Cloud, Spring Security versions
- Vue/React version → compatible UI library versions (Element UI → Element Plus, etc.)
- Django version → compatible DRF, Celery, database driver versions

### Step 3: Analyze Breaking Changes

For each version jump, categorize changes:

#### 3.1 API Changes

| Change Type | Description | Example |
|-------------|-------------|---------|
| **Removed API** | API completely removed | `javax.*` → `jakarta.*` in Spring Boot 3 |
| **Renamed API** | API renamed or moved | `vue-router` v3 → v4 API changes |
| **Signature Change** | Parameters/return types changed | Python `asyncio` API changes between 3.9→3.10 |
| **Behavior Change** | Same API, different behavior | Jackson default config changes |
| **Deprecated → Removed** | Previously deprecated, now removed | Java 11 removed Java EE modules |

#### 3.2 Configuration Changes

- Default configuration value changes
- Removed/renamed configuration properties
- New required configurations
- Environment variable changes

#### 3.3 Dependency Changes

- Transitive dependency version bumps
- Removed transitive dependencies
- New required dependencies
- Peer dependency changes (npm)

#### 3.4 Runtime / Environment Changes

- Minimum runtime version requirements (e.g., Java 17 minimum for Spring Boot 3)
- Build tool version requirements
- OS/platform support changes
- Container/base image changes

### Step 4: Assess Dependency Compatibility

**CRITICAL: Framework upgrades often require coordinated dependency upgrades.**

Build a compatibility matrix:

```
Component          | Current Ver | Target Ver | Compatible? | Action Required
-------------------|-------------|------------|-------------|----------------
Spring Boot        | 2.7.x       | 3.2.x      | No          | Major upgrade
Spring Framework   | 5.3.x       | 6.1.x      | Required    | Must upgrade
Spring Security    | 5.7.x       | 6.2.x      | Required    | Must upgrade
Java               | 8           | 17         | Required    | Must upgrade
Jakarta EE         | javax.*     | jakarta.*  | Required    | Package rename
```

**For each ecosystem, check:**

```bash
# npm — check peer dependency conflicts
npm ls --all  # after updating package.json

# pip — check dependency conflicts
pip check

# maven — check dependency tree
mvn dependency:tree

# go — check module compatibility
go mod tidy; go mod verify

# cargo — check compatibility
cargo tree
```

### Step 5: Evaluate Configuration & Behavior Changes

Check for silent behavior changes that could cause runtime issues:

- **Default value changes**: New defaults that differ from old behavior
- **Feature flags**: New features enabled/disabled by default
- **Security defaults**: Stricter security defaults (CORS, CSRF, cookie policies)
- **Serialization changes**: JSON/XML serialization behavior differences
- **Database schema changes**: Required migrations, new constraints
- **API response format changes**: Changed response structures

### Step 6: Generate Report

Output a Markdown report using this template:

```markdown
# Cross-Version Upgrade Analysis Report / 跨版本升级分析报告

**Project / 项目:** <project-name>
**Date / 分析时间:** <yyyy-MM-dd HH:mm:ss>
**Component / 升级组件:** <name>
**Current Version / 当前版本:** <from-version>
**Target Version / 目标版本:** <to-version>
**Upgrade Type / 升级类型:** <Patch/Minor/Major/Cross-major>
**Overall Risk / 整体风险:** <Low/Medium/High/Critical>

---

## Executive Summary / 概要总结

<1-2 paragraph overview: upgrade scope, key breaking changes, estimated effort, critical blockers>

## Upgrade Path / 升级路径

<If crossing multiple major versions, outline the recommended step-by-step upgrade path>

| Step / 步骤 | Target / 目标版本 | Key Changes / 关键变更 | Risk / 风险 |
|-------------|-------------------|------------------------|-------------|
| 1           | <intermediate>    | <summary>              | <level>     |
| 2           | <final>           | <summary>              | <level>     |

## Breaking Changes / 破坏性变更

### 🔴 Critical Breaking Changes / 严重破坏性变更

#### <Change Title>

| Field / 字段 | Detail / 详情 |
|--------------|---------------|
| **Category / 分类** | <API Removed / Config Changed / Behavior Changed / etc.> |
| **Impact / 影响范围** | <which code/config is affected> |
| **Migration / 迁移方式** | <how to fix> |
| **Reference / 参考链接** | <official doc link> |

**Before / 升级前:**
```<language>
<old code/config example>
```

**After / 升级后:**
```<language>
<new code/config example>
```

---

(Repeat for each breaking change)

### 🟡 Moderate Breaking Changes / 中等破坏性变更

| Change / 变更 | Category / 分类 | Impact / 影响 | Migration / 迁移方式 |
|---------------|-----------------|---------------|----------------------|
| <title>       | <type>          | <scope>       | <how to fix>         |

### 🟢 Minor Changes / 轻微变更

| Change / 变更 | Category / 分类 | Notes / 备注 |
|---------------|-----------------|--------------|
| <title>       | <type>          | <notes>      |

## Dependency Compatibility Matrix / 依赖兼容性矩阵

| Component / 组件 | Current / 当前 | Required / 要求 | Action / 操作 |
|------------------|----------------|-----------------|---------------|
| <name>           | <version>      | <version>       | <upgrade/keep/rename> |

## Configuration Changes / 配置变更

| Property / 配置项 | Old Value / 旧值 | New Value / 新值 | Required / 必须 | Notes / 备注 |
|-------------------|------------------|------------------|-----------------|--------------|
| <property>        | <old>            | <new>            | Yes/No          | <notes>      |

## Deprecated Features / 已废弃特性

| Feature / 特性 | Deprecated Since / 废弃版本 | Removed In / 移除版本 | Alternative / 替代方案 |
|----------------|----------------------------|-----------------------|------------------------|
| <feature>      | <version>                  | <version/N/A>         | <alternative>          |

## Migration Checklist / 迁移检查清单

- [ ] <Step 1: e.g., Upgrade runtime to Java 17>
- [ ] <Step 2: e.g., Rename javax.* to jakarta.*>
- [ ] <Step 3: e.g., Update configuration properties>
- [ ] <Step 4: e.g., Update deprecated API calls>
- [ ] <Step 5: e.g., Run test suite and fix failures>
- [ ] <Step 6: e.g., Update CI/CD pipeline configuration>
- [ ] <Step 7: e.g., Update Docker base image>

## Risk Assessment / 风险评估

| Risk / 风险 | Likelihood / 可能性 | Impact / 影响 | Mitigation / 缓解措施 |
|-------------|---------------------|---------------|-----------------------|
| <risk>      | High/Medium/Low     | High/Medium/Low | <mitigation>        |

## Data Sources Consulted / 查询数据源

- [x] Official Migration Guide
- [x] Release Notes / Changelogs
- [x] GitHub Issues & Discussions
- [x] Community Forums / Stack Overflow
- [x] Dependency Compatibility Check
```

### Step 7: Save Report to File

Save the report to:

```
<project-root>/output/reports/upgrade/<ecosystem>/<yyyyMMdd>-<component>-<from>-to-<to>.md
```

Example paths:
- `output/reports/upgrade/spring/20260714-spring-boot-2.7-to-3.2.md`
- `output/reports/upgrade/npm/20260714-vue-2-to-3.md`
- `output/reports/upgrade/python/20260714-django-3.2-to-4.2.md`
- `output/reports/upgrade/standalone/20260714-tomcat-9-to-10.md`

Create the directory if it doesn't exist:
```bash
mkdir -p <project-root>/output/reports/upgrade/<ecosystem>
```

## Ecosystem-Specific Guidance

When analyzing specific technology stacks, consult [ecosystems.md](ecosystems.md) for detailed guidance on:

- **Python**: Django, Flask, FastAPI upgrade patterns, Python version migration (2→3, 3.x→3.y)
- **Vue / Frontend**: Vue 2→3 migration, React major version upgrades, npm ecosystem migration, Node.js version upgrades
- **Java**: Java version migration (8→11→17→21), Maven/Gradle plugin compatibility
- **Spring**: Spring Boot major upgrades (2.x→3.x), Spring Framework 5→6, Spring Cloud version alignment, Jakarta EE migration
- **Standalone Components**: Tomcat, Nginx, MySQL, Redis, RabbitMQ upgrade considerations
- **Cross-cutting patterns**: Common upgrade pitfalls mapped across Python, JS, and Java/Spring stacks

## Important Notes

- **Always check intermediate versions**: When crossing major versions, review each intermediate major version's migration guide — some changes cascade.
- **Test in a staging environment first**: Never upgrade directly in production. Create a branch, upgrade, and run the full test suite.
- **Backup before upgrading**: Database migrations may be irreversible. Always backup data before upgrading database-related components.
- **Check downstream dependencies**: Upgrading one component may require upgrading others. Always verify the full dependency tree.
- **Review removed transitive dependencies**: Some transitive deps may be removed in the new version — verify no code directly imports them.
- **Plan for parallel testing**: For critical upgrades, consider running old and new versions in parallel (blue-green deployment) during the transition.
- **Check license changes**: Major version upgrades sometimes change the license (e.g., from Apache 2.0 to SSPL for MongoDB 4.0→5.0+).
- **Freshness**: Upgrade guides and compatibility data change as new patches are released. Always note the analysis date in the report.
- **Parallel queries**: Use WebSearch with multiple queries in parallel to speed up data collection from different sources.
