# Routes

### Route configuration

In `src/router/routes.ts` the application routes are configured.

{% code overflow="wrap" %}
```typescript
import express, { Application } from 'express'
import userRouter from './userRouter'
import Logger from '../util/log/Logger'

/**
  * Configures application routes.
  *
  * @param {Application} app - The Express application.
  */
const routes = (app: Application) => {
   try {
     app.use(
       express.json(),
       userRouter
     )
   } catch (error) {
     Logger.error(':: Router :: Routes ::', error)
   }
}

export { routes }
```
{% endcode %}

### Declaration of API routes

Each API has its own file where its routes are declared. See the user API routes in the example.

{% code overflow="wrap" %}
```typescript
import express from 'express'
import UserController from '../controller/UserController'

/**
  * Router to handle user-related routes.
  */
const userRouter = express.Router()

userRouter.post('/api/user', UserController.createUser, userRouter)
userRouter.get('/api/user', UserController.getUser, userRouter)
...

export default userRouter
```
{% endcode %}
