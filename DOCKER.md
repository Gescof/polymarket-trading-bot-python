# Docker Deployment Guide

This guide explains how to run the Polymarket Trading Bot using Docker Compose.

## Prerequisites

- Docker Engine 20.10+ 
- Docker Compose 2.0+
- Your trading configuration (wallet addresses, private keys, etc.)
- **External MongoDB connection** (MongoDB Atlas, self-hosted, or other MongoDB service)

## Quick Start

### 1. Configure Environment Variables

Copy the example environment file and fill in your values:

```bash
cp .env.example .env
```

Then edit `.env` and provide your:
- `USER_ADDRESSES` - Trader addresses to copy
- `PROXY_WALLET` - Your proxy wallet address
- `PRIVATE_KEY` - Your private key (keep secure!)
- `RPC_URL` - Your Polygon RPC endpoint
- `MONGO_URI` - Your external MongoDB connection string

### 2. Start the Bot

Start the bot service:

```bash
docker-compose up -d
```

This will:
- Build the Docker image for the bot
- Start the trading bot application
- Connect to your external MongoDB instance

### 3. View Logs

Watch the bot logs in real-time:

```bash
docker-compose logs -f polymarket-bot
```

## Common Commands

### Stop All Services

```bash
docker-compose down
```

### Stop and Remove All Data (including database)

```bash
docker-compose down -v
```

### Restart the Bot

```bash
docker-compose restart polymarket-bot
```

### Rebuild After Code Changes

```bash
docker-compose up -d --build
```

### View Running Containers

```bash
docker-compose ps
```

### Execute Commands Inside the Container

```bash
# Open a shell in the bot container
docker-compose exec polymarket-bot bash

# Run a specific script
docker-compose exec polymarket-bot python -m src.scripts.wallet.check_my_stats
```

## Service Configuration

### Services Included

1. **polymarket-bot**: The main trading application

### Data Persistence

The following directories are mounted as volumes:
- `./data`: Application data and outputs
- `./logs`: Application logs

## Troubleshooting

### Bot Won't Start

1. Check logs: `docker-compose logs polymarket-bot`
2. Verify all required environment variables are set in `.env`
3. Verify your `MONGO_URI` is correct and the external MongoDB is accessible

### MongoDB Connection Failed

If the bot can't connect to your external MongoDB:

1. Verify the `MONGO_URI` in your `.env` file is correct
2. Check that your MongoDB service is running and accessible
3. Ensure firewall rules allow connections from your Docker container
4. Test connectivity from your host machine first:
   ```bash
   # Install mongosh if needed, then test:
   mongosh "your-mongo-uri-here"
   ```

### Permission Issues with Volumes

On Linux/Mac, you may need to adjust permissions:

```bash
sudo chown -R $USER:$USER ./data ./logs
```

### Reset Everything

To completely reset (⚠️ destroys all data):

```bash
docker-compose down -v
rm -rf data/ logs/
docker-compose up -d
```

## Production Deployment

### Security Best Practices

1. **Secure your private key**:
   - Never commit `.env` to version control
   - Consider using Docker secrets or environment-specific files

2. **Secure your MongoDB connection**:
   - Use strong passwords in your MongoDB connection string
   - Enable authentication on your MongoDB instance
   - Use connection strings with TLS/SSL when possible
   - Whitelist only necessary IP addresses in MongoDB Atlas or firewall

3. **Enable firewall rules**:
   - Only expose necessary ports
   - Restrict Docker container network access if needed

## Environment Variables Reference

See `.env.example` for a complete list of configurable environment variables.

### Required Variables

- `USER_ADDRESSES`: Addresses to copy trade
- `PROXY_WALLET`: Your wallet address
- `PRIVATE_KEY`: Your private key
- `RPC_URL`: Polygon RPC endpoint
- `MONGO_URI`: External MongoDB connection string

### Optional Variables

- `COPY_STRATEGY`: Strategy type (default: `fixed`)
- `MULTIPLIER`: Position size multiplier (default: `0.5`)
- `POLL_INTERVAL`: Polling interval in seconds (default: `1.0`)
- `MIN_POSITION_SIZE`: Minimum position in USDC (default: `10`)
- `MAX_POSITION_SIZE`: Maximum position in USDC (default: `1000`)

## Support

For issues or questions:
- 📱 Telegram: [t.me/dexoryn_here](https://t.me/dexoryn_here)
- 🎮 Discord: `.dexoryn`
