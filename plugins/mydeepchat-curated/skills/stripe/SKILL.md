---
name: stripe
description: Look up Stripe customers, subscriptions, charges, and payment methods. Cancel subscriptions and issue refunds. Use when investigating billing issues, subscription cancellations, or payment disputes. Requires STRIPE_SECRET_KEY env var.
allowed-tools: Bash, Read
---
<!-- FORK ADAPTADO: Quitar referencia hardcodeada a 'Civitai CustomerSubscription table'; documentar que el script companion query.mjs no está incluido y proveer alternativa con stripe-cli; adaptar rutas a Windows; STRIPE_SECRET_KEY como env var (nunca hardcodeado) · origen: civitai-civitai-claude-skills-stripe-skill-md · 2026-06-26 -->

# Stripe

Look up and manage Stripe customers, subscriptions, charges, and refunds for support investigations.

## Setup

### Required

- `STRIPE_SECRET_KEY` — from Stripe Dashboard > Developers > API Keys (Secret key)
  - Set as environment variable: `$env:STRIPE_SECRET_KEY = "sk_live_..."`
  - **NEVER hardcode this key in code or skill files**

### Option A: Stripe CLI (Recommended)

Install Stripe CLI for Windows: [stripe.com/docs/stripe-cli](https://stripe.com/docs/stripe-cli)

```powershell
# Login (only needed once)
stripe login

# Or use API key directly
$env:STRIPE_API_KEY = $env:STRIPE_SECRET_KEY
```

### Option B: Node.js Script

If you have a `query.mjs` script in `.claude/skills/stripe/`, use it:

```powershell
node ".claude\skills\stripe\query.mjs" customer user@example.com
```

> **Note**: The `query.mjs` companion script is not included. Create it using the Stripe Node.js SDK (`npm install stripe`) or use the Stripe CLI commands below.

## Quick Reference — Stripe CLI

```powershell
# Look up customer by email
stripe customers list --email="user@example.com"

# Get customer details
stripe customers retrieve cus_1ABC123

# List subscriptions for a customer
stripe subscriptions list --customer=cus_1ABC123

# Get subscription details
stripe subscriptions retrieve sub_1ABC123

# List charges for a customer
stripe charges list --customer=cus_1ABC123

# Cancel subscription IMMEDIATELY
stripe subscriptions cancel sub_1ABC123

# Cancel at end of billing period
stripe subscriptions update sub_1ABC123 --cancel-at-period-end=true

# Full refund of a charge
stripe refunds create --charge=ch_1ABC123

# Partial refund (amount in cents: 500 = $5.00)
stripe refunds create --charge=ch_1ABC123 --amount=500

# Refund with reason
stripe refunds create --charge=ch_1ABC123 --reason=requested_by_customer

# List payment methods
stripe payment_methods list --customer=cus_1ABC123 --type=card
```

## Quick Reference — API via PowerShell

```powershell
$headers = @{ Authorization = "Bearer $env:STRIPE_SECRET_KEY" }

# Search customer by email
Invoke-RestMethod -Uri "https://api.stripe.com/v1/customers/search?query=email:'user@example.com'" -Headers $headers

# Get customer
Invoke-RestMethod -Uri "https://api.stripe.com/v1/customers/cus_1ABC123" -Headers $headers

# List subscriptions
Invoke-RestMethod -Uri "https://api.stripe.com/v1/subscriptions?customer=cus_1ABC123" -Headers $headers
```

## Commands Summary

| Task | Stripe CLI |
|------|-----------|
| Find customer by email | `stripe customers list --email="..."` |
| Get customer details | `stripe customers retrieve cus_xxx` |
| List subscriptions | `stripe subscriptions list --customer=cus_xxx` |
| Get subscription | `stripe subscriptions retrieve sub_xxx` |
| List charges | `stripe charges list --customer=cus_xxx` |
| Cancel subscription (immediate) | `stripe subscriptions cancel sub_xxx` |
| Cancel at period end | `stripe subscriptions update sub_xxx --cancel-at-period-end=true` |
| Full refund | `stripe refunds create --charge=ch_xxx` |
| Partial refund | `stripe refunds create --charge=ch_xxx --amount=CENTS` |
| List payment methods | `stripe payment_methods list --customer=cus_xxx --type=card` |
| List invoices | `stripe invoices list --customer=cus_xxx` |

## Refund Reasons

Valid `--reason` values:

- `duplicate` — Charge was a duplicate
- `fraudulent` — Charge was fraudulent
- `requested_by_customer` — Customer requested the refund

## Safety Notes

- **Cancel** terminates the subscription immediately by default. Use `--cancel-at-period-end=true` for graceful cancellation.
- **Refunds** are **destructive and irreversible**. Always confirm with the human operator before executing.
- Preview what would be refunded before acting: `stripe charges list --customer=cus_xxx` and review charges manually.
- The customer lookup command (`stripe customers retrieve`) is read-only and safe to run at any time.

## Amount Reference

Stripe uses smallest currency unit (cents for USD/CLP):

- USD $10.50 = 1050 cents
- CLP $1,000 = 100000 (centavos)
- Use `--amount` parameter in smallest currency unit

## ID Prefixes

- Customers: `cus_`
- Charges: `ch_`
- Subscriptions: `sub_`
- Invoices: `in_`
- Products: `prod_`
- Prices: `price_`
- Payment Intents: `pi_`
- Refunds: `re_`

## When to Use This Skill

- Investigating billing/subscription issues
- Verifying payment history for disputed charges
- Canceling subscriptions for users who request it
- Processing refunds for confirmed billing errors
- Cross-referencing Stripe data with application database records
