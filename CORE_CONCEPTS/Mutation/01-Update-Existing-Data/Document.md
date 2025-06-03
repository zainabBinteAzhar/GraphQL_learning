# GraphQL Mutation: Update Existing Data

This example demonstrates how to **update existing data** using a GraphQL mutation. Specifically, we'll update a Human’s name in the database and return the updated information to the client.

---

## Mutation Definition

To update a human’s name, we define a mutation field in the schema. The mutation accepts two arguments:

- `id`: the unique identifier of the human
- `name`: the new name to be set

### Schema

```graphql
type Mutation {
  updateHumanName(id: ID!, name: String!): Human
}
```

The mutation returns the updated `Human` object so the client can immediately reflect the changes in the UI.

---

## Operation

We use the `mutation` keyword instead of `query` because we're modifying data.

### GraphQL Mutation

```graphql
mutation UpdateHumanName($id: ID!, $name: String!) {
  updateHumanName(id: $id, name: $name) {
    id
    name
  }
}
```

---

## Variables

```json
{
  "id": "1000",
  "name": "Luke Starkiller"
}
```

---

## Response

```json
{
  "data": {
    "updateHumanName": {
      "id": "1000",
      "name": "Luke Starkiller"
    }
  }
}
```

---

## Purpose

This mutation is used to:

- **Update an existing record** in the backend
- **Return the updated data** so the frontend stays in sync with the backend
- **Encapsulate logic** for updating a resource in a clean, reusable operation

---

## Benefits

- Keeps the client state consistent by returning updated data
- Encourages single-responsibility mutations
- Works seamlessly with typed APIs and UI frameworks

---