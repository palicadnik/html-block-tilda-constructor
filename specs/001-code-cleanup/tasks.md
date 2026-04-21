# Tasks: Очистка кода конструктора постельного белья

**Input**: Design documents from `/specs/001-code-cleanup/`
**Prerequisites**: plan.md (required), spec.md (required), research.md, data-model.md

**Tests**: Нет автоматизированных тестов. Проверка — ручное тестирование в браузере по quickstart.md.

**Organization**: Задачи сгруппированы по user stories для независимой реализации и тестирования.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2, US3)
- Include exact file paths in descriptions

## Path Conventions

- Единственный файл: `page1.html` в корне репозитория

---

## Phase 1: Setup

**Purpose**: Нет setup-задач — проект уже инициализирован, работа ведётся в единственном файле.

**Checkpoint**: Переходим к удалению мёртвого кода.

---

## Phase 2: User Story 1 — Удаление мёртвого кода (Priority: P1) 🎯 MVP

**Goal**: Убрать неиспользуемые функции, переменные и закомментированный код из `page1.html`

**Independent Test**: Открыть конфигуратор в браузере, пройти полный путь (конфигурация → элемент → размер → цвет → заказ → корзина) для конфигураций 2/1 и 4/2. Всё должно работать идентично.

### Implementation for User Story 1

- [x] T001 [US1] Удалить закомментированный объект `sizes` (~строки 2532-2537) в `page1.html`
- [x] T002 [US1] Удалить функцию `getSizesArray()` (~строки 2858-2871) в `page1.html`
- [x] T003 [P] [US1] Удалить функцию `showPalette()` (~строки 3222-3225) в `page1.html`
- [x] T004 [P] [US1] Удалить функцию `updateAllImagesForConfig()` (~строки 2641-2654) в `page1.html`
- [x] T005 [US1] Удалить функции `loadFromLocalStorage()` и `saveToLocalStorage()` и все вызовы `saveToLocalStorage()` (2 вызова) в `page1.html`
- [x] T006 [P] [US1] Удалить переменную `currentCategory` (~строка 2854) в `page1.html`
- [x] T007 [US1] Удалить блоки `if (window.applyToAllPillows)` и `if (window.applyToAllBlankets)` в обработчике размеров (~строки 3129-3142) в `page1.html`
- [x] T008 [US1] Удалить переменную `notification` (~строка 2852) и обновить `showNotification` для использования `document.getElementById('notification')` напрямую в `page1.html`
- [x] T009 [US1] Добавить `<div id="notification" class="notification"></div>` внутри `.customizer-container` перед закрывающим `</div>` в `page1.html`

**Checkpoint**: Мёртвый код удалён. Уведомления работают. Конфигуратор полностью функционален.

---

## Phase 3: User Story 2 — Удаление избыточных debug-логов (Priority: P2)

**Goal**: Убрать отладочные `console.log` вызовы, сохранив `console.error` и `console.warn`

**Independent Test**: Открыть конфигуратор, выполнить полный путь, проверить что консоль браузера чиста от отладочных сообщений. `console.error` для реальных ошибок должен работать.

### Implementation for User Story 2

- [x] T010 [US2] Удалить дублированный `console.log('Start Script 3')` (оставить один экземпляр или удалить оба) в `page1.html`
- [x] T011 [US2] Удалить все отладочные `console.log` вызовы из функций `switchConfig`, `updateAllImagesForConfig`, `initConfiguration`, `updateConfiguration` в `page1.html`
- [x] T012 [US2] Удалить все отладочные `console.log` вызовы из функций `initCustomizer`, `showProductCustomization`, `showSizeOptions`, `loadSwatches` в `page1.html`
- [x] T013 [US2] Удалить все отладочные `console.log` вызовы из функций `selectProduct`, `getKindForElement`, `getImageUrlForElement`, `updateImagesForConfig`, `updateSingleImage` в `page1.html`
- [x] T014 [US2] Удалить отладочные `console.log` из функций `proceedToCheckout`, `resetToInitial`, `initializeImages`, `updateElementVisibility`, обработчиков back/click в `page1.html`

