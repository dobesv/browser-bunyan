# bunyan Changelog

## bunyan 0.6.0

- Add 'pid' automatic log record field.


## bunyan 0.5.3

- Add 'client_req' (HTTP client request) standard formatting in `bunyan` CLI
  default output.
- Improve `bunyan` CLI default output to include *all* log record keys. Unknown keys
  are either included in the first line parenthetical (if short) or in the indented
  subsequent block (if long or multiline).


## bunyan 0.5.2

- [issue #3] More type checking of `new Logger(...)` and `log.child(...)`
  options.
- Start a test suite.


## bunyan 0.5.1

- [issue #2] Add guard on `JSON.stringify`ing of log records before emission.
  This will prevent `log.info` et al throwing on record fields that cannot be
  represented as JSON. An error will be printed on stderr and a clipped log
  record emitted with a 'bunyanMsg' key including error details. E.g.:
  
        bunyan: ERROR: could not stringify log record from /Users/trentm/tm/node-bunyan/examples/unstringifyable.js:12: TypeError: Converting circular structure to JSON
        {
          "name": "foo",
          "hostname": "banana.local",
          "bunyanMsg": "bunyan: ERROR: could not stringify log record from /Users/trentm/tm/node-bunyan/examples/unstringifyable.js:12: TypeError: Converting circular structure to JSON",
        ...

  Some timing shows this does effect log speed: 
  
        $ node tools/timeguard.js     # before
        Time try/catch-guard on JSON.stringify:
         - log.info:  0.07365ms per iteration
        $ node tools/timeguard.js     # after
        Time try/catch-guard on JSON.stringify:
         - log.info:  0.07368ms per iteration


## bunyan 0.5.0

- Use 10/20/... instead of 1/2/... for level constant values. Ostensibly this
  allows for intermediary levels from the defined "trace/debug/..." set.
  However, that is discouraged. I'd need a strong user argument to add
  support for easily using alternative levels. Consider using a separate
  JSON field instead.
- s/service/name/ for Logger name field. "service" is unnecessarily tied
  to usage for a service. No need to differ from log4j Logger "name".
- Add `log.level(...)` and `log.levels(...)` API for changing logger stream
  levels.
- Add `TRACE|DEBUG|INFO|WARN|ERROR|FATAL` level constants to exports.
- Add `log.info(err)` special case for logging an `Error` instance. For
  example `log.info(new TypeError("boom")` will produce:

        ...
        "err": {
          "message": "boom",
          "name": "TypeError",
          "stack": "TypeError: boom\n    at Object.<anonymous> ..."
        },
        "msg": "boom",
        ...


## bunyan 0.4.0

- Add `new Logger({src: true})` config option to have a 'src' attribute be
  automatically added to log records with the log call source info. Example:

        "src": {
          "file": "/Users/trentm/tm/node-bunyan/examples/src.js",
          "line": 20,
          "func": "Wuzzle.woos"
        },


## bunyan 0.3.0

- `log.child(options[, simple])` Added `simple` boolean arg. Set `true` to
  assert that options only add fields (no config changes). Results in a 10x
  speed increase in child creation. See "tools/timechild.js". On my Mac, 
  "fast child" creation takes about 0.001ms. IOW, if your app is dishing
  10,000 req/s, then creating a log child for each request will take
  about 1% of the request time.
- `log.clone` -> `log.child` to better reflect the relationship: streams and
  serializers are inherited. Streams can't be removed as part of the child
  creation. The child doesn't own the parent's streams (so can't close them).
- Clean up Logger creation. The goal here was to ensure `log.child` usage
  is fast. TODO: measure that.
- Add `Logger.stdSerializers.err` serializer which is necessary to get good
  Error object logging with node 0.6 (where core Error object properties
  are non-enumerable).


## bunyan 0.2.0

- Spec'ing core/recommended log record fields.
- Add `LOG_VERSION` to exports.
- Improvements to request/response serializations.


## bunyan 0.1.0

First release.
