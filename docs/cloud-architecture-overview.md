# Cloud Architecture Overview

## System Context

```mermaid
flowchart LR
  user[User] --> frontend[React Frontend\npackages/frontend]
  frontend -->|HTTPS /api/tasks| api[Express API\npackages/backend]
  api --> store[(In-Memory SQLite Store\nbetter-sqlite3)]
```

The application is a monorepo with a React frontend and an Express API. The API
stores task data in an in-memory SQLite database, so data is not retained when
the backend process restarts.

## Create a TODO Sequence

```mermaid
sequenceDiagram
  actor User
  participant Form as React TaskForm
  participant App as React App
  participant API as Express API
  participant Store as In-Memory SQLite Store

  User->>Form: Enter title, description, and optional due date
  User->>Form: Submit TODO
  Form->>App: onSave(task)
  App->>API: POST /api/tasks with task JSON
  API->>API: Validate required title
  API->>Store: INSERT task
  Store-->>API: Created task
  API-->>App: 201 Created with task JSON
  App->>App: Increment refresh key
  App->>Form: Clear submitted fields
  App->>API: GET /api/tasks
  API->>Store: SELECT tasks
  Store-->>API: Task list
  API-->>App: 200 OK with task list
  App-->>User: Render updated TODO list
```