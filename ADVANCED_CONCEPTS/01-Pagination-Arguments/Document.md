# GraphQL Pagination Keywords: `first`, `last`, `after`, `before`

GraphQL doesn't have built-in pagination logic, but the **Connection Pattern** is widely used for efficient pagination. This is where `first`, `last`, `after`, and `before` come in.

## 📌 What Are They?

| Argument | Purpose |
|----------|---------|
| `first`  | Get the **first N items** after a cursor (forward pagination) |
| `last`   | Get the **last N items** before a cursor (backward pagination) |
| `after`  | Skip items **after this cursor** (used with `first`) |
| `before` | Skip items **before this cursor** (used with `last`) |

---

## 🧪 Examples

### 1. Fetch the First 5 Items

```graphql
{
  posts(first: 5) {
    edges {
      node {
        id
        title
      }
    }
    pageInfo {
      endCursor
      hasNextPage
    }
  }
}
```

### 2. Fetch the Next 5 Items After a Cursor

```graphql
{
  posts(first: 5, after: "cursor123") {
    edges {
      node {
        id
        title
      }
    }
    pageInfo {
      endCursor
      hasNextPage
    }
  }
}
```

### 3. Fetch the Last 5 Items (from the end)

```graphql
{
  posts(last: 5) {
    edges {
      node {
        id
        title
      }
    }
    pageInfo {
      startCursor
      hasPreviousPage
    }
  }
}
```

### 4. Fetch the Previous 5 Items Before a Cursor

```graphql
{
  posts(last: 5, before: "cursor987") {
    edges {
      node {
        id
        title
      }
    }
    pageInfo {
      startCursor
      hasPreviousPage
    }
  }
}
```

---

## 💡 Common Use in Fragments

You might see these arguments inside **fragments** too when paginating nested connections:

```graphql
fragment CommentsSection on Post {
  comments(first: 10, after: $afterCursor) {
    edges {
      node {
        author
        content
      }
    }
    pageInfo {
      endCursor
      hasNextPage
    }
  }
}
```
---

## ✅ Summary

- Use `first`/`after` to go **forward** through a list.
- Use `last`/`before` to go **backward** through a list.

---