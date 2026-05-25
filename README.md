
# Простое фронтенд-приложение (сборка статики)

Минимальный фронтенд-проект на Node.js, который собирается в статику и отображает текст:

> **Виртуализация и контейнеризация**

## Требования

- Node.js 18+ (рекомендуется 20+)
- npm 9+

## Установка

```bash
npm install
````

## Запуск в режиме разработки

```bash
npm run dev
```

Открой в браузере: `http://localhost:5173`

## Сборка статики

```bash
npm run build
```

Результат сборки появится в папке `dist/`.

## Локальная проверка собранной статики

```bash
npm run preview
```

Открой в браузере: `http://localhost:4173`

## Структура проекта

* `index.html` — точка входа
* `src/main.js` — логика приложения
* `src/style.css` — стили
* `dist/` — результат сборки (генерируется)

---

## Запуск в Docker (ДЗ-2: «Основы контейнеризации»)

Проект содержит `Dockerfile` (multi-stage build) и `.dockerignore`.

### Сборка образа

```bash
docker build -t ruzaliasib/frontend:1.0.0 -t ruzaliasib/frontend:latest .
```

### Запуск контейнера

```bash
docker run --rm -p 8080:80 ruzaliasib/frontend:1.0.0
```

Открой в браузере: `http://localhost:8080` или проверь через `curl`:

```bash
curl -i http://localhost:8080
```

### Размер образа

```bash
docker images ruzaliasib/frontend
```

Получено:

| TAG    | DISK USAGE | CONTENT SIZE |
|--------|------------|--------------|
| 1.0.0  | 75.9 MB    | 21.8 MB      |
| latest | 75.9 MB    | 21.8 MB      |

### Что внутри Dockerfile

- **Stage 1 (`builder`)** — `node:20-alpine`: ставит зависимости через `npm ci`
  и собирает статику командой `npm run build`.
- **Stage 2 (`runtime`)** — `nginx:1.27-alpine`: получает только содержимое
  `dist/` и раздаёт его на порту 80. Node.js и `node_modules` в финальный
  образ не попадают.

Best practices, использованные в Dockerfile:
- multi-stage build → лёгкий runtime-образ;
- сначала копируются `package.json` / `package-lock.json` → потом `npm ci` →
  только потом исходники (корректное кэширование слоёв);
- `npm ci` вместо `npm install` (детерминированная сборка);
- фиксированные теги базовых образов (`node:20-alpine`, `nginx:1.27-alpine`);
- открыт только нужный порт (`EXPOSE 80`);
- контекст сборки минимизирован через `.dockerignore`.

