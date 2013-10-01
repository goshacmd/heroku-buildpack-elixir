## Heroku buildpack: Elixir

This is a Heroku buildpack for Elixir apps. It uses
[Mix](http://elixir-lang.org/getting_started/mix/1.html).

### Configure your Heroku App

```bash
$ heroku config:add BUILDPACK_URL="https://github.com/goshakkk/heroku-buildpack-elixir.git" -a YOUR_APP
```

or

```bash
$ heroku create --buildpack "https://github.com/goshakkk/heroku-buildpack-elixir.git"
```

### Select an Erlang version

The Erlang/OTP release version that will be used to build and run your
application is now sourced from a dotfile called `.preferred_otp_version`. It
needs to be the branch or tag name from the http://github.com/erlang/otp
repository, and further, needs to be one of the versions that precompiled
binaries are available for.

Currently supported OTP versions:

* master (R17A)
* OTP_R16B02
* OTP_R16B01
* OTP_R16B
* OTP_R15B03
* OTP_R15B02
* OTP_R15B01

To select the version for your app:

```bash
$ echo OTP_R16B02 > .preferred_otp_version
$ git commit -m "Select R16B02 as preferred OTP version" .preferred_otp_version
```

If no version is explicitly specified, `master` will be used.

### Select an Elixir version

The application will be compiled and run using Elixir master and Mix.

You can specify custom branch or tag name from the
https://github.com/elixir-lang/elixir repository in the
`.preferred_elixir_version` dotfile.

### Setup a Procfile

Heroku needs a Procfile in order to run your application. Create a Procfile with a `web` process defined:

#### Dynamo

```bash
$ echo 'web: mix server -p $PORT' > Procfile
```

The buildpack sets `MIX_ENV=prod` so you don't have to.

**Important Note:** Single quotes are important here. `$PORT` is an environment variable supplied by Heroku. If you use double quotes
in the above `echo` call, your local shell will try to interpolate the contents, and you'll end up with `-p ` and not `-p $PORT`.

#### Cowboy

```bash
$ echo 'web: mix run --no-halt' > Procfile
```

You should bind to port specified in `PORT` environment variable:

```elixir
{ :ok, _ } = :cowboy.start_http(
  :http, 100,
  [port: port(System.get_env("PORT"))],
  [env: [dispatch: dispatch]]
)
```

```elixir
def port(nil), do: 8080
def port(value) do
  binary_to_integer(value)
end
```

**Important Note:** If you bind to any other port than the assigned one, your process will be killed with an R11 error code.

### Bundling

`heroku-elixir-buildpack` supports only applications which use
[Mix](http://elixir-lang.org/getting_started/mix/1.html) to manage
dependencies.

### Build your Heroku App

```bash
$ git push heroku master
```

You may need to write a new commit and push if your code was already up to date.
