# 📘 Writing Data with Mutations

Next to requesting information from a server, the majority of applications also need some way of making **changes to the data** that’s currently stored in the backend. With **GraphQL**, these changes are made using **mutations**.

There are generally **three kinds of mutations**:
- Creating new data
- Updating existing data
- Deleting existing data

## ✍️ Mutation Syntax

Mutations follow the same **syntactical structure as queries**, but they always start with the `mutation` keyword.

---

# ⚖️ REST vs GraphQL: Handling Side Effects

This document explains how **side effects** (like writing to a database, changing a value, sending an email, etc.) are handled differently in **REST** and **GraphQL**, and what the **GraphQL specification** says about where side effects are allowed.

---

## 🔁 In REST

- Any HTTP method can technically modify data.
- But by convention:

  - `GET` should **not** cause any side effects. It's for **reading** data only.
  - `POST`, `PUT`, `DELETE` are used for **writing**, **updating**, or **deleting** data.

> ⚠️ These are **best practices**, not rules enforced by the protocol itself.

---

## 🧠 In GraphQL

- Any **resolver function** in GraphQL could technically modify data (just like a REST handler).
- But the **GraphQL specification** enforces a strict rule:

> 📜 “The resolution of fields other than top-level mutation fields must always be side effect-free and idempotent.”

This means:

- `query` operations and any **nested fields** must **not** change data.
- They should be:
  - **Read-only**
  - **Idempotent** (running them multiple times has the same effect as once)
- Only top-level `mutation` operations are allowed to cause **side effects**, like:
  - Creating a user
  - Sending a message
  - Updating a record

---

## ✅ Why This Matters

- Keeps GraphQL behavior **predictable**
- Makes `query` operations **safe to repeat or cache**
- Centralizes **all data-changing logic** under `mutation` operations

---

## ⚠️ What is a “side effect”?

A side effect means something that **changes the system or the outside world**, like:

- Writing to a database  
- Sending an email or message  
- Modifying a global variable  
- Logging into a file or analytics tool

---

## 🛑 Bad Example (Not Allowed in Query)

Imagine this query:

```graphql
query {
  user(id: "123") {
    name
    logAccessTime  # ❌ This should not be in a query!
  }
}
```

This seems like it reads data — but the logAccessTime field might be doing this:

const logAccessTime = (parent, args) => {
  database.insert({ userId: parent.id, timestamp: Date.now() }) // ❌ writing to DB
  return "Logged"
}

## ✅ Good Example (Spec-Compliant Mutation)

```graphql
mutation {
  logUserAccess(id: "123") {
    status
  }
}
```
# ❓ Why Are Arguments Written Twice in a GraphQL Mutation?

In GraphQL, it might **look like arguments are provided twice** in a mutation, but they serve two **distinct roles**.

---

## 🔁 Example Mutation

```graphql
mutation UpdateHumanName($id: ID!, $name: String!) {
  updateHumanName(id: $id, name: $name) {
    id
    name
  }
}
```

### Part 1: Variable Definitions
($id: ID!, $name: String!) declares the variables the mutation expects as input — like function parameters in JavaScript.

### Part 2: Using Variables
updateHumanName(id: $id, name: $name) passes those variables to the mutation field — like calling a function with arguments.


## ✅ Summary Table

| Operation Type | Can Read? | Can Write (Side Effects)? | Idempotent Required? |
|----------------|-----------|---------------------------|----------------------|
| **query**      | ✅ Yes    | ❌ No                     | ✅ Yes               |
| **mutation**   | ✅ Yes    | ✅ Yes                    | ❌ No                |

---

📘 Following this separation helps keep APIs robust, scalable, and easy to reason about.
