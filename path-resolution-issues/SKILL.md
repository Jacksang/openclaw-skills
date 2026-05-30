# Path Resolution Issues - Tilde (~) Handling

**Issue:** When using `~/` paths in shell commands, the tilde expands to the user's home directory (e.g., `/home/dell/`), which can cause path resolution mismatches.

**Solution:** Always use full absolute paths instead of `~` shortcuts when referencing specific project files.

---

## Problem

The tilde `~` in Linux represents the current user's home directory. When used in commands:
- `~/projects/file.md` expands to `/home/dell/projects/file.md`
- This can cause path resolution issues when the actual path differs slightly

**Example Error:**
```
Could not find file at '~/path/to/file'
Issue: '~/' expands to '/home/dell/' but file path may differ
```

---

## Best Practices

### ✅ CORRECT: Use Full Paths
```bash
# Use absolute paths
cd /home/dell/.openclaw/workspace/projects/smart_learn
cat /home/dell/.openclaw/workspace/projects/smart_learn/plan/D8.2_INTEGRATION_COMPLETION_SUMMARY.md
```

### ❌ AVOID: Tilde shortcuts
```bash
# Can cause resolution issues
cd ~/.openclaw/workspace/projects/smart_learn
cat ~/projects/smart_learn/plan/D8.2_INTEGRATION_COMPLETION_SUMMARY.md
```

---

## When to Use Full Paths

1. **File Operations**: Reading/writing project files
2. **Git Commands**: Repository paths
3. **Development**: Project directory navigation
4. **API URLs**: When constructing URLs programmatically
5. **Tool Configuration**: Setting paths in configuration files

---

## Workspace Path Reference

- **Workspace Root:** `/home/dell/.openclaw/workspace`
- **Smart Learn Project:** `/home/dell/.openclaw/workspace/projects/smart_learn`
- **User Home Directory:** `/home/dell`

---

## Documentation Location

- **This note:** `/home/dell/.openclaw/workspace/skills/path-resolution-issues/SKILL.md`
- **Related:** Any project documentation should reference full paths

---

**Last Updated:** 2026-04-09  
**Status:** Documented and resolved
