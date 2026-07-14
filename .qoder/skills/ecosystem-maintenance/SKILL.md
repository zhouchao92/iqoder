---
name: ecosystem-maintenance
description: Maintain and update ecosystem reference information (ecosystems.md) for skills in the project. Scan existing skills, review and refresh ecosystem data, add new ecosystem entries, and record maintenance timestamps in checkpoint files under the output directory. Use when the user asks to update, maintain, or review skill ecosystem information, check ecosystem freshness, or manage ecosystem data lifecycle.
---

# Ecosystem Maintenance

Maintain the ecosystem reference files (`ecosystems.md`) used by skills in this project. Ensure ecosystem data stays current, complete, and consistent across all skills, and track maintenance history via checkpoint files.

## Scope

This skill manages `ecosystems.md` files located in skill directories under `.qoder/skills/*/ecosystems.md`. It also writes checkpoint records to `output/checkpoints/` to track when each ecosystem file was last maintained.

## Workflow

Copy this checklist and track progress:

```
Task Progress:
- [ ] Step 1: Discover all skills with ecosystems.md
- [ ] Step 2: Read and assess current ecosystem data freshness
- [ ] Step 3: Identify gaps, outdated entries, or missing ecosystems
- [ ] Step 4: Update ecosystems.md with new or refreshed content
- [ ] Step 5: Write checkpoint file to record maintenance timestamp
- [ ] Step 6: Generate maintenance summary report
```

### Step 1: Discover Skills with Ecosystem Files

Scan all skill directories for `ecosystems.md` files:

```bash
# List all ecosystems.md files
find .qoder/skills -name "ecosystems.md" -type f
```

Build an inventory:

| Skill        | ecosystems.md Path                  | Last Modified | Size    |
|--------------|-------------------------------------|---------------|---------|
| <skill-name> | .qoder/skills/<skill>/ecosystems.md | <date>        | <lines> |

### Step 2: Assess Ecosystem Data Freshness

For each `ecosystems.md`, evaluate:

1. **Version lifecycle tables**: Are EOL dates current? Check against known EOL schedules.
2. **Migration guides**: Are upgrade paths up to date with latest major versions?
3. **CVE / vulnerability data**: Are high-risk package lists current?
4. **Coverage**: Are all relevant ecosystems/components covered?
5. **Consistency**: Are shared ecosystems (e.g., Python, Java, Node.js) consistent across skills?

**Key checks:**

| Check Item                  | How to Verify                                                                      |
|-----------------------------|------------------------------------------------------------------------------------|
| Python EOL table            | Compare with https://devguide.python.org/versions/                                 |
| Node.js EOL table           | Compare with https://nodejs.org/en/about/previous-releases                         |
| Java EOL table              | Compare with https://www.oracle.com/java/technologies/java-se-support-roadmap.html |
| Spring Boot version mapping | Check https://spring.io/projects/spring-boot#support                               |
| PostgreSQL EOL              | Check https://www.postgresql.org/support/versioning/                               |
| New major releases          | WebSearch for latest major versions of covered ecosystems                          |

### Step 3: Identify Gaps and Updates Needed

Categorize findings:

| Category              | Description                                           | Priority |
|-----------------------|-------------------------------------------------------|----------|
| **Missing ecosystem** | An important ecosystem not covered at all             | High     |
| **Outdated version**  | Version lifecycle table has wrong EOL dates           | High     |
| **Missing version**   | New major/minor version not listed in migration guide | Medium   |
| **Stale data**        | CVE lists or risk assessments need refresh            | Medium   |
| **Inconsistency**     | Same ecosystem has different data across skills       | Low      |

### Step 4: Update Ecosystem Files

For each `ecosystems.md` that needs updates:

1. **Read** the current file content
2. **Research** latest data using WebSearch/WebFetch:
   - `WebSearch "<ecosystem> latest version release <current_year>"`
   - `WebSearch "<ecosystem> end of life schedule"`
   - `WebSearch "<ecosystem> migration guide <old_version> to <new_version>"`
3. **Update** the file with new information:
   - Add new version entries to lifecycle tables
   - Update EOL dates
   - Add new migration guide entries for new major versions
   - Refresh high-risk package lists
   - Fix any inconsistencies
