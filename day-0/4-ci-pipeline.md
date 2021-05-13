# Green Field Cloud Native App - Day 0

## Configure a Continuous Integration Pipeline

You will use [Github Actions](https://docs.github.com/en/actions) to
implement your pipelines in this course.

1.  The codebase supplies the initial Continuous Integration pipeline
    workflow,
    but it is not at the initial commit of the repository you forked.
    It is at a commit tagged as `ci-pipeline`.
    It was not supplied as part of the initial commit because we do not
    want it to run until you configure your review environment..

1.  Cherry pick the `ci-pipeline` commit to pull in the Continuous
    Integration pipeline.

    ```bash
    git cherry-pick ci-pipeline
    ```

    Review it at `.github/workflows/ci-pipeline.yml`

    -   The pipeline is triggered on push event to the `tracker`
        repository.
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

1.  Given you have made changes to the deployment manifest templates,
    and pulled in the pipeline,
    you need to stage,
    commit,
    and push your changes to kick off your CI pipeline:

    ```bash
    git add deployments/cf
    git commit -m'configured review environment'
    git push origin main
    ```

1.  This push event to the `tracker` repository will kick of the
    pipeline.
    Monitor it at the following url
    (make sure to substitute <your github account>):

    ```bash
    https://github.com/<your github account>/tracker/actions
    ```

    DEMO
