# Ecto with JSONB on Postgresql

- `Reading`: Use fragment with `->` or `->>` to extract values from JSONB.
- `Writing`: Use jsonb_set within fragment to modify the JSONB structure.
- `Advanced`: For more complex operations, you can leverage PostgreSQL's built-in
JSONB functions and operators, such as `jsonb_set`, `jsonb_insert`, `jsonb_array_elements`, and more.

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
