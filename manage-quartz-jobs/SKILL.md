# manage-quartz-jobs

Guidelines for managing background jobs and task scheduling in OFBiz.

## Core Concepts

OFBiz uses a built-in Job Scheduler (persisted in `JobSandbox`) to manage asynchronous and recurring tasks.

- **JobSandbox**: The main entity tracking job execution, status (`SERVICE_PENDING`, `SERVICE_RUNNING`, etc.), and persistence.
- **JobPoller**: Background engine that retrieves pending jobs from the database and executes them in a thread pool.
- **RecurrenceRule**: Legacy way to define recurring schedules (frequency, interval, count).
- **TemporalExpression**: Modern, flexible way to define complex recurrence logic (intersections, unions, ranges).

## Scheduling Services

### Using LocalDispatcher (Java/Groovy)

Services can be scheduled programmatically using the `LocalDispatcher.schedule` methods.

```java
// Schedule a one-time job
dispatcher.schedule("MyJobName", "pool", "myServiceName", serviceContext, startTime);

// Schedule a recurring job with RecurrenceRule
dispatcher.schedule("MyJobName", "pool", "myServiceName", serviceContext, startTime, frequency, interval, count, endTime, maxRetry);
```

### Async Execution

`runAsync` is a shorthand for scheduling a job for immediate execution in the background.

```java
dispatcher.runAsync("myServiceName", serviceContext);
```

## Temporal Expressions

Temporal expressions are defined in `TemporalExpression` and related entities. They should be used for complex scheduling needs.

- **Intersection**: Matches if all child expressions match.
- **Union**: Matches if any child expression matches.
- **Difference**: Matches if the first child matches and the second does not.
- **Frequency**: Standard repeating interval.

### Example Seed Data for Temporal Expression

```xml
<TemporalExpression tempExprId="EVERY_NIGHT_11" tempExprTypeId="FREQUENCY" 
    date1="2000-01-01 23:00:00.000" integer1="12" integer2="4"/> <!-- 4 is DAILY (Calendar.DAY_OF_YEAR) -->
```

## Configuration

Job poller settings are in `framework/service/config/serviceengine.xml`.

```xml
<thread-pool send-to-pool="pool"
             purge-job-days="4"
             failed-retry-min="3"
             ttl="120000"
             jobs="100"
             min-threads="2"
             max-threads="5"
             poll-enabled="true"
             poll-db-millis="30000">
    <run-from-pool name="pool"/>
</thread-pool>
```

## Best Practices

- **Job Persistence**: Recurring jobs should usually be persisted in `JobSandbox` via XML data or code.
- **Atomic Jobs**: Ensure services are idempotent as jobs might be retried on failure.
- **Pool Efficiency**: Avoid long-running jobs in the default pool; use dedicated pools if necessary.
- **Monitoring**: Check `JobSandbox` for `SERVICE_FAILED` or `SERVICE_CRASHED` statuses.
- **Purging**: OFBiz includes jobs to purge old `JobSandbox` records; ensure they are running.
