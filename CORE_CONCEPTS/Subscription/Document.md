# GraphQL Subscriptions – Real-Time Data with Ease

GraphQL Subscriptions enable **real-time updates** from your server to your client. Unlike queries (read) and mutations (write), subscriptions use a **persistent connection** (typically via WebSocket) to push updates as events occur.

---

## What Is a Subscription?

A **subscription** is a GraphQL operation that allows the server to **send data to clients when a specific event happens**, like a new review being posted.

```graphql
subscription NewReviewCreated {
  reviewCreated {
    rating
    commentary
  }
}
```

---

## How It Works

1. **Client subscribes** using a WebSocket connection.
2. **Server listens** to a pub/sub event (e.g. `REVIEW_CREATED`).
3. **Event occurs** (e.g. via mutation like `createReview`).
4. **Server publishes** data to all subscribers.
5. **Client receives** live updates automatically.

---

## Example Schema

```graphql
type Subscription {
  reviewCreated: Review
}
```

---

## Rules to Remember

- Only **one root field** per subscription operation.
- Use **named operations** when writing multiple subscriptions in one document.
- The transport protocol (WebSocket, SSE) is **not defined by GraphQL**—you must choose one.

---

## Subscriptions vs Live Queries

| Feature       | Subscriptions                 | Live Queries                      |
|---------------|-------------------------------|------------------------------------|
| Triggered by  | Events (e.g. new review)      | Any data change                   |
| Connection    | Long-lived (e.g. WebSocket)   | Typically polling or cache-based  |
| Spec Status   | Official                      | Experimental/varies by library    |

---

## Summary

GraphQL Subscriptions let you build **interactive, live features** by reacting to backend events in real-time—perfect for chats, notifications, dashboards, etc.

```graphql
# One operation per subscription
subscription {
  reviewCreated {
    rating
  }
}
```

Can be implemnented using libraries like **Apollo Server**, **graphql-ws**, or **subscriptions-transport-ws** with your favorite backend.

---
## 🧠 Simplest Explanation of "Using Subscriptions at Scale"

GraphQL subscriptions are powerful for real-time updates, but harder to scale than queries or mutations because:

- The server must **keep each client’s connection and data context alive**.
- **Scaling across servers is tricky** — clients need to stick to the same server instance.
- Clients need **advanced logic** to handle dropped connections or data races.
- The whole setup needs **careful architecture** (WebSockets, pub/sub, load balancing, etc.).

🟢 **Best for** fast-changing data like chats, notifications.  
🔴 **Not ideal for** rarely changing data — use polling or re-fetching instead.

