# Green Field Cloud Native App - Day 0

## Configure the Review Environment

### Create the review environment

You will run the *Review Environment* on the same foundation and same
*Organization* as your development sandbox.

In reality you might run this on an alternate foundation and/or
organization,
but for constraints running the course on common infrastructure,
you will run under your instructor assigned organization.

1.  Create a review space as follows:

    ```bash
    cf create-space review
    ```

1.  Switch your workstation context to point to the review environment:

    ```bash
    cf target -s review
    ```

### Deploy the review database server instance in review environment

Before you can configure and deploy an application in review environment,
you need to create a database server machine.

You will create the database with the same procedure as you did when
creating the database in the developer sandbox:

1.  Execute the following:

    ```bash
    cf create-service p.mysql db-small tracker-database
    ```

1.  View the output of the command,
    you should see something like this:

    ```bash
    Creating service instance tracker-database in org <your org> / space review as <user username>...
    OK

    Create in progress. Use 'cf services' or 'cf service tracker-database' to check operation status.
    ```

1.  Run the following command to see the status of the database creation
    process:

    ```bash
    cf services
    ```

    You will see an output similar to the following:

    ```bash
    Getting services in org <your org> / space review as <your username>...

    name               service   plan       bound apps   last operation       broker                   upgrade available
    tracker-database   p.mysql   db-small                create in progress   dedicated-mysql-broker   no
    ```
