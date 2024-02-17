# Schemas

### Definição

Um esquema é um objeto JSON que define a estrutura e o conteúdo dos seus dados. Os esquemas representam tipos de dados em vez de valores específicos.

### Tipos

Uma das funções do esquema é definir o tipo de dado que será inserido num determinado campo. Em nossos projetos utilizamos a biblioteca [**Joi**](https://joi.dev/) para validação dos dados.

### Exemplo

O objeto `noteSchema` é definido. Este objeto possui quatro propriedades: `userId`, `createdAt`, `updatedAt` e `note`.

* `userId` é definido como uma `string` que deve seguir o formato de um **UUID versão 4**. Isso é aplicado pelo método `guid({ version: 'uuidv4' })`. O método `require()` indica que esta propriedade deve ser fornecida ao criar ou atualizar uma nota.
* `createAt` é uma `string` cujo padrão é a data e hora atuais no formato **ISO**, se não for fornecida. Isso é conseguido chamando `new Date().toISOString()` dentro do método `default()`.
* `updateAt` também é uma `string`, mas o padrão é nulo se não for fornecido. Esta propriedade será atualizada manualmente sempre que a nota for modificada.
* `note` é uma `string` que pode conter até 2.000 caracteres. O método `max(2000)` impõe esse limite. Assim como o `userId`, essa propriedade é obrigatória.

Por fim, noteSchema é exportado como exportação padrão deste módulo, permitindo que seja importado e utilizado em outras partes da aplicação. Este esquema pode ser usado para validar objetos de notas antes de serem armazenados no banco de dados, garantindo que eles tenham a estrutura e os tipos corretos.

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
