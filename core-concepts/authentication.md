# 🔐 Authentication

CICADA utilizes Supabase Auth for robust and secure user authentication. This allows users to sign in using various OAuth providers.

## Key Features

-   **OAuth Providers**: Supports login via:
    -   Google
    -   GitHub
    -   Facebook
    (Configured within the Supabase project dashboard)
-   **Session Management**: Supabase handles session tokens (JWTs) and refreshes them automatically. Sessions are persisted in `localStorage` by default.
-   **User Context**: The `AuthContext` (`src/context/AuthContext.tsx`) provides user information and authentication status throughout the application.
-   **Protected Routes**: The `ProtectedRoute` component (`src/components/auth/ProtectedRoute.tsx`) restricts access to certain parts of the application (e.g., admin areas) based on authentication status.
-   **Admin Access**: Currently, all authenticated users are considered administrators. This is a simplification and can be extended with role-based access control (RBAC) by checking custom claims in the JWT or a separate roles table.

## Workflow

1.  **Login Page (`src/pages/Login.tsx`)**: Users are presented with options to log in using configured OAuth providers.
2.  **OAuth Flow**:
    -   User clicks a provider button.
    -   The application calls `signInWithProvider` from `src/lib/supabase.ts`.
    -   Supabase redirects the user to the OAuth provider's authentication page.
    -   After successful authentication with the provider, the user is redirected back to the application's callback URL (`/auth/callback`).
3.  **Auth Callback (`src/components/auth/AuthCallback.tsx`)**:
    -   This component handles the OAuth callback.
    -   It exchanges the authorization code for a session with Supabase.
    -   If successful, the user is authenticated, and the session is established.
    -   The user is then typically redirected to the home page or their intended destination.
4.  **User State**:
    -   The `AuthProvider` listens to Supabase auth state changes.
    -   When a user logs in or out, the `user` object and `isAdmin` status in `AuthContext` are updated, causing relevant parts of the UI to re-render.
5.  **Logout**:
    -   Users can log out via the `UserProfile` component.
    -   This calls the `signOut` function from `src/lib/supabase.ts`, clearing the session.

## Relevant Files

-   `src/context/AuthContext.tsx`: Manages and provides authentication state.
-   `src/lib/supabase.ts`: Initializes Supabase client and provides auth helper functions (`signInWithProvider`, `signOut`).
-   `src/pages/Login.tsx`: Login page UI.
-   `src/components/auth/AuthButton.tsx`: Reusable button for OAuth providers.
-   `src/components/auth/AuthCallback.tsx`: Handles the OAuth redirect.
-   `src/components/auth/ProtectedRoute.tsx`: Protects routes based on auth status.
-   `src/components/auth/UserProfile.tsx`: Displays user info and logout option.
-   `supabase/seed/Documents_table.sql`: Defines RLS policies based on `auth.uid()`.
