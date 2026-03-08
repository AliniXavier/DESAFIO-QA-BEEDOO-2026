# DESAFIO-QA-BEEDOO-2026

## Sobre este repositório
Este repositório contém toda a documentação referente ao desafio técnico para a vaga de **Analista de Qualidade de Software Júnior** na Beedoo.

**Aplicação testada:** https://creative-sherbet-a51eac.netlify.app/

---
# Etapa 1
## 1.Criação do repositório

**Repositório:** https://github.com/AliniXavier/DESAFIO-QA-BEEDOO-2026

---

## 2. Exploração da aplicação
### Qual você acredita ser o objetivo da aplicação?
A aplicação tem como objetivo permitir o **cadastro e a listagem de cursos educacionais**. Por meio dela é possível registrar informações como nome, descrição, instrutor, imagem de capa, período de realização, número de vagas e modalidade (Presencial ou Online). Os cursos cadastrados ficam disponíveis em uma lista pública, onde também é possível excluí-los.

### Quais são os principais fluxos disponíveis?
| Fluxo | Descrição |
|---|---|
| **Listar cursos** | Exibe todos os cursos em formato de cards com imagem, tipo, descrição, datas, vagas e botão de exclusão. |
| **Cadastrar curso** | Formulário com os campos: Nome, Descrição, Instrutor, URL da imagem de capa, Data de início, Data de fim, Número de vagas e Tipo de curso (Presencial/Online). |
| **Excluir curso** | Botão "Excluir Curso" presente em cada card da listagem. |

> ⚠️ **Observação:** Não há fluxo de autenticação (login/logout) nem de edição de cursos. Qualquer pessoa com acesso à URL pode cadastrar e tentar excluir cursos sem qualquer restrição.

### Quais pontos do sistema você considera mais críticos para teste?
Com base na exploração da aplicação e na análise técnica via DevTools, identifiquei os seguintes pontos como mais críticos, ordenados por prioridade:

**1. Ausência de autenticação:**
Toda a aplicação é acessível publicamente sem login. Qualquer pessoa pode cadastrar cursos e acionar a exclusão sem nenhum controle de identidade ou permissão. Confirmado via DevTools: nenhuma requisição contém header de `Authorization`.

**2. Ausência de validação de campos no cadastro:**
O formulário não impede o envio com campos vazios, permite datas incoerentes, valores numéricos inválidos e URLs sem formato válido. É possível criar registros completamente vazios, poluindo a listagem com cards sem nenhuma informação. A ausência dessas validações é um problema básico de integridade de dados.

**3. Comportamento da API:**
Utilizei o painel Network do Chrome DevTools para inspecionar as chamadas HTTP reais. Os endpoints `POST /test-api/courses` e `DELETE /test-api/courses/:id` retornam `405 Method Not Allowed`, mas o front-end exibe mensagens de sucesso sem verificar o status code da resposta.

**4. Funcionalidade de exclusão inoperante:**
A exclusão falha silenciosamente, exibe sucesso sem executar a ação, comprometendo completamente a confiança do usuário no sistema. 

**5. Ausência de edição de cursos:**
Não existe funcionalidade para corrigir as informações de um curso já cadastrado. Se um dado for inserido errado: uma data incorreta, um nome com erro de digitação, um número de vagas equivocado; o usuário não tem nenhuma forma de corrigir. Isso é agravado pelo fato de que a exclusão também não funciona, deixando o usuário completamente sem saída para lidar com dados incorretos.

**6. Inconsistência visual e de UX na listagem:**
Cursos cadastrados com campos incompletos ou vazios geram cards visualmente quebrados na listagem: sem imagem, sem datas, sem vagas e sem nome. Do ponto de vista de UX, isso representa uma falha grave: o sistema não fornece nenhum feedback ao usuário sobre o dado ausente, simplesmente exibe o card incompleto como se fosse um registro válido. A interface não trata a ausência dessas informações na exibição, resultando em uma página desconfigurada que transmite falta de confiabilidade e prejudica a experiência de quem acessa a aplicação.

