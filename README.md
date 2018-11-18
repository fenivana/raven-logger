# raven-logger
Logging messages to stdout/stderr as well as reporting to Sentry in one command.

## Usage
```js
const Logger = require('raven-logger')
const sentry = require('@sentry/node')

sentry.init({ dsn: 'https://<key>@sentry.io/<project>' })

const logger = new Logger({ sentry })

async function main() {
  try {
    const data = require('fs').readFileSync('foo.txt')
  } catch (e) {
    // You can provide the timestamp and eventId to the user,
    // so he/she can give feedback with these information to help locating the issue.
    const { timestamp, eventId } = logger.error(e)
  }
}

main()
```

## Constructor
```js
new Logger({ sentry, timezone, logLevel = 'debug', reportLevel: 'info', debugTrace = true })
```

#### Arguments:
`sentry`: Object, the [sentry module](https://www.npmjs.com/package/@sentry/node). Optional.
  If not provided, logs won't be send to sentry.

`timezone`: String | Boolean. Optional. Timezone of timestamp. Defaults to `true`.
  * `String`: [List of tz database time zones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones).
  * `true`: use system timezone.
  * `false`: use UTC timezone.

`logLevel`: Optional. What level of logs should output to stdout or stderr.
  Enabling logging at a given level also enables logging at all higher levels.  
  Levels from high to low: `critical`, `fatal`, `error`, `warning`, `info`, `log`, `debug`.  
  Defaults to `debug`.

`reportLevel`: Optional. What level of logs should report to sentry server.
  Enabling reporting at a given level alsa enables reporting at all higher levels.  
  Defaults to `info`.

`debugTrace`: Boolean. Optional. Whether to log trace at `debug` level. Defaults to `true`.

You can change the settings through theses properties after initialization:
```js
logger.sentry
logger.timezone
logger.logLevel
logger.reportLevel
logger.debugTrace
```

## Logging methods
* logger.debug(...messages)
* logger.log(...messages)
* logger.info(...messages)
* logger.warn(...messages)
* logger.error(...messages)
* logger.fatal(...messages)
* logger.critical(...messages)

### Arguments:
`...message`: Any type. Final output string will be `util.format(...messages)`, same as `console.log(...messages)`.

### Returns:
```js
{ timestamp, eventId }
```

The logging format is `[timestamp][eventId][logLevel] messages`.

`timestamp`: String. The timestamp prepending the log message, in [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) format.
  If the log level is disabled logging, nothing will be logged, so `timestamp` and `eventId` will be empty string.

`eventId`: String. The evend ID prepending the log message.
  If sentry is set, the value is generated by sentry. Else it is generated from random bytes.
  If the log level is disabled reporting, `eventId` will be empty string.
