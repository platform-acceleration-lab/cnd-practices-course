# Green Field Cloud Native App - Day 0

## Bootstrap a database backed web app project

### Get the scaffold codebase and review it

-   Fork the
    [tracker](https://github.com/platform-acceleration-lab/tracker)
    repo to your Github account.

-   Checkout the start point from the tracker codebase:

    ```bash
    cd ~/workspace
    git clone https://github.com/<your account>/tracker.git
    cd tracker
    ```

    *Tip*:
    Consider setting up
    [SSH keys and using SSH protocol](https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh) with
    [Deploy keys](https://docs.github.com/en/developers/overview/managing-deploy-keys#deploy-keys),
    or set up
    [OAuth token](https://docs.github.com/en/developers/overview/managing-deploy-keys#https-cloning-with-oauth-tokens) for https access,
    if you prefer to avoid interactive authentication.

-   The current start point of the codebase contains a starter project
    that has the following:

    -   A master project that coordinates build of multiple subprojects.
        See `settings.gradle` to view the configuration.

    -   Sub projects for:

        -   A database-backed blocking web application:
            `applications/tracker/build.gradle`

        -   An application to manage the changes (migrations) of the
            `tracker` application databases:
            `db-migrations/tracker-migration/build.gradle`

    -   Build dependencies for both projects include:

        -   *Spring Boot*
        -   *Spring JDBC* or *Spring Data JDBC* (for database access)
        -   Either *Postgres* or *MySQL* database backends are supported
        -   *Spring Boot Actuator* - used for lifecycle management and
            monitoring your application processes

    -   Database migration tests:
        `db-migrations/tracker-migration/src/test/java/com/vmware/education/tracker/MigrationTests.java`

    -   Web application health tests
        `applications/tracker/src/main/java/com/vmware/education/tracker/ActuatorTests.java`

You have all the code here necessary to start work,
but you will need local databases set up first.

### Setup local development databases

You will use Docker to manage and run your local development
databases.

Using docker makes it convenient to spin up, destroy, or refresh your
development databases without working about persistent installation of
a particular version of a database engine.

You will create a single database *server* instance,
but within it you will create two schemas (or databases),
one for development, and one for local testing.

### Run the scaffold locally to test the plumbing

-   Build and run tests:

    ```bash
    ./gradlew clean build
    ```

-   Run the migration application locally via boot run:

    ```bash
    ./gradlew :db-migrations:tracker-migration:bootRun
    ```

-   Run the tracker application locally via boot run:

    ```bash
    ./gradlew :application:tracker:bootRun
    ```

-   View the output of the following endpoints,
    in a browser of your choice:

    -   Database [migration endpoints](http://localhost:8081/actuator/flyway):

        This will show that you have no database migrations yet.
        As we build out our application functionality we will create
        migrations as we go along.

    -   Tracker application
        [health endpoint](http://localhost:8080/actuator/health):

        Notice that you see full details here.
        You will see that the Spring Boot application will show the
        state of the connections to the database.

    -   Tracker application
        [readiness endpoint](http://localhost:8080/actuator/health/readiness):

        This indicates whether or not the tracker application is ready
        to do work,
        and typically used by platforms at start up time to determine
        when to route traffic to it.

    -   Tracker application
        [liveness endpoint](http://localhost:8080/actuator/health/liveness):

        This indicates whether or not the tracker application is "live",
        or able to do work.
        It can be configured with the application deployment such that
        the platform can monitor and dispose of unhealthy application
        processes.