**7. Ausência de tratamento para lista vazia:**
Quando não há cursos cadastrados, a página de listagem exibe apenas o título **"Lista de cursos"** sem nenhuma mensagem informativa ao usuário. Do ponto de vista técnico, isso indica que o front-end não trata o retorno de array vazio da API, simplesmente não renderiza nada. Uma boa implementação exibiria uma mensagem como **"Nenhum curso cadastrado ainda"**, orientando o usuário sobre o estado atual da aplicação.

**8. Ausência de paginação na listagem:**
A listagem busca e exibe todos os cursos de uma vez, sem paginação ou limite de resultados. Em um cenário real com centenas de registros, isso causaria lentidão progressiva e potencial travamento da página. Um endpoint GET /courses sem paginação é um problema de performance e escalabilidade que se torna crítico conforme o volume de dados cresce.

**9. Erro de escrita no título da aplicação:**
O título exibido na barra de navegação é "Beedoo QA Chalenge", mas "Challenge" está grafado incorretamente com ausência da letra "l". Embora seja um detalhe, acredito ser importante registrar inconsistências de texto na interface, pois elas impactam a credibilidade e o profissionalismo do produto entregue ao usuário final.

---
## 3. Criação de cenários e casos de teste

### Decisões tomadas para criação dos testes

Estruturei os testes em quatro grupos, por ordem de criticidade:

**1. Segurança e autenticação:**
Antes de qualquer teste funcional, avaliei se a aplicação possui controle de acesso. A ausência de autenticação é o problema mais crítico pois compromete toda a base de segurança do sistema. Como desenvolvedor back-end, sei que sem middleware de autenticação (como JWT), todos os endpoints ficam publicamente acessíveis e, confirmei isso via DevTools.

**2. Integridade da API:**
Aproveitei meu conhecimento de Node.js e Express para inspecionar as chamadas HTTP reais via DevTools. Esse nível de análise permite identificar a causa raiz dos problemas, não apenas os sintomas visíveis na interface. Por exemplo: o bug de exclusão não é **"o botão não funciona"**, mas sim **"o endpoint DELETE retorna 405 porque o método não está implementado no servidor".**

**3. Validações de formulário:**
Testei os limites de cada campo individualmente: obrigatoriedade, datas incoerentes, valores numéricos inválidos e formato de URL.

**4. Comportamentos inesperados:**
Verifiquei cenários que usuários reais poderiam acionar: campos vazios, valores extremos e inputs que poderiam gerar comportamentos inesperados no sistema.

> ⚠️ **Observação:** Os casos de teste foram documentados nos **dois formatos** disponíveis:

- **Passo a passo estruturado** — sequência numerada de ações, mais direto e prático para acompanhamento durante a execução manual
- **Gherkin (DADO / QUANDO / ENTÃO)** — linguagem semi-natural amplamente usada em QA profissional, facilita a leitura por qualquer pessoa do time, inclusive não técnica.

Ambos os formatos estão disponíveis na planilha, em colunas separadas para cada caso de teste.

#### Planilha de casos de teste

🔗 **Casos de Teste e Bugs**
*https://docs.google.com/spreadsheets/d/1nKBF62cE4Fcr9HzkPgdALJ805W95m8N7zqT3r6y9pJk/edit?usp=sharing*

---

### 4. Execução dos testes

**Metodologia utilizada:**
Os testes foram executados manualmente no navegador Chrome, com o painel DevTools (abas Network e Console) aberto durante toda a execução para capturar as chamadas HTTP reais da aplicação.

#### Pasta com evidências da execução

🔗 **Evidências**
*https://drive.google.com/drive/folders/1tE9Ne_SgjYjtveZyYZHs0TKIRVvPuviT?usp=sharing*

---

### 5. Registro de Bugs

O relatório completo de bugs está na aba **"Bugs"** da planilha de casos de teste, contendo título, passos para reproduzir, resultado atual, resultado esperado e severidade.

