# GraphQL: Adding New Data (Mutations)

## Concept Overview

In GraphQL, **mutations** are used to add, update, or delete data on the server. This is similar to `POST`, `PUT`, and `DELETE` in REST APIs — but with more structured control and predictability.

---

## REST vs GraphQL

| Feature | REST | GraphQL |
|--------|------|---------|
| Adding data | `POST` to a URL like `/reviews` | `mutation { createReview(...) }` |
| Sending data | JSON body in the request | Structured `input` objects |
| Output | Whole new response | You decide exactly what to return |

---

## Schema Definition

Here’s how we define the types for our mutation in the GraphQL schema:

```graphql
enum Episode {
  NEWHOPE
  EMPIRE
  JEDI
}

input ReviewInput {
  stars: Int!
  commentary: String
}

type Review {
  stars: Int!
  commentary: String
}

type Mutation {
  createReview(episode: Episode!, review: ReviewInput!): Review
}
```

### Notes:
- `Episode` is an **enum**, like a dropdown with allowed values.
- `ReviewInput` is an **input object type**, used to pass structured arguments.
- `Review` is an **output object type**, used to return the created review.
- `createReview` is a **mutation field** defined under the root `Mutation` type.

---

## Mutation Operation (Client Side)

This is how a client (e.g., React Native app) sends data:

```graphql
mutation CreateReviewForEpisode($ep: Episode!, $review: ReviewInput!) {
  createReview(episode: $ep, review: $review) {
    stars
    commentary
  }
}
```

### With Variables:

```json
{
  "ep": "JEDI",
  "review": {
    "stars": 5,
    "commentary": "This is a great movie!"
  }
}
```

### Expected Response:

```json
{
  "data": {
    "createReview": {
      "stars": 5,
      "commentary": "This is a great movie!"
    }
  }
}
```

---

## What You Learned

### ✔ `Mutation` returns an object — just like a query — and you can pick what fields to return.

### ✔ `ReviewInput` is for input only. It **cannot** be used as an output. That’s why we define a separate `Review` type to return data.

### ✔ You **define** input types in the schema, but you **pass** values in the mutation operation.

---

## ⚠️ Common Confusions Explained

### ❓ Why is `Review` used instead of `ReviewInput` for return?

- `ReviewInput` is only for **input**. GraphQL does not allow input types to be returned.
- `Review` is a regular object type that can be returned with fields you specify.

### ❓ What does this mean?

```graphql
mutation CreateReviewForEpisode($ep: Episode!, $review: ReviewInput!) {
```

- You're **declaring dynamic variables**: `$ep` and `$review`.
- You use these variables below:

```graphql
createReview(episode: $ep, review: $review)
```

This is similar to writing:

```js
function CreateReview(ep, review) {
  return createReview({ episode: ep, review });
}
```

### ❓ What’s the difference between `episode: $ep` vs `$ep: Episode!`?

- `$ep: Episode!` — Declaring the variable type
- `episode: $ep` — Using the variable in your mutation call

---

## ❌ Wrong Schema Example

```graphql
type Mutation {
  createReview( episode: JEDI, review: { stars: 5, commentary: "Awesome!" }): Review
}
```

You cannot pass actual values inside the schema. That’s only allowed inside a client-side mutation operation.

---

## ✅ Correct Execution Example

Here’s a valid mutation request you send from client:

```graphql
mutation {
  createReview(
    episode: JEDI,
    review: { stars: 5, commentary: "Awesome!" }
  ) {
    stars
    commentary
  }
}
```

---

## Backend Resolver Example (Node.js)

If you're using Node.js with Apollo Server, your mutation resolver might look like this:

```js
const resolvers = {
  Mutation: {
    createReview: (parent, args, context) => {
      const { episode, review } = args;
      return context.db
        .createNewReview(episode, review)
        .then((data) => ({
          stars: data.stars,
          commentary: data.commentary,
        }));
    },
  },
};
```

---

## Final Tips

- Define schema carefully: never pass values in the schema.
- Always return a dedicated object type like `Review`, not `ReviewInput`.
- Client-side variables = declared at the top, used in the mutation.
- Use GraphQL Playground or tools like Apollo Client to test mutations interactively.

---

## Summary Table

| Operation Type | Can Read? | Can Write (Side Effects)? | Idempotent Required? |
|----------------|-----------|----------------------------|------------------------|
| `query`        | ✅ Yes    | ❌ No                     | ✅ Yes                |
| `mutation`     | ✅ Yes    | ✅ Yes                    | ❌ No                 |
