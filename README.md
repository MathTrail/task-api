# mathtrail-task
Task factory — turns Mentor's strategic parameters into concrete math problems by orchestrating LLM generation and template selection.

## Mission & Responsibilities
- Receive generation parameters from Mentor (type, difficulty, topic)
- Select task generation method (LLM via taskgen, or template DB)
- Assemble final task with metadata (hints, solution, difficulty)
- Serve tasks to UI via REST API
- Track task lifecycle (created → assigned → attempted → completed)

## Tech Stack
- **Language**: Go
- **Framework**: Gin (HTTP), GORM (ORM)
- **Database**: PostgreSQL (task templates, task instances)
- **Events**: Dapr pub/sub over Kafka

## API & Communication (Dapr)
- **Inbound**: REST API (GET /tasks/next, GET /tasks/{id}), Dapr invocation from UI
- **Outbound**: Dapr invoke → mathtrail-llm-taskgen (generate problem)
- **Subscribes**: `mentor.strategy.updated` (receive generation parameters)
- **Publishes**: `task.attempt.completed` (notify Profile + Mentor)

## Data Persistence
- **PostgreSQL**: task_templates, task_instances, task_attempts

## Secrets
- `DB_PASSWORD` — PostgreSQL password
- Vault path: `secret/data/{env}/mathtrail-task/`

## Infrastructure
Standard `infra/` layout:
- `infra/helm/` — Helm chart + environment overlays (dev, on-prem, cloud)
- `infra/terraform/` — Database module (K8s pod vs managed RDS)
- `infra/ansible/` — On-prem node preparation

## Development
- `just dev` — Skaffold dev loop
- `just test` — Go unit tests

## Roadmap
1. Define Task data model (templates, instances, attempts)
2. Implement Dapr subscriber for `mentor.strategy.updated`
3. Build REST API for task delivery (GET /tasks/next with difficulty filter)
