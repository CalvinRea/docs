# How to Contribute

We welcome contributions to the CICADA project! Whether you're fixing a bug, adding a new feature, or improving documentation, your help is appreciated.

## Getting Started

1.  **Fork the Repository**: Start by forking the main CICADA repository to your own GitHub account.
2.  **Clone Your Fork**: Clone your forked repository to your local machine.
    ```bash
    git clone https://github.com/YOUR_USERNAME/CICADA-main.git
    cd CICADA-main
    ```
3.  **Set Up Upstream Remote**: Add the original repository as the "upstream" remote.
    ```bash
    git remote add upstream https://github.com/ajojotank/CICADA
    ```
    (Replace `original_owner` with the actual owner of the main repository).
4.  **Install Dependencies**: Ensure you have all necessary prerequisites installed (Node.js, etc.) and then run:
    ```bash
    npm install
    ```
5.  **Create a New Branch**: Create a new branch for your changes. Choose a descriptive name (e.g., `feat/add-user-roles`, `fix/login-button-bug`).
    ```bash
    git checkout -b your-branch-name
    ```

## Making Changes

1.  **Code**: Make your desired code changes. Follow the existing coding style and conventions.
2.  **Testing**:
    -   If you're fixing a bug, try to write a test that reproduces the bug before fixing it.
    -   If you're adding a new feature, write unit tests for any new API service logic.
    -   Ensure all existing tests pass by running `npm test`.
3.  **Linting**: Ensure your code adheres to the project's linting rules. You can run the linter with:
    ```bash
    npm run lint
    ```
4.  **Commit Your Changes**: Write clear and concise commit messages.
    ```bash
    git add .
    git commit -m "feat: Implement user role management in AuthContext"
    ```
5.  **Keep Your Branch Updated**: Periodically, rebase your branch on the latest changes from the upstream `main` branch to avoid merge conflicts.
    ```bash
    git fetch upstream
    git rebase upstream/main
    ```

## Submitting a Pull Request

1.  **Push Your Branch**: Push your feature branch to your forked repository on GitHub.
    ```bash
    git push origin your-branch-name
    ```
2.  **Open a Pull Request (PR)**:
    -   Go to the original CICADA repository on GitHub.
    -   You should see a prompt to create a pull request from your recently pushed branch.
    -   If not, go to the "Pull requests" tab and click "New pull request".
    -   Choose your forked repository's branch to compare against the original repository's `main` branch.
3.  **Describe Your PR**:
    -   Write a clear title and description for your pull request.
    -   Explain the changes you've made and why.
    -   If your PR addresses an existing issue, link to it (e.g., "Closes #123").
4.  **Review Process**:
    -   Project maintainers will review your PR.
    -   Be prepared to address any feedback or make further changes.
    -   The CI/CD pipeline (GitHub Actions) will run checks on your PR. Ensure these pass.
5.  **Merge**: Once your PR is approved and all checks pass, a maintainer will merge it into the `main` branch.

## Coding Guidelines (General)

-   Follow the existing code style and structure.
-   Write clear, readable, and maintainable code.
-   Comment your code where necessary, especially for complex logic.
-   Ensure your changes do not introduce breaking changes without proper discussion.
-   Update documentation if your changes affect existing features or add new ones.

Thank you for contributing to CICADA!
