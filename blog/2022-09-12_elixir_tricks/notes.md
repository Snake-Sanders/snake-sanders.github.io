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

## Show Module's documentation

There is help documentation available in the console, just type `h` a pass it
the module you want to know more about.

```Elixir
iex(1)> h Enum
```

## Show Module's functions

Most of the times your modules don't have documentation available, thus
to peak the exported funtion from such a module you can use `exports`.

```Elixir
iex(1)> exports Enum
all?/1                
all?/2                
any?/1                
any?/2  
...
```      

## Inspect the processes

In the iex type `:observer.start()`

This will open the GUI with information about the system. 

## To run the application

Type `mix run --no-halt`

## re-execute a previous command

Using `v(n)` where `n` < 0, iex will return the result of a previous command line.

```elixir
iex(1)> "hi" <> " you"
"hi you"
iex(2)> "hello"
"hello"
iex(3)> v(-2)
"hi you"
```

# Working with processes

Let's say you are running a program that started a process, you know the pid but you don't have a reference to it.

the log in the console showing the pid would be:

`started process pid:#PID<0.429.0>`

You can create a pid obect as follows

```
my_pid = :c.pid(0,429,0)
```

# Adding dependencies

For a quick reference on a dependency you can peak the version using the command `mix hex.info <dep_name>`. This will give you the latest version already formated to be include your `mix.ex`.

e.g.: `{:jason, "~> 1.4"}`

```
mix hex.info jason 

A blazing fast JSON parser and generator in pure Elixir.

Config: {:jason, "~> 1.4"}
Releases: 1.5.0-alpha.2, 1.5.0-alpha.1, 1.4.1, 1.4.0, 1.3.0, 1.2.2, 1.2.1, 1.2.0, ...

Licenses: Apache-2.0
Links:
  GitHub: https://github.com/michalmuskala/jason
```
