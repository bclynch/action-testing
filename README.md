# Action Testing
- [Docs](https://help.github.com/en/categories/automating-your-workflow-with-github-actions)
- Workflows use YAML. [Learn more about YAML here](https://www.codeproject.com/Articles/1214409/Learn-YAML-in-five-minutes)

## Workflows
- [Configuring a Workflow](https://help.github.com/en/articles/configuring-a-workflow)
- Workflows are custom automated processes that you can set up in your repository to build, test, package, release, or deploy any project on GitHub. With workflows you can automate your software development life cycle with a wide range of tools and services.
- Workflows must have at least one job, and jobs contain a set of steps that perform individual tasks. Steps can run commands or use an action. You can create your own actions or use actions shared by the GitHub community and customize them as needed.
- You can configure a workflow to start when a GitHub event occurs, on a schedule, or from an external event.

### [Syntax](https://help.github.com/en/articles/workflow-syntax-for-github-actions)
- `name`
    - The name of your workflow. GitHub displays the names of your workflows on your repository's actions page. If you omit this field, GitHub sets the name to the workflow's filename.
- `on`
    - *Required* The name of the GitHub event that triggers the workflow. You can provide a single event string, array of events, or an event configuration map that schedules a workflow or restricts the execution of a workflow to specific files, tags, or branch changes.
    - [Events that trigger workflows](https://help.github.com/en/articles/events-that-trigger-workflows)
    - Examples:
    ```
        # Trigger on push
        on: push

        # Trigger the workflow on push or pull request
        on: [push, pull_request]
    ```
#### [Default env vars](https://help.github.com/en/articles/virtual-environments-for-github-actions#default-environment-variables)
#### [Creating custom secrets / vars](https://help.github.com/en/articles/virtual-environments-for-github-actions#creating-and-using-secrets-encrypted-variables)
- Example: 
    ```
    steps:
        - name: Hello world action
            with: # Set the secret as an input
                super_secret: ${{ secrets.SuperSecret }}
            env: # Or as an environment variable
                super_secret: ${{ secrets.SuperSecret }}
    ```
#### GITHUB_TOKEN
- The `GITHUB_TOKEN` secret is a GitHub App installation token scoped to a repository. GitHub creates the `GITHUB_TOKEN` secret for you by default, but you must include it in your workflow file in order for actions to use it. You can use the `GITHUB_TOKEN` secret to make authenticated GitHub API calls on behalf of an action.
- Example:
    ```
    steps:
    - name: My first action
        env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
    - name: My second action
        env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
    ```

### Workflow Limits
- You can execute up to 20 workflows concurrently per repository.
- You can execute up to 1000 API requests in an hour across all actions within a repository.
- Each job in a workflow can run for up to 6 hours of execution time.
- You can run up to 20 jobs concurrently per repository across all workflows.

### Checkout Action
- There are several standard actions you can use in your workflow. The checkout action is a standard action that you must include in your workflow before other actions when:
    - Your workflow requires a copy of your repository's code, such as when you're building and testing your repository or using continuous integration.
    - There's at least one action in your workflow that is defined in the same repository.
- To use the standard checkout action without further specifications, include this step:
```
- uses: actions/checkout@v1
```

### Referencing Actions in Workflow
- Workflows can use actions defined in:
    - A public repository
    - The same repository where your workflow file references the actions
    - A published Docker container image on Docker Hub
- To use an action defined in a private repository, both the workflow file and the action must be in the same repository.

### Example repository file structure
```
|-- hello-world (repository)
|   |__ .github
|       └── workflows
|           └── my-first-workflow.yml
|       └── actions
|           |__ hello-world-action
|               └── action.yml
```

Example workflow
```
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      # This step checks out a copy of your repository.
      - uses: actions/checkout@v1
      # This step references the directory that contains the action.
      - uses: ./.github/actions/hello-world-action
```

## [Actions](https://help.github.com/en/articles/about-actions)
- Individual tasks that you combine as steps to create a job. Actions are the smallest portable building block of a workflow. You can create your own actions, use actions shared from the GitHub community, and customize public actions. To use an action in a workflow, you must include it as a step.
- You can create actions by writing custom code that interacts with your repository in any way you'd like, including integrating with GitHub's APIs and any publicly available third-party API. For example, an action can publish npm modules, send SMS alerts when urgent issues are created, or deploy production-ready code.
- Actions run in a virtual environment or Docker container. You can define an action's inputs, outputs, and environment variables.
- [Creating a README for actions](https://help.github.com/en/articles/about-actions#creating-a-readme-file-for-your-action)
- [Actions toolkit](https://github.com/actions/toolkit) can be useful for action development

### [YAML Syntax For Actions](https://help.github.com/en/articles/metadata-syntax-for-github-actions#about-yaml-syntax-for-github-actions)
- `name`
    - *Required* The name of your action. GitHub displays the name in the Actions tab to help visually identify actions in each job.
- `description`
    - *Required* A short description of the action.
- `inputs`
    - *Optional* Input parameters allow you to specify data that the action expects to use during runtime. GitHub stores input parameters as environment variables. Input ids with uppercase letters are converted to lowercase during runtime. We recommended using lowercase input ids.
- `outputs`
    - *Optional* Output parameters allow you to specify the data that subsequent actions can use later in the workflow after the action that defines these outputs has run.
- `runs`
    - *Required* The command to run when the action executes.
- `using`
    - *Required* The application to use to execute the code specified in main.
    - exs:
    ```
    # using node.js
    using: 'node12'
    main: 'main.js'
    ```
    ```
    # using Docker
    using: 'docker'
    image: 'docker://debian:stetch-slim'
    ```
- `env`
    - *Optional* Specifies a key/value map of environment variables to set in the virtual environment.

## Continuous Integration
- [Docs](https://help.github.com/en/articles/about-continuous-integration)
- [Using Action](https://help.github.com/en/articles/setting-up-continuous-integration-using-github-actions)
- When you commit code to your repository, you can continuously build and test the code to make sure that the commit doesn't introduce errors. Your tests can include code linters (which check style formatting), security checks, code coverage, functional tests, and other custom checks.