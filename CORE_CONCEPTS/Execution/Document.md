# GraphQL Execution Explained

This guide helps you understand how GraphQL executes a query and provides data for requested fields, including field resolution, type systems, and async behavior.

---

## 📌 Overview

Once a GraphQL document is parsed and validated, the **execution phase** begins. During this phase:

- GraphQL resolves fields in the query.
- Each field maps to a function called a **resolver**.
- The structure of the result **mirrors the query**.

---

## 🔧 Example Type System

```graphql
type Query {
  human(id: ID!): Human
}

type Human {
  name: String
  appearsIn: [Episode]
  starships: [Starship]
}

enum Episode {
  NEWHOPE
  EMPIRE
  JEDI
}

type Starship {
  name: String
}
```

---

## 📤 Sample Query

```graphql
query {
  human(id: 1002) {
    name
    appearsIn
    starships {
      name
    }
  }
}
```

### 🧾 Response

```json
{
  "data": {
    "human": {
      "name": "Han Solo",
      "appearsIn": [
        "NEWHOPE",
        "EMPIRE",
        "JEDI"
      ],
      "starships": [
        {
          "name": "Millenium Falcon"
        },
        {
          "name": "Imperial shuttle"
        }
      ]
    }
  }
}
```

---

## 🔄 Execution Breakdown

### 🔹 Fields as Functions

Each field is treated like a function:

- Scalars end the resolution.
- Objects trigger further field resolution.
- Always ends in **Scalar or Enum** types.

---

## 🧠 Root Fields & Resolvers

- The top-level `Query` type is the **entry point**.
- Fields like `human(id: ID!)` have resolver functions.

```js
function Query_human(obj, args, context, info) {
  return context.db.loadHumanByID(args.id).then(
    userData => new Human(userData)
  );
}
```

#### Resolver Parameters

- `obj`: The previous result (often unused in root).
- `args`: Arguments passed to the field.
- `context`: Shared object (user, DB, etc).
- `info`: Metadata about the field.

---

## ⏱ Asynchronous Resolvers

```js
function Query_human(obj, args, context, info) {
  return context.db.loadHumanByID(args.id).then(
    userData => new Human(userData)
  );
}
```

- Resolves asynchronously using **Promises** (or Futures/Tasks in other languages).
- GraphQL automatically waits for async values **before proceeding**.

---

## ⚙️ Trivial Resolvers

```js
function Human_name(obj, args, context, info) {
  return obj.name;
}
```

- Simple fields can auto-resolve based on the object’s property.
- Many libraries omit these if a default name match exists.

---

## 🔄 Scalar Coercion

```js
Human: {
  appearsIn(obj) {
    return obj.appearsIn; // [4, 5, 6]
  }
}
```

- Internally uses `4, 5, 6` but coerces them to `"NEWHOPE"`, etc.
- **GraphQL enforces type system contracts** via coercion.

---

## 📚 List Resolvers

```js
function Human_starships(obj, args, context, info) {
  return obj.starshipIDs.map(id =>
    context.db.loadStarshipByID(id).then(
      shipData => new Starship(shipData)
    )
  );
}
```

- Resolver returns **list of Promises**.
- GraphQL resolves all concurrently and recursively processes children (`name` in `starships`).

---

## 📦 Producing Final Result

- Every field resolution adds to the result map.
- Field names (or aliases) become keys.
- Result structure **matches query**.
- Delivered as JSON to client.

---

## 🧪 Final Recap

```graphql
query {
  human(id: 1002) {
    name
    appearsIn
    starships {
      name
    }
  }
}
```

### Result

```json
{
  "data": {
    "human": {
      "name": "Han Solo",
      "appearsIn": [
        "NEWHOPE",
        "EMPIRE",
        "JEDI"
      ],
      "starships": [
        {
          "name": "Millenium Falcon"
        },
        {
          "name": "Imperial shuttle"
        }
      ]
    }
  }
}
```

---

## ✅ Summary

| Concept              | Description |
|----------------------|-------------|
| **Type System**       | Drives field resolution logic |
| **Resolvers**         | Functions to fetch data |
| **Scalars/Enums**     | Endpoints of a query |
| **Async Handling**    | Promises/Futures supported |
| **Coercion**          | Adapts raw values to expected types |
| **Execution Tree**    | Recursively builds result based on selection set |

---
