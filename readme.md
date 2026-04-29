# ProblemAção — Plataforma de Inteligência e Ação sobre Atendimento ao Cliente

---

## 1. Problema que Resolve

Times de atendimento ao cliente lidam com **milhares de interações diárias** (tickets, chats, e-mails) e enfrentam três dores concretas:

| Dor | Impacto |
|---|---|
| **Cegueira operacional** | Sem visão consolidada, temas críticos são detectados tarde demais — geralmente quando já escalaram para a diretoria ou redes sociais. |
| **Paralisia analítica** | Mesmo quando um padrão é identificado, o time gasta dias para entender causa-raiz, redigir relatórios e propor ações. O ciclo insight → ação é medido em semanas. |
| **Fragmentação de dados** | Informações vivem em ferramentas distintas (Zendesk, e-mail, chat). Não existe uma "fonte única da verdade" sobre o que o cliente está dizendo. |

**Em resumo**: o time sabe que tem problemas, mas não sabe **quais são os mais urgentes** nem **o que fazer com eles** — e quando descobre, já perdeu a janela de ação.

---

## 2. Visão Clara do Produto

> **ProblemAção** é uma plataforma de IA Generativa que transforma dados brutos de atendimento ao cliente em **visibilidade imediata** (dashboard de temas críticos) e **ação concreta** (planos de ação gerados por IA e executáveis com um clique) — reduzindo o ciclo de detecção-a-ação de semanas para horas.

### Proposta de valor em uma frase:
*"Veja o que importa, aja no que importa — antes que vire crise."*

### Dois pilares integrados:

```
┌─────────────────────────────────────────────────────┐
│                  INSIGHTACTION                      │
│                                                     │
│   ┌─────────────────┐    ┌────────────────────┐     │
│   │   Dashboard     │    |   Planos de ação   │     │
│   │                 │    │                    │     │
│   │ • Radar crítico │    │ • Análise profunda │     │
│   │ • Clusters      │    │ • Planos de ação   │     │
│   │ • Alertas       │    │ • Conversacional   │     │
│   │ • Tendências    │    │ • Export/Integração│     │
│   └─────────────────┘    └────────────────────┘     │
│            ▲                       ▲                │
│            └───────┐   ┌──────────┘                 │
│                    │   │                            │
│            ┌───────────────────┐                    │
│            │   DATA PIPELINE   │                    │
│            │  Ingestão + IA    │                    │
│            └───────────────────┘                    │
└─────────────────────────────────────────────────────┘
```

O **Dashboard** é a porta de entrada — o gestor bate o olho e entende a situação. Quando precisa agir, clica no tema e entra no **Planos de ação**, onde conversa com os dados e gera planos concretos.

---

## 3. Funcionalidades-chave (MVP)

Premissas:
- Squad de 4-5 pessoas: 1 PM, 1 Designer, 2 Devs Full-stack, 1 Dev ML/Data
- Sprint de 2 semanas
- Total MVP: 10 semanas (5 sprints)


| Dimensão | Decisão |
|---|---|
| **Produto** | Plataforma dual: Dashboard de visibilidade + planos de ação |
| **Diferencial** | Único produto que vai de "ver o problema" a "ter o plano de ação" em um fluxo contínuo |
| **MVP em** | 10 semanas com squad de 5 pessoas |
| **Custo de infra LLM** | ~$200/mês para 50k tickets |
| **Primeira validação real** | Semana 4 (Gate 2 — demo do dashboard) |
| **Maior risco** | Alucinação do LLM (mitigado por RAG + aprovação humana) |
| **Go-to-market** | Piloto com 1 squad → escala interna → potencial SaaS multi-tenant |


