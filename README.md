# move-tech-ci-registry
# 🚀 CI/CD Pipeline: GitHub Actions + Magalu Cloud Container Registry

![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=for-the-badge&logo=github-actions&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Flask](https://img.shields.io/badge/Flask-000000?style=for-the-badge&logo=flask&logoColor=white)
![Magalu Cloud](https://img.shields.io/badge/Magalu_Cloud-0086FF?style=for-the-badge&logo=icloud&logoColor=white)
![Python](https://img.shields.io/badge/Python_3.12-3776AB?style=for-the-badge&logo=python&logoColor=white)

Este repositório contém uma esteira automatizada de **Integração Contínua (CI/CD)** para a construção e publicação de imagens Docker no **Magalu Cloud Container Registry** a cada `git push` efetuado na branch principal (`main`).

---

## 📌 Visão Geral da Arquitetura

A pipeline foi projetada para garantir **rastreabilidade, velocidade e automação total** do processo de publicação de artefatos containerizados.

```text
[ Desenvolvedor ] ──( git push )──> [ GitHub Repositório ]
                                           │
                                           ▼
                                  [ GitHub Actions ]
                                           │
                                 ┌─────────┴─────────┐
                                 │  Docker Buildx    │
                                 │  Metadata & Tags  │
                                 │  Login & Auth     │
                                 └─────────┬─────────┘
                                           │
                                           ▼
                       [ Magalu Cloud Container Registry ]
                        └── container-registry.br-ne1...
```

---

## 🛠️ Tecnologias Utilizadas

- **Linguagem & Framework:** Python 3.12 + Flask 3.0.3
- **Containerização:** Docker (Buildx / OCI)
- **CI/CD Orchestration:** GitHub Actions
- **Cloud Provider:** Magalu Cloud (Container Registry - Região `br-ne1`)

---

## 📂 Estrutura do Projeto

```text
.
├── .github/
│   └── workflows/
│       └── publish.yml         # Workflow de automação do CI/CD
├── app.py                      # Aplicação Flask com endpoints / e /health
├── Dockerfile                  # Receita de build da imagem OCI/Docker
├── .dockerignore               # Arquivos ignorados no contexto de build
├── requirements.txt            # Dependências Python
└── README.md                   # Documentação do projeto
```

---

## ⚡ Como Funciona a Pipeline (`publish.yml`)

O workflow é disparado automaticamente a cada `push` na branch `main`. Ele executa as seguintes etapas:

1. **Checkout do Código:** Baixa a versão mais recente do repositório no runner `ubuntu-latest`.
2. **Definição de Endereço:** Monta o caminho dinâmico da imagem OCI no formato `HOST/REGISTRY_NAME/IMAGE_NAME`.
3. **Configuração do Docker Buildx:** Habilita o construtor estendido do Docker para suporte a cache e tags eficientes.
4. **Geração de Metadados:** Gera tags automáticas como `latest` e o **Git SHA curto** (ex: `a1b2c3d`) para rastreabilidade de commits.
5. **Validação de Credenciais:** Garante a presença dos `Secrets` configurados antes de prosseguir.
6. **Autenticação no Registry:** Autentica de forma segura via `docker/login-action`.
7. **Build & Push:** Compila a imagem Docker e envia o artefato para o Magalu Cloud Container Registry.
8. **Resumo da Execução:** Escreve no `GITHUB_STEP_SUMMARY` o link, tag e o **Digest SHA256** imutável gerado.

---

## 🔒 Configuração de Segredos (GitHub Secrets)

Para garantir a segurança das credenciais, cadastre os seguintes segredos em **Settings > Secrets and variables > Actions > Repository secrets**:

| Nome do Secret | Descrição |
| :--- | :--- |
| `MAGALU_REGISTRY_USERNAME` | Usuário de acesso ao Container Registry |
| `MAGALU_REGISTRY_PASSWORD` | Senha de acesso ao Container Registry |

---

## 🧩 Configuração de Variáveis (GitHub Variables)

Para evitar reescrever endereços públicos de infraestrutura no código do workflow, configure a seguinte variável pública em **Settings > Secrets and variables > Actions > Variables**:

| Nome da Variável | Valor de Exemplo | Descrição |
| :--- | :--- | :--- |
| `MAGALU_REGISTRY_ENDPOINT` | `container-registry.br-ne1.magalu.cloud` | Endpoint do Container Registry da Magalu Cloud |

---

## 🧪 Executando a Aplicação Localmente

Antes de enviar alterações para a esteira, você pode testar e validar o container na sua máquina local:

### 1. Construir a Imagem
```bash
docker build -t cicd-pipeline:1.0.0 .
```

### 2. Executar o Contêiner
```bash
docker run -d -p 5000:5000 --name app-local cicd-pipeline:1.0.0
```

### 3. Validar Endpoints
- **Aplicação:** `http://localhost:5000/`
- **Healthcheck:** `http://localhost:5000/health`

---

## 🏷️ Estratégia de Tags e Rastreabilidade

- **`latest`:** Aponta sempre para a versão mais recente publicada na branch principal.
- **`<sha-curto>`:** Identificador único do commit (ex: `e4f5a6b`), permitindo rastrear exatamente qual código gerou cada imagem.
- **`Digest (SHA256)`:** Assinatura digital imutável da imagem gerada no registry para auditoria de produção.

---

## 👨‍💻 Autor

Desenvolvido como parte do laboratório de **Integração Contínua com GitHub Actions e Magalu Cloud**.