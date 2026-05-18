# SmartHub Assistant — Chatbot ChargeGrid Intelligence

> Camada conversacional do produto ChargeGrid SmartHub, projetada para apoiar o operador comercial de eletropostos GoodWe na resolução de dúvidas operacionais de primeiro nível.

**EV Challenge 2026 · FIAP × GoodWe**
Disciplina: **Prompt and Artificial Intelligence** · 1º Ano de Ciência da Computação · Semestre 2026.1
Professor: Jorge Luiz Gomes

---

## Índice

1. [Sobre o projeto](#sobre-o-projeto)
2. [Integrantes do grupo](#integrantes-do-grupo)
3. [Problema abordado](#problema-abordado)
4. [Contexto escolhido](#contexto-escolhido)
5. [Persona atendida](#persona-atendida)
6. [Tecnologias utilizadas](#tecnologias-utilizadas)
7. [Justificativa técnica](#justificativa-técnica)
8. [Arquitetura e fluxograma](#arquitetura-e-fluxograma)
9. [System prompt](#system-prompt)
10. [Modelo de teste](#modelo-de-teste)
11. [Como executar (previsto para Sprint 2)](#como-executar-previsto-para-sprint-2)
12. [Roadmap de evolução](#roadmap-de-evolução)
13. [Limitações conhecidas](#limitações-conhecidas)
14. [Diário de iteração do system prompt](#diário-de-iteração-do-system-prompt)
15. [Estrutura do repositório](#estrutura-do-repositório)
16. [Papéis no grupo](#papéis-no-grupo)
17. [Demonstração em vídeo](#demonstração-em-vídeo)
18. [Licença](#licença)

---

## Sobre o projeto

O **SmartHub Assistant** é o canal conversacional do produto **ChargeGrid SmartHub**, uma plataforma de gestão operacional de eletropostos comerciais desenvolvida pelo grupo no escopo do EV Challenge 2026 (FIAP × GoodWe).

Diferente de um chatbot genérico, este assistente é projetado como **uma das camadas do produto** que o grupo está construindo de forma multidisciplinar ao longo do ano. A arquitetura completa do SmartHub é apresentada em paralelo na disciplina de Pensamento Computacional e Automação com Python, e este chatbot ocupa especificamente a função de **interface de Tier 1 para o operador comercial**, conforme indicado no slide 6 do deck conceitual do produto ("Decisão com dados — IA para prever picos, otimizar potência e apoiar o gestor").

A entrega da Sprint 1 desta disciplina cobre o **planejamento técnico e a documentação** do chatbot. A implementação funcional em Python será entregue na Sprint 2.

---

## Integrantes do grupo

| Nome completo | RM |
|---------------|-----|
| João Pedro do Vale Quagliano | 570233 |
| Leticia Aiko Okano | 571988 |
| Thiago Calazans Luz Nakano | 569151 |
| Enzo Scattolin Furtado | 570824 |
| Guilherme De Lucena Fontes | 569658 |
| Matheus Levi Dagel | 571961 |

Turma: **1CCPH** · Curso: **Ciência da Computação**

---

## Problema abordado

O documento oficial do Challenge (seção 2.1) caracteriza o problema central do contexto comercial como a **ausência de mecanismos integrados em eletropostos para orquestrar potência, registrar ciclos, faturar e comunicar**. Esse problema se manifesta no dia a dia operacional em quatro frentes principais:

1. **Orquestração de potência** — distribuir a energia disponível entre múltiplos pontos de recarga sem estourar a demanda contratada da instalação.
2. **Registro auditável de ciclos** — cada sessão precisa ser rastreável (kWh entregues, duração, identificação do veículo, tarifa aplicada).
3. **Faturamento** — emissão correta de cobrança, integração com meios de pagamento, gestão de estornos.
4. **Comunicação operacional** — atender dúvidas recorrentes do operador sobre disponibilidade, status de sessão e falhas reportadas.

A frente 4 é especificamente onde o SmartHub Assistant atua. O chatbot **não substitui** o painel gestor do SmartHub nem o suporte humano da GoodWe — ele atua como filtro inteligente de Tier 1, resolvendo dúvidas operacionais recorrentes sem necessidade de abertura de ticket e orientando o escalonamento correto quando o problema sai do escopo.

---

## Contexto escolhido

O grupo selecionou o **Contexto A — ChargeGrid Intelligence (uso comercial)**, em detrimento do Contexto B (EV ChargeOps — uso condominial), por três razões técnicas:

1. **Alinhamento multidisciplinar.** O produto ChargeGrid SmartHub está sendo desenvolvido pelo grupo de forma transversal nas demais disciplinas do 1º ano (Pensamento Computacional, Modelagem Matemática, Modelagem Linear para ML, Soluções em Energias Renováveis). Manter o mesmo contexto operacional em todas as disciplinas reduz retrabalho e fortalece a coerência narrativa do produto.
2. **Maior densidade de jobs-to-be-done.** O contexto comercial concentra um leque mais amplo de operações repetitivas (configuração de tarifa, conferência de sessão, leitura de KPI, troubleshooting básico) que se beneficiam diretamente de um agente conversacional bem condicionado.
3. **Modelo de receita verificável.** O contexto comercial possui um modelo de monetização claro (cobrança por kWh + taxa de serviço + taxa de ociosidade), o que permite validar empiricamente o impacto do chatbot em métricas observáveis — redução de tempo de resposta, queda de tickets de baixa complexidade, taxa de retenção do operador.

---

## Persona atendida

**Operador Comercial** — profissional responsável pela gestão diária da operação de uma estação de recarga em ambiente comercial (varejo, frotas, shoppings, estacionamentos, supermercados, postos de combustível com vagas EV).

**Jobs-to-be-done do operador atendidos pelo SmartHub Assistant:**

- Esclarecer dúvidas sobre o funcionamento dos quatro pilares do ChargeGrid (controle de demanda, protocolos abertos, tarifação e pagamento, IA aplicada).
- Interpretar relatórios e KPIs visíveis no painel gestor (potência entregue, tempo de ociosidade, ticket médio, taxa de conversão, falhas reportadas).
- Executar Procedimentos Operacionais Padrão (POP) recorrentes — abertura, encerramento, pausa, retomada e auditoria de sessões; configuração simples de tarifa; conferência de recibos.
- Reconhecer rapidamente situações que exigem escalonamento ao suporte técnico humano da GoodWe.

**Por que esta persona e não o técnico de campo?** O técnico de campo também é uma persona plausível no Contexto A, mas seu trabalho depende majoritariamente de inspeção física do equipamento — tarefa que um chatbot Tier 1 não cobre adequadamente. O operador comercial, ao contrário, opera quase exclusivamente via painel digital, o que faz dele a persona ideal para um assistente conversacional.

---

## Tecnologias utilizadas

### Stack escolhido

| Camada | Tecnologia | Versão |
|--------|-----------|--------|
| Modelo de Linguagem (LLM) | **Google Gemini 2.0 Flash** (via Google AI Studio) | API v1 |
| Orquestração de prompts | **LangChain** | `>=0.2.0` |
| Linguagem | **Python** | `>=3.10` |
| Ambiente de execução | Google Colab (primário) · VS Code local (secundário) | — |
| Versionamento | Git + GitHub (público) | — |
| Interface (Sprint 2) | **Streamlit** | `>=1.30` |
| Vector store (Sprint 2 — opcional para RAG) | **ChromaDB** | `>=0.4` |

### Comparativo — por que Gemini 2.0 Flash

A escolha do LLM foi feita comparando quatro opções aprovadas pelo regulamento (seção 6.1 do documento oficial — todas com custo zero):

| Opção | Prós | Contras | Decisão |
|-------|------|---------|---------|
| **Gemini 2.0 Flash** | Free tier real (60 req/min, 1M tokens/dia); qualidade alta em português brasileiro; function calling nativo; multimodal (preparado para imagem em Sprint 2) | Lock-in no Google AI Studio; latência variável entre regiões | **✅ Escolhido** |
| Groq + Llama 3.1 70B | Latência extremamente baixa (~500 tokens/s); free tier generoso | Qualidade em PT-BR inferior ao Gemini para domínio técnico; sem suporte multimodal | Alternativa de fallback |
| Hugging Face + Mistral 7B Instruct | Open weights; controle granular; sugerido no esqueleto técnico do PDF | PT-BR inferior; free tier de inference API limitado em throughput | Descartado |
| Ollama (Llama 3.1 8B local) | 100% offline; sem dependência de API externa | Exige hardware com >=16GB de RAM; deploy mais complexo; sem multimodal nativo | Reserva (para nota bônus em Sprint 2) |

### Documentação fallback

Caso o serviço do Gemini fique indisponível na data da entrega da Sprint 2, o código está estruturado para trocar o provedor com poucas linhas de alteração — usando a abstração `ChatModel` do LangChain. O fallback documentado é **Groq + Llama 3.1**.

---

## Justificativa técnica

A combinação **Gemini 2.0 Flash + LangChain + Streamlit** foi escolhida por cinco critérios objetivos:

1. **Custo zero verificado.** O free tier do Google AI Studio cobre 60 requisições/minuto e 1 milhão de tokens/dia — folga confortável para os testes da Sprint 2 e para a demonstração em vídeo.
2. **Qualidade em português brasileiro.** O domínio do projeto (operação de eletropostos comerciais no Brasil) exige resposta natural em PT-BR. O Gemini é, comprovadamente em benchmarks recentes, um dos LLMs com melhor desempenho em PT-BR entre os modelos gratuitos.
3. **Function calling nativo.** Habilita o roadmap da Sprint 2 sem mudança de provedor. Caso o grupo opte pelo diferencial de function calling (bônus indicado na seção 5.6 do documento oficial), o Gemini suporta nativamente — não exige adaptação posterior.
4. **Multimodalidade preparada.** O Gemini 2.0 aceita entrada de imagem. Em Sprint 2, isso permite ao operador enviar uma foto da tela de erro do carregador e o chatbot processar visualmente — diferencial competitivo de alto impacto.
5. **LangChain como camada de abstração.** Usar LangChain (em vez de chamar a API do Gemini diretamente) protege o código contra troca futura de provedor, isola dependências e oferece utilitários prontos para gestão de histórico, prompt templates e function calling.

---

## Arquitetura e fluxograma

O fluxograma completo da arquitetura conversacional está em [`docs/fluxograma.png`](docs/fluxograma.png) (versão raster) e [`docs/fluxograma.svg`](docs/fluxograma.svg) (versão vetorial).

![Fluxograma do SmartHub Assistant](docs/fluxograma.png)

**Cinco decisões arquiteturais importantes** representadas no fluxograma:

1. **Injeção explícita do system prompt** antes de cada chamada ao modelo, garantindo que o contexto operacional GoodWe nunca seja perdido.
2. **Histórico com janela deslizante (N=10 turnos)** — previne estouro do limite de tokens em conversas longas e mantém a coerência da memória de contexto.
3. **Filtro de escopo antes da geração** — separa o caminho de "dúvida operacional" do caminho de "recusa elegante" para perguntas fora do domínio.
4. **Validação de regras pós-geração** — verifica se a resposta respeita as regras de segurança do system prompt (não inventar especificação, não dar conselho elétrico).
5. **Logging operacional** — registra cada turno para análise posterior, alimentando as métricas de avaliação propostas para Sprint 2.

A caixa lateral do fluxograma indica os quatro elementos previstos para incorporação em Sprint 2 (RAG, function calling, interface Streamlit e few-shot examples avançados).

---

## System prompt

O system prompt oficial do SmartHub Assistant está em [`prompts/system_prompt.md`](prompts/system_prompt.md).

Estrutura em **sete blocos formais**:

1. **PAPEL** — identidade do assistente
2. **OBJETIVO** — meta de impacto e jobs atendidos
3. **ESCOPO** — domínios cobertos e domínios proibidos
4. **REGRAS** — sete regras de comportamento, incluindo regra prioritária de emergência elétrica
5. **TOM E FORMATO** — diretrizes de estilo, comprimento e estrutura de resposta
6. **COMPORTAMENTO EM BORDA** — saudação, despedida, frustração, conversa longa, ambiguidade
7. **EXEMPLOS DE REFERÊNCIA (few-shot)** — cinco exemplos curados para estabilizar o estilo

Os seis primeiros blocos seguem a estrutura sugerida na seção 9 do documento oficial. O sétimo bloco (few-shot) é adição do grupo, baseada no conteúdo do Módulo 2 da ementa (Aula 6 — zero-shot, few-shot e CoT), e tem o objetivo de reduzir variabilidade do modelo em produção.

---

## Modelo de teste

O protocolo completo de teste está em [`tests/modelo_de_teste.md`](tests/modelo_de_teste.md).

São **10 casos de teste** distribuídos em 10 categorias distintas:

| # | Categoria |
|---|-----------|
| T01 | Factual |
| T02 | Instrucional (POP) |
| T03 | Fora de escopo (domínio próximo) |
| T04 | Fora de escopo (domínio distante) |
| T05 | Ambígua com referência ao histórico |
| T06 | Edge case de segurança (emergência elétrica) |
| T07 | Jailbreak / desvio de escopo |
| T08 | Hostil / frustração do operador |
| T09 | Comparativa com concorrente |
| T10 | Pedido de dado em tempo real |

Cada caso possui critérios objetivos de aprovação (checkboxes verificáveis) e está mapeado contra as regras do system prompt por meio de uma **matriz de cobertura** documentada no próprio arquivo. Não há regras sem teste correspondente.

A execução do protocolo está prevista para a Sprint 2, com resultados a serem registrados em `tests/resultados.md`.

---

## Como executar (previsto para Sprint 2)

> **Nota da Sprint 1:** Esta seção documenta o procedimento de execução planejado para a entrega da Sprint 2. Não há código executável neste estágio.

### Execução em Google Colab

1. Abrir o notebook `notebooks/chatbot.ipynb` no Google Colab.
2. Em **Secrets**, adicionar a variável `GOOGLE_API_KEY` com o token gratuito do Google AI Studio.
3. Executar as células em ordem.

### Execução local

```bash
git clone https://github.com/<organizacao>/smarthub-assistant.git
cd smarthub-assistant
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
export GOOGLE_API_KEY="seu_token_aqui"
python chatbot.py
```

### Variáveis de ambiente necessárias

| Variável | Origem | Obrigatória |
|----------|--------|-------------|
| `GOOGLE_API_KEY` | https://aistudio.google.com/app/apikey | Sim |
| `GROQ_API_KEY` | https://console.groq.com (fallback) | Não |

---

## Roadmap de evolução

| Marco | Entrega | Status |
|-------|---------|--------|
| **Sprint 1** | Planejamento técnico, system prompt v1.0, modelo de teste, fluxograma, README | ✅ Concluído |
| **Sprint 2** | Chatbot funcional, execução do protocolo de teste, vídeo de demonstração | ⏳ Em planejamento |
| Sprint 2 — Diferenciais opcionais | RAG com ChromaDB, function calling, interface Streamlit, suporte bilíngue PT/EN | ⏳ Sob avaliação |

---

## Limitações conhecidas

A documentação aberta das limitações do projeto é uma boa prática indicada na seção 10.1 do documento oficial do Challenge. As limitações conhecidas da entrega atual (Sprint 1) são:

1. **System prompt v1.0 não foi validado empiricamente.** O protocolo de teste só será executado em Sprint 2. Iterações certas-de-acontecer estão previstas.
2. **Não há tratamento de janela de contexto estressada.** O bloco de comportamento em borda menciona conversas longas (>8 turnos), mas nenhum caso de teste valida esse comportamento. Será adicionado em `v1.1`.
3. **Cobertura idiomática limitada a português brasileiro.** Caso o grupo opte por adicionar suporte bilíngue (PT/EN) como diferencial em Sprint 2, será necessário expandir o modelo de teste.
4. **Dependência de API externa (Google AI Studio).** Mitigada pelo fallback documentado para Groq + Llama 3.1, mas implica risco operacional em produção real.
5. **Acoplamento implícito ao painel gestor do SmartHub.** O system prompt referencia abas e funcionalidades de um painel cuja implementação ocorre em outras disciplinas do projeto integrado. Caso a interface real mude, o prompt precisará ser atualizado.

---

## Diário de iteração do system prompt

Esta seção documenta a evolução do system prompt ao longo das sprints, conforme boa prática indicada na seção 10.1 do documento oficial.

### v1.0 — Sprint 1 (versão atual)

Primeira versão. Estrutura em sete blocos formais. Pendente de validação empírica via execução do protocolo de teste em Sprint 2.

### v1.1+ — Sprint 2 (a ser preenchido)

Iterações serão documentadas em [`prompts/iteracoes.md`](prompts/iteracoes.md) à medida que forem realizadas, com o formato:

```
v1.0 → v1.1
Motivo: [observação dos testes que justificou a mudança]
Mudança: [descrição da alteração no prompt]
Resultado: [efeito da mudança quando re-testado]
```

---

## Estrutura do repositório

```
smarthub-assistant/
├── README.md
├── .gitignore
├── docs/
│   ├── fluxograma.png             # versão raster do fluxograma
│   └── fluxograma.svg             # versão vetorial do fluxograma
├── prompts/
│   ├── system_prompt.md           # system prompt v1.0 (Sprint 1)
│   └── iteracoes.md               # diário de iteração (Sprint 2)
└── tests/
    ├── modelo_de_teste.md         # protocolo de teste com 10 casos (Sprint 1)
    └── resultados.md              # resultados da execução (Sprint 2)
```

Adições previstas para Sprint 2:

```
├── notebooks/
│   └── chatbot.ipynb              # notebook Colab principal
├── chatbot.py                     # script executável local
├── requirements.txt               # dependências Python
└── docs/
    └── video_demo_link.md         # link do vídeo no YouTube (não listado)
```

---

## Papéis no grupo

Distribuição de responsabilidades inspirada na seção 7.2 do documento oficial do Challenge. Os papéis abaixo são complementares — não significam que cada responsabilidade pertence exclusivamente a um integrante, mas que cada um lidera a respectiva frente.

| Papel | Lead | Responsabilidades |
|-------|------|-------------------|
| Product Owner | A definir | Alinhamento com o problema GoodWe; consolidação do README; apresentação do vídeo final |
| Tech Lead | A definir | Stack, esqueleto de código, configuração do repositório, revisão de commits |
| Prompt Engineer | A definir | Iteração do system prompt; documentação do diário de iteração |
| QA / Testes | A definir | Execução do protocolo de teste; registro de resultados; identificação de regressões |
| Documentação | A definir | Manutenção do README; preparação do fluxograma; roteiro do vídeo de demonstração |

> Os leads serão atribuídos na primeira reunião de Sprint 2.

---

## Demonstração em vídeo

> A ser entregue na Sprint 2.

O vídeo de demonstração (até 3 minutos, não listado no YouTube) mostrará o chatbot em funcionamento com pelo menos três interações relevantes ao contexto operacional GoodWe, executando casos do modelo de teste e demonstrando o comportamento esperado em cada categoria.

Link a ser inserido aqui em Sprint 2.

---

## Licença

Trabalho acadêmico. Distribuição livre para fins educacionais, conforme orientação do plano de aulas da disciplina Prompt and Artificial Intelligence (FIAP, Ciência da Computação 1º Ano, 2026.1).

---

**EV Challenge 2026 · FIAP × GoodWe**
*Bons projetos. Boas conversas com o modelo. Boas decisões.*