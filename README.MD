<div align="center">
  <img src="https://cdn.akadenia.com/images/akadenia-webp/logo/horizontal-logo.svg" alt="Akadenia" width="200" />

  ## @akadenia/api

  <img src="https://cdn.akadenia.com/images/badges/npm-version-api.svg" alt="npm version" />
  <img src="https://cdn.akadenia.com/images/badges/license-mit.svg" alt="License: MIT" />
  <img src="https://cdn.akadenia.com/images/badges/typescript.svg" alt="TypeScript" />

  An opinionated Axios wrapper that gives every HTTP request a consistent response shape, built-in retry logic, and structured error handling — so you stop writing the same boilerplate across every project.

  [Documentation](https://akadenia.com/packages/akadenia-api) · [GitHub](https://github.com/akadenia/AkadeniaAPI) · [Issues](https://github.com/akadenia/AkadeniaAPI/issues)
</div>

---

## Installation

```bash
npm install @akadenia/api
```

## Quick Start

```typescript
import { AxiosApiClient } from "@akadenia/api"

const api = new AxiosApiClient({
  baseUrl: "https://api.example.com",
  timeout: 10000,   // ms, default 30000
  retries: 3,       // default 3
})

const response = await api.get<User>("/users/1")

if (response.success) {
  console.log(response.data) // typed as User
} else {
  console.error(response.message) // e.g. "Unauthorized"
}
```

## Features

- **Consistent response shape** — every call returns `AkadeniaApiResponse<T>` with a `success` boolean, typed `data`, and a human-readable `message`
- **Built-in retries** — configurable retry count, delay function, and `onRetry` callback via `axios-retry`
- **Structured error handling** — HTTP 400/401/403/404/422/500 and network errors all map to meaningful messages automatically
- **Header management** — set headers at construction time or per-request via `setHeader()`
- **Full TypeScript support** — generic response types, exported interfaces, and full `AxiosError` passthrough
- **Axios-compatible** — access the underlying `AxiosInstance` via `getInstance()` for advanced use cases

## API Reference

### `new AxiosApiClient(options)`

| Option | Type | Default | Description |
|---|---|---|---|
| `baseUrl` | `string` | required | Base URL for all requests |
| `headers` | `HeadersType` | — | Default headers for every request |
| `timeout` | `number` | `30000` | Request timeout in milliseconds |
| `retries` | `number` | `3` | Number of retry attempts on failure |
| `retryDelay` | `(count) => number` | — | Custom delay function between retries |
| `onRetry` | `(count, error) => void` | — | Callback fired on each retry |

### Methods

```typescript
api.get<T>(url, config?)
api.post<T>(url, data?, config?)
api.put<T>(url, data?, config?)
api.patch<T>(url, data?, config?)
api.delete<T>(url, config?)
api.setHeader(name, value)
api.getInstance() // returns AxiosInstance
```

All methods return `Promise<AkadeniaApiResponse<T>>`.

### Response Types

```typescript
type AkadeniaApiResponse<T> = AkadeniaApiSuccessResponse<T> | AkadeniaApiErrorResponse

type AkadeniaApiSuccessResponse<T> = AxiosResponse<T> & {
  success: boolean
  message?: string
}

type AkadeniaApiErrorResponse = {
  success: false
  message: string  // "Unauthorized" | "Not Found" | "Network Error" | ...
  data?: any
  error: any
}
```

## Examples

### Authentication header

```typescript
const api = new AxiosApiClient({ baseUrl: "https://api.example.com" })
api.setHeader("Authorization", `Bearer ${token}`)
```

### Custom retry logic

```typescript
const api = new AxiosApiClient({
  baseUrl: "https://api.example.com",
  retries: 5,
  retryDelay: (count) => count * 1000, // 1s, 2s, 3s...
  onRetry: (count, error) => console.warn(`Retry #${count}`, error.message),
})
```

### Per-request headers

```typescript
const response = await api.get("/secure-endpoint", {
  headers: { "X-Custom-Header": "value" },
})
```

## Requirements

- Node.js >= 20
- TypeScript >= 4.x (optional but recommended)

## License

[MIT](LICENSE)
