# Rotas

### Configuração das rotas

Em `src/router/routes.ts` são configuradas as rotas da aplicação.

{% code overflow="wrap" %}
```typescript
import express, { Application } from 'express'
import userRouter from './userRouter'
import Logger from '../util/log/Logger'

/**
 * Configura as rotas da aplicação.
 *
 * @param {Application} app - A aplicação Express.
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

### Declaração das rotas da API

Cada API tem um arquivo próprio onde são declaradas suas rotas. Veja no exemplo as rotas da API de usuários.

{% code overflow="wrap" %}
```typescript
import express from 'express'
import UserController from '../controller/UserController'

/**
 * Router para lidar com rotas relacionadas ao usuário.
 */
const userRouter = express.Router()

userRouter.post('/api/user', UserController.createUser, userRouter)
userRouter.get('/api/user', UserController.getUser, userRouter)
...

export default userRouter
```
{% endcode %}
