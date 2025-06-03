## 📌 What is Validation?

Validation is the step after parsing and before execution in a GraphQL operation. It ensures that:
- Fields queried exist on the correct types.
- The structure of the operation matches the schema.
- Any type-specific rules are respected.

> ✅ A valid GraphQL operation guarantees early error feedback — no runtime logic needed to catch invalid queries.

---

## 🚫 Common Validation Errors

### 1. **Querying Non-Existent Fields**
You can only query fields that are defined on a type.

**❌ Invalid**
```graphql
query {
  hero {
    favoriteSpaceship
  }
}
```

**💥 Error**
```
Cannot query field "favoriteSpaceship" on type "Character".
```

---

### 2. **Missing Selection Set for Non-Scalars**
If a field returns an object type, you must specify subfields.

**❌ Invalid**
```graphql
query {
  hero
}
```

**💥 Error**
```
Field "hero" of type "Character" must have a selection of subfields.
```

---

### 3. **Selection Set on Scalar Fields**
Scalar types like `String` or `Int` cannot have subfields.

**❌ Invalid**
```graphql
query {
  hero {
    name {
      firstCharacterOfName
    }
  }
}
```

**💥 Error**
```
Field "name" must not have a selection since type "String!" has no subfields.
```

---

### 4. **Querying Abstract Types Without Fragments**
Interfaces or unions require **fragments** to access subtype-specific fields.

**❌ Invalid**
```graphql
query {
  hero {
    name
    primaryFunction
  }
}
```

**💥 Error**
```
Cannot query field "primaryFunction" on type "Character". Did you mean to use an inline fragment on "Droid"?
```

**✅ Valid (Named Fragment)**
```graphql
query {
  hero {
    name
    ...DroidFields
  }
}

fragment DroidFields on Droid {
  primaryFunction
}
```

**✅ Valid (Inline Fragment)**
```graphql
query {
  hero {
    name
    ... on Droid {
      primaryFunction
    }
  }
}
```

---

### 5. **Cyclic Fragment Spreads**
Fragments cannot recursively reference themselves.

**❌ Invalid**
```graphql
query {
  hero {
    ...NameAndAppearancesAndFriends
  }
}

fragment NameAndAppearancesAndFriends on Character {
  name
  appearsIn
  friends {
    ...NameAndAppearancesAndFriends
  }
}
```

**💥 Error**
```
Cannot spread fragment "NameAndAppearancesAndFriends" within itself.
```

---

## ✅ Valid Example Using Fragments
```graphql
query {
  hero {
    ...NameAndAppearances
    friends {
      ...NameAndAppearances
      friends {
        ...NameAndAppearances
      }
    }
  }
}

fragment NameAndAppearances on Character {
  name
  appearsIn
}
```

---

## 📤 Validation Error Responses

When the server detects a validation error:
- Execution **does not occur**.
- Response includes only an `errors` array with details.
- No partial `data` is returned.

**Example Response**
```json
{
  "errors": [
    {
      "message": "Cannot query field \"favoriteSpaceship\" on type \"Character\".",
      "locations": [
        { "line": 4, "column": 5 }
      ]
    }
  ]
}
```
---

## 🧠 Summary

| Rule | Error Type | Fix |
|------|------------|-----|
| Query non-existent field | Schema violation | Use only defined fields |
| Omit subfields for object types | Structural | Add selection set |
| Add subfields to scalar | Structural | Remove subfields |
| Query subtype fields without fragment | Type error | Use fragments |
| Recursive fragment spread | Cycle error | Avoid self-referencing |
