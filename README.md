<div align="center">
  <img src="https://res.cloudinary.com/adonisjs/image/upload/q_100/v1564392111/adonis-banner_o9lunk.png" width="600px">
</div>

# Event emitter
> Async event emitter with support for typed events

[![circleci-image]][circleci-url] [![npm-image]][npm-url] ![][typescript-image] [![license-image]][license-url]

This module is extracted from [AdonisJs](https://adonisjs.com). It is an Async event emitter with support for Typed events when using typescript.

## Features
- Asynchronous
- Support for Typed events (when using Typescript)
- Comes with fake emitter for testing

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
## Table of contents

- [Usage](#usage)
- [Using with AdonisJs](#using-with-adonisjs)
- [Asynchronous](#asynchronous)
- [Typed events](#typed-events)
- [Fake emitter](#fake-emitter)
    - [Application code](#application-code)
    - [Test](#test)
- [AdonisJs usage](#adonisjs-usage)
- [API](#api)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Usage
Install the package from npm registry as follows:

```sh
npm i @adonisjs/events

# yarn
yarn add @adonisjs/events
```

And then use it as follows:

```ts
import { Emitter } from '@adonisjs/events/build/standalone'
const emitter = new Emitter()

emitter.on('signup', async (data) => {
  // 
})

emitter.emit('signup', { id: 1, email: 'foo@bar.com' })
```

## Using with AdonisJs
The `@adonisjs/core` module includes this module by default. However, here's how you can set it up manually.

```ts
const providers = [
  '@adonisjs/events'
]
```

And then also register the typings file inside `tsconfig.json` file.

```json
{
  "files": ["./node_modules/@adonisjs/events/build/adonis-typings/events.d.ts"]
}
```

## Asynchronous
Node.js inbuilt event emitter is synchronous and blocks the event loop while emitting events. On the other hand, this module relies on [emittery](https://github.com/sindresorhus/emittery) to emit events asynchronously.

## Typed events
When using Typescript, you can attach data types to a event name and the Typescript compiler will complain, if types aren't the same.

```ts
import { Emitter } from '@adonisjs/events/build/standalone'

type EventsMap = {
  'new:user': { id: number },
}

const emitter = new Emitter<EventsMap>()

emitter.emit('new:user', { id: 1 }) // works
emitter.emit('new:user', 1) // compiler error
```

Just like the `emit` method, all other methods enforce types on typed events.

```ts
const emitter = new Emitter<EventsMap>()

emitter.on('new:user', (user) => {
})

emitter.once('new:user', (user) => {
})
```

## Fake emitter
When writing tests, you may want to suppress the events and instead run assertions to ensure that your code is emitting right events with correct data.

You can do this by using the `FakeEmitter` instance during tests.

#### Application code
```ts
import { EmitterContract } from '@adonisjs/events/build/standalone'

export default class UserController {
  constructor (protected emitter) {}

  create () {
    const user = makeSomeDbCall()
    this.emitter.emit('new:user', user)
  }
}
```

When writing tests, you can pass the `FakeEmitter`, instead of the real emitter instance.

#### Test

```ts
import { FakeEmitter } from '@adonisjs/events/build/standalone'
import UserController from './UserController'

const emitter = new FakeEmitter()
new UserController(emitter).create()

assert.deepEqual(emitter.events, [{
  event: 'new:user',
  data: user,
}])

// Clear stored events inside memory
emitter.clear()
```

## AdonisJs usage
When using with AdonisJs, you can also define listeners as a reference to the IoC container binding. For example:

```ts
// Add event listener
Event.on('new:user', 'User.signup')

// Remove event listener
Event.off('new:user', 'User.signup')
```

AdonisJs will make an instance of `app/Listeners/User.ts` file and will call the signup method on it. This keeps your events file clean, since you can abstract the listeners code to dedicated files.

Also, you can define a custom namespace to lookup listeners from.

```ts
Event.namespace('App/MyListeners')
```

Now AdonisJs will look inside the `MyListeners` directory vs `Listeners`.

## API
Following are the autogenerated files via Typedoc

* [API](docs/README.md)

[circleci-image]: https://img.shields.io/circleci/project/github/adonisjs/events/master.svg?style=for-the-badge&logo=circleci
[circleci-url]: https://circleci.com/gh/adonisjs/events "circleci"

[typescript-image]: https://img.shields.io/badge/Typescript-294E80.svg?style=for-the-badge&logo=typescript
[typescript-url]:  "typescript"

[npm-image]: https://img.shields.io/npm/v/@adonisjs/events.svg?style=for-the-badge&logo=npm
[npm-url]: https://npmjs.org/package/@adonisjs/events "npm"

[license-image]: https://img.shields.io/npm/l/@adonisjs/events?color=blueviolet&style=for-the-badge
[license-url]: LICENSE.md "license"
