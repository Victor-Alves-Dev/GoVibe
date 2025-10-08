<p align="center">
  <img src="img/idioma.png" alt="GoVibe" height="70">
</p>

<h1 align="center">GoVibe — Recomendador de Destinos com IA</h1>

<p align="center">
  Plataforma que sugere destinos de viagem a partir do perfil do usuário, combinando questionário estruturado, pré-processamento e Rede Neural (Keras).
</p>

<p align="center">
  <a><img alt="Status" src="https://img.shields.io/badge/status-MVP-gray"></a>
  <a><img alt="License" src="https://img.shields.io/badge/license-MIT-blue"></a>
  <a><img alt="Python" src="https://img.shields.io/badge/python-3.9%2B-3776AB"></a>
  <a><img alt="Keras" src="https://img.shields.io/badge/keras-tensorflow-FF6F00"></a>
  <a><img alt="Stack" src="https://img.shields.io/badge/stack-HTML%20CSS%20JS%20%7C%20Python%20ML-black"></a>
</p>

---

## Preview
<p align="center">
  <img src="img/brasil.png" alt="Brasil" width="32%">
  <img src="img/franca.png" alt="França" width="32%">
  <img src="img/japao.png"  alt="Japão"  width="32%">
</p>

> Assim que tiver screenshots da interface (Home, Formulário e Resultados), substitua por imagens reais em `docs/screens/`.

---

## Visão Geral
O **GoVibe** coleta preferências (orçamento, duração, clima, paisagem, estilo, idioma, região, cultura local, interesse cultural, fama do destino, culinária, liberdade/regulação e companhia), transforma em vetores, aplica **pré-processamento** (codificação/escala) e utiliza uma **Rede Neural** para estimar a afinidade do usuário com países/destinos.  
O site inclui carrossel, tema claro/escuro, suporte a múltiplos idiomas, questionário passo a passo e tela de resultados.

---

## Principais Recursos
- Questionário multi-passo com 13 dimensões.
- Recomendações com **ML + Rede Neural** (Keras).
- Artefatos de pré-processamento e modelo versionados:
  `encoder_perfis.npy`, `scaler_perfis.pkl`, `colunas_treinadas.pkl`, `modelo_perfis.keras`.
- Front-end responsivo com alternância de idioma e tema.
- Modal por país com informações úteis (moeda, clima, época, fuso).
- **API de Feedback (opcional | local)** para avaliações e comentários do usuário.

---

## Tecnologias
- **Front-end:** HTML, CSS (`style.css`, `slides.css`), JS (`script.js`, `slides.js`, `darkmode.js`, `traducao.js`, `resultados.js`)
- **ML/Back-office:** Python 3.9+, NumPy, Pandas, scikit-learn, TensorFlow/Keras
- **Dados:** CSVs de países e mapeamentos (`base_paises_perfis.csv`, `destinos_com_perfis.csv`, `paises_por_perfil.csv`)
- **Artefatos de treino:** `modelo_perfis.keras`, `encoder_perfis.npy`, `scaler_perfis.pkl`, `colunas_treinadas.pkl`
- **Scripts:** `treinar_modelo.py`, `prever_perfil.py`, `paises_por_perfil.py`

---

## Organização do Repositório
img/ # imagens e ícones
index.html # landing + carrossel + formulário + modais
resultado.html # tela de resultados
style.css, slides.css # estilos
script.js, slides.js # UI e interações
darkmode.js, traducao.js # tema e tradução
resultados.js # exibição de resultados (UI)

Dados / ML
base_paises_perfis.csv
destinos_com_perfis.csv
paises_por_perfil.csv
colunas_treinadas.pkl
encoder_perfis.npy
scaler_perfis.pkl
modelo_perfis.keras
treinar_modelo.py
prever_perfil.py
requirements.txt

Deploys/infra (opcional)
Procfile
render.yaml
.runtime.txt

yaml
Copiar código

---

## Como Executar o Front-end (estático)
Sem servidor dedicado:

