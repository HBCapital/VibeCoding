# FlowOne Technology Stack

> Detailed technologies and libraries used in FlowOne CMS

## 🎯 Selection Principles

FlowOne is built on **modern PHP** with PSR standards and best practices. Each technology is chosen based on:

- ✅ **Performance**: High speed and efficiency
- ✅ **Maintainability**: Easy to maintain and extend
- ✅ **Community Support**: Large community, comprehensive documentation
- ✅ **PSR Compliance**: Follow PHP standards
- ✅ **Security**: Security prioritized first

## 🏗️ Core Technologies

### Language & Runtime

#### PHP 8.4+

- **JIT Compiler**: 20-30% performance boost for computational tasks
- **Named Arguments**: More readable and maintainable code
- **Union Types & Attributes**: Better type safety
- **Match Expressions**: Cleaner conditional logic
- **Null Safe Operator**: Avoid null pointer errors

```php
// Modern PHP 8.4 example
class PostService {
    public function create(
        string $title,
        string $content,
        PostStatus $status = PostStatus::DRAFT
    ): Post {
        return match($status) {
            PostStatus::PUBLISHED => $this->publishPost($title, $content),
            PostStatus::DRAFT => $this->saveDraft($title, $content),
            default => throw new InvalidStatusException()
        };
    }
}
```

#### Composer 2.0+

- **Dependency Management**: Manage packages and dependencies
- **Autoloading**: PSR-4 autoloading
- **Scripts**: Task automation and custom commands

---

## 📦 Core Dependencies

### 1. Database Layer

#### PDO (Built-in)

- **Purpose**: Database abstraction
- **Why**: Native PHP extension, zero overhead, secure prepared statements

#### Custom Query Builder

- **Purpose**: Fluent query interface
- **Inspired by**: Laravel Eloquent, but lightweight
- **Features**:
  - Method chaining
  - Query scopes
  - Relationships (has-many, belongs-to, many-to-many)
  - Soft deletes

```php
// Query Builder Example
Post::query()
    ->where('status', 'published')
    ->whereDate('published_at', '>', now()->subDays(7))
    ->with('author', 'tags')
    ->orderBy('published_at', 'desc')
    ->paginate(10);
```

#### Database Adapters

- **SQLite**: Development and small deployments
- **MySQL 8.0+**: Production standard
- **MariaDB 10.6+**: MySQL alternative with better performance

---

### 2. HTTP Layer (PSR-7 & PSR-15)

#### guzzlehttp/psr7

- **Purpose**: PSR-7 HTTP message implementation
- **Version**: ^2.0
- **Features**: Request/Response objects, Stream handling

#### middlewares/utils

- **Purpose**: PSR-15 middleware utilities
- **Version**: ^3.0
- **Features**: Middleware dispatcher, common middlewares

```php
// Middleware stack
$app->pipe(new CorsMiddleware());
$app->pipe(new AuthenticationMiddleware());
$app->pipe(new CsrfMiddleware());
$app->pipe(new RateLimitMiddleware());
```

---

### 3. Routing

#### nikic/fast-route

- **Purpose**: Fast HTTP router
- **Version**: ^1.3
- **Why**: Extremely fast (compiled regex), simple API, widely used

```php
// Routing example
$router->get('/posts/{id:\d+}', PostController::class . '@show');
$router->post('/posts', PostController::class . '@store');
$router->put('/posts/{id}', PostController::class . '@update');
$router->delete('/posts/{id}', PostController::class . '@destroy');
```

**Alternative considered**: Symfony Routing (more features but heavier)

---

### 4. Dependency Injection Container (PSR-11)

#### PHP-DI

- **Purpose**: Powerful DI container
- **Version**: ^7.0
- **Features**:
  - Autowiring
  - Annotations/Attributes
  - Lazy loading
  - Compilation for production

```php
// Container configuration
$container = ContainerBuilder::buildDevContainer();

// Auto-wiring
class PostController {
    public function __construct(
        private PostRepository $posts,
        private CacheInterface $cache
    ) {}
}
```

---

### 5. Templating Engine

#### Twig

- **Purpose**: Secure template engine
- **Version**: ^3.0
- **Features**:
  - Auto-escaping (XSS prevention)
  - Template inheritance
  - Filters & functions
  - Sandboxed mode for themes

```twig
{# templates/post.twig #}
{% extends "layouts/default.twig" %}

{% block content %}
    <article>
        <h1>{{ post.title }}</h1>
        <div>{{ post.content|raw }}</div>
    </article>
{% endblock %}
```

**Alternative**: Blade (Laravel) - considered for familiarity

---

### 6. Caching (PSR-6 & PSR-16)

#### symfony/cache

