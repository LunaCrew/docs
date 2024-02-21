# Types

These types are used to impose a certain structure on data throughout the codebase, making the code more predictable and less error-prone.

#### Examples of types

```typescript
/**
  * Represents a user service.
  * Can be an object, null or undefined.
  */
export type UserService = object | null | undefined

/**
  * Represents the result of creating a document.
  * @template T – The type of the document.
  */
export type CreateDocument = InsertOneResult<Document>
```
