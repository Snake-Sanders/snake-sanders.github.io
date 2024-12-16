# Ecto with JSONB on Postgresql

# JSON Arrays

## Query an element in a JSON Array

```elixir
fragment("? @> ?", s.configuration, ^["autotuning"]
```

## Add an element to a JSON Array

```elixir
|> update([s], set: [configuration: fragment("? || ?", s.configuration, ^["autotuning"])])
```

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

## Remoe a list of keys from a map

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
