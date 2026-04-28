# 🛒 Sistema de Controle de Caixa — Café & Parafuso

> Parte do ecossistema **Café & Parafuso** · Desenvolvido por alunos do Curso Técnico em Informática

---

## 📌 Sobre o Projeto

O Sistema de Controle de Caixa é o **módulo financeiro e administrativo** do Café & Parafuso. Ele é responsável por **receber os pedidos finalizados** vindos do Carrinho de Compras, **registrar cada venda** e **gerar relatórios gerenciais** que ajudam o dono do negócio a tomar decisões. <br><br>
Pense neste sistema como o **“livro-caixa digital”** da lanchonete: ele não processa vendas direta‐
mente — quem faz isso é o Carrinho. O Caixa **registra, organiza e analisa** os dados dessas vendas.

**Este sistema se integra com:**
- [X] Sistema de Carrinho de Compras
- [X] Sistema Principal (Café & Parafuso)
- [X] Sistema de Controle de Chamados
- [ ] Sistema de Catálogo de Produtos

---

## 🚀 Escopo Funcional (Alto Nível)

- [ ] Recepção de Pedidos Finalizados
- [ ] Registro de Vendas
- [ ] Relatórios Gerenciais
- [ ] Painel Administrativo

---

## 🛠️ Tecnologias

| Camada | Tecnologia | Versão Recomendada |
|---|---|---|
| Linguagem | Java | 17+ |
| Framework | Spring Boot | 4.0.6 |
| Template Engine | Thymeleaf | 3.1.5 |
| Banco de Dados | PostgreSQL | 15+ |
| ORM | Spring Data JPA / Hibernate | - |
| Build Tool | Maven | 3.9+ |
| FrontEnd | HTML5 + CSS3 + TailwindCSS 4 | - |
| Servidor | Tomcat Embedded (Spring Boot) | - |
| Controle de versão | Git + GitHub | - |

---

## 📁 Estrutura do Projeto

```
cp-cash-control/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── br/com/cp-cash-control/paybox/
│   │   │       ├── controller/          ← Controladores (recebem requisições)
│   │   │       │   ├── web/             ← Controllers do painel administrativo
│   │   │       │   └── api/             ← Controllers da API REST
|   │   │       ├── service/             ← Regras de negócio
│   │   │       │   ├── VendaService     ← Registro e consulta de vendas
│   │   │       │   └── RelatorioService ← Geração de relatórios
|   │   │       ├── repository/          ← Acesso ao banco de dados
|   │   │       ├── model/               ← Entidades (Produto, Categoria)
|   │   │       └── dto/                 ← Objetos de transferência de dados
|   |   └── resources/
│   │       ├── static/                  ← Arquivos estáticos (CSS, JS)
│   │       ├── templates/               ← Páginas Thymeleaf (HTML)
│   │       │   ├── painel.html          ← Dashboard principal
│   │       │   ├── vendas.html          ← Lista de vendas com filtros
│   │       │   └── relatorios.html      ← Telas de relatórios
│   │       └── application.properties   ← Configurações do sistema
│   └── test/
├── docs/
├── .gitignore
├── CONTRIBUTING.md
├── pom.xml                              ← Dependências do projeto (Maven)     
└── README.md
```

---

## ▶️ Como Executar

```bash
# 1. Clone o repositório
git clone https://github.com/SEU_USUARIO/cp-cash-control.git

# 2. Acesse a pasta
cd cp-cash-control

# 3. Execute o projeto
# (instruções específicas da equipe)
```

---

## 🤝 Como Contribuir

Leia o arquivo [CONTRIBUTING.md](./CONTRIBUTING.md) antes de qualquer alteração.

---

## 👥 Equipe

| Nome | Função |
|---|---|
| André Souza | Desenvolvedor |
| Daniel Barbosa | Desenvolvedor |
| Débora Silva | Desenvolvedora |
| Evaldo Oliveira | Desenvolvedor |
| Gabriela Nascimento | Desenvolvedora |
| Luis dos Reis | Desenvolvedor |
| Natan Ferreira | Desenvolvedor |
| Saulo Mendes | Desenvolvedor |
| Sthephany Simões | Desenvolvedora |
| Victoria de Souza | Desenvolvedora |

**Orientador:** Lenoln Muniz · [LinkedIn](https://linkedin.com/in/lenoln-io)

---

## 📄 Licença

Este projeto é de uso educacional, desenvolvido como projeto integrador do Curso Técnico em Informática.