Resumo dos bugs identificados:
| # | Título | Severidade |
|---|---|---|
| BUG-01 | Ausência de autenticação — qualquer usuário pode cadastrar e excluir cursos sem login. | Crítica |
| BUG-02 | Front-end exibe mensagem de sucesso ignorando status code de erro da API. | Crítica |
| BUG-03 | Endpoint DELETE retorna 405 — exclusão de curso não funciona no servidor. | Crítica |
| BUG-04 | Cadastro permitido com campos vazios ou apenas com o nome preenchido. | Alta |
| BUG-05 | Datas incoerentes são aceitas — data de fim igual ou anterior à data de início | Alta |
| BUG-06 | Número de vagas zero é aceito. | Alta |
| BUG-07 | URL de imagem inválida gera requisição GET com input do usuário como rota. | Alta |
| BUG-08 | Falta de mensagem que demonstre a inexistência de cursos cadastrados.| Baixa |
| BUG-09 | Lista de cursos sem paginação. | Baixa |
| BUG-10 | Erro de escrita no título da aplicação — "Chalenge" ao invés de "Challenge". | Baixa |

---

# Etapa 2 – Análise e Tomada de Decisão

Esta etapa está descrita no formulário do Desafio.

---

# Extra - Análises e mehorias sugeridas

Além dos bugs encontrados, identifiquei oportunidades de melhoria que elevariam a qualidade da aplicação:

**Implementar autenticação e autorização:**
Adicionar fluxo de login com JWT, protegendo os endpoints para que apenas usuários autenticados possam cadastrar e excluir cursos.

**Implementar edição de cursos (PUT/PATCH):**
A aplicação possui um CRUD incompleto, falta o método de atualização. Sem edição, o usuário não tem como corrigir informações de um curso já cadastrado. Isso é agravado pelo fato de que a exclusão também não funciona, deixando o usuário completamente sem saída para corrigir dados incorretos.

**Tratar erros da API no front-end:**
O front-end deve verificar o status code de cada resposta antes de exibir mensagens ao usuário.

**Validar formato de URL no campo de imagem**
O campo deve aceitar apenas strings no formato `https://...`. O valor digitado não deve ser usado diretamente em requisições sem validação prévia.

**Adicionar confirmação antes de excluir**
Operações destrutivas devem solicitar confirmação do usuário antes de serem executadas.

**Exibir mensagem quando a lista está vazia**
Quando não há cursos cadastrados, exibir mensagem informativa como "Nenhum curso cadastrado ainda" em vez de uma página em branco.

**Implementar paginação na listagem**
Limitar o número de cursos exibidos por página para garantir performance e escalabilidade conforme o volume de dados cresce.

---

# Transparência

***Uso de Inteligência Artificial:***

Durante este desafio, utilizei o **Claude (Anthropic)** como ferramenta de apoio para estruturar e documentar o trabalho.

**O que a IA fez:**
- Ajudou a organizar e formatar a documentação dos casos de teste e bugs.
- Sugeriu categorias de cenários de teste com base nos fluxos descritos.
- Auxiliou na ampliação de ideias cenários e brainstorm em cima dos testes identificados.
- Auxiliou na estruturação do README e da planilha.

**O que eu fiz:**
- Explorei e executei todos os testes diretamente na aplicação.
- Identifiquei todos os bugs por conta própria durante a exploração.
- Utilizei o DevTools para inspecionar as chamadas HTTP e interpretar os status codes, com base no meu conhecimento de desenvolvimento back-end com JavaScript, Node.js, Express.js, API RESTful e Banco de Dados.
- Avaliei, questionei e adaptei todas as sugestões da IA com base na minha análise real.
- Tomei todas as decisões sobre priorização, severidade e o que incluir ou descartar.

A IA foi usada como suporte para documentação. A análise técnica, a identificação de bugs e o raciocínio crítico foram inteiramente meus.

---

# Contato
**Candidato:** *Alini Xavier da Costa*

**LinkedIn:** *https://www.linkedin.com/in/alinixavier/*
