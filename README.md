# Puihaha E-learning

**Created by Carl Michael R. Eugenio**

A Laravel membership management and transaction processing system. It provides an admin dashboard for managing members, membership categories and levels, applications, renewals, and payments.

## Features

- Dark interface built with Pico.css and a small project stylesheet.
- Admin login and account registration.
- Dashboard showing member totals, pending transactions, and collected payments.
- Member registration, profiles, editing, search, and archive/deactivation.
- Unique membership numbers generated when a member is registered.
- Membership categories and levels with fees, validity periods, and benefits.
- Application and renewal transactions with unique references.
- Payment records connected to transactions.
- Approval is available only after a transaction has been paid.
- Member transaction and payment history, plus printable member profiles.
- Expiration command that marks ended memberships as expired.

## Technology and requirements

- PHP 8.2 or later, with PDO MySQL enabled
- Composer
- MySQL 8 or compatible MariaDB
- A browser
- Internet access to load Pico.css from jsDelivr

## Set up with the SQL import file

These steps use the included SQL file to create the database and starter records.

1. Extract the project ZIP and open a terminal in the extracted project folder.
2. Start your MySQL service. With XAMPP, start **MySQL** in the XAMPP Control Panel.
3. Open phpMyAdmin, choose **Import**, select `database/membership_tps.sql`, and import it. The file creates the `membership_tps` database, tables, starter categories and levels, and a demo admin account.
4. Install PHP dependencies and create your environment file:

   ```bash
   composer install
   copy .env.example .env
   ```

   On macOS or Linux, use `cp .env.example .env` instead of `copy`.

5. Open `.env` and check the application and database values. The defaults are:

   ```env
   APP_NAME="Puihaha E-learning"
   APP_URL=http://127.0.0.1:8000
   DB_CONNECTION=mysql
   DB_HOST=127.0.0.1
   DB_PORT=3306
   DB_DATABASE=membership_tps
   DB_USERNAME=root
   DB_PASSWORD=
   SESSION_DRIVER=file
   ```

   Change `DB_USERNAME` or `DB_PASSWORD` if your MySQL setup uses different credentials. Keep `SESSION_DRIVER=file` unless you have also created a `sessions` table.

6. Generate the Laravel application key and clear cached configuration:

   ```bash
   php artisan key:generate
   php artisan config:clear
   ```

7. Start the local server:

   ```bash
   php artisan serve
   ```

