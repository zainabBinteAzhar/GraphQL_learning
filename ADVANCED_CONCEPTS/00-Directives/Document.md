# 📘 GraphQL Directives Guide

GraphQL **directives** are annotations used to **modify execution** or **provide metadata** about a field, fragment, or type in a query or schema.

They are **prefixed with `@`** and offer control over **what is included, skipped, deprecated**, or even **loaded incrementally**.

---

## 🧠 What Are Directives?

> Directives are modifiers that allow dynamic behavior in GraphQL queries or schemas — like skipping fields, including them conditionally, or marking them as deprecated.

They can be attached to:
- Fields
- Fragments
- Fragment spreads
- Schema definitions (e.g. types or fields)

---

## ✅ Built-in Directives

### 1. `@include(if: Boolean!)`
Include the field **only if** the condition is true.

```graphql
query GetDog($showBreed: Boolean!) {
  dog {
    name
    breed @include(if: $showBreed)
  }
}
```

---

### 2. `@skip(if: Boolean!)`
**Skip** the field if the condition is true.

```graphql
query GetDog($hideBreed: Boolean!) {
  dog {
    name
    breed @skip(if: $hideBreed)
  }
}
```

> ⚖️ Use `@include` and `@skip` when you want **conditional logic** in your queries.

---

### 3. `@deprecated(reason: String)`
Marks a field or enum as deprecated in the schema.

```graphql
type Dog {
  name: String
  age: Int @deprecated(reason: "Use birthYear instead")
}
```

> 📦 Use in schema files to guide clients to newer fields without breaking older clients.

---

### 4. `@specifiedBy(url: String!)`
Used on custom scalar types to specify an external definition.

```graphql
scalar DateTime @specifiedBy(url: "https://example.com/date-spec")
```

> 🧩 Useful for tools and validators to understand custom scalar behavior.

---

## 🚀 Advanced Directives (Incremental Delivery)

### 5. `@defer` *(Execution Only)*
Defer a field’s result to be delivered later, improving perceived performance.

```graphql
query {
  dog {
    name
    details @defer
  }
}
```

> 📡 Use in large payloads to prioritize what's needed first.

---

### 6. `@stream` *(Execution Only)*
Streams list items as they become available.

```graphql
query {
  dogs @stream {
    name
  }
}
```

> 📬 Use when rendering partial lists faster is better than waiting for full results.

---

## 📋 Summary Table

| Directive         | Purpose                                | Use Case                             |
|------------------|----------------------------------------|--------------------------------------|
| `@include`        | Conditionally **include** field        | Toggle UI or filter data             |
| `@skip`           | Conditionally **skip** field           | Hide optional fields                 |
| `@deprecated`     | Mark a field as **deprecated**         | Schema evolution                     |
| `@specifiedBy`    | Attach **scalar spec URL**             | Custom scalar types                  |
| `@defer`          | **Delay delivery** of a field          | Progressive rendering                |
| `@stream`         | **Stream list** data as it loads       | Infinite scroll or partial results   |

---

## 🧠 Best Practices

- Use `@include`/`@skip` for clean, reusable queries.
- Always add `reason` to `@deprecated` for clarity.
- Use `@defer` and `@stream` in clients that support **incremental delivery** (e.g., Apollo, Relay).
- Avoid overusing directives — keep queries simple when possible.

---