# Cloud RAG Architecture Comparison

## 1. Provider Summary

| Feature | **Azure (Microsoft)** | **AWS (Amazon)** | **Google Cloud (GCP)** |
| :--- | :--- | :--- | :--- |
| **Architecture Name** | *Azure OpenAI "On Your Data"* | *Knowledge Bases for Amazon Bedrock* | *Vertex AI Search & Conversation* |
| **Storage** | Azure Blob Storage | Amazon S3 | Google Cloud Storage (GCS) |
| **Ingestion Engine** | Azure AI Search Indexers | Knowledge Bases (Managed) | Vertex AI Agent Builder |
| **Embedding Model** | Azure OpenAI (`text-embedding-3`) | Titan V2, Cohere, etc. | Vertex AI Embeddings (`text-embedding-gecko`) |
| **Vector Database** | **Azure AI Search** (Tiered pricing) | **OpenSearch Serverless** (Hourly pricing) | **Vertex AI Vector Search** (Per-node/query) |
| **LLM** | GPT-4o, GPT-4o-mini | Claude 3.5 Sonnet, Llama 3 | Gemini 1.5 Pro, Gemini 1.5 Flash |
| **Key Advantage** | Best for Enterprise/Office 365 integration. | Most flexible model choice (Claude, Llama, Titan). | Best "out of the box" retrieval; unique "Grounding" feature. |
| **Primary Cost Model** | **Fixed Monthly Tiers** (Service Tiers) | **Provisioned Capacity** (OCUs/Hour) | **Pay-Per-Query** (Great for startups) |

---

## 2. Architecture Diagrams

### Azure RAG Architecture
*Standard enterprise flow using Azure AI Search as the vector backbone.*

```mermaid
graph LR
    subgraph Setup ["Part 1: Ingestion (One-time)"]
        A[PDF Document] -->|1. Upload| B(Blob Storage)
        B -->|2. Read & Chunk| C{Indexer}
        C -->|3. Generate Vector| D[Azure OpenAI<br>Embedding Model]
        D -->|4. Save| E[(Azure AI Search<br>Vector DB)]
    end

    subgraph Usage ["Part 2: Retrieval (Real-time)"]
        F(User Question) -->|5. Convert to Vector| G[Azure OpenAI<br>Embedding Model]
        G -->|6. Search| E
        E -->|7. Return Context| H[App / Code]
        H -->|8. Build Prompt| I[GPT-4 Model]
        I -->|9. Answer| J(Final Response)
    end

    style E fill:#0072C6,stroke:#fff,color:#fff
    style I fill:#0072C6,stroke:#fff,color:#fff
```

### Part 1: Setup (Indexing)

1. **Upload:** You upload your files (PDFs, Word docs) to an **Azure Blob Storage** container.
2. **Read & Chunk:** An **Azure AI Search Indexer** automatically detects the new file, opens it, and breaks the text into smaller, manageable pieces ("chunks").
3. **Generate Vector:** The indexer sends these text chunks to the **Azure OpenAI Embedding Model**, which translates text into numbers (vectors).
4. **Save:** These vectors are saved into the **Azure AI Search** index, ready to be searched.

### Part 2: Usage (The User Asks a Question)

5. **Convert:** When a user asks a question, your app sends the text to the same **Embedding Model** to turn the question into numbers.
6. **Search:** The app sends those numbers to **Azure AI Search** to find the most similar document chunks.
7. **Return Context:** Azure AI Search returns the specific paragraphs that contain the answer.
8. **Build Prompt:** Your app combines the **User's Question** + **The Retrieved Paragraphs** into a single message for the AI.
9. **Answer:** **GPT-4** reads the prompt and generates the final answer based *only* on the provided paragraphs.

## AWS Bedrock Architecture
*Serverless flow using "Knowledge Bases" to automate ingestion.*

```mermaid
graph LR
    subgraph Setup ["Part 1: Knowledge Base"]
        A[PDF Document] -->|1. Upload| B(Amazon S3)
        B -->|2. Sync API| C[Bedrock<br>Knowledge Base]
        C -->|3. Auto-Embed| D[Titan / Cohere<br>Model]
        D -->|4. Store| E[(OpenSearch<br>Serverless)]
    end

    subgraph Usage ["Part 2: RetrieveAndGenerate"]
        F(User Question) -->|5. Call API| G[Bedrock Agent<br>Runtime]
        G -->|6. Semantic Search| E
        E -->|7. Fetch Chunks| G
        G -->|8. Augment| H[Claude 3.5 / Llama<br>LLM]
        H -->|9. Generate| I(Final Response)
    end

    style E fill:#FF9900,stroke:#232F3E,color:#000
    style C fill:#FF9900,stroke:#232F3E,color:#000
```

