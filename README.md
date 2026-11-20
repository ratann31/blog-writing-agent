# Blog Writing Agent

An AI-powered blog generation application built with **Python, LangGraph, Streamlit, and LLM APIs**. It takes a topic, decides whether fresh web research is needed, creates a structured blog outline, writes the sections in parallel, and can generate supporting images.

## Demo

> **Add your project demo here:** record a short screen capture of the Streamlit app and save it as `assets/demo.gif` (or `assets/demo.mp4`). Then uncomment the relevant line below.

<!-- ![Blog Writing Agent Demo](assets/demo.gif) -->
<!-- [Watch the demo video](assets/demo.mp4) -->

Suggested demo flow:
1. Enter a blog topic in the sidebar.
2. Select the “As-of date”.
3. Click **Generate Blog**.
4. Show the generated plan, research evidence, Markdown preview, images, and event logs.
5. Download the Markdown file or the complete Markdown + images bundle.

## Features

- **Research-aware routing:** classifies a topic as `closed_book`, `hybrid`, or `open_book` before writing.
- **Web research:** uses Tavily search results when research is needed and a Tavily API key is configured.
- **Structured planning:** creates a blog title, audience, tone, blog type, constraints, and section-level tasks.
- **Parallel section writing:** uses LangGraph workers to write individual blog sections.
- **Image planning and generation:** proposes up to three useful diagrams/images and can generate them with Gemini.
- **Markdown preview:** displays the generated blog and local images in Streamlit.
- **Downloads:** export the blog as Markdown or download a ZIP containing the Markdown and generated images.
- **Past blogs:** load previously saved Markdown blogs from the working directory.

## Architecture

```text
User enters topic
       |
       v
     Router
       |
       +---- Research needed? ---- Yes ----> Tavily Research
       |                                      |
       No                                     |
       |                                      |
       +------------------+-------------------+
                          |
                          v
                    Orchestrator
                 (structured blog plan)
                          |
                          v
                Parallel section workers
                          |
                          v
                    Merge sections
                          |
                          v
                    Decide images
                          |
                          v
                Generate/place images
                          |
                          v
                  Final Markdown blog
```

## Project Structure

```text
blog-writing-agent-main/
├── 1_bwa_basic.ipynb
├── 2_bwa_improved_prompting.ipynb
├── 3_bwa_research.ipynb
├── 4_bwa_research_fine_tuned.ipynb
├── 5_bwa_image.ipynb
├── bwa_backend.py
├── bwa_frontend.py
└── tavily_test.ipynb
```

The notebooks show the project's iterative development, from basic blog generation and prompting improvements through research and image generation. The Streamlit application uses `bwa_frontend.py` and the LangGraph workflow in `bwa_backend.py`.

## Tech Stack

- **Python**
- **Streamlit** — user interface
- **LangGraph** — workflow orchestration and parallel section generation
- **LangChain / langchain-openai** — model integration
- **OpenAI model (`gpt-4.1-mini`)** — routing, planning, and content generation
- **Tavily** — web search for research-enabled blog generation
- **Google GenAI (`gemini-2.5-flash-image`)** — optional image generation
- **Pydantic** — structured output schemas
- **Pandas** — displaying structured plan/evidence data

## Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/<YOUR_USERNAME>/blog-writing-agent.git
cd blog-writing-agent
```

### 2. Create and activate a virtual environment

**Windows (PowerShell):**

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
```

**macOS / Linux:**

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Install dependencies

Install the packages used by the application:

```bash
pip install streamlit langgraph langchain langchain-openai langchain-community pydantic python-dotenv pandas google-genai
```

### 4. Configure environment variables

Create a `.env` file in the project root:

```env
OPENAI_API_KEY=your_openai_api_key
TAVILY_API_KEY=your_tavily_api_key
GOOGLE_API_KEY=your_google_api_key
```

- `OPENAI_API_KEY` is required for the LLM workflow.
- `TAVILY_API_KEY` enables web research. Without it, the research helper returns no search evidence.
- `GOOGLE_API_KEY` is required for Gemini image generation when the workflow requests images.

Do not commit `.env` or expose API keys in the repository. Add `.env` to `.gitignore`.

### 5. Run the application

From the project root, run:

```bash
streamlit run bwa_frontend.py
```

Streamlit will print a local URL (typically `http://localhost:8501`). Open it in your browser.

## How to Use

1. Type a topic into the **Topic** field in the sidebar.
2. Choose the date used as the research reference date.
3. Click **Generate Blog**.
4. Explore the output in the tabs:
   - **Plan** — generated outline and section tasks.
   - **Evidence** — sources returned by the research step, when available.
   - **Markdown Preview** — rendered blog content.
   - **Images** — image plan and generated files, when available.
   - **Logs** — workflow progress and event output.
5. Download the Markdown file or the Markdown + images ZIP bundle.

## Configuration Notes

- The backend currently initializes `ChatOpenAI` with `gpt-4.1-mini`.
- Research is optional and depends on Tavily search results and configuration.
- Image generation is optional and uses the Gemini image model configured in `bwa_backend.py`.
- If image generation fails, the backend keeps the blog usable and inserts an explanatory placeholder instead of stopping the entire workflow.
- Generated Markdown files and the `images/` directory are written relative to the application's current working directory.

## Security

- Never commit `.env`, API keys, access tokens, or other credentials.
- If a key was accidentally pushed to GitHub, revoke/rotate it immediately; deleting it from the latest commit alone may not remove it from Git history.

## Future Improvements

Potential next steps for the project include:
- Add automated tests for graph nodes and end-to-end blog generation.
- Add user authentication and per-user blog history.
- Persist generated blogs in a database instead of relying on local Markdown files.
- Add configurable writing styles, target length, and model selection.
- Add retries, timeouts, and clearer error reporting for external API calls.

## License

No license is currently specified. Add a `LICENSE` file if you intend to define how others may use, modify, and distribute this project.
