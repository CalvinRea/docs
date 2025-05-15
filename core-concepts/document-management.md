# 📂 Document Management

CICADA provides a comprehensive system for managing constitutional archives, distinguishing between private and public documents, and organizing them into folders.

## Key Features

-   **Dual File Systems**:
    -   **Private**: Documents uploaded here are associated with the authenticated user and are only accessible by them. Stored in the `documents_private` table and `private-files` storage bucket.
    -   **Public**: Documents uploaded here are accessible to anyone (read-only for unauthenticated users). Uploads and modifications are restricted to authenticated users. Stored in the `documents_public` table and `public-files` storage bucket. Public documents use a predefined set of categories as folders.

-   **Folder Structure**:
    -   Private documents can be organized into user-created nested folders.
    -   Public documents are organized by predefined categories (e.g., 'Supreme Court Cases', 'Legislation').
    -   The `FolderTree` component (`src/components/admin/file/FolderTree.tsx`) allows navigation and management of these folders.

-   **CRUD Operations**:
    -   **Create**: Upload new documents via `UploadForm.tsx`. Create new folders via `FolderDialog.tsx`.
    -   **Read**: View and search documents. Document details and previews are available in `SourceModal.tsx`.
    -   **Update**: Edit document metadata (title, description, tags, etc.) using `FileEditDialog.tsx`. Edit folder names.
    -   **Delete**: Delete files and folders, with confirmation dialogs.

-   **Metadata**:
    -   Each document can have associated metadata: title, description, tags, source, date, folder/category.
    -   AI-generated insights: summary, key sections, citations.

-   **File Handling**:
    -   Uploads are handled by `UploadForm.tsx`, which interacts with `documentService.ts` (facade) and ultimately `privateDocumentService.ts` or `publicDocumentService.ts`.
    -   Files are stored in Supabase Storage.
    -   `parseUploadedDocuments` Edge Function processes uploaded files for text extraction and AI analysis.

## Admin Interface (`src/pages/AdminFiles.tsx`)

The `AdminFiles` page, utilizing the `FileTable.tsx` component, is the central hub for document management. It offers:
-   A toggle between private and public file systems.
-   A folder tree for navigation.
-   A table display of files in the current folder.
-   Search and sorting capabilities.
-   Actions for editing, deleting, and viewing files.
-   Responsive design for mobile and desktop.

## Workflow for a New Document

1.  User uploads a file via `UploadForm.tsx`.
2.  The file is uploaded to Supabase Storage (either `private-files` or `public-files` bucket).
3.  A record is created in the corresponding database table (`documents_private` or `documents_public`).
4.  The `uploadDocument` service may trigger (or an event listener on the storage/table could trigger) the `parseUploadedDocuments` Supabase Edge Function.
5.  The Edge Function:
    -   Downloads the file from storage.
    -   Extracts text content.
    -   Generates embeddings for RAG.
    -   Calls Gemini API for summary, key sections, and citations.
    -   Updates the document record in the database with these AI-generated insights.
    -   Stores embeddings in the `private_vectors` or `public_vectors` table.

## Relevant Files

-   **Services**:
    -   `src/services/api/documentService.ts`: Facade for document operations.
    -   `src/services/api/privateDocumentService.ts`: Handles private documents.
    -   `src/services/api/publicDocumentService.ts`: Handles public documents.
    -   `src/services/api/folderService.ts`: Manages folder structures.
-   **Components**:
    -   `src/components/admin/UploadForm.tsx`: For uploading new documents.
    -   `src/components/admin/FileTable.tsx`: Main component for browsing and managing files.
    -   `src/components/admin/file/*`: Various sub-components for the file table (rows, headers, pagination, etc.).
    -   `src/components/admin/FileEditDialog.tsx`: For editing document metadata.
    -   `src/components/admin/FolderDialog.tsx`: For creating/editing folders.
    -   `src/components/chat/SourceModal.tsx`: Displays document details and AI insights.
-   **Supabase**:
    -   `supabase/functions/parseUploadedDocuments/index.ts`: Edge Function for processing documents.
    -   `supabase/seed/Documents_table.sql`: Defines `documents_private` and `documents_public` tables.
    -   `supabase/seed/Vectors_table.sql`: Defines `private_vectors` and `public_vectors` tables.
