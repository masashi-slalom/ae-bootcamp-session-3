# Product Requirements Document (PRD) - Todo App Upgrade

## 1. Overview

Upgrade the basic Todo app, which currently supports task titles and completion status, with due dates, priorities, and date-based filters. The MVP should make tasks easier to organize while remaining simple and teachable, using local storage only and requiring no backend changes.

---

## 2. MVP Scope

- Keep task titles required.
- Add an optional `dueDate` field using the ISO `YYYY-MM-DD` format.
  - Treat an invalid due-date value as absent.
- Add a `priority` field with the values `P1`, `P2`, and `P3`.
  - Default new tasks to `P3`.
- Provide date-based filter tabs:
  - **All**: show completed and incomplete tasks.
  - **Today**: show only incomplete tasks due today.
  - **Overdue**: show only incomplete tasks whose due date is before today.
- Store task data locally. Do not introduce backend or external-storage changes.

---

## 3. Post-MVP Scope

- Visually highlight overdue tasks.
- Sort tasks in this order:
  1. Overdue tasks first.
  2. Priority from `P1` through `P3`.
  3. Due date in ascending order.
  4. Tasks without a due date last.
- Display color-coded priority badges:
  - `P1`: red.
  - `P2`: orange.
  - `P3`: gray.

---

## 4. Out of Scope

- Notifications.
- Recurring tasks.
- Multi-user support.
- Keyboard navigation and additional accessibility features.
- Backend changes or external storage.