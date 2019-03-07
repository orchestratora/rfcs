# Function decoding in configuration

## Summary

Be able to decode serialized JS functions in configurations.

## Motivation

Have the ability to accept functions from user-provided configuration that needed to be serialized to JSON and retrieved from network.

## Detailed Explanation

Create a type that can decode stringified (serialised) function into valid JS function for any configuration property.

## Rationale and Alternatives

-

## Implementation

### Behavior

- Custom `io-ts` type `FunctionFromString` that will accept 1 of the next inputs:
  1. serialised function
  2. object `{args: string[], body: string}`, where `args` is a list of function's arguments and `body` is function's body

- If input is invalid - validation will fail

- If input is accepted then type will, in case:
  1. Try to parse input string to extract function's arguments and body and then construct new `Function` based on that info.
  In case of failure - validation will fail.
  2. construct new `Function` based on input object.

- When function is constructed it's arguments should be resolved by the map `INJECTOR_MAP` via local `Injector` and bound to function.
- **NEW** Arguments whose name starts with `$` must be:
  - Skipped by injector resolution algorithm
  - Moved to the end of the arguments list saving their relative order to same arguments (`arg1, $arg2, arg3, $arg4` => `arg1, arg3, $arg2, $arg4`) so that they will not be bound and accepted from caller.
- `INJECTOR_MAP` is a `multi provider` of type `{[key: string]: Type<any> | InjectionToken<any>}`.
  In this way any external package can provide custom extra mappings for their functions to inject.
- If some arguments were not resolved by `Injector` we will use default value if provided, otherwise the error will be thrown.
- Errors from function will be captured, wrapped in `FunctionError` and handled via `ErrorStrategy`.

_Extra:_
- `INJECTOR_MAP` by default will contain mappings for core services and components:
  - `getInjector()` - will return local `Injector` with mapping capability built-in (same instance used to resolve function's arguments)
  - `InjectFlags` - will return `InjectFlags` enum from `@angular/core` (useful  to use with `getInjector().get(...)`)
  - `getComponent()` - will return closest dynamic component instance
  - `getConfiguration()` - will return closest dynamic component configuration object
  - `isConfigurationValid()` - will return boolean telling the validation status of configuration of closest dynamic component
  - `getRenderItem()` - will return closest `RenderItemComponent` instance

### Configuration Options Decorator

#### `@OptionFunction`
```ts
@OptionFunction(customInjector?: CustomInjectorFactory)
```
> - Will enable validation as well as decoding of serialised function.
> - Optional `CustomInjectorFactory` can be supplied to provide custom arguments resolution of specific function's arguments.

#### `CustomInjectorFactory`
```ts
import { Injector } from '@angular/core';
type CustomInjectorFactory = (parentInjector: Injector) => Injector;
```
> - Accepts parent injector and returns configured new `Injector`

### Example

```ts
// Define config with function property
class MyConfig {
  @OptionFunction()
  fn: Function;
}

// Decode and validate config at runtime
const myConfig = this.configurationService.decode(
  MyConfig,
  { fn: '() => "hi from custom function!"' },
  customInjector // This must be `local Injector` that will be used to resolve any function's arguments
);

// Use decoded config
myConfig.fn(); // Will return `hi from custom function!`
```

## Prior Art

None

## Links

- Migrated from [#28](https://github.com/orchestratora/orchestrator/issues/28)
- Implemented in [#29](https://github.com/orchestratora/orchestrator/pull/29)
