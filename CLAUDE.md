# 川口市議会 議事録AI検索くん — 引き継ぎメモ
松原市版の仕様（CLAUDE.md / DEPLOY_SPEC.md）を流用した川口市版。詳細は README.md と deploy/DEPLOY.md。
- tenant=kawaguchi, tenant_id=366 / 年度は src/config.py の START_YEAR, LATEST_YEAR で一元管理
- src/parser.py は括弧なし書式（◎奥ノ木信夫市長　本文）対応。役職語彙は _DEPT_WORDS
- 本番は松原市と同一VPS: port 8001, service gijiroku-kawaguchi, Nginx は reload（restart しない）
- GitHub: Maruyoshi-Takafumi-ux/kawaguchi-council-minutes-ai-search（松原版の health-gear とは別アカウント）
- Playwright は通常ブラウザUA必須（HeadlessChrome UAだと sorry ページへ転送）
