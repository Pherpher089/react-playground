# Data Base Learnings

## Joins

- (INNER) JOIN: Returns only rows that have matching values in both tables
- LEFT (OUTER) JOIN: Returns all rows from the left table, and only the matched rows from the right table
- RIGHT (OUTER) JOIN: Returns all rows from the right table, and only the matched rows from the left table
- FULL (OUTER) JOIN: Returns all rows when there is a match in either the left or right table

### Inner Joins

Returns only rows that match in both tabels

> Example: Work Order Desk

If a user has no order they do not appear

### Left Join

Returns all rows from the left table + matching rows from the right table.

> Example: Work Order Desk

Shows all users, and include orders if they have any.

If a user has no orders, they still appear, but order fields are null.

### Right Join

Same idea as left Join, but keeps all rows from the right table. Less commonly used because you can usually rewrite it as a LEFT JOIN

### Full Outer Join

Returns all rows from both tables, matching where possible. Non-matches from either side still appear with nulls.

> Interview Tips: An inner join only returns matching records from both tables. A left join returns everything from the left table and only the matching records from the right table, with nulls everywhere there isn't a match.

## Optimizing

### Making Read queries faster

> Intervew Tips: First I’d identify the bottleneck with an execution plan like EXPLAIN. Then I’d look at indexing columns used in WHERE clauses, JOINs, and ORDER BYs. I’d also avoid SELECT \*, reduce unnecessary joins, paginate large result sets, and consider caching if the data doesn’t change often.

Key terms:

- Indexing: Database lookup shortcut
- Query Plan / EXPLAIN: Shows how the DB is executing the query
- Pagination: dont return 100k rows at once
- Caching: don't recompute/fetch the same expenxive data repeatedly.

### Optimizing lots of writes

1. Batch writes - Insert 50 or 100 rows at a time

- Fewer network roudnd trips
- Fewer transactions
- Less DB overhead

> Interview Tips: If there are a large number of writes, I’d look at batching them instead of writing one record per request. That reduces transaction overhead and network round trips.

2. Use a Queue
   Instead of writing directly to the DB during every request, push events into a queue like RabbitMQ, SQS or Kafka, then process them asynchronously.

- Smooths trafic spikes
- Protects the DB
- Lets workers process wirtes at a controlled rate

> Interview Tips: If writes are coming in too quickly, I’d consider putting them through a queue so the application can accept traffic quickly while background workers process writes at a sustainable rate.

3. Reduce index on write-heavy tables

Indexes make reads faster, but the make writes slower because every insert/update also has to update the indexes.

> Interview Tip: For write-heavy tables, I’d be careful not to over-index. Indexes help reads, but they add overhead to writes, so I’d only keep indexes that support important query patterns.

4. Partition large tables

partitioning means splitting one huge table into smaller physical chunks, usually by date, tenant, reagon or ID range.

- Easier to write/query smaller chunks
- Better maintenance
- Can improve performance at scale

> Interview Tips: For very large write-heavy tables, partitioning can help, especially if data is naturally grouped by time or tenant. For example, event logs are often partitioned by date.

5. Append-only tables

For event-style data, don't constantly update rows. Just insert new records.

Example:

Instead of updating a status history record repeatedly, insert:

- status changed to open
- status changed to in progress
- status changed to complete

Why it helps:

- inserts are simpler than updates
- avoids lock contention
- preserves history

> Interview Tips: If the use case allows it, I’d consider an append-only model where we insert events rather than constantly updating existing rows. That can be more write-friendly and also gives us a history of changes.
