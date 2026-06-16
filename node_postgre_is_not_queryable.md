## node-postgre error: Client has encountered a connection error and is not queryable

https://node-postgres.com/

### Error
"message":"Client has encountered a connection error and is not queryable","stack":"Error: Client has encountered a connection error and is not queryable\n at /usr/src/app/node_modules/pg/lib/client.js:566:27\

### solutions
Make sure the connection uses a connection pool

```javascript
import pg from 'pg'
const { Pool } = pg
 
const pool = new Pool()
 
// the pool will emit an error on behalf of any idle clients
// it contains if a backend error or network partition happens
pool.on('error', (err, client) => {
  console.error('Unexpected error on idle client', err)
  process.exit(-1)
})
 
const client = await pool.connect()
const res = await client.query('SELECT * FROM users WHERE id = $1', [1])
console.log(res.rows[0])
 
client.release()
```
