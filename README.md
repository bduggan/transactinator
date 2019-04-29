transactinator
---------------

A little demo of distributed transactions with postgres.

These are a few scripts which

* create two databases with one table each (`credits` and `debits`) with a numeric `amount` column

* insert the same amount into each one

* demonstrate two-phased commit and rollback

## Usage

```
# Init and start two servers on two ports and create dbs
./init-and-start-dbs

# Do a complete distributed insert with commit
./do-distributed-insert

# Start an insert but don't commit
./do-partial-insert
# Note there are no changes
./select-all
# Rollback
./rollback-all-prepared
# Note: still no changes
./select-all

# Try again, this time we will commit
./do-partial-insert
./select-all
# Same as above
./commit-all-prepared
./select-all
# this time we see the changes

# clean up
./stop-and-purge-dbs
```

## Notes

* Prepared transactions are the first phase of two-phased commit.

* `max_prepared_transactions` needs to be set to a nonzero value on both databases (by default it is 0).

* `prepare transaction 'foo'` can fail, just like `commit` can, e.g. this
will throw a contraint violation error when running `prepare transaction`:

```
alter table debits add constraint unique_amt unique (amt) deferrable initially deferred;
insert into debits (amt) values (10);
insert into debits (amt) values (10);
prepare transaction 'foo';
```

## References

* [prepare transaction](https://www.postgresql.org/docs/11/sql-prepare-transaction.html),
[rollback prepared](https://www.postgresql.org/docs/11/sql-rollback-prepared.html), [commit prepared](https://www.postgresql.org/docs/11/sql-commit-prepared.html)

