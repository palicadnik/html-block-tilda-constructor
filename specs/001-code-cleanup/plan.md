# Implementation Plan: Очистка кода конструктора

**Branch**: `001-code-cleanup` | **Date**: 2026-03-28 | **Spec**: [spec.md](./spec.md)
**Input**: Feature specification from `/specs/001-code-cleanup/spec.md`

## Summary

Удаление мёртвого кода, избыточных debug-логов, исправление дублирующихся ID товаров и добавление отсутствующего HTML-элемента для уведомлений в `page1.html`. Чисто рефакторинговая задача — никаких новых функций, только очистка и исправление багов.

## Technical Context

**Language/Version**: Vanilla HTML/CSS/JavaScript (ES6+)
**Primary Dependencies**: Нет (Tilda platform APIs используются через глобальные функции)
**Storage**: N/A (localStorage удаляется в рамках этой задачи)
**Testing**: Ручное тестирование в браузере (нет автоматизированных тестов)
**Target Platform**: Современные браузеры (Chrome, Safari, Firefox, Edge)
**Project Type**: Встраиваемый HTML-блок для Tilda
**Performance Goals**: N/A (cleanup, не оптимизация)
**Constraints**: Единственный файл `page1.html`, без сборщиков
**Scale/Scope**: ~3800 строк, один файл

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

| Principle | Status | Notes |
|-----------|--------|-------|
| I. Single-File Embedding | PASS | Все изменения в `page1.html`, без новых файлов |
| II. Tilda Platform Compatibility | PASS | CSS классы и Tilda API не затрагиваются |
| III. Data-Driven Product Configuration | PASS | Структура `products` сохраняется, только ID исправляются |
| IV. Multi-Step Wizard Integrity | PASS | Шаги 0-4 не меняются, навигация сохраняется |
| V. Simplicity and Vanilla JS | PASS | Код становится проще, никаких новых абстракций |

## Project Structure

### Documentation (this feature)

```text
specs/001-code-cleanup/
├── plan.md              # This file
├── research.md          # Phase 0 output
├── spec.md              # Feature specification
└── checklists/
    └── requirements.md  # Quality checklist
```

### Source Code (repository root)

```text
page1.html               # Единственный файл, все изменения здесь
```

**Structure Decision**: Проект состоит из одного файла `page1.html`. Все изменения производятся в этом файле. Никаких новых файлов или директорий не создаётся.

## Complexity Tracking

> Нет нарушений конституции — секция не заполняется.
