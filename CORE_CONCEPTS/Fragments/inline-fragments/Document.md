
# GraphQL Inline Fragments

Inline fragments are essential when dealing with GraphQL union types or interfaces, because the actual response object can be one of several types, and you need to safely access type-specific fields.

---

## 🧩 What is an Inline Fragment?

An inline fragment in GraphQL allows you to conditionally include fields based on the runtime type of the object you're querying.

---

## ✅ Syntax of Inline Fragment

```graphql
... on TypeName {
  field1
  field2
}
```

- `...` = fragment  
- `on TypeName` = conditionally applies only if the object is of that type

---

## 🔀 When Do You Use It?

You use inline fragments when querying an interface or a union, because the exact object type returned can vary.

---

## 👇 Example with Interface

Suppose we have this schema:

```graphql
interface Character {
  id: ID!
  name: String!
}

type Human implements Character {
  id: ID!
  name: String!
  height: Float!
}

type Droid implements Character {
  id: ID!
  name: String!
  primaryFunction: String!
}

type Query {
  characters: [Character!]!
}
```

Since `characters` returns a list of `Character` (an interface), you don’t know if each item is a `Human` or `Droid`.

---

### ✅ Query with Inline Fragments

```graphql
query {
  characters {
    id
    name
    ... on Human {
      height
    }
    ... on Droid {
      primaryFunction
    }
  }
}
```

---

### 🧪 Output:

```json
{
  "data": {
    "characters": [
      {
        "id": "1",
        "name": "Luke Skywalker",
        "height": 1.72
      },
      {
        "id": "2",
        "name": "R2-D2",
        "primaryFunction": "Astromech"
      }
    ]
  }
}
```

---

## 🧪 Example with a Union

```graphql
union SearchResult = Human | Droid | Starship

type Query {
  search(text: String!): [SearchResult!]!
}
```

### Query:

```graphql
query {
  search(text: "R2") {
    ... on Human {
      name
      height
    }
    ... on Droid {
      name
      primaryFunction
    }
    ... on Starship {
      model
      name
    }
  }
}
```

---

## 💡 Key Takeaways

| Concept         | Explanation                                       |
|-----------------|-------------------------------------------------|
| Inline Fragment | `... on TypeName { fields }`                     |
| Used for        | Unions and Interfaces                            |
| Why?            | You only get access to type-specific fields inside the matching fragment |
| Alternative     | Named fragments (but inline is easier for small, type-specific queries) |

---