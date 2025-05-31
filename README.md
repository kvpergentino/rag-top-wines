# RAG: Assistente de Vinhos (LLM Local)

Este é um projeto guiado e demonstra a implementação do padrão **Retrieval-Augmented Generation (RAG)** para desenvolver um sistema de recomendação de vinhos. A arquitetura integra um mecanismo de recuperação de informações baseado em embeddings com um LLM para gerar respostas contextualmente relevantes.

Os principais objetivos de aprendizado e implementação deste projeto incluem:
* Implementar o padrão RAG utilizando um conjunto de dados personalizado.
* Aplicar dados específicos para resolver um problema de recomendação de vinhos via RAG.
* Compreender a sinergia entre um LLM e um banco de dados vetorial (utilizado Qdrant) para gerar respostas úteis.

## Estrutura do Repositório

O projeto está organizado da seguinte forma:

```
.
├── data/
│   └── top_rated_wines.csv   # Conjunto de dados de vinhos (top_rated_wines.csv).
├── notebooks/
│   ├── 01.managing-data.ipynb    # Notebook dedicado à exploração e pré-processamento dos dados.
│   └── 02.embeddings.ipynb       # Notebook principal que contém a lógica de criação de embeddings e a implementação do RAG.
├── LICENSE.txt               # Arquivo de licença do projeto.
└── README.md
└── requirements.txt          # Lista das dependências Python do projeto.

```

## Tecnologias Utilizadas
Este projeto emprega as seguintes bibliotecas e ferramentas:
* **Qdrant**: Banco de dados de vetores em memória, utilizado para armazenar e realizar buscas por similaridade nos embeddings.
* **Sentence Transformers**: Biblioteca para a criação de embeddings (representações vetoriais densas) a partir de descrições textuais de vinhos. O modelo `all-MiniLM-L6-v2` foi empregado para este fim.
* **Llamafile**: Um executável standalone que encapsula um modelo LLM (Llama 3.2 3B Instruct) e o runtime `llama.cpp`. Esta solução permite a execução local do LLM sem a necessidade de compilação complexa ou instalação de dependências adicionais de C++.
* **OpenAI's Python API**: Utilizada para interagir com o Llamafile, que expõe uma API compatível com os endpoints da OpenAI.

## Guia de Instalação e Execução

### 1. Configuração do Ambiente Python

Recomenda-se a criação de um ambiente virtual para isolar as dependências do projeto.

```bash
python -m venv .venv
source .venv/bin/activate # Para Windows, use: ./.venv/Scripts/activate
pip install -r requirements.txt
```
*(As bibliotecas `pandas`, `ipykernel`, `ipywidgets`, `qdrant-client`, `sentence-transformers`, e `openai` serão instaladas).*

### 2. Configuração e Execução do LLM Local (Llamafile)

O Llamafile permite a execução do LLM localmente, eliminando a dependência de serviços externos.

* **Download do Llamafile**:
    Baixe o arquivo do modelo `Llama-3.2-3B-Instruct.Q6_K.llamafile` do Hugging Face:
    [https://huggingface.co/Mozilla/Llama-3.2-3B-Instruct-llamafile/blob/main/Llama-3.2-3B-Instruct.Q6_K.llamafile?download=true](https://huggingface.co/Mozilla/Llama-3.2-3B-Instruct-llamafile/blob/main/Llama-3.2-3B-Instruct.Q6_K.llamafile?download=true)

* **Preparação do Executável (Windows)**:
    1.  Renomeie o arquivo baixado para `llama3_2.exe` (ex: `C:\llama3_2.exe`).
    2.  Abra um **Prompt de Comando (como Administrador)** e navegue até o diretório onde o arquivo foi salvo.
    3.  Execute o comando para iniciar o servidor do LLM. Este comando inicia a API na porta padrão `8080`.
        ```bash
        C:\llama3_2.exe --port 8080 -c 4096
        ```
        * `--port 8080`: Define a porta da API.
        * `-c 4096`: Configura o tamanho da janela de contexto do modelo.
    * Mantenha este terminal aberto enquanto o projeto estiver em uso, pois ele hospeda o servidor do LLM.

### 3. Execução dos Notebooks

Após o sucesso na inicialização do servidor Llamafile, proceda com a execução dos notebooks Jupyter.

* Abra o `01.managing-data.ipynb` e `02.embeddings.ipynb` na pasta `notebooks/`.
* **Ajuste no caminho do arquivo de dados**: No notebook `01.managing-data.ipynb`, o caminho para o dataset CSV deve ser corrigido devido à nova estrutura de pastas:
    ```python
    df = pd.read_csv('../data/top_rated_wines.csv')
    ```
* **Ajuste no nome do modelo LLM**: No notebook `02.embeddings.ipynb`, na célula que estabelece a conexão com o LLM, o parâmetro `model` deve ser atualizado para refletir o nome exato do modelo servido pelo Llamafile:
    ```python
    completion = client.chat.completions.create(
        model="llama-3.2-3B-Instruct.Q6_K", # Nome do modelo retornado pelo Llamafile
        # ...
    )
    ```
* Execute todas as células dos notebooks em sequência. A resposta do LLM deverá ser gerada com sucesso, demonstrando a integração RAG.

## Resultados e Conclusões

Este projeto permitiu a implementação prática do padrão RAG, evidenciando a capacidade de combinar a recuperação de informações de uma base de dados vetorial com a geração de texto por um LLM local. A utilização do Llamafile simplificou significativamente o setup do LLM, tornando a experimentação com RAG mais acessível.

---