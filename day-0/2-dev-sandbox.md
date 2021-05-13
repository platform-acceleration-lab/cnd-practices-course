# Green Field Cloud Native App - Day 0

## Configure and run in a platform development sandbox (Cloud Foundry)

### Access the development sandbox on the platform

1.  You should have been provided Cloud Foundry credentials
    by your instructor before the class.
    If you have not received, ask your instructor now.

1.  You also have a command line client installed on your worksation,
    the `cf` command.

1.  Login to Cloud Foundry with the `cf login` command.
    You will be prompted for a user name and password,
    using what your instructor provided to you.

1.  Observe the output.
    On Cloud Foundry,
    you are provided an *Organization*, or *Org* for short.

    This is where you can organize and deploy your applications
    and their backing databases into one or more *Spaces*.

1.  You are also provided a *Space* called `sandbox`.
    Consider this where you will deploy your development sandbox
    environment.

1.  Run the `cf target` command.
    This will show you the "coordinate" that your local workstation is
    pointed to,
    including a URL for the Cloud Foundry platform controller,
    the user,
    the org,
    and space.

    You will see later that the `cf target` command can be use to switch
    your workstation context to point to different orgs or spaces.

### Deploy the development database server instance in development sandbox

Before you can configure and deploy an application,
you need to create a database server machine.

Cloud Foundry provides the ability for you to provision dedicated
MySQL machines via self-service.

Let's do that now:

1.  Execute the following:

    `cf create-service p.mysql db-small tracker-database`

1.  View the output of the command,
    you should see something like this:

    ```bash
    Creating service instance tracker-database in org <your org> / space sandbox as <user username>...
    OK

    Create in progress. Use 'cf services' or 'cf service tracker-database' to check operation status.
    ```

    You can see that this is an *asynchronous* operation - or that the
    command does not wait until the database machine is created before
    the command ends.

1.  Run the following command to see the status of the database creation
    process:

    `cf services`

    You will see an output similar to the following:

    ```bash
    Getting services in org <your org> / space sandbox as <your username>...

    name               service   plan       bound apps   last operation       broker                   upgrade available
    tracker-database   p.mysql   db-small                create in progress   dedicated-mysql-broker   no
    ```

    Notice the database server is still in the creation process.
    We will come back later to check the status.

### Configure the application deployment for development sandbox

You are supplied with configuration templates for the application and
migration at `deployments/cf/` directory for the various environments,
but you will need to configure specific for your environments.

For now,
configure the development environment in your currently targeted
`sandbox` space with the *Ingress Route* or *Hostname* for you migration
and tracker applications:

1.  Find out the base domain of your Cloud Foundry foundation by running
    the following command:

    `cf domains`

    Ignore any entries that are named or marked with "Internal" or
    "Mesh".

1.  Review the `deployments/cf/dev/manifest.yml` file `route`
    specifications.

    They will look like this:

    -   Migration application:
        `- route: tracker-migration-dev-{your initials}.{DOMAIN}`

    -   Tracker application:
        `- route: tracker-{your initials}.{DOMAIN}`

    The `{DOMAIN}` placeholder will be replaced by the domain you
    identified in step 1.

    The `{your initials}` placeholder is added to make the hostnames
    unique.

    An example of what these will look like (with your specific initials):

    - `- route: tracker-migration-dev-wwk.apps.evans.pal.pivotal.io`
    - `- route: tracker-dev-wwk.apps.evans.pal.pivotal.io`

1.  You will need to *reserve* DNS hostnames for your development
    sandbox applications,
    but before you attempt to do so,
    you need to check if they are already reserved on the foundation.
    Use the following command to check of the hostnames (routes) are
    already reserved:

    - `cf check-route tracker-migration-dev-{your initials} {DOMAIN}`
    - `cf check-route tracker-dev-{your initials} {DOMAIN}`

    example:

    - `cf check-route tracker-migration-dev-wwk apps.evans.pal.pivotal.io`
    - `cf check-route tracker-dev-wwk apps.evans.pal.pivotal.io`

    The output of the commands will tell you whether the hostnames
    (routes) are reserved.

    If they are reserved,
    try a different value in the {your initials} placeholder,
    until you get values that are not reserved.

