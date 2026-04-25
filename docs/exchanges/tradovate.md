# Tradovate Setup Guide

Tradovate is a futures trading platform. It uses username/password authentication instead of API keys — you get a session token back that the bot uses for all requests.

---

## What You Need

- A Tradovate account (demo or live)
- Your Tradovate **username** and **password**

---

## How Authentication Works

Send a POST request with your credentials:

```bash
curl -X POST https://live.tradovateapi.com/v1/auth/accesstokenrequest \
     -H "Content-Type: application/json" \
     -H "Accept: application/json" \
     -d '{
          "name": "your username",
          "password": "your password",
          "appId": "Sample App",
          "appVersion": "1.0",
          "cid": 8,
          "deviceId": "123e4567-e89b-12d3-a456-426614174000",
          "sec": "f03741b6-f634-48d6-9308-c8fb871150c2"
         }'
```

**Response:**
```json
{
    "accessToken": "your trading token",
    "mdAccessToken": "your market data token",
    "expirationTime": "2021-06-15T15:40:30.056Z",
    "userStatus": "Active",
    "hasLive": true,
    "hasFunded": true,
    "hasMarketData": true
}
```

- `accessToken` — used to place and manage trades
- `mdAccessToken` — used to read market data
- Tokens expire and are refreshed automatically by the bot

---

## Endpoint

| Mode | URL |
|------|-----|
| Live | `https://live.tradovateapi.com/v1/auth/accesstokenrequest` |

> **Note:** Tradovate API access requires a live funded account. Demo accounts do not have API access.

---

## .env Configuration

```
TRADOVATE_USERNAME=your_username
TRADOVATE_PASSWORD=your_password
```

---

## Safety Rules

- Never share your password
- Tradovate does not have withdrawal permissions tied to API access — your funds are protected by your account login
