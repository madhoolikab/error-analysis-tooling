# Recipe Chatbot — Eval Viewer

Reference implementation for the blog post **"Stop Sabotaging Your Product with Ready-Made Evals"**.

> Blog post: <!-- Placeholder - ADD_BLOG_URL_HERE -->

The repo contains a vegetarian recipe chatbot, a script to run a query dataset against it, and a browser-based viewer to annotate and analyse the results.

---

## Quickstart

**Prerequisites:** Python 3.10+, an API key for any LLM provider supported by [LiteLLM](https://docs.litellm.ai/docs/providers) (OpenRouter, OpenAI, Anthropic, Gemini, Together, etc.)

```bash
# 1. Install dependencies
pip install -r requirements.txt

# 2. Configure your API key
cp .env.example .env
# Edit .env and set MODEL_NAME + your provider's API key

# 3. Run the query dataset against the chatbot
python scripts/bulk_test.py

# 4. Open the eval viewer
python server.py results/results_<timestamp>.json
# Then open http://localhost:8765 in your browser
```

**Want to skip straight to the viewer?** An example results file is already included:

```bash
python server.py results/example_results.json
```

---

## Project Structure

```
├── backend/
│   ├── system_prompt.md   # System prompt for the recipe chatbot
│   └── utils.py           # LiteLLM wrapper — swap models via .env
├── scripts/
│   └── bulk_test.py       # Runs sample_queries.csv concurrently, saves results JSON
├── data/
│   └── sample_queries.csv # 37 test queries across cuisines, dietary needs, difficulty
├── results/
│   └── example_results.json  # Pre-generated results — load this to try the viewer immediately
├── viewer.html            # Single-page annotation UI
└── server.py              # Serves viewer.html and persists annotations back to the results file
```

---

## Eval Viewer Features

- Sidebar with all queries, colour-coded by annotation status (done / review / unannotated)
- Editable notes per result, saved back to the JSON file
- Search bar to find specific failure patterns across traces
- Keyboard shortcuts for navigating between results
- Progress bar

![Eval Viewer](eval_viewer.png)

---

## Changing the Model

Edit `MODEL_NAME` in `.env` to any LiteLLM-supported model string, e.g.:

```
MODEL_NAME=anthropic/claude-sonnet-4-6
MODEL_NAME=openai/gpt-4.1-mini
MODEL_NAME=gemini/gemini-2.0-flash
```

---

## License

GNU GPL Version 3 — see [LICENSE](LICENSE).
