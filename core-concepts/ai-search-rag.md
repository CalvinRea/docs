# 🤖 AI Search (Retrieval Augmented Generation - RAG)

CICADA employs an AI-powered search system based on Retrieval Augmented Generation (RAG) to provide users with accurate and contextually relevant answers from the constitutional archives. This is primarily handled by the `cleoChat` Supabase Edge Function (located in `supabase/functions/test/index.ts` in the provided codebase, its content and purpose align with a RAG chat bot).

## Overview of RAG

Retrieval Augmented Generation (RAG) is an AI framework that enhances the responses of Large Language Models (LLMs) by grounding them in specific, up-to-date information retrieved from an external knowledge base. Instead of solely relying on the LLM's pre-trained (and potentially outdated or too general) knowledge, a RAG system first:

1.  **Retrieves**: Fetches relevant documents or text snippets from a designated knowledge source (in CICADA's case, the uploaded constitutional documents and their processed embeddings).
2.  **Augments**: Adds this retrieved information as context to the user's original query.
3.  **Generates**: Passes the augmented query (original query + context) to an LLM to produce an answer.

This approach significantly improves the factual accuracy, relevance, and specificity of LLM-generated responses, especially for domain-specific queries.

## Workflow in CICADA

![image](/images/Process%20View.png)

1.  **User Query**: A user submits a natural language question through the chat interface (`src/components/chat/ChatWindow.tsx`).
2.  **Query Embedding**:
    -   The `cleoChat` Edge Function receives the user's question.
    -   It utilizes a text embedding model (e.g., from Google Gemini, such as `text-embedding-004`) to convert the user's question into a numerical vector representation (an embedding). This embedding captures the semantic meaning of the query.
3.  **Vector Search (Retrieval)**:
    -   The generated query embedding is used to search for semantically similar embeddings stored in the `private_vectors` and `public_vectors` tables within the Supabase PostgreSQL database.
    -   This search is performed by the `match_documents` PostgreSQL function (`supabase/seed/Document_function.sql`). This function calculates similarity scores (e.g., based on cosine similarity or L2 distance) between the query embedding and the pre-computed embeddings of document chunks.
    -   The function returns the most relevant document chunks (snippets of text from the original documents) that match the query's intent. It searches both public vectors and, if the user is authenticated and has private documents, their private vectors as well.
4.  **Context Augmentation**:
    -   The text content from these retrieved document chunks is compiled to form a rich, relevant context.
5.  **LLM Prompting (Generation)**:
    -   The `cleoChat` Edge Function constructs a carefully designed prompt for a powerful LLM (e.g., Google Gemini's `gemini-1.5-flash-latest` model). This prompt typically includes:
        -   The original user question.
        -   The chat history (if it's a follow-up question, for conversational context).
        -   The retrieved document chunks (the augmented context).
        -   Specific instructions for the LLM, such as to base its answer *only* on the provided context, to answer concisely, and to cite the source documents it used.
6.  **Response Generation & Streaming**:
    -   The LLM processes this augmented prompt and generates an answer.
    -   The `cleoChat` function is designed to stream this response back to the client application (`ChatWindow.tsx`). This means the answer appears word by word or sentence by sentence, improving perceived performance.
    -   The streamed response may also include structured information about the source documents that were heavily relied upon for the answer.
7.  **Display to User**:
    -   The `ChatWindow.tsx` component receives and renders the streamed AI response.
    -   It also processes and displays information about the source documents (e.g., titles, snippets, links to view the full document via `SourceCard.tsx` and `SourceModal.tsx`), allowing users to verify the information and explore the original texts.

## Key Components Enabling RAG

-   **Document Processing Pipeline (`parseUploadedDocuments` Edge Function)**:
    -   **Text Extraction**: When new documents are uploaded, this function extracts their textual content.
    -   **Chunking**: The extracted text is divided into smaller, semantically meaningful chunks. This is important because embeddings are typically generated for these smaller pieces rather than entire large documents.
    -   **Embedding Generation**: Each chunk is converted into a vector embedding using a text embedding model.
    -   **Storage**: These embeddings, along with their corresponding text content and a reference to the parent document ID, are stored in the `private_vectors` or `public_vectors` tables in Supabase.

-   **Vector Database (Supabase with `pgvector`)**:
    -   Supabase PostgreSQL, enhanced with the `pgvector` extension, serves as the vector database.
    -   The `private_vectors` and `public_vectors` tables store the embeddings.
    -   Specialized indexes (HNSW - Hierarchical Navigable Small World) are created on the embedding columns to enable extremely fast and efficient similarity searches over millions of vectors.

-   **`match_documents` PostgreSQL Function**:
    -   This custom SQL function is the engine for the retrieval step. It takes a query embedding and search parameters (like the number of results to return and a similarity threshold).
    -   It efficiently queries the vector tables, calculates similarity, and returns the top-matching document chunks along with their original document metadata.
    -   It handles filtering for user-specific data when searching `private_vectors`.

-   **`cleoChat` Edge Function (RAG Orchestrator)**:
    -   This function orchestrates the entire RAG pipeline as described in the workflow above. It integrates query embedding, vector search (via `match_documents`), prompt engineering for the LLM, and response streaming.
    -   It may also handle conversational memory (chat history) to provide context for follow-up questions.

-   **Frontend Chat Interface (`src/components/chat/ChatWindow.tsx`)**:
    -   Provides the user interface for submitting queries.
    -   Communicates with the `cleoChat` Edge Function (via `chatService.ts`).
    -   Renders the AI's streamed responses and the identified source documents in a user-friendly manner.

## Benefits of RAG in CICADA

-   **Enhanced Accuracy and Relevance**: Answers are grounded in the actual content of the uploaded constitutional documents, significantly reducing the risk of the LLM generating incorrect or "hallucinated" information.
-   **Access to Specific and Current Information**: The system can answer questions based on the very latest documents added to the archive, unlike LLMs that rely solely on their general training data which has a knowledge cut-off.
-   **Transparency and Verifiability**: By citing and allowing users to inspect the source documents, CICADA builds trust and allows users to verify the AI's claims and delve deeper into the original texts.
-   **Improved Contextual Understanding**: LLMs, when augmented with relevant retrieved context, can better understand nuanced queries and synthesize information from multiple parts of the archive.
-   **Reduced Need for Fine-Tuning**: RAG provides a way to make LLMs knowledgeable about specific domains without the expensive and complex process of fine-tuning the entire model on that domain's data.

By implementing this RAG architecture, CICADA aims to provide a powerful, reliable, and transparent AI search experience for users exploring constitutional archives.