4. **Preserve** existing valid data — only update what's changed

**Update principles:**
- Keep table formats consistent with existing style
- Add new entries at the bottom of version tables (chronological order)
- Mark newly added content with the maintenance date in comments if helpful
- Do not remove historical version data unless it's been superseded

### Step 5: Write Checkpoint File

After updating ecosystem files, write a checkpoint record:

**Checkpoint file path:**
```
output/checkpoints/ecosystem-maintenance-<yyyyMMdd-HHmmss>.md
```

**Checkpoint file template:**

```markdown
# Ecosystem Maintenance Checkpoint

**Maintenance Time / 维护时间:** <yyyy-MM-dd HH:mm:ss>
**Operator / 操作者:** Agent (ecosystem-maintenance skill)

## Updated Files / 更新文件

| File / 文件                         | Action / 操作                 | Changes / 变更摘要 |
|-------------------------------------|-------------------------------|--------------------|
| .qoder/skills/<skill>/ecosystems.md | Updated / Created / No change | <summary>          |

## Details / 变更详情

### <skill-name>/ecosystems.md

- <change 1>
- <change 2>
- ...

## Next Maintenance / 下次维护建议

- <suggested next action or review date>
```

**Checkpoint rules:**
- Always create the `output/checkpoints/` directory if it doesn't exist
- Each maintenance run creates a **new** checkpoint file (never overwrite previous ones)
- Filename uses timestamp to ensure uniqueness and chronological ordering
- If no changes were needed, still write a checkpoint with "No change" actions

### Step 6: Generate Summary

Output a brief summary to the user:

```
Ecosystem Maintenance Complete / 生态圈维护完成
================================================
Skills scanned: <count>
Files updated: <count>
Files unchanged: <count>
Checkpoint saved: output/checkpoints/ecosystem-maintenance-<timestamp>.md

Changes:
- <skill-1>/ecosystems.md: <summary>
- <skill-2>/ecosystems.md: <summary>
```

## Ecosystem Categories Reference

The following ecosystem categories should be covered across skills:

### Package-Based Ecosystems

| Ecosystem           | Package Manager | Key Data to Maintain                                    |
|---------------------|-----------------|---------------------------------------------------------|
| npm / yarn / pnpm   | Node.js         | Package risks, Node.js lifecycle, supply chain patterns |
| Python (pip/poetry) | PyPI            | High-risk packages, Python version lifecycle            |
| Maven / Gradle      | Maven Central   | Java ecosystem risks, Spring version mapping            |
| Go                  | pkg.go.dev      | Go module risks                                         |
| Rust (Cargo)        | crates.io       | Rust ecosystem risks                                    |
| NuGet               | nuget.org       | .NET ecosystem risks                                    |
| Ruby                | rubygems.org    | Ruby ecosystem risks                                    |

### Standalone Components

| Component  | Key Data to Maintain                                   |
|------------|--------------------------------------------------------|
| Tomcat     | Version lifecycle, migration paths, security patterns  |
| Nginx      | Version lifecycle, config migration, security patterns |
| MySQL      | Version lifecycle, migration paths, auth changes       |
| PostgreSQL | Version lifecycle, migration paths, security patterns  |
| Redis      | Version lifecycle, migration paths                     |
| RabbitMQ   | Version lifecycle, Erlang dependency matrix            |
| Node.js    | Version lifecycle (LTS schedule)                       |
| Python     | Version lifecycle (PEP 664)                            |
| Java       | Version lifecycle (LTS releases)                       |

## Important Notes

- **Never delete existing ecosystem data** unless it has been officially superseded. Historical data is valuable for cross-version analysis.
- **Cross-skill consistency**: When the same ecosystem appears in multiple skills' `ecosystems.md`, keep the data consistent. If Python EOL dates are updated in one skill, update them in all skills.
- **Checkpoint immutability**: Once a checkpoint file is written, never modify it. Always create new checkpoint files for new maintenance runs.
- **Freshness target**: Ecosystem data should be reviewed at least quarterly, or whenever a new major version is released for a covered ecosystem.
- **Parallel research**: Use WebSearch with multiple queries in parallel to speed up data collection.
- **Minimal changes**: Only update what's actually changed. Don't rewrite entire files for minor updates.
