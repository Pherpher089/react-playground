# Dotnet Service Architecture Notes

## Domain Model

### What is a domain model?

> A set of classes that represent concepts and enforce business ruels.

Not:

- Database tables
- DTOs
- JSON contracts
- EF models
- API responses

Those are infastructure consers.

#

The domain models is pure business meaning.

#

### Concrete Example

Imagine a bad version of WorkOrder:

```c#
public class WorkOrder
{
  public guid Id { get; set; }
  public string Title { get; set; }
}
```

This is just a data container.

There are no rules.

You can create:

```C#
new WorkOrder { Title = ""};
```

That makes no business sense.

#

### A Real Domain Model

A real domain model enforces invarients:

- Title must not be empty
- Title length must be from 3 - 100
- CreatedAt must be set at creation
- Status must start as open
- You can't move from Done->Open

Now it looks like this:

```C#
public class WorkOrder
{
    public Guid Id { get; }
    public string Title { get; private set; }
    public WorkOrderStatus Status { get; private set; }
    public DateTime CreatedAtUtc { get; }

    public WorkOrder(string title)
    {
        if (string.IsNullOrWhiteSpace(title))
            throw new ArgumentException("Title cannot be empty.");

        Id = Guid.NewGuid();
        Title = title;
        Status = WorkOrderStatus.Open;
        CreatedAtUtc = DateTime.UtcNow;
    }
}
```

Now:

- you can't create an invalid WorkOrder.
- The rules live inside the modle.
- No controller can bypass them.

That's domain modeling

#

## What is a `DbContext`

`DbContext` is the main EF Core class that represents a session with the database.

Thisk of it as a **Unit of Work** + **Change Tracker** + **Query Gateway**.

It does three major things:

### 1) Tracks Objects

When EF loads entities from the database, it tracks them.

Example:

```C#
var workOrder = db.WorkOrders.First();
WorkOrder.SetTitle("New title");

await db.SaveChangesAsync();
```

EF knows:

- which entity changed
- what properties changed
- what SQL update to generate

#

### 2) Translates LINQ -> SQL

When you write:

```C#
var orders = db.workOrders
  . where(x => x.Status == WorkOrderStatus.Open)
  .ToList();
```

EF translates that to something like:

```SQL
SELECT *
FROM WorkOrders
WHERE Status = 1
```

#

### 3) Persists changes

Where you call:

```C#
await db.SaveChangesAsync();
```

EF generates:

```SQL
INSERT
UPDATE
DELETE
```

statements for anything being tracked.

#

## Mental Model

you can think of `DbContext` as:

Application Code > DbContext > Database

It is the gateway to the database.

#

What is the `DbContextOptions<T>`

```C#
public WorkOrderDeskContext(DbContextOptions<WorkOrderDeskContext> options)
    : base(options);
```

This is **Configuration information for the context**.

Examples of what goes inside `DbContextOptions`:

- Which database provide to use (SQLite, SQL Server, Postgress)
- Connection string
- Logging options
- Query behavior
- Migrations assembly

Example when we wire it later:

```C#
builder.Services.AddDbContext<WorkOrderDeskContext>(options =>
{
  options.UseSqlite("Data Source=workorders.db");
})
```

That configuration is stored inside the DbContextOptions.

#

## Why pass it to base(options)?

Because the parent class (DbContext) needs that configuration.

It means:

> Call the base constructor of DbContext and give it these options.

Without that, EF wouldn't know:

- what database to connect to
- how to translate queries
- where migrations live

#

## What is the `=>` syntax?

This is called an **expression-bodied member**.

Example:

```C#
public DbSet<workOrder> WorkOrders => Set<WorkOrder>();
```

This is shorthand for:

```C#
Public DbSet<WorkOrder> WorkOrders
{
  get
  {
    return Set<WorkOrder>();
  }
}
```

So `=>` just means return the result of this expression.

#

Why use it here?
Cleaner syntax for simple properties.

#

## What is `Set<T>()`

This is a method on the `DbContext`.

It returns the `DbSet` for a given entity type.

So this:

```C#
public DbSet<WorkOrder> WorkOrders => Set<WorkOrder>();
```

means:

> Give me the EF collection that represents the WorkOrders table.

#

## What is a "model" in `OnModelCreating`?

In EF Core, the model means:

> The map between your C# domain classes and the database schema.

### So what is `OnModelCreating` doing?

It is where EF builds that mapping.

EF builds an internal object called:

`IModel`

Which contains:

- entities
- keys
- indexes
- releationships
- column types
- constraints

Example configuration:

```C#
modelBuilder.Entity<WorkOrder>()
    .Property(x => x.Title)
    .HasMaxLength(100)
    .IsRequired();
```

That tells EF:

```SQL
Title VARCHAR(100) NOT NULL
```

### What happens during startup

When the app starts:

1️⃣ EF scans the DbContext
2️⃣ EF runs OnModelCreating
3️⃣ EF builds the model metadata
4️⃣ EF caches that model for performance

This metadata is used for:

- migrations
- query translation
- change tracking
