# 川口市版 VPSデプロイ手順（松原市と同一VPSに同居）

松原市は port 8000 / `gijiroku` で稼働中のため、川口市は **port 8001 / `gijiroku-kawaguchi`** を使う。

0. **事前確認**: `bash deploy/vps_check.sh`（読み取りのみ）→ 出力を共有
1. **DNS**（ムームードメイン）: ホスト名 `kawaguchi` / A / VPSのIP（松原と同じ）
2. **配置**
   ```bash
   git clone https://github.com/Maruyoshi-Takafumi-ux/kawaguchi-council-minutes-ai-search.git /opt/kawaguchi-council-minutes-ai-search
   cd /opt/kawaguchi-council-minutes-ai-search
   python3 -m venv venv && source venv/bin/activate
   pip install -r requirements.txt && mkdir -p data
   cp .env.example .env && nano .env   # GEMINI_API_KEY を設定（チャットに貼らない）
   ```
3. **DB**: ローカルで検証済みの `data/gikai.db` を `scp` で転送するか、VPSで `python collect_all_years.py && python summarize.py --parse`
4. **サービス**
   ```bash
   cp deploy/gijiroku-kawaguchi.service /etc/systemd/system/
   systemctl daemon-reload && systemctl enable --now gijiroku-kawaguchi
   curl -s http://127.0.0.1:8001/api/years
   ```
5. **Nginx / SSL**（DNS反映後）
   ```bash
   cp deploy/nginx-kawaguchi.conf /etc/nginx/sites-available/kawaguchi
   ln -s /etc/nginx/sites-available/kawaguchi /etc/nginx/sites-enabled/
   nginx -t && systemctl reload nginx
   certbot --nginx -d kawaguchi.council-minutes-ai-search.jp
   ```
   ※ `reload` を使う（`restart` だと松原市が一瞬止まる）。
6. **LLMO**: Search Console にプロパティ追加 → `google-site-verification` を `<title>` 直後に挿入 → 再起動
