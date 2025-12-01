# FlowOne Architecture

> Detailed technical architecture for FlowOne CMS

## 📐 Architecture Overview

FlowOne uses a **modern, modular, and API-first** architecture with clear separation between core, plugins, themes and frontend.

```
┌─────────────────────────────────────────────────────┐
│                  Frontend Layer                     │
│  ┌──────────────┐         ┌──────────────┐         │
│  │  Admin SPA   │         │ Public Site  │         │
│  │   (Vue 3)    │         │ (SSR/Twig)   │         │
│  └──────┬───────┘         └──────┬───────┘         │
│         │                        │                  │
│         └────────┬───────────────┘                  │
└──────────────────┼──────────────────────────────────┘
                   │ REST / GraphQL
┌──────────────────┼──────────────────────────────────┐
│                  ▼                                   │
│            API Gateway (PSR-15 Middleware)          │
│                                                      │
│  ┌───────────┐  ┌──────────┐  ┌─────────────┐     │
│  │   Core    │  │ Plugins  │  │   Themes    │     │
│  │  Engine   │  │  Layer   │  │   Engine    │     │
│  └─────┬─────┘  └────┬─────┘  └──────┬──────┘     │
│        │             │                │             │
│        └─────────────┴────────────────┘             │
│                      │                               │
│                      ▼                               │
│         ┌────────────────────────┐                  │
│         │  DB Abstraction Layer  │                  │
│         │   (PDO + Query Builder)│                  │
│         └────────────┬───────────┘                  │
└──────────────────────┼──────────────────────────────┘
                       │
         ┌─────────────┴─────────────┐
         ▼                           ▼
    ┌─────────┐              ┌───────────┐
    │ SQLite  │              │   MySQL   │
    │         │              │  MariaDB  │
    └─────────┘              └───────────┘
```

## 🗄️ Database Design

### Core Tables

#### 1. Users Table

```sql
CREATE TABLE users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    display_name VARCHAR(255),
    roles JSON DEFAULT '["viewer"]', -- ['admin', 'editor', 'author', 'viewer']
    status ENUM('active', 'inactive', 'banned') DEFAULT 'active',
    meta JSON, -- Flexible metadata storage
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    updated_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    last_login DATETIME
);

CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_status ON users(status);
```

#### 2. Posts Table (Polymorphic Content)

```sql
CREATE TABLE posts (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    type VARCHAR(50) NOT NULL, -- 'page', 'post', 'product', etc.
    title VARCHAR(255) NOT NULL,
    slug VARCHAR(255) UNIQUE NOT NULL,
    content TEXT,
    excerpt TEXT,
    status ENUM('draft', 'published', 'scheduled', 'archived') DEFAULT 'draft',
    author_id INTEGER NOT NULL,
    parent_id INTEGER DEFAULT NULL, -- For hierarchical content
    meta JSON, -- SEO, custom fields, etc.
    published_at DATETIME,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    updated_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (author_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (parent_id) REFERENCES posts(id) ON DELETE SET NULL
);

CREATE INDEX idx_posts_type ON posts(type);
CREATE INDEX idx_posts_slug ON posts(slug);
CREATE INDEX idx_posts_status ON posts(status);
CREATE INDEX idx_posts_author ON posts(author_id);
CREATE INDEX idx_posts_published ON posts(published_at);
```

#### 3. Terms (Taxonomy System)

```sql
CREATE TABLE terms (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    taxonomy VARCHAR(50) NOT NULL, -- 'category', 'tag', 'custom_taxonomy'
    name VARCHAR(255) NOT NULL,
    slug VARCHAR(255) NOT NULL,
    description TEXT,
    parent_id INTEGER DEFAULT NULL,
    meta JSON,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(taxonomy, slug),
    FOREIGN KEY (parent_id) REFERENCES terms(id) ON DELETE SET NULL
);

CREATE INDEX idx_terms_taxonomy ON terms(taxonomy);
CREATE INDEX idx_terms_slug ON terms(slug);
```

#### 4. Post-Term Relationships

```sql
CREATE TABLE post_term (
    post_id INTEGER NOT NULL,
    term_id INTEGER NOT NULL,
    PRIMARY KEY (post_id, term_id),
    FOREIGN KEY (post_id) REFERENCES posts(id) ON DELETE CASCADE,
    FOREIGN KEY (term_id) REFERENCES terms(id) ON DELETE CASCADE
);

CREATE INDEX idx_post_term_post ON post_term(post_id);
CREATE INDEX idx_post_term_term ON post_term(term_id);
```

#### 5. Media Library

