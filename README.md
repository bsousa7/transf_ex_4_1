# Exercício 4.1 — API REST de uma aplicação de TODO list (POST/GET/PUT)

**Aluno:** Bruno Aires de Sousa
**Disciplina:** IDP-TD 2026
**Framework usado:** FastAPI + Uvicorn

---

## O que esta API faz

API REST que serve de **backend de uma aplicação de TODO list** — gerencia
**tarefas** (`{id, titulo, concluida}`), com armazenamento em memória, rodando em
`http://localhost:8000`. Implementa POST (criar), GET (ler) e
PUT (atualizar), seguindo o contrato do [tutorial_4.1.md](tutorial_4.1.md#3-contrato-da-api-obrigatório--o-autograder-depende-disto).

## Estrutura

- [app/main.py](app/main.py) — implementação da API
- [requirements.txt](requirements.txt) — dependências (`fastapi`, `uvicorn`)
- [`.autograde-exercise`](.autograde-exercise) — marcador do autograder (conteúdo: `4.1`)

## Como rodar

```bash
pip install -r requirements.txt
uvicorn app.main:app --port 8000
```

## Como validar

Com a API rodando (recém-reiniciada, store vazio), em outro terminal dentro do repo:

```bash
autograde validar 4.1
```

## Endpoints

| Método | Rota | Descrição |
|---|---|---|
| GET | `/health` | liveness — `{"status":"ok"}` |
| POST | `/tarefas` | cria tarefa a partir de `{"titulo": "..."}` |
| GET | `/tarefas/{id}` | lê uma tarefa (404 se não existe) |
| GET | `/tarefas` | lista todas |
| PUT | `/tarefas/{id}` | atualiza `titulo` e `concluida` |

## Decisões de implementação

O FastAPI foi escolhido por ser o framework conforme indicação no próprio tutorial e por eliminar boa parte do código boilerplate, dado que ele serializa e deserializa JSON automaticamente, aplica o status_code=201 no POST sem configuração extra e valida o corpo das requisições via Pydantic antes mesmo de chegar na função, reduzindo assim a chance de bugs silenciosos com tipos errados.

O store foi modelado como um dicionário Python simples (dict[int, dict]) com um contador global _proximo_id iniciando em 1. A chave do dict é o próprio id, o que garante lookup em O(1) nos endpoints de GET e PUT. O contador só cresce e nunca reutiliza ids, comportamento correto para REST. Como o store vive em memória, ele zera ao reiniciar o processo.

O corpo das requisições foi separado em dois modelos Pydantic: TarefaIn (só titulo) para o POST, e TarefaUpdate (titulo e concluida) para o PUT. Essa separação impede que o cliente envie campos que não deveria controlar — como o id ou o valor inicial de concluida — e torna o contrato de cada endpoint explícito e validado automaticamente pelo framework.

O tratamento de 404 foi feito com raise HTTPException(status_code=404) imediatamente após verificar if id not in _tarefas, tanto no GET quanto no PUT. O FastAPI converte esse raise numa resposta HTTP com o status correto sem nenhuma lógica adicional. Colocar essa verificação antes de qualquer acesso ao dict evita KeyError e garante que ids inexistentes nunca passem adiante na execução.