### Part 1: Setup (Knowledge Base)

1. **Upload:** You upload documents to an **Amazon S3** bucket.
2. **Sync:** You click the "Sync" button in **Bedrock Knowledge Bases**.
3. **Auto-Embed:** AWS automatically splits the files and sends them to the **Titan/Cohere embedding model**.
4. **Store:** The resulting vectors are securely stored in **Amazon OpenSearch Serverless**.

### Part 2: Usage (RetrieveAndGenerate)

5. **Call API:** The user asks a question. Your app makes a single API call: `RetrieveAndGenerate`.
6. **Semantic Search:** Bedrock automatically searches the **OpenSearch** index for relevant info.
7. **Fetch Chunks:** The relevant text chunks are retrieved internally by Bedrock.
8. **Augment:** Bedrock automatically pastes these chunks into a prompt for the **Claude** or **Llama** model.
9. **Generate:** The model returns the final answer to your application.
10. 
## Google Cloud (Vertex AI) Architecture
*Simplified flow emphasizing "Grounding" and managed retrieval.*

```mermaid
graph LR
    subgraph Setup ["Part 1: Data Store"]
        A[PDF Document] -->|1. Upload| B(Cloud Storage GCS)
        B -->|2. Import| C[Vertex AI<br>Agent Builder]
        C -->|3. Index| D[(Vertex AI<br>Search Index)]
    end

    subgraph Usage ["Part 2: Grounding"]
        F(User Question) -->|4. Ask with Grounding| G[Gemini Pro<br>Model]
        G -->|5. Verify Facts| D
        D -->|6. Retrieve Snippets| G
        G -->|7. Cite Sources| G
        G -->|8. Answer| H(Response w/ Citations)
    end

    style C fill:#4285F4,stroke:#fff,color:#fff
    style G fill:#4285F4,stroke:#fff,color:#fff
    style F fill:#4285F4,stroke:#fff,color:#fff

```

### Part 1: Setup (Data Store)

1. **Upload:** You upload your documents to a **Google Cloud Storage** bucket.
2. **Import:** You create a "Data Store" in **Vertex AI Agent Builder** and point it to your bucket.
3. **Index:** Google automatically parses, chunks, and indexes the content using its internal **Google Search** technology.

### Part 2: Usage (Grounding)

4. **Ask with Grounding:** The user asks a question. Your app sends it to the **Gemini** model with a "Grounding" tool attached.
5. **Verify Facts:** Before answering, the **Gemini** model pauses and checks your **Vertex AI Search Index** for facts.
6. **Retrieve Snippets:** It pulls specific sentences from your PDFs that prove the answer.
7. **Cite Sources:** Gemini checks if the facts match the snippets.
8. **Answer:** Gemini generates the response and includes clickable **citations** (footnotes) showing exactly where the info came from.


## 3. Cloud RAG Pricing Comparison

*Last Updated: January 2026*