```sql
CREATE TABLE media (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    filename VARCHAR(255) NOT NULL,
    path VARCHAR(500) NOT NULL,
    url VARCHAR(500) NOT NULL,
    mime_type VARCHAR(100) NOT NULL,
    size INTEGER NOT NULL, -- bytes
    width INTEGER,
    height INTEGER,
    sizes JSON, -- Responsive image sizes: {"thumbnail": "/path", "medium": "/path"}
    alt_text VARCHAR(255),
    uploaded_by INTEGER,
    meta JSON,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (uploaded_by) REFERENCES users(id) ON DELETE SET NULL
);

CREATE INDEX idx_media_mime ON media(mime_type);
CREATE INDEX idx_media_uploaded_by ON media(uploaded_by);
```

#### 6. Plugins Registry

```sql
CREATE TABLE plugins (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name VARCHAR(100) UNIQUE NOT NULL,
    version VARCHAR(20) NOT NULL,
    enabled BOOLEAN DEFAULT 0,
    installed_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    updated_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    meta JSON -- Config, settings, etc.
);

CREATE INDEX idx_plugins_enabled ON plugins(enabled);
```

### SQLite Optimization Strategies

1. **JSON Extension**: Leverage SQLite's JSON1 extension for flexible metadata
2. **Smart Indexing**: Index only frequently queried columns
3. **Avoid Complex JOINs**: Denormalize when appropriate for read-heavy operations
4. **Use INTEGER PRIMARY KEY**: Auto-increments and aliases ROWID for performance
5. **WAL Mode**: Enable Write-Ahead Logging for better concurrency

## 🔌 Plugin & Theme System

### Plugin Architecture

#### Hook/Event System

```php
// Action Hook Example
Hook::addAction('post.created', function($post) {
    // Send notification, update cache, etc.
}, priority: 10);

// Filter Hook Example
Hook::addFilter('post.content', function($content) {
    return $content . "\n<!-- Powered by FlowOne -->";
}, priority: 5);

// Async Hook for Background Processing
Hook::addAsyncAction('media.uploaded', function($media) {
    // Generate thumbnails in background
}, priority: 10);
```

#### Plugin Manifest (`plugin.json`)

```json
{
  "permissions": ["posts.read", "posts.write", "settings.seo"],
  "autoload": {
    "psr-4": {
      "FlowOne\\Plugins\\SEO\\": "src/"
    }
  },
  "hooks": {
    "post.save": "SEO\\PostMetaHandler::onSave"
  }
}
```

#### Plugin Sandboxing

1. **Namespace Isolation**: Each plugin runs in its own namespace
2. **Permission Model**: RBAC-based permissions check
3. **Resource Limits**: Memory and execution time limits
4. **Safe Mode**: Auto-disable plugin if causing critical errors
5. **Rollback Support**: Snapshot before plugin activation

#### Plugin Signing & Distribution

```bash
# Developer signs plugin
flowone plugin:sign seo-optimizer --key=private.key

# User verifies signature on install
flowone plugin:verify seo-optimizer.zip --public-key=publisher.pub

# Install from marketplace
flowone plugin:install seo-optimizer
```

### Theme System

#### Theme Structure

```
themes/
└── my-theme/
    ├── theme.json          # Theme manifest
    ├── functions.php       # Theme logic
    ├── templates/
    │   ├── layouts/
    │   │   └── default.twig
    │   ├── pages/
    │   │   ├── home.twig
    │   │   └── single.twig
    │   └── partials/
    │       ├── header.twig
    │       └── footer.twig
    ├── assets/
    │   ├── css/
    │   ├── js/
    │   └── images/
    └── lang/               # i18n translations
```

#### Theme Manifest (`theme.json`)

```json
{
  "name": "flowone-default",
  "version": "1.0.0",
  "title": "FlowOne Default Theme",
  "screenshot": "screenshot.png",
  "min_core_version": "1.0.0",
  "supports": ["custom-logo", "responsive-images", "dark-mode", "gutenberg"],
  "template_engine": "twig",
  "asset_pipeline": "vite"
}
```

#### Child Theme Support

```php
// child-theme/functions.php
Theme::extend('flowone-default');

// Override parent templates
Theme::overrideTemplate('single.twig', __DIR__ . '/templates/single.twig');

// Enqueue additional assets
Theme::enqueueStyle('child-style', '/assets/css/child.css');
```

## ⚡ Performance & Scalability

### Cache Layers

```
┌─────────────────────────────────────────┐
│        1. OPcache (PHP Bytecode)        │
├─────────────────────────────────────────┤
│     2. Full-Page Cache (File/Redis)     │
├─────────────────────────────────────────┤
│     3. Object Cache (PSR-6: Redis)      │
├─────────────────────────────────────────┤
│    4. Database Query Cache (Built-in)   │
└─────────────────────────────────────────┘
```

### Horizontal Scaling Architecture

