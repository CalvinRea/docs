# Database Schema Overview

CICADA uses a PostgreSQL database managed by Supabase. The schema is designed to store documents, their associated metadata, AI-generated insights, vector embeddings for search, and user activity logs.

Key SQL scripts defining the schema are located in `supabase/seed/`:
-   `Documents_table.sql`: Defines tables for private and public documents, and RLS policies.
-   `Vectors_table.sql`: Defines tables for storing vector embeddings of document content.
-   `Logs_table.sql`: Defines a table for logging user actions.
-   `Document_function.sql`: Defines the `match_documents` PostgreSQL function for similarity search.

## Main Tables

-   **`public.documents_private`**: Stores metadata and AI insights for documents uploaded by users for their private use. Access is restricted to the owning user.
-   **`public.documents_public`**: Stores metadata and AI insights for documents intended for public access. Read access is generally open, while write access is restricted.
-   **`public.private_vectors`**: Stores text chunks and their corresponding vector embeddings derived from `documents_private`. Used for AI-powered search on private documents. Access is restricted to the owning user.
-   **`public.public_vectors`**: Stores text chunks and their corresponding vector embeddings derived from `documents_public`. Used for AI-powered search on public documents.
-   **`public.logs`**: A general-purpose table to log significant user events like uploads, searches, or deletions.

## Extensions Used

-   **`pgcrypto`**: Provides cryptographic functions, used here for `gen_random_uuid()` to generate UUIDs for primary keys.
-   **`vector` (pgvector)**: Enables storage and searching of vector embeddings, crucial for the RAG (Retrieval Augmented Generation) search functionality.

## Relationships

-   `documents_private.user_id` references `auth.users(id)`.
-   `documents_public.uploader_user_id` references `auth.users(id)`.
-   `private_vectors.user_id` references `auth.users(id)`.
-   `private_vectors.document_id` references `documents_private(id)`.
-   `public_vectors.document_id` references `documents_public(id)`.
-   `logs.user_id` references `auth.users(id)`.

All relationships involving user IDs use `on delete cascade`, meaning if a user is deleted from `auth.users`, their associated documents, vectors, and logs will also be deleted. Similarly, if a document is deleted, its associated vectors are cascaded.

## Row Level Security (RLS)

RLS is extensively used to control data access at the database row level. Policies are defined for each table to ensure users can only interact with data they are authorized to see or modify. This is a cornerstone of Supabase's security model. 

## Vector Indexes

HNSW (Hierarchical Navigable Small World) indexes are created on the `embedding` columns of `private_vectors` and `public_vectors` tables to enable fast and efficient similarity searches.

This schema forms the backbone of CICADA's data storage and retrieval capabilities, supporting both structured metadata and unstructured content for AI processing.
