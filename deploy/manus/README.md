# Skyvern deployment configuration

هذا المجلد يحتوي على إعدادات نشر آمنة قابلة لإعادة الاستخدام. المستودع نفسه هو كود Skyvern، بينما تُنشأ الأسرار على الخادم المستهدف ولا تُحفظ في Git.

## المتطلبات

- Python 3.11–3.13
- `uv`
- PostgreSQL
- Chromium من Playwright
- مفتاح لمزوّد LLM واحد على الأقل لتشغيل مهام الذكاء الاصطناعي

## تثبيت وتشغيل سريع

```bash
uv venv --python 3.11 .venv
uv pip install --python .venv/bin/python 'skyvern[server]==1.0.48'
.venv/bin/python -m playwright install chromium
cp deploy/manus/.env.example .env
# عدّل .env على الخادم فقط، ولا ترفع النسخة المعبأة.
.venv/bin/skyvern quickstart --non-interactive \
  --database-string "$DATABASE_STRING" \
  --skip-llm-setup --skip-mcp \
  --browser-type chromium-headless --analytics-id anonymous --no-start
.venv/bin/skyvern run server
```

## تشغيل MCP عبر HTTP

```bash
SKYVERN_MCP_PUBLIC_URL="https://YOUR_HOST/mcp" \
.venv/bin/skyvern run mcp \
  --transport streamable-http \
  --host 0.0.0.0 --port 8001 --path /mcp --stateless-http
```

يحتاج العميل إلى رابط `/mcp` وترويسة `x-api-key` بقيمة `SKYVERN_API_KEY`. لا تضع قيمة المفتاح داخل هذا المستودع أو في رابط عام.

## ملاحظات أمنية

- لا ترفع `.env` أو مفاتيح LLM أو كلمات مرور PostgreSQL أو ملفات الجلسات.
- استخدم مدير أسرار أو متغيرات بيئية على الخادم.
- حدّث `SKYVERN_API_KEY` وبيانات PostgreSQL بقيم جديدة عند كل نشر.
- هذا المستودع لا يجعل Skyvern خدمة دائمة؛ التشغيل الدائم يتطلب خادمًا أو منصة استضافة مستمرة.
