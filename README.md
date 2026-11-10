# template-docker

Docker Compose stack of the template application, deployed by Dokploy from this repository.

| Service    | Image                                     | Container port |
| ---------- | ----------------------------------------- | -------------- |
| `postgres` | `postgres:17-alpine`                      | 5432 (internal) |
| `api`      | `ghcr.io/tiogars/template-java-api`       | 8080           |
| `backend`  | `ghcr.io/tiogars/template-react-backend`  | 8080           |

## Dokploy

1. Create a **Docker Compose** service: provider GitHub, repository `tiogars/template-docker`,
   branch `main`, compose path `./docker-compose.yml`.
2. **Environment**: copy `.env.example` and replace every `change-me` and example URL.
   `POSTGRES_PASSWORD`, `APP_ADMIN_PASSWORD`, `APP_JWT_SECRET`, `APP_CORS_ALLOWED_ORIGINS`
   and `API_BASE_URL` are required.
3. **Domains**: `backend` → port `8080` (console), `api` → port `8080` (API). `API_BASE_URL`
   must be the API domain and `APP_CORS_ALLOWED_ORIGINS` the console domain.
4. Deploy. GHCR images must be public, or GHCR credentials must be registered in Dokploy.
   The images are pulled again on every deployment (`pull_policy: always`), so a redeploy picks up
   the latest `latest` image; pin `API_IMAGE_TAG` / `BACKEND_IMAGE_TAG` to `sha-<commit>` or
   `v<version>` for a reproducible release.

On the first start, PostgreSQL runs `postgres/initdb/01-task-domains.sql` (copy of
`template-java-api/src/main/resources/db/manual/task-domains.sql`; keep both in sync).

## Local test

```bash
cp .env.example .env
docker compose -f docker-compose.yml -f compose.local.yml up -d --wait
```

Console: <http://localhost:5173> (`admin` / `change-me`), API: <http://localhost:8989/swagger-ui.html>.

See the [deployment guide](https://github.com/tiogars/template-docs/blob/main/src/deployment.md).