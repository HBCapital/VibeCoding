# VibeKit

> Professional AI coding assistant with 47 slash commands

**VibeKit** is a comprehensive development workflow automation system providing 47 professional slash commands organized into 18 groups, covering everything from documentation generation to security audits.

## 🎯 What is VibeKit?

VibeKit provides a structured approach to AI-assisted development through:

- **47 Slash Commands** organized in 18 logical groups
- **Global Rules** for consistent AI behavior
- **Multi-Editor Support** (Cursor, Windsurf, Antigravity)
- **Git-Based Sync** via VSCode extension

## ✨ Features

### 📝 Documentation (17 commands)

Generate any documentation: README, API docs, architecture, installation guides, etc.

### 🔍 Code Quality (4 commands)

Code review, architecture review, comprehensive audits, refactoring

### 🔐 Security (2 commands)

OWASP Top 10 audits, security best practices

### ⚡ Performance (2 commands)

Performance profiling and optimization

### 🔀 Git Workflows (3 commands)

Conventional commits, PR descriptions, workflow guidance

### 🌐 API Design (2 commands)

RESTful/GraphQL API design and documentation

### 🔄 Migrations (2 commands)

Database and code migrations

### 🧹 Cleanup (2 commands)

Dead code removal, dependency cleanup

### 💻 Language-Specific (3 commands)

TypeScript, JavaScript, PHP best practices

### 🛠️ Development Tools (10 commands)

Testing, debugging, database design, UI/UX, DevOps, and more

## 🚀 Quick Start

### 1. Clone Repository with Submodules

```bash
# Clone repository with submodules
git clone --recursive https://github.com/HBCapital/VibeCoding
cd VibeKit

# Or if already cloned, initialize submodules
git submodule update --init --recursive
```

### 2. Install VSCode Extension

```bash
# Navigate to VSCode extension
cd extensions/vscode
npm install
npm run compile
```

Press `F5` in VSCode to launch Extension Development Host.

### 3. Configure Repository

1. Open Command Palette (`Ctrl+Shift+P`)
2. Run: `VibeKit: Configure Git Repository`
3. Enter: `https://github.com/HBCapital/VibeCoding`

### 4. Sync Commands

Click VibeKit icon in status bar or run:

```
VibeKit: Sync Rules & Agents
```

### 5. Start Using

In your AI editor, type:

```
/vibekit
```

This shows all available commands and quick start guide.

## 📦 Command Groups

| Group    | Count | Purpose                     |
| -------- | ----- | --------------------------- |
| doc-     | 17    | Documentation generation    |
| review-  | 4     | Code review & audits        |
| lang-    | 3     | Language-specific practices |
| git-     | 3     | Version control workflows   |
| perf-    | 2     | Performance optimization    |
| sec-     | 2     | Security audits             |
| api-     | 2     | API design                  |
| migrate- | 2     | Migrations                  |
| clean-   | 2     | Code cleanup                |
| Others   | 10    | Testing, debugging, etc.    |

**Total**: 47 commands + 1 help command

See [COMMANDS.md](./docs/COMMANDS.md) for complete reference.

## 📁 Project Structure

```
VibeKit/
├── agents/             # 47 slash commands
├── rules/              # Global rules (vibekit.md)
├── docs/               # Documentation
├── extensions/         # Editor extensions (Git submodules)
│   ├── vscode/        # VSCode extension → VibeKit-vscode repo
│   └── zed/           # Zed extension → VibeKit-zed repo
├── .gitmodules         # Submodule configuration
├── package.json        # Monorepo config
└── README.md          # This file
```

> **Note**: Extensions are Git submodules. Use `git clone --recursive` or `git submodule update --init --recursive`

## 🎨 Supported Editors

### Cursor

- Rules: `.cursorrules`
- Commands: `.cursor/agents/*.md`

### Windsurf

- Rules: `.windsurfrules`
- Commands: `.windsurf/agents/*.md`

### Antigravity (Google)

- Rules: `.antigravity/rules/*.md`
- Commands: `.antigravity/agents/*.md`

## 📖 Documentation

- [Command Reference](./docs/COMMANDS.md) - All 47 commands
- [VSCode Extension](./extensions/vscode/README.md) - Extension guide
- [Development Guidelines](./docs/GUIDELINE.md) - Coding standards
- [Architecture](./docs/ARCHITECTURE.md) - System design
- [Monorepo Guide](./MONOREPO.md) - Project management

## 🛠️ Development

### Install Dependencies

```bash
npm install
```

### Build Extensions

```bash
npm run build
```

### Watch Mode

```bash
npm run watch:vscode
```

### Testing

```bash
npm test
```

See [MONOREPO.md](./MONOREPO.md) for detailed development guide.

## 🤝 Contributing

Contributions welcome! See [CONTRIBUTING.md](./docs/CONTRIBUTING.md)

### Workflow

1. Fork repository
2. Create feature branch: `git checkout -b feature/amazing-feature`
3. Commit changes: `git commit -m 'feat: add amazing feature'`
4. Push to branch: `git push origin feature/amazing-feature`
5. Open Pull Request

## 📄 License

Apache-2.0 - See [LICENSE](./LICENSE)

## 🔗 Links

- **Repository**: [github.com/HBCapital/VibeCoding](https://github.com/HBCapital/VibeCoding)
- **Issues**: [Report bugs or request features](https://github.com/HBCapital/VibeCoding/issues)
- **VSCode Marketplace**: Coming soon

---

**Made with ❤️ by HBCapital**
