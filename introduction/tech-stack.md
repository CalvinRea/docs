# 🏗️ Tech Stack

CICADA is built using a modern, robust, and scalable technology stack:

-   **Frontend**:
    -   **Framework**: React (v18) with Vite
    -   **Language**: TypeScript
    -   **UI Components**: shadcn/ui
    -   **Styling**: Tailwind CSS
    -   **Routing**: React Router DOM (v6)
    -   **State Management**: React Context API, `useState`, `useEffect`, custom hooks
    -   **Forms**: React Hook Form with Zod for validation
    -   **HTTP Client/Data Fetching**: `fetch` API, TanStack Query (React Query) for server state management

-   **Backend (PaaS)**:
    -   **Platform**: Supabase
    -   **Authentication**: Supabase Auth (OAuth with Google, GitHub, Facebook)
    -   **Database**: PostgreSQL (via Supabase)
    -   **Storage**: Supabase Storage (for private and public files)
    -   **Edge Functions**: Deno (TypeScript) on Supabase for serverless functions (e.g., AI processing, RAG, stats)
    -   **AI Integration**: Google Gemini (via Supabase Edge Functions) for RAG, summarization, and embedding generation.

-   **Development & Tooling**:
    -   **Package Manager**: npm
    -   **Build Tool**: Vite
    -   **Linting**: ESLint with TypeScript ESLint
    -   **Testing**: Vitest (unit tests)
    -   **Version Control**: Git & GitHub

-   **Deployment**:
    -   **Frontend Hosting**: Azure Static Web Apps
    -   **CI/CD**: GitHub Actions

-   **Key Libraries & Utilities**:
    -   `lucide-react` for icons
    -   `clsx` & `tailwind-merge` for utility class composition
    -   `date-fns` for date manipulation
    -   `react-markdown` for rendering markdown content
    -   `sonner` for toast notifications
