# ApiDockerQt

Projeto da API (crud-mysql) para integração com cliente Qt.

API REST simples em **Ktor + Kotlin + MySQL**, com autenticação JWT, para cadastro e gestão de:

- Usuários
- Alunos
- Responsáveis

## Stack

- Kotlin / Ktor
- Exposed ORM + HikariCP
- MySQL
- JWT (auth-jwt)
- Docker (via tarefas do plugin Ktor)

## Pré-requisitos

- JDK 17+ (recomendado)
- MySQL em execução

## Configuração de ambiente

Crie um arquivo `.env` na raiz do projeto com:

```env
JDBC_URL=jdbc:mysql://localhost:3306/{db_name}
DB_USER={db_user}
DB_PASSWORD={db_password}
JWT_SECRET=um_segredo_forte
JWT_ISSUER=api-docker-qt
JWT_AUDIENCE=api-docker-qt-users
```

## Executar localmente

```bash
./gradlew run
```

Servidor padrão: `http://0.0.0.0:8080`

## Rotas

### Pública

- `GET /`  
  Retorna `Hello World!`.

- `POST /register`  
  Cadastra usuário.
  ```json
  {
    "nome": "admin",
    "senha": "123456"
  }
  ```

- `POST /login`  
  Autentica usuário e retorna token JWT.
  ```json
  {
    "nome": "admin",
    "senha": "123456"
  }
  ```
  Resposta:
  ```json
  {
    "token": "seu.jwt.aqui"
  }
  ```

### Protegidas (`Authorization: Bearer <token>`)

- `POST /registerAlunoAndResponsavel`  
  Cadastra aluno e responsável.
  ```json
  {
    "nomeAluno": "João",
    "dataNascimento": "2015-08-10",
    "sexoAluno": "M",
    "rgAluno": "1234567",
    "cpfAluno": "12345678901",
    "nomeResponsavel": "Maria",
    "telefoneResponsavel": "11999999999"
  }
  ```

- `PUT /alunos/{id}`  
  Atualiza aluno e responsável vinculados.
  ```json
  {
    "nomeAluno": "João Silva",
    "dataNascimento": "2015-08-10",
    "sexoAluno": "M",
    "nomeResponsavel": "Maria Silva",
    "telefoneResponsavel": "11888888888"
  }
  ```

- `GET /alunos`  
  Lista todos os alunos com seus responsáveis.

- `GET /alunos/search?q=joao`  
  Pesquisa por nome, CPF, RG ou nome do responsável.

- `DELETE /alunos/{id}`  
  Remove aluno (e responsável sem vínculos restantes, quando aplicável).

## Exemplo rápido com cURL

```bash
# Login
TOKEN=$(curl -s -X POST http://localhost:8080/login \
  -H "Content-Type: application/json" \
  -d '{"nome":"admin","senha":"123456"}' | jq -r .token)

# Listar alunos
curl -X GET http://localhost:8080/alunos \
  -H "Authorization: Bearer $TOKEN"
```

## Comandos úteis (Gradle)

- `./gradlew test` — roda testes
- `./gradlew build` — build completo
- `./gradlew buildFatJar` — gera JAR executável
- `./gradlew buildImage` — cria imagem Docker
- `./gradlew publishImageToLocalRegistry` — publica imagem no registry local
- `./gradlew run` — inicia a API localmente
- `./gradlew runDocker` — executa com imagem Docker local
