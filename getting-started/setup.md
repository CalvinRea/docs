# 🛠️ Setup Instructions

Follow these steps to get the CICADA project running on your local machine:

1.  **Clone the Repository**:
    Open your terminal and run:
    ```bash
    git clone https://github.com/yourusername/CICADA-main.git
    cd CICADA-main
    ```
    (Replace `yourusername` with the actual repository owner if different.)

2.  **Install Dependencies**:
    Using npm, install the project dependencies:
    ```bash
    npm install
    ```

3.  **Environment Variable Configuration**:
    -   Copy the example environment file:
        ```bash
        cp .env.example .env
        ```
    -   Open the newly created `.env` file and update it with your Supabase project credentials:
        ```env
        VITE_SUPABASE_URL=your_supabase_project_url
        VITE_SUPABASE_ANON_KEY=your_supabase_anon_key
        ```
        You can find these in your Supabase project settings (Project Settings > API).
    -   The `.env` file also lists placeholders for server-side OAuth credentials (`GOOGLE_CLIENT_ID`, `GITHUB_CLIENT_ID`, etc.) and `GEMINI_API_KEY`. These are typically configured directly in your Supabase project settings (Authentication > Providers for OAuth, and Environment Variables for Edge Functions for Gemini API Key). **Do not prefix these secret keys with `VITE_` as that would expose them to the client.**

4.  **Supabase Project Configuration**:

    -   **Database Setup**:
        -   Navigate to the SQL Editor in your Supabase dashboard (Database > SQL Editor).
        -   Execute the SQL scripts from the `supabase/seed/` directory in the following order:
            1.  `Documents_table.sql` (Creates document tables and RLS)
            2.  `Vectors_table.sql` (Creates vector tables for embeddings and RLS)
            3.  `Logs_table.sql` (Creates logs table)
            4.  `Document_function.sql` (Creates the `match_documents` PostgreSQL function)
        -   This will set up the necessary tables, RLS policies, and database functions.

    -   **Storage Buckets**:
        -   In your Supabase dashboard, navigate to Storage.
        -   Create two buckets:
            1.  `private-files` (Keep "Public bucket" unchecked)
            2.  `public-files` (Check "Public bucket")
        -   The RLS policies for storage access are defined in `Documents_table.sql` and will be applied automatically if they don't already exist for these bucket names.

    -   **Edge Functions**:
        -   Deploy the Edge Functions located in the `supabase/functions/` directory to your Supabase project. You can do this using the Supabase CLI:
            ```bash
            # Login to Supabase (if you haven't already)
            npx supabase login

            # Link your local project to your Supabase project
            npx supabase link --project-ref YOUR_PROJECT_REF

            # Deploy all functions
            npx supabase functions deploy --project-ref YOUR_PROJECT_REF
            ```
            Replace `YOUR_PROJECT_REF` with your actual Supabase project reference.
        -   The key functions are:
            -   `getDashboardStats`: Fetches statistics for the admin dashboard.
            -   `parseUploadedDocuments`: Processes uploaded documents, extracts text, generates embeddings, and AI insights using Gemini.
            -   `test` (renamed or should be `cleoChat` based on content): Handles RAG chat functionality with Gemini. Ensure this function is named appropriately in your deployment (`cleoChat` seems to be the intended name for the chat function).
        -   **Important**: Set the `GEMINI_API_KEY` environment variable for these functions in your Supabase project settings (Project Settings > Edge Functions > Your Function > Environment Variables).

    -   **Authentication Providers (OAuth)**:
        -   In your Supabase dashboard, go to Authentication > Providers.
        -   Enable and configure the OAuth providers you wish to use (e.g., Google, GitHub, Facebook). You'll need to provide the Client ID and Client Secret obtained from each provider. The redirect URI will be `https://<your-supabase-project-ref>.supabase.co/auth/v1/callback`.

After completing these steps, your local development environment should be ready.
