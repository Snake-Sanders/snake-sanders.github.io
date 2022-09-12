# Starting with Phoenix and LiveView

- [Starting with Phoenix and LiveView](#starting-with-phoenix-and-liveview)
  - [Database](#database)
  - [Phoenix](#phoenix)
  - [Create a new project](#create-a-new-project)
  - [Use generators](#use-generators)
  - [TailwindCss](#tailwindcss)

Assuming Elixir is already installed

## Database

Install a database

By default Phoenix uses Postgresql

    brew install postgresql

## Phoenix

Install Phoenix:

    mix archive.install hex phx_new

No longer need for WebPacker not Node.

    https://fly.io/blog/phoenix-moves-to-esbuild-for-assets/

## Create a new project    

    mix phx.new prag

Mix guides you through the next steps

We are almost there! The following steps are missing:

    $ cd prag

Then configure your database in config/dev.exs and run:

    $ mix ecto.create

Start your Phoenix app with:

    $ mix phx.server

You can also run your app inside IEx (Interactive Elixir) as:

    $ iex -S mix phx.server    

## Use generators

* Generator the standard module

https://hexdocs.pm/phoenix/Mix.Tasks.Phx.Gen.Html.html

* Generator the LiveView module

https://hexdocs.pm/phoenix/Mix.Tasks.Phx.Gen.Live.html


The most usefull information is within the mix task help page

    mix help phx.gen.live

## TailwindCss

The main installation is defined in TailwindCss page

https://tailwindcss.com/docs/guides/phoenix

but to add some extra features check

https://pragmaticstudio.com/tutorials/adding-tailwind-css-to-phoenix