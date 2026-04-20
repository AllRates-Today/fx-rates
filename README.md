# 📈 fx-rates

[![npm version](https://img.shields.io/npm/v/fx-rates)](https://www.npmjs.com/package/fx-rates)
[![npm downloads](https://img.shields.io/npm/dm/fx-rates)](https://www.npmjs.com/package/fx-rates)
[![license](https://img.shields.io/npm/l/fx-rates)](./LICENSE)

Lightweight Node.js library and CLI for **real-time mid-market currency exchange rates** — 160+ currencies sourced from **Refinitiv (Reuters)** and interbank feeds via the [AllRatesToday API](https://allratestoday.com).

- ⚡ **Real-time** rates, not daily snapshots
- 🌍 **160+ currencies** including majors, emerging-market, precious metals (XAU, XAG)
- 📦 **Zero runtime dependencies** (uses native `fetch`)
- 🖥️ Works as a **Node library AND CLI**
- 🔒 Mid-market rates (no retail markup)

---

## 📦 Installation

```bash
npm install fx-rates
```

Or with yarn / pnpm:

```bash
yarn add fx-rates
pnpm add fx-rates
```

---

## 🔑 Get a free API key

This package calls the authenticated AllRatesToday API. Grab a free key at
**[allratestoday.com/register](https://allratestoday.com/register/)** — no credit card required.

Export it once:

```bash
export ALLRATESTODAY_API_KEY="art_live_..."
```

Or pass it explicitly to each call (see below).

---

## 🚀 Usage

### Node.js — get a rate

```js
import { rate } from "fx-rates";

const r = await rate("USD", "INR");
console.log(r);
// {
//   date: "2026-04-20",
//   base: "USD",
//   target: "INR",
//   rate: 83.2145
// }
```

### Node.js — convert an amount

```js
import { convert } from "fx-rates";

const out = await convert(100, "USD", "EUR");
console.log(out);
// {
//   from: { currency: "USD", amount: 100 },
//   to:   { currency: "EUR", amount: 92.34 },
//   rate: 0.9234,
//   date: "2026-04-20"
// }
```

### Pass the API key explicitly

```js
const r = await rate("GBP", "USD", { apiKey: "art_live_..." });
```

### TypeScript

```ts
import { rate, RateResult, RateOptions } from "fx-rates";

const opts: RateOptions = { timeoutMs: 5000 };
const r: RateResult = await rate("EUR", "JPY", opts);
```

### Custom fetch (Node < 18 or test doubles)

```js
import { rate } from "fx-rates";
import fetch from "node-fetch";

const r = await rate("USD", "INR", { fetch });
```

---

## 💻 CLI

Install globally:

```bash
npm install -g fx-rates
```

Then:

```bash
$ fx-rates USD INR
{
  "date": "2026-04-20",
  "base": "USD",
  "target": "INR",
  "rate": 83.2145
}
```

Convert an amount (third positional argument):

```bash
$ fx-rates USD EUR 250
{
  "from": { "currency": "USD", "amount": 250 },
  "to":   { "currency": "EUR", "amount": 230.85 },
  "rate": 0.9234,
  "date": "2026-04-20"
}
```

Pipe into `jq`:

```bash
$ fx-rates GBP USD | jq '.rate'
1.3512
```

---

## 📚 API reference

### `rate(base, target, options?)`

Fetch the latest mid-market rate for a single pair.

| Param    | Type    | Description                                  |
|----------|---------|----------------------------------------------|
| `base`   | string  | ISO 4217 code of the source currency (`"USD"`, `"EUR"`, `"XAU"`, …) |
| `target` | string  | ISO 4217 code of the target currency         |
| `options`| object  | Optional (see below)                         |

Returns `Promise<RateResult>`:

```ts
interface RateResult {
  date: string;    // ISO date, e.g. "2026-04-20"
  base: string;    // "USD"
  target: string;  // "INR"
  rate: number;    // 83.2145
}
```

### `convert(amount, from, to, options?)`

Multiply an amount by the current rate.

### `RateOptions`

```ts
interface RateOptions {
  apiKey?: string;       // defaults to process.env.ALLRATESTODAY_API_KEY
  baseUrl?: string;      // override for staging / self-hosted proxies
  fetch?: typeof fetch;  // custom fetch implementation
  timeoutMs?: number;    // default 15000
}
```

### Errors

All errors are thrown as `AllRatesTodayError` with a `.status` field (HTTP status where applicable):

```js
import { rate, AllRatesTodayError } from "fx-rates";

try {
  await rate("USD", "XXX");
} catch (err) {
  if (err instanceof AllRatesTodayError) {
    console.error(err.status, err.message);
  }
}
```

---

## 🌍 Supported currencies

160+ currencies including majors (USD, EUR, GBP, JPY, CHF, CAD, AUD, NZD), emerging-market currencies (INR, CNY, BRL, MXN, TRY, ZAR, SGD, HKD, KRW, THB, PHP, PKR, BDT, LKR, NGN, GHS, KES, AED, SAR, EGP), and precious metals (XAU, XAG).

Full list: [`allratestoday.com/api/v1/symbols`](https://allratestoday.com/api/v1/symbols).

---

## 🔗 Related projects

- [`moneyify`](https://www.npmjs.com/package/moneyify) – Cashify-compatible converter with the same auto-fetch backing. Use when you need conversion math, expression parsing (`"10 USD to EUR"`), or big.js precision.
- [`@allratestoday/sdk`](https://www.npmjs.com/package/@allratestoday/sdk) – Full-featured SDK with historical data, batch requests, and webhooks.
- [`react-currency-localizer-realtime`](https://www.npmjs.com/package/react-currency-localizer-realtime) – React hooks and components for auto-localized pricing.
- [`allratestoday`](https://allratestoday.com) – The REST API that powers this package.

---

## 🤖 AI disclosure

This project contains code generated by Large Language Models (LLMs), under human supervision and proofreading. All published versions are reviewed, tested, and released by a human maintainer.

---

## 📄 License

[MIT](./LICENSE) © [AllRatesToday](https://allratestoday.com) — maintained by [Chathuranga Basnayaka](https://github.com/cahthuranag).
