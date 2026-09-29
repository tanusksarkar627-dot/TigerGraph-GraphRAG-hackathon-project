# TigerGraph-GraphRAG-hackathon-project
This is an Agentic AI model built for a working Agentic GraphRAG system covering the core challenge. Our system answer questions in three ways - RAG, GraphRAG, and Agentic GraphRAG and show where each approach succeeds or fails.
🧠 Agentic GraphRAG with TigerGraph

«An evidence-driven question answering system that combines Retrieval-Augmented Generation, Knowledge Graph reasoning, and autonomous agentic investigation.»

""TigerGraph" (https://img.shields.io/badge/Database-TigerGraph%20Savanna-blue)" (#)
""GraphRAG" (https://img.shields.io/badge/Architecture-Agentic%20GraphRAG-purple)" (#)
""Python" (https://img.shields.io/badge/Python-3.11+-yellow)" (#)
""License" (https://img.shields.io/badge/License-MIT-green)" (#)

---

📌 Overview

This project was developed for the TigerGraph Agentic GraphRAG Hackathon.

The goal is to build a question-answering system that can investigate complex questions using:

- 📄 Document evidence
- 🔎 Vector similarity search
- 🕸️ Knowledge graph relationships
- 🤖 Autonomous agentic reasoning
- 📊 Evidence evaluation
- 📈 Quantitative benchmarking

Unlike a conventional RAG application that retrieves documents once and generates an answer, our system allows an orchestrator agent to decide what information to investigate next based on the evidence already collected and the remaining information gaps.

The system evaluates the same benchmark questions through three progressively more capable approaches:

                         USER QUESTION
                              │
             ┌────────────────┼────────────────┐
             │                │                │
             ▼                ▼                ▼
          RAG              GraphRAG       Agentic GraphRAG
             │                │                │
             ▼                ▼                ▼
       Vector / Docs      Graph + Docs    Autonomous Investigation
             │                │                │
             ▼                ▼                ▼
          Answer           Answer          Evidence + Answer

This allows us to compare not only answer quality, but also accuracy, completeness, evidence coverage, and token efficiency.

---

🎯 Problem

Traditional question-answering systems often struggle when a question requires information scattered across multiple documents or connected through relationships.

For example:

«"What is the relationship between Entity A, Entity B, and Event C, and what evidence supports that relationship?"»

A standard RAG pipeline may retrieve documents containing the individual entities but fail to understand how they are connected.

A graph-based system can identify relationships, but complex questions may require several different investigations.

An agentic system addresses this by repeatedly asking:

What do I know?
      ↓
What am I still missing?
      ↓
Which tool should I use next?
      ↓
What evidence did I find?
      ↓
Is the evidence sufficient?
      │
   ┌──┴──┐
   │     │
  YES    NO
   │     │
   ▼     └──────► Investigate again
Answer

---

🚀 Our Approach

The system implements three independent pipelines.

1. Standard RAG

Retrieval-Augmented Generation retrieves relevant documents or chunks using semantic/vector similarity.

flowchart LR
    Q[User Question] --> E[Embedding]
    E --> V[Vector Search]
    V --> D[Relevant Documents]
    D --> L[LLM]
    L --> A[Final Answer]

Strengths

- Simple architecture
- Fast retrieval
- Good for directly stated information
- Relatively low reasoning overhead

Limitations

- Primarily document-centric
- Can miss relationships between entities
- May struggle with multi-hop questions
- Retrieval quality depends heavily on semantic similarity

---

2. GraphRAG

GraphRAG combines graph traversal with document/vector evidence.

flowchart LR
    Q[User Question]
    Q --> EL[Entity Linking]
    EL --> G[TigerGraph]
    G --> R[Graph Traversal]
    R --> E[Related Entities]
    E --> D[Supporting Documents]
    D --> L[LLM]
    L --> A[Final Answer]

The graph provides structural context that may not be obvious from individual documents.

Example reasoning

Entity A
   │
   ├── related_to ──► Entity B
   │                      │
   │                      └── participated_in ──► Event C
   │
   └── mentioned_in ──► Document D

The system can combine these relationships with textual evidence before generating the answer.

---

3. Agentic GraphRAG 🤖

Agentic GraphRAG adds an autonomous investigation layer.

Instead of following a fixed retrieval sequence, an orchestrator agent decides the next action dynamically.

flowchart TD
    Q[Complex User Question] --> O[Orchestrator Agent]

    O --> P[Inspect Current Evidence]
    P --> D{Information Gap?}

    D -->|No| S[Evidence Sufficient]
    S --> A[Generate Final Answer]

    D -->|Yes| T{Choose Next Tool}

    T --> V[Vector Search]
    T --> G[Graph Traversal]
    T --> R[Document Retrieval]
    T --> E[Entity Linking]
    T --> X[Aggregation / Analysis]

    V --> M[Evidence Manager]
    G --> M
    R --> M
    E --> M
    X --> M

    M --> C[Update Investigation State]
    C --> O

The important difference is that the agent does not simply execute:

Search → Graph → Search → Answer

Instead, the next action depends on:

- The original question
- Entities identified so far
- Graph relationships discovered
- Evidence already collected
- Missing information
- Evidence quality
- Investigation budget
- Stopping criteria

---

🏗️ System Architecture

flowchart TB

    U[👤 User]

    UI[Web Interface]

    ORC[🤖 Agent Orchestrator]

    STATE[🧠 Investigation State]
    EVID[📚 Evidence Manager]
    STOP[🛑 Stopping Criteria]

    RAG[📄 RAG Retriever]
    GRAPH[🕸️ Graph Retriever]
    VECTOR[🔎 Vector Search]
    DOC[📑 Document Retriever]
    ENTITY[🔗 Entity Linking]
    AGG[📊 Aggregation / Reasoning]

    TG[(TigerGraph Savanna)]
    DOCS[(Document Store)]
    VDB[(Vector Index)]

    LLM[🧠 LLM / Reasoning Model]

    BENCH[📈 Benchmark & Metrics]
    OUT[💬 Answer + Evidence]

    U --> UI
    UI --> ORC

    ORC --> STATE
    ORC --> EVID
    ORC --> STOP

    ORC --> RAG
    ORC --> GRAPH
    ORC --> VECTOR
    ORC --> DOC
    ORC --> ENTITY
    ORC --> AGG

    GRAPH --> TG
    VECTOR --> VDB
    DOC --> DOCS
    ENTITY --> TG

    RAG --> EVID
    GRAPH --> EVID
    VECTOR --> EVID
    DOC --> EVID
    AGG --> EVID

    EVID --> STATE
    STATE --> ORC

    EVID --> LLM
    STOP --> LLM

    LLM --> OUT
    OUT --> UI

    RAG --> BENCH
    GRAPH --> BENCH
    ORC --> BENCH

---

🧩 Agent Harness

The agent harness acts as the control layer around the reasoning model.

It is responsible for managing:

Component| Responsibility
State| Maintains the current investigation
Tools| Provides graph, vector, document and analysis capabilities
Evidence| Stores retrieved information and provenance
Context| Controls what information is sent to the reasoning model
Budget| Limits unnecessary tool calls and token usage
Stopping Criteria| Determines when the investigation is sufficient
Error Handling| Handles failed or empty retrievals
Trace| Records the investigation path for explainability

---

🔄 Agent Investigation Loop

The core agentic loop is:

flowchart TD

    START([Question]) --> PLAN[Analyze Question]

    PLAN --> ENTITY[Identify Entities / Concepts]

    ENTITY --> STATE[Initialize Investigation State]

    STATE --> DECIDE[Orchestrator Chooses Next Action]

    DECIDE --> TOOL{Tool}

    TOOL -->|Graph| GRAPH[TigerGraph Traversal]
    TOOL -->|Vector| VECTOR[Similarity Search]
    TOOL -->|Documents| DOC[Document Retrieval]
    TOOL -->|Entity| LINK[Entity Linking]
    TOOL -->|Analysis| ANALYZE[Aggregation / Reasoning]

    GRAPH --> EVID[Collect Evidence]
    VECTOR --> EVID
    DOC --> EVID
    LINK --> EVID
    ANALYZE --> EVID

    EVID --> CHECK{Enough Evidence?}

    CHECK -->|No| GAP[Identify Missing Information]
    GAP --> DECIDE

    CHECK -->|Yes| VERIFY[Evaluate Evidence]

    VERIFY --> ANSWER[Generate Grounded Answer]

    ANSWER --> END([Final Response])

---

🕸️ Why TigerGraph?

TigerGraph provides the graph layer used to represent entities and their relationships.

The graph allows the system to move beyond isolated document retrieval and investigate connected information.

Conceptually:

                 ┌──────────────┐
                 │   Entity A   │
                 └──────┬───────┘
                        │
                  related_to
                        │
                        ▼
                 ┌──────────────┐
                 │   Entity B   │
                 └──────┬───────┘
                        │
                  participated_in
                        │
                        ▼
                 ┌──────────────┐
                 │    Event C   │
                 └──────┬───────┘
                        │
                   described_by
                        │
                        ▼
                 ┌──────────────┐
                 │  Document D  │
                 └──────────────┘

TigerGraph Savanna provides managed graph infrastructure and supports graph development, querying, APIs, vector capabilities, and AI/MCP integrations.

---

📚 Evidence-First Answering

The system does not treat the language model's generated text as evidence.

Instead, the answer generation process follows:

Question
   ↓
Investigation
   ↓
Retrieved Evidence
   ↓
Evidence Evaluation
   ↓
Evidence Selection
   ↓
Reasoning
   ↓
Answer

Each important claim should be traceable to retrieved evidence wherever the underlying dataset permits it.

This improves:

- Explainability
- Reproducibility
- Debugging
- Judgeability
- Resistance to unsupported claims

---

📊 Benchmarking

One of the central requirements of the project is to evaluate all three approaches using the same questions.

flowchart LR

    Q[Benchmark Question Set]

    Q --> RAG[RAG]
    Q --> GR[GraphRAG]
    Q --> AGR[Agentic GraphRAG]

    RAG --> M1[Metrics]
    GR --> M2[Metrics]
    AGR --> M3[Metrics]

    M1 --> DASH[📊 Comparison Dashboard]
    M2 --> DASH
    M3 --> DASH

Metrics

The benchmark records metrics such as:

Metric| Purpose
Accuracy| How correct is the answer?
Completeness| How much of the required information was covered?
Token Usage| How much model context was consumed?
Evidence Count| How much supporting evidence was retrieved?
Tool Calls| How many investigation actions were required?
Latency| How long did the pipeline take?
Evidence Coverage| How well are answer claims supported?

The exact evaluation implementation may vary depending on the official benchmark and available ground truth.

---

⚖️ Pipeline Comparison

The project intentionally does not assume that Agentic GraphRAG is automatically better for every question.

Instead, the benchmark asks:

«When does each architecture succeed, and when does it fail?»

                 QUESTION
                    │
       ┌────────────┼────────────┐
       ▼            ▼            ▼
      RAG        GraphRAG    Agentic GraphRAG
       │            │            │
       ▼            ▼            ▼
   Semantic      Structural    Adaptive
   Retrieval     Retrieval     Investigation
       │            │            │
       └────────────┼────────────┘
                    ▼
               COMPARISON
                    │
        ┌───────────┼───────────┐
        ▼           ▼           ▼
     Accuracy   Completeness   Efficiency

---

🔬 Investigation Trace

For Agentic GraphRAG, the interface exposes the investigation process.

Example:

Question
  │
  ├── Step 1: Identify entities
  │       └── Entity A
  │       └── Entity B
  │
  ├── Step 2: Traverse graph
  │       └── Found relationship A → B
  │
  ├── Step 3: Retrieve supporting documents
  │       └── Document 17
  │       └── Document 42
  │
  ├── Step 4: Detect missing evidence
  │       └── Event information incomplete
  │
  ├── Step 5: Search again
  │       └── Found Event C
  │
  ├── Step 6: Evaluate evidence
  │       └── Evidence sufficient
  │
  └── Final Answer

This trace makes the agent's behavior inspectable instead of presenting only a final generated response.

---

🖥️ User Interface

The planned interface is designed around three things:

1. Ask

┌──────────────────────────────────────────────┐
│ Ask a complex question...                    │
│                                              │
│ [__________________________________________] │
│                                              │
│              [ Investigate ]                 │
└──────────────────────────────────────────────┘

2. Compare

┌────────────────┬────────────────┬────────────────────┐
│      RAG       │    GraphRAG    │ Agentic GraphRAG   │
├────────────────┼────────────────┼────────────────────┤
│ Answer         │ Answer         │ Answer             │
│                │                │                    │
│ Sources        │ Graph paths    │ Investigation trace│
│                │                │                    │
│ Tokens         │ Tokens         │ Tokens             │
└────────────────┴────────────────┴────────────────────┘

3. Investigate

Agentic GraphRAG exposes:

- Current reasoning state
- Tools selected
- Retrieved evidence
- Graph paths
- Missing information
- Investigation steps
- Final evidence-backed answer

---

🧱 Project Structure

.
├── app/
│   ├── api/
│   ├── agents/
│   │   ├── orchestrator/
│   │   ├── state/
│   │   └── tools/
│   │
│   ├── retrieval/
│   │   ├── rag/
│   │   ├── graph/
│   │   └── vector/
│   │
│   ├── evidence/
│   ├── evaluation/
│   └── config/
│
├── frontend/
│
├── data/
│   └── README.md
│
├── graph/
│   ├── schema/
│   ├── queries/
│   └── README.md
│
├── benchmarks/
│   ├── questions/
│   ├── evaluation/
│   └── results/
│
├── scripts/
│   ├── ingestion/
│   ├── evaluation/
│   └── utilities/
│
├── tests/
│
├── docs/
│   ├── architecture.md
│   └── evaluation.md
│
├── .env.example
├── requirements.txt
├── README.md
└── LICENSE

«The exact directory structure may evolve during implementation. The important principle is keeping retrieval, agent orchestration, evaluation, and infrastructure concerns separated.»

---

⚙️ Technology Stack

Layer| Technology
Graph Database| TigerGraph Savanna
Graph Querying| GSQL / TigerGraph APIs
Vector Retrieval| TigerGraph Vector capabilities / configured vector layer
Backend| Python
Agent Orchestration| Custom orchestration layer
LLM| Configurable model provider
Frontend| Web-based interface
Evaluation| Automated benchmark pipeline
Version Control| Git + GitHub

TigerGraph's current Savanna platform provides APIs for interacting with graph databases, while pyTigerGraph can be used by Python applications to query and operate on the database.

---

🔐 Security

Secrets are never committed to this repository.

Sensitive configuration should be provided through environment variables.

Example:

TIGERGRAPH_HOST=
TIGERGRAPH_SECRET=
TIGERGRAPH_GRAPH=
LLM_API_KEY=

A safe configuration flow is:

Environment / Secret Manager
          │
          ▼
      Application
          │
          ▼
   TigerGraph / LLM APIs

The repository should contain:

.env.example

but never:

.env

or real API keys/database credentials.

TigerGraph Savanna uses database secrets for application and AI-tool access, so credentials should be treated as sensitive configuration.

---

🚀 Getting Started

Prerequisites

Before running the project, you will need:

- Python 3.11+
- Git
- A configured TigerGraph Savanna workspace
- A TigerGraph graph/database
- Required model/API credentials
- The benchmark dataset

---

1. Clone the repository

git clone <YOUR_REPOSITORY_URL>
cd <YOUR_REPOSITORY_NAME>

---

2. Create a virtual environment

python -m venv .venv

Activate it:

Linux / macOS

source .venv/bin/activate

Windows

.venv\Scripts\activate

---

3. Install dependencies

pip install -r requirements.txt

---

4. Configure environment variables

Copy:

cp .env.example .env

Then configure the required credentials.

Never commit ".env".

---

5. Configure TigerGraph

The application expects access to the configured TigerGraph Savanna graph.

The graph should contain the schema and data required by the benchmark.

TigerGraph Savanna supports loading data, designing schemas, running GSQL queries, exploring graph data, and connecting applications through APIs.

---

6. Run the application

python -m app

Or use the project-specific startup command provided by the implementation.

---

🧪 Running the Benchmark

The benchmark executes the same question set through:

              Benchmark Dataset
                     │
          ┌──────────┼──────────┐
          ▼          ▼          ▼
         RAG      GraphRAG   Agentic GraphRAG
          │          │          │
          ▼          ▼          ▼
       Evaluate   Evaluate   Evaluate
          │          │          │
          └──────────┼──────────┘
                     ▼
              Compare Results

Example:

python scripts/run_benchmark.py

Results are written to:

benchmarks/results/

Example result structure:

{
  "question_id": "Q001",
  "pipeline": "agentic_graphrag",
  "accuracy": 0.0,
  "completeness": 0.0,
  "tokens": 0,
  "tool_calls": 0,
  "latency_ms": 0
}

«Metric values shown above are placeholders representing the output format, not benchmark results.»

---

📈 Evaluation Philosophy

We evaluate the systems using the same questions and comparable conditions.

The objective is not to manufacture a result where the most complex architecture always wins.

Instead, we investigate:

RAG

«Does semantic document retrieval provide enough evidence?»

GraphRAG

«Does explicit graph structure improve multi-hop reasoning?»

Agentic GraphRAG

«Does adaptive investigation improve evidence coverage on questions where a single retrieval strategy is insufficient?»

This makes the benchmark a comparison of architectures, rather than a demonstration designed around one favorable example.

---

🧠 Design Principles

1. Evidence before generation

The system should retrieve evidence before asking the model to formulate an answer.

2. Dynamic investigation

The agent should choose its next action based on the current investigation state.

3. Explicit stopping criteria

The agent should stop when the evidence is sufficient, rather than continuing indefinitely.

4. Traceability

Important answer claims should be connected to evidence.

5. Reproducibility

Benchmark questions, configurations, metrics and experiment results should be reproducible.

6. Failure awareness

The system should be able to report when available evidence is insufficient instead of confidently inventing information.

---

🛑 Failure Handling

The system should handle situations such as:

- No matching entity
- Empty graph traversal
- No relevant documents
- Conflicting evidence
- Insufficient evidence
- API failure
- Tool timeout
- LLM failure
- Invalid query
- Investigation budget exceeded

Example:

Question
   ↓
Investigation
   ↓
No sufficient evidence
   ↓
┌─────────────────────────────┐
│ Evidence insufficient       │
│                             │
│ The system cannot establish │
│ this claim from the current │
│ dataset.                    │
└─────────────────────────────┘

---

🔮 Future Extensions

The architecture is designed to support additional reasoning capabilities.

Potential extensions include:

- ⏳ Temporal reasoning
- 🔀 Conflicting evidence resolution
- 📅 Fact supersession
- 🏷️ Source authority weighting
- ❓ Uncertainty estimation
- 🧩 More specialized investigation tools
- 🧠 Improved planning strategies
- 📊 Cost-aware agent planning
- 🔍 Claim-level evidence verification
- ⚡ Parallel tool execution

These capabilities can be added without replacing the core retrieval architecture.

---

🏆 Hackathon Alignment

The system is designed around the core evaluation areas of the challenge:

Evaluation Area| How the Project Addresses It
Investigation Accuracy| Multi-step evidence gathering
Evidence Quality| Explicit evidence collection and provenance
Agentic Effectiveness| Dynamic tool selection
Efficiency| Token/tool-call tracking
Engineering Quality| Modular retrieval and orchestration
Innovation
Agentic investigation over graph + vector + documents
Presentation
Interactive comparison and investigation trace
📊 What Makes the System Agentic?
A simple workflow:
Question
   ↓
Search
   ↓
Answer
is not sufficient to demonstrate autonomous investigation.
Our agent instead follows:
Question
   ↓
Understand
   ↓
Investigate
   ↓
Observe
   ↓
Identify missing information
   ↓
Choose next action 
   ↓
Investigate again
   ↓
Evaluate evidence
   ↓
Decide whether to stop
   ↓
Answer
The important property is:
The next investigation step is determined by the current evidence and information gaps rather than being a permanently fixed sequence.
🎥 Demo
The demonstration should show:
A complex question being submitted.
The RAG result.
The GraphRAG result.
The Agentic GraphRAG investigation.
Graph evidence.
Retrieved document evidence.
Agent tool decisions.
Final answer with supporting evidence.
Metrics comparison.
A question where the approaches behave differently.
🗺️ High-Level Architecture
                         ┌─────────────────┐
                         │      USER       │
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │   WEB CLIENT    │
                         └────────┬────────┘
                                  │
                                  ▼
                    ┌─────────────────────────┐
                    │   AGENT ORCHESTRATOR    │
                    │                         │
                    │  State + Planning +     │
                    │  Tool Selection +       │
                    │  Stopping Criteria      │
                    └───────────┬─────────────┘
                                │
              ┌─────────────────┼──────────────────┐
              │                 │                  │
              ▼                 ▼                  ▼
       ┌────────────┐    ┌────────────┐    ┌─────────────┐
       │   VECTOR   │    │    GRAPH   │    │  DOCUMENT   │
       │   SEARCH   │    │   SEARCH   │    │  RETRIEVAL  │
       
       └─────┬──────┘    └─────┬──────┘    └──────┬──────┘
             │                 │                  │
             ▼                 ▼                  ▼
       ┌──────────────────────────────────────────────┐
       │              EVIDENCE MANAGER                │
       └──────────────────────┬───────────────────────┘
                              │
                              ▼
                       ┌─────────────┐
                       │  REASONING  │
                       │    MODEL    │
                       └──────┬──────┘
                              │
                              ▼
                    ┌───────────────────┐
                    │ ANSWER + EVIDENCE │
                    └───────────────────┘
👥 Team
Sl.no   Name                             Role
 1.     Tanusk Sarkar                  Idea Representator
 2.     Prerona Mukherjee              Lead Backend developer
 3.     Ruposhree Paramanik            Lead Frontend developer
 4.     Rupayan Ghosh                  Researcher
📜 License
This project is released under the MIT License.
See LICENSE for details.
🙏 Acknowledgements
This project was developed for the TigerGraph Agentic GraphRAG Hackathon.
We acknowledge the TigerGraph ecosystem and the technologies that make graph-based retrieval, vector search, and agentic investigation possible.
⭐ Final Summary
                 ┌──────────────────────────┐
                 │      COMPLEX QUESTION    │
                 └────────────┬─────────────┘
                              │
                              ▼
                 ┌──────────────────────────┐
                 │    AGENTIC INVESTIGATION │
                 └────────────┬─────────────┘
                              │
             ┌────────────────┼────────────────┐
             ▼                ▼                ▼
        📄 Documents      🕸️ Graph          🔎 Vectors
             │                │                │
             └────────────────┼────────────────┘
                              ▼
                    📚 Evidence Manager
                              │
                              ▼
                       🧠 Reasoning
                              │
                              ▼
                    💬 Evidence-backed
                         ANSWER
                              │
                              ▼
                     📊 BENCHMARK
                              │
             ┌────────────────┼────────────────┐
             ▼                ▼                ▼
            RAG           GraphRAG       Agentic GraphRAG
Retrieve. Connect. Investigate. Verify. Answer.
