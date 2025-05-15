```markdown
---
title: Features
description: Key features of the CICADA platform.
---

# ✨ CICADA Features

CICADA offers a comprehensive suite of features for both users and administrators:

-   🔐 **Secure Authentication**:
    -   OAuth integration with Google, Facebook, and GitHub via Supabase Auth.
    -   Role-based access control (future enhancement, currently all authenticated users are admins).

-   📂 **Smart Document Management**:
    -   Structured directory system (folders) for organizing archival content.
    -   Support for both private (user-specific) and public document repositories.
    -   File and folder creation, editing, and deletion.
    -   Context menus for quick actions on files and folders.

-   🤖 **AI-Powered Search (RAG)**:
    -   Natural language queries for searching through documents.
    -   Retrieval Augmented Generation (RAG) for contextually relevant answers.
    -   Display of source documents used to generate answers.

-   🎯 **Metadata Management**:
    -   Rich document tagging and metadata editing capabilities (title, description, date, source, tags).
    -   AI-generated summaries, key sections, and citations for documents.

-   📱 **Modern UI/UX**:
    -   Beautiful, responsive interface built with shadcn/ui and Tailwind CSS.
    -   Light and dark theme support.
    -   User-friendly chat interface for search.
    -   Optimized for both desktop and mobile devices.

-   📊 **Admin Dashboard**:
    -   Comprehensive analytics: document count, recent uploads, storage usage, popular searches.
    -   System status monitoring: API, search index, document processing, database backup.

-   🔍 **Insight Modal**:
    -   Detailed view of documents including previews (PDFs).
    -   AI-extracted insights: summary, key sections, citations.
    -   Direct download links for documents.

-   🌐 **Public Search Interface**:
    -   User-friendly search with query history.
    -   Ability to load and re-run past search queries.

-   📤 **Document Upload & Processing**:
    -   Drag-and-drop file uploads.
    -   Client-side validation and progress indicators.
    -   Automatic background processing of uploaded documents by Supabase Edge Functions to extract text and generate AI insights.

-   🍪 **Cookie Consent Management**:
    -   GDPR-compliant cookie consent banner.
```