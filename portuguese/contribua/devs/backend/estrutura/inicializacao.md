# Inicialização

Em `src/app.ts` são inicializados a aplicação do **Express** e a conexão com o [**MongoDB**](inicializacao.md#conexao-ao-mongodb).

### Conexão ao MongoDB

{% code overflow="wrap" %}
```typescript
import { MongoClient, ServerApiVersion } from 'mongodb'

/**
* Instância-se o cliente MongoDB.
* @observações
* Este cliente é usado para conectar-se ao banco de dados MongoDB.
* @público
*/
export const client = new MongoClient(process.env.DB_URI || '', {
  serverApi: {
    version: ServerApiVersion.v1,
    strict: true,
    deprecationErrors: true,
  }
})

/**
* Conecta-se ao banco de dados MongoDB.
* @returns {Promise<void>} Uma promessa que é resolvida quando a conexão é estabelecida. 
*/
const connect = async () => {
  try {
    await client.connect()
    await client.db().command({ ping: 1 })
    Logger.success(':: Connected to MongoDB! ::')
  } catch (error) {
    await client.close()
    Logger.error(':: Database Connection ::', error)
  }
}

/**
* Declara o objeto "collections" contendo referências a diversas coleções de banco de dados.
* @property {Collection} users – A coleção de usuários.
* @property {Collection} notas - A coleção de notas.
* @property {Collection} lembretes - A coleção de lembretes.
* @property {Collection} altComms – A coleção altComms.
* @property {Collection} pomodoroTimers - A coleção pomodoroTimers.
* @property {Collection} tarefas - A coleção de tarefas.
* @property {Collection} rotinas - A coleção de rotinas.
*/
const collections = {
  users: client.db().collection('users'),
  notes: client.db().collection('notes'),
  reminders: client.db().collection('reminders'),
  altComms: client.db().collection('alt-comms'),
  pomodoroTimers: client.db().collection('pomodoro-timers'),
  tasks: client.db().collection('tasks'),
  routines: client.db().collection('routines')
}

/*
* Exporta-se o objeto 
*/
export { collections }
```
{% endcode %}

### Aplicação Express

{% code overflow="wrap" %}
```typescript
import express from 'express'

/**
 * Instância da aplicação Express.
 */
const app: express.Application = express()
const PORT = process.env.PORT || 3000

/**
 * Inicia o servidor e escuta na porta especificada.
 */
const start = () => {
  try {
    app.listen(PORT, () => {
      Logger.success(`:: Server running on port ${PORT} ::`)
    })
    app.use('/api/docs', swaggerUi.serve, swaggerUi.setup(swaggerDoc))
  } catch (error) {
    Logger.error(':: App :: Start server ::', error)
  }
}
```
{% endcode %}
