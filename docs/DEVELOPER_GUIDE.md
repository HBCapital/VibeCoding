# Developer Guide

Welcome to the FlowOne development guide. This document provides instructions on how to set up your development environment.

## Prerequisites

- **PHP**: 8.4 or higher
- **Composer**: 2.0 or higher
- **Node.js**: 18.0 or higher
- **Database**: SQLite (for dev) or MySQL 8.0+

## Installation

1.  **Clone the repository**

    ```bash
    git clone https://github.com/hbclm/FlowOne.git
    cd FlowOne
    ```

2.  **Install PHP dependencies**

    ```bash
    composer install
    ```

3.  **Install Node.js dependencies**

    ```bash
    npm install
    ```

4.  **Setup Environment**
    Copy `.env.example` to `.env` and configure your database settings.

    ```bash
    cp .env.example .env
    ```

5.  **Run Migrations**

    ```bash
    php flowone migrate
    ```

6.  **Start Development Server**
    ```bash
    php flowone serve
    ```

## Directory Structure

- `src/`: Core application code
- `public/`: Public entry point
- `plugins/`: Plugin directory
- `themes/`: Theme directory
- `tests/`: Unit and integration tests

## Coding Standards

Please refer to [GUIDELINE.md](./GUIDELINE.md) for coding standards and best practices.
