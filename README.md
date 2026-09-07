# MVCC Engine - ACID-Compliant Transaction Manager

A multi-version concurrency control (MVCC) database engine in C++ implementing all four SQL standard isolation levels with proper ACID guarantees.

## Architecture

```
+------------------+     +------------------+     +------------------+
| TransactionManager|     | TransactionManager|     | TransactionManager|
| (T1, SERIALIZABLE)|     | (T2, READ_COMMIT)|     | (T3, REPEAT_READ)|
+--------+---------+     +--------+---------+     +--------+---------+
         |                        |                        |
         +------------------------+------------------------+
                                  |
                    +-------------v-------------+
                    |       Database            |
                    |  (In-Memory KV Store)     |
                    |                           |
                    |  data: map<int,           |
                    |    Database_Struct>       |
                    |                           |
                    |  Database_Struct:         |
                    |    - recent_commit        |
                    |    - recent_write (deque) |
                    |    - commit_value (map)   |
                    +---------------------------+
                                  |
                    +-------------v-------------+
                    |    Garbage Collector      |
                    |    (Background Thread)    |
                    +---------------------------+
```

## Isolation Levels

| Level | Dirty Reads | Non-Repeatable Reads | Phantom Reads |
|-------|-------------|---------------------|---------------|
| READ_UNCOMMITTED | Possible | Possible | Possible |
| READ_COMMITTED | Prevented | Possible | Possible |
| REPEATABLE_READ | Prevented | Prevented | Possible |
| SERIALIZABLE | Prevented | Prevented | Prevented |

## Key Features

- **MVCC** - Multiple versions per key, tagged by transaction ID
- **Snapshot Isolation** - REPEATABLE_READ captures snapshot at transaction start
- **Optimistic Concurrency** - READ_COMMITTED detects write-write conflicts at commit
- **Write Buffering** - Writes buffered locally, flushed to DB at commit
- **Garbage Collection** - Background thread cleans old versions while preserving active snapshots
- **RAII Rollback** - Destructor auto-rolls back uncommitted transactions

## Build & Run

```bash
make run      # Build with cmake
make exec     # Run the executable
```

## Tech Stack

C++17, STL (map, deque, set, mutex, thread), CMake
