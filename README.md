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

The `memberships:expire` command marks approved transactions with past expiry dates as expired. 

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

## Current limitations

- All authenticated accounts are administrators; there are no user roles or permission levels yet.
- Payments are entered by an administrator. No payment gateway is connected.
- The system accepts one full payment per transaction; installments and refunds are not implemented.
- Categories and levels are deactivated rather than hard-deleted to protect existing records.
- The demo fees and benefits are sample data; adjust them for your actual requirements.
"# WebSystemTechnologiesFinal" 
