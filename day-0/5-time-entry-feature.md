# Green Field Cloud Native App - Day 0

## Build a basic time tracking application.

Now that you have a fully functioning Continuous Integration pipeline,
as well as Development and Review environments,
you can start building features.

### Requirements

The product owner on the application team has requested a REST backend
application with basic REST CRUD operations for a simple time tracking
application.

The key domain object is the *Time Entry*,
and has the following attributes:

1.  Id:
    The resource identifier of a *Time Entry* resource.

1.  User Id:
    The id of the user resource reporting time.
    The User resource is managed in another application.

1.  Project Id:
    The id of the project resource against which the user reports their
    time.

1.  Timestamp (Date):
    The timestamp when the time is reported

1.  Hours:
    The number of hours (rounded to the nearest hour) reported

Some other requirements:

1.  CRUD operations will use the REST guidelines for usage of
    HTTP status codes.

1.  Attempted updates against time entry resources that do not exist
    will return HTTP Status 404 (not found).

### Integration with previous work

Work is already in progress on the codebase.
Other developers have completed the create and read operations.
You will complete the work on update and delete operations using a
Test-Driven-Development approach.

Follow these steps to prepare for starting your work:

1.  Cherry-pick the following commits to simulate the initial
    (completed) work:

    ```bash
    git cherry-pick time-entries-create
    git cherry-pick time-entries-read
    git cherry-pick time-entries-delete
    ```

1.  Review each commit to get an idea of the design and your team's
    coding style and idoms.
    It will give you hints for how to finish the time entry feature in
    the tracker application

INSTRUCTOR DEMO

### Tips

1.  Review each test case in the TimeEntryControllerTest class,
    notice the following:

    -   Notice the pattern of setting up text fixtures,
        executing the the controller methods under test,
        and verifying successful execution of each method.

        ```java
        // Test setup
        ...

        // Unit under test

        ... controller.<method> ...

        // Verification

        verify(timeEntryRepository) ...
        assertThat(...)
        ...
        ```
    -   Notice the use of mocking patterns,
        such as the mock trained stubs in the test fixture setup:

        ```java
        doReturn(expectedResult)
            .when(timeEntryRepository)
            ...
        ```

        and use of mock verify in the test verification stage:

        ```java
        verify(timeEntryRepository)
            ...
        ```
1.  Use the TimeEntryControllerTest unit test to guide the
    implementation of the TimeEntryController,
    using a Test Driven approach:

    -   Author the test first,
        using the pattern described in previous step.

    -   Run the test first and watch it fail (red).
        Analyze the failure.

    -   Initially the failure will be compilation errors.
        Do the bare minimimum implementation to pass the
        compilation error.

    -   On subsequent assertion or runtime failures,
        implement the fix to clear the failure.
        Repeat this step in the test case until it passes (green).

INSTRUCTOR DEMO

### Implement controller handler method unit tests and implementations

The unit tests drive out the design for the Java code.

You will implement the following:

1.  Add a new test case in the
    `applications/tracker/src/test/java/com/vmware/education/tracker/timeentry/TimeEntryControllerTest.java`
    class:

    ```java
    @Test
    public void testUpdate() {
        // TODO implement the test
    }
    ```

    The test case will demonstrate the design where when saving the
    the *TimeEntry* representation as a *TimeEntryRecord*,
    and return an HTTP status code of 200.

1.  Get the test to pass by implementing a new *handler method* in the
    `applications/tracker/src/main/java/com/vmware/education/tracker/timeentry/TimeEntryController.java`
    class:

    ```java
    @Test
    public ResponseEntity<TimeEntryInfo> update(Long id, TimeEntryForm timeEntryForm) {
        // TODO implement the test
    }
    ```

1.  Add a new test case in
    `applications/tracker/src/test/java/com/vmware/education/tracker/timeentry/TimeEntryControllerTest.java`
    class:

    ```java
    @Test
    public void testUpdate() {
        // TODO implement the test
    }
    ```

    The test case will demonstrate the design where checking the
    existence of the record in the repository before saving the
    *TimeEntry* representation as a *TimeEntryRecord*,
    and return an HTTP status code of 200 if it exists,
    and 404 if it does not.

1.  Get the test to pass by updating the `update()` *handler method* in
    the
    `applications/tracker/src/main/java/com/vmware/education/tracker/timeentry/TimeEntryController.java` class.

### Implement controller handler API tests and associated controller annotations

The API tests drive out the REST API design.
You will see parity across the API and unit tests.

In real projects,
developers may question the need for the unit tests since they are
implicitly covered with the API integration tests.

In this course we do both to get you experienced with both unit and
integration tests.

Implement the update API tests that mirror the unit tests you added in the previous section and annotate the `update()` handler
methods and the associated method arguments to handle the requests in
a Spring Web MVC context.