- **Purpose**: Caching framework
- **Version**: ^6.0
- **Features**:
  - PSR-6 & PSR-16 implementations
  - Multiple backends (File, Redis, APCu, Memcached)
  - Tag-based invalidation

```php
// PSR-6 example
$cache = new FilesystemAdapter();
$item = $cache->getItem('post_' . $id);

if (!$item->isHit()) {
    $post = Post::find($id);
    $item->set($post);
    $item->expiresAfter(3600);
    $cache->save($item);
}

return $item->get();
```

---

### 7. Console CLI

#### symfony/console

- **Purpose**: CLI framework
- **Version**: ^6.0
- **Features**:
  - Command creation
  - Input/Output formatting
  - Progress bars
  - Interactive prompts

```php
// Custom command
class MigrateCommand extends Command {
    protected function configure() {
        $this->setName('migrate')
             ->setDescription('Run database migrations');
    }

    protected function execute(InputInterface $input, OutputInterface $output) {
        $output->writeln('<info>Running migrations...</info>');
        // Migration logic
    }
}
```

---

## 🔐 Security Libraries

### 1. Authentication & Authorization

#### lcobucci/jwt

- **Purpose**: JWT token handling
- **Version**: ^5.0
- **Features**: Token generation, validation, parsing

#### league/oauth2-server

- **Purpose**: OAuth2 server implementation
- **Version**: ^8.0
- **Features**: Authorization server, resource server

```php
// JWT authentication
$token = JWTBuilder::create()
    ->issuedBy('flowone')
    ->withClaim('uid', $user->id)
    ->expiresAt(now()->addDays(7))
    ->getToken();
```

### 2. Password Hashing

#### Built-in: password_hash()

```php
// Using Argon2id (recommended)
$hash = password_hash($password, PASSWORD_ARGON2ID, [
    'memory_cost' => 65536,
    'time_cost' => 4,
    'threads' => 3
]);
```

### 3. CSRF Protection

#### Custom Implementation

- Token generation per session
- Double-submit cookie pattern
- SameSite cookie attribute

---

## 📊 API Layer

### REST API

#### Custom Implementation

- RESTful resource controllers
- JSON:API specification compliance (optional)
- Pagination, filtering, sorting
- Rate limiting

```php
// RESTful endpoints
GET    /api/posts              # List posts
POST   /api/posts              # Create post
GET    /api/posts/{id}         # Get post
PUT    /api/posts/{id}         # Update post
DELETE /api/posts/{id}         # Delete post
```

### GraphQL (Optional)

#### webonyx/graphql-php

- **Purpose**: GraphQL server implementation
- **Version**: ^15.0
- **Features**: Schema definition, resolvers, batching

```graphql
# GraphQL schema
type Post {
  id: ID!
  title: String!
  content: String
  author: User!
  tags: [Tag!]!
}

type Query {
  posts(status: String, limit: Int): [Post!]!
  post(id: ID!): Post
}
```

---

## 🎨 Frontend Stack

### Admin UI (SPA)

#### Vue 3

- **Purpose**: Admin dashboard
- **Features**: Composition API, reactive state, component-based

#### Vite

- **Purpose**: Build tool
- **Features**: Fast HMR, optimized builds, plugin ecosystem

#### Pinia

- **Purpose**: State management
- **Features**: Lightweight, TypeScript support, devtools

#### Vue Router

- **Purpose**: Client-side routing
- **Features**: Dynamic routes, lazy loading

```vue
<!-- Admin component example -->
<script setup>
import { ref, onMounted } from "vue";
import { usePosts } from "@/stores/posts";

const posts = usePosts();

onMounted(() => {
  posts.fetchAll();
});
</script>

<template>
  <div class="posts-list">
    <PostCard v-for="post in posts.items" :key="post.id" :post="post" />
  </div>
</template>
```

### Public Frontend (SSR)

**Option 1**: Server-side rendering with Twig (SEO-optimized)

**Option 2**: Vue SSR (for dynamic apps)

- Nuxt 3 integration (optional)
- Universal rendering
- Better SEO than SPA

---

## 🛠️ Development Tools

### Testing

#### PHPUnit

- **Purpose**: Unit & integration testing
- **Version**: ^10.0
- **Features**: Assertions, mocking, code coverage

```php
// Test example
class PostServiceTest extends TestCase {
    public function test_creates_post_successfully() {
        $service = new PostService();
        $post = $service->create('Test', 'Content');

        $this->assertInstanceOf(Post::class, $post);
        $this->assertEquals('Test', $post->title);
    }
}
```

#### Pest (Alternative)

- Modern testing framework
- Cleaner syntax
- Built on PHPUnit

### Code Quality

#### PHP-CS-Fixer

