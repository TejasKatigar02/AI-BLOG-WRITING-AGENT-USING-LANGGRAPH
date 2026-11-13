# Blog Writing Agent

An AI agent that writes technical blog posts from a single topic. It decides whether a topic needs web research, plans the article as a set of sections, writes each section in parallel, and adds diagrams where they help. A Streamlit interface lets you generate posts, review the plan and sources, preview the Markdown, and download the result.

Built with LangGraph, LangChain, OpenAI, Tavily, and Google Gemini.

## How It Works

The agent is a LangGraph workflow:

1. **Router:** Decides whether the topic needs research and which mode to use: `closed_book` (evergreen concepts), `hybrid` (evergreen plus some current examples), or `open_book` (news or "latest" topics).
2. **Research:** For hybrid and open-book topics, runs Tavily web searches and extracts deduplicated evidence with source URLs and dates. Open-book results are limited to a recent window (7 days).
3. **Orchestrator:** Turns the topic and evidence into a structured plan with a blog title, audience, tone, and 5–9 tasks, each with bullets and a word target.
4. **Workers:** Write each section in parallel. Cited claims link to the evidence URLs, and news roundups avoid drifting into tutorial content.
5. **Reducer:** Merges the sections, asks the model whether up to three diagrams would help, generates them with Gemini, and inserts them into the Markdown.

Output is saved to `<blog-title>.md` in the working folder, with images in `images/`.

## Features

- Automatic routing between closed-book, hybrid, and open-book writing
- Source-grounded sections with inline links for research-backed claims
- Structured planning with Pydantic schemas
- Parallel section writing with LangGraph `Send`
- Generated diagrams inserted into the article, with a text fallback if image generation fails
- Streamlit UI with plan, evidence, preview, images, and logs tabs
- Download as Markdown or as a ZIP bundle with images
- Browse and reload previously saved posts from the sidebar

## Requirements

- Python 3.10 or newer
- An OpenAI API key (required)
- A Tavily API key (optional, enables web research)
- A Google API key with access to `gemini-2.5-flash-image` (optional, enables diagrams)

Python packages:

```
streamlit
pandas
langgraph
langchain-core
langchain-openai
langchain-community
tavily-python
pydantic
python-dotenv
google-genai
```

## Setup

1. Clone the repository and move into it:

   ```bash
   git clone https://github.com/YOUR-USERNAME/YOUR-REPO.git
   cd YOUR-REPO
   ```

2. Install the dependencies:

   ```bash
   pip install streamlit pandas langgraph langchain-core langchain-openai langchain-community tavily-python pydantic python-dotenv google-genai
   ```

3. Create a `.env` file in the project folder with your keys:

   ```bash
   OPENAI_API_KEY=your-openai-key
   TAVILY_API_KEY=your-tavily-key
   GOOGLE_API_KEY=your-google-key
   ```

   Keep `.env` out of Git. Do not commit real keys.

## Usage

Run the Streamlit app from the project folder, since the frontend imports `bwa_backend.py` from the same directory:

```bash
streamlit run bwa_frontend.py
```

Then:

1. Enter a topic in the sidebar.
2. Pick an as-of date. Recency filtering is based on this date.
3. Click **Generate Blog** and watch the node-by-node progress.
4. Review the **Plan**, **Evidence**, and **Images** tabs, then read the result in **Markdown Preview**.
5. Download the Markdown file or the bundle.

Without a Tavily key the agent still runs, but it skips research and writes from its own knowledge. Without a Google key, diagrams are replaced with a message describing the image that would have been generated.

## Project Structure

| File | Purpose |
| --- | --- |
| `bwa_backend.py` | LangGraph workflow: router, research, planning, workers, and image reducer |
| `bwa_frontend.py` | Streamlit interface |
| `1_bwa_basic.ipynb` | First prototype: basic blog generation |
| `2_bwa_improved_prompting.ipynb` | Prompt refinements |
| `3_bwa_research.ipynb` | Adding web research |
| `4_bwa_research_fine_tuned.ipynb` | Research tuning |
| `5_bwa_image.ipynb` | Image generation experiments |
| `tavily_test.ipynb` | Quick check of Tavily search results |
| `images/` | Generated diagrams (created at runtime) |

## Notes

- Generated posts and images are written to the folder you run the app from. Add `*.md` and `images/` to `.gitignore` if you don't want them in the repository.
- The model used is `gpt-4.1-mini`, set in `bwa_backend.py`.

## License

Add your preferred license here.
