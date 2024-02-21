# Controllers

_Controllers_ are responsible for executing HTTP operations. Each API has its own _controller_. See how a user _controller_ operation works `src/controller/UserController.ts`.

### Step by step

1. The method takes three parameters: `req` (the request object), `res` (the response object), and `next` (a function to call the next middleware).
2. It starts with a `try-catch` block to handle any errors that may occur during code execution.
3. Inside the `try` block, it calls `CreateUserService.execute(req, res, next)` to create a new user. If no user is created, the function returns immediately.
4. If a user is created, it will attempt to insert it into the database users collection using `Collections.users.insertOne(user)`. This returns a `Promise` that resolves to a `CreateDocument` object, which contains information about the operation.
5. If the insertion is successful (that is, the result is true), it sends a response with the HTTP status code `201 (Created)` and a JSON object containing the ID of the inserted user.
6. If the insertion is unsuccessful, it sends a response with HTTP status code `500 (Internal Server Error)` and logs the error.
7. After the operation, it logs an informational message indicating that the `CreateUser` endpoint has been called.
8. In the `catch` block, if an error occurs during code execution, it records the error, sends a response with the HTTP status code `500 (Internal Server Error)` and calls the next function with the error.

### Example

{% code overflow="wrap" %}
```typescript
import { NextFunction, Request, Response } from 'express'
import { collections } from '../app'
import { CreateDocument } from '../types/Document'
import { UserService } from '../types/Service'
import HttpStatusCode from '../util/enum/HttpStatusCode'
import CreateUserService from '../service/CreateUserService'
import Logger from '../util/log/Logger'

export default class UserController {
  public static readonly createUser = async (req: Request, res: Response, next: NextFunction): Promise<void> => {
    try {
      const user: UserService = CreateUserService.execute(req, res, next)
      if (!user) return

      const result: CreateDocument = await collections.users.insertOne(user)

      if (result) {
        res.status(HttpStatusCode.CREATED).json({ id: result.insertedId })
      } else {
        res.status(HttpStatusCode.INTERNAL_SERVER_ERROR).json(
          ParserError.http(HttpStatusCode.INTERNAL_SERVER_ERROR, 'internal error')
        )
        Logger.error(':: Controller :: UserController :: CreateUser ::', result)
      }
      Logger.info(':: Calling Endpoint :: CreateUser ::')
    } catch (error) {
      Logger.error(':: Controller :: UserController :: CreateUser ::', error)
      res.status(HttpStatusCode.INTERNAL_SERVER_ERROR).json(
        ParserError.http(HttpStatusCode.INTERNAL_SERVER_ERROR, 'An error occurred')
      )
      next(error)
    }
  }
}
```
{% endcode %}
