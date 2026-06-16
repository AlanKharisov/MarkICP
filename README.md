# MarkICP Admin Company

Веб-кабинет для компаний/брендов экосистемы MarkICP: создание NFT (single / editions / коллекции), массовый выпуск, CRM (доставки, COD, NFC-метки), AI-генерация артов, профиль.

Стек: **Vite + React + TypeScript + Internet Computer (ICP / Plug Wallet) + Firebase Auth**.

## Архитектура минтинга

Минтинг перенесён на блокчейн **Internet Computer**:

- **Reverse Gas Model** — газ платит приложение (канистра), а не конечный пользователь.
- Для 10 000 NFT (~1 GB on-chain) стоимость составляет около **$7.44 в год** при собственной ICRC-7 канистре.
- Альтернатива — бесплатный минтинг на лаунчпадах [Yuku](https://yuku.app) или [Entrepot](https://entrepot.app/create).
- Фронтенд авторизуется через **Plug Wallet** и передаёт `Principal` бэкенду; сам минт выполняет Rust-бэкенд через ICRC-7 канистру.

## Запуск локально

```bash
npm install
cp .env.example .env.local
# отредактируй .env.local под себя
npm run dev
```

Откроется на `http://localhost:3002`.

## Переменные окружения

| Переменная                | Назначение                                                                  |
| ------------------------- | --------------------------------------------------------------------------- |
| `VITE_API_BASE_URL`       | Базовый URL Rust API (локально `http://localhost:8090`, прод `https://markicp-backend.example.com`). |
| `VITE_ICP_HOST`           | Хост ICP: `https://icp0.io` для mainnet или `http://127.0.0.1:4943` для локальной реплики `dfx`. |
| `VITE_ICP_NFT_CANISTER_ID`| ID канистры ICRC-7. Пустое до деплоя.                                       |

Firebase API key захардкожен в `src/firebase.ts` — это публичный ключ клиента, гарантии безопасности обеспечиваются authorized-domains в Firebase Console.

## Скрипты

| Скрипт            | Что делает                          |
| ----------------- | ----------------------------------- |
| `npm run dev`     | Dev сервер на :3002                 |
| `npm run build`   | Билд в `dist/` (tsc + vite build)   |
| `npm run preview` | Превью прод-билда                   |

## Деплой (Vercel)

1. Импортируй репозиторий в [Vercel](https://vercel.com/new).
2. Framework Preset: **Vite** (определится автоматически из `vercel.json`).
3. В **Environment Variables** задай `VITE_API_BASE_URL`, `VITE_ICP_HOST` и `VITE_ICP_NFT_CANISTER_ID`.
4. После деплоя добавь домен Vercel в [Firebase Console → Authentication → Settings → Authorized domains](https://console.firebase.google.com/), иначе Firebase Auth откажет.

## Бэкенд

Rust/Axum API + ICRC-7 канистра живут отдельно. Этот фронт их НЕ содержит — только клиент.

## Связанные приложения

- `identity/` — основное мобильное/веб-приложение пользователя
- `admin/` — внутренняя админка платформы
- `api/` — Rust бэкенд (общий для всех фронтов)
- `icrc7-canister/` — канистра ICRC-7 на Internet Computer