**Checkpoint**: Консоль браузера чиста. `console.error` и `console.warn` сохранены.

---

## Phase 4: User Story 3 — Исправление дублирующихся ID товаров (Priority: P3)

**Goal**: Присвоить уникальные ID всем товарам с дублирующимися идентификаторами

**Independent Test**: В консоли браузера выполнить проверку уникальности ID (см. quickstart.md). Должен вернуть пустой массив дубликатов. Выбор каждого из исправленных товаров должен корректно добавлять его в корзину.

### Implementation for User Story 3

- [x] T015 [US3] Исправить ID товара ЛЕЙКА: `"pillows14"` → `"pillows30"` в `products.pillows.prints` в `page1.html`
- [x] T016 [P] [US3] Исправить ID товара ЛУГ: `"pillows14"` → `"pillows31"` в `products.pillows.prints` в `page1.html`
- [x] T017 [P] [US3] Исправить ID товара РАСКРАСКА: `"pillows14"` → `"pillows32"` в `products.pillows.prints` в `page1.html`
- [x] T018 [P] [US3] Исправить ID товара ТИХИЙ ЧАС: `"pillows14"` → `"pillows33"` в `products.pillows.prints` в `page1.html`
- [x] T019 [P] [US3] Исправить ID товара ТОЧКА ЗАПЯТАЯ: `"pillows14"` → `"pillows34"` в `products.pillows.prints` в `page1.html`
- [x] T020 [US3] Исправить ID товара НЕБО (mattresses): `"mattresses9"` → `"mattresses10"` в `products.mattresses.solids` в `page1.html`

**Checkpoint**: Все ID товаров уникальны. Каждый товар идентифицируется однозначно.

---

## Phase 5: Финальная проверка

**Purpose**: Верификация всех изменений

- [x] T021 Выполнить полный ручной тест по quickstart.md для конфигурации 2 подушки / 1 одеяло в `page1.html`
- [x] T022 Выполнить полный ручной тест по quickstart.md для конфигурации 4 подушки / 2 одеяла в `page1.html`
- [x] T023 Проверить уникальность ID через console-скрипт из quickstart.md в `page1.html`

---

## Dependencies & Execution Order

### Phase Dependencies

- **User Story 1 (Phase 2)**: Нет зависимостей — можно начинать сразу
- **User Story 2 (Phase 3)**: Рекомендуется после US1 (чтобы не удалять логи из функций, которые сами будут удалены)
- **User Story 3 (Phase 4)**: Нет зависимостей на US1/US2 — можно выполнять параллельно
- **Финальная проверка (Phase 5)**: После завершения всех user stories

### Within Each User Story

- T001-T009 (US1): T001-T004, T006 можно параллельно; T005 (localStorage) и T007 (applyToAll) независимы; T008-T009 (notification) — последовательно
- T010-T014 (US2): T010 первым (дубль), затем T011-T014 параллельно по группам функций
- T015-T020 (US3): T015-T019 параллельно (все в разных записях products), T020 параллельно с остальными

### Parallel Opportunities

```bash
# US1 — параллельные удаления:
T001, T003, T004, T006  # Независимые удаления в разных местах файла

# US2 — параллельные удаления логов по группам функций:
T011, T012, T013, T014  # Разные функции, не пересекаются

# US3 — параллельные исправления ID:
T015, T016, T017, T018, T019, T020  # Разные записи в products
```

---

## Implementation Strategy

### MVP First (User Story 1 Only)

1. Complete Phase 2: Удаление мёртвого кода + добавление notification
2. **STOP and VALIDATE**: Тест по quickstart.md
3. Конфигуратор работает, уведомления отображаются

### Incremental Delivery

1. US1: Удаление мёртвого кода → Тест → Commit
2. US2: Удаление debug-логов → Тест → Commit
3. US3: Исправление ID → Тест → Commit
4. Финальная проверка → Commit

---

## Notes

- Все задачи в одном файле `page1.html` — строго последовательное сохранение
- [P] задачи можно выполнять параллельно в смысле анализа, но записываются в файл последовательно
- Номера строк в задачах приблизительны — сверяться с текущим содержимым файла
- После каждой user story — ручной тест в браузере