| # | Funcionalidade | Descrição | Prioridade | Critérios de Aceitação (resumo) |
|---|---|---|---|---|
| F1 | **Ingestão Multicanal** | Upload CSV + integração com 1 ferramenta (Zendesk ou Freshdesk via API). Normalização de campos. | Essencial | CSV até 500MB aceito e validado (campos obrigatórios). Integração Zendesk importa tickets das últimas 24h via job diário. Registros normalizados para schema interno. Duplicatas ignoradas. Relatório pós-ingestão com totais e erros. |
| F2 | **Clusterização Semântica** | Embeddings via LLM API → agrupamento (HDBSCAN) → rotulação automática com linguagem de negócio. | Essencial | Embeddings gerados para 100% dos tickets novos em até 2h (50k). Clusters com mínimo 5 tickets, rotulados em linguagem de negócio pelo LLM. ≥80% dos tickets coerentes com o rótulo (validação humana). Clusters <1% agrupados em "Temas menores". |
| F3 | **Radar de Criticidade** | Dashboard com cards dos top temas ranqueados por score composto. Atualização diária. | Essencial | Top 10 clusters por score (volume × sentimento × crescimento). Cards com rótulo, score 0-100, volume, sentimento e variação %. Filtros por período, canal e criticidade. Carregamento <2s. Pesos configuráveis pelo Admin. |
| F4 | **Aprovundamento por Tema** | Clique no cluster abre análise profunda com causa-raiz gerada por IA (chain-of-thought). | Essencial | Painel abre em <3s com: descrição expandida, causa-raiz com raciocínio explícito citando ≥2 tickets, lista paginada de tickets (ordenável por data/sentimento). Botões claros para "Gerar plano" e "Conversar sobre o tema". |
| F5 | **Geração de Plano de Ação** | LLM gera plano: problema, causa-raiz, ações MoSCoW, owner, KPIs. | Essencial | Plano em <10s com 5 seções obrigatórias (problema, causa-raiz, 3-7 ações MoSCoW com prazo, owner por cargo/área, ≥2 KPIs). Cita ≥3 tickets como evidência. Editável inline. Botão "Regenerar" sem perder versão anterior. Disclaimer "Gerado por IA". |
| F6 | **Exportação e importação de Planos** | Exportar como PDF, card Jira ou mensagem Slack/Teams. | Essencial | Export PDF formatado em <5s. Card no Jira com título, descrição e labels. Mensagem Slack formatada. Preview + confirmação antes do envio. Log de exportações. Credenciais encrypted at rest. |
| F7 | **Feedback Loop** | Usuário corrige rótulos e avalia planos → melhoria dos prompts. | Essencial | Correção de rótulo inline (Gestor+). 👍/👎 em planos e respostas do chat, com comentário opcional. Feedbacks armazenados com metadados completos. Relatório agregado para Admin. Rótulos corrigidos viram few-shot examples automaticamente. Correções do usuário nunca revertidas sem ação do Admin. |
| F8 | **Anonimização de PII** | Pipeline automático que remove CPF, nome, e-mail, telefone antes de enviar ao LLM. | Essencial | Detecta e substitui CPF, CNPJ, nomes, e-mail, telefone e endereço por placeholders. Taxa de detecção ≥95%. Anonimização antes de qualquer chamada a LLM externo. Pipeline local (spaCy + regex). Teste automatizado no CI/CD com 50 edge cases. Dados originais restritos ao Admin no PostgreSQL. |
| F9 | **Perfis de utilização** | Perfis: Admin, Gestor, Analista. | Essencial | 3 perfis com permissões progressivas: Analista (visualiza), Gestor (+chat, planos, export), Admin (+config, usuários, logs). Acesso negado exibe mensagem e registra log. Auth via e-mail+senha (bcrypt). Sessão expira após 8h de inatividade. |
| F10 | **Alertas de Temas Emergentes** | Notificação (e-mail ou Slack) quando tema novo ultrapassa threshold. | Importante | Detecta clusters novos ou com crescimento ≥X% (configurável). Notificação em até 15min via e-mail ou Slack. Conteúdo: nome, score, volume, variação e link direto. Sem alertas duplicados por ciclo. |
| F11 | **Chat Contextual (RAG)** | Interface conversacional sobre os dados do cluster. | Importante | Chat pré-contextualizado no cluster. Resposta em <5s (streaming). Respostas baseadas em RAG com citação de fonte. Suporta filtros temporais, volume, perfil e conteúdo. Recusa perguntas fora do escopo. Histórico de 20 turnos. PII nunca exibida. |
| F12 | **Tendência Temporal** | Gráfico de evolução dos top temas nos últimos 7/14/30 dias. | Desejável | Gráfico de linhas dos top 5 clusters com toggle de período (7/14/30d). Tooltip com data, volume, score e variação. Carregamento <3s, responsivo. Clusters extintos aparecem tracejados com label "(resolvido?)". |
| F13 | **Histórico de Planos** | Registro com status e comparação temporal. | Desejável | Planos salvos com metadados e status (pendente/execução/concluído) editável. Filtros por status, tema, data, autor. Comparação temporal: volume/sentimento entre plano anterior e atual (melhorou/piorou/estabilizou). Flag "ação atrasada" após 90 dias pendente. |

