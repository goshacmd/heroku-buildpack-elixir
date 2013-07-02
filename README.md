## Heroku buildpack: Elixir

This is a Heroku buildpack for Elixir apps. It uses
[Mix](http://elixir-lang.org/getting_started/mix.html).

### Configure your Heroku App

    $ heroku config:add BUILDPACK_URL="https://github.com/goshakkk/heroku-buildpack-elixir.git" -a YOUR_APP

or

    $ heroku create --buildpack "https://github.com/goshakkk/heroku-buildpack-elixir.git"

### Select an Erlang version

The Erlang/OTP release version that will be used to build and run your
application is now sourced from a dotfile called `.preferred_otp_version`. It
needs to be the branch or tag name from the http://github.com/erlang/otp
repository, and further, needs to be one of the versions that precompiled
binaries are available for.

Currently supported OTP versions:

* master (R15B02 pre)
* master-pu (R16B pre)
* OTP_R15B
* OTP_R15B01
* OTP_R15B02

To select the version for your app:

    $ echo OTP_R15B02 > .preferred_otp_version
    $ git commit "Select R15B02 as preferred OTP version" .preferred_otp_version

If no version is explicitly specified, `master` will be used.

### Elixir version

The application will be compiled and run using Elixir master (v0.7.0.dev at the
time) and Mix.

You can specify custom branch or tag name from the
https://github.com/elixir-lang/elixir repository in the
`.preferred_elixir_version` dotfile.

### Setup a Procfile

Heroku needs a Procfile in order to run your application. Create a Procfile with a `web` process defined:

    $ echo 'web: MIX_ENV=prod mix server -p $PORT' > Procfile
    
**Important Note:** Single quotes are important here. `$PORT` is an environment variable supplied by Heroku. If you use double quotes 
in the above `echo` call, your local shell will try to interpolate the contents, and you'll end up with `-p ` and not `-p $PORT`.

### Bundling

`heroku-elixir-buildpack` supports only applications which use
[Mix](http://elixir-lang.org/getting_started/mix.html) to manage
dependencies.

### Build your Heroku App

    $ git push heroku master

You may need to write a new commit and push if your code was already up to date.
