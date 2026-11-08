# AI_Final_Project LRT
# StatMind AI: AI-Powered Marketing Analytics Agent

Course project for **CSS4007 Artificial Intelligence** (Narxoz University, section CSS4007-ENG-5).  
Team: Akim Lunara, Rustamova Radmila, Doshchanova Tomiris.

StatMind AI lets a small e-commerce owner or marketing manager upload campaign data and ask questions in plain English or Russian. An LLM agent decides which analysis to run, calls tested Python tools, and answers with verified numbers, charts and a short explanation. It says so when the data cannot answer a question instead of inventing values.

## The problem

Small e-commerce businesses run campaigns across several marketing channels but often have no dedicated analyst. Spreadsheet reports make it hard to compare channels and identify inefficient ad spending. A general chatbot asked to analyse a table may generate incorrect numbers. Our users need reliable, actionable insights without statistical or programming skills.

## What the product does

- Upload a campaign file (CSV or XLSX) and get automatic schema and data-quality checks.
- Ask questions such as:
  - "Which channel had the highest ROAS last month?"
  - "Which channels and campaigns contributed most to the ROAS decline in week 3?"
  - "Which campaign has the worst CPA?"
- Get an answer with verified numbers, relevant charts and information about the tools used.
- Supported metrics: CTR, CPC, conversion rate, CPA and ROAS.
- Compare performance across channels, campaigns and time periods.
- Identify sales and advertising performance trends.
- Perform multi-step analysis using several Python tools.
- Ambiguous request: the agent asks a clarifying question.
- Question the data cannot answer: the agent clearly explains the limitation.

**Project scope:** The agent focuses on historical marketing campaign performance analysis. It does not establish causal relationships, automatically optimize advertising budgets or integrate with live advertising platforms.

**Stretch goals:** Statistical anomaly detection and a downloadable PDF report.

## How it works

The AI feature is a **tool-use agent loop** built on the Gemini API:

1. The agent reads the user's question and plans the analysis.
2. It selects and calls approved Python tools (inspect data, calculate KPIs, compare channels/campaigns/periods and analyse trends).
3. It reads each tool result and can chain several calls (for example: identify a ROAS decline, compare periods and calculate channel contributions).
4. It generates the final answer using the calculated results and relevant visualizations.
5. If the request is ambiguous or unsupported by the data, it asks for clarification or explains why the answer cannot be provided.

All numerical calculations are performed by tested Python code, not by the LLM. The agent cannot execute arbitrary code: it can only call approved tools through a validated tool dispatcher using predefined JSON schemas.

```text
User question -> Streamlit app -> Agent (Gemini function calling)
                                     |        ^
                                     v        |
                              Tool dispatcher (validated calls)
                                     |
                                     v
                         Analytics engine (pandas)
                                     |
                                     v
                              Numbers and charts
                                     |
                                     v
                              Agent final answer
```

## Marketing KPIs

The analytics engine calculates five key marketing performance indicators:

| Metric | Formula | Description |
|---|---|---|
| CTR (Click-Through Rate) | Clicks / Impressions × 100% | Percentage of impressions that generated clicks |
| CPC (Cost Per Click) | Ad Spend / Clicks | Average advertising cost per click |
| Conversion Rate | Conversions / Clicks × 100% | Percentage of clicks that resulted in conversions |
| CPA (Cost Per Acquisition) | Ad Spend / Conversions | Average advertising cost per conversion |
| ROAS (Return on Ad Spend) | Revenue / Ad Spend | Revenue generated per unit of advertising spending |

**Calculation rules:**

- All KPIs are calculated using Python functions.
- Channel and period KPIs are calculated from aggregated numerators and denominators, not by averaging individual ratios.
- Division by zero is handled explicitly.
- Missing or invalid values are identified during data-quality checks.
- Revenue and advertising spend must use the same currency.
- ROAS is reported as a ratio (for example, 3.5 means 3.5 units of revenue per 1 unit of ad spend).
- The agent does not invent missing values or unsupported explanations.

## Evaluation

We measure the AI feature using **30 fixed test questions** in five categories:

1. KPI calculation.
2. Channel and period comparison.
3. Trend analysis.
4. Multi-step diagnosis.
5. Unanswerable or ambiguous questions.

The test set contains **20 English questions and 10 Russian questions**, covering the main evaluation categories.

Reference answers are based on known results planted in the synthetic dataset and independently verified using Python calculations. A team member who did not implement the analytics tools prepares or checks the reference answers.

A small public marketing dataset is used as an independent test to examine whether the agent works beyond synthetic examples.

### Evaluation metrics

