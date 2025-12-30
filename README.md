# RAG Code Review

Projeto desenvolvido durante o curso "IA para Devs" da Rocketseat.

Este repositório contém um notebook (codeReviewRag.ipynb) e um conjunto de bibliotecas e exemplos que demonstram um fluxo de RAG (Retrieval-Augmented Generation) aplicado a revisão de código.

**Objetivo**: construir um pipeline que carregue um repositório de código, gere embeddings dos arquivos, indexe-os em uma base vetorial e utilize um LLM local para gerar revisões de código e sugestões de melhoria.

## Estrutura do repositório

- `codeReviewRag.ipynb`: notebook principal com o fluxo de clonagem do repositório, parsing, chunking, indexação e invocação do modelo.
- `repo/`: pasta onde o repositório alvo (ex: `langchain`) é clonado para análise.
- `libs/`: dependências e subprojetos utilizados no projeto (inclui implementações e wrappers do LangChain e utilitários).

## Principais componentes

- Carregamento de documentos: `GenericLoader` com `LanguageParser` para ler arquivos Python.
- Splitter: `RecursiveCharacterTextSplitter` configurado para linguagem Python.
- Indexação: `Chroma` para base vetorial local.
- Embeddings: `OllamaEmbeddings` (modelo `nomic-embed-text:v1.5`).
- LLM de conversação: `ChatOllama` (ex.: `phi3:mini`).
- Pipeline de recuperação e geração: `create_retrieval_chain` + `create_stuff_documents_chain`.

## Como usar

1. Instale dependências (recomendado usar `pipenv` ou `poetry`). Exemplo com `pipenv`:

```powershell
pipenv install --dev
pipenv shell
```

2. Configure o Ollama localmente e garanta que os modelos necessários estão instalados (ex.: `nomic-embed-text:v1.5` e `phi3:mini`). Verifique com:

```powershell
ollama list
```

3. Execute o notebook `codeReviewRag.ipynb` numa sessão do Jupyter ou VS Code:

- O notebook clona o repositório alvo em `./repo`.
- Usa `GenericLoader` para carregar arquivos Python, aplica chunking e cria embeddings.
- Cria uma instância de `Chroma` com os documentos embeddados e monta o `retriever`.
- Invoca o chain de retrieval+LLM para gerar revisões.

Exemplo de célula do notebook (trecho relevante):

```python
from git import Repo
repo_path = "./repo"
repo = Repo.clone_from("https://github.com/langchain-ai/langchain", to_path=repo_path)
```

E a execução final do chain:

```python
response = retrieveval_chain.invoke({"input": "You can review and suggest improvements to the RunnableBinding code."})
print(response['answer'])
```

## Configurações importantes

- Ollama: serviço local para modelos — instale e carregue os modelos mencionados.
- Chroma: a base vetorial é criada localmente; configure diretórios caso necessário.
- Ajuste `chunk_size` e `chunk_overlap` do `RecursiveCharacterTextSplitter` conforme necessidade.