8. Visit [http://127.0.0.1:8000](http://127.0.0.1:8000).

The SQL file already creates and records the included migrations, so you do not need to run migrations after importing it.

## Alternative: create tables with migrations

Use this method if you do not want to import the SQL file. Create an empty MySQL database named `membership_tps`, configure `.env`, install dependencies, and run:

```bash
php artisan key:generate
php artisan migrate --seed
php artisan serve
```

Do not use the SQL import and the migration setup as separate fresh-install methods on the same empty database; choose one method.

## Deploy on Vercel

Vercel can run this Laravel app through its Docker-based Container Runtime. This deployment path is currently in beta; Laravel does not have a zero-configuration Vercel preset. The project includes `Dockerfile`, `Caddyfile`, `.dockerignore`, and `vercel.json` for this approach. See Vercel's [Laravel with Docker guide](https://vercel.com/kb/guide/laravel-php-with-docker) and [Container Runtime overview](https://vercel.com/blog/dockerfile-on-vercel).

The app needs a **hosted MySQL database** that Vercel can reach. Do not use MySQL running on your own computer or a local database file; the deployed container cannot reach your computer's `127.0.0.1`, and Vercel's container filesystem is not durable. Keep the database close to the Vercel compute region where possible.

### Use TiDB Cloud

TiDB Cloud Starter and Essential provide a managed, MySQL-compatible database that can be connected to this Laravel app. The project is configured to read the `TIDB_HOST`, `TIDB_PORT`, `TIDB_DATABASE`, `TIDB_USER`, and `TIDB_PASSWORD` variables supplied by the TiDB Cloud Marketplace integration. Laravel's usual `DB_*` variables take priority if you set them too.

1. Push this project to GitHub and import it into Vercel first.
2. In Vercel, open **Integrations** (or the Marketplace), find **TiDB Cloud**, and add it to this Vercel project.
3. Follow TiDB's setup flow to select or create an organization, project, **Starter or Essential** cluster, and database. Choose a region near your Vercel deployment. Save the database password when TiDB shows it.
4. Select **General** if the integration asks which framework you use. Let it add the `TIDB_*` environment variables to the Vercel project. Do not put the password in Git or in a committed `.env` file.
5. Create the schema once. The simplest option is to use a MySQL-compatible client to import `database/membership_tps.sql` into the TiDB database. Alternatively, point your local `.env` at TiDB and run `php artisan migrate --force --seed` once. Avoid running both setup methods on the same database.
6. Deploy or redeploy the Vercel project. The included container has the CA certificate bundle needed for TiDB's TLS connection.

TiDB requires TLS for public connections. Starter and Essential clusters use public endpoints, and Vercel's outgoing IP addresses may vary, so a restrictive IP allowlist can prevent the app from connecting. TiDB is MySQL-compatible, but does not support every MySQL feature; this project's schema and Laravel queries use the supported common features.

### Prepare the project and database

1. Push the project to a GitHub repository. Include `Dockerfile`, `Caddyfile`, `vercel.json`, and `database/migrations/2026_11_23_000006_create_sessions_table.php`.
2. Create a hosted MySQL-compatible database with your chosen provider. If you use TiDB Cloud, follow the TiDB steps above. Save its host, port, database name, username, and password.
3. Point a local `.env` temporarily at that hosted database and run the migrations once from your computer:

   ```bash
   php artisan migrate --force --seed
   ```

   This creates the tables and demo records, including the database session table. Do not run migrations from the container startup command; concurrent instances could try to migrate at the same time. Keep the hosted database credentials out of Git.

### Add the project to Vercel

1. Create or sign in to your Vercel account, choose **Add New → Project**, and import the GitHub repository.
2. Leave the project root set to the Laravel project folder. The included `vercel.json` routes requests to the Docker container.
3. Add the following values under **Project Settings → Environment Variables**. Add them for Production, and add Preview values only if Preview deployments should connect to a database.

   | Variable | Value |
   |---|---|
   | `APP_NAME` | `Puihaha E-learning` |
   | `APP_ENV` | `production` |
   | `APP_DEBUG` | `false` |
   | `APP_KEY` | A generated Laravel key; see below |
   | `APP_URL` | Your deployed `https://...vercel.app` URL |
   | `DB_CONNECTION` | `mysql` |
   | `DB_HOST` | Host from your database provider; omit when using TiDB's `TIDB_HOST` variable |
   | `DB_PORT` | Port from your provider, usually `3306`; omit when using TiDB's `TIDB_PORT` variable |
   | `DB_DATABASE` | Database name; omit when using TiDB's `TIDB_DATABASE` variable |
   | `DB_USERNAME` | Database username; omit when using TiDB's `TIDB_USER` variable |
   | `DB_PASSWORD` | Database password; omit when using TiDB's `TIDB_PASSWORD` variable |
   | `SESSION_DRIVER` | `database` |
   | `CACHE_STORE` | `array` |
   | `QUEUE_CONNECTION` | `sync` |
   | `LOG_CHANNEL` | `stderr` |

4. Generate an application key locally with `php artisan key:generate --show`, then copy its output into the Vercel `APP_KEY` variable. Keep the same key for that deployment environment and do not commit it.
5. Deploy the project. Vercel will build the Docker image and provide a deployment URL. Set `APP_URL` to that URL, save the variable, and redeploy.
6. Open the deployed URL and sign in. The seeded demo administrator is `admin@example.com` with password `password`; change it before sharing the site.

You can also deploy from the project folder with the Vercel CLI: install it using `npm install -g vercel`, run `vercel login`, then run `vercel deploy --prod`.

### Vercel deployment notes

- The container is stateless. This app uses MySQL for its data and sessions; do not depend on files written inside the running container to persist.
- This project has no upload feature. If uploads are added later, store files in durable object storage rather than the container filesystem.
- The membership expiry command is included, but Vercel will not run Laravel's `schedule:work` continuously for you. Configure a Vercel Cron HTTP endpoint or an external scheduler before relying on automatic expiry.
- Public account registration currently grants administrator access. Disable or protect registration before opening the deployed system to the public.
- The database, Vercel project, and Git repository are separate services. Importing the SQL file into a local phpMyAdmin database does not make that database available to Vercel.

## Sign-in and registration

The SQL import and database seeder provide this local demo account:

- **Email:** `admin@example.com`
- **Password:** `password`

The login page links to the registration page. New accounts are signed in after registration. **At present, every registered account has administrator access**, as requested for this version. Keep public registration limited to a trusted local environment until role-based access is added.

If you changed the password directly in MySQL and login fails, set it through Laravel so it is stored as a bcrypt hash:

```bash
php artisan tinker
```

At the Tinker prompt, run:

```php
$user = \App\Models\User::where('email', 'admin@example.com')->firstOrFail();
$user->password = \Illuminate\Support\Facades\Hash::make('YourNewPassword123!');
$user->save();
```

Replace the sample password with one you choose, then enter `exit` and sign in with it. Do not store a plain-text password in the database.

## Main workflows

### Register a member

1. Sign in and open **Members → Add member**.
2. Enter the member's name, email, category, and level.
3. Saving the form generates a membership number and creates a pending application transaction using the selected level's fee.

### Record a payment and approve a transaction

1. Open **Payments** and choose a pending transaction.
2. Enter the full amount due, payment method, and payment date. Partial or excess payments are not accepted.
3. Recording the payment changes the transaction status to **Paid**.
4. Open **Transactions** and choose **Approve**. Approval sets the membership start and expiry dates using the validity period saved for that transaction.

### Create a renewal

Open **Transactions**, choose a member in **Create renewal**, and submit. The system creates a pending renewal at the member's current level fee.

### Manage categories and levels

Use **Categories** and **Levels** to add or edit their details. Deactivate categories or levels that should no longer be offered. Existing records keep their references to these entries.

### Expire memberships

The `memberships:expire` command marks approved transactions with past expiry dates as expired. For local development, run the Laravel scheduler in a second terminal:

```bash
php artisan schedule:work
```

You can also run the expiry command manually:

```bash
php artisan memberships:expire
```

## Project structure

| Path | Purpose |
|---|---|
| `app/Http/Controllers/` | Login, registration, members, categories, levels, transactions, payments, and dashboard logic |
| `app/Models/` | Eloquent models and database relationships |
| `app/Console/Commands/` | Scheduled membership expiry command |
| `database/migrations/` | Laravel table definitions |
| `database/seeders/` | Demo admin, categories, and levels |
| `database/membership_tps.sql` | Importable MySQL database and starter data |
| `resources/views/` | Blade pages for login, registration, dashboard, and management screens |
| `routes/web.php` | Web routes and authentication access rules |
| `public/css/app.css` | Dark theme and page layout styles |
| `.env.example` | Sample local environment configuration |

## Troubleshooting

### `Table 'membership_tps.sessions' doesn't exist`

Open `.env`, set `SESSION_DRIVER=file`, save, and run:

```bash
php artisan config:clear
```

If you prefer database sessions, create the Laravel sessions migration and run it:

```bash
php artisan make:session-table
php artisan migrate
```

### `This password does not use the Bcrypt algorithm`

The database password value was likely entered as plain text or another hash type. Use the Tinker steps above to save a bcrypt hash.

### MySQL connection error

Make sure MySQL is running and that the `DB_HOST`, `DB_PORT`, `DB_DATABASE`, `DB_USERNAME`, and `DB_PASSWORD` values in `.env` match your local setup. Then run `php artisan config:clear`.

### Changes are not showing

After changing `.env`, run `php artisan config:clear`. If a page's CSS still appears unchanged, hard-refresh the browser.

## Current limitations

- All authenticated accounts are administrators; there are no user roles or permission levels yet.
- Payments are entered by an administrator. No payment gateway is connected.
- The system accepts one full payment per transaction; installments and refunds are not implemented.
- Categories and levels are deactivated rather than hard-deleted to protect existing records.
- The demo fees and benefits are sample data; adjust them for your actual requirements.
"# WebSystemTechnologiesFinal" 
