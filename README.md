# EcoBot — Your Personal Energy Analyst

EcoBot is a conversational energy analysis application.
It allows users to upload solar production and household consumption data
and query them in natural language through an interactive chat.

---

## Architecture

The application uses a **single agent** built with the **ReAct** pattern via LangGraph.

**Why a single agent?**

The domain is narrow and well-defined: two datasets and a fixed set of operations
(cleaning, analysis, visualization, and recommendations).
A multi-agent system would add unnecessary coordination complexity for this scope.

The agent has access to **6 tools**:

| Tool                | Responsibility                                                               |
| ------------------- | ---------------------------------------------------------------------------- |
| clean_data          | Cleans raw CSV data (NaN interpolation, negative values → 0)                 |
| analyze_production  | Descriptive statistics on solar production                                   |
| user_consumption    | Descriptive statistics on household consumption                              |
| charts_design       | Generates PNG charts (daily production + production vs consumption)          |
| advisor             | Personalized energy-saving recommendations (surplus hours, self-sufficiency) |
| forecast_production | Estimates today's solar production via Forecast.Solar API                    |

The underlying model is `llama-3.3-70b-versatile` via the **Groq API**.
The user interface is built with **Streamlit**.

---

## Prerequisites

- Python 3.10+
- A valid Groq API key
- Jupyter Notebook or JupyterLab
- The required Python libraries installed in your environment.

---

## Setup Instructions

### 1. Install dependencies

You can install the dependencies manually:

```bash
pip install streamlit langchain langchain-groq langgraph python-dotenv pandas numpy matplotlib seaborn requests
```

Or install them from a `requirements.txt` file, if available.

### 2. Configure API Key

Create a `.env` file in the project root:

```env
GROQ_API_KEY=your_groq_api_key_here
```

A free API key can be created from [https://console.groq.com](https://console.groq.com).

### 3. Generate the Streamlit app

Run the notebook cell that writes `app.py`.

This project generates the Streamlit app from the notebook, so `app.py`
must be created or overwritten before running Streamlit.

### 4. Run the app

```bash
streamlit run app.py
```

The app will open in your browser at `http://localhost:8501`.

---

## Usage

1. Upload the two CSV files from the **sidebar**:
   - `solar_production_raw.csv`
   - `household_consumption.csv`
(If the columns don't match, an error message will appear before the agent is initialized.)

2. Wait for the confirmation message:
   ✅ *"Datas uploaded! EcoBot is ready."*
3. Ask your questions in the chat interface.

---

## Example Use Cases

**Production analysis**

> "How much energy did I produce?"
> → Total kWh, hourly average, best day, worst day, peak production hour.

**Consumption analysis**

> "What is my household consumption?"
> → Total consumed energy, hourly average, highest and lowest day, peak hour.

**Visualization**

> "Show me a chart of my production vs consumption"
> → A PNG chart with daily production and a production-versus-consumption comparison.

**Energy-saving recommendations**

> "How can I reduce my electricity bill?"
> → Best surplus hours for appliance usage, evening peak analysis, and self-sufficiency suggestions.

**Solar forecast**
>"How much energy will I produce today?"
>→ Hourly forecast and daily total via Forecast.Solar API (default: Lima, Peru, 5 kWp).

---

## Project Structure

```text
├── agente.ipynb
├── app.py
├── .env
├── solar_production_raw.csv
├── household_consumption.csv
├── solar_production_clean.csv       
├── household_consumption_clean.csv 
└── charts/
    └── analisi_energetica.png
```

`app.py` is generated from the notebook.
The cleaned CSV files and chart image are created during execution.

---

## Tech Stack

- **LLM:** `llama-3.3-70b-versatile` via Groq
- **Agent framework:** LangGraph with `create_react_agent`
- **UI:** Streamlit
- **Data processing:** Pandas, NumPy
- **Visualization:** Matplotlib, Seaborn.
