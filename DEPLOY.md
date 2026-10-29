# demokonto-mt5.de — Deploy Guide

## Структура файлов

```
demokonto-mt5.de/
├── index.html          # Главная страница (90 KB)
├── fonts/              # Локальные шрифты Manrope
│   ├── ManropeV5VF.woff2          # Variable font (primary)
│   ├── ManropeV5-Regular.woff2
│   ├── ManropeV5-Medium.woff2
│   ├── ManropeV5-SemiBold.woff2
│   ├── ManropeV5-Bold.woff2
│   ├── ManropeV5-ExtraBold.woff2
│   ├── ManropeV5-Light.woff2
│   └── ManropeV5-ExtraLight.woff2
├── robots.txt          # Allow all, Sitemap ссылка
├── sitemap.xml         # Один URL: https://demokonto-mt5.de/
├── netlify.toml        # Конфиг для Netlify
├── vercel.json         # Конфиг для Vercel
├── llms.txt            # AI-краулер мета
└── content.md          # Контент страницы в Markdown
```

---

## Вариант 1 — Netlify (рекомендован)

1. Зайти на [netlify.com](https://netlify.com) → **Sites → Add new site → Deploy manually**
2. Перетащить папку `demokonto-mt5.de/` в дроп-зону
3. После деплоя: **Site settings → Domain management → Add custom domain** → `demokonto-mt5.de`
4. Netlify автоматически выдаст SSL-сертификат

`netlify.toml` уже настроен:
```toml
[build]
  publish = "."

[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200
```

---

## Вариант 2 — Vercel

1. Установить Vercel CLI: `npm i -g vercel`
2. В папке проекта: `vercel --prod`
3. Привязать домен в дашборде: **Project → Settings → Domains**

`vercel.json` уже настроен с rewrites.

---

## Вариант 3 — любой статик-хостинг (Apache / Nginx / S3)

Загрузить содержимое папки в корень сайта. Убедиться что:
- `index.html` находится в корне
- Папка `fonts/` лежит рядом с `index.html` (не вложена глубже)
- MIME-тип для `.woff2` → `font/woff2`

**Nginx пример:**
```nginx
server {
    listen 443 ssl;
    server_name demokonto-mt5.de www.demokonto-mt5.de;
    root /var/www/demokonto-mt5.de;
    index index.html;

    location ~* \.woff2$ {
        add_header Cache-Control "public, max-age=31536000";
        add_header Access-Control-Allow-Origin "*";
    }

    location / {
        try_files $uri $uri/ /index.html;
    }
}
```

---

## DNS-настройки

| Тип  | Имя | Значение              |
|------|-----|-----------------------|
| A    | @   | IP вашего сервера     |
| CNAME| www | demokonto-mt5.de.     |

Для Netlify/Vercel DNS-записи указаны в их дашборде после добавления домена.

---

## Зависимости

| Зависимость | Источник | Статус |
|---|---|---|
| Manrope шрифт | `/fonts/` (локально) | ✓ включён |
| Three.js r128 | `cdnjs.cloudflare.com` | CDN (нужен интернет) |
| Google Fonts | — | ✗ удалён |

> **Важно:** Three.js загружается с CDN. Если нужен полностью офлайн-деплой — скачать `three.min.js` и положить рядом с `index.html`, заменив src в теге `<script>`.

---

## Технические характеристики

- **Размер:** ~90 KB HTML + ~340 KB шрифты
- **Шрифт:** Manrope Variable (локальный, без Google Fonts)
- **Интерактивные элементы:**
  - Three.js глобус (WebGL) с drag и hover-stop
  - Canvas 2D частицы (числа, свечи, графики)
  - Live candlestick чарт (EUR/USD) с тиками
  - Мини-чарт с OHLC
  - Переключатель таймфреймов (1M/5M/1H/4H/1D)
  - Калькулятор margin/pip
  - Sticky sidenav с scroll-highlight
  - Бегущая строка котировок
- **Канонический URL:** `https://demokonto-mt5.de/`
- **Язык:** Немецкий (de)
- **Schema.org:** Organization, WebPage, FAQPage

---

## Чеклист перед публикацией

- [ ] Проверить домен demokonto-mt5.de доступен и привязан
- [ ] SSL-сертификат активен (HTTPS)
- [ ] Открыть `robots.txt` — убедиться что Sitemap URL правильный
- [ ] Проверить в DevTools → Network что шрифты грузятся из `/fonts/` (не 404)
- [ ] Проверить Three.js глобус на мобильном (touch-вращение)
- [ ] Заменить все ссылки `libertex.com/platforms/metatrader5` на целевые партнёрские URL
