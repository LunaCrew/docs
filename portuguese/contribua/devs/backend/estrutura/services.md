# Services

As _services_ são responsáveis por aplicar as regras de negócio e executar as validações necessárias antes da requisição no [controller](controllers.md). Cada operação tem uma _service_ própria.

Vamos ver como funciona `src/service/CreateUserService.ts`.

### Passo a passo

A classe possui dois métodos: `execute` e `_buildQuery`.

#### execute()

`execute(req: Request, res: Response, next: NextFunction): UserService`

Este é um método estático que recebe três parâmetros: `req` (a solicitação HTTP), `res` (a resposta HTTP) e `next` (uma função de retorno de chamada para passar controle para a próxima função de middleware).&#x20;

Este método valida o corpo da solicitação em relação a um esquema predefinido (`userSchema`). Se a validação falhar, ele envia um código de status HTTP `BAD_REQUEST` com os detalhes do erro e registra o erro. Se a validação for aprovada, ele chamará o método `_buildQuery` com os dados validados.

#### \_buildQuery()

`_buildQuery(user: User): object`

Este é um método estático privado que recebe um objeto `User`, atribui um novo `UUID` ao campo `_id`, criptografa a senha usando o método `Password.encrypt` e retorna o objeto de usuário atualizado.

### Exemplo

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
