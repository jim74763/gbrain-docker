# gbrain-docker

Personal self-hosted deployment of [GBrain](https://github.com/garrytan/gbrain) — a
personal knowledge brain / memory server for AI agents — built and run as a Docker
container. This repo is a fork; for the full project (what GBrain is, how it works,
all features) see the **[official repo](https://github.com/garrytan/gbrain)**.

## Quick start

```bash
cp .env.testing.example .env   # or create .env yourself
# set GBRAIN_ADMIN_BOOTSTRAP_TOKEN=<a random secret> in .env
docker compose up -d --build
```

Admin dashboard: `http://localhost:3131/admin` (log in with your bootstrap token).

## Model provider

Defaults to [OpenRouter](https://openrouter.ai) — one API key routes both chat and
embeddings to Anthropic/OpenAI/Google/etc. Add to `.env`:

```
OPENROUTER_API_KEY=<your key>
```

- Chat/reasoning model (`GBRAIN_MODEL` in `docker-compose.yml`) takes effect on
  container restart — no reinit needed.
- Embedding model (`GBRAIN_INIT_ARGS`) is only applied on a brand-new brain
  (embedding dimensions are baked into the schema at `gbrain init` time).
  Switching an existing brain's embedding provider needs a destructive
  `gbrain init --force` re-embed.

To use Anthropic/OpenAI directly instead, see the commented-out keys in
`docker-compose.yml`.

## Data

Brain data (PGLite database, tokens, logs) lives on the host at `./gbrain_data/`
(bind-mounted, gitignored). Back that directory up to keep your data.

## Notes

- Container runs as a non-root user (uid 10001). If `./gbrain_data` gets recreated,
  re-run `sudo chown -R 10001:10001 gbrain_data`.
- Exposing this beyond localhost (e.g. over Tailscale) may need a firewall rule for
  Docker's published port — see your firewall's docs if the container is unreachable
  from other devices despite `docker ps` showing it's up.

## License

Same as upstream — see [garrytan/gbrain](https://github.com/garrytan/gbrain) for
license and full documentation.