1.  Reserve your development sandbox application hostnames (routes):

    - `cf create-route sandbox {DOMAIN} --hostname tracker-migration-dev-{your initials}`
    - `cf create-route sandbox {DOMAIN} --hostname tracker-dev-{your initials}`

    example:

    - `cf create-route sandbox apps.evans.pal.pivotal.io --hostname tracker-migration-dev-wwk`
    - `cf create-route sandbox apps.evans.pal.pivotal.io --hostname tracker-dev-wwk`

1.  Configure the `route` specifications in the
    `deployments/cf/dev/manifest.yml` file according to the values you
    reserved in the previous step.

### Deploy and run the tracker migration and application in the development sandbox environment

Let's deploy the tracker migration and application:

1.  Before you deploy your applications,
    make sure the `tracker-database` database machine has finished
    provisioning:

    `cf services`

1.  Once you have verified the database machine is running,
    deploy the tracker migration and application manually:

    `cf push -f deployments/cf/dev/manifest.yml`

    Notice the tracker migration is deployed first.
    The database is required by the tracker application,
    so the `tracker-migration` is required to deploy before the
    `tracker` application for the initial release.

    Later we will see that the migration and application may change at
    different rates.

1.  View the output of the following endpoints:

    -   Database migration endpoints:
        `https://{tracker development migration route}/actuator/flyway`

        This will show that you have no database migrations yet.
        As we build out our application functionality we will create
        migrations as we go along.

    -   Tracker application health endpoint:
        `https://{tracker development application route}/actuator/health`

        Notice that you see full details here.
        You will see that the Spring Boot application will show the
        state of the connections to the database.

    -   Tracker application readiness endpoint:
        `http://{tracker development application route}/actuator/health/readiness`

        This indicates whether or not the tracker application is ready
        to do work,
        and typically used by platforms at start up time to determine
        when to route traffic to it.

    -   Tracker application liveness endpoint:
        `http://{tracker development application route}/actuator/health/liveness`

        This indicates whether or not the tracker application is "live",
        or able to do work.
        It can be configured with the application deployment such that
        the platform can monitor and dispose of unhealthy application
        processes.

## Configure the Review Environment

### Create the review environment

You will run the *Review Environment* on the same foundation and same
*Organization* as your development sandbox.

In reality you might run this on an alternate foundation and/or
organization,
but for constraints running the course on common infrastructure,
you will run under your instructor assigned organization.

1.  Create a review space as follows:

    `cf create-space review`

1.  Switch your workstation context to point to the review environment:

    `cf target -s review`

### Deploy the review database server instance in review environment

Before you can configure and deploy an application in review environment,
you need to create a database server machine.

You will create the database with the same procedure as you did when
creating the database in the developer sandbox:

1.  Execute the following:

    `cf create-service p.mysql db-small tracker-database`

1.  View the output of the command,
    you should see something like this:

    ```bash
    Creating service instance tracker-database in org <your org> / space review as <user username>...
    OK

    Create in progress. Use 'cf services' or 'cf service tracker-database' to check operation status.
    ```

1.  Run the following command to see the status of the database creation
    process:

    `cf services`

    You will see an output similar to the following:

    ```bash
    Getting services in org <your org> / space review as <your username>...

    name               service   plan       bound apps   last operation       broker                   upgrade available
    tracker-database   p.mysql   db-small                create in progress   dedicated-mysql-broker   no
    ```

## Configure a Continuous Integration Pipeline

You will use [Github Actions](https://docs.github.com/en/actions) to
implement your pipelines in this course.

1.  The codebase supplies the initial Continuous Integration pipeline
    workflow.
    Cherry pick it now:

    `git cherry-pick pipeline`

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

    `cf target`

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
## Build a basic CRUD app

- time entry
- spring data jdbc
