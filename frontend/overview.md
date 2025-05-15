```markdown
---
title: Frontend Overview
description: An overview of the CICADA frontend architecture.
---

# Frontend Architecture Overview

The CICADA frontend is a single-page application (SPA) built with React, Vite, and TypeScript. It's responsible for user interaction, data presentation, and communication with the Supabase backend.

## Key Technologies

-   **React**: Core library for building the user interface.
-   **Vite**: Build tool providing fast development server and optimized production builds.
-   **TypeScript**: For static typing, improving code quality and maintainability.
-   **React Router DOM**: Handles client-side routing.
-   **Tailwind CSS & shadcn/ui**: For styling and pre-built UI components, ensuring a modern and responsive design.
-   **TanStack Query (React Query)**: Manages server state, caching, and data fetching logic.
-   **React Hook Form & Zod**: For form handling and validation.

## Directory Structure (`src/`)

The `src/` directory contains the core frontend code:

-   `App.tsx`: Root application component, sets up routing and global providers.
-   `main.tsx`: Entry point of the application, renders the root component.
-   `pages/`: Contains components representing different pages/views of the application.
-   `components/`: Contains reusable UI components, organized into subdirectories:
    -   `admin/`: Components specific to the admin interface.
    -   `auth/`: Authentication-related components.
    -   `chat/`: Components for the AI chat/search interface.
    -   `common/`: Shared components like headers, theme toggles.
    -   `ui/`: Base UI components from shadcn/ui.
    -   `cookie-consent.tsx`: Cookie consent banner.
-   `context/`: React Context API providers (e.g., `AuthContext`, `ThemeContext`).
-   `hooks/`: Custom React hooks for reusable logic.
-   `lib/`: Utility functions and Supabase client initialization (`supabase.ts`, `utils.ts`).
-   `services/`: Modules for interacting with the backend API (Supabase functions and database).
    -   `api/`: Contains typed service functions for different backend resources.
-   `types/`: TypeScript type definitions, especially for shared structures like file types.
-   `assets/`: Static assets (though not explicitly shown in the provided list, usually present).
-   `index.css`, `App.css`: Global styles and Tailwind CSS setup.

## State Management

-   **Local Component State**: `useState` and `useReducer` for managing state within individual components.
-   **Global State**: React Context API (`AuthContext`, `ThemeContext`) for global concerns like authentication status and theme.
-   **Server State**: TanStack Query (React Query) is used for fetching, caching, and updating data from the Supabase backend. This simplifies data synchronization and loading states.

## Routing

Client-side routing is managed by `react-router-dom`. Routes are defined in `App.tsx`, mapping URL paths to specific page components. `ProtectedRoute.tsx` is used to restrict access to certain routes based on authentication.

## Styling

Styling is primarily done using Tailwind CSS. shadcn/ui provides a set of beautifully designed, accessible, and customizable components built on top of Radix UI and Tailwind CSS. Global styles and Tailwind configurations are in `index.css` and `tailwind.config.ts`.

## Communication with Backend

-   The frontend communicates with Supabase directly for database operations (CRUD) and storage.
-   Supabase Edge Functions are invoked for more complex backend logic, AI processing, and RAG chat.
-   API calls are encapsulated within the `src/services/api/` modules, providing a clean interface for components to interact with the backend.
```