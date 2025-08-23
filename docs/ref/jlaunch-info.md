
this is info aboyu jBoilerplate that we have created by our team.

A modern Vue 3 boilerplate with TypeScript, Shadcn UI, and flexible database options.

Features
🚀 Vue 3 with Composition API
🔥 TypeScript for type safety
🎨 Shadcn UI with customizable components
📦 Pinia for state management
🔄 Vue Router with route guards
🌐 Vue I18n for internationalization
🔍 Knex.js for database access
🎭 Dark mode with system preference detection
🚦 Form validation with Vuelidate
📱 Responsive design
📈 Dashboard components and layouts
🛠️ Admin and SuperAdmin interfaces
⚡ Vite for lightning-fast development
🗄️ Flexible database options (MySQL, PostgreSQL, SQLite)
🐳 Docker deployment options
🛠️ CLI for easy setup
Prerequisites
Node.js 16+
pnpm 8+
Docker and Docker Compose (for containerized deployment)
Quick Start
Using the CLI (Recommended)
The jBoilerplate CLI makes it easy to set up your project with your preferred database configuration:

# Install dependencies
pnpm install

# Run the setup CLI
pnpm run cli:setup
The CLI will guide you through configuring your application with the following options:

Full stack deployment - Includes a MySQL database container
App-only deployment - Connect to your own external database
Manual Setup
Option 1: Full Stack Deployment (with built-in database)
Create a .env file with database credentials:
# Docker settings
DB_CLIENT=mysql
DB_HOST=db
DB_PORT=3306
DB_USER=jboilerplate
DB_PASSWORD=jboilerplate
DB_NAME=jboilerplate
DB_ROOT_PASSWORD=rootpassword

# App settings
VITE_DB_CLIENT=mysql
VITE_DB_HOST=db
VITE_DB_PORT=3306
VITE_DB_USER=jboilerplate
VITE_DB_PASSWORD=jboilerplate
VITE_DB_NAME=jboilerplate
Start the application with database:
# Start with database
pnpm run docker:full
Option 2: App-Only Deployment (connect to external database)
Create a .env file with your external database credentials:
# App settings
VITE_DB_CLIENT=mysql  # or pg for PostgreSQL, sqlite3 for SQLite
VITE_DB_HOST=your-db-host
VITE_DB_PORT=3306     # or 5432 for PostgreSQL
VITE_DB_USER=your-username
VITE_DB_PASSWORD=your-password
VITE_DB_NAME=your-database
Start the application only:
# Start application only
pnpm run docker:app-only
Database Migrations
Run migrations to set up your database schema:

# Create a new migration
pnpm run migrate:make migration_name

# Run migrations
pnpm run migrate:latest

# Rollback migrations
pnpm run migrate:rollback

# Create a seed file
pnpm run seed:make seed_name

# Run seeds
pnpm run seed:run
Development
# Start development server
pnpm run dev

# Build for production
pnpm run build

# Run tests
pnpm run test
Environment Variables
Variable	Description	Default
VITE_DB_CLIENT	Database client (mysql, pg, sqlite3)	mysql
VITE_DB_HOST	Database host	localhost
VITE_DB_PORT	Database port	3306
VITE_DB_USER	Database username	jboilerplate
VITE_DB_PASSWORD	Database password	jboilerplate
VITE_DB_NAME	Database name	jboilerplate
VITE_DB_SSL	Enable SSL for database connection	false
Project Structure
jBoilerplate/
├── cli/                   # CLI tool for project setup
├── migrations/            # Database migrations
├── public/               # Static assets
├── seeds/                # Database seed files
├── src/
│   ├── assets/           # Application assets
│   ├── components/       # Vue components
│   │   └── ui/           # Shadcn UI components
│   ├── composables/      # Vue composables
│   ├── constants/        # Application constants
│   ├── layouts/          # Page layouts
│   ├── lib/              # Utilities and libraries
│   │   └── db/           # Database integration
│   ├── locales/          # I18n translation files
│   ├── pages/            # Application pages
│   │   ├── admin/        # Admin pages
│   │   └── superadmin/   # SuperAdmin pages
│   ├── plugins/          # Vue plugins
│   ├── router/           # Vue Router configuration
│   ├── services/         # API and other services
│   ├── stores/           # Pinia stores
│   └── types/            # TypeScript type definitions
├── templates/            # Project templates
├── components.json       # Shadcn UI configuration
├── docker-compose.yml    # Full stack Docker configuration
├── docker-compose.app-only.yml # App-only Docker configuration
├── knexfile.js          # Knex.js configuration
└── vite.config.mts      # Vite configuration
License
MIT