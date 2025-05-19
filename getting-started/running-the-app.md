# 🚀 Running the Application

Once you have completed the [Setup Instructions](/getting-started/setup), you can start the CICADA application in development mode.

Open your terminal in the project's root directory (`CICADA-main/`) and run:

```bash
npm run dev
```

This command will:
1.  Start the Vite development server.
2.  Compile the React application.
3.  Make the application available in your web browser, typically at `http://localhost:5173` (Vite will show the exact URL in the terminal).

The development server supports Hot Module Replacement (HMR), so changes you make to the source code will be reflected in the browser automatically without a full page reload.

**Accessing the Application:**
-   Open your web browser and navigate to the URL provided by Vite (e.g., `http://localhost:5173`).
-   You should see the CICADA homepage.

**Development Mode Build:**
If you need to create a development build (e.g., for testing specific build outputs without full optimization), you can use:
```bash
npm run build:dev
```
This will build the application in development mode and place the output in the `dist` folder.
