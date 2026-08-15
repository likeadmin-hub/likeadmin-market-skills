# 算力超市skills

为兼容 Agent Skills 的 AI Agent 提供算力超市模型与应用 API 的智能编排能力。技能会先发现当前租户可用的模型和应用，再选择兼容的工作流；本地媒体会在确认后通过上传接口转换为可提交的 URL。

## Install

```bash
npx skills add likeadmin-hub/likeadmin-market-skills
```

OpenClaw users can install the same skill with:

```bash
npx clawhub@latest install likeadmin-market-skills
```

## Configure

Provide your tenant API origin and API key, then import them into the local credential store:

```bash
export LIKEADMIN_MARKET_BASE_URL="https://your-tenant-api-origin"
export LIKEADMIN_MARKET_API_KEY="your-api-key"
printf '%s\n%s\n' "$LIKEADMIN_MARKET_BASE_URL" "$LIKEADMIN_MARKET_API_KEY" | node scripts/api-client.mjs config import-lines
unset LIKEADMIN_MARKET_BASE_URL LIKEADMIN_MARKET_API_KEY
node scripts/api-client.mjs config status
```

Credentials are stored only in `~/.likeadmin-market-skills/credentials.json` with restrictive permissions. The skill never hardcodes a tenant URL or API key.

See [SKILL.md](SKILL.md) for workflow selection, supported orchestration recipes, and media-upload safeguards.
