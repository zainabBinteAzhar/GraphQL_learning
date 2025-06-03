# 🗑️ GraphQL: Deleting Data (Mutations)

In GraphQL, you delete data using a **mutation**, similar to `DELETE` in REST.

---

## Schema Definition

```graphql
type Mutation {
  deleteStarship(id: ID!): ID!
}
```

- `deleteStarship` is a mutation that takes an `ID` and returns the deleted `ID`.

---

## Operation Example

```graphql
mutation DeleteStarship($id: ID!) {
  deleteStarship(id: $id)
}
```

### Variables

```json
{
  "id": "3003"
}
```

### Response

```json
{
  "data": {
    "deleteStarship": "3003"
  }
}
```

---

## 🧠 Key Concepts

- GraphQL knows have separte keywords like DELETE, PUT, PATCH as in REST.
- It uses single mutation keyword.
- Server get's to know about it using it's action namr like here: "deleteStarShip".
- On the backend, this field is connected to a resolver that performs the delete logic.

---