- **Purpose**: Code style fixer
- **Standard**: PSR-12

#### PHPStan

- **Purpose**: Static analysis
- **Level**: 8 (strictest)

#### Psalm

- **Purpose**: Type checker
- **Features**: Find bugs, enforce types

```bash
# Code quality commands
composer run cs-fix      # Fix code style
composer run phpstan     # Static analysis
composer run test        # Run tests
```

---

## 📁 Asset Management

### Image Processing

#### intervention/image

- **Purpose**: Image manipulation
- **Version**: ^3.0
- **Features**:
  - Resize, crop, watermark
  - Format conversion (WebP)
  - Thumbnail generation

```php
// Generate responsive images
$image = Image::make($upload)
    ->resize(1200, null, function ($constraint) {
        $constraint->aspectRatio();
    })
    ->save('uploads/images/large.jpg');
```

### File Storage

#### league/flysystem

- **Purpose**: Filesystem abstraction
- **Version**: ^3.0
- **Adapters**: Local, S3, FTP, etc.

```php
// Abstract storage
$filesystem->write('posts/image.jpg', $content);

// Switch to S3 in production (no code changes)
$adapter = new AwsS3V3Adapter($client, $bucket);
```

---

## 🚀 Queue & Background Jobs

#### symfony/messenger (Optional)

- **Purpose**: Message bus
- **Version**: ^6.0
- **Transports**: Redis, RabbitMQ, Doctrine

```php
// Dispatch background job
$bus->dispatch(new GenerateThumbnailMessage($mediaId));
```

---

## 📈 Monitoring & Logging

#### monolog/monolog

- **Purpose**: Logging library
- **Version**: ^3.0
- **Handlers**: File, syslog, Slack, email

```php
// Logging example
$logger->info('Post created', ['id' => $post->id]);
$logger->warning('Cache miss', ['key' => $cacheKey]);
$logger->error('Database error', ['exception' => $e]);
```

---

## 🌐 Internationalization (i18n)

#### symfony/translation

- **Purpose**: Multi-language support
- **Version**: ^6.0
- **Formats**: PHP, YAML, JSON

```php
// Translation example
echo trans('posts.created_successfully');
echo trans('welcome.message', ['name' => $user->name]);
```

---

## 📋 PSR Standards Compliance

FlowOne adheres to the following PSR standards:

| PSR        | Description           | Implementation    |
| ---------- | --------------------- | ----------------- |
| **PSR-1**  | Basic Coding Standard | Code style        |
| **PSR-4**  | Autoloading Standard  | Composer autoload |
| **PSR-6**  | Caching Interface     | symfony/cache     |
| **PSR-7**  | HTTP Messages         | guzzlehttp/psr7   |
| **PSR-11** | Container Interface   | PHP-DI            |
| **PSR-12** | Extended Coding Style | PHP-CS-Fixer      |
| **PSR-15** | HTTP Handlers         | Middleware stack  |
| **PSR-16** | Simple Cache          | symfony/cache     |
| **PSR-18** | HTTP Client           | guzzlehttp/client |

---

## 📦 Complete composer.json

```json
{
  "name": "flowone/flowone",
  "description": "Modern, lightweight, secure CMS platform",
  "type": "project",
  "license": "MIT",
  "require": {
    "php": "^8.4",
    "ext-pdo": "*",
    "ext-pdo_sqlite": "*",
    "ext-pdo_mysql": "*",
    "ext-mbstring": "*",
    "ext-json": "*",
    "ext-openssl": "*",
    "ext-gd": "*",
    "guzzlehttp/psr7": "^2.0",
    "middlewares/utils": "^3.0",
    "nikic/fast-route": "^1.3",
    "php-di/php-di": "^7.0",
    "twig/twig": "^3.0",
    "symfony/cache": "^6.0",
    "symfony/console": "^6.0",
    "symfony/translation": "^6.0",
    "lcobucci/jwt": "^5.0",
    "league/oauth2-server": "^8.0",
    "league/flysystem": "^3.0",
    "intervention/image": "^3.0",
    "monolog/monolog": "^3.0"
  },
  "require-dev": {
    "phpunit/phpunit": "^10.0",
    "phpstan/phpstan": "^1.10",
    "friendsofphp/php-cs-fixer": "^3.0"
  },
  "autoload": {
    "psr-4": {
      "FlowOne\\": "src/",
      "FlowOne\\Plugins\\": "plugins/",
      "FlowOne\\Themes\\": "themes/"
    }
  },
  "scripts": {
    "test": "phpunit",
    "phpstan": "phpstan analyse",
    "cs-fix": "php-cs-fixer fix"
  }
}
```

---

**Technology decisions are always open to discussion**. If you have suggestions for better alternatives, please open an issue!
