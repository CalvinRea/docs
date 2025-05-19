# Backend Overview (Supabase)

The backend for CICADA is built entirely on **Supabase**, a Backend-as-a-Service (BaaS) platform that provides a suite of tools built on top of PostgreSQL. Supabase offers database hosting, authentication, storage, and serverless edge functions.

## Key Supabase Features Used

-   **PostgreSQL Database**:
    -   Relational database for storing application data (documents, users, logs, vector embeddings).
    -   Schema defined via SQL (see `supabase/seed/`).
    -   Row Level Security (RLS) for fine-grained access control.
    -   `pgvector` extension for storing and searching vector embeddings.

-   **Authentication (Supabase Auth)**:
    -   Manages user sign-up, login, and session management.
    -   Supports OAuth providers (Google, GitHub, Facebook).
    -   Integrates with RLS policies through `auth.uid()` and `auth.role()`.

-   **Storage (Supabase Storage)**:
    -   Stores uploaded files (e.g., PDFs).
    -   Organized into buckets (`private-files` and `public-files`).
    -   Access controlled via RLS policies on the `storage.objects` table.

-   **Edge Functions**:
    -   Serverless functions written in TypeScript (using Deno runtime).
    -   Deployed from the `supabase/functions/` directory.
    -   Used for:
        -   AI-powered chat and RAG (`cleoChat` function, likely in `test/index.ts`).
        -   Processing uploaded documents (`parseUploadedDocuments/index.ts`).
        -   Fetching aggregated statistics (`getDashboardStats/index.ts`).
    -   Can be invoked directly from the client-side application.
    -   Can securely access other Supabase services (database, storage) using service roles or user context.

## Backend Architecture

The backend is not a traditional monolithic server but rather a collection of services provided by Supabase:

1.  **Database Layer**:
    -   Tables: `documents_private`, `documents_public`, `private_vectors`, `public_vectors`, `logs`.
    -   PostgreSQL Functions: `match_documents` for vector similarity search.
    -   RLS policies enforce data access rules at the database level.

2.  **Authentication Layer**:
    -   Managed by Supabase Auth. Client interacts via Supabase JS library.

3.  **File Storage Layer**:
    -   Managed by Supabase Storage. Client uploads/downloads files via Supabase JS library, respecting storage RLS policies.

4.  **Serverless Compute Layer (Edge Functions)**:
    -   **`getDashboardStats`**: Aggregates data from database tables to provide admin dashboard statistics.
    -   **`parseUploadedDocuments`**:
        -   Triggered (conceptually, or called) after a document is uploaded.
        -   Downloads the document from storage.
        -   Uses Gemini API to:
            -   Extract text.
            -   Chunk text.
            -   Generate embeddings for chunks.
            -   Generate summary, key sections, citations.
        -   Stores embeddings in the appropriate `*_vectors` table.
        -   Updates the document record in `documents_*` table with AI insights.
    -   **`cleoChat` (in `test/index.ts`)**:
        -   Handles user chat queries for RAG.
        -   Embeds the user query.
        -   Performs vector search using `match_documents` against `*_vectors` tables.
        -   Constructs a prompt with retrieved context and sends it to Gemini.
        -   Streams the LLM's response back to the client.
        -   Handles fetching full source document details.

## Security

-   **RLS**: Core to Supabase security, ensuring users can only access data they are permitted to.
-   **Service Role Keys**: Used by Edge Functions for privileged operations (e.g., updating any document after processing), but user-context is preferred where possible.
-   **Environment Variables**: Sensitive keys (like `GEMINI_API_KEY`) are stored as environment variables for Edge Functions within the Supabase project settings, not exposed to the client.

The Supabase backend provides a scalable and managed infrastructure, allowing developers to focus on application logic rather than server maintenance.
