# api documentation for  [amok (v1.1.3)](http://amokjs.com)  [![npm package](https://img.shields.io/npm/v/npmdoc-amok.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-amok) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-amok.svg)](https://travis-ci.org/npmdoc/node-npmdoc-amok)
#### Live editing, testing and debugging for JavaScript

[![NPM](https://nodei.co/npm/amok.png?downloads=true)](https://www.npmjs.com/package/amok)

[![apidoc](https://npmdoc.github.io/node-npmdoc-amok/build/screenCapture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-amok_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-amok/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-amok/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-amok/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "Casper Beyer",
        "email": "caspervonb@qup.io"
    },
    "bin": {
        "amok": "bin/amok"
    },
    "bugs": {
        "url": "https://github.com/caspervonb/amok/issues"
    },
    "dependencies": {
        "browser_process": "^1.0.0",
        "chokidar": "^1.0.3",
        "commander": "^2.8.1",
        "compiler_process": "^1.0.0",
        "mime": "^1.3.4",
        "rdbg": "^1.0.0",
        "temp": "^0.8.1",
        "ware": "^1.3.0",
        "ws": "^0.7.2"
    },
    "description": "Live editing, testing and debugging for JavaScript",
    "devDependencies": {
        "babel": "^5.6.14",
        "coffee-script": "1.9.2",
        "node-libs-browser": "0.5.2",
        "sculpt": "^0.1.7",
        "tape": "^4.0.0",
        "typescript": "1.5.0-beta",
        "watchify": "^2.6.2",
        "webpack": "1.9.10"
    },
    "directories": {},
    "dist": {
        "shasum": "08406ab9308de44c6936181ebd440ab638b2251c",
        "tarball": "https://registry.npmjs.org/amok/-/amok-1.1.3.tgz"
    },
    "files": [
        "lib/",
        "bin/"
    ],
    "gitHead": "3979f0f2a912b37457707d2ce0db006e62f1f0d4",
    "homepage": "http://amokjs.com",
    "keywords": [
        "realtime",
        "live",
        "reload",
        "editing",
        "react",
        "browserify",
        "watchify",
        "webpack",
        "development",
        "server"
    ],
    "license": "MIT",
    "main": "lib/index.js",
    "maintainers": [
        {
            "name": "caspervonb",
            "email": "caspervonb@qup.io"
        }
    ],
    "name": "amok",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git+https://github.com/caspervonb/amok.git"
    },
    "scripts": {
        "test": "make test"
    },
    "version": "1.1.3"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module amok](#apidoc.module.amok)
1.  [function <span class="apidocSignatureSpan">amok.</span>browser (port, command, args, options)](#apidoc.element.amok.browser)
1.  [function <span class="apidocSignatureSpan">amok.</span>compiler (command, args, options)](#apidoc.element.amok.compiler)
1.  [function <span class="apidocSignatureSpan">amok.</span>createRunner ()](#apidoc.element.amok.createRunner)
1.  [function <span class="apidocSignatureSpan">amok.</span>hotpatch ()](#apidoc.element.amok.hotpatch)
1.  [function <span class="apidocSignatureSpan">amok.</span>multiplexer (port, host, debugPort, debugHost)](#apidoc.element.amok.multiplexer)
1.  [function <span class="apidocSignatureSpan">amok.</span>print (output)](#apidoc.element.amok.print)
1.  [function <span class="apidocSignatureSpan">amok.</span>repl (input, output, options)](#apidoc.element.amok.repl)
1.  [function <span class="apidocSignatureSpan">amok.</span>server (port, host)](#apidoc.element.amok.server)
1.  [function <span class="apidocSignatureSpan">amok.</span>watch (glob)](#apidoc.element.amok.watch)
1.  number <span class="apidocSignatureSpan">amok.</span>_eventsCount
1.  object <span class="apidocSignatureSpan">amok.</span>_events
1.  object <span class="apidocSignatureSpan">amok.</span>client
1.  object <span class="apidocSignatureSpan">amok.</span>domain
1.  object <span class="apidocSignatureSpan">amok.</span>plugins
1.  object <span class="apidocSignatureSpan">amok.</span>settings



# <a name="apidoc.module.amok"></a>[module amok](#apidoc.module.amok)

#### <a name="apidoc.element.amok.browser"></a>[function <span class="apidocSignatureSpan">amok.</span>browser (port, command, args, options)](#apidoc.element.amok.browser)
- description and source-code
```javascript
function plugin(port, command, args, options) {
  if (typeof args === 'undefined') {
    args = [];
  } else {
    args = args.slice(0);
  }

  return function browser(client, runner, done) {
    var server = net.createServer();

    server.once('error', function(error) {
      debug('server error', error);
      done(error);
    });

    server.once('listening', function() {
      debug('server listening');

      server.once('close', function() {
        debug('server close');

        var url = runner.get('url');

        // TODO, move into browser_process.createProfile, deleting the first run file
        // should be equivilent of providing these options, and be compatible with how firefox does it.
        args.unshift('--no-first-run', '--no-default-browser-check');
        var dirname = temp.path(command);
        args.unshift.apply(args, web.options(command, {
          profile: dirname,
          url: url,
          debug: port,
        }));

        debug('spawn %s %s', command, args.join(' '));
        web.spawn(command, args, options, function (error, browser) {
          if (error) {
            debug('bail %s', error.description);
            return done(error);
          }

          runner.once('close', function kill() {
            debug('kill process');
            browser.kill('SIGTERM');
          });

          debug('find %s', url);
          setTimeout(function find(retry) {
            rdbg.get(port, 'localhost', function (error, targets) {
              if (error) {
                targets = [];
              }

              var matches = targets.filter(function (target) {
                return url === target.url;
              });

              if (matches.length > 0) {
                debug('ready');
                return done();
              } else if (retry) {
                return setTimeout(find, 1000, retry--);
              }

              if (error === undefined) {
                error = new Error('Cannot find browser tab \'' + url + '\'');
              }

              debug('find error', error);
              done(error);
            });
          }, 1000, 120);
        });
      });

      debug('close server');
      server.close();
    });

    debug('starting server on port', port);
    server.listen(port);
  };
}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.amok.compiler"></a>[function <span class="apidocSignatureSpan">amok.</span>compiler (command, args, options)](#apidoc.element.amok.compiler)
- description and source-code
```javascript
function plugin(command, args, options) {
  if (typeof args == 'undefined') {
    args = [];
  } else {
    args = args.slice(0);
  }

  return function compiler(client, runner, done) {
    var entry = args.filter(function (arg) {
      return arg.match(/(.js|.ts|.coffee)$/);
    })[0];

    var dirname = temp.mkdirSync(command);
    var pathname = path.normalize(entry);
    var basename = path.basename(pathname);
    var filename = path.join(dirname, basename).replace(/\.[^\.]+$/, '.js');

    var scripts = {};
    scripts[pathname] = filename;
    runner.set('scripts', scripts);

    args.unshift.apply(args, script.options(command, {
      outfile: filename,
      watch: true,
    }));

    debug('spawn %s %s', command, args.join(' '));
    script.spawn(command, args, options, function (error, compiler) {
      if (error) {
        debug('bail %s', error.description);
        return done(error);
      }

      runner.once('close', function kill() {
        debug('kill');
        compiler.kill('SIGTERM');
      });

      debug('wait %s', filename);
      process.nextTick(function wait() {
        fs.stat(filename, function (error, stat) {
          if (error || stat.size === 0) {
            return process.nextTick(wait);
          }

          setTimeout(function () {
            debug('ready');
            done();
          }, 250);
        });
      });
    });
  };
}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.amok.createRunner"></a>[function <span class="apidocSignatureSpan">amok.</span>createRunner ()](#apidoc.element.amok.createRunner)
- description and source-code
```javascript
function createRunner() {
  return new Runner();
}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.amok.hotpatch"></a>[function <span class="apidocSignatureSpan">amok.</span>hotpatch ()](#apidoc.element.amok.hotpatch)
- description and source-code
```javascript
function plugin() {
  return function hotpatch(client, runner, done) {
    var cwd = runner.get('cwd');
    if (cwd) {
      cwd = path.resolve(cwd);
    } else {
      cwd = process.cwd();
    }

    var scripts = {};
    var watchers = {};

    client.on('close', function () {
      debug('close');
      scripts = {};

      Object.keys(watchers).forEach(function (key) {
        watchers[key].close();
      });

      watchers = {};
    });

    client.on('connect', function () {
      debug('connect');

      client.debugger.on('clear', function () {
        debug('clear');
        scripts = {};

        Object.keys(watchers).forEach(function (key) {
          watchers[key].close();
        });

        watchers = {};
      });

      client.debugger.on('scriptParse', function (script) {
        debug('parse %s', util.inspect(script));

        var uri = url.parse(script.url);
        var filename = null;
        var sources = runner.get('scripts') || {};

        if (uri.protocol === 'file:') {
          filename = path.normalize(uri.pathname);
          if (filename.match(/^\\[a-zA-Z]:\\/)) {
            filename = filename.slice(1);
          }
        } else if (uri.protocol === 'http:') {
          filename = uri.pathname.slice(1);
          if (sources[path.normalize(filename)]) {
            filename = path.resolve(sources[path.normalize(filename)]);
          } else {
            filename = path.resolve(cwd, filename);
          }
        }

        if (!filename) {
          return;
        }

        scripts[filename] = script;
        if (watchers[filename]) {
          return;
        }

        var dirname = path.dirname(filename);

        debug('watch directory %s', dirname);
        var watcher = fs.watch(dirname);
        watchers[dirname] = watcher;

        var streams = {};
        watcher.on('change', function (event, filename) {
          if (!filename) {
            return;
          }

          filename = path.resolve(dirname, filename);

          var script = scripts[filename];
          if (!script) {
            return;
          }

          debug(event, filename);
          if (streams[filename]) {
            return;
          }

          var source = '';
          var stream = fs.createReadStream(filename);
          streams[filename] = stream;

          stream.setEncoding('utf-8');
          stream.on('data', function(chunk) {
            source += chunk;
          });

          stream.on('end', function() {
            streams[filename] = null;

            if (source.length === 0) {
              return;
            }

            debug('patch script %s (%d bytes) ', script.url, source.length);
            client.debugger.setScriptSource(script, source, function (error, result) {
              if (error) {
                debug('set source error %s', util.inspect(error));
                return client.emit('error', error);
              }

              var detail = JSON.stringify({
                detail: {
                  filename: path.relative(cwd, filename),
                  source: source,
                }
              });

              var cmd = 'var event = new CustomEvent(\'patch\',' +
              detail + ');\nwindow.dispatchEvent(event);';

              debug('evaluate patch event');
              client.runtime.evaluate(cmd, function (error) {
                if (error) {
                  debug('evaluate error %s', util.inspect(error));
                  return client.emit('error', error);
                }
              });
            });
          });
        });
      });

      client.debugger.enable(function (error) {
        if (error) {
          return client.emit('error', error);
        }

        debug('debugger');
      });

      client.runtime.enable(function (error) {
        if (error) {
          return client.emit('error', error);
        }

        debug('runtime');
      });
    });

    debug('done');
    done();
  };
}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.amok.multiplexer"></a>[function <span class="apidocSignatureSpan">amok.</span>multiplexer (port, host, debugPort, debugHost)](#apidoc.element.amok.multiplexer)
- description and source-code
```javascript
function plugin(port, host, debugPort, debugHost) {
  return function multiplexer(client, runner, done) {
    // TODO The meat of this implementation should probaly go into an rdbg adapter module.
    var upstream = {};

    var server = http.createServer();
    server.on('request', function (request, response) {
      var upstreamRequest = http.request({
        port: debugPort,
        hostname: debugHost,
        path: request.url,
      });

      if (request.url == '/json' || request.url === '/json/list') {
        upstreamRequest.on('response', function (upstreamResponse) {
          var data = '';

          upstreamResponse.on('data', function (chunk) {
            data += chunk;
          });

          upstreamResponse.on('end', function () {
            var targets = JSON.parse(data);
            targets.forEach(function (target) {
              var webSocketDebuggerUrl = Object.keys(upstream).filter(function(key) {
                return key.search(target.id);
              })[0];

              if (target.webSocketDebuggerUrl || webSocketDebuggerUrl) {
                target.webSocketDebuggerUrl = url.format({
                  protocol: 'ws',
                  slashes: true,
                  port: port,
                  hostname: host,
                  pathname: '/devtools/page/' + target.id,
                });
              }

              if (target.webSocketDebuggerUrl) {
                target.devtoolsFrontendUrl = util.format('/devtools/inspector.html?ws=%s', target.webSocketDebuggerUrl.slice(5));
              }
            });

            response.end(JSON.stringify(targets, undefined, 2));
          });
        });

        upstreamRequest.end();
      } else {
        upstreamRequest.on('response', function (upstreamResponse) {
          upstreamResponse.pipe(response);
        });

        upstreamRequest.end();
      }
    });

    var socket = ws.createServer({ server: server });
    socket.on('connection', function (connection) {
      var upstreamUrl = url.format({
        protocol: 'ws',
        slashes: true,
        hostname: debugHost,
        port: debugPort,
        pathname: connection.upgradeReq.url,
      });

      if (!upstream[upstreamUrl]) {
        var upstreamConnection = ws.createConnection(upstreamUrl);
        upstream[upstreamUrl] = {
          localId: 0,
          connection: upstreamConnection,
          connections: [connection],
          mappings: {},
          notifications: [],
        };

        upstreamConnection.on('close', function() {
          upstream[upstreamUrl].connections.forEach(function(connection) {
            connection.close();
          });

          delete upstream[upstreamUrl];
        });

        upstreamConnection.on('message', function (data) {
          var message = JSON.parse(data);

          if (message.id === undefined) {
            upstream[upstreamUrl].notifications.push(JSON.parse(data));
            upstream[upstreamUrl].connections.forEach(function (connection) {
              connection.send(data);
            });
          } else {
            var mappings = upstream[upstreamUrl].mappings[message.id];
            message.id = mappings.id;
            mappings.connection.send(JSON.stringify(message));
          }
        });
      } else {
        upstream[upstreamUrl].connections.push(connection);
      }

      connection.notifications = upstream[upstreamUrl].notifications.slice(0);
      connection.upstream = upstream[upstreamUrl];

      connection.on('message', function (data) {
        var upstream = connection.upstream;

        var message = JSON.parse(data);

        if (message.method && message.method.match(/enable$/)) {
          var domain = message.method.split('.')[0];

          connection.notifications = connection.notifications.filter(function (notification) {
            if (notification.method.search(domain)) {
              connection.send(JSON.stringify(notification));
              return false;
            }

            return true;
          });
        }

        var local = upstream.localId++; ...
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.amok.print"></a>[function <span class="apidocSignatureSpan">amok.</span>print (output)](#apidoc.element.amok.print)
- description and source-code
```javascript
function plugin(output) {
  return function print(client, runner, done) {
    client.console.on('data', function (message) {
      output.write(message.text + '\n');
    });

    client.on('connect', function () {
      client.console.enable(function (error) {
        if (error) {
          return client.emit('error', error);
        }
        debug('console');
      });
    });

    debug('ready');
    done();
  };
}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.amok.repl"></a>[function <span class="apidocSignatureSpan">amok.</span>repl (input, output, options)](#apidoc.element.amok.repl)
- description and source-code
```javascript
function plugin(input, output, options) {
  if (typeof options === 'undefined') {
    options = {};
  }

  return function repl(client, runner, done) {
    debug('start');
    var repl = repls.start({
      input: input,
      output: output,
      useColors: options.useColors
    });

    repl.on('exit', function() {
      runner.close();
    });

    repl.eval = function (cmd, context, filename, callback) {
      client.runtime.evaluate(cmd, function (error, output) {
        if (error) {
          return callback(error);
        }

        var result = output.result;
        if (result.type === 'string') {
          return callback(null, result.value);
        } else if (result.type === 'function') {
          var fn = new Function('return ' + result.description.replace('[native code]', '/* remote code */'));
          return callback(null, fn());
        } else if (result.subtype === 'error') {
          return callback(result.description);
        } else if (result.type === 'object') {
          var obj = {};
          return callback(null, obj);
        } else {
          return callback(null, eval(result.value));
        }
      });
    };

    repl.complete = function (line, callback) {
      callback([], line);
    };

    client.console.on('data', function (message) {
      repl.output.clearLine();
      repl.output.cursorTo(0);
      repl.output.write(message.text + '\n');
      repl.prompt(true);
    });

    client.on('connect', function () {
      client.console.enable(function (error) {
        if (error) {
          return client.emit('error', error);
        }

        debug('console enabled');
      });

      client.runtime.enable(function (error) {
        if (error) {
          return client.emit('error');
        }

        debug('runtime');
      });
    });

    debug('ready');
    done();
  };
}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.amok.server"></a>[function <span class="apidocSignatureSpan">amok.</span>server (port, host)](#apidoc.element.amok.server)
- description and source-code
```javascript
function plugin(port, host) {
  return function server(client, runner, done) {
    var server = http.createServer();

    server.once('error', function (error) {
      done(error);
    });

    server.once('listening', function ready() {
      runner.set('url', url.format({
        protocol: 'http',
        port: port,
        hostname: host,
        pathname: '/'
      }));

      debug('ready');
      done();
    });

    runner.once('close', function close() {
      debug('close');
      server.close();
    });

    server.on('request', function handle(request, response) {
      debug('handle %s', request.url);

      var pathname = url.parse(request.url).pathname;
      if (pathname === '/') {
        pathname = '/index.html';
      }

      var scripts = runner.get('scripts') || {};
      var cwd = runner.get('cwd') || process.cwd();
      var filename = scripts[path.normalize(pathname.slice(1))] || path.normalize(pathname.slice(1));

      if (!path.isAbsolute(filename)) {
        filename = path.join(cwd, filename);
      }

      fs.stat(filename, function (error, stat) {
        response.setHeader('content-type', mime.lookup(filename));

        if (error) {
          if (pathname === '/index.html') {
            debug('generate index.html');

            response.write('<!DOCTYPE html><html><head>');
            response.write('<title>' + path.basename(cwd) + '</title>');
            response.write('</head><body>');

            if (scripts) {
              Object.keys(scripts).forEach(function (src) {
                response.write('<script src="' + src + '"></script>');
              });
            }

            return response.end('</body></html>');
          } else if (pathname === '/favicon.ico') {
            debug('generate favicon.ico');
            return response.end();
          } else {
            debug('404 %s', pathname);
            response.statusCode = 404;
            return response.end('404');
          }
        }

        if (stat.isFile()) {
          debug('stream %s', filename);
          fs.createReadStream(filename).pipe(response);
        } else {
          debug('403 %s', pathname);
          response.statusCode = 403;
          return response.end('403');
        }
      });
    });

    server.listen(port, host);
  };
}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.amok.watch"></a>[function <span class="apidocSignatureSpan">amok.</span>watch (glob)](#apidoc.element.amok.watch)
- description and source-code
```javascript
function plugin(glob) {
  return function watch(client, runner, done) {
    var cwd = runner.get('cwd');
    if (cwd) {
      cwd = path.resolve(cwd);
    } else {
      cwd = process.cwd();
    }

    debug('watch %s', glob);

    var watcher = chokidar.watch(glob, {
      ignoreInitial: true,
      cwd: cwd,
    });

    watcher.once('error', function error(error) {
      debug('error %s', util.inspect(error));
      done(error);
    });

    watcher.once('ready', function ready() {
      debug('ready');
      done();
    });

    runner.once('close', function close() {
      debug('close');
      watcher.close();
    });

    client.on('close', function () {
      watcher.removeAllListeners('all');
    });

    client.on('connect', function () {
      watcher.on('all', function emit(event, filename) {
        debug('%s %s', event, filename);

        var detail = JSON.stringify({
          detail: {
            filename: url.resolve('/', filename).slice(1)
          }
        });

        var cmd = 'var event = new CustomEvent(\'' + event + '\',' +
          detail + ');\nwindow.dispatchEvent(event);';

        client.runtime.evaluate(cmd, function (error, result) {
          if (error) {
            debug('error %s', error.description);
            return client.emit('error', error);
          }
        });
      });

      client.runtime.enable(function (error) {
        if (error) {
          return client.emit('error', error);
        }

        debug('runtime');
      });
    });
  };
}
```
- example usage
```shell
...
if (watchers[filename]) {
  return;
}

var dirname = path.dirname(filename);

debug('watch directory %s', dirname);
var watcher = fs.watch(dirname);
watchers[dirname] = watcher;

var streams = {};
watcher.on('change', function (event, filename) {
  if (!filename) {
    return;
  }
...
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