```

**File: `core-concepts/ai-search-rag.md`**
```markdown
---
title: AI Search (RAG)
description: How CICADA implements AI-powered search using Retrieval Augmented Generation.
---

# 🤖 AI Search (Retrieval Augmented Generation - RAG)

CICADA employs an AI-powered search system based on Retrieval Augmented Generation (RAG) to provide users with accurate and contextually relevant answers from the constitutional archives. This is primarily handled by the `cleoChat` Supabase Edge Function (referred to as `test/index.ts` in the file structure, but its content suggests a RAG chat bot).

## Overview of RAG

RAG combines the strengths of large language models (LLMs) with information retrieval. Instead of solely relying on the LLM's pre-trained knowledge, RAG first retrieves relevant documents or text snippets from a knowledge base (in this case, the uploaded documents) and then uses these snippets as context for the LLM to generate an answer.

## Workflow

1.  **User Query**: A user submits a natural language question through the chat interface (`src/components/chat/ChatWindow.tsx`).
2.  **Query Embedding**:
    -   The `cleoChat` Edge Function receives the user's question.
    -   It uses a text embedding model (e.g., from Gemini) to convert the user's question into a numerical vector (embedding).
3.  **Vector Search (Retrieval)**:
    -   The query embedding is used to search for similar embeddings in the `private_vectors` and `public_vectors` tables in Supabase.
    -   The `match_documents` PostgreSQL function (`supabase/seed/Document_function.sql`) is crucial here. It performs a similarity search (e.g., cosine similarity or L2 distance) between the query embedding and the stored document chunk embeddings.
    -   This retrieves the most relevant document chunks (content snippets) related to the user's query. It searches both public vectors and, if the user is authenticated, their private vectors.
4.  **Context Augmentation**:
    -   The retrieved document chunks are compiled to form a context.
5.  **LLM Prompting (Generation)**:
    -   The `cleoChat` Edge Function constructs a prompt for an LLM (e.g., Gemini). This prompt includes:
        -   The original user question.
        -   The retrieved context (relevant document chunks).
        -   Instructions for the LLM to answer the question based *only* on the provided context and to cite sources.
6.  **Response Generation**:
    -   The LLM processes the prompt and generates an answer.
    -   The `cleoChat` function streams this response back to the client.
7.  **Display to User**:
    -   The `ChatWindow.tsx` component displays the streamed AI response.
    -   It also processes and displays the source documents (from the retrieved chunks) that the AI used, allowing users to verify information.

## Key Components

-   **Document Processing (`parseUploadedDocuments` Edge Function)**:
    -   When documents are uploaded, this function is responsible for:
        -   Extracting text content.
        -   Chunking the text into manageable pieces.
        -   Generating embeddings for each chunk using an embedding model.
        -   Storing these embeddings along with their content and document ID in `private_vectors` or `public_vectors`.

-   **Vector Database**:
    -   Supabase PostgreSQL with the `pgvector` extension acts as the vector database.
    -   `private_vectors` and `public_vectors` tables store the embeddings.
    -   HNSW indexes (`private_vectors_embedding_idx`, `public_vectors_embedding_idx`) are created for efficient similarity searches.

-   **`match_documents` Function (`supabase/seed/Document_function.sql`)**:
    -   A PostgreSQL function that takes a query embedding, match count, similarity threshold, and table name.
    -   It returns the top matching documents and their similarity scores.
    -   Handles filtering for user-specific data in `private_vectors`.

-   **`cleoChat` Edge Function (`supabase/functions/test/index.ts`)**:
    -   Orchestrates the RAG pipeline: embedding the query, retrieving documents, prompting the LLM, and streaming the response.
    -   Handles multi-turn conversations.
    -   Includes logic for function calling (tool use) by the LLM, potentially for more complex queries or actions.
    -   Manages fetching full document details based on IDs returned from vector search.

-   **Frontend Chat Interface (`src/components/chat/ChatWindow.tsx`)**:
    -   Sends user queries to the `cleoChat` function.
    -   Receives and renders the streamed AI response and source documents.

## Benefits of RAG in CICADA

-   **Accuracy**: Answers are grounded in the actual content of the archived documents, reducing hallucinations.
-   **Transparency**: Users can see the source documents, promoting trust and verifiability.
-   **Up-to-date Information**: The search is always based on the current set of documents in the archive.
-   **Contextual Understanding**: LLMs can understand nuanced queries and synthesize information from multiple sources.