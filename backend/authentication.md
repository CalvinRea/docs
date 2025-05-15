# Backend Authentication (Supabase Auth)

CICADA relies on **Supabase Auth** for all its user authentication and management needs. Supabase Auth is a powerful, built-in feature of the Supabase platform that provides secure and easy-to-implement authentication.

## Key Features Used

-   **OAuth Providers**:
    -   CICADA is configured to support login via Google, GitHub, and Facebook.
    -   These are enabled and configured within the Supabase project dashboard (Authentication > Providers).
    -   Supabase handles the complexities of the OAuth 2.0 flow, including redirects and token exchange.

-   **JWT (JSON Web Tokens)**:
    -   Upon successful authentication, Supabase issues a JWT to the client.
    -   This JWT is automatically included by the Supabase JS client library (`supabase-js`) in subsequent requests to the Supabase backend (database, storage, edge functions).
    -   The JWT contains user information, including the `user_id` (`sub` claim) and `role`.

-   **Session Management**:
    -   Supabase Auth manages user sessions.
    -   It supports automatic token refreshing to keep users logged in.
    -   Sessions can be persisted in `localStorage` (default behavior of `supabase-js`) or other storage mechanisms.

-   **User Management**:
    -   Authenticated users are automatically added to the `auth.users` table in your Supabase database.
    -   This table stores user metadata like email, provider information, and the unique `user_id` (UUID).

-   **Integration with Row Level Security (RLS)**:
    -   Supabase Auth is tightly integrated with PostgreSQL's RLS.
    -   RLS policies defined on database tables can use helper functions like `auth.uid()` (returns the ID of the authenticated user) and `auth.role()` (returns the role of the authenticated user) to control data access.
    -   This is fundamental to how CICADA secures private documents and user-specific data. For example, `documents_private.user_id = auth.uid()`.

-   **Secure Passwordless Login**: While CICADA focuses on OAuth, Supabase Auth also supports other methods like email/password, magic links, phone OTP, etc., which could be integrated if needed.

## Client-Side Interaction (`src/lib/supabase.ts`)

The frontend interacts with Supabase Auth via helper functions in `src/lib/supabase.ts`:
-   `signInWithProvider(provider: Provider)`: Initiates the OAuth login flow for the specified provider.
-   `signOut()`: Logs out the current user and clears their session.
-   `getSession()`: Retrieves the current session information.
-   `getCurrentUser()`: Retrieves the current authenticated user object.

The `AuthProvider` (`src/context/AuthContext.tsx`) uses these functions and listens to `supabase.auth.onAuthStateChange` to keep the application's UI in sync with the user's authentication state.

## Edge Function Authentication

-   When Edge Functions are invoked from the client, the Supabase JS client automatically includes the user's JWT in the `Authorization` header.
-   Inside an Edge Function, you can access user information from this JWT to perform operations in the context of that user.
    ```typescript
    // Example in an Edge Function
    const authHeader = req.headers.get("Authorization")!;
    const { data: { user } } = await supabaseClient.auth.getUser(authHeader.replace("Bearer ", ""));
    if (user) {
      // Perform actions based on user.id
    }
    ```
    The `cleoChat` function utilizes this to get the `user_id` for accessing private vectors.

Supabase Auth provides a comprehensive and secure foundation for CICADA's authentication requirements, simplifying development while ensuring robust security.
