

***

# Flask CRUD com Autenticação e Controle de Acesso

Este projeto é uma aplicação web desenvolvida em Flask que implementa autenticação de usuário, CRUD de usuários, gerenciamento de perfis e integração com banco de dados MySQL. Ele inclui mecanismos de autenticação, controle de sessão, hash de senhas e permissões por nível de usuário, podendo ser facilmente adaptado e expandido conforme a necessidade.

## Funcionalidades

- Registro de novos usuários
- Login/Logout com hash seguro de senha (bcrypt)
- CRUD de usuários (apenas para administradores)
- Controle de sessão e rotas protegidas
- Gerenciamento de diferentes níveis de permissão (admin/user)
- Integração com banco de dados MySQL via SQLAlchemy
- Testes de rotas via Postman, facilitando o desenvolvimento de APIs RESTful

***

### Estrutura de Arquivos

| Arquivo                       | Função Principal                                        |
|-------------------------------|-------------------------------------------------------|
| `app.py`                      | App principal Flask e definição de rotas/crud         |
| `models/user.py`              | Definição do modelo de usuário e configuração ORM     |
| `database.py`                 | Singleton do SQLAlchemy para integração com o banco   |
| `docker-compose.yml`          | (Arquivo esperado para subir stack com Docker)        |
| (Testes/Postman)              | Teste de endpoints durante o desenvolvimento          |

***

## Detalhamento dos Arquivos

### app.py

Responsável por iniciar o Flask, configurar a conexão com o banco de dados, gerenciar autenticação de sessão, e definir as rotas que implementam o CRUD de usuários.

- **Configuração:**  
  - `SECRET_KEY` protege sessões (“flask.session”).
  - `SQLALCHEMY_DATABASE_URI`: string de conexão para MySQL.
- **LoginManager:**  
  - Controla autenticação/estado da sessão.
  - Exige login antes de acessar algumas rotas (decorator `@login_required`).
- **Função load_user:**  
  - Carrega usuário a partir do banco, usando o ID salvo na sessão.
- **Rotas:**
  - `POST /login`: autentica usuário, verifica senha (bcrypt).
  - `GET /logout`: encerra sessão logada.
  - `POST /user`: registra novo usuário (com senha já em hash).
  - `GET /user/<id>`: retorna dados do usuário logado (rota protegida).
  - `PUT /user/<id>`: atualiza senha do usuário, acessível só pelo próprio usuário/admin.
  - `DELETE /user/<id>`: deleta usuário, apenas admin pode usar. Não é possível que o admin delete a si mesmo.
  - `GET /hello-world`: rota pública de teste.
- **Execução:**  
  - `app.run(debug=True)` ativa debug para desenvolvimento, exibindo erros no navegador.

### models/user.py

Define o modelo de usuário para o banco via SQLAlchemy:

- Herda de UserMixin, o que permite integração com Flask-Login.
- Campos:
  - `id`: identificador primário único.
  - `username`: nome de login, único, obrigatório.
  - `password`: senha, salva já em formato protegido (hash).
  - `role`: nível de acesso (“user” por default, “admin” para privilégios extras).

### database.py

- Apenas inicializa e exporta o objeto `db` do SQLAlchemy que será usado em todas as partes do app para conexão e manipulação de dados.

### docker-compose.yml

- Não foi fornecido totalmente. Normalmente define os serviços Docker necessários (Flask app + MySQL). É esperado, por convenção, para subir todo ambiente rapidamente.

### Testes com Postman

- Foram usados para testar todas as rotas da API, simulando chamadas de API REST para autenticação, criação, leitura, atualização e deleção de usuários.

***

## Considerações de Segurança e Melhoria

- O projeto já utiliza bcrypt para proteção de senha (nunca armazena informações sensíveis em texto claro).
- O acesso a rotas sensíveis é controlado tanto por autenticação (`login_required`) quanto por checagem de permissão no próprio endpoint.
- Dados sensíveis, como segredo Flask e senhas/usuário do banco, devem ser movidos para variáveis de ambiente antes de qualquer deploy em produção.

***

## Como Executar Localmente

1. Instale dependências:
   ```
   pip install flask flask-login flask-sqlalchemy bcrypt pymysql
   ```
2. Configure o banco MySQL conforme a string de conexão.
3. Rode o app:
   ```
   python app.py
   ```
4. Use Postman (ou outro cliente HTTP) para testar os endpoints REST.

***

## Observação sobre Erros no Código

- No `login()` de `app.py`, há um pequeno typo: `str.econde(user.password)` deve ser corrigido para `str.encode(user.password)`.
- O arquivo `docker-compose.yml` não foi incluído, apenas foi colado novamente o código do modelo User; seria interessante adicionar o YAML correto para facilitar testes com Docker.

***

## Resumo Técnico

Trata-se de um exemplo robusto de backend Flask com autenticação, CRUD completo de usuário, permissões e integração com banco relacional, já pronto para customização e uso em projetos reais.