| Metric | Target | Result |
|---|---|---|
| Tool-selection accuracy | ≥ 85% | _to be filled_ |
| Numerical accuracy | ≥ 95% | _to be filled_ |
| Faithfulness | ≥ 95% | _to be filled_ |
| Correct clarification or refusal | ≥ 80% | _to be filled_ |
| Explanation quality | Average ≥ 4/5 | _to be filled_ |

**Metric definitions:**

- **Tool-selection accuracy:** Percentage of test cases in which the agent selects the required analytical tools and follows an appropriate sequence of tool calls.
- **Numerical accuracy:** Percentage of evaluated numerical values that meet the predefined error tolerance. Relative error ≤ 1% is used for non-zero reference values, while a predefined absolute tolerance is used for zero or near-zero values.
- **Faithfulness:** Percentage of numerical claims in generated explanations that match the corresponding Python tool outputs within the applicable precision.
- **Correct clarification or refusal:** Percentage of ambiguous or unanswerable questions correctly handled without fabricated answers.
- **Explanation quality:** Two team members independently assess correctness, completeness, clarity and absence of unsupported claims using a five-point overall rating scale.

### Baseline comparison

The baseline uses the **same Gemini model without Python tools**, answering questions using an equivalent text representation of the available campaign data.

Both systems are evaluated using the same questions, reference answers and applicable evaluation criteria.

We compare numerical accuracy, faithfulness, explanation quality and handling of unanswerable questions. Tool-selection accuracy applies only to the tool-using agent.

The purpose is to measure whether tool use improves reliability and analytical performance compared with an LLM-only approach.

### Evaluation procedure

1. Generate a synthetic dataset with reproducible values.
2. Prepare 30 fixed test questions and reference answers.
3. Run the Gemini agent with Python tools.
4. Run the same Gemini model without tools.
5. Compare outputs with the reference answers.
6. Calculate evaluation metrics.
7. Review incorrect answers and tool-call failures.
8. Summarize results in the final evaluation report.

Evaluation responses are planned to be cached to reduce repeated API usage.

## Data

### Synthetic dataset

The main dataset is generated using a Python script with a fixed random seed.

**Expected columns:**

- `date`
- `campaign`
- `channel`
- `impressions`
- `clicks`
- `ad_spend`
- `conversions`
- `revenue`

The dataset covers several months and includes predefined analytical patterns, such as:

- A channel with the highest ROAS.
- Differences in campaign performance.
- Changes in advertising spending.
- A measurable ROAS decline.
- Trends across time periods.

These known patterns provide reference values for evaluating the agent.

### Public dataset

A small public marketing dataset will be used for independent testing.

**Dataset name:** _to be added_  
**Source:** _to be added_  
**Licence:** _to be added_

The dataset must contain the required analytical fields or be transformed into the expected schema using documented preprocessing.

**Data limitations:**

- No personal customer data.
- No live integration with advertising platforms.
- Historical campaign data only.
- No unsupported causal conclusions.

## Setup

Requirements:

- Python 3.11 or newer.
- Gemini API key from [Google AI Studio](https://aistudio.google.com/).
- Python dependencies listed in `requirements.txt`.

<!-- TODO: Verify all commands and file paths against the final repository before code freeze. -->

### 1. Clone the repository

```bash
git clone <repository-url>
cd <repository-folder>
```

### 2. Create a virtual environment

```bash
python -m venv .venv
```

Activate the environment:

**macOS / Linux:**

```bash
source .venv/bin/activate
```

**Windows:**

```powershell
.venv\Scripts\Activate.ps1
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Configure API access

Create a `.env` file based on `.env.example`.

```bash
cp .env.example .env
```

On Windows, the file can be copied manually or using the appropriate shell command.

Add your Gemini API key:

```dotenv
GEMINI_API_KEY=your-key-here
```

**Security:** Never commit `.env` files or API keys to GitHub.

### 5. Generate synthetic data

```bash
python -m data.generate
```

### 6. Start the application

```bash
streamlit run app.py
```

### 7. Run unit tests

```bash
pytest
```

### 8. Run evaluation

```bash
python -m evaluation.run
```

The evaluation script is planned to run the 30-question test set, calculate metrics and compare the AI agent with the baseline.

## LLM provider and limits

The project uses the **Gemini API** through Google AI Studio.

The Gemini model handles:

- User-question interpretation.
- Analytical planning.
- Function calling.
- Multi-step tool coordination.
- Explanation generation.
- Clarification and refusal decisions.

Python tools handle:

- Data validation.
- KPI calculations.
- Channel and campaign comparisons.
- Period comparisons.
- Trend analysis.
- Numerical results and visualization data.

The LLM client is designed to support alternative providers if necessary, such as OpenRouter or a local Ollama model.

Provider switching is a planned fallback and depends on model compatibility and available resources.

API keys are stored in environment variables.

## Repository structure

<!-- TODO: Update the structure to match the actual repository. -->

```text
.
├── app.py                 # Streamlit interface
├── agent/                 # Agent loop, tool dispatcher, LLM client
├── analytics/             # KPI calculations, comparisons, trends, charts
├── data/                  # Synthetic data generator, loaders, validation
├── evaluation/            # Test set, reference answers, metrics, baseline
├── tests/                 # Unit and numerical tests
├── agent-logs/            # Selected agentic coding sessions
├── requirements.txt
├── .env.example
├── PLAN.md
├── AGENTS.md
├── DECISIONS.md
├── REFLECTION.md
└── README.md
```

## Team and responsibilities

| Member | Module |
|---|---|
| Akim Lunara | **Data and application.** Synthetic dataset generator with planted ground truth, CSV/XLSX loading, schema and data-quality checks, Streamlit application, test-question preparation and independent reference answers. |
| Rustamova Radmila | **Analytics engine.** KPI formulas, channel/campaign/period comparisons, trend calculations, Plotly visualizations, unit and numerical tests, tool schemas agreed with Tomiris. |
| Doshchanova Tomiris | **AI agent.** Gemini function calling, multi-step tool loop, safe tool dispatcher, clarification and refusal logic, response generation, integration and evaluation scripts. |

**Shared responsibilities:**

- All members participate in the 30-question evaluation.
- All members review incorrect outputs and baseline comparison results.
- All members contribute to the final metrics report.
- Everyone maintains their own commits and agent logs.
- Everyone documents important decisions and reflections.
- Every team member should be able to explain the complete AI pipeline.

## How we built it (agentic development)

The project is developed using agentic coding tools.

**Planned development tools:**

- **OpenCode** (free, open source) with available coding models for day-to-day development.
- **Claude Code** (paid) for planning, complex implementation and debugging.
- **GitHub Copilot Free** as an additional development option, subject to available features and limits.

The development process is documented in the repository:

- `PLAN.md` — project plan, milestones and implementation strategy.
- `AGENTS.md` — instructions and guidelines for coding agents.
- `agent-logs/` — selected records of agentic coding sessions.
- `DECISIONS.md` — important implementation decisions, problems and corrections.
- `REFLECTION.md` — team reflections on AI-assisted development and project results.

Any significant development-tool change is documented in `DECISIONS.md`.

## Roadmap

### Checkpoint — 16 November 2026

The first working MVP should demonstrate:

- Upload one campaign CSV file.
- Validate required columns.
- Ask: "Which channel has the highest ROAS?"
- Let the Gemini agent select and call the correct Python tool.
- Display the calculated channel ranking.
- Generate the answer using the tool output.
- Prepare the synthetic dataset generator.
- Agree on tool schemas.
- Run a 10-question mini evaluation.

**Main checkpoint goal:** Demonstrate a working end-to-end tool-use agent.

### Final release — 6 December 2026

The final project should include:

- CSV/XLSX upload.
- Schema and data-quality checks.
- Five marketing KPIs: CTR, CPC, conversion rate, CPA and ROAS.
- Channel, campaign and period comparisons.
- Trend analysis and charts.
- Multi-step analytical reasoning through tool calls.
- Clarification and refusal logic.
- Full 30-question evaluation.
- Baseline comparison.
- Unit and numerical tests.
- Complete documentation.
- Reproducible setup instructions.
- Git tag `v1.0`.

### Stretch goals

If time permits:

1. **Statistical anomaly alerts:** Detect unusual campaign performance using predefined statistical methods.
2. **Downloadable PDF report:** Export analytical results, charts and explanations into a PDF document.

These features are optional and are not required for the core MVP.

## Limitations

- The agent supports a predefined set of marketing analytics operations rather than unrestricted data analysis.
- The agent cannot execute arbitrary Python code.
- The system depends on the accuracy of the uploaded dataset and implemented analytical tools.
- Questions outside the available data or supported analytical operations are handled through clarification or explicit limitations.
- The agent identifies associations and contributions to KPI changes but does not establish causal relationships.
- Synthetic data may produce easier evaluation cases than real campaign exports.
- Independent testing with a public dataset is intended to assess generalization.
- The Gemini API has usage limits, so evaluation runs are planned to be cached and executed in batches.
- The system does not provide real-time advertising optimization or direct integration with advertising platforms.

## Acknowledgements and licences

The project uses or plans to use the following technologies:

- **Python** — analytical programming.
- **pandas** — data processing and calculations.
- **Streamlit** — web application interface.
- **Plotly** — data visualization.
- **Gemini API** — LLM-powered tool-use agent.
- **pytest** — automated testing.

**Public dataset:** _Name, source and licence to be added._

**Library licences:** _To be verified and documented before final submission._

## Project status

**Current stage:** Planning and MVP development.

Evaluation results, screenshots and final implementation details will be added after the corresponding components have been completed and tested.

