# Services

The _services_ are responsible for applying the business rules and performing the necessary validations before the request in controller. Each operation has its own _service_.

Let's see how `src/service/CreateUserService.ts` works.

### Step by step

The class has two methods: `execute` and `_buildQuery`.

#### **execute()**

`execute(req: Request, res: Response, next: NextFunction): UserService`

This is a static method that takes three parameters: `req` (the HTTP request), `res` (the HTTP response), and `next` (a callback function to pass control to the next middleware function).& #x20;

This method validates the request body against a predefined schema (`userSchema`). If validation fails, it sends a `BAD_REQUEST` HTTP status code with the error details and logs the error. If the validation passes, it will call the `_buildQuery` method with the validated data.

#### **\_buildQuery()**

`_buildQuery(user: User): object`

This is a private static method that takes a `User` object, assigns a new `UUID` to the `_id` field, encrypts the password using the `Password.encrypt` method, and returns the updated user object.

### Example

{% code overflow="wrap" %}
```typescript
import { NextFunction, Request, Response } from 'express'
import { v4 as newUUID } from 'uuid'
import { User } from '../types/User'
import { UserService } from '../types/Service'
import userSchema from '../schema/userSchema'
import Password from '../util/security/Password'
import HttpStatusCode from '../util/enum/HttpStatusCode'
import Logger from '../util/log/Logger'

export default class CreateUserService {
   static execute(req: Request, res: Response, next: NextFunction): UserService {
     try {
       const { error, value } = userSchema.validate(req.body)

       if (error) {
         res.status(HttpStatusCode.BAD_REQUEST).json({ error: error.details })

         Logger.error(':: Service :: CreateUserService :: Validation ::', error)
         return null
       } else {
         return this._buildQuery(value)
       }
     } catch (error) {
       Logger.error(':: Service :: CreateUserService ::', error)
       next(error)
       return null
     }
   }

   private static _buildQuery(user: User): object {
     user._id = newUUID()
     user.password = Password.encrypt(user.password)
     return user
   }
}
```
{% endcode %}
