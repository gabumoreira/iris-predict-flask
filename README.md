# API de Classificação de Íris

Este projeto implementa uma API RESTful em Flask para servir um modelo de Machine Learning treinado para classificar espécies de flores de Íris com base em suas características (comprimento e largura da sépala e da pétala).

## Funcionalidades

-   **Autenticação JWT:** Endpoints protegidos que requerem um token de autenticação.
-   **Predição em Tempo Real:** Endpoint `/predict` para obter classificações do modelo.
-   **Armazenamento de Predições:** Todas as predições são salvas em um banco de dados SQLite.
-   **Histórico de Predições:** Endpoint `/predictions` para listar as predições já realizadas.
-   **Cache Simples:** Um cache em memória para acelerar predições repetidas.

## Tecnologias Utilizadas

-   **Python 3**
-   **Flask:** Micro-framework web para a criação da API.
-   **Scikit-learn:** Para carregar e utilizar o modelo de ML.
-   **SQLAlchemy:** ORM para interação com o banco de dados.
-   **PyJWT:** Para geração e validação de tokens JWT.
-   **Numpy:** Para manipulação de dados numéricos.

## Configuração e Instalação

1.  **Clonar o repositório:**
    ```bash
    git clone <url-do-repositorio>
    cd iris
    ```

2.  **Criar e ativar um ambiente virtual:**
    ```bash
    python -m venv venv
    # Windows
    .\venv\Scripts\activate
    # Linux/macOS
    source venv/bin/activate
    ```

3.  **Instalar as dependências:**
    (Assumindo que existe um arquivo `requirements.txt`)
    ```bash
    pip install -r requirements.txt
    ```
    Se não houver um `requirements.txt`, instale as bibliotecas manualmente:
    ```bash
    pip install flask sqlalchemy joblib numpy pyjwt
    ```

4.  **Executar a aplicação:**
    ```bash
    python api_modelo.py
    ```
    O servidor estará rodando em `http://127.0.0.1:5000`.

## Endpoints da API

### 1. Login

-   **Endpoint:** `POST /login`
-   **Descrição:** Autentica um usuário e retorna um token JWT.
-   **Corpo da Requisição (JSON):**
    ```json
    {
        "username": "admin",
        "password": "secret"
    }
    ```
-   **Resposta de Sucesso (200 OK):**
    ```json
    {
        "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
    }
    ```

### 2. Predição

-   **Endpoint:** `POST /predict`
-   **Descrição:** Realiza uma predição com base nas características da flor. Requer autenticação.
-   **Cabeçalho:**
    -   `Authorization: Bearer <seu-token-jwt>`
-   **Corpo da Requisição (JSON):**
    ```json
    {
        "sepal_length": 5.1,
        "sepal_width": 3.5,
        "petal_length": 1.4,
        "petal_width": 0.2
    }
    ```
-   **Resposta de Sucesso (200 OK):**
    ```json
    {
        "predicted_class": 0
    }
    ```

### 3. Listar Predições

-   **Endpoint:** `GET /predictions`
-   **Descrição:** Retorna uma lista paginada das predições armazenadas. Requer autenticação.
-   **Cabeçalho:**
    -   `Authorization: Bearer <seu-token-jwt>`
-   **Parâmetros de Query (Opcionais):**
    -   `limit`: Número de registros a retornar (padrão: 10).
    -   `offset`: Posição inicial para a busca (padrão: 0).
-   **Exemplo de Requisição:**
    ```
    GET /predictions?limit=5&offset=0
    ```
-   **Resposta de Sucesso (200 OK):**
    ```json
    [
        {
            "id": 1,
            "sepal_length": 5.1,
            "sepal_width": 3.5,
            "petal_length": 1.4,
            "petal_width": 0.2,
            "predicted_class": 0,
            "created_at": "2023-10-27T10:00:00.123456"
        }
    ]
    ```

## Como Usar (Exemplo com `curl`)

1.  **Obtenha o token:**
    ```bash
    TOKEN=$(curl -X POST -H "Content-Type: application/json" \
    -d '{"username": "admin", "password": "secret"}' \
    http://127.0.0.1:5000/login | jq -r .token)
    ```

2.  **Faça uma predição:**
    ```bash
    curl -X POST -H "Content-Type: application/json" \
    -H "Authorization: Bearer $TOKEN" \
    -d '{"sepal_length": 5.1, "sepal_width": 3.5, "petal_length": 1.4, "petal_width": 0.2}' \
    http://127.0.0.1:5000/predict
    ```

3.  **Liste as predições:**
    ```bash
    curl -X GET -H "Authorization: Bearer $TOKEN" http://127.0.0.1:5000/predictions
    ```
