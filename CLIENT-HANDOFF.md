# BB Store — Client Source Handover

## What is included

This package is the complete tracked application source from the `main` branch of:

- Repository: https://github.com/ffgg35646-ops/bb
- Source branch: `main`
- Source commit: `0e755aaeb30fbd6fb7eb0ca4d509b4d9a014fe7c`

It includes the Next.js application, API routes, React components, MongoDB and Telegram integrations, scripts, static assets, product/catalog source data files (CSV/JSON/TXT), configuration files, and this handover guide.

The package also contains a generated `package-lock.json` so the client can install the exact dependency tree with `npm ci`.

## Requirements

- Node.js 24.x (LTS line)
- npm 11.x
- A MongoDB database (MongoDB Atlas or another reachable MongoDB deployment)
- A Telegram bot and the Telegram numeric user ID of the administrator
- A hosting provider that supports Next.js Node.js applications (Vercel is supported by the included configuration)

## Install and run locally

1. Extract the ZIP.
2. Copy `.env.example` to `.env.local`.
3. Fill in the environment variables listed below.
4. Install dependencies:
   ```bash
   npm ci
   ```
5. Start the development server:
   ```bash
   npm run dev
   ```
6. Open http://localhost:3000.

For a production build:
```bash
npm run build
npm start
```

## Environment variables

| Variable | Required | Purpose |
|---|---|---|
| `MONGODB_URI` | Yes | MongoDB connection string. Create a client-owned database user with read/write access only to the project database. |
| `MONGODB_DB` | Recommended | Database name. The application defaults to `erd_algwal` if this variable is omitted. |
| `TELEGRAM_BOT_TOKEN` | For Telegram admin | Token of the client's own Telegram bot, created through BotFather. |
| `TELEGRAM_ADMIN_ID` | For Telegram admin | Numeric Telegram user ID allowed to administer the store. |
| `TELEGRAM_WEBHOOK_SECRET` | For Telegram admin | Random secret using letters, numbers, underscore, or hyphen; 1–256 characters. |
| `TELEGRAM_WEBHOOK_URL` | For Telegram admin | Full HTTPS URL ending in `/api/telegram/webhook`. |
| `NEXT_PUBLIC_SITE_URL` | Recommended | Public site origin, for example `https://shop.example.com`, with no trailing slash. |
| `MODEL_API_KEY` | Optional | Server-side key for the AI phone-case color-mask workflow. |

Never commit real values to GitHub or place secrets in variables prefixed with `NEXT_PUBLIC_`.

## MongoDB setup and existing data

- The application uses the `categories`, `products`, `orders`, `store_settings`, `store_pages`, `store_banners`, Telegram session/update, and customer-review collections as needed by its features.
- The catalogue API initializes its configured seed catalogue and uses the MongoDB catalogue for live product/category data. Existing admin-managed/imported records have safeguards in the catalogue initialization path.
- The ZIP contains source catalogue data and the supplied product data files in the repository. It does **not** contain the live MongoDB database, customer orders, user information, Telegram sessions, or database credentials.
- To keep an existing store's live data, point the deployment at the same authorized database. To move to a new database, export/import the required collections with MongoDB Database Tools (`mongodump` / `mongorestore`) using a secure transfer channel. Do not put database dumps or credentials in a public repository or this source ZIP.
- Take a database backup before any migration. Verify product image references after migration; some legacy images may use Telegram file IDs while other images are external URLs.

## Telegram administrator setup

1. Create a new bot for the client with BotFather.
2. Set `TELEGRAM_BOT_TOKEN`, `TELEGRAM_ADMIN_ID`, `TELEGRAM_WEBHOOK_SECRET`, and `TELEGRAM_WEBHOOK_URL`.
3. Deploy the application to an HTTPS domain.
4. The build script attempts webhook registration when the three Telegram webhook variables are available. It skips registration when they are absent.
5. To register or refresh it manually from an environment where those variables are set:
   ```bash
   npm run telegram:set-webhook
   ```

The webhook accepts private messages from the configured administrator ID and validates Telegram's secret-token header.

## Deploying to Vercel

1. Import the client's GitHub repository or upload the extracted source to a client-owned repository.
2. Select the Next.js framework preset.
3. Set the environment variables above in the intended Vercel environment.
4. Use `npm ci` for installation and `npm run build` for the build command.
5. Attach the client's custom domain and make sure `NEXT_PUBLIC_SITE_URL` and `TELEGRAM_WEBHOOK_URL` use that final HTTPS domain.
6. Verify the storefront, MongoDB connection, Telegram webhook, order creation, and admin notifications after deployment.

Do not use the seller's existing Vercel project, MongoDB credentials, Telegram bot token, or domain as the client's production infrastructure.

## Application features represented in this source

- Arabic RTL storefront, category browsing, product listing/details, and search.
- Cart and checkout/order submission flows.
- Product and category administration through the Telegram bot.
- Customer order management and administrator notifications.
- Product images, banners/sliders, footer/store settings, customer reviews, policies, and customer-service content.
- Printable order, receipt, sales-invoice, and installment documents.
- Saudi region/city datasets and related checkout address support.
- Phone-case color-mask preparation utilities and related admin tooling.

## Important integration boundary

The current source documents the checkout payment page as a preparatory/manual flow. A production payment gateway is **not configured by this source package** and the checkout page must not be represented to the client as a live card/debit integration. Any gateway onboarding, credentials, callbacks, reconciliation, and live-payment testing must be handled as a separate authorized integration.

## Security and handover checklist

- Create client-owned MongoDB, Telegram, hosting, and domain accounts.
- Use fresh client credentials; do not reuse the developer's tokens.
- Keep `.env.local` and all real secrets outside Git.
- Restrict MongoDB network access and database privileges.
- Back up the live database before import or migration.
- Rotate credentials if any were ever shared in chat, logs, or source control.
- Test the admin bot with the client's administrator ID before handover.

## Package exclusions

The source ZIP intentionally excludes `.git` history, `node_modules`, `.next`, Vercel local metadata, real `.env` files, logs, and local backups. These are generated locally or contain machine-specific/sensitive data and are not required to build the application.