```bash
git clone https://github.com/<Vivito1>/<repo>.git
cd <repo>

# Servir arquivos (exemplo com Python)
python -m http.server 8080
# Acesse http://localhost:8080
A lógica de recomendação usada na UI consome artefatos locais. O pipeline de ML roda pelos scripts Python.

Ambiente de ML (treino e predição)
bash
Copiar código
python -m venv .venv
# Windows: .venv\Scripts\activate
# Linux/Mac:
source .venv/bin/activate
pip install -r requirements.txt
Treino

bash
Copiar código
python treinar_modelo.py
Gera/atualiza: modelo_perfis.keras, encoder_perfis.npy, scaler_perfis.pkl, colunas_treinadas.pkl.

Predição

bash
Copiar código
# Exemplo de chamada (ajuste conforme argumentos suportados)
python prever_perfil.py --orcamento 2000_5000 --duracao media --clima ameno \
  --paisagem historica --estilo cultura --idioma ingles --regiao europa \
  --cultura_local diferente --interesse_cultura alto --fama_pais equilibrado \
  --comida variada --liberdade regras --companhia casal
API de Feedback (opcional | apenas local)
A API recebe avaliações do site (nota 1–5, comentário e nome opcional) e lista feedbacks para a UI.
Não há URL pública e não existe acesso exposto: a API fica desabilitada por padrão e deve ser executada apenas em ambiente local/desenvolvimento.

Status
Produção: desabilitada (sem endpoint público).

Desenvolvimento: use localhost apenas quando necessário.

Endpoints (base local, ex.: http://localhost:8000)
Método	Rota	Descrição	Corpo/Params	Resposta
POST	/api/feedback	Cria um feedback	{ rating, nome?, comentario }	201 Created + objeto
GET	/api/feedback	Lista feedbacks com paginação	?offset=0&limit=10	200 OK + lista
GET	/health	Healthcheck	—	200 OK

Esquema de dados sugerido:
id (uuid) · rating (int 1–5) · nome (string opcional) · comentario (string) · created_at (utc) · ip_hash (opcional)

Execução local (exemplo com FastAPI)
Ajuste o nome do módulo conforme seu arquivo (ex.: feedback_api.py).

bash
Copiar código
# Ativar venv e instalar dependências (se ainda não tiver):
# pip install fastapi uvicorn pydantic[dotenv]

uvicorn feedback_api:app --host 127.0.0.1 --port 8000 --reload
Variáveis de Ambiente (front-end)
Definidas no JS para evitar chamadas em produção:

js
Copiar código
// script.js
const FEEDBACK_API_ENABLED = false;            // produção: false
const FEEDBACK_API_BASEURL = "http://localhost:8000"; // dev local

// No envio:
if (FEEDBACK_API_ENABLED) {
  fetch(`${FEEDBACK_API_BASEURL}/api/feedback`, { method: "POST", ... });
} else {
  // fallback: mensagem local ou desabilitar botão
}
Segurança e privacidade
CORS restrito a http://localhost em desenvolvimento.

Sem dados sensíveis; armazene somente o necessário.

Se optar por IP hash para antifraude, documente e informe no aviso de privacidade.

Defina retenção (ex.: 90 dias) e limite de taxa (ex.: 10 req/min/IP).

Arquitetura
mermaid
Copiar código
flowchart LR
U[Usuário] -->|Formulário| F[index.html + script.js]
F -->|Perfil| P[Pré-processamento]
P -->|One-Hot/Scale| M[(modelo_perfis.keras)]
M --> R[Ranking de países/destinos]
R --> G[resultados.js + resultado.html]

subgraph ML Offline
  D[(CSVs: países/perfis)]
  T[treinar_modelo.py]
  D --> T --> M
  T --> E[encoder_perfis.npy]
  T --> S[scaler_perfis.pkl]
  T --> C[colunas_treinadas.pkl]
end

subgraph Opcional (dev)
  A[(Feedback API - local)]
  F <-- avaliações --> A
end
Próximos Passos
Endpoint de predição (FastAPI/Flask) para integrar o modelo de forma isolada.

Métricas de recomendação (Top-k, MRR) e relatório automatizado.

Explicabilidade (ex.: SHAP) para transparência.

Internacionalização consolidada em arquivo único.

Telemetria de cliques para re-treino com feedback implícito.

Como contribuir
Pull requests são bem-vindos. Abra uma issue descrevendo mudanças e passos de teste.

## Licença
Uso **exclusivo para análise/portfólio**. Não é permitido copiar, usar,
modificar ou redistribuir o código sem autorização por escrito do autor.
Veja o arquivo `LICENSE`.

Autor
Victor Alves