---

## 4. Arquitetura Simplificada

```
                        ┌──────────────────────────┐
                        │      FRONTEND (SPA)      │
                        │  React + Next.js (SSR)   │
                        │                          │
                        │  ┌────────┐ ┌──────────┐ │
                        │  │ Pulse  │ │ Action   │ │
                        │  │ View   │ │ Engine   │ │
                        │  │(Dash)  │ │(Chat+Plan│ │
                        │  └────────┘ └──────────┘ │
                        └──────────┬───────────────┘
                                   │ REST + WebSocket
                                   ▼
                        ┌──────────────────────────┐
                        │     API GATEWAY          │
                        │   (Auth + Rate Limit)    │
                        └──────────┬───────────────┘
                                   │
                    ┌──────────────┼──────────────┐
                    ▼              ▼              ▼
          ┌──────────────┐ ┌────────────┐ ┌────────────────┐
          │  INGESTION   │ │  ANALYSIS  │ │   ACTION       │
          │  SERVICE     │ │  SERVICE   │ │   SERVICE      │
          │              │ │            │ │                │
          │• CSV parser  │ │• Embedding │ │• RAG engine    │
          │• Zendesk API │ │• Clustering│ │• Chat handler  │
          │• PII removal │ │• Scoring   │ │• Plan generator│
          │• Normalizer  │ │• Summaries │ │• Export (Jira) │
          └──────┬───────┘ └─────┬──────┘ └───────┬────────┘
                 │               │                │
                 ▼               ▼                ▼
          ┌────────────────────────────────────────────┐
          │              DATA LAYER                    │
          │                                            │
          │  ┌──────────┐  ┌──────────┐  ┌──────────┐ │
          │  │PostgreSQL│  │ Qdrant/  │  │  Redis   │ │
          │  │(tickets, │  │ Pinecone │  │ (cache,  │ │
          │  │ planos,  │  │(vectors) │  │  sessões)│ │
          │  │ users)   │  │          │  │          │ │
          │  └──────────┘  └──────────┘  └──────────┘ │
          └────────────────────────────────────────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │   LLM APIs (ext.)   │
                    │  OpenAI / Anthropic  │
                    │  (embeddings + chat) │
                    └─────────────────────┘
```

### Fluxo de dados principal:

```
1. INGESTÃO (diária/batch)
   CSV/API → PII removal → Normalização → PostgreSQL

2. ANÁLISE (batch pós-ingestão)
   Tickets novos → Embeddings API → Qdrant
   → HDBSCAN clustering → Scoring → Resumos LLM
   → Resultados salvos no PostgreSQL

3. VISUALIZAÇÃO (on-demand)
   Frontend requisita → API retorna clusters + scores + resumos

4. AÇÃO (on-demand, por interação do usuário)
   Usuário seleciona tema → RAG busca tickets relevantes no Qdrant
   → LLM gera análise/plano → Usuário refina via chat
   → Export para Jira/Slack/PDF
```

---

## 5. Stack Técnica

