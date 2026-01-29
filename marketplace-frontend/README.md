# Spaces Marketplace Frontend

Web interface for the Spaces Protocol secondary marketplace.

### Prerequisites

- Node.js (v18+)
- npm

### Installation

```bash
npm install
```

### Environment Variables

Copy the example environment file and configure:

```bash
cp env.example .env
```

### Development

```bash
npm run dev
```

Starts the development server at `http://localhost:3000` with hot reload.

### Production Build

```bash
npm run build
```

You can use `pm2` or something similar to run it.

## Features

- Browse and search space listings
- Sort by date or price
- Post new listings via JSON input
- View listing details with CLI purchase commands
- Internationalized domain name support (punycode)

## Deployment

Allowed hosts configured in `vite.config.ts`:
- `spaces.market`
- `testnet.spaces.market`

## Related

- [Spaces Protocol](https://spacesprotocol.org)
- [space-cli Documentation](https://docs.spacesprotocol.org)

## License

MIT
