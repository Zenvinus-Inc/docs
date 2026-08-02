# Contributing API Docs

## Adding New Endpoints

### 1. Add `@Tags` Annotation in Handler

Every handler function must have exactly one `@Tags` annotation that determines which spec file it goes to:

```go
// @Summary Create flight order
// @Tags flights                    // ← Must match one of these tags
// @Router /v1/booking/flight-orders [post]
func (h *Handler) CreateOrder(c *gin.Context) { ... }
```

**Valid Tags by Group:**

| Group | Valid Tags | Example Usage |
|-------|-----------|---------------|
| **basic** | `auth`, `users`, `admin` | `/api/v1/auth/login` → `@Tags auth` |
| **finance** | `payments`, `wallet`, `withdraws` | `/api/v1/wallet/topups` → `@Tags wallet` |
| **booking** | `flights`, `hotels`, `cars-and-transfers`, `travel` | `/v1/shopping/flight-offers` → `@Tags flights` |

### 2. Run Commands

From the boilerplate repo (`go-rest-api-boilerplate`):

```bash
# Regenerate swagger.json with new endpoint
make swag

# Convert and sync to docs repo
make docs-sync DOCS_DIR=/path/to/zenvinus-docs
```

This produces:
- `api-reference/basic.json` - auth, users, admin endpoints
- `api-reference/finance.json` - payments, wallet, withdraws
- `api-reference/booking.json` - flights, hotels, transfers, travel

### 3. Verify & Commit

```bash
cd /path/to/zenvinus-docs
git status  # Should show basic.json, finance.json, booking.json changed
git add api-reference/*.json
git commit -m "Add <feature-name> endpoint documentation"
git push
```

## Existing Endpoints Summary

### Basic API (7 paths)
- `@Tags auth`: login, logout, refresh, me, register
- `@Tags users`: get/update/delete user
- `@Tags admin`: list users

### Finance API (12 paths)
- `@Tags payments`: create payment, get payments, events
- `@Tags wallet`: top-ups, transactions, balance
- `@Tags withdraws`: inquiry, withdrawal list, management

### Booking API (27 paths)
- `@Tags flights`: flight-offers search/pricing/prediction, orders, seatmaps, dates, destinations (9 paths)
- `@Tags hotels`: hotel-offers, orders, sentiments, location lists (7 paths)
- `@Tags cars-and-transfers`: transfer offers/orders/search + cancellation (4 paths)
- `@Tags travel`: locations, airlines, activities, market-insights, trip-purpose (10 paths)

## What's Filtered Out

- Health check endpoints (`/health*`) - not shown in docs
- Webhook endpoints (`/webhooks*`) - internal use only

## Tag Normalization

Old tags are automatically normalized during conversion:
- `flight` → `flights`
- `hotel` → `hotels`
- `transfers` → `cars-and-transfers`
- `market-insights` → `travel`
- `trip` → `travel`
- `Health` → `health` (ignored)
- `locations` → `travel`
- `activities` → `travel`
