---
name: publish-clawhub
description: >
  Publish chenyu-aigc skill to ClawHub (clawhub.ai).
  Trigger when: 正式发布, publish to clawhub, release version, 发布版本,
  发布 x.x.x, publish skill, 上线新版本.
  Use this skill whenever the user mentions publishing, releasing, or pushing a skill version,
  even if they just say a version number like "正式发布 1.0.2".
---

# Publish chenyu-aigc to ClawHub

## Publish Flow

When the user says something like "正式发布 1.0.2":

### 1. Extract version

Parse the semver version (e.g. `1.0.2`) from the user's message.

### 2. Verify login

```bash
clawhub whoami
```

If not logged in, prompt the user to run `! clawhub login`.

### 3. Update version in SKILL.md

Edit `skills/chenyu-aigc/SKILL.md`, update the `version:` field in YAML frontmatter to the new version.

### 4. Confirm with user

Show: skill slug (`chenyu-aigc`), new version, and files in `skills/chenyu-aigc/`. Ask the user for a changelog message or generate one from recent changes.

### 5. Publish

```bash
clawhub publish /Users/dionren/chenyu-aigc/skills/chenyu-aigc \
  --slug chenyu-aigc \
  --version <version> \
  --changelog "<changelog>"
```

### 6. Report result

Confirm success and show the published artifact ID.
