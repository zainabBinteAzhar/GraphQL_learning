# GraphQL: Multiple Fields in Mutations

## Concept

- You can include **multiple mutation fields** in one operation.
- Unlike `query` fields (which run **in parallel**), **mutation fields run in series** (one after another).

---

## Example

```graphql
mutation {
  firstShip: deleteStarship(id: "3001")
  secondShip: deleteStarship(id: "3002")
}
```

### Response:
```json
{
  "data": {
    "firstShip": "3001",
    "secondShip": "3002"
  }
}
```

---

## Why Serial Execution?

- Prevents **race conditions** (e.g., modifying or deleting the same item twice).
- Ensures the **order of operations** is preserved.

---

## ⚠️ Not a Transaction

- If `firstShip` succeeds but `secondShip` fails, GraphQL won’t revert the first one.
- GraphQL **does not support rollback** of successful operations.

---

## Summary

| Feature             | Query Fields | Mutation Fields |
|---------------------|--------------|------------------|
| Execution Order      | Parallel     | Serial (ordered) |
| Rollback on Failure | ❌ No        | ❌ No             |
| Use Case            | Read data    | Modify data      |

```
