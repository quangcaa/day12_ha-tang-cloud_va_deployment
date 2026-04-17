# Lab 12 — Production AI Agent

Production-ready AI agent kết hợp tất cả concepts: Docker multi-stage, API security, rate limiting, cost guard, health checks, graceful shutdown.

## 🚀 Public URL

- **Railway:** https://06-production.up.railway.app

## 📁 Cấu Trúc

```
06-lab-complete/
├── app/
│   ├── __init__.py
│   ├── main.py           # Entry point — tất cả tính năng
│   ├── config.py          # 12-factor config (env vars)
│   ├── auth.py            # API Key authentication
│   ├── rate_limiter.py    # Sliding window rate limiting
│   └── cost_guard.py      # Daily budget protection
├── utils/
│   └── mock_llm.py        # Mock LLM responses
├── Dockerfile             # Multi-stage, non-root, < 500MB
├── docker-compose.yml     # Agent + Redis + Nginx (scale 3)
├── nginx.conf             # Load balancer config
├── railway.toml           # Railway deploy config
├── requirements.txt
├── .env.example           # Template biến môi trường
├── .env.local             # Local config (KHÔNG commit)
├── .dockerignore
└── check_production_ready.py  # Script kiểm tra 20/20
```

## ⚡ Chạy Local

### Cách 1: Docker Compose (3 agents + Redis + Nginx)

```bash
# 1. Copy file config
cp .env.example .env.local

# 2. Build và chạy
docker compose build --no-cache
docker compose up --scale agent=3

# 3. Test (port 80 qua Nginx)
curl http://localhost/health
curl http://localhost/ready
curl -X POST http://localhost/ask \
  -H "X-API-Key: dev-key-change-me-in-production" \
  -H "Content-Type: application/json" \
  -d '{"question": "Hello"}'
```

### Cách 2: PowerShell (Windows)

```powershell
# Health check
Invoke-RestMethod -Uri "http://localhost/health"

# Ready check
Invoke-RestMethod -Uri "http://localhost/ready"

# API call
Invoke-RestMethod -Uri "http://localhost/ask" -Method POST `
  -Headers @{"X-API-Key"="dev-key-change-me-in-production"; "Content-Type"="application/json"} `
  -Body '{"question": "Hello"}'
```

## ☁️ Deploy lên Railway

```bash
# 1. Cài Railway CLI
npm i -g @railway/cli

# 2. Login
railway login

# 3. Tạo project
railway init

# 4. Set biến môi trường
railway variables set PORT=8000
railway variables set ENVIRONMENT=production
railway variables set AGENT_API_KEY=your-secret-api-key
railway variables set JWT_SECRET=your-jwt-secret

# 5. Deploy
railway up

# 6. Lấy public URL
railway domain
```

## 🔒 Security Features

| Feature | Mô tả |
|---------|--------|
| **API Key Auth** | Header `X-API-Key` bắt buộc, 401 nếu thiếu |
| **Rate Limiting** | Sliding window, 20 req/min mặc định |
| **Cost Guard** | Giới hạn $5/ngày, block khi vượt budget |
| **Security Headers** | X-Content-Type-Options, X-Frame-Options |
| **Non-root Docker** | Container chạy user `agent`, không phải root |

## 🏥 Health & Operations

| Endpoint | Mô tả |
|----------|--------|
| `GET /health` | Liveness probe — platform restart nếu fail |
| `GET /ready` | Readiness probe — load balancer ngừng route nếu 503 |
| `GET /metrics` | Thống kê (cần API key) |
| `GET /docs` | Swagger UI (chỉ có ở dev mode) |

## ✅ Production Readiness

```
20/20 checks passed (100%)
🎉 PRODUCTION READY!
```
