```markdown
---
title: Edge Functions Overview
description: An overview of Supabase Edge Functions used in CICADA.
---

# Supabase Edge Functions Overview

CICADA leverages Supabase Edge Functions for server-side logic that needs to be executed in a secure and scalable environment. These are serverless functions written in TypeScript and run on Deno. They are deployed from the `supabase/functions/` directory of the project.

## Key Responsibilities of Edge Functions

-   **AI Processing**: Interacting with external AI services like Google Gemini for tasks such as text embedding, summarization, and RAG.
-   **Complex Business Logic**: Orchestrating operations that involve multiple steps or services (e.g., RAG pipeline).
-   **Data Aggregation**: Performing calculations or aggregations on database data for reporting (e.g., admin dashboard stats).
-   **Secure Operations**: Executing tasks that require privileged access (e.g., using a service role key to update records).

## Deployed Edge Functions

### 1. `getDashboardStats`
   - **Path**: `supabase/functions/getDashboardStats/index.ts`
   - **Purpose**: To compute and return statistics for the admin dashboard.
   - **Logic**:
     -   Connects to the Supabase database using a service role client.
     -   Queries tables like `documents_private`, `documents_public`, and `logs`.
     -   Calculates:
         -   Total document count.
         -   Number of recent uploads.
         -   Approximate storage used (from document metadata).
         -   Top 5 popular searches (from the `logs` table).
     -   Returns these statistics as a JSON response.
   - **Invoked by**: Frontend `AdminDashboard` page via `adminService.getAdminStats()`.

### 2. `parseUploadedDocuments`
   - **Path**: `supabase/functions/parseUploadedDocuments/index.ts`
   - **Purpose**: To process newly uploaded documents, extract content, generate AI insights, and create embeddings.
   - **Trigger**: Conceptually, this function should be triggered after a new document is successfully uploaded to storage and its initial record is created in the database. This could be via a direct call from the client after upload, or ideally via a database trigger or storage event.
   - **Logic**:
     -   Receives `documentId`, `filename`, and `isPublic` status as input.
     -   Retrieves the document record from the database.
     -   Downloads the document file (e.g., PDF) from Supabase Storage.
     -   Uses Google Gemini API:
         -   Uploads the file to Gemini.
         -   Prompts Gemini to extract text, generate a summary, identify key sections, and list citations.
         -   Chunks the extracted text.
         -   Generates vector embeddings for each chunk.
     -   Stores the generated embeddings and their content into the appropriate vectors table (`private_vectors` or `public_vectors`), linking them to the `document_id` and `user_id` (if private).
     -   Updates the original document record in `documents_private` or `documents_public` with the AI-generated summary, key sections, and citations.
   - **Key Operations**: File I/O, external API calls (Gemini), database writes.

### 3. `cleoChat` (Located in `supabase/functions/test/index.ts`)
   - **Path**: `supabase/functions/test/index.ts` (The content strongly suggests this is the main RAG chat function, potentially named `cleoChat` or similar in deployment).
   - **Purpose**: To handle user chat queries, perform Retrieval Augmented Generation (RAG), and stream responses.
   - **Logic**:
     -   Receives user query, chat history, and `user_id`.
     -   **Embedding**: Embeds the user's query using Gemini.
     -   **Retrieval (RAG)**:
         -   Calls the `match_documents` PostgreSQL function to search `public_vectors`.
         -   If `user_id` is provided, also calls `match_documents` to search `private_vectors` (filtered by `user_id`).
         -   Merges and ranks the retrieved document chunks.
     -   **Contextual Prompting**: Constructs a prompt for Gemini, including the user's query, chat history, and the retrieved document chunks as context.
     -   **Generation & Streaming**:
         -   Sends the prompt to Gemini.
         -   Streams the LLM's response (text and potential source citations) back to the client.
         -   May handle function calls/tool usage if defined in the Gemini prompt.
     -   **Source Resolution**: Fetches full metadata for cited source documents from `documents_*` tables to be displayed to the user.
   - **Invoked by**: Frontend `ChatWindow` component via `chatService.fetchChatResponse()`.

## Common Practices

-   **CORS Headers**: Edge Functions include CORS headers to allow requests from the frontend application's domain. Preflight `OPTIONS` requests are handled.
-   **Supabase Client**: Functions initialize a Supabase client, often using the `SUPABASE_SERVICE_ROLE_KEY` for privileged database access when necessary, or passing the user's JWT for operations that should be performed in the user's context.
-   **Error Handling**: Functions should include robust error handling and return appropriate HTTP status codes.

These Edge Functions form the core of CICADA's backend processing and AI integration capabilities.
```