| Camada | Tecnologia | Justificativa |
|---|---|---|
| **Frontend** | React + Next.js + TailwindCSS | SSR para performance, ecossistema maduro, time fácil de contratar |
| **Gráficos/Dashboard** | Recharts ou Tremor | Bibliotecas React-native para dashboards, rápidas de implementar |
| **Chat UI** | Vercel AI SDK | Streaming de respostas do LLM, UX de chat já resolvida |
| **Backend API** | Python (FastAPI) | Async, tipado, ecossistema de ML/NLP maduro |
| **Orquestração LLM** | LangChain + LangSmith | RAG, chain-of-thought, observabilidade de prompts |
| **Embeddings** | OpenAI `text-embedding-3-small` | Custo baixo (~$0.02/1M tokens), qualidade comprovada |
| **LLM (geração)** | Claude Sonnet ou GPT-4o-mini | Balanço custo × qualidade para MVP. Upgrade para modelos maiores se necessário |
| **Vector DB** | Qdrant (self-hosted) ou Pinecone (managed) | Qdrant para controle de dados; Pinecone para zero-ops |
| **Banco relacional** | PostgreSQL | Tickets normalizados, planos, usuários, histórico |
| **Cache/Sessão** | Redis | Cache de resultados de clusterização, sessões de chat |
| **PII Removal** | spaCy (pt_core_news_lg) + regex | NER local — dados sensíveis nunca saem do servidor |
| **Jobs/Batch** | Celery + Redis (broker) | Pipeline de ingestão e clusterização agendada |
| **Infra** | Docker + Docker Compose (dev) / AWS ECS ou Railway (prod) | Simplicidade para MVP, escala quando necessário |
| **CI/CD** | GitHub Actions | Testes + deploy automatizado |
| **Observabilidade** | LangSmith (LLM) + Sentry (app) + Grafana (infra) | Visibilidade sobre custos de LLM, erros e performance |

### Estimativa de custo de LLM (MVP, 50k tickets/mês):
- Embeddings: ~$5/mês
- Geração (resumos + chat): ~$50-150/mês (depende do uso conversacional)
- **Total infra LLM: ~$200/mês** (muito viável para validação)

---

## 6. Roadmap Visual

### Premissas
- Squad de 4-5 pessoas: 1 PM, 1 Designer, 2 Devs Full-stack, 1 Dev ML/Data
- Sprint de 2 semanas
- Total MVP: **10 semanas** (5 sprints)

```
         S1          S2          S3          S4          S5
     Sem 1-2     Sem 3-4     Sem 5-6     Sem 7-8     Sem 9-10
    ┌───────┐   ┌───────┐   ┌───────┐   ┌───────┐   ┌───────┐
    │FUNDA- │   │ DASH  │   │PLANOS │   │INTE-  │   │POLISH │
    │ÇÃO    │   │ BOARD │   │ AÇÃO  │   │GRAÇÃO │   │+LAUNCH│
    └───────┘   └───────┘   └───────┘   └───────┘   └───────┘
```

| Sprint | Semanas | Entregas | Detalhamento |
|---|---|---|---|
| **S1 - Fundação** | 1-2 | Infra + Pipeline de dados | Setup do projeto (repo, CI/CD, Docker). Pipeline de ingestão CSV → PII removal → PostgreSQL → Embeddings → Qdrant. Clusterização HDBSCAN + scoring básico. RBAC básico. **Entrega**: dados processados e clusterizados a partir de um CSV real do cliente. |
| **S2 - Dashbord** | 3-4 | Dashboard funcional | Frontend: tela de login + dashboard com radar de criticidade (cards dos top clusters), resumos automáticos por LLM, gráfico de tendência temporal. Drill-down para lista de tickets por cluster. **Entrega**: dashboard navegável com dados reais. Demo interna. |
| **S3 - Planos de ação** | 5-6 | Chat + Planos de ação | RAG engine (LangChain + Qdrant). Interface de chat contextualizada por cluster. Geração de plano de ação estruturado (prompt engineering). Transição fluida: dashboard → clique no tema → chat + plano. **Entrega**: fluxo completo detecção → análise → plano. |
| **S4 - Integração** | 7-8 | Conectores + Alertas | Integração com Zendesk/Freshdesk (ingestão via API). Export de planos para Jira + Slack. Alertas de temas emergentes (e-mail/Slack). Feedback loop (validação de clusters e planos pelo usuário). **Entrega**: produto conectado ao ecossistema real do cliente. |
| **S5 - Polish + Launch** | 9-10 | MVP pronto para produção | Testes de carga e segurança. Refinamento de UX com base em feedback do piloto (S4). Documentação. Onboarding flow. Observabilidade (LangSmith + Sentry). **Entrega**: MVP em produção com 1 squad piloto do cliente. |

### Marcos de validação (gates):

