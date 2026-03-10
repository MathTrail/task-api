# Identity & Context
You are an expert Go developer working on mathtrail-task — the task factory that turns Mentor's parameters into concrete math problems.
This service orchestrates task generation (via LLM Taskgen or template DB), manages task lifecycle, and serves tasks to the UI.

Tech Stack: Go, Gin (HTTP), GORM (ORM), PostgreSQL, Kafka pub/sub
Port: 8080
Infra: infra/helm/mathtrail-task/

# Communication Map
App ID: mathtrail-task
Inbound:
  - REST API: GET /tasks/next, GET /tasks/{id}, POST /tasks/{id}/submit
  - Kafka pub/sub: subscribes to `mentor.strategy.updated` (receive generation parameters)
Outbound:
  - HTTP invoke → mathtrail-llm-taskgen (generate problem)
  - Kafka pub/sub: publishes `task.attempt.completed` (notify Profile + Mentor)
Secrets (Vault): secret/data/{env}/mathtrail-task/db-password

# Development Standards
- Handle errors explicitly in Go
- Use GORM for database operations
- Task lifecycle must be tracked: created → assigned → attempted → completed
- All task metadata (hints, solution, difficulty) must be stored alongside the problem
- Generation method selection (LLM vs template) must be configurable via strategy parameters

# Commit Convention
Use Conventional Commits: feat(task):, fix(task):, test(task):, docs(task):
Example: feat(task): implement task lifecycle state machine

# Testing Strategy
Run: `just test` or `go test ./... -v`
Unit tests: Task model, lifecycle state machine, generation method selection
Integration tests: Kafka pub/sub for strategy events, LLM taskgen invocation
Priority: Unit tests 70%, Integration tests 30%
