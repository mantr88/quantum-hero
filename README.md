# Apogee — Hero Section

Пікселево точна, повністю адаптивна hero-секція лендінгу під назвою **Apogee**: повноекранне відео на фоні, заголовок з описом і двома CTA-кнопками, скляна картка зі статистикою доходу (анімований бар-чарт) та навігація з мобільним меню.

## Стек

- **Vite 5** — збірка та dev-сервер
- **React 18.3** + **TypeScript 5.5**
- **Tailwind CSS 3.4** (PostCSS + Autoprefixer), без розширення теми — вся стилізація через arbitrary values (`text-[15.5px]` тощо)
- **lucide-react** — іконки `ChevronDown`, `Menu`, `X`

## Структура проєкту

```
index.html                 # шрифт Suisse Intl, заголовок сторінки
src/
  main.tsx                 # createRoot + StrictMode
  App.tsx                  # рендерить лише <Hero />
  index.css                # Tailwind-директиви + всі CSS keyframes анімацій
  components/
    Hero.tsx                # BAR_HEIGHTS, Animate, RevenueCard, Hero (default export), Nav
vite.config.ts             # аліас @/* → src/*
tailwind.config.js
postcss.config.js
tsconfig.json / .app.json / .node.json
eslint.config.js
```

## Запуск

```bash
npm install
npm run dev        # dev-сервер на http://localhost:5173
```

Інші команди:

```bash
npm run build       # продакшн-збірка у dist/
npm run preview     # локальний перегляд зібраного dist/
npm run typecheck   # tsc --noEmit
npm run lint        # ESLint
```

## Особливості реалізації

- **Фонове відео** (`<video autoPlay loop muted playsInline>`) із CloudFront, без градієнтного оверлею — колір секції `#080A19` проглядає лише під час завантаження.
- **Вхідні анімації** — виключно CSS `@keyframes` (`fade-up/down/left/right/scale`, `bar-grow`) з `animation-fill-mode: forwards` та easing `cubic-bezier(0.16, 1, 0.3, 1)`. Немає ні IntersectionObserver, ні анімаційних бібліотек — черговість керується `animationDelay` в inline-стилях компонента `Animate`.
- **RevenueCard** — бар-чарт із 32 стовпців (`BAR_HEIGHTS`), де останні 4 позначені як "прогнозні" (тьмяніші, `rgba(255,255,255,0.1)`), з вертикальними напрямними лініями та шкалою часу знизу (останній підпис `16:00` навмисно продубльований).
- **Nav** — на `≥lg` показує центральну скляну пілюлю навігації та праву пілюлю з Login/Book a demo; на менших екранах — кнопка-гамбургер, що відкриває повноекранне меню. Оверлей меню **ніколи не демонтується** (лише перемикається `visible/invisible` і opacity/transform), тому анімація закриття теж програється. Іконки Menu/X накладені одна на одну й крос-фейдяться. При відкритому меню скрол `body` блокується через `useEffect`.
- Усі брейкпоінти — стокові Tailwind (`sm`=640px, `md`=768px, `lg`=1024px); ключові adaptive-значення (`px-[82px]`, `max-w-[1800px]`, `max-w-[593px]`, `max-w-[405px]` тощо) заведені як arbitrary values.

## Кольори

| Токен | Значення | Де використовується |
|---|---|---|
| Фон секції | `#080A19` | `<section>`, мобільний бекдроп (90%) |
| Світла кнопка | `#E9E9E9` / текст `#0A0707` | "Book a demo" (усі місця) |
| Скло навігаційної пілюлі | `rgba(10,7,7,0.35)` | центральна пілюля, гамбургер |
| Скло авторизаційної пілюлі | `rgba(0,0,0,0.35)` | права пілюля Login/Book a demo |
| Скло картки / панелі | `rgba(17,16,15,0.35)` картка · `rgba(17,16,15,0.6)` мобільна панель | RevenueCard, мобільне меню |
| Активний/прогнозний бар | `white` / `rgba(255,255,255,0.1)` | бар-чарт |

Backdrop-blur: `17px` (пілюлі) · `20px` (картка) · `24px` (мобільний бекдроп) · `30px` (мобільна панель).
