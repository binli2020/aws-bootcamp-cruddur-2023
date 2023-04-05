# Create sql files

Create a `db` directory. Create a sql file `db/schema.sql`
```sql
-- https://www.postgresql.org/docs/current/uuid-ossp.html
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

-- forcefully drop our tables if they already exist
DROP TABLE IF EXISTS public.users cascade;
DROP TABLE IF EXISTS public.activities;

CREATE TABLE public.users (
  uuid UUID default uuid_generate_v4() primary key,
  display_name text,
  handle text,
  cognito_user_id text,
  created_at timestamp default current_timestamp NOT NULL
);

CREATE TABLE public.activities (
  uuid UUID default uuid_generate_v4() primary key,
  user_uuid UUID REFERENCES public.users(uuid) NOT NULL,
  message text NOT NULL,
  replies_count integer default 0,
  reposts_count integer default 0,
  likes_count integer default 0,
  reply_to_activity_uuid integer,
  expires_at timestamp,
  created_at timestamp default current_timestamp NOT NULL
);
```

Create a sql file `db/seed.sql`
```sql
-- this file was manually created
INSERT INTO public.users (display_name, handle, cognito_user_id)
VALUES
  ('Andrew Brown', 'andrewbrown' ,'MOCK'),
  ('Andrew Bayko', 'bayko' ,'MOCK');

INSERT INTO public.activities (user_uuid, message, expires_at)
VALUES
  (
    (SELECT uuid from public.users WHERE users.handle = 'andrewbrown' LIMIT 1),
    'This was imported as seed data!',
    current_timestamp + interval '10 day'
  )
```

# Set environment variables

Set env vars in terminal
```sh
export CONNECTION_URL="postgresql://postgres:password@127.0.0.1:5432/cruddur"
export PROD_CONNECTION_URL="postgresql://root:<password>@<rds_db_instance_endpoint>:5432/cruddur"
```

Set env vars in the `docker-compose.yml`
```yml
  backend-flask:
    environment:
      CONNECTION_URL: "${CONNECTION_URL}"
      PROD_CONNECTION_URL: "${PROD_CONNECTION_URL}"
```

# Create bash scripts

Create a `backend-flask/bin` directory.
Create the following bash scripts in the directory.

`db-connect`: connect to a database
```sh
#! /usr/bin/bash

psql $CONNECTION_URL
```

`db-create`: create a database
```sh
#! /usr/bin/bash

NO_DB_CONNECTION_URL=$(sed 's/\/cruddur//g' <<<"$CONNECTION_URL")
echo $NO_DB_CONNECTION_URL

psql $NO_DB_CONNECTION_URL -c "CREATE database cruddur;"
```

`db-drop`: drop a database
```sh
#! /usr/bin/bash

NO_DB_CONNECTION_URL=$(sed 's/\/cruddur//g' <<<"$CONNECTION_URL")
psql $NO_DB_CONNECTION_URL -c "DROP database cruddur;"
```

`db-schema-load`: load schema from a file
```sh
#! /usr/bin/bash

schema_path=$(realpath .)/db/schema.sql
echo $schema_path

if [ "$1" = "prod" ]; then
  echo "Running in production mode"
  URL=$PROD_CONNECTION_URL
else
  URL=$CONNECTION_URL
fi

psql $URL cruddur < $schema_path
```

`db-seed`: load the seed data
```sh
#! /usr/bin/bash

seed_path=$(realpath .)/db/seed.sql
echo $seed_path

if [ "$1" = "prod" ]; then
  echo "Running in production mode"
  URL=$PROD_CONNECTION_URL
else
  URL=$CONNECTION_URL
fi

psql $URL cruddur < $seed_path
```
