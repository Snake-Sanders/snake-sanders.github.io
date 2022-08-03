# Binaries in Elixir

_2022-04-09_

- [Binaries in Elixir](#binaries-in-elixir)
- [Examples](#examples)
  - [Converting an integer to a binary](#converting-an-integer-to-a-binary)
  - [Little and big endian](#little-and-big-endian)
  - [Matching an integer bigger than 8 bits](#matching-an-integer-bigger-than-8-bits)
  - [From string to binary](#from-string-to-binary)

Starting with binaries in Elixir can be tricky, here is a great tutorial from `Blackode` for starting off:

https://medium.com/blackode/playing-with-elixir-binaries-strings-dd01a40039d5

# Examples

Here I leave some examples

## Converting an integer to a binary

Say we have an IP address `192.168.1.235` encoded in a binary, of 4 bytes (32 bits)

```elixir
iex> ip = <<192, 168, 1, 235>>
```

Inspecting the content of `ip` we can see that it is a binary:

```elixir
iex> ip
<<192, 168, 1, 235>>
```
We can also inspect the type of variable by typing `i`:
```elixir
iex> i
Term
    <<192, 168, 1, 235>>
Data type
    BitString
Byte size
    4
Description
    This is a binary: a collection of bytes. It's printed with the `<<>>`
    syntax (as opposed to double quotes) because it is not a UTF-8 encoded
    binary (the first invalid byte being `<<192>>`)
Reference modules
    :binary
Implemented protocols
    Collectable, IEx.Info, Inspect, List.Chars, String.Chars, Timex.Protocol
```

To convert this binary to an integer, we do:

```elixir
iex> <<int_ip::32>> = ip
<<192, 168, 1, 235>>
```

Then `int_ip` is represented as a 32 bits integer

```elixir
iex> int_ip
3232236011
```

To convert the integer back to a binary, we do:

```elixir
iex> bin_ip = <<int_ip::32>>
<<192, 168, 1, 235>>

iex> bin_ip
<<192, 168, 1, 235>>
```

## Little and big endian

Examples of endianess attribute working on the `left` and `right` side of the matching:

```elixir
iex> ip
3232236011

iex(85)> <<a::little-32>> = <<ip::size(32)>>
<<192, 168, 1, 235>>

iex(86)> a
3942754496

iex(87)> <<a::32>> = <<ip::little-size(32)>>
<<235, 1, 168, 192>>

iex(88)> a
3942754496

iex(89)> <<a::32>> = <<ip::big-size(32)>>
<<192, 168, 1, 235>>

iex(90)> a
3232236011

iex(91)> <<a::big-32>> = <<ip::size(32)>>
<<192, 168, 1, 235>>

iex(92)> a
3232236011

iex> <<a::32>> = <<ip::size(32)>>
<<192, 168, 1, 235>>

iex> a
3232236011
```

## Matching an integer bigger than 8 bits

One might be tempted to take an number and "cast" it to a binary, like so:

```elixir
iex> data = <<3232236011>>
<<235>>
```

but this will only take the first 8 bits from the number representation, whatever this is, 8 bits, 16, 32, 64, etc

therefore the following pattern matching will not work:

```elixir
<<head::8, tail::binary>> = <<3232236011>>
<<235>>
iex> head
235
iex> tail
""
```

to do this pattern matching we need to indicate the size of the binary on the right.

```elixir
<<head::8, tail::binary>> = <<3232236011::32>>
<<192, 168, 1, 235>>
```

## From string to binary

In this case I was analyzing a communication package exchange and I dumped the message payload on the console. In order to decode the payload and create test cases I use this copied from the console dump log of a data frame payload.
The console printed in hex a stream of bytes as follows:

```
b'000000000000000001000000e6072b101606090400000000c0a801eb59bc000070c30000000006000000000000000000'
```

1. upercase, and `0x` prefix, **not with capital** `0X`.
2. add the length with the suffix `::`, here as `::384`

```
<<0x000000000000000001000000E6072B101606090400000000C0A801EB59BC000070C30000000006000000000000000000::384>>