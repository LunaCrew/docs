# Schemas

### Definition

A schema is a JSON object that defines the structure and content of your data. Schemas represent data types rather than specific values.

### Types

One of the functions of the schema is to define the type of data that will be inserted in a given field. In our projects we use the [**Joi**](https://joi.dev/) library to validate data.

### Example

The `noteSchema` object is defined. This object has four properties: `userId`, `createdAt`, `updatedAt` and `note`.

* `userId` is defined as a `string` that must follow the format of a **UUID version 4**. This is applied by the `guid({ version: 'uuidv4' })` method. The `require()` method indicates that this property must be provided when creating or updating a note.
* `createAt` is a `string` that defaults to the current date and time in **ISO** format if not provided. This is achieved by calling `new Date().toISOString()` inside the `default()` method.
* `updateAt` is also a `string`, but defaults to null if not provided. This property will be updated manually whenever the note is modified.
* `note` is a `string` that can contain up to 2,000 characters. The `max(2000)` method imposes this limit. Just like `userId`, this property is mandatory.

Finally, noteSchema is exported as this module's default export, allowing it to be imported and used in other parts of the application. This schema can be used to validate note objects before they are stored in the database, ensuring they have the correct structure and types.

```typescript
import joi from 'joi'

const noteSchema = joi.object({
   userId: joi.string().guid({ version: 'uuidv4' }).required(),
   createdAt: joi.string().default(new Date().toISOString()),
   updatedAt: joi.string().default(null),
   note: joi.string().max(2000).required()
})

export default noteSchema
```
