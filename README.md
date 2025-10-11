<p align="center">
  <img src="img/idioma.png" alt="GoVibe" height="70">
</p>

<h1 align="center">GoVibe — Recomendador de Destinos com IA</h1>

<p align="center">
  <a href="https://peachpuff-rook-575098.hostingersite.com"><img alt="Demo" src="https://img.shields.io/badge/demo-ao%20vivo-22c55e"></a>
  <a><img alt="Status" src="https://img.shields.io/badge/status-MVP-gray"></a>
  <a><img alt="Uso" src="https://img.shields.io/badge/uso-Portf%C3%B3lio%20apenas-red"></a>
  <a><img alt="Stack" src="https://img.shields.io/badge/stack-Frontend%20%7C%20Recomendador%20IA-black"></a>
</p>

<p align="center">
  <a href="https://peachpuff-rook-575098.hostingersite.com"><b>👉 Acessar Demo ao vivo</b></a>
</p>

---

## Visão Geral
O **GoVibe** transforma preferências declarativas do usuário em um **vetor semântico** e produz um **ranking de destinos**. O projeto é dividido em **UI (pública)** e **motor de recomendação (privado)**. Esta versão do repositório é **apenas de apresentação**: fluxos, telas e arquitetura em alto nível.

---

## Preview
<p align="center">
  <img src="img/brasil.png" alt="Brasil" width="32%">
  <img src="img/franca.png" alt="França" width="32%">
  <img src="img/japao.png"  alt="Japão"  width="32%">
</p>

---

## Detalhes Técnicos (alto nível)

### 1) Espaço de Recursos (Features)
Preferências convertidas para um vetor **denso** via codificação mista:
- **Categorias** (`orçamento`, `duração`, `clima`, `paisagem`, `estilo`, `idioma`, `região`, `cultura_local`, `interesse_cultura`, `fama_pais`, `comida`, `liberdade`, `companhia`).
- **Codificação**: *one-hot* para nominais; *ordinal* para escalas; *binária* quando aplicável.
- **Normalização**: *min-max* ou *z-score* conforme a distribuição.
- **Tratamento de faltantes**: imputação por moda/mediana e categoria “desconhecido”.

> As colunas, mapeamentos e escalas são versionados e validados por *checksum* (não expostos publicamente).

### 2) Arquitetura do Modelo (Recomendador)
- **Paradigma**: *Learning-to-Rank* simplificado sobre um **MLP** (Keras).
- **Entrada**: vetor de preferências do usuário + vetores de perfil de destino.
- **Head**: regressão escalar ∈ [0,1] (afinidade).
- **Camadas**: 2–3 *Dense* com *BatchNorm* e *Dropout*; ativações ReLU/SiLU.
- **Loss**: BCE/Huber (ajustada por distribuição de rótulos).
- **Otimização**: AdamW, *early stopping* por AUC/val-loss.
- **Regularização**: L2 nos pesos + *label smoothing* leve.
- **Aferição**: **Top-k**, **MRR**, **Recall@k** por região/idioma e *drift* temporal.

> O MLP é encapsulado em um *pipeline* de pré-processamento e serve apenas **offline** para gerar artefatos; inferência online é isolada.

### 3) Pós-Processamento de Ranking
- **Normalização de escores** (softmax com *temperature*).
- **Diversidade** via **MMR** (Maximal Marginal Relevance) balanceando relevância e diversidade regional/cultural.
- **Regras de negócio**: filtros de idioma, clima ou orçamento se “hard constraints” forem marcados.

### 4) UI / Front-end
- **Framework-agnóstico**, *vanilla* **HTML/CSS/JS** com:
  - **Wizard multi-passo** com validação progressiva.
  - **Tema claro/escuro** persistido (localStorage).
  - **i18n** por *resource bundles* (JSON) carregados sob demanda.
  - **Accessibility-first**: semântica, foco visível, *aria-labels*, contraste AA.
  - **Performance**: *lazy images*, bundling mínimo, *debounce* de inputs e cache de traduções.
- **Resultados**: cards + modal com moeda, clima, melhor época e fuso (conteúdo editorial curado).

### 5) Integração (sem expor motor)
- A UI **nunca** envia dados a um endpoint público de ML.
- O ranking exibido na demo deriva de **artefatos pré-gerados** + regras client-side (mock controlado).
- Opcional (desenvolvimento local): *feedback* do usuário (nota/comentário) em endpoint privado com **rate-limit** e CORS restrito.

### 6) Observabilidade & Qualidade
- **Telemetria** anônima (cliques/filtros) apenas em ambiente privado, para re-treino.
- **Validação de consistência**: schema do vetor, versões de codificação e *hash* dos artefatos.
- **Testes**: smoke de UI, verificação de strings i18n, testes de integridade dos mapeamentos.

---

## Arquitetura (diagrama)

flowchart LR
  U[Usuario] --> UI[UI: Wizard, i18n, Tema]
  UI --> PP[Preprocessamento]
  PP --> M[MLP Ranker]
  M --> R[Escore destinos]
  R --> DV[MMR e Regras]
  DV --> EX[Exibicao: Ranking e Modais]

  subgraph Ciclo_offline_privado
    DS[Curadoria destinos]
    FT[Engenharia atributos]
    TR[Treino e Validacao]
    AR[Artefatos versionados]
    DS --> FT --> TR --> AR
    AR -.-> PP
    AR -.-> M
  end


Segurança & Privacidade
Sem coleta de dados sensíveis; preferências não são persistidas na demo.

Política de “no public endpoints” para o motor de ML.

Se feedback local for habilitado: hash opcional de IP, retenção curta, rate-limit e CORS fechado.

Status
MVP em evolução: UI pública para demonstração; motor, datasets e artefatos permanecem privados.

Licença
Uso exclusivo para análise/portfólio. É proibido copiar, usar, modificar ou redistribuir sem autorização por escrito do autor. Consulte LICENSE.

Autor
Victor Alves
