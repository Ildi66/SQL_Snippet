# Optimizing Database Structures

## Shrinking a file or database -- Best Practices

#### Consider the following information when you plan to shrink a file:

  * A shrink operation is most effective after an operation that creates lots of unused space, such as a truncate table or a drop table operation.
  * Most databases require some free space to be available for regular day-to-day operations. If you shrink a database repeatedly and notice that the database size grows again, this indicates that the space that was shrunk is required for regular operations. In these cases, repeatedly shrinking the database is a wasted operation.
  * A shrink operation does not preserve the fragmentation state of indexes in the database, and generally increases fragmentation to a degree. This is another reason not to repeatedly shrink the database.
  * Shrink multiple files in the same database sequentially instead of concurrently. Contention on system tables can cause delays due to blocking.

## How to Display Data and Log Space Information for a Database
```sql
--- Check the available space
SELECT name , size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0 AS AvailableSpaceInMB
FROM sys.database_files;

--- Check the used space
exec sp_spaceused;

--- Check the fragmentation of indexes
SELECT object_id, index_type_desc, avg_fragmentation_in_percent, avg_fragment_size_in_pages, page_count
FROM sys.dm_db_index_physical_stats(DB_ID('AdventureWorks2017'),NULL,NULL,NULL,NULL)
WHERE avg_fragmentation_in_percent > 0 
ORDER BY avg_fragmentation_in_percent DESC;
```
## How to shrink a database
```sql
-- Shrink a database
USE [AdventureWorks2017]
GO
DBCC SHRINKDATABASE(N'AdventureWorks2017' )
GO

-- Shrink a file
USE [AdventureWorks2017]
GO
DBCC SHRINKFILE (N'AdventureWorks2017' , 0, TRUNCATEONLY)
GO
```
## Link
[Shrink a database - Microsoft](https://docs.microsoft.com/en-us/sql/relational-databases/databases/shrink-a-database?view=sql-server-2017)

[Shrink a file - Microsoft](https://docs.microsoft.com/en-us/sql/relational-databases/databases/shrink-a-file?view=sql-server-2017)

[Display Data and Log Space Information](https://docs.microsoft.com/en-us/sql/relational-databases/databases/display-data-and-log-space-information-for-a-database?view=sql-server-2017)
