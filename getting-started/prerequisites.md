```markdown
---
title: Prerequisites
description: Software and accounts needed to run CICADA locally.
---

# Prerequisites

Before you can set up and run CICADA locally, ensure you have the following installed and configured:

-   **Node.js**:
    -   Version 16.x or later. You can download it from [nodejs.org](https://nodejs.org/).
    -   npm (Node Package Manager) is included with Node.js.

-   **Git**:
    -   Required for cloning the repository. Download from [git-scm.com](https://git-scm.com/).

-   **A Supabase Account**:
    -   You can create a free account at [supabase.com](https://supabase.com/).
    -   You will need a Supabase project to connect the application to. Both local Supabase CLI setup or a cloud-hosted project can be used.

-   **OAuth Provider Credentials (Optional, for full auth functionality)**:
    -   If you want to test OAuth login with providers like Google, Facebook, or GitHub, you'll need to create applications with these providers and obtain client IDs and secrets.
    -   These are configured in your Supabase project settings and referenced in the `.env` file (for secrets not exposed to the client).

-   **Google Gemini API Key (for AI features)**:
    -   Required for document processing and AI search capabilities.
    -   This key should be set as an environment variable in your Supabase project for the Edge Functions.
    -   Obtain it from [Google AI Studio](https://aistudio.google.com/app/apikey).
```