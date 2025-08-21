# Documento de Diretrizes Tecnológicas do Projeto

## 1. Contexto e Objetivo
Este projeto tem como objetivo construir um microserviço escalável com as seguintes características principais:

- **Backend principal em Python**, utilizando **FastAPI** como framework para criação de APIs modernas, rápidas e tipadas.  
- **Integração administrativa com Flower** para monitoramento e gerenciamento de tarefas assíncronas.  
- **Banco de dados relacional PostgreSQL** como armazenamento central.  
- **Possibilidade de backend ou bibliotecas em Rust**, integradas ao Python para alto desempenho.  
- **Documentação externa hospedada no GitHub Pages**.  
- **Documentação interna mantida no Backstage** para consulta técnica da equipe.  

Dentro desse contexto, as tecnologias **PL/Python (PostgreSQL)** e **Alembic** serão elementos estruturantes na camada de dados e devem ser utilizadas de forma estratégica.

---

## 2. PostgreSQL com PL/Python

### 2.1 O que é PL/Python
- PL/Python é uma linguagem procedural do PostgreSQL que permite escrever funções diretamente no banco utilizando Python.  
- Essas funções podem executar lógica de negócios, processamento de dados e transformações sem a necessidade de trazer os dados para a aplicação Python externa.  

### 2.2 Papel no projeto
No microserviço, PL/Python será utilizado para:  
- Processamento intensivo de dados diretamente no banco (minimizando transferência de grandes volumes pela rede).  
- Criação de funções e triggers avançadas que encapsulem regras críticas.  
- Integração com lógica já escrita em Python, mantendo consistência entre aplicação e banco.  

### 2.3 Justificativa de uso
- Evita duplicação de lógica: regras que precisam estar “próximas” dos dados podem residir no próprio PostgreSQL.  
- Melhora performance em cenários onde a latência de ida/volta ao servidor seria crítica.  
- Permite aproveitar bibliotecas Python seguras no contexto do banco (respeitando as restrições de segurança do **PL/PythonU**, quando aplicável).  

### 2.4 Considerações estratégicas
- Limitar o uso de PL/Python apenas para regras críticas que exijam alta performance ou que não dependam de integrações externas.  
- Garantir que funções escritas sejam idempotentes e testáveis.  
- Documentar cada função no Backstage, descrevendo entradas, saídas e dependências.  

---

## 3. Alembic (Migrações de Banco de Dados)

### 3.1 O que é Alembic
- Alembic é uma ferramenta de versionamento de esquemas para bancos de dados, projetada para ser utilizada com **SQLAlchemy**.  
- Permite gerenciar mudanças estruturais (migrations) de forma incremental, segura e rastreável.  

### 3.2 Papel no projeto
- Será responsável por controlar a evolução do esquema do banco PostgreSQL.  
- Todas as alterações — criação de tabelas, índices, colunas, constraints — serão registradas como migrações.  
- Garantirá que todos os ambientes (desenvolvimento, teste, produção) estejam sincronizados com o mesmo estado de banco.  

### 3.3 Justificativa de uso
- Evita inconsistências entre ambientes.  
- Permite rollback seguro em caso de falhas.  
- Cria histórico versionado de todas as mudanças estruturais, essencial para auditoria e manutenção.  

### 3.4 Considerações estratégicas
- Nenhuma alteração no banco deve ser feita manualmente fora de uma migração Alembic.  
- Migrações devem ser revisadas e aprovadas antes de aplicação em produção.  
- Relacionar cada migração a uma tarefa/documento no Backstage para rastreabilidade.  

---

## 4. Integração das Tecnologias

### 4.1 Fluxo sugerido
1. Definição de modelos de dados no backend Python (**SQLAlchemy**).  
2. Geração de migrações **Alembic** a partir desses modelos.  
3. Aplicação das migrações para manter o banco atualizado.  
4. Criação de funções críticas em **PL/Python** quando necessário, versionadas junto com as migrações.  
5. Documentação de cada mudança (modelo e função) no **Backstage**, com explicação e contexto.  

### 4.2 Benefícios da combinação
- Alembic garante controle e rastreabilidade da estrutura do banco.  
- PL/Python permite desempenho e proximidade da lógica aos dados.  
- Juntos, mantêm consistência técnica e agilidade de desenvolvimento.  

---

## 5. Documentação e Governança
- **GitHub Pages**: conterá documentação externa, de alto nível, para stakeholders e usuários finais.  
- **Backstage**: conterá documentação técnica interna, incluindo:  
  - Esquema do banco e histórico de migrações.  
  - Lista de funções PL/Python, com propósito e exemplos de uso.  
  - Procedimentos para aplicação de migrações e rollback.  
  - Diretrizes de segurança para funções no banco.  

---

## 6. Pilares de Qualidade
- **Escalabilidade**: funções em PL/Python devem ser eficientes e projetadas para alto volume de dados.  
- **Manutenibilidade**: migrações Alembic claras e reversíveis.  
- **Segurança**: uso restrito de PL/PythonU (untrusted) apenas quando estritamente necessário.  
- **Observabilidade**: monitoramento do estado do banco e execução de funções críticas.  
