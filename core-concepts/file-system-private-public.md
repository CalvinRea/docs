# 🗂️ File System: Private vs. Public

CICADA implements a dual file system concept to cater to different access and privacy requirements for documents. This distinction is managed through separate Supabase tables, storage buckets, and Row Level Security (RLS) policies.

## Private File System

-   **Purpose**: Allows users to upload and manage documents that are personal or sensitive and should not be accessible by others.
-   **Storage**:
    -   Database Table: `public.documents_private`
    -   Supabase Storage Bucket: `private-files`
    -   Embeddings Table: `public.private_vectors`
-   **Access Control**:
    -   Strict RLS policies ensure that a user can only access, modify, or delete their own private documents and associated vectors.
    -   Operations are tied to the authenticated user's ID (`auth.uid()`).
-   **Organization**: Users can create and manage their own folder structures within their private space.
-   **Search**: When an authenticated user performs a search, the RAG system queries their `private_vectors` in addition to public vectors, ensuring their private documents are included in search results visible only to them.

## Public File System

-   **Purpose**: Hosts documents that are intended for public access and general knowledge sharing.
-   **Storage**:
    -   Database Table: `public.documents_public`
    -   Supabase Storage Bucket: `public-files` (configured as a public bucket)
    -   Embeddings Table: `public.public_vectors`
-   **Access Control**:
    -   **Read**: Anyone (authenticated or unauthenticated) can read public documents and their content.
    -   **Write/Modify/Delete**: Restricted to authenticated users, typically administrators or designated uploaders. RLS policies ensure only the uploader (`uploader_user_id`) can modify or delete their own public document contributions unless service role overrides are used.
-   **Organization**: Public documents are organized into predefined categories (e.g., "Legislation", "Case Law") which act as top-level folders.
-   **Search**: Public documents and their vectors are accessible in search results for all users.

## Key Differentiators

| Feature           | Private File System                                     | Public File System                                           |
| ----------------- | ------------------------------------------------------- | ------------------------------------------------------------ |
| **Visibility**    | Only to the owner/uploader.                             | To everyone (read); uploader/admin (write).                  |
| **DB Table**      | `documents_private`                                     | `documents_public`                                           |
| **Storage Bucket**| `private-files` (protected)                             | `public-files` (publicly accessible URLs)                    |
| **Vector Table**  | `private_vectors`                                       | `public_vectors`                                             |
| **RLS Focus**     | `user_id = auth.uid()`                                  | Read: `true`; Write: `uploader_user_id = auth.uid()`         |
| **Folder Mgmt**   | User-created nested folders.                            | Predefined categories.                                       |
| **URL Type**      | Signed URLs generated on-demand for access.             | Direct public URLs.                                          |

## User Interface

-   In the admin file management section (`src/components/admin/FileTable.tsx`), users can toggle between viewing and managing their "Private Files" or "Public Files".
-   The `UploadForm.tsx` allows users to specify whether a document should be uploaded to the private or public system.

## Implications for AI Search

-   The RAG system (`cleoChat` Edge Function) is designed to query both `public_vectors` and, if the user is authenticated, `private_vectors` (filtered by `user_id`).
-   This ensures that search results are comprehensive, including relevant public documents for everyone and private documents specifically for the logged-in user.
-   The `match_documents` PostgreSQL function has logic to handle filtering by `user_id` when querying private vectors.

This dual system provides flexibility, allowing CICADA to serve as both a personal knowledge base and a public archive.