| Feature | **Azure (On Your Data)** | **AWS (Knowledge Bases)** | **Google (Vertex AI Agent)** |
| :--- | :--- | :--- | :--- |
| **Main Cost Driver** | **Search Service Tiers**<br>*(Fixed monthly cost)* | **Compute Units (OCUs)**<br>*(Hourly rate for vector DB)* | **Per-Query + Storage**<br>*(Pay per use)* |
| **Vector Storage** | **Azure AI Search**<br>• Basic Tier: **$73.91/mo**<br>• Standard S1: **$247.17/mo**<br>• Standard S2: **$988.67/mo**<br>• Storage Optimized L1: **$2,469.33/mo** | **OpenSearch Serverless**<br>• **$0.24** per OCU/hour<br>• Min. 2 OCUs (Indexing + Search)<br>• Base cost: **~$350/mo** for 24/7<br>• Additional OCU scaling as needed | **Vertex AI Search**<br>• Storage: **$0.05/GB/month**<br>• Index creation: **$0.65/GB** (one-time)<br>• Free tier: First 1 million documents |
| **Retrieval / Search** | Included in Service Tier<br>*(Unlimited queries)* | Included in OCU hourly cost<br>*(Query performance scales with OCUs)* | **Enterprise Edition:**<br>• $4.00 per 1,000 queries<br>• Includes grounding & citations<br>**Standard Edition:**<br>• $1.50 per 1,000 queries |
| **Embedding Models** | **Azure OpenAI Embeddings**<br>• text-embedding-3-small: **$0.02/1M tokens**<br>• text-embedding-3-large: **$0.13/1M tokens**<br>• text-embedding-ada-002: **$0.10/1M tokens** | **Amazon Bedrock Embeddings**<br>• Titan Embeddings G1: **$0.10/1M tokens**<br>• Titan Embeddings V2: **$0.02/1M tokens**<br>• Cohere Embed: **$0.10/1M tokens** | **Vertex AI Embeddings**<br>• text-embedding-004: **$0.025/1M tokens**<br>• text-multilingual-embedding-002: **$0.025/1M tokens**<br>• textembedding-gecko: **$0.025/1M tokens** |
| **LLM Inference** | **Azure OpenAI** (Pay per Token)<br>• **GPT-4o**: $2.50 (input) / $10.00 (output) per 1M tokens<br>• **GPT-4o-mini**: $0.15 (input) / $0.60 (output) per 1M tokens<br>• **GPT-4 Turbo**: $10.00 (input) / $30.00 (output) per 1M tokens<br>• **o1-preview**: $15.00 (input) / $60.00 (output) per 1M tokens | **Amazon Bedrock** (Pay per Token)<br>• **Claude 3.5 Sonnet v2**: $3.00 (input) / $15.00 (output) per 1M tokens<br>• **Claude 3.5 Haiku**: $0.80 (input) / $4.00 (output) per 1M tokens<br>• **Llama 3.3 70B**: $0.99 (input) / $0.99 (output) per 1M tokens<br>• **Titan Text Premier**: $0.50 (input) / $1.50 (output) per 1M tokens | **Vertex AI Models** (Pay per Token)<br>• **Gemini 2.0 Flash**: $0.075 (input) / $0.30 (output) per 1M tokens<br>• **Gemini 1.5 Pro**: $1.25 (input) / $5.00 (output) per 1M tokens<br>• **Gemini 1.5 Flash**: $0.075 (input) / $0.30 (output) per 1M tokens<br>• **Gemini 1.0 Pro**: $0.50 (input) / $1.50 (output) per 1M tokens |
| **Data Processing** | **AI Search Indexing**<br>• Included in service tier<br>• OCR: Additional charge via AI Services<br>• Skillsets: Custom enrichment | **Knowledge Base Sync**<br>• Chunking: Included<br>• Embedding generation costs apply<br>• S3 storage: $0.023/GB/month | **Document AI Processing**<br>• Parsing: $0.01 per page<br>• OCR: $1.50 per 1,000 pages<br>• Classification: $0.02 per page<br>• Chunking: Included |
| **Additional Costs** | • Azure Blob Storage: **$0.018/GB/month**<br>• Bandwidth: $0.05-$0.087/GB egress<br>• Private endpoints: $0.01/hour | • S3 Storage: **$0.023/GB/month**<br>• Data transfer: $0.09/GB out to internet<br>• VPC endpoints: $0.01/hour | • Cloud Storage: **$0.020/GB/month**<br>• Network egress: $0.12/GB<br>• Logging: Additional if enabled |
| **Free Tier / Credits** | • $200 Azure credit (new accounts)<br>• Free tier: Limited AI services | • $300 AWS credits (new accounts)<br>• Free tier: 12 months limited | • $300 GCP credits (new accounts)<br>• Free tier: Always free limits |
| **Best For** | **Predictable monthly costs**<br>Enterprise with fixed budgets<br>High query volume | **Flexible scaling**<br>Variable workloads<br>Multi-model experimentation | **Pay-as-you-grow**<br>Startups & prototypes<br>Variable traffic |

### Cost Example: 10,000 Documents, 100K Queries/Month

| Provider | **Estimated Monthly Cost** | **Breakdown** |
| :--- | :--- | :--- |
| **Azure** | **$320 - $400** | • Search: $247 (Standard S1)<br>• LLM: $30-50 (GPT-4o-mini)<br>• Storage: $20<br>• Embeddings: $10 |
| **AWS** | **$380 - $450** | • OpenSearch: $350 (2 OCUs 24/7)<br>• LLM: $20-30 (Llama 3.3)<br>• Storage: $5<br>• Embeddings: $5 |
| **Google** | **$480 - $550** | • Queries: $400 (100K @ $4/1K)<br>• LLM: $40-60 (Gemini 1.5 Flash)<br>• Storage: $20<br>• Embeddings: $5 |

*Note: Actual costs vary based on document size, query complexity, response length, and region. Prices shown are for US regions.*
