# Initialization

In `src/app.ts` the **Express** application and the connection with **MongoDB** are initialized.

### Connection to MongoDB

{% code overflow="wrap" %}
```typescript
import { MongoClient, ServerApiVersion } from 'mongodb'

/**
* Instantiate the MongoDB client.
* @comments
* This client is used to connect to the MongoDB database.
* @public
*/
export const client = new MongoClient(process.env.DB_URI || '', {
   serverApi: {
     version: ServerApiVersion.v1,
     strict: true,
     deprecationErrors: true,
   }
})

/**
* Connects to MongoDB database.
* @returns {Promise<void>} A promise that is resolved when the connection is established.
*/
const connect = async() => {
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
* Declares the "collections" object containing references to several database collections.
* @property {Collection} users – The collection of users.
* @property {Collection} notes - The collection of notes.
* @property {Collection} reminders - The collection of reminders.
* @property {Collection} altComms – The altComms collection.
* @property {Collection} pomodoroTimers - The pomodoroTimers collection.
* @property {Collection} tasks - The collection of tasks.
* @property {Collection} routines - The collection of routines.
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
* Export the object
*/
export { collections }
```
{% endcode %}

### Express Application

{% code overflow="wrap" %}
```typescript
import express from 'express'

/**
  * Express application instance.
  */
const app: express.Application = express()
const PORT = process.env.PORT || 3000

/**
  * Starts the server and listens on the specified port.
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
