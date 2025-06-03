# GraphQL Validation Rules 

This guide summarizes GraphQL validation rules based on the official GraphQL Specification, mini but essential checks for the graphql query.

---

## 1 Operation Definitions

### 1.1 Operation Name Uniqueness
Each operation in a document must have a unique name.

**✅ Valid**
```graphql
query GetDogName { dog { name } }
query GetDogBarkVolume { dog { barkVolume } }
```

**❌ Invalid**
```graphql
query GetDog { dog { name } }
query GetDog { dog { barkVolume } }
```

---

## 2 Selection Sets

### 2.1 Field Selections on Objects, Interfaces, and Unions
You can only select fields that exist on the returned type.

**✅ Valid**
```graphql
{
  dog {
    name
  }
}
```

**❌ Invalid**
```graphql
{
  dog {
    barkVolume {
      value
    }
  }
}
```
_(Assuming `barkVolume` is a scalar, not an object)_

### 2.2 Leaf Field Selections
Scalar fields (leaf fields) cannot have sub-selections.

**✅ Valid**
```graphql
{
  dog {
    name
  }
}
```

**❌ Invalid**
```graphql
{
  dog {
    name {
      firstLetter
    }
  }
}
```

### 2.3 Field Selection Merging
All fields with the same response name must be unifiable (same return type and arguments).

**✅ Valid**
```graphql
{
  dog {
    name
  }
  dog {
    name
  }
}
```

**❌ Invalid**
```graphql
{
  dog {
    name
  }
  dog {
    name(locale: "fr")
  }
}
```

### TO REMEMBER: Merging Same Fields with Different Arguments

- GraphQL merges fields with the **same response name** automatically.
- This means if you write the same field multiple times without differences, it runs only **once**.

✅ Valid:
```graphql
{
  dog {
    name
    name
  }
}
```
> ✅ Returns `name` only once.

❌ Invalid:
```graphql
{
  dog {
    name(locale: "en")
    name(locale: "fr")
  }
}
```
> ❌ Fails because same field name (`name`) used with different arguments.

---

### ✅ Solution: Use Aliases

Use **aliases** to give unique names to each version of the field:

```graphql
{
  dog {
    englishName: name(locale: "en")
    frenchName: name(locale: "fr")
  }
}
```

This works perfectly and gives a clear JSON response:

```json
{
  "dog": {
    "englishName": "Max",
    "frenchName": "Maxime"
  }
}
```


---

## 3 Arguments

### 3.1 Argument Names Are Unique
Arguments passed to a field or directive must be uniquely named.

**✅ Valid**
```graphql
{
  dog {
    isHouseTrained(atOtherHomes: true)
  }
}
```

**❌ Invalid**
```graphql
{
  dog {
    isHouseTrained(atOtherHomes: true, atOtherHomes: false)
  }
}
```

---

## 4 Variables

### 4.1 Variable Uniqueness
Variable definitions in an operation must be unique.

**✅ Valid**
```graphql
query GetDog($a: Boolean, $b: Int) {
  dog {
    name
  }
}
```

**❌ Invalid**
```graphql
query GetDog($a: Boolean, $a: Int) {
  dog {
    name
  }
}
```

### 4.2 All Variables Are Used
All defined variables in an operation must be used somewhere.

**✅ Valid**
```graphql
query GetDog($showName: Boolean) {
  dog {
    name @include(if: $showName)
  }
}
```

**❌ Invalid**
```graphql
query GetDog($showName: Boolean) {
  dog {
    name
  }
}
```

### 4.3 All Used Variables Are Defined
All variables used in an operation must be defined.

**✅ Valid**
```graphql
query GetDog($if: Boolean) {
  dog {
    name @include(if: $if)
  }
}
```

**❌ Invalid**
```graphql
query GetDog {
  dog {
    name @include(if: $if)
  }
}
```

### 4.4 Variable Types Must Be Input Types
Variables must use input types (scalars, enums, or input objects), not output types.

**✅ Valid**
```graphql
query GetDog($input: Boolean) {
  dog {
    name @include(if: $input)
  }
}
```

**❌ Invalid**
```graphql
query GetDog($input: Dog) {
  dog {
    name
  }
}
```

### 4.5 Variable Default Values Must Match Type
Default values of variables must be the correct type.

**✅ Valid**
```graphql
query GetDog($age: Int = 3) {
  dog {
    age
  }
}
```

**❌ Invalid**
```graphql
query GetDog($age: Int = "three") {
  dog {
    age
  }
}
```

---

## 5 Fragments

### 5.1 Fragment Name Uniqueness
Fragment definitions must have unique names.

**✅ Valid**
```graphql
fragment A on Dog { name }
fragment B on Dog { barkVolume }
```

**❌ Invalid**
```graphql
fragment A on Dog { name }
fragment A on Dog { barkVolume }
```

### 5.2 Fragment Spread Type Existence
Fragments must be defined on existing types.

**✅ Valid**
```graphql
fragment DogInfo on Dog {
  name
}
```

**❌ Invalid**
```graphql
fragment InvalidType on NotAType {
  name
}
```

### 5.3 Fragments on Composite Types
Fragments can only be defined on objects, interfaces, or unions (composite types).

**✅ Valid**
```graphql
fragment PetFields on Pet {
  name
}
```

**❌ Invalid**
```graphql
fragment NameFields on String {
  length
}
```

### 5.4 Fragment Spread Is Possible
A fragment spread must be possible based on the type condition and actual type.

**✅ Valid**
```graphql
fragment PetFields on Pet {
  name
}

query {
  dog {
    ...PetFields
  }
}
```

**❌ Invalid**
```graphql
fragment HumanFields on Human {
  totalPets
}

query {
  dog {
    ...HumanFields
  }
}
```

### 5.5 Fragment Spreads Must Not Form Cycles
Fragments must not create circular references.

**✅ Valid**
```graphql
fragment A on Dog {
  ...B
}
fragment B on Dog {
  name
}
```

**❌ Invalid**
```graphql
fragment A on Dog {
  ...B
}
fragment B on Dog {
  ...A
}
```

### TO REMEMBER: **Fragments must not form cycles** 
— meaning no fragment can end up referencing itself, directly or indirectly.

---

### ✅ What’s Allowed
```graphql
fragment A on Dog {
  ...B
}
fragment B on Dog {
  name
}
```

---

### ❌ What’s Not Allowed (Cycles)

1. **Self-reference:**
   ```graphql
   fragment A on Dog {
     ...A
   }
   ```

2. **Mutual reference:**
   ```graphql
   fragment A on Dog {
     ...B
   }
   fragment B on Dog {
     ...A
   }
   ```

3. **Indirect cycle:**
   ```graphql
   fragment A on Dog {
     ...B
   }
   fragment B on Dog {
     ...C
   }
   fragment C on Dog {
     ...A
   }
   ```

---

### Reminder

- Using `...FragmentName` inside operations like:
  ```graphql
  query {
    dog {
      ...DogDetails
    }
  }
  fragment DogDetails on Dog {
    name
    breed
  }
  ```
  ✅ **is valid** — as long as no cycles are formed inside the fragment definitions.



---

## 6 Input Value Validation

### 6.1 Argument Uniqueness in Object Fields
Input object fields must have unique keys.

**✅ Valid**
```graphql
{
  createUser(input: { name: "Zainab", age: 25 })
}
```

**❌ Invalid**
```graphql
{
  createUser(input: { name: "Zainab", name: "Zee" })
}
```

### 6.2 Input Fields Must Be Defined
You must not provide input fields that are not defined in the schema.

**✅ Valid**
```graphql
{
  createUser(input: { name: "Zainab" })
}
```

**❌ Invalid**
```graphql
{
  createUser(input: { nickname: "Zee" })
}
```

---

## 7 Directives

### 7.1 Directives Are Defined
Only directives defined in the schema can be used.

**✅ Valid**
```graphql
{
  dog {
    name @include(if: true)
  }
}
```

**❌ Invalid**
```graphql
{
  dog {
    name @foo(if: true)
  }
}
```

### 7.2 Directive Locations Are Valid
Directives can only be used in allowed locations.

**✅ Valid**
```graphql
{
  dog {
    name @include(if: true)
  }
}
```

**❌ Invalid**
```graphql
fragment invalid on Dog @include(if: true) {
  name
}
```

---

## 8 Variables in Fragments

### 8.1 Variable Usage in Fragment Must Be Allowed by Operations
Fragments must not use variables unless they are defined in the operation.

**✅ Valid**
```graphql
query GetDog($if: Boolean) {
  ...DogName
}

fragment DogName on Dog {
  name @include(if: $if)
}
```

**❌ Invalid**
```graphql
fragment DogName on Dog {
  name @include(if: $if)
}

query GetDog {
  ...DogName
}
```

---
