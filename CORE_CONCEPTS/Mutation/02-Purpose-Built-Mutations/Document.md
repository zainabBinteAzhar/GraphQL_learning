# Purpose-Built Mutations in GraphQL

## Overview

Unlike REST APIs that use generalized endpoints (e.g., PATCH `/human`), GraphQL supports **purpose-built mutations**. Instead of one generic `updateHuman` mutation, you can define specific mutations like `updateHumanName` tailored to precise tasks.

## Benefits

- **Expressive schema:** Purpose-built fields can use **Non-Null input types**, avoiding many nullable arguments.
- **Simplified validation:** Schema enforces required inputs, reducing runtime checks.
- **Better data relationships:** GraphQL lets you model complex associations that don’t fit simple CRUD, such as associating user ratings with films.

## Example Mutation

```graphql
mutation RateFilm($episode: Episode!, $rating: FilmRating!) {
  rateFilm(episode: $episode, rating: $rating) {
    episode
    viewerRating
  }
}
```

## Variables
```json
{
  "episode": "EMPIRE",
  "rating": "THUMBS_UP"
}
```
## Response
```json
{
  "data": {
    "rateFilm": {
      "episode": "EMPIRE",
      "viewerRating": "THUMBS_UP"
    }
  }
}

```
# Mutation Types: Generic vs Purpose-Built

## Overview

- **Generic Mutation:**  
  A single mutation updating multiple fields with mostly optional inputs. Server handles which fields to update.

- **Purpose-Built Mutations:**  
  Multiple focused mutations, each updating specific fields with required inputs. Clearer and simpler server logic.

## Differences

| Aspect          | Generic Mutation          | Purpose-Built Mutation       |
|-----------------|--------------------------|-----------------------------|
| Mutations Count | One                      | Multiple                    |
| Input Fields    | Mostly optional (nullable) | Required (Non-Null)          |
| Server Logic    | Complex checks           | Simple, focused action       |
| API Clarity     | Lower                    | Higher                      |
| Validation      | Runtime                  | Schema enforced             |

## Summary

Purpose-built mutations make GraphQL APIs clearer, easier to maintain, and validate.
