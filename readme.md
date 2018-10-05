# Blog Proxy Buildpack

This is a custom buildpack that reverse proxies a heroku app to the Common blog running on wpengine.

The project was forked from [heroku-buildpack-nginx](https://github.com/heroku/heroku-buildpack-nginx) and heavily customized.

### Language/App Server Agnostic

nginx-buildpack provides a command named `bin/start-nginx-solo` this command takes another command as an argument. You must pass your app server's startup command to `start-nginx`.

For example, to get NGINX and Unicorn up and running:

```bash
$ cat Procfile
web: bin/start-nginx bundle exec unicorn -c config/unicorn.rb
```

### Running the Proxy on Heroku

Setup a new Heroku app and create a `Procfile` with the following:

```
web: bin/start-nginx-solo
```

Setup the buildpack:

```
heroku buildpacks:add common/blog-proxy
```

Deploy and build the heroku app

### Setting the Worker Processes

You can configure NGINX's `worker_processes` directive via the
`NGINX_WORKERS` environment variable.

For example, to set your `NGINX_WORKERS` to 8 on a PX dyno:

```bash
$ heroku config:set NGINX_WORKERS=8
```

### Customizable NGINX Config

You can provide your own NGINX config by creating a file named `nginx.conf.erb` in the config directory of your app. Start by copying the buildpack's [default config file](config/nginx.conf.erb).

### Customizable NGINX Compile Options

See [scripts/build_nginx](scripts/build_nginx) for the build steps. Configuring is as easy as changing the "./configure" options.

You can run the builds in a [Docker](https://www.docker.com/) container:

```
$ make build # It outputs the latest builds to bin/cedar-*
```

To test the builds:

```
$ make shell
$ cp bin/nginx-$STACK bin/nginx
$ FORCE=1 bin/start-nginx-solo
```

### Deploying Buildpack Changes
```
heroku buildpacks:publish common/blog-proxy master
```

### Logging

NGINX will output the following style of logs:

```
measure.nginx.service=0.007 request_id=e2c79e86b3260b9c703756ec93f8a66d
```

You can correlate this id with your Heroku router logs:

```
at=info method=GET path=/ host=salty-earth-7125.herokuapp.com request_id=e2c79e86b3260b9c703756ec93f8a66d fwd="67.180.77.184" dyno=web.1 connect=1ms service=8ms status=200 bytes=21
```

## License
Copyright (c) 2013 Ryan R. Smith
Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:
The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.
THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
