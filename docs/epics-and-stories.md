# Todo App Upgrade Epics and Stories

Based on the MVP and Post-MVP requirements in `docs/prd-todo.md`. Technical
requirements reference the current frontend and backend implementation.

## MVP

- Epic: Task Due Dates
  - Story: Add optional due dates to tasks
    - Acceptance Criteria:
      - A task can be created or updated with no due date.
      - A due date is stored using the `YYYY-MM-DD` format when provided.
    - Technical Requirements:
      - Update `packages/frontend/src/TaskForm.js` to manage a `dueDate` value for both create and edit flows.
      - Include `dueDate` in the task object passed from `TaskForm` to `App` only when a valid date is supplied.
  - Story: Ignore invalid due-date values
    - Acceptance Criteria:
      - A due-date value that is not a valid `YYYY-MM-DD` date is treated as absent.
      - A task with an invalid due-date value remains usable without a due date.
    - Technical Requirements:
      - Add a shared frontend date-validation helper that verifies both the `YYYY-MM-DD` format and a real calendar date.
      - Normalize invalid due dates to `null` before storing or rendering a task.

- Epic: Task Priorities
  - Story: Add P1, P2, and P3 task priorities
    - Acceptance Criteria:
      - A task priority can be set to `P1`, `P2`, or `P3`.
    - Technical Requirements:
      - Add a priority selector to `packages/frontend/src/TaskForm.js` with only `P1`, `P2`, and `P3` options.
      - Represent priority in the frontend task model as `priority: 'P1' | 'P2' | 'P3'`.
  - Story: Default new tasks to P3 priority
    - Acceptance Criteria:
      - A newly created task has priority `P3` when no priority is specified.
    - Technical Requirements:
      - Initialize the new-task priority state in `TaskForm` to `P3` and reset it to `P3` after a successful submission.
      - Apply `P3` when loading a stored task with no priority to support existing task data.

- Epic: Date-Based Task Filters
  - Story: Show all tasks in the All filter
    - Acceptance Criteria:
      - The All filter shows both completed and incomplete tasks.
    - Technical Requirements:
      - Add All, Today, and Overdue filter controls and selected-filter state to `packages/frontend/src/TaskList.js`.
      - Implement the All filter as the unfiltered task collection, without excluding completed tasks.
  - Story: Show incomplete tasks due today
    - Acceptance Criteria:
      - The Today filter shows incomplete tasks with a due date equal to the current date.
      - The Today filter excludes completed tasks.
    - Technical Requirements:
      - Filter the `TaskList` task collection in the browser using the local calendar date and `task.completed === false`.
      - Compare normalized `YYYY-MM-DD` values without constructing UTC dates, preventing timezone shifts.
  - Story: Show incomplete overdue tasks
    - Acceptance Criteria:
      - The Overdue filter shows incomplete tasks with a due date before the current date.
      - The Overdue filter excludes completed tasks.
    - Technical Requirements:
      - Reuse the frontend date helper to identify tasks with a valid `dueDate` earlier than the local current date.
      - Exclude completed and undated tasks before rendering the Overdue filter.

- Epic: Local Task Storage
  - Story: Store task due dates and priorities locally
    - Acceptance Criteria:
      - Task due dates and priorities are stored locally with the task data.
      - No backend or external storage is used for task due dates or priorities.
    - Technical Requirements:
      - Replace the task `fetch` calls in `packages/frontend/src/App.js` and `packages/frontend/src/TaskList.js` with a browser-local persistence adapter.
      - Persist the complete task collection, including `dueDate` and `priority`, in `localStorage` under one documented application key.
      - Do not change `packages/backend/src/app.js`, its SQLite schema, or its `/api/tasks` endpoints for this upgrade.

## Post-MVP

- Epic: Overdue Task Visibility
  - Story: Highlight overdue tasks
    - Acceptance Criteria:
      - An overdue task is visually distinguishable from a task that is not overdue.
    - Technical Requirements:
      - Add an `isOverdue` presentation helper in `TaskList` that returns true only for incomplete tasks due before the local current date.
      - Apply a distinct Material UI style to overdue `ListItem` components without changing completed-task styling.

- Epic: Task Sorting
  - Story: Sort overdue tasks first
    - Acceptance Criteria:
      - Overdue tasks appear before tasks that are not overdue.
    - Technical Requirements:
      - Create a non-mutating task-sorting helper in `TaskList` that ranks overdue tasks before all other tasks.
      - Apply the sorter after filtering and before mapping tasks to `ListItem` components.
  - Story: Sort tasks by priority
    - Acceptance Criteria:
      - Within the same overdue status, tasks are ordered by priority: `P1`, then `P2`, then `P3`.
    - Technical Requirements:
      - Use an explicit priority-rank map of `P1: 1`, `P2: 2`, and `P3: 3` as the second sort key.
      - Treat a task with no priority as `P3` during sorting for backward compatibility.
  - Story: Sort tasks by due date
    - Acceptance Criteria:
      - Within the same overdue status and priority, tasks with due dates are ordered by ascending due date.
    - Technical Requirements:
      - Compare validated ISO date strings as the third sort key after overdue status and priority.
      - Preserve the existing relative order when tasks have the same due date.
  - Story: Place undated tasks last
    - Acceptance Criteria:
      - Within the same overdue status and priority, tasks without a due date appear after dated tasks.
    - Technical Requirements:
      - Rank a missing or invalid `dueDate` after valid dates in the due-date sort step.

- Epic: Priority Badges
  - Story: Display color-coded priority badges
    - Acceptance Criteria:
      - Each task displays a priority badge.
      - `P1` badges are red, `P2` badges are orange, and `P3` badges are gray.
    - Technical Requirements:
      - Render a Material UI `Chip` in `packages/frontend/src/TaskList.js` for each task's priority.
      - Define a single priority-to-color mapping for P1 red, P2 orange, and P3 gray, and use it for every badge.