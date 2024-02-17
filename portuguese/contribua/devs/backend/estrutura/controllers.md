# Controllers

Os _controllers_ são responsáveis por executar as operações HTTP. Cada API tem seu próprio _controller_. Veja como funciona uma operação do _controller_ de usuários `src/controller/UserController.ts`.

### Passo a passo

1. O método usa três parâmetros: `req` (o objeto de solicitação), `res` (o objeto de resposta) e `next` (uma função para chamar o próximo middleware).
2. Ele começa com um bloco `try-catch` para lidar com quaisquer erros que possam ocorrer durante a execução do código.
3. Dentro do bloco `try`, ele chama [`CreateUserService.execute(req, res, next)`](services.md) para criar um novo usuário. Se nenhum usuário for criado, a função retornará imediatamente.
4. Se um usuário for criado, ele tentará inseri-lo na coleção de usuários do banco de dados usando `Collections.users.insertOne(user)`. Isso retorna uma `Promise` que resolve um objeto `CreateDocument`, que contém informações sobre a operação.
5. Se a inserção for bem-sucedida (ou seja, o resultado for verdadeiro), ele envia uma resposta com o código de status HTTP `201 (Criado)` e um objeto JSON contendo o ID do usuário inserido.
6. Se a inserção não for bem-sucedida, ele envia uma resposta com o código de status HTTP `500 (Erro interno do servidor)` e registra o erro.
7. Após a operação, ele registra uma mensagem informativa indicando que o endpoint `CreateUser` foi chamado.
8. No bloco `catch`, caso ocorra algum erro durante a execução do código, ele registra o erro, envia uma resposta com o código de status HTTP `500 (Erro Interno do Servidor)` e chama a próxima função com o erro.

### Exemplo

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
