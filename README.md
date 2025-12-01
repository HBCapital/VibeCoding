# VibeCoding Template

> A comprehensive project template for AI-assisted development

**VibeCoding** is a standardized project template designed to help AI assistants and development teams quickly bootstrap new software projects with proper documentation, development guidelines, and team collaboration standards.

## 📋 What is VibeCoding?

VibeCoding provides:

- ✅ **Pre-structured documentation framework** - Ready-to-use templates for all essential project docs
- ✅ **Development guidelines** - Best practices for code quality, testing, and collaboration
- ✅ **AI-friendly initialization** - Systematic questions and placeholders for AI to fill
- ✅ **Flexible templates** - Support for various project types (CMS, API, Web App, etc.)
- ✅ **Multi-tech stack support** - Works with Python, Node.js, PHP, Java, and more
- ✅ **Team collaboration ready** - Contributing guidelines, code standards, and security practices

## 🎯 Who is this for?

### AI Assistants 🤖

- Follow structured initialization process
- Ask systematic questions
- Generate consistent project documentation
- Create appropriate directory structures

### Development Teams 👥

- Start new projects faster
- Maintain consistent documentation standards
- Follow established best practices
- Onboard new team members easily

### Solo Developers 👨‍💻

- Professional project structure from day one
- Comprehensive documentation templates
- Industry-standard guidelines
- Future-proof organization

## 🚀 Quick Start

### Step 1: Get the Template

**Option A: Using Terminal**

```bash
mkdir ProjectName
git clone https://github.com/HBCapital/VibeCoding.git
cd ProjectName
```

**Option B: Using AI Agent Editors (Antigravity, Windsurf, Cursor)**

1. Open a new empty project window
2. Ask your agent:
   > "Clone the VibeCoding repo (https://github.com/HBCapital/VibeCoding) here to start a new project."

### Step 2: Initialize with AI

Ask your AI assistant to:

1. Read [`PROJECT_INIT.md`](./PROJECT_INIT.md)
2. Ask you the initialization questions
3. Generate your project documentation

### Step 3: Start Development

Follow the generated documentation and guidelines in `docs/GUIDELINE.md`.

## 📂 What's Included?

### Template Files (`.template.md`)

These files contain placeholders that AI will fill during initialization:

- `README.template.md` - Project overview
- `docs/ARCHITECTURE.template.md` - System architecture
- `docs/TECH_STACK.template.md` - Technology stack details
- `docs/ROADMAP.template.md` - Development roadmap
- `docs/INSTALLATION.template.md` - Setup instructions
- `docs/BUSINESS.template.md` - Business model (optional)
- `docs/RISKS.template.md` - Risk analysis (optional)
- `docs/MIGRATION.template.md` - Migration guide (optional)

### Generic Guidelines (Keep as-is)

These files don't need modification:

- `docs/GUIDELINE.md` - Development principles and best practices
- `LICENSE` - License file

### Conditional Files (Generated based on project type)

- `docs/CONTRIBUTING.md` - Contribution guide (for open-source projects)
- `docs/SECURITY.md` - Security practices (if public security reporting)

### Initialization Files

- `PROJECT_INIT.md` - Instructions for AI to initialize projects
- `.ai/` - AI context directory (created during initialization)

---

## 🤖 AI Initialization Process

### For AI Assistants

When a user requests project initialization:

1. **FIRST**: Ask about project idea and analyze (Step 0)
   - Get project concept and initial features
   - Suggest additional features
   - Wait for user confirmation
2. **Read** [`PROJECT_INIT.md`](./PROJECT_INIT.md) for complete instructions
3. **Ask** user all 26 questions systematically
4. **Process** each `.template.md` file:
   - Replace `{{PLACEHOLDERS}}` with user's answers
   - Save as final `.md` file
   - Delete `.template.md` file
5. **Generate** LICENSE file based on user's choice
6. **Generate** `.ai/TODO.md` with project-specific task breakdown
7. **Conditionally create** CONTRIBUTING.md and SECURITY.md
8. **Create** `.ai/PROJECT_CONTEXT.md` with project details
9. **Update** `.gitignore` to exclude `.ai/` directory
10. **Create** directory structure for chosen tech stack

### Post-Initialization Cleanup

Files to delete after initialization:

- `PROJECT_INIT.md` (instructions no longer needed)
- All `.template.md` files (already processed)

---

## 📖 Documentation

- **[PROJECT_INIT.md](./PROJECT_INIT.md)** - Complete initialization guide for AI
- **[docs/GUIDELINE.md](./docs/GUIDELINE.md)** - Development guidelines and best practices

---

## 🎨 Supported Project Types

VibeCoding templates support various project types:

- 🌐 Web Applications (Full-stack)
- 🔌 API/Backend Services
- 📝 CMS/Content Platforms
- 📱 Mobile Backends
- 🛒 E-commerce Platforms
- ☁️ SaaS Applications
- 🔧 Internal Tools/Dashboards
- 📚 Libraries/Frameworks

## 🛠️ Supported Tech Stacks

### Backend

- Python (FastAPI, Django, Flask)
- Node.js (Express, NestJS, Fastify)
- PHP (Laravel, Symfony)
- Java (Spring Boot)
- Go, Ruby, and more

### Frontend

- Vue.js (Vue 3, Nuxt)
- React (Next.js, CRA)
- Angular
- Svelte (SvelteKit)
- Plain HTML/CSS/JS

### Databases

- PostgreSQL, MySQL/MariaDB
- MongoDB, Redis
- SQLite, and more

## 📄 License

This template is licensed under the **MIT License** - see [LICENSE](./LICENSE)

Projects created using this template can use any license you choose.

---

**Built with ❤️ for better AI-human collaboration**
