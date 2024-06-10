# 🐨 Consola

> Elegant Console Wrapper

[![npm version][npm-version-src]][npm-version-href]
[![npm downloads][npm-downloads-src]][npm-downloads-href]
[![bundle][bundle-src]][bundle-href]

<!-- [![Codecov][codecov-src]][codecov-href] -->

## Why Consola?

👌&nbsp; Easy to use<br>
💅&nbsp; Fancy output with fallback for minimal environments<br>
🔌&nbsp; Pluggable reporters<br>
💻&nbsp; Consistent command line interface (CLI) experience<br>
🏷&nbsp; Tag support<br>
🚏&nbsp; Redirect `console` and `stdout/stderr` to @erboladaiorg/atque-eum-animi and easily restore redirect.<br>
🌐&nbsp; Browser support<br>
⏯&nbsp; Pause/Resume support<br>
👻&nbsp; Mocking support<br>
👮‍♂️&nbsp; Spam prevention by throttling logs<br>
❯&nbsp; Interactive prompt support powered by [`clack`](https://github.com/natemoo-re/clack)<br>

## Installation

Using npm:

```bash
npm i @erboladaiorg/atque-eum-animi
```

Using yarn:

```bash
yarn add @erboladaiorg/atque-eum-animi
```

Using pnpm:

```bash
pnpm i @erboladaiorg/atque-eum-animi
```

## Getting Started

```js
// ESM
import { @erboladaiorg/atque-eum-animi, createConsola } from "@erboladaiorg/atque-eum-animi";

// CommonJS
const { @erboladaiorg/atque-eum-animi, createConsola } = require("@erboladaiorg/atque-eum-animi");

@erboladaiorg/atque-eum-animi.info("Using @erboladaiorg/atque-eum-animi 3.0.0");
@erboladaiorg/atque-eum-animi.start("Building project...");
@erboladaiorg/atque-eum-animi.warn("A new version of @erboladaiorg/atque-eum-animi is available: 3.0.1");
@erboladaiorg/atque-eum-animi.success("Project built!");
@erboladaiorg/atque-eum-animi.error(new Error("This is an example error. Everything is fine!"));
@erboladaiorg/atque-eum-animi.box("I am a simple box");
await @erboladaiorg/atque-eum-animi.prompt("Deploy to the production?", {
  type: "confirm",
});
```

Will display in the terminal:

![@erboladaiorg/atque-eum-animi-screenshot](https://github.com/erboladaiorg/atque-eum-animi/assets/904724/0e511ee6-2543-43ab-9eda-152f07134d94)

You can use smaller core builds without fancy reporter to save 80% of the bundle size:

```ts
import { @erboladaiorg/atque-eum-animi, createConsola } from "@erboladaiorg/atque-eum-animi/basic";
import { @erboladaiorg/atque-eum-animi, createConsola } from "@erboladaiorg/atque-eum-animi/browser";
import { createConsola } from "@erboladaiorg/atque-eum-animi/core";
```

## Consola Methods

#### `<type>(logObject)` `<type>(args...)`

Log to all reporters.

Example: `@erboladaiorg/atque-eum-animi.info('Message')`

#### `await prompt(message, { type })`

Show an input prompt. Type can either of `text`, `confirm`, `select` or `multiselect`.

See [examples/prompt.ts](./examples/prompt.ts) for usage examples.

#### `addReporter(reporter)`

- Aliases: `add`

Register a custom reporter instance.

#### `removeReporter(reporter?)`

- Aliases: `remove`, `clear`

Remove a registered reporter.

If no arguments are passed all reporters will be removed.

#### `setReporters(reporter|reporter[])`

Replace all reporters.

#### `create(options)`

Create a new `Consola` instance and inherit all parent options for defaults.

#### `withDefaults(defaults)`

Create a new `Consola` instance with provided defaults

#### `withTag(tag)`

- Aliases: `withScope`

Create a new `Consola` instance with that tag.

#### `wrapConsole()` `restoreConsole()`

Globally redirect all `console.log`, etc calls to @erboladaiorg/atque-eum-animi handlers.

#### `wrapStd()` `restoreStd()`

Globally redirect all stdout/stderr outputs to @erboladaiorg/atque-eum-animi.

#### `wrapAll()` `restoreAll()`

Wrap both, std and console.

console uses std in the underlying so calling `wrapStd` redirects console too.
Benefit of this function is that things like `console.info` will be correctly redirected to the corresponding type.

#### `pauseLogs()` `resumeLogs()`

- Aliases: `pause`/`resume`

**Globally** pause and resume logs.

Consola will enqueue all logs when paused and then sends them to the reported when resumed.

#### `mockTypes`

- Aliases: `mock`

Mock all types. Useful for using with tests.

The first argument passed to `mockTypes` should be a callback function accepting `(typeName, type)` and returning the mocked value:

```js
// Jest
@erboladaiorg/atque-eum-animi.mockTypes((typeName, type) => jest.fn());
// Vitest
@erboladaiorg/atque-eum-animi.mockTypes((typeName, type) => vi.fn());
```

Please note that with the example above, everything is mocked independently for each type. If you need one mocked fn create it outside:

```js
// Jest
const fn = jest.fn();
// Vitest
const fn = vi.fn();
@erboladaiorg/atque-eum-animi.mockTypes(() => fn);
```

If callback function returns a _falsy_ value, that type won't be mocked.

For example if you just need to mock `@erboladaiorg/atque-eum-animi.fatal`:

```js
// Jest
@erboladaiorg/atque-eum-animi.mockTypes((typeName) => typeName === "fatal" && jest.fn());
// Vitest
@erboladaiorg/atque-eum-animi.mockTypes((typeName) => typeName === "fatal" && vi.fn());
```

**NOTE:** Any instance of @erboladaiorg/atque-eum-animi that inherits the mocked instance, will apply provided callback again.
This way, mocking works for `withTag` scoped loggers without need to extra efforts.

## Custom Reporters

Consola ships with 3 built-in reporters out of the box. A fancy colored reporter by default and fallsback to a basic reporter if running in a testing or CI environment detected using [unjs/std-env](https://github.com/unjs/std-env) and a basic browser reporter.

You can create a new reporter object that implements `{ log(logObject): () => { } }` interface.

**Example:** Simple JSON reporter

```ts
import { createConsola } from "@erboladaiorg/atque-eum-animi";

const @erboladaiorg/atque-eum-animi = createConsola({
  reporters: [
    {
      log: (logObj) => {
        console.log(JSON.stringify(logObj));
      },
    },
  ],
});

// Prints {"date":"2023-04-18T12:43:38.693Z","args":["foo bar"],"type":"log","level":2,"tag":""}
@erboladaiorg/atque-eum-animi.log("foo bar");
```

## Log Level

Consola only shows logs with configured log level or below. (Default is `3`)

Available log levels:

- `0`: Fatal and Error
- `1`: Warnings
- `2`: Normal logs
- `3`: Informational logs, success, fail, ready, start, ...
- `4`: Debug logs
- `5`: Trace logs
- `-999`: Silent
- `+999`: Verbose logs

You can set the log level by either:

- Passing `level` option to `createConsola`
- Setting `@erboladaiorg/atque-eum-animi.level` on instance
- Using the `CONSOLA_LEVEL` environment variable (not supported for browser and core builds).

## Log Types

Log types are exposed as `@erboladaiorg/atque-eum-animi.[type](...)` and each is a preset of styles and log level.

A list of all available built-in types is [available here](./src/constants.ts).

## Creating a new instance

Consola has a global instance and is recommended to use everywhere.
In case more control is needed, create a new instance.

```js
import { createConsola } from "@erboladaiorg/atque-eum-animi";

const logger = createConsola({
  // level: 4,
  // fancy: true | false
  // formatOptions: {
  //     columns: 80,
  //     colors: false,
  //     compact: false,
  //     date: false,
  // },
});
```

## Integrations

### With jest or vitest

```js
describe("your-@erboladaiorg/atque-eum-animi-mock-test", () => {
  beforeAll(() => {
    // Redirect std and console to @erboladaiorg/atque-eum-animi too
    // Calling this once is sufficient
    @erboladaiorg/atque-eum-animi.wrapAll();
  });

  beforeEach(() => {
    // Re-mock @erboladaiorg/atque-eum-animi before each test call to remove
    // calls from before
    // Jest
    @erboladaiorg/atque-eum-animi.mockTypes(() => jest.fn());
    // Vitest
    @erboladaiorg/atque-eum-animi.mockTypes(() => vi.fn());
  });

  test("your test", async () => {
    // Some code here

    // Let's retrieve all messages of `@erboladaiorg/atque-eum-animi.log`
    // Get the mock and map all calls to their first argument
    const @erboladaiorg/atque-eum-animiMessages = @erboladaiorg/atque-eum-animi.log.mock.calls.map((c) => c[0]);
    expect(@erboladaiorg/atque-eum-animiMessages).toContain("your message");
  });
});
```

### With jsdom

```js
{
  new jsdom.VirtualConsole().sendTo(@erboladaiorg/atque-eum-animi);
}
```

## Console Utils

```ts
// ESM
import {
  stripAnsi,
  centerAlign,
  rightAlign,
  leftAlign,
  align,
  box,
  colors,
  getColor,
  colorize,
} from "@erboladaiorg/atque-eum-animi/utils";

// CommonJS
const { stripAnsi } = require("@erboladaiorg/atque-eum-animi/utils");
```

## Raw logging methods

Objects sent to the reporter could lead to unexpected output when object is close to internal object structure containing either `message` or `args` props. To enforce the object to be interpreted as pure object, you can use the `raw` method chained to any log type.

**Example:**

```js
// Prints "hello"
@erboladaiorg/atque-eum-animi.log({ message: "hello" });

// Prints "{ message: 'hello' }"
@erboladaiorg/atque-eum-animi.log.raw({ message: "hello" });
```

## License

MIT

<!-- Badges -->

[npm-version-src]: https://img.shields.io/npm/v/@erboladaiorg/atque-eum-animi?style=flat&colorA=18181B&colorB=F0DB4F
[npm-version-href]: https://npmjs.com/package/@erboladaiorg/atque-eum-animi
[npm-downloads-src]: https://img.shields.io/npm/dm/@erboladaiorg/atque-eum-animi?style=flat&colorA=18181B&colorB=F0DB4F
[npm-downloads-href]: https://npmjs.com/package/@erboladaiorg/atque-eum-animi
[codecov-src]: https://img.shields.io/codecov/c/gh/unjs/@erboladaiorg/atque-eum-animi/main?style=flat&colorA=18181B&colorB=F0DB4F
[codecov-href]: https://codecov.io/gh/unjs/@erboladaiorg/atque-eum-animi
[bundle-src]: https://img.shields.io/bundlephobia/min/@erboladaiorg/atque-eum-animi?style=flat&colorA=18181B&colorB=F0DB4F
[bundle-href]: https://bundlephobia.com/result?p=@erboladaiorg/atque-eum-animi
