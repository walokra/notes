# PostgreSQL

## Nullable columns

```
select table_name, column_name, is_nullable, data_type from information_schema.columns where table_schema = 'public' and is_nullable = 'YES';
```

```
select concat('SELECT ''', table_name, '.', column_name, ''', COUNT(*) FROM ', table_name, ' WHERE ', column_name, ' IS NULL') from information_schema.columns where table_schema = 'public' and is_nullable = 'YES';
```

```
select concat('SELECT ''', table_name, '.', column_name, ''', COUNT(*) c FROM ', table_name, ' WHERE ', column_name, ' IS NULL HAVING c = 0') from information_schema.columns where table_schema = 'public' and is_nullable = 'YES';
```

```
select array_to_string(array_agg(x.query), E'\n UNION ')
from (
         select concat(E'SELECT \'', table_name, '.', column_name, E'\' colname, COUNT(*) c FROM "', table_name,
                       '" WHERE "',
                       column_name, '" IS NULL HAVING COUNT(*) = 0') query
         from information_schema.columns
         where table_schema = 'public'
           and is_nullable = 'YES') x;
```

More usable
```
select array_to_string(array_agg(x.query), E'\n UNION ')
from (
         select concat(E'SELECT \'', table_name, '.', column_name, E'\' colname, COUNT(*) null_rows FROM "', table_name,
                       '" WHERE "',
                       column_name, '" IS NULL') query
         from information_schema.columns
         where table_schema = 'public'
           and is_nullable = 'YES') x;
```