```
                  ┌──────────────┐
                  │ Load Balancer│
                  └──────┬───────┘
                         │
        ┌────────────────┼────────────────┐
        │                │                │
   ┌────▼────┐      ┌────▼────┐     ┌────▼────┐
   │ App     │      │ App     │     │ App     │
   │ Server 1│      │ Server 2│     │ Server N│
   └────┬────┘      └────┬────┘     └────┬────┘
        │                │                │
        └────────────────┼────────────────┘
                         │
        ┌────────────────┼────────────────┐
        │                │                │
   ┌────▼────┐      ┌────▼────┐     ┌────▼────┐
   │  Redis  │      │   S3    │     │  MySQL  │
   │  Cache  │      │ Storage │     │ Primary │
   └─────────┘      └─────────┘     └────┬────┘
                                          │
                                     ┌────▼────┐
                                     │  MySQL  │
                                     │ Replica │
                                     └─────────┘
```

### Image Optimization

1. **On-Upload Processing**:
   - Generate responsive sizes (thumbnail, medium, large)
   - WebP conversion (fallback to original format)
   - Lazy loading by default
2. **CDN Integration**:
   - Automatic asset URL rewriting
   - Cache-busting via versioned URLs
   - Precompressed assets (gzip, brotli)

### Database Optimization

#### For SQLite

- **WAL Mode**: Better concurrency
- **Pragma optimizations**: `journal_mode=WAL`, `synchronous=NORMAL`
- **Connection pooling**: Reuse connections

#### For MySQL/MariaDB

- **Read replicas**: Distribute read load
- **Connection pooling**: pgBouncer or ProxySQL
- **Partitioning**: For large tables (e.g., logs)
- **Query caching**: Built-in or Redis-based

## 🔐 Security Architecture

### Authentication & Authorization

```php
// Multi-factor authentication support
Auth::login($email, $password, $mfaToken);

// JWT-based API authentication
$token = Auth::issueToken($user, expires: '7 days');

// OAuth2 providers
Auth::withProvider('google')->redirect();

// Role-based authorization
if (Auth::user()->can('posts.delete')) {
    Post::delete($id);
}
```

### Plugin Capability Model

```php
// Define plugin capabilities
Plugin::register('my-plugin', [
    'capabilities' => [
        'manage_seo' => 'Manage SEO settings',
        'view_analytics' => 'View analytics data'
    ]
]);

// Check capability before action
if (Plugin::can('my-plugin', 'manage_seo')) {
    // Execute privileged action
}
```

### Input Sanitization & Validation

```php
// Request validation (Laravel-style)
$validated = Request::validate([
    'title' => 'required|string|max:255',
    'content' => 'required|string',
    'status' => 'in:draft,published'
]);

// Output escaping (Twig auto-escapes)
{{ post.title }} <!-- Auto-escaped -->
{{ post.content|raw }} <!-- Explicitly unescaped -->
```

## 🛠️ Developer Tools

### CLI Commands

```bash
# Project management
flowone new my-project              # Create new project
flowone serve                       # Dev server
flowone build                       # Production build

# Database
flowone migrate                     # Run migrations
flowone migrate:rollback            # Rollback last migration
flowone db:seed                     # Seed database

# Plugin development
flowone plugin:create my-plugin     # Scaffold new plugin
flowone plugin:install ./my-plugin  # Install local plugin
flowone plugin:publish              # Publish to marketplace

# Theme development
flowone theme:create my-theme       # Scaffold new theme
flowone theme:activate my-theme     # Activate theme
flowone theme:build                 # Build assets (Vite)

# Maintenance
flowone cache:clear                 # Clear all caches
flowone optimize                    # Run optimizations
flowone update                      # Update core
```

### Testing Framework

```php
// Unit testing (PHPUnit)
class PostServiceTest extends TestCase {
    public function test_create_post() {
        $post = PostService::create([
            'title' => 'Test Post',
            'content' => 'Content'
        ]);

        $this->assertDatabaseHas('posts', [
            'title' => 'Test Post'
        ]);
    }
}

// Integration testing
class APITest extends IntegrationTestCase {
    public function test_api_create_post() {
        $response = $this->post('/api/posts', [
            'title' => 'API Post',
            'content' => 'Content'
        ]);

        $response->assertStatus(201);
    }
}
```

## 📦 Packaging & Distribution

### Composer-Native Installation

```json
{
  "require": {
    "flowone/core": "^1.0",
    "flowone/plugin-seo": "^1.0",
    "flowone/theme-default": "^1.0"
  }
}
```

### Marketplace Integration

```bash
# Search marketplace
flowone search seo

# Install from marketplace
flowone install flowone/plugin-seo

# Update all packages
flowone update
```

---

**Next Steps**: See [INSTALLATION.md](./INSTALLATION.md) for setup instructions.
