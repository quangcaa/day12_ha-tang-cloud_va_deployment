# Deployment Information

## Public URL
https://06-production.up.railway.app

## Platform
Railway

## Test Commands

### Health Check
```powershell
Invoke-RestMethod -Uri "https://06-production.up.railway.app/health"
```

### API Test (with authentication)
```powershell
Invoke-RestMethod -Uri "https://06-production.up.railway.app/ask" -Method POST `
  -Headers @{"X-API-Key"="dzcojsai"; "Content-Type"="application/json"} `
  -Body '{"question": "Hello"}'
```

## Environment Variables Set
- PORT=8000
- ENVIRONMENT=production
- AGENT_API_KEY=(set via Railway)
- JWT_SECRET=(set via Railway)
- REDIS_URL=(set via Railway)
- DAILY_BUDGET_USD=5.0
- RATE_LIMIT_PER_MINUTE=20
