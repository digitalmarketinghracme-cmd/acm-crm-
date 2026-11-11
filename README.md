# ACME Switchgears CRM & ERP – MVP

A working full-stack starter application for ACME Switchgears with login, customer records, product/stock records, quotation management, quotation PDF download, dashboard and activity log.

## Run locally

1. Install Node.js 20 or later.
2. Extract this folder.
3. Run `npm install`.
4. Copy `.env.example` to `.env`.
5. Change `SESSION_SECRET` and `ADMIN_PASSWORD`.
6. Run `npm start`.
7. Open `http://localhost:3000`.

The first admin user is created from `ADMIN_USERNAME` / `ADMIN_PASSWORD` in `.env` (defaults to `admin` / `ChangeMe123!` outside production; production refuses to start with the default).

SQLite database file `acme.sqlite` is created automatically in the project directory.

## Included
- Session-based login and password hashing.
- Customer CRUD.
- Product CRUD and basic stock/minimum-stock fields.
- Quotation creation with multiple line items.
- Server-side quotation PDF generation.
- Quotation status workflow.
- Dashboard and activity log.
- Responsive browser interface.

## Deploying
See `DEPLOYMENT.md` for the Docker + automatic HTTPS setup, backups and operations.

## Important scope and production checklist
This is an initial working MVP, not a completed production ERP. Before live business use:
- Configure HTTPS, secure cookies, a strong session secret, persistent database backups and production hosting.
- Add user administration and granular role-based access.
- Add full purchase, supplier, sales-order, invoice, payment, returns, accounting and HR modules.
- Add transactional inventory movement and stock deduction on confirmed orders.
- Add email/WhatsApp provider integrations and website enquiry API/webhook.
- Review GST rules and rounding with the company's accountant; tax logic in this MVP is a basic estimate and must be validated for actual invoices.
- Add CSRF protection, rate limiting, stronger validation, audit retention and access controls.
- Add company logo/signature uploads and final PDF branding.
- Configure persistent session storage for production (the default in-memory session store is for development only).

## PDF notes
The PDF includes ACME company identity, customer information, line items, HSN, quantities, rates, subtotal, GST, round-off, total, amount in words, bank details and terms. It uses the saved quotation data. This starter does not yet reproduce every visual detail of the reference PDF.
