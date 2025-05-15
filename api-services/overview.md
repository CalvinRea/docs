# API Services Overview

The `src/services/api/` directory contains modules that encapsulate the logic for interacting with the CICADA backend, which is primarily Supabase (database and Edge Functions). These services provide a clean, typed, and reusable interface for frontend components to fetch data, submit changes, and invoke backend operations.

## Purpose

-   **Abstraction**: Decouple frontend components from the direct implementation details of backend calls.
-   **Reusability**: Provide common functions for operations like fetching documents, uploading files, or getting chat responses.
-   **Type Safety**: Utilize TypeScript types (defined in `src/services/api/types.ts`) for request parameters and response data, improving code reliability.
-   **Centralization**: Group related API interactions into dedicated service files (e.g., `documentService.ts`, `chatService.ts`).

## Structure

-   **`index.ts`**: Re-exports all types and functions from individual service files for easy importing.
-   **`types.ts`**: Defines shared TypeScript interfaces and types used across different API services (e.g., `Document`, `ChatResponse`, `FolderStructure`).
-   **Individual Service Files**:
    -   `adminService.ts`: For admin-specific operations like fetching dashboard stats.
    -   `chatService.ts`: For interacting with the AI chat backend (e.g., `cleoChat` Edge Function).
    -   `documentService.ts`: A facade for document-related operations, delegating to `privateDocumentService.ts` or `publicDocumentService.ts` based on context or type.
    -   `folderService.ts`: For managing folder structures.
    -   `privateDocumentService.ts`: Handles CRUD operations and URL generation specifically for private documents.
    -   `publicDocumentService.ts`: Handles CRUD operations specifically for public documents.

## Interaction with Supabase

These services primarily use the Supabase JavaScript client (`supabase-js`) from `src/lib/supabase.ts` to:

-   **Query Database Tables**: Perform `select`, `insert`, `update`, `delete` operations on Supabase PostgreSQL tables (e.g., `documents_private`, `documents_public`).
-   **Invoke Edge Functions**: Call Supabase Edge Functions (e.g., `getAdminStats`, `cleoChat`, `parseUploadedDocuments` indirectly).
-   **Manage Storage**: Upload files to and retrieve files/URLs from Supabase Storage.

## Example: `documentService.ts`

This service often acts as a high-level interface. For instance, `fetchDocumentById` might try to fetch from public documents first, then private if the user is authenticated and the document isn't found publicly, or based on an explicit `fileSystemType` parameter.

```typescript
// Conceptual snippet from documentService.ts
import * as privateDocService from './privateDocumentService';
import *salt publicDocService from './publicDocumentService';
import { Document, FileSystemType } from './types';

export const fetchDocuments = async (fileSystemType: FileSystemType = 'private'): Promise<Document[]> => {
  if (fileSystemType === 'public') {
    return publicDocService.fetchPublicDocuments();
  }
  // Assumes private if not public, requires auth check within privateDocService
  return privateDocService.fetchPrivateDocuments();
};
```

## Error Handling

Service functions typically include error handling for API calls, throwing errors or returning structured error responses that can be caught and managed by the calling components (often with the help of TanStack Query).

These API services are crucial for maintaining a well-organized and maintainable frontend codebase by separating data interaction logic from UI presentation.

