# Ecto with JSONB on Postgresql

- `Reading`: Use fragment with `->` or `->>` to extract values from JSONB.
- `Writing`: Use jsonb_set within fragment to modify the JSONB structure.
- `Advanced`: For more complex operations, you can leverage PostgreSQL's built-in
JSONB functions and operators, such as `jsonb_set`, `jsonb_insert`, `jsonb_array_elements`, and more.

# JSONB colun

## Reading from a JSONB column

If you want to query specific fields within a JSONB column 
(e.g., `jsonb_data->'key'`), you can use fragment to access that part of the JSON data.

```elixir
from(u in User,
  select: fragment("?->>'key_name'", u.jsonb_data)
)
```

If you want to compare it with a value, use the following:

```elixir
from(u in User,
  where: fragment("?->>'key_name' = ?", u.jsonb_data, "value"),
  select: u
)
```

In the example above, `jsonb_data->>'key_name'` extracts the value of the 
`key_name` field from the `jsonb_data` column as text and checks if it matches 
`"value"`.

## Writing to a JSONB column

```elixir
Repo.update_all(
  from(u in User, where: u.id == ^user_id),
  set: [jsonb_data: fragment("jsonb_set(?, ?, ?)", u.jsonb_data, "'key_name'", "'new_value'")]
)
```

## Appending a new key-value pair

You can append a new key-value pair using the `jsonb_set` or `jsonb_insert` function:

```elixir
Repo.update_all(
  from(u in User, where: u.id == ^user_id),
  set: [jsonb_data: fragment("jsonb_set(?, ?, ?)", u.jsonb_data, "'new_key'", "'new_value'")]
)
```

# JSON Arrays

## Query an element in a JSON Array

```elixir
fragment("? @> ?", s.configuration, ^["autotuning"]
```

## Add an element to a JSON Array

```elixir
|> update([s], set: [configuration: fragment("? || ?", s.configuration, ^["autotuning"])])
```
**Note**

You can also use the `jsonb_array_elements ` function in fragment if you're dealing with arrays inside JSONB.

## Delete an element in a JSON Array

```elixir
query =
    from s in Sensor,
    update: [
      set: [
        configuration: fragment("(
          SELECT jsonb_agg(elem)
          FROM jsonb_array_elements(?) elem
          WHERE elem != ?::jsonb
        )", s.configuration, ^"autotuning")
      ]
    ]

Repo.update_all(query, [])
```

# JSON Maps

## Remove a key from a map

# For JSONB maps (removing a key)
```elixir
    query = from s in Sensor,
      update: [
        set: [
          configuration: fragment("? - ?", s.configuration, "autotuning")
        ]
      ]

    Repo.update_all(query, [])
```

## Remove a list of keys from a map

```elixir 
query =
  from s in Sensor,
    update: [
      set: [
        configuration: fragment("? - array[?, ?, ?]", s.configuration, "key1", "key2", "key3")
      ]
    ]

Repo.update_all(query, [])
```
