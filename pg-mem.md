# Using pg-mem for testing database

Resources:

- [using pg-mem for smooth integration testing](https://swizec.com/blog/pg-mem-and-jest-for-smooth-integration-testing/)

```
import { randomUUID } from 'crypto';
import fs from 'fs';
import path from 'path';
import { DataType, newDb } from 'pg-mem';

// Create pg-mem database
const db = newDb();

db.public.registerFunction({
    implementation: () => randomUUID(),
    name: 'gen_random_uuid',
    returns: DataType.uuid,
});

// Create schema
db.public.none(
    fs.readFileSync(path.join(__dirname, '..', 'test-resources', 'schema', 'schema.sql'), 'utf8')
);

// Create a backup (insert data that will be common to all tests before that if required)
const backup = db.backup();

...
backup.restore();

const sel = db.public.many(`select * from schema.user`);
console.debug({ sel });
...
```
