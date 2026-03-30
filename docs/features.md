# Apex Runtime Features & Limits

SquireX includes a secondary capability: a local Apex execution engine for running tests without deploying to a Salesforce org.

> **Note**: The primary capability of SquireX is the Agentforce Capability Scan. The Apex runtime is a secondary feature.

## Mock SObject Store
SquireX provides an in-memory SObject store with pre-configured schemas:

| Object | Supported Fields |
|--------|------------------|
| Account | Name, Industry, Type, BillingCity, AnnualRevenue, NumberOfEmployees |
| Contact | FirstName, LastName, Email, Phone, AccountId |
| Opportunity | Name, Amount, CloseDate, StageName, AccountId |
| Lead | FirstName, LastName, Company, Email, Status |
| Case | Subject, Description, Status, Priority, AccountId, ContactId |
| Task | Subject, Status, Priority, ActivityDate, WhoId, WhatId |
| User | Username, FirstName, LastName, Email, IsActive |

## DML Operations
| Operation | Status | Notes |
|-----------|--------|-------|
| insert | ✅ | Auto-generates Salesforce-style IDs |
| update | ✅ | Requires record with valid Id |
| upsert | ✅ | Supports external ID fields |
| delete | ✅ | Removes records from store |
| merge | ✅ | Combines records, deletes duplicates |

## SOQL Engine
| Feature | Status | Example |
|---------|--------|---------|
| SELECT fields | ✅ | `SELECT Id, Name FROM Account` |
| WHERE clause | ✅ | `WHERE Name = 'Acme'` |
| AND / OR / NOT | ✅ | `WHERE Name = 'X' AND Industry = 'Y'` |
| LIKE operator | ✅ | `WHERE Name LIKE 'Acme%'` |
| IN / NOT IN | ✅ | `WHERE Id IN :idList` |
| NULL check | ✅ | `WHERE Email != null` |
| Bind variables | ✅ | `:variableName` |
| ORDER BY | ✅ | `ORDER BY Name DESC NULLS LAST` |
| LIMIT / OFFSET | ✅ | `LIMIT 100 OFFSET 10` |
| Date literals | ✅ | `TODAY`, `YESTERDAY`, `LAST_N_DAYS:7` |
| COUNT() | ✅ | `SELECT COUNT() FROM Account` |
| GROUP BY | ✅ | `GROUP BY Industry` |
| Subqueries | ❌ | Not yet supported |

## System Methods
| Method | Status |
|--------|--------|
| System.debug() | ✅ |
| System.assert() | ✅ |
| System.assertEquals() | ✅ |
| System.assertNotEquals() | ✅ |
| System.now() / System.today() | ✅ |
| Assert.areEqual() / Assert.areNotEqual() | ✅ |
| Assert.isTrue() / Assert.isFalse() | ✅ |
| Assert.isNull() / Assert.isNotNull() | ✅ |
| Assert.fail() | ✅ |

## Governor Limits
SquireX tracks and enforces governor limits:

| Limit | Synchronous | Asynchronous |
|-------|-------------|--------------|
| SOQL Queries | 100 | 200 |
| SOQL Rows | 50,000 | 50,000 |
| DML Statements | 150 | 150 |
| DML Rows | 10,000 | 10,000 |
| CPU Time | 10,000 ms | 60,000 ms |

## Limitations

The Apex runtime is designed for fast local feedback, not 100% Salesforce parity:

- **No org connection** — all analysis runs entirely offline
- **No triggers** — trigger execution not yet implemented
- **No SOSL** — not yet supported
- **No async execution** — Batch, Queueable, Future run synchronously
- **No sharing rules** — user permissions not enforced in mock runtime

**For full integration testing, deploy to a Salesforce scratch org.**
