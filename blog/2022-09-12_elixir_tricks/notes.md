# Elixir tricks

## Check running applications in iex

```Elixir 
iex(1)> Application.started_applications()
[
  {:webhook, 'webhook', '0.1.0'},
  {:poison, 'An incredibly fast, pure Elixir JSON library', '5.0.0'},
  {:plug_cowboy, 'A Plug adapter for Cowboy', '2.5.2'},
  {:cowboy_telemetry, 'Telemetry instrumentation for Cowboy',
   '0.4.0'},
  {:cowboy, 'Small, fast, modern HTTP server.', '2.9.0'},
  ...
  ```

## Escape an incomplete expresion in iex

Use column '`:`' to break an incomplete expresion in `iex`

```Elixir
iex(2)> {
...(2)> :
** (SyntaxError) iex:3:1: unexpected token: ":" (column 1, code point U+003A)
    |
  3 | :
    | ^

iex(2)> 
```

## Show available modules

Type column '`:`' and then press `TAB`

```Elixir
iex(1)> :
Elixir.Access                              
Elixir.Agent                               
Elixir.Agent.Server                        
Elixir.Application   
...
```

## Inspect the processes

In the iex type `:observer.start()`

This will open the GUI with information about the system. 

## To run the application

type `mix run --no-halt`
