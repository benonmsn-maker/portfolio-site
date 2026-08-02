# Agentic RAG Project (CrewAI)

## Overview

This project implements an Agentic Retrieval-Augmented Generation (RAG) workflow in a Jupyter notebook using CrewAI orchestration. The system has two role-based agents:

- Router Agent: classifies each question into a route (`pdf`, `web`, or optional `llm`)
- Retriever Agent: executes the selected route using the appropriate tool and returns a grounded answer

The notebook demonstrates retrieval from both static PDF content and dynamic web search, then records reasoning traces for each run.

## Why CrewAI

CrewAI was selected because it supports:

- explicit role-based agent design
- clear task contracts (`Task`) and orchestration (`Crew`)
- sequential collaboration patterns that match router-to-retriever workflows
- auditable execution flow for traceable reasoning

## System Architecture

The architecture contains four layers:

1. Input layer
- Accepts natural-language questions.

2. Routing layer (Router Agent)
- Uses CrewAI tasking to classify each question into `pdf`, `web`, or optional `llm`.

3. Retrieval layer (Retriever Agent + tools)
- `PDFSearchTool` from `crewai_tools` handles static PDF retrieval.
- `TavilySearchResults` handles current web retrieval.

4. Trace layer
- Stores route decisions and answer previews in structured logs.
- Displays a tabular reasoning trace in the notebook.

## Agent Logic and Responsibilities

### Router Agent

- Reads the user question
- Determines whether the best path is PDF retrieval, web retrieval, or optional direct LLM path
- Outputs one route token used by downstream tasks

### Retriever Agent

- Receives the selected route and original question
- Invokes the corresponding tool (`PDFSearchTool` or `TavilySearchResults`)
- Produces a grounded answer from retrieved evidence
- Reports limitations when context is missing

## Coordination Flow

1. User submits a natural-language question
2. Router Agent classifies route (`pdf`, `web`, or `llm`)
3. Retriever Agent executes the selected retrieval path
4. Retriever Agent generates grounded output
5. System logs route and response preview in trace log
6. Notebook shows final answers and trace visualization

## Project Files

- `agentic_rag_notebook.ipynb`: full notebook implementation and demo
- `requirements.txt`: Python dependencies
- `README.md`: architecture and implementation summary

## Setup

1. Install dependencies:

```bash
pip install -r requirements.txt
```

2. Set API keys (optional for partial demo, recommended for full run):

```bash
# PowerShell
$env:OPENAI_API_KEY="your_openai_key"
$env:TAVILY_API_KEY="your_tavily_key"
```

3. Open `agentic_rag_notebook.ipynb` and run from top to bottom.

## Demo Expectations

A successful notebook run should show:

- route classification by Router Agent
- tool-driven retrieval by Retriever Agent
- at least one PDF-grounded output
- at least one web-grounded output (if `TAVILY_API_KEY` is configured)
- trace logs for each question

## Challenges Faced and Trade-offs Made

- Dependency variability:
  Tool APIs can vary by package version. The notebook includes guarded tool invocation paths to reduce runtime breakage.

- Reliability vs strictness:
  Routing output is parsed into fixed route tokens to prevent downstream failures from free-form LLM text.

- Optional web path constraints:
  Web retrieval requires network and a valid Tavily key. The notebook reports this limitation instead of failing silently.

- CrewAI and Tavily tool compatibility:
  In this environment, passing TavilySearchResults directly in the CrewAI agent tools list caused validation/type issues. The trade-off was to keep Tavily fully integrated for web retrieval, but invoke it through a dedicated helper path instead of registering it as a CrewAI BaseTool object.

- Windows console encoding crash during validation:
  The notebook checker initially failed with a `UnicodeEncodeError` when printing PDF-derived Unicode characters (for example, Greek symbols) in a cp1252 terminal. The trade-off was to harden the checker output encoding to UTF-8 with replacement behavior for reliable cross-terminal execution.

- Simplicity vs production depth:
  The implementation prioritizes clarity and grading-aligned evidence over advanced production hardening.

## Submission Checklist Mapping

- Multi-agent coordination (Router + Retriever): included
- CrewAI orchestration with defined roles/tasks: included
- PDF and web retrieval paths: included
- Interaction/trace logging: included
- Notebook demo and README rationale: included
