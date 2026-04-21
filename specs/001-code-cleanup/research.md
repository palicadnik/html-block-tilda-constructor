# Research: Очистка кода конструктора

**Date**: 2026-03-28
**Feature**: 001-code-cleanup

## Анализ мёртвого кода

### Decision: Безопасность удаления функций

**Rationale**: Проведён полный анализ вызовов каждой функции в `page1.html`. Функции, привязанные к `window.*`, считаются потенциально доступными из Tilda-контекста. Локальные функции без внешних вызовов безопасны для удаления.

**Findings**:

| Функция/переменная | Тип | Вызывается | Решение |
|---------------------|-----|------------|---------|
| `getSizesArray()` | локальная | нигде | Удалить |
| `showPalette()` | локальная | нигде | Удалить |
| `updateAllImagesForConfig()` | локальная | нигде | Удалить |
| `loadFromLocalStorage()` | локальная | нигде | Удалить |
| `saveToLocalStorage()` | локальная | 2 вызова | Удалить (данные не читаются) |
| `currentCategory` | переменная | нигде | Удалить |
| `notification` | переменная | в `showNotification` | Исправить (добавить HTML) |
| Комментированный `sizes` | комментарий | N/A | Удалить |

**Alternatives considered**: Оставить `saveToLocalStorage` для будущего — отклонено: неиспользуемый код противоречит цели очистки.

### Decision: Обработка `applyToAllPillows` / `applyToAllBlankets`

**Rationale**: Условия `window.applyToAllPillows` и `window.applyToAllBlankets` проверяются в обработчике кнопок размера, но нигде не устанавливаются. Это нереализованная фича.

**Decision**: Удалить блоки `if` с этими условиями.

**Alternatives considered**: Оставить как заглушку для будущей фичи — отклонено: если фича понадобится, её лучше реализовать целиком.

### Decision: Стратегия удаления `console.log`

**Rationale**: Код содержит ~50 вызовов `console.log` для отладки. В продакшене они засоряют консоль.

**Decision**: Удалить все `console.log` кроме критичных. Сохранить все `console.error` и `console.warn`.

**Alternatives considered**: Заменить на условное логирование (`if (DEBUG)`) — отклонено: избыточная сложность для одного файла без сборщика.

### Decision: Схема уникальных ID для дублирующихся товаров

**Rationale**: 5 товаров prints-наволочек имеют `id: "pillows14"`, 2 товара mattresses имеют `id: "mattresses9"`. Нужна схема присвоения уникальных ID.

**Decision**: Использовать схему `{category}{sequence}`, где sequence — порядковый номер, продолжающий существующую нумерацию:
- Текущие уникальные ID наволочек: pillows2, pillows4, pillows6, pillows8, pillows10, pillows12, pillows14, pillows16, pillows18, pillows20, pillows22, pillows24, pillows25, pillows26, pillows27, pillows28, pillows29
- Свободные номера для prints-дубликатов (ЛЕЙКА, ЛУГ, РАСКРАСКА, ТИХИЙ ЧАС, ТОЧКА ЗАПЯТАЯ): pillows30, pillows31, pillows32, pillows33, pillows34
- Для mattresses НЕБО (дубликат mattresses9): mattresses10

**Alternatives considered**: Использовать UUID — отклонено: нарушает существующую схему именования.

### Decision: Добавление HTML-элемента notification

**Rationale**: `showNotification` вызывается 8+ раз для обратной связи с пользователем, но элемент `#notification` отсутствует в HTML.

**Decision**: Добавить `<div id="notification" class="notification"></div>` внутри `.customizer-container`, перед закрывающим `</div>`. CSS-стили для `.notification` уже существуют в коде.

**Alternatives considered**: Использовать `alert()` — отклонено: плохой UX, блокирует взаимодействие.
