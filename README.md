# Tutorial Completo de MCP com GitHub Copilot

> Aprenda o que é o Model Context Protocol (MCP), como configurar MCP Servers no Visual Studio Code e como combinar diferentes MCPs em um fluxo completo de investigação, diagnóstico, correção e validação de uma aplicação.

---

## Sumário

- [1. Introdução](#1-introdução)
- [2. O que você aprenderá](#2-o-que-você-aprenderá)
- [3. Pré-requisitos](#3-pré-requisitos)
- [4. O que é MCP](#4-o-que-é-mcp)
  - [4.1. O problema que o MCP resolve](#41-o-problema-que-o-mcp-resolve)
  - [4.2. Arquitetura do MCP](#42-arquitetura-do-mcp)
  - [4.3. Conceitos importantes](#43-conceitos-importantes)
- [5. Como o GitHub Copilot utiliza um MCP](#5-como-o-github-copilot-utiliza-um-mcp)
- [6. Como configurar MCP no VS Code](#6-como-configurar-mcp-no-vs-code)
  - [6.1. Formas de adicionar um MCP](#61-formas-de-adicionar-um-mcp)
  - [6.2. O arquivo `.vscode/mcp.json`](#62-o-arquivo-vscodemcpjson)
  - [6.3. Como validar a configuração](#63-como-validar-a-configuração)
  - [6.4. Configuração global ou por workspace](#64-configuração-global-ou-por-workspace)
- [7. MCPs utilizados neste tutorial](#7-mcps-utilizados-neste-tutorial)
- [8. Microsoft Learn MCP](#8-microsoft-learn-mcp)
- [9. Azure MCP](#9-azure-mcp)
- [10. Playwright MCP](#10-playwright-mcp)
- [11. Chrome DevTools MCP](#11-chrome-devtools-mcp)
- [12. Power BI MCP](#12-power-bi-mcp)
- [13. Como os MCPs se complementam](#13-como-os-mcps-se-complementam)
- [14. Laboratório: resolvendo um problema real com múltiplos MCPs](#14-laboratório-resolvendo-um-problema-real-com-múltiplos-mcps)
- [15. Passo 1 — Entender](#15-passo-1--entender)
- [16. Passo 2 — Investigar](#16-passo-2--investigar)
- [17. Passo 3 — Reproduzir](#17-passo-3--reproduzir)
- [18. Passo 4 — Diagnosticar](#18-passo-4--diagnosticar)
- [19. Passo 5 — Corrigir](#19-passo-5--corrigir)
- [20. Passo 6 — Validar](#20-passo-6--validar)
- [21. Resultado final do laboratório](#21-resultado-final-do-laboratório)
- [22. Boas práticas de uso de MCP](#22-boas-práticas-de-uso-de-mcp)
- [23. Troubleshooting](#23-troubleshooting)
- [24. Desafios para continuar aprendendo](#24-desafios-para-continuar-aprendendo)
- [25. Referências](#25-referências)

---

# 1. Introdução

O GitHub Copilot é capaz de compreender o código-fonte, os arquivos do projeto e o contexto fornecido pelo desenvolvedor.

Porém, muitas tarefas reais de desenvolvimento exigem acesso a informações e ferramentas que estão fora do repositório, como:

- documentação oficial;
- recursos em cloud;
- configurações de infraestrutura;
- aplicações em execução;
- navegador;
- logs;
- métricas;
- sistemas corporativos.

É justamente neste ponto que o **Model Context Protocol (MCP)** se torna importante.

O MCP permite conectar o GitHub Copilot a ferramentas e fontes externas, ampliando significativamente o que o agente consegue fazer.

Neste tutorial, utilizaremos os seguintes MCPs:

- **Microsoft Learn**
- **Azure**
- **Playwright**
- **Chrome DevTools**
- **Power BI**

O principal laboratório deste repositório utiliza quatro deles em conjunto:

```text
Entender
   ↓
Investigar
   ↓
Reproduzir
   ↓
Diagnosticar
   ↓
Corrigir
   ↓
Validar
```

A ideia é sair de uma pergunta simples para um fluxo completo de resolução de problemas.

---

# 2. O que você aprenderá

Ao final deste tutorial, você deverá ser capaz de:

- entender o que é MCP;
- compreender a arquitetura Host → Client → Server;
- configurar MCP Servers no Visual Studio Code;
- validar se um MCP está funcionando;
- utilizar ferramentas externas por meio do GitHub Copilot;
- consultar documentação oficial com o Microsoft Learn MCP;
- interagir com recursos Azure;
- reproduzir jornadas com o Playwright MCP;
- diagnosticar problemas com o Chrome DevTools MCP;
- compreender os principais cenários do Power BI MCP;
- combinar múltiplos MCPs em uma mesma tarefa;
- executar um fluxo completo de investigação, diagnóstico, correção e validação.

---

# 3. Pré-requisitos

Antes de começar, recomenda-se possuir:

- Visual Studio Code atualizado;
- GitHub Copilot habilitado;
- acesso ao GitHub Copilot Chat;
- suporte a Agent Mode;
- Node.js instalado, quando exigido pelo MCP;
- `npx` disponível no ambiente;
- navegador Google Chrome;
- uma conta Azure para os exemplos envolvendo Azure;
- permissões adequadas para acessar os recursos utilizados.

> Os requisitos podem variar de acordo com cada MCP Server.

Também é importante compreender que alguns MCPs acessam sistemas protegidos por autenticação e permissões.

Por isso:

- utilize apenas os acessos necessários;
- evite executar ações destrutivas sem revisão;
- verifique quais ferramentas o servidor disponibiliza;
- siga o princípio do menor privilégio.

---

# 4. O que é MCP

**MCP** significa **Model Context Protocol**.

De forma simplificada, MCP é um padrão utilizado para conectar aplicações baseadas em modelos de IA a ferramentas, sistemas e fontes externas.

Sem MCP, o agente trabalha principalmente com:

- conhecimento do modelo;
- código;
- arquivos do projeto;
- contexto fornecido pelo usuário.

Com MCP, o agente pode ganhar novas capacidades.

Por exemplo:

```text
GitHub Copilot
      │
      ├── Microsoft Learn
      ├── Azure
      ├── Playwright
      ├── Chrome DevTools
      └── Power BI
```

O MCP não substitui o GitHub Copilot.

O Copilot continua sendo o agente responsável por:

- interpretar o pedido;
- planejar a tarefa;
- selecionar ferramentas;
- analisar resultados;
- tomar decisões;
- sugerir ou aplicar alterações.

O MCP fornece as capacidades externas.

---

## 4.1. O problema que o MCP resolve

Imagine o seguinte pedido:

```text
Descubra por que minha aplicação está lenta.
```

Sem ferramentas externas, o Copilot pode:

- analisar o código;
- levantar hipóteses;
- sugerir boas práticas;
- revisar configurações presentes no repositório.

Mas ele pode não saber:

- o que está acontecendo no Azure;
- quais erros estão ocorrendo no navegador;
- se o problema realmente pode ser reproduzido;
- quais requisições estão lentas;
- quais métricas estão degradadas.

Com MCP, o fluxo pode ser ampliado:

```text
                    ┌── Microsoft Learn
                    │
GitHub Copilot ─────┼── Azure
                    │
                    ├── Playwright
                    │
                    └── Chrome DevTools
```

Assim, o Copilot pode combinar:

```text
Código
+
Documentação
+
Ambiente
+
Comportamento real
+
Diagnóstico técnico
```

---

## 4.2. Arquitetura do MCP

Uma visão simplificada:

```text
Host
  │
  └── MCP Client
          │
          └── MCP Server
                  │
                  └── Serviço externo
```

No contexto deste tutorial:

```text
VS Code
  │
  └── GitHub Copilot
          │
          └── MCP Server
                  │
                  ├── Microsoft Learn
                  ├── Azure
                  ├── Browser
                  └── Power BI
```

Podemos representar os componentes da seguinte forma:

| Componente | Neste tutorial |
|---|---|
| Host | Visual Studio Code |
| MCP Client | GitHub Copilot |
| MCP Server | Learn, Azure, Playwright, Chrome DevTools e Power BI |
| Sistema externo | Documentação, Azure, navegador e Power BI |

---

## 4.3. Conceitos importantes

### Tools

São ferramentas que o agente pode executar.

Exemplos:

- abrir uma página;
- clicar em um botão;
- consultar um recurso;
- buscar uma documentação;
- analisar uma requisição.

### Resources

São informações que podem ser disponibilizadas como contexto.

### Prompts

Alguns MCP Servers podem fornecer prompts reutilizáveis.

### Servidor local

Executado na própria máquina.

Exemplo conceitual:

```text
VS Code
   ↓
Processo local
   ↓
Ferramenta externa
```

### Servidor remoto

Executado em outro ambiente e acessado por uma URL.

```text
VS Code
   ↓
Internet
   ↓
MCP Server remoto
```

### Permissões

O MCP não deve ser entendido como uma forma de contornar permissões.

Se o usuário não possui acesso a determinado recurso, o MCP também deve respeitar essas restrições.

---

# 5. Como o GitHub Copilot utiliza um MCP

Um fluxo simplificado pode ser representado assim:

```text
Usuário
   ↓
Prompt
   ↓
GitHub Copilot
   ↓
Seleciona uma ferramenta
   ↓
MCP Server
   ↓
Sistema externo
   ↓
Resultado
   ↓
GitHub Copilot continua a tarefa
```

Por exemplo:

```text
Descubra por que minha aplicação está lenta.
```

O Copilot pode construir um plano como:

```text
1. Consultar documentação oficial
2. Verificar o ambiente
3. Abrir a aplicação
4. Reproduzir o problema
5. Analisar o navegador
6. Corrigir o código
7. Testar novamente
```

O ponto principal é:

> O MCP amplia as capacidades do agente.

---

# 6. Como configurar MCP no VS Code

Existem diferentes formas de adicionar MCP Servers ao Visual Studio Code.

A disponibilidade exata das opções pode variar conforme:

- versão do VS Code;
- MCP Server utilizado;
- políticas da organização;
- ambiente local.

---

## 6.1. Formas de adicionar um MCP

As formas mais comuns incluem:

- instalação por meio da interface do VS Code;
- MCP Gallery;
- configuração manual;
- configuração por workspace;
- configuração global;
- comandos fornecidos pelo próprio projeto do MCP Server.

Em ambientes corporativos, alguns MCPs podem ser:

- previamente aprovados;
- disponibilizados por política;
- configurados pela organização;
- limitados a determinados usuários.

---

## 6.2. O arquivo `.vscode/mcp.json`

Uma configuração específica do projeto pode ser mantida em:

```text
.vscode/
└── mcp.json
```

Um exemplo conceitual:

```json
{
  "servers": {
    "meu-servidor": {
      "command": "comando",
      "args": [
        "argumento-1",
        "argumento-2"
      ]
    }
  }
}
```

Dependendo do MCP, também podem existir configurações com:

- URL;
- transporte;
- variáveis de ambiente;
- autenticação;
- argumentos adicionais.

> Sempre utilize a documentação oficial do MCP Server para obter a configuração atual.

---

## 6.3. Como validar a configuração

Após adicionar um MCP:

1. confirme que o servidor aparece no VS Code;
2. verifique se ele inicia corretamente;
3. confirme quais ferramentas estão disponíveis;
4. execute um prompt simples;
5. verifique se o Copilot utilizou o MCP esperado.

Exemplo:

```text
Consulte a documentação oficial da Microsoft e explique o que é Azure App Service.
```

---

## 6.4. Configuração global ou por workspace

Existem dois cenários principais.

### Configuração global

O MCP fica disponível para vários projetos.

```text
Configuração do usuário
        ↓
Múltiplos workspaces
```

### Configuração por workspace

O MCP fica associado a um repositório específico.

```text
Repositório
   ↓
.vscode/mcp.json
```

Para um repositório educacional como este, a configuração por workspace pode ser interessante porque:

- documenta o ambiente;
- facilita o onboarding;
- aproxima a configuração do tutorial;
- torna o exemplo mais reproduzível.

---

# 7. MCPs utilizados neste tutorial

| MCP | Principal finalidade |
|---|---|
| Microsoft Learn | Documentação e conhecimento oficial |
| Azure | Interação com recursos e serviços Azure |
| Playwright | Automação e interação com aplicações web |
| Chrome DevTools | Diagnóstico técnico no navegador |
| Power BI | Interação com dados, modelos e cenários de Power BI |

Todos os MCPs serão apresentados usando uma estrutura semelhante:

```text
O que é
Para que serve
Quando utilizar
Pré-requisitos
Como configurar
Como validar
Exemplos de prompts
Principais cuidados
Documentação
```

---

# 8. Microsoft Learn MCP

## O que é

O Microsoft Learn MCP permite utilizar conteúdo oficial da Microsoft como fonte de conhecimento para o agente.

Ele é especialmente útil quando a tarefa envolve tecnologias Microsoft.

---

## Para que serve

Pode ser utilizado para:

- consultar documentação oficial;
- entender conceitos;
- aprender sobre serviços;
- pesquisar boas práticas;
- obter exemplos;
- entender recomendações arquiteturais;
- apoiar troubleshooting.

---

## Quando utilizar

Exemplos:

```text
Como funciona o Azure App Service?
```

```text
Quais mecanismos existem para diagnosticar problemas de performance?
```

```text
Quais boas práticas são recomendadas para este serviço?
```

```text
Qual é a diferença entre estas duas opções?
```

---

## Como configurar

A configuração pode variar conforme a forma de distribuição utilizada no seu ambiente.

Siga a documentação oficial do Microsoft Learn MCP e adicione o servidor ao VS Code.

Depois:

1. confirme que o servidor está ativo;
2. verifique as ferramentas disponibilizadas;
3. abra o GitHub Copilot Chat;
4. execute um prompt que exija consulta à documentação.

---

## Como validar

Exemplo:

```text
Consulte o Microsoft Learn e explique o que é Azure App Service.
```

---

## Exemplos de prompts

### Aprendizado

```text
Consulte a documentação oficial e explique como funciona o Azure App Service.
```

### Arquitetura

```text
Com base na documentação oficial, quais serviços são recomendados para hospedar uma aplicação web?
```

### Troubleshooting

```text
Pesquise na documentação oficial quais mecanismos existem para investigar problemas de performance no Azure App Service.
```

### Boas práticas

```text
Consulte o Microsoft Learn e liste as principais boas práticas para este serviço.
```

---

# 9. Azure MCP

## O que é

O Azure MCP permite que o GitHub Copilot interaja com recursos e serviços Azure por meio de ferramentas disponibilizadas ao agente.

---

## Para que serve

Dependendo das capacidades disponíveis, pode ajudar a:

- identificar recursos;
- consultar configurações;
- investigar serviços;
- entender a arquitetura implantada;
- buscar informações operacionais;
- apoiar troubleshooting;
- trabalhar com recursos Azure por linguagem natural.

---

## Pré-requisitos

Você pode precisar de:

- uma conta Azure;
- autenticação válida;
- subscription;
- permissões adequadas;
- acesso aos recursos utilizados.

---

## Como configurar

A configuração depende da versão e da forma de distribuição do Azure MCP adotada no ambiente.

Em geral:

1. adicione o Azure MCP ao VS Code;
2. conclua o fluxo de autenticação;
3. confirme a subscription utilizada;
4. valide as permissões;
5. verifique quais ferramentas estão disponíveis.

---

## Como validar

Exemplo:

```text
Liste os recursos Azure disponíveis na subscription utilizada neste laboratório.
```

---

## Exemplos de prompts

```text
Quais recursos estão relacionados a esta aplicação?
```

```text
Investigue possíveis problemas relacionados ao ambiente.
```

```text
Verifique quais informações estão disponíveis para diagnosticar esta aplicação.
```

```text
Explique a arquitetura dos recursos encontrados.
```

---

## Cuidados

O acesso do MCP deve respeitar as permissões do usuário.

Antes de permitir ações de escrita:

- revise a operação;
- confirme o recurso afetado;
- verifique o ambiente;
- evite ações destrutivas sem necessidade.

---

# 10. Playwright MCP

## O que é

O Playwright MCP permite que o agente interaja com aplicações web por meio de um navegador.

Em termos simples:

> O Playwright MCP dá olhos e mãos ao Copilot dentro do navegador.

---

## Para que serve

Pode ser utilizado para:

- abrir páginas;
- navegar;
- clicar em botões;
- preencher formulários;
- validar textos;
- executar jornadas;
- reproduzir bugs;
- explorar uma aplicação;
- validar comportamentos.

---

## Exemplo

```text
Abra a aplicação, faça login, adicione um produto ao carrinho e tente finalizar a compra.
```

O agente pode executar:

```text
Abrir aplicação
      ↓
Login
      ↓
Produtos
      ↓
Carrinho
      ↓
Checkout
```

---

## Como configurar

A configuração depende da forma de instalação adotada.

Em geral:

1. instale ou adicione o Playwright MCP;
2. confirme os pré-requisitos do ambiente;
3. inicie o servidor;
4. verifique se as ferramentas aparecem no VS Code;
5. execute um primeiro fluxo simples.

---

## Primeira automação

```text
Abra a aplicação e navegue até a página de produtos.
```

Depois:

```text
Adicione um produto ao carrinho e tente finalizar a compra.
```

---

## Exemplos de prompts

```text
Abra a aplicação e verifique se a página inicial carrega corretamente.
```

```text
Faça login com o usuário de teste e navegue até o dashboard.
```

```text
Execute o fluxo de checkout e documente qualquer comportamento inesperado.
```

```text
Reproduza o erro descrito nesta issue.
```

---

## Playwright MCP versus testes Playwright

É importante diferenciar exploração agentic de testes automatizados versionados.

```text
Exploração com MCP
        ↓
Entender a jornada
        ↓
Confirmar o comportamento
        ↓
Gerar teste automatizado
        ↓
Versionar o teste
```

O MCP pode ajudar a explorar e compreender uma jornada.

Depois, essa jornada pode ser transformada em um teste automatizado permanente.

---

# 11. Chrome DevTools MCP

## O que é

O Chrome DevTools MCP permite que o agente utilize capacidades de diagnóstico do navegador.

Em termos simples:

> O Chrome DevTools MCP dá ferramentas de investigação técnica ao Copilot.

---

## Para que serve

Pode ser utilizado para:

- investigar erros no console;
- analisar requisições de rede;
- verificar falhas;
- estudar o comportamento da página;
- investigar performance;
- coletar evidências técnicas.

---

## Exemplo

```text
Analise esta página e descubra por que ela está lenta.
```

Um possível resultado:

```text
GET /api/products
Tempo: 6,2 segundos
Payload: 15 MB
```

---

## Como configurar

Em geral:

1. adicione o Chrome DevTools MCP ao VS Code;
2. confirme os pré-requisitos;
3. valide o navegador utilizado;
4. inicie o MCP Server;
5. confirme as ferramentas disponíveis.

---

## Primeiro diagnóstico

```text
Analise a página e identifique erros no console e nas requisições de rede.
```

---

## Exemplos de prompts

```text
Verifique se existem erros no console.
```

```text
Analise as principais requisições de rede e identifique chamadas lentas.
```

```text
Investigue a performance desta página.
```

```text
Identifique a causa provável do problema observado durante o checkout.
```

---

## Playwright versus Chrome DevTools

| Playwright | Chrome DevTools |
|---|---|
| Reproduz a jornada | Investiga tecnicamente |
| Interage com a interface | Analisa o navegador |
| Simula o usuário | Busca a causa |
| Valida comportamento | Analisa evidências |

Uma forma simples de lembrar:

> Playwright mostra que o problema aconteceu. Chrome DevTools ajuda a explicar por que aconteceu.

---

# 12. Power BI MCP

O Power BI MCP não faz parte do laboratório principal deste tutorial, mas é importante conhecê-lo.

---

## Para que serve

Dependendo do MCP e das capacidades disponibilizadas, pode apoiar cenários como:

- consulta de dados;
- análise de informações;
- exploração de modelos;
- geração de insights;
- apoio ao desenvolvimento e manutenção de modelos semânticos.

---

## Exemplos de prompts

```text
Quais foram as principais tendências de vendas no último trimestre?
```

```text
Analise este modelo e identifique possíveis oportunidades de melhoria.
```

```text
Explique os principais indicadores disponíveis neste conjunto de dados.
```

---

## Como configurar

Siga a documentação específica do Power BI MCP utilizado no seu ambiente.

Em ambientes corporativos, confirme:

- autenticação;
- permissões;
- workspace;
- dados acessíveis;
- operações permitidas.

---

# 13. Como os MCPs se complementam

Os MCPs podem ser utilizados isoladamente.

Porém, o maior valor aparece quando diferentes capacidades são combinadas.

```text
Microsoft Learn
      │
      │ Conhecimento
      ▼
Azure
      │
      │ Ambiente
      ▼
Playwright
      │
      │ Comportamento
      ▼
Chrome DevTools
      │
      │ Diagnóstico
      ▼
GitHub Copilot
      │
      │ Correção
      ▼
Playwright + Chrome DevTools
      │
      ▼
Validação
```

Podemos resumir assim:

| Pergunta | MCP |
|---|---|
| Como isso deveria funcionar? | Microsoft Learn |
| O que está acontecendo no ambiente? | Azure |
| O usuário consegue executar a jornada? | Playwright |
| Por que a aplicação está apresentando esse comportamento? | Chrome DevTools |

---

# 14. Laboratório: resolvendo um problema real com múltiplos MCPs

Agora vamos combinar os conceitos apresentados anteriormente.

---

## Cenário

Uma aplicação web está hospedada no Azure.

Os usuários relatam o seguinte problema:

> A tela de produtos está lenta e, algumas vezes, o checkout não funciona.

Nosso objetivo será:

```text
Entender
   ↓
Investigar
   ↓
Reproduzir
   ↓
Diagnosticar
   ↓
Corrigir
   ↓
Validar
```

Cada etapa terá um objetivo claro.

---

# 15. Passo 1 — Entender

## Objetivo

```text
Compreender o contexto, o problema e o objetivo.
```

---

## MCP principal

**Microsoft Learn**

---

## Por que utilizar

Antes de investigar o ambiente, precisamos compreender:

- a tecnologia envolvida;
- como o serviço funciona;
- quais mecanismos de diagnóstico existem;
- quais boas práticas são recomendadas;
- quais caminhos de investigação fazem sentido.

---

## O que fazer

1. descreva o problema;
2. identifique a tecnologia envolvida;
3. consulte a documentação oficial;
4. entenda os mecanismos disponíveis;
5. construa um plano inicial de investigação.

---

## Prompt sugerido

```text
Nossa aplicação está hospedada no Azure App Service e apresenta
problemas de performance.

Consulte a documentação oficial da Microsoft e explique quais
recursos e mecanismos podem ser utilizados para investigar
problemas de performance nesse cenário.
```

---

## Resultado esperado

```text
Contexto
+
Conhecimento
+
Plano inicial de investigação
```

---

# 16. Passo 2 — Investigar

## Objetivo

```text
Buscar evidências no ambiente real.
```

---

## MCP principal

**Azure MCP**

---

## Por que utilizar

Depois de entender como a tecnologia funciona, precisamos descobrir:

- quais recursos existem;
- como o ambiente está estruturado;
- quais configurações estão ativas;
- quais sinais podem indicar o problema.

---

## Atividades

- identificar recursos;
- entender a arquitetura;
- consultar configurações;
- analisar informações disponíveis;
- buscar sinais relacionados ao problema.

---

## Prompt sugerido

```text
Identifique os recursos Azure relacionados a esta aplicação
e procure informações que possam ajudar a investigar
o problema de performance.
```

---

## Resultado esperado

Um conjunto de evidências do ambiente.

```text
Recursos
+
Configurações
+
Sinais
+
Hipóteses
```

---

# 17. Passo 3 — Reproduzir

## Objetivo

```text
Confirmar que o problema realmente ocorre.
```

---

## MCP principal

**Playwright MCP**

---

## Jornada

```text
Abrir aplicação
      ↓
Acessar produtos
      ↓
Selecionar produto
      ↓
Adicionar ao carrinho
      ↓
Finalizar compra
```

---

## Prompt sugerido

```text
Abra a aplicação, acesse a página de produtos,
adicione um produto ao carrinho e tente finalizar a compra.

Documente qualquer comportamento inesperado.
```

---

## O que observar

- a página abriu?
- a navegação funcionou?
- algum botão falhou?
- a aplicação ficou lenta?
- alguma etapa não foi concluída?
- o problema é reproduzível?

---

## Resultado esperado

```text
Problema reproduzido ✓
```

ou:

```text
Problema não reproduzido
      ↓
Coletar mais contexto
```

---

# 18. Passo 4 — Diagnosticar

## Objetivo

```text
Descobrir a causa raiz.
```

---

## MCP principal

**Chrome DevTools MCP**

Possível apoio:

**Azure MCP**

---

## O que investigar

- console;
- requisições de rede;
- erros;
- chamadas lentas;
- payloads grandes;
- falhas de API;
- problemas de performance;
- correlação com o ambiente.

---

## Prompt sugerido

```text
Analise a aplicação usando as ferramentas do Chrome DevTools.

Verifique erros, requisições de rede e problemas de performance
e identifique a causa provável do problema.
```

---

## Possível resultado

```text
POST /api/checkout
Tempo: 8,4 segundos

Erro:
Timeout ao chamar o serviço de checkout
```

ou:

```text
GET /api/products
Payload: 15 MB
Tempo: 6,2 segundos
```

---

## Resultado esperado

```text
Sintoma
   ↓
Evidência
   ↓
Causa provável
   ↓
Causa raiz
```

---

# 19. Passo 5 — Corrigir

## Objetivo

```text
Aplicar a solução.
```

---

## Protagonista

**GitHub Copilot**

Os MCPs fornecem as evidências.

O Copilot utiliza:

```text
Código
+
Documentação
+
Ambiente
+
Comportamento
+
Diagnóstico
```

---

## Prompt sugerido

```text
Com base nas evidências coletadas,
identifique a causa raiz e implemente uma correção.
```

---

## Possível correção

Imagine que o problema seja:

```text
A página de produtos carrega todos os registros de uma única vez.
```

Uma possível solução:

- implementar paginação;
- reduzir o payload;
- melhorar o tratamento de erros;
- revisar timeouts;
- melhorar feedback visual;
- criar testes para o cenário.

---

## Fluxo recomendado

```text
Diagnóstico
   ↓
Plano de correção
   ↓
Alteração no código
   ↓
Revisão
   ↓
Explicação da solução
```

---

# 20. Passo 6 — Validar

## Objetivo

```text
Comprovar que a solução realmente resolveu o problema.
```

---

## MCPs utilizados

### Playwright

Para:

```text
Validação funcional
```

### Chrome DevTools

Para:

```text
Validação técnica e de performance
```

### Azure MCP

Opcionalmente, para:

```text
Verificação do ambiente
```

---

## Primeiro: validar funcionalmente

```text
Execute novamente o fluxo completo de compra e confirme
que o usuário consegue finalizar a jornada.
```

---

## Depois: validar tecnicamente

```text
Analise novamente a aplicação e compare o comportamento
de rede e performance com o cenário anterior.
```

---

## Fluxo

```text
Executar novamente a jornada
            ↓
Validar comportamento
            ↓
Analisar performance
            ↓
Comparar antes × depois
```

---

## Exemplo de comparação

### Antes

```text
/api/products     6,2s
/api/checkout     8,4s
Fluxo             Falha
```

### Depois

```text
/api/products     0,8s
/api/checkout     1,2s
Fluxo             Sucesso
```

---

# 21. Resultado final do laboratório

| Etapa | Ferramenta | Resultado |
|---|---|---|
| Entender | Microsoft Learn | Conhecimento |
| Investigar | Azure | Evidências do ambiente |
| Reproduzir | Playwright | Problema confirmado |
| Diagnosticar | Chrome DevTools | Causa identificada |
| Corrigir | GitHub Copilot | Solução implementada |
| Validar | Playwright + Chrome DevTools | Solução comprovada |

O fluxo completo:

```text
Microsoft Learn
      ↓
Entender

Azure
      ↓
Investigar

Playwright
      ↓
Reproduzir

Chrome DevTools
      ↓
Diagnosticar

GitHub Copilot
      ↓
Corrigir

Playwright + Chrome DevTools
      ↓
Validar
```

A principal mensagem deste tutorial é:

> **O Copilot deixa de apenas conhecer o código e passa a investigar o sistema.**

---

# 22. Boas práticas de uso de MCP

## Habilite apenas o necessário

Quanto mais ferramentas disponíveis, maior a superfície de ação do agente.

Prefira habilitar apenas os MCPs necessários para a tarefa.

---

## Use o princípio do menor privilégio

O agente deve possuir apenas as permissões necessárias.

Evite:

- permissões administrativas sem necessidade;
- acesso amplo a ambientes produtivos;
- credenciais compartilhadas;
- execução irrestrita.

---

## Revise ações de escrita

Antes de alterar:

- infraestrutura;
- recursos cloud;
- dados;
- configurações;
- sistemas externos;

confirme:

- o recurso;
- o ambiente;
- o impacto;
- a reversibilidade.

---

## Proteja credenciais

Nunca armazene secrets diretamente no repositório.

Prefira:

- variáveis de ambiente;
- mecanismos de autenticação;
- secret stores;
- credenciais temporárias;
- identidades gerenciadas, quando aplicável.

---

## Diferencie leitura de escrita

Operações de leitura tendem a ser mais seguras para:

- exploração;
- diagnóstico;
- aprendizado;
- investigação.

Operações de escrita exigem mais atenção.

---

## Valide o MCP Server

Antes de adotar um servidor:

- verifique a origem;
- revise a documentação;
- entenda as ferramentas disponibilizadas;
- valide requisitos de segurança;
- confirme se é adequado ao ambiente corporativo.

---

# 23. Troubleshooting

## MCP não aparece

Verifique:

- configuração;
- versão do VS Code;
- políticas da organização;
- arquivo `mcp.json`;
- instalação.

---

## MCP não inicia

Verifique:

- comando;
- argumentos;
- Node.js;
- `npx`;
- dependências;
- variáveis de ambiente;
- logs.

---

## As ferramentas não aparecem

Verifique:

- se o servidor está ativo;
- se o processo iniciou corretamente;
- se o MCP expõe ferramentas;
- se a configuração está correta;
- se existem restrições organizacionais.

---

## Azure não consegue acessar recursos

Verifique:

- autenticação;
- subscription;
- tenant;
- RBAC;
- permissões;
- escopo.

---

## O navegador não inicia

Verifique:

- navegador instalado;
- dependências;
- permissões;
- processo existente;
- parâmetros de inicialização.

---

## O Copilot não utiliza o MCP esperado

Tente tornar o prompt mais explícito.

Exemplo:

```text
Use o Microsoft Learn MCP para consultar a documentação oficial.
```

ou:

```text
Use o Playwright MCP para reproduzir este fluxo no navegador.
```

ou:

```text
Use o Chrome DevTools MCP para analisar as requisições de rede.
```

---

# 24. Desafios para continuar aprendendo

## Desafio 1 — Explorar outro serviço Azure

Utilize:

- Microsoft Learn;
- Azure MCP.

Objetivo:

```text
Entender um novo serviço
      ↓
Investigar um ambiente real
```

---

## Desafio 2 — Criar uma nova jornada

Utilize o Playwright MCP.

Exemplo:

```text
Login
  ↓
Busca
  ↓
Detalhes
  ↓
Ação final
```

---

## Desafio 3 — Investigar performance

Utilize o Chrome DevTools MCP para:

- analisar rede;
- encontrar uma requisição lenta;
- identificar a causa;
- propor uma melhoria.

---

## Desafio 4 — Transformar exploração em teste

Fluxo:

```text
Explorar com MCP
      ↓
Confirmar jornada
      ↓
Gerar teste Playwright
      ↓
Versionar
```

---

## Desafio 5 — Deixar o agente planejar

Dê apenas o objetivo:

```text
Descubra por que esta aplicação está lenta
e proponha uma correção.
```

Observe:

- quais ferramentas o agente escolhe;
- em qual ordem;
- quais evidências coleta;
- como constrói a solução.

---

# 25. Referências

Consulte sempre a documentação oficial e atualizada de cada tecnologia utilizada.

Sugestões:

- documentação do Model Context Protocol;
- documentação do MCP no Visual Studio Code;
- documentação do GitHub Copilot;
- Microsoft Learn MCP;
- Azure MCP;
- Playwright MCP;
- Chrome DevTools MCP;
- Power BI MCP.

---

# Conclusão

O MCP permite transformar o GitHub Copilot em algo muito mais poderoso do que um assistente que conhece apenas o código.

Com os MCPs apresentados neste tutorial, podemos conectar o agente a diferentes partes do ciclo de desenvolvimento.

```text
Microsoft Learn
      ↓
Conhecimento

Azure
      ↓
Ambiente

Playwright
      ↓
Comportamento

Chrome DevTools
      ↓
Diagnóstico

GitHub Copilot
      ↓
Correção

Playwright + Chrome DevTools
      ↓
Validação
```

O resultado é um fluxo mais completo:

```text
Entender
   ↓
Investigar
   ↓
Reproduzir
   ↓
Diagnosticar
   ↓
Corrigir
   ↓
Validar
```

> **O Copilot deixa de apenas conhecer o código e passa a investigar o sistema.**
