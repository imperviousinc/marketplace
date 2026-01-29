# Overview

This repository contains the backend for the Spaces Protocol secondary marketplace.

## Install

```bash
go mod download
```

## Components

- REST API to query the marketplace
- Indexer that keeps track of available spaces and invalidates them once they have been bought or transferred

## Database

This repo uses [goose](https://github.com/pressly/goose) and [sqlc](https://sqlc.dev/) for schema management and Go SQL query generation. Related files can be found in `/sql`.

Typical processes for:

1. Addition of a new query:
   Create an SQL file with the query, run `sqlc generate` (with needed env vars).
2. Change of schema:
   Create a migration file and run (on the server) `goose up` (with needed env vars).

Consult the respective tool documentation for more information.
Keep in mind that you have to change the schema if the listing structure changes, e.g., if the marketplace is modified to
accommodate subspace listings (in that case it would also require to change indexer logic to invalidate space pointers).

## Indexer

Uses [spacesprotocol/explorer-indexer](https://github.com/spacesprotocol/explorer-indexer) for blockchain interaction.

Indexer is capable of handling chain reorganizations, as it tracks and stores block hashes. It constantly checks
for new blocks. If a new one is found, it looks for its ancestor and tries to locate it in the db. If it doesn't find it,
that means there is a reorganization, and it re-validates all listings starting from the common ancestor block.

If a `transfer` transaction is found for a listing, the listing becomes invalid.

## Endpoints

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/space/<name>` | Get a single listing by space name |
| `GET` | `/listings` | Get all valid listings (supports sorting/pagination) |
| `POST` | `/postListing` | Create or update a listing |
| `GET` | `/healthcheck` | System health status |

**Query parameters for `/listings`:**
- `sort_by`: `price` or `timestamp` (default: `timestamp`)
- `sort_order`: `asc` or `desc` (default: `desc`)
- `limit`: 1-100 (default: 9)
- `offset`: >= 0 (default: 0)

Server-side request validation is handled in `cmd/rest/validator.go`.

## Dependencies

- Go 1.21+
- PostgreSQL
- [goose](https://github.com/pressly/goose) - database migrations
- [sqlc](https://sqlc.dev/) - SQL code generation
- A running [Spaces Protocol](https://github.com/spacesprotocol) node

## Environment

See `env.example` for available environment variables.

## Run

0. Set up environment variables.
1. (once, for the first time) Run migrations:
`goose up`
2. Run two processes (you can dockerize them):
   - rest: `go run cmd/rest/*`
   - indexer: `go run cmd/indexer/*`

## License

MIT

