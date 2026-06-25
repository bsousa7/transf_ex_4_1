# Exercício 4.1 — API REST de TODO list

API REST minimalista de gerenciamento de tarefas, construída com **FastAPI**.

## Endpoints

| Método | Rota              | Descrição                        |
|--------|-------------------|----------------------------------|
| GET    | `/health`         | Verifica se a API está no ar     |
| POST   | `/tarefas`        | Cria uma nova tarefa             |
| GET    | `/tarefas`        | Lista todas as tarefas           |
| GET    | `/tarefas/{id}`   | Busca uma tarefa pelo id         |
| PUT    | `/tarefas/{id}`   | Atualiza título e status da tarefa |

## Como rodar

```bash
pip install -r requirements.txt
uvicorn app.main:app --port 8000
```

## Exemplo de uso

```bash
# Verificar saúde
curl -s http://localhost:8000/health

# Criar tarefa
curl -s -X POST http://localhost:8000/tarefas \
  -H "Content-Type: application/json" \
  -d '{"titulo":"estudar APIs"}'

# Listar tarefas
curl -s http://localhost:8000/tarefas

# Buscar tarefa por id
curl -s http://localhost:8000/tarefas/1

# Atualizar tarefa
curl -s -X PUT http://localhost:8000/tarefas/1 \
  -H "Content-Type: application/json" \
  -d '{"titulo":"estudar APIs REST","concluida":true}'
```

## Estrutura

```
.
├── app/
│   ├── __init__.py
│   └── main.py
├── requirements.txt
├── README.md
└── .autograde-exercise
```
