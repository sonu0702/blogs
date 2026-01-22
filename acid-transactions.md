---
title: "ACID Transactions"
date: "2024-09-07"
description: "A transaction is a group of SQL commands whose results will be made available to the rest of the system as a unit when the transaction commits or not at all, if the transaction aborts."
tags: ["systemdesign", "webdev", "performance" , "learning"]
---

### ACID Transactions

What is a Transaction any way ?

A transaction is a group of SQL commands whose results will be made available to the rest of the system as a unit when the transaction commits or not at all, if the transaction aborts.

Transactions are expected to be ACID.

ACID, an acronym for Atomicity, Consistency, Isolation and Durability defines a set of properties that ensure database transactions are processed reliably.

ACID is most often associated with transactions on a single database server, but `distributed transactions` extend that guarantee across multiple databases. 

I found a good article on distributed transaction and it implementation  - [https://hazelcast.com/glossary/distributed-transaction](https://hazelcast.com/glossary/distributed-transaction/)

Some databases may not support distributed transactions like postgresql do not support distributed transactions.

### Can you nest transactions ?

I would suggest read this article by Brent Ozar - [https://www.brentozar.com/archive/2023/02/can-you-nest-transactions-in-sql-server/](https://www.brentozar.com/archive/2023/02/can-you-nest-transactions-in-sql-server/)

### Let’s see ACID properties one by one.

1. **Atomicity**: Atomicity ensures set of transaction is treated as an individual unit of work. It means either all the operations are successful or none of them are applied to the database.
    
    If any of part of the transaction fails, the entire transaction is rolled back, and the database is restored to its previous consistent state.
    
    Example : In a banking transaction Atomicity ensures either both credit and debit operation occur, or neither does.
    
    ```sql
    BEGIN TRANSACTION;
    UPDATE accounts SET balance = balance - 100 where account_id=1;
    UPDARE accounts SET balance = balance + 100 where account_id=2;
    COMMIT;
    ```
    
2. **Consistency**: Consistency ensures that a transactions brings the database from one valid state to another valid state. It means all the data integrity constraints such as foreign key, check constraints are satisfied before and after the transactions. 
3. **Isolation**: Isolation ensures that transactions are executed in a way that they do not interfere with each other means the intermediate state of one transaction is invisible to another parallel transaction.
    
    #### Isolation levels:
    
    **Read Uncommitted**: Transactions can see uncommitted changes from other transactions.
    
    **Read Committed**: Transactions can only see committed changes from other transactions.
    
    **Repeatable Read**: Ensures that if a transactions read a row, it will read the same row consistently during its entire execution.
    
    **Serializable**: Provides the highest level of isolation, ensuring transactions are executed serially.
    
    ```sql
    -- Check Isolation Level in Postgresql
    
    SHOW default_transaction_isolation;
    
    -- output : `read committed`
    ```
    
    Here is good read on ACID implementation in Posgresql - https://www.postgresql.org/files/developer/transactions.pdf
    
4. **Durability**: it ensures that once a transaction is committed, it will remains so, even in the event of a system failure. This means that committed data will not be lost.

ACID transaction has 4 phases `Begin Transaction` , `Execute Transaciton` , `Commit Transaction` , `Rollback Transaction`

### Databases implement ACID transactions through a combination of techniques, including

1. **Logging**: Detailed records of all the transactions are kept, allowing for recovery in case of failure.
2. **Locking**: Data is locked during a transaction to prevent concurrent access and ensure isolation.
3. **Tow-Phase Commit**: A protocol used to coordinate the commitment of a transaction across multiple systems.

There are lot of techniques to implement transactions and use cases where transactions are used. At present it is a fundamental concept in database management systems that ensure data integrity, reliability and consistency.

By understanding and leveraging ACID transactions, developers can deliver a reliable and predictable applications.

---

Thank you so much for reading. ❤️