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

### Configure the application deployment for review environment

You are supplied with review environment configuration template for the
application and migration at `deployments/cf/review` directory.

Configure the review environment in your currently targeted `review`
space with the *Ingress Route* or *Hostname* for you migration
and tracker applications:

1.  If you do not remember your domain from configuring the development
    environment,
    you can find out the base domain of your Cloud Foundry foundation by
    running the following command:

    ```bash
    cf domains
    ```

    Ignore any entries that are named or marked with "Internal" or
    "Mesh".

1.  Review the `deployments/cf/review/manifest.yml` file `route`
    specifications.

    They will look like this:

    -   Migration application:
        `- route: tracker-migration-review-{your initials}.{DOMAIN}`

    -   Tracker application:
        `- route: tracker-review-{your initials}.{DOMAIN}`

    The `{DOMAIN}` placeholder will be replaced by the domain you
    identified in step 1.

    The `{your initials}` placeholder is added to make the hostnames
    unique.

    An example of what these will look like (with your specific initials):

    - `- route: tracker-migration-review-wwk.apps.evans.pal.pivotal.io`
    - `- route: tracker-review-wwk.apps.evans.pal.pivotal.io`

1.  Like when you set up your development environment,
    you will need to *reserve* DNS hostnames for your review
    environment applications.
    But before you attempt to do so,
    you need to check if they are already reserved on the foundation.
    Use the following command to check of the hostnames (routes) are
    already reserved:

    ```bash
    cf check-route tracker-migration-review-{your initials} {DOMAIN}
    ```

    ```bash
    cf check-route tracker-review-{your initials} {DOMAIN}
    ```

    example:

    - `cf check-route tracker-migration-review-wwk apps.evans.pal.pivotal.io`
    - `cf check-route tracker-review-wwk apps.evans.pal.pivotal.io`

    The output of the commands will tell you whether the hostnames
    (routes) are reserved.

    If they are reserved,
    try a different value in the {your initials} placeholder,
    until you get values that are not reserved.

1.  Reserve your development sandbox application hostnames (routes):

    ```bash
    cf create-route review {DOMAIN} --hostname tracker-migration-review-{your initials}
    ```

    ```bash
    cf create-route review {DOMAIN} --hostname tracker-review-{your initials}
    ```

    example:

    - `cf create-route review apps.evans.pal.pivotal.io --hostname tracker-migration-review-wwk`
    - `cf create-route review apps.evans.pal.pivotal.io --hostname tracker-review-wwk`

1.  Configure the `route` specifications in the
    `deployments/cf/review/manifest.yml` file according to the values you
    reserved in the previous step.
