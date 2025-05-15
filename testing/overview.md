# Testing Overview

CICADA includes a suite of unit tests for its API service layer, primarily focused on ensuring the correct behavior of functions interacting with the Supabase backend. These tests are written using **Vitest**, a Vite-native testing framework.

## Testing Framework

-   **Vitest**: Chosen for its speed, Vite integration, and familiar Jest-like API.
-   **Mocking**: Utilizes Vitest's mocking capabilities (`vi.fn()`, `vi.spyOn()`, `vi.mock()`) to isolate units under test and simulate dependencies, especially Supabase client calls.

## Test File Location

Test files are located in the `src/tests/api/` directory, mirroring the structure of the API services they are testing:
-   `adminService.test.ts`
-   `chatService.test.ts`
-   `documentService.test.ts` (though this seems to be a placeholder or conceptual, as detailed tests are in specific service files)
-   `folderService.test.ts`
-   `privateDocumentService.test.ts`
-   `publicDocumentService.test.ts`

## Scope of Tests

The existing tests primarily cover the **API service layer** (`src/services/api/`). They focus on:
-   **Correct Invocation of Supabase Client Methods**: Ensuring that service functions call the appropriate `supabase.from().select()`, `supabase.functions.invoke()`, etc., with the correct parameters.
-   **Handling of Supabase Responses**: Testing how service functions process successful responses and errors from Supabase.
-   **Data Transformation**: Verifying that data fetched from Supabase is correctly mapped to the expected frontend types (e.g., `Document`, `AdminStats`).
-   **Error Handling**: Ensuring that errors from Supabase or internal logic are caught and propagated or handled gracefully (e.g., returning default values).

## Common Testing Patterns

-   **Mocking Supabase Client**:
    The Supabase client (`supabase`) is mocked to prevent actual network calls during tests.
    ```typescript
    // Example from adminService.test.ts
    vi.mock("@/lib/supabase", () => ({
      supabase: {
        functions: {
          invoke: vi.fn(), // Mock the invoke method
        },
        // ... other Supabase methods as needed by the service under test
      },
    }));
    ```
-   **Arranging Mocks**:
    Before each test (`beforeEach`), mock implementations are set up to return specific data or errors.
    ```typescript
    // Example from adminService.test.ts
    const mockedInvoke = supabase.functions.invoke as Mock;
    // ...
    beforeEach(() => {
      vi.clearAllMocks(); // Clear mocks between tests
      mockedInvoke.mockResolvedValue({ data: mockAdminStats, error: null });
    });
    ```
-   **Spying on Console**: `vi.spyOn(console, 'error')` is used to check if errors are logged as expected, especially in catch blocks.
-   **Assertions**: `expect` from Vitest is used to make assertions about function return values, mock call counts, arguments, and logged errors.

## Running Tests
Tests are run using the npm test script defined in package.json
This command will execute Vitest, which discovers and runs all *.test.ts (or similar patterned) files.