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

![image](/images/Scenarios%20View.png)

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