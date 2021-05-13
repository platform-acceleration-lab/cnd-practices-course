# Green Field Cloud Native App - Day 0

## Configure a Continuous Integration Pipeline

You will use [Github Actions](https://docs.github.com/en/actions) to
implement your pipelines in this course.

1.  The codebase supplies the initial Continuous Integration pipeline
    workflow.
    Cherry pick it now:

    ```bash
    git cherry-pick pipeline
    ```

1.  Review the pipeline at `.github/workflows/ci.yml`

    -   The `build-and-publish` job is the key part of
        Continuous Integration that allows developers to integrate their
        work frequently on a mainline branch.
        It builds and tests the migration and application code with an
        integrated database.

    -   The `deploy` job takes the published Spring Boot application
        fat jar files from the `build-and-publish` job,
        and deploys them to the *Review* environment.
        Notice the `env` section,
        it sources credentials that you will configure next.

1.  Recall the Cloud Foundry credentials you used when you manually
    connected to,
    and deployed to your developer sandbox environment.
    Use the following command if you do not remember:

    ```bash
    cf target
    ```

    *Note:*
    You need to configure the `CF_SPACE` as `review`.
    Contact your instructor if you forgot your password.

1.  With the Cloud Foundry credentials you recalled from the last step,
    configure the following
    [Github Repository Secrets](https://docs.github.com/en/actions/reference/encrypted-secrets#creating-encrypted-secrets-for-a-repository):

    -   Cloud Foundry Foundation Controller URL:
        `CF_API_URL`
    -   Organization:
        `CF_ORG`
    -   Space:
        `CF_SPACE`
    -   User Name (the instructor gave to you): `CF_USERNAME`
    -   User Password (the instructor gave to you): `CF_PASSWORD`

1.

