<div align="center">
  <img src="https://res.cloudinary.com/adonisjs/image/upload/q_100/v1564392111/adonis-banner_o9lunk.png" width="600px">
</div>

# Env
[![circleci-image]][circleci-url] [![npm-image]][npm-url] ![][typescript-image] [![license-image]][license-url]

Environment variables parser and reader for Node.js. This module parses the raw string in dotfile format and set `process.env` variables from it. Also, you can make use of bash like syntax for [variable interpolation](#variable-interpolation).

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
## Table of contents

- [Usage](#usage)
- [Usage with AdonisJs](#usage-with-adonisjs)
- [Casting values](#casting-values)
- [Variable interpolation](#variable-interpolation)
  - [Following will fail](#following-will-fail)
  - [Do this instead](#do-this-instead)
  - [Escape characters](#escape-characters)
- [API](#api)
- [Maintainers](#maintainers)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Usage
Install the package from npm as follows:

```sh
npm i @adonisjs/env

# yarn
yarn add @adonisjs/env
```

and then use it as follows

```ts
import { env } from '@adonisjs/env/build/standalone'

env.process(`
  PORT=3333
  HOST=127.0.0.1
`)
```

and then read the values as follows

```ts
env.get('PORT')

// or
process.env.PORT
```

The `Env.process` method **will not overwrite** the existing environment variables (that is how it should be). However, if you want to overwrite existing values, maybe inside testing environment, then you can call the `process` method as follows:

```ts
env.process('PORT=3333', true)
```

## Usage with AdonisJs
The `@adonisjs/core` includes this module and hence there is no need to install it seperately. However, here are the instructions to setup the provider.

```ts
export const providers = [
  '@adonisjs/env/build/providers/EnvProvider',
]
```

After this, you have to register the typings file inside `files` array for Typescript to pick the ambient module.

> All this hassle is required, since this module is never meant to be installed standalone.

**tsconfig.json**
```json
{
  "files": ["./node_modules/@adonisjs/env/build/adonis-typings/env.d.ts"]
}
```

The `EnvProvider` will look for `.env` file is the project root and parses it's content automatically. When not using this with AdonisJs, then you need to perform this process by yourself. 

## Casting values
The values saved as environment variables are always string. However, this module does the values casting for you, when you read them using `Env.get`.

```ts
env.process(`
  CACHE_VIEWS=false
`)

process.env.CACHE_VIEWS // string
env.get('CACHE_VIEWS') // boolean
```

Following values are casted

| .env Value | Casted value |
|------------|--------------|
| `'null'` | `null` |
| `'true'` | `true` |
| `'1'` | `true` |
| `'false'` | `false` |
| `'0'` | `false` |

## Variable interpolation
You can also reference environment variables by using bash like interpolation syntax.

```ts
env.process(`
  HOST=localhost
  PORT=3333
  URL=$HOST:$PORT
`)
```

All `letter`, `numbers` and `_` after the dollar sign will be considered as a variable reference. Any other characters apart from the above mentioned aren't allowed. However, you can wrap your variable substitution inside curly braces `{}` when using characters other than the whitelisted one's.

### Following will fail
The following reference to `$REDIS-USER` will fail, since the parser will stop at `-` and consider `USER` as a static string.

```ts
env.process(`
  REDIS-USER=foo
  REDIS-URL=localhost@$REDIS-USER
`)
```

### Do this instead
The usage of curly braces `{}` tells the parser to parse until the closing brace.

```ts
env.process(`
  REDIS-USER=foo
  REDIS-URL=localhost@${REDIS-USER}
`)
```

### Escape characters
Quite often, you will have strings where you want the `$` dollar character to be considered as a literal value. In that case you can escape the character as follows.

```ts
env.process(`
  PASSWORD=pa\\$\\$word
`)

process.env.PASSWORD // pa$$word
```

> **DO NOTE**: The usage of double escape `\\` is required when you are typing the string directly in Javascript, since the first escape is swallowed by JS. However, if you are writing the enviornment variables inside the `.env` file, then a single escape `\` is required.

## API
Following are the autogenerated files via Typedoc

* [API](docs/README.md)

## Maintainers
[Harminder virk](https://github.com/thetutlage)

[circleci-image]: https://img.shields.io/circleci/project/github/adonisjs/env/master.svg?style=for-the-badge&logo=circleci
[circleci-url]: https://circleci.com/gh/adonisjs/env "circleci"

[npm-image]: https://img.shields.io/npm/v/@adonisjs/env.svg?style=for-the-badge&logo=npm
[npm-url]: https://npmjs.org/package/@adonisjs/env "npm"

[typescript-image]: https://img.shields.io/badge/Typescript-294E80.svg?style=for-the-badge&logo=typescript

[license-url]: LICENSE.md
[license-image]: https://img.shields.io/aur/license/pac.svg?style=for-the-badge