- **Gate 1 (fim S1)**: Cliente confirma que a clusterização faz sentido com seus dados reais
- **Gate 2 (fim S2)**: Demo do dashboard — stakeholders validam que a visualização é útil
- **Gate 3 (fim S3)**: Teste do fluxo completo com 3-5 gestores — coletam-se NPS e tempo de tarefa
- **Gate 4 (fim S5)**: Go/No-go para escalar para outros times

---

## 7. Ciclo de Vida Planejado

```
┌─────────────────────────────────────────────────────────────────────┐
│                     CICLO DE VIDA DO PRODUTO                        │
│                                                                     │
│  DISCOVERY   BUILD MVP    PILOT     SCALE      MATURE    EVOLVE    │
│  (4 sem)     (10 sem)    (4 sem)   (12 sem)   (ongoing) (ongoing) │
│                                                                     │
│  ┌──────┐   ┌──────┐   ┌──────┐   ┌──────┐   ┌──────┐  ┌──────┐  │
│  │Pesqui│   │Sprints│   │1 squad│   │Rollout│  │Otimiz│  │Novas │  │
│  │sa +  │──▶│ S1-S5 │──▶│piloto │──▶│geral  │──▶│ação  │──▶│verti-│  │
│  │Valid.│   │       │   │       │   │       │  │      │  │cais  │  │
│  └──────┘   └──────┘   └──────┘   └──────┘   └──────┘  └──────┘  │
│                                                                     │
│  Mês 1      Mês 2-4     Mês 5      Mês 6-8    Mês 9+    Mês 12+  │
└─────────────────────────────────────────────────────────────────────┘
```

| Fase | Duração | Objetivo | Entregas-chave | Métricas de sucesso |
|---|---|---|---|---|
| **Descoberta** | 4 semanas | Validar problema e solução com o cliente | Entrevistas com 5-8 usuários. Mapeamento de jornada atual. Definição de personas. Análise de dados de amostra. Protótipo lo-fi validado. | 80%+ dos entrevistados confirmam as dores. Dados do cliente são processáveis. |
| **Construção do MVP** | 10 semanas | Construir produto funcional (Sprints S1-S5) | Plataforma funcional com Pulse View + Action Engine. Piloto interno testado. | Todas as funcionalidades must-have entregues. Zero bugs críticos. |
| **Piloto** | 4 semanas | Validar valor com usuários reais | 1 squad usa a ferramenta no dia-a-dia. Coleta intensiva de feedback. Iteração rápida (hotfixes semanais). | Redução de ≥40% no tempo de triagem. NPS ≥ 40. Uso recorrente (≥3x/semana). |
| **Escalada** | 12 semanas | Expandir para toda a operação | Rollout para todos os times. Novas integrações (mais canais). Refinamento de modelos com dados acumulados. Treinamento de usuários. | Adoção ≥70% dos times elegíveis. ROI mensurável (horas economizadas). |
| **Maturação** | Contínuo | Otimizar e consolidar | Fine-tuning de prompts com feedback acumulado. Automação de processos repetitivos. SLAs definidos. Monitoramento de drift nos clusters. | Custo por insight reduzido em 30%. Uptime ≥99.5%. |
| **Evolução** | Contínuo (Mês 12+) | Expandir para novas verticais | Módulos: predição de churn, análise de NPS, integração com CRM. Possibilidade de oferecer como SaaS multi-tenant para outros clientes. | Novas fontes de receita. Expansão de base de clientes. |

### Estratégia de feedback contínuo:

```
Usuário usa o produto
        │
        ▼
┌─────────────────┐     ┌──────────────────┐     ┌─────────────────┐
│ Feedback in-app │────▶│ Análise semanal   │────▶│ Backlog reprior │
│ (thumbs up/down,│     │ pelo PM           │     │ izado           │
│  correção de    │     │ (quant + qual)    │     │                 │
│  clusters)      │     │                   │     │                 │
└─────────────────┘     └──────────────────┘     └─────────────────┘
        │
        ▼
┌─────────────────┐
│ Prompt tuning   │  ← Feedback negativo em planos → ajuste de prompt
│ automático      │  ← Cluster mal rotulado → few-shot examples
└─────────────────┘
```

---

## 8. Gestão de Riscos Aplicada à Solução


## Matriz de Riscos 

