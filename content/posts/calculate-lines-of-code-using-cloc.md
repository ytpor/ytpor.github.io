---
title: 'Counting Lines of Code with cloc'
date: 2026-06-30T10:00:00+08:00
draft: false
url: /2026/06/calculate-lines-of-code-using-cloc
tags: ["cloc", "loc", "tools"]
---

`cloc` counts source lines of code across a project, separating code lines from comment lines and blank lines. For most reporting purposes - cost estimation, change tracking, and audits - only the code line count matters.

Install it with Homebrew on macOS, APT on Debian/Ubuntu, or Chocolatey on Windows:

```bash
brew install cloc          # macOS
sudo apt install cloc      # Debian / Ubuntu
choco install cloc         # Windows
```

Run it from the project root and exclude dependency and generated directories. Without exclusions, vendored libraries and build artifacts inflate the count. The directories to exclude depend on the ecosystem.

For Node.js and TypeScript projects:

```bash
cloc . --exclude-dir=node_modules,dist,.next,.nuxt,.svelte-kit,build
```

For PHP projects using Composer or Laravel:

```bash
cloc . --exclude-dir=vendor,storage,.phpunit.cache
```

For Python projects:

```bash
cloc . --exclude-dir=.venv,__pycache__,.pytest_cache,dist,build
```

For JVM languages (Java, Kotlin), Go, or Rust:

```bash
cloc . --exclude-dir=target,build,bin,dist,vendor
```

The output breaks down line counts by language:

```
Language          files   blank  comment    code
------------------------------------------------
Python               42     620      310    4200
JavaScript           18     190       85    1800
------------------------------------------------
SUM:                 60     810      395    6000
```

Use the `code` column as the LOC figure. Export results to JSON or CSV for reporting:

```bash
cloc . --exclude-dir=... --json > loc-report.json
cloc . --csv --out=loc-report.csv
```
