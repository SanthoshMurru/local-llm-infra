# Combined Docker Compose Stack (Infra + Open WebUI)

This repo now includes a single compose file that combines:
- Existing infra stack (`clickhouse`, `ollama`, `litellm`, `postgres`, `librechat`, `mongodb`, `meilisearch`, `mcp-clickhouse`)
- Open WebUI stack (`open-webui`, `openwebui-postgres`, `openwebui-redis`)

## Files
- `docker-compose.combined.yml`
- `openwebui/.env` (Open WebUI runtime env)
- `litellm-config.yaml`
- `librechat.yaml`

## Start everything
```bash
docker compose -f docker-compose.combined.yml up -d
```

## Stop everything
```bash
docker compose -f docker-compose.combined.yml down
```

## Key service endpoints
- LibreChat: `http://localhost:3080`
- LiteLLM: `http://localhost:4000`
- Ollama API: `http://localhost:11434`
- Open WebUI: `http://localhost:8085`
- ClickHouse HTTP: `http://localhost:8123`
- MCP ClickHouse: `http://localhost:8001`

## Required Open WebUI env alignment
Current Open WebUI port mapping is `8085:8080` in `docker-compose.combined.yml`.

Update `openwebui/.env` to match your public URL/origin. For local access:
```env
WEBUI_URL=http://localhost:8085
CORS_ALLOW_ORIGIN=http://localhost:8085
OPENAI_API_BASE_URL=http://litellm:4000/v1
OPENAI_API_KEY=
DATABASE_URL=
```

## Persistence locations
- ClickHouse: `./ch_data`, `./ch_logs`
- MongoDB: `./mongo_data`
- Open WebUI app data: `./openwebui/openwebui_data`
- Open WebUI Postgres data: `./openwebui/openwebui_postgres_data`
- Open WebUI Redis data: `./openwebui/openwebui_redis_data`
- LiteLLM Postgres data: Docker volume `postgres_data`
- Ollama models: Docker volume `ollama_storage`

## Health checks and verification
```bash
docker compose -f docker-compose.combined.yml ps
docker compose -f docker-compose.combined.yml logs --tail=100 open-webui
curl -I http://localhost:8085
```

## Notes
- This combined file is additive; your existing `docker-compose.yml` and `openwebui/docker-compose.yml` are untouched.
- If Open WebUI shows permission/CORS issues, mismatched `WEBUI_URL` and `CORS_ALLOW_ORIGIN` is the first thing to fix.
