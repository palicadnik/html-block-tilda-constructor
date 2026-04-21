# html-block-tilda-constructor

Конструктор постельного белья для [palicadnik.ru](https://palicadnik.ru) — одностраничный HTML-блок, встраиваемый в Tilda через элемент T123 (HTML-код).

Пользователь выбирает количество подушек и одеял, затем для каждого слота задаёт размер и цвет. На checkout всё корректно уходит в Tilda-корзину с `externalid` для синхронизации остатков с МойСклад через CommerceML.

## Стек

- Vanilla **HTML + CSS + JS** (ES6+), один файл — `page1.html` (~1570 строк).
- Никакого билда, бандлеров, пакетных менеджеров.
- Платформа: [Tilda](https://tilda.cc) (Zero Block + блок T123).
- Каталог товаров: REST-ручка на [Amvera](https://amvera.io) — `https://konstruktor-palicadnik.amvera.io/getProducts`.
- Остатки/заказы: Tilda ↔ МойСклад через **CommerceML** ([док Tilda](https://help-ru.tilda.cc/online-store-payments/moysklad)).

## Как это работает

1. **Верстка слотов** — живёт в Zero Block'ах Tilda (картинки кровати в разных конфигурациях: `grp21`, `grp22`, `grp41`, `grp42` — где цифры = подушки × одеяла).
2. **Логика конфигуратора** — `page1.html`, встраивается в тот же Zero Block через HTML-элемент T123.
3. **Сценарий пользователя:**
   - Страница грузится → дефолт `2 подушки + 1 одеяло` (`grp21`) показан сразу.
   - Клик на «+» у слота → показывается выбор размера.
   - Выбор размера → появляется палитра цветов, отфильтрованная по размеру.
   - Выбор цвета → картинка слота обновляется, цена/название пишутся в шаг 3.
   - «Далее» → сводка заказа → «Оформить» → все выбранные позиции летят в `tcart__addProduct` с `externalid` (привязка к модификации в МойСклад).

## Контракт API `/getProducts`

```json
{
  "pillows":    { "items": [ /* Item[] */ ] },
  "blankets":   { "items": [ /* Item[] */ ] },
  "mattresses": { "items": [ /* Item[] */ ] }
}
```

**Item:**
```json
{
  "id": "pillows4",
  "name": "ТОПЛЕНОЕ МОЛОКО",
  "sku": "НАВОЛОЧКА ТОПЛЕНОЕ МОЛОКО",
  "picture": "https://static.tildacdn.com/.../toplenka.png",
  "image": [
    { "kind": "1/2", "url": "..." },
    { "kind": "2/2", "url": "..." },
    { "kind": "1/4", "url": "..." },
    { "kind": "2/4", "url": "..." },
    { "kind": "3/4", "url": "..." },
    { "kind": "4/4", "url": "..." }
  ],
  "sizes": [
    { "size": "50х70 см", "price": 1500, "count": 29, "externalCode": "Kv6..." },
    { "size": "70х70 см", "price": 1600, "count": 19, "externalCode": "Kv7..." }
  ]
}
```

**Ключевые поля:**
- `image[].kind` — `N/M` (позиция/всего) — разные картинки для разных конфигураций кровати.
- `sizes[].externalCode` — идентификатор модификации в МойСклад; уходит в Tilda как `externalid` для корректного списания остатков. Fallback — по `sku`.
- `sizes[].count` — остаток, на фронте пока не используется.

При падении API (сетевая ошибка, HTTP ≥ 400) фронт подставляет пустой скелет `{ items: [] }` и показывает уведомление «Каталог товаров временно недоступен» — UI не ломается, но товары выбрать нельзя.

## Локальная разработка

```bash
# открыть напрямую в браузере (fetch до Amvera работает из file://, CORS настроен)
open page1.html

# или через локальный сервер — нагляднее для DevTools
python3 -m http.server 8080
# → http://localhost:8080/page1.html
```

Никаких npm/install — правь `page1.html`, обновляй страницу.

### Структура файла

Всё в одном `page1.html`:
- `<!-- layout container -->` — разметка конфигуратора (левый экран + правая панель).
- `<style>` — стили (включая CSS-классы `.grp21/22/41/42`, управляющие видимостью групп слотов).
- `<script>` — логика:
  - `window.proceedToCheckout` — отправка в Tilda-корзину.
  - `DOMContentLoaded` — fetch `/getProducts`, инициализация `currentSelections`.
  - `showProductCustomization` / `showSizeOptions` / `loadSwatches` — UI-состояния.
  - `selectProduct` / `updateProductImages` / `getImageUrlForElement` — смена картинок слотов.
  - `initCustomizer` — event-делегирование, клики по «+», back/next.

### Ключевое состояние

| Имя | Назначение |
|---|---|
| `products` | весь каталог из API |
| `currentSelections` | что показано на экране (дефолт + юзерский выбор) |
| `window.userSelections` | только те позиции, где юзер явно выбрал цвет — уходит в корзину |
| `window.beddingState.config` | текущая конфигурация (`"2-1" \| "2-2" \| "4-1" \| "4-2"`) |
| `currentCustomizing` | какой слот редактируется прямо сейчас |

## Интеграции

### Tilda Cart API

```js
tcart__addProduct({
  name, price, sku,
  img: product.picture,
  externalid: sizeObj.externalCode  // для МойСклад
});
tcart__open();  // открывает корзину Tilda
```

Tilda API открытый, но недокументированный — опираемся на практику и [обратную разработку](https://vc.ru/dev/1650163-sozdanie-nestandartnyh-modifikacii-s-pomoshyu-javascript-api-tilda).

### МойСклад ↔ Tilda

Связка работает на стороне Tilda через [CommerceML](https://help-ru.tilda.cc/online-store-payments/moysklad). Фронт никакого кода для этого не содержит — только передаёт `externalid` на позицию корзины. Матчинг делается при синке заказов в МойСклад: сначала по `externalid`, fallback на `sku`.

## Деплой

1. Внести правки в `page1.html` локально.
2. Скопировать весь файл.
3. В Tilda → страница конструктора → блок T123 «HTML-код» → вставить → Сохранить → Опубликовать.

Бэкенд `/getProducts` деплоится отдельно в Amvera (вне этого репозитория).

## Известные ограничения

- Единственный файл → сложно дробить и ревьюить; namespace `window.*` для состояния.
- Нет тестов — верификация ручная.
- Русский UI-текст захардкожен — i18n не заложен.
- Сетевой отказ API = каталог недоступен (fallback на пустой скелет, retry-логика не реализована).

## Файлы

```
page1.html               # всё: вёрстка, стили, логика конфигуратора
products-legacy.json     # снапшот хардкод-каталога до перехода на API (справочник схемы)
specs/001-code-cleanup/  # артефакты рефакторинга (spec-driven development через speckit)
.specify/, .claude/      # конфиги для spec-kit и Claude Code
CLAUDE.md                # контекст проекта для Claude
```