| # | Risco | Probabilidade | Impacto | Categoria | Estratégia | Responsável | Plano de mitigação |
|---|---|---|---|---|---|---|---|
| R1 | **Alucinação do LLM** — planos de ação com informações inventadas | Alta | Alto | Técnico | Mitigar | **Dev ML/Data** | RAG com source attribution obrigatória (toda afirmação cita ticket original). Planos são sempre "sugeridos" — humano aprova. Testes automatizados de faithfulness com RAGAS. |
| R2 | **Vazamento de dados sensíveis (PII)** para APIs externas de LLM | Média | Crítico | Segurança | Prevenir | **Dev ML/Data + PM** | Pipeline de anonimização (spaCy NER + regex) antes de qualquer chamada ao LLM. Auditoria semanal de logs. Testes de PII leakage no CI/CD. Contrato de DPA com provider de LLM. |
| R3 | **Qualidade da clusterização** — clusters sem sentido para o negócio | Média | Alto | Técnico | Mitigar | **Dev ML/Data** | Feedback loop: usuário corrige rótulos → few-shot examples melhoram o prompt. Gate 1 valida com dados reais antes de avançar. Parâmetros de HDBSCAN ajustáveis. |
| R4 | **Baixa adoção** — time continua usando planilhas | Média | Alto | Mercado | Prevenir | **PM + Designer** | UX simples (dashboard + chat, sem curva de aprendizado). Briefing semanal automático por e-mail (o produto "vem até o usuário"). Pilot com early adopters entusiastas. Métricas de adoção desde o dia 1. |
| R5 | **Custo de LLM escala descontroladamente** com volume de dados | Média | Médio | Financeiro | Mitigar | **PM + Dev ML/Data** | Usar modelos menores (GPT-4o-mini, Claude Haiku) para tarefas batch. Cache de resumos já gerados (Redis). Embeddings são baratos — custo concentra em geração. Monitoramento de custo via LangSmith. Budget alert em $X/mês. |
| R6 | **Dependência de API externa** (OpenAI/Anthropic) — downtime ou mudança de pricing | Baixa | Alto | Técnico | Transferir | **Dev Backend** | Abstração via LangChain (troca de provider em 1 linha). Fallback configurado: se Provider A cai, usa Provider B. Cache de respostas frequentes. |
| R7 | **Dados do cliente insuficientes ou mal estruturados** | Média | Alto | Dados | Mitigar | **PM + Dev Backend** | Discovery (fase 1) inclui análise de amostra de dados reais. Pipeline de normalização tolerante a formatos variados. Documentação de requisitos mínimos de dados. |
| R8 | **Scope creep** — cliente pede features além do MVP | Alta | Médio | Gestão | Prevenir | **PM** | Roadmap claro com gates. PM atua como filtro. Backlog de "v1.1" para features desejáveis mas não essenciais. Revisão de escopo a cada sprint. |
| R9 | **Latência** — dashboard lento ou chat demora para responder | Média | Médio | Técnico | Mitigar | **Dev Backend + Dev Frontend** | Processamento pesado em batch (não real-time). Cache de clusters/resumos. Streaming de respostas do chat (Vercel AI SDK). Meta: dashboard <2s, primeira palavra do chat <1s. |
| R10 | **Viés nos insights** — IA reforça padrões existentes e ignora temas emergentes | Baixa | Alto | Ético/Técnico | Mitigar | **Dev ML/Data + PM** | Detecção explícita de "temas novos" (clusters com <7 dias de vida). Seção "Sinais Fracos" no dashboard. Revisão humana periódica dos clusters ignorados. |

### Plano de contingência para riscos críticos:

```
SE alucinação persistir (R1) após tuning:
  → Adicionar camada de verificação: segundo LLM valida afirmações do primeiro
  → Restringir geração a templates semi-estruturados (menos liberdade criativa)

SE vazamento de PII detectado (R2):
  → Kill switch imediato na integração com LLM externo
  → Fallback para modelo local (Llama 3 via Ollama) até resolução
  → Notificação ao DPO do cliente em <24h

SE adoção ficar abaixo de 30% no piloto (R4):
  → Sprint dedicada a UX research + redesign
  → Considerar pivô: de plataforma para relatório automático semanal (menor fricção)
```

---
