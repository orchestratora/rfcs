# Configuration Metadata

## Summary

Allow to collect metadata of dynamic component's configuration and access it in runtime for validation and other purposes.

## Motivation

To be able to validate user-provided configuration against expected-by-component configuration.

As we advance this library we will need to have more information about component's config.
For example if we want to render GUI to configure components visually we will need to display all the configuration in a user-friendly way to setup a component.
So the idea is to use decorators to describe metadata about the configuration and validate it in runtime.

## Detailed Explanation

We can define special decorators that will capture needed metadata and than use that info at runtime to render controls and validate against received user config.

These decorators will be Property Decorators applied on props in configuration classes.

Type information may be accessed via ReflectMetadata API provided under TypeScript's design:type key (ref: https://www.typescriptlang.org/docs/handbook/decorators.html at the end of the page).

SIDE NOTE: The type information right now is limited and there is some early process on that direction to allow to capture full type info https://gist.github.com/rbuckton/4b8df4908aad49aac212.

### Example
```ts
@Injectable()
export class LayoutFlatConfig {
  @OptionRequired() // This is how they will be applied
  wrap: string;
}
```

## Rationale and Alternatives

[`io-ts`](https://github.com/gcanti/io-ts) has runtime validation and decoding with great integration for TypeScript that we can use here.

But we will need to automatically convert our class configurations into `io-ts codec`s.

## Implementation

### Runtime types

We will define runtime types that will better represent types:

- `TypeOf<T>` - any runtime type
  Constructor: `new TypeOf<T>(type: T)`
- `ArrayOf<T>` - array of some type
  Constructor: `new ArrayOf<T>(type: T)`
- `AnyOf<T>` - set of allowed types (unions)
  Constructor: `new AnyOf<T>(types: T[])`

We might use [io-ts](https://github.com/gcanti/io-ts) for runtime validations.
It's pretty good at describing types and it already does validation + decoding/encoding.

In this case we will generate at runtime `io-ts` codes based on our configuration and then validate user configs by running validations of generated `io-ts` codes.

### Behavior

- Every failed validation at runtime will throw an `InvalidConfigurationError`.
- Option's type will be retrieved and evaluated from `design:type` metadata by default.
  Validation will take place if type is:
  - `Primitive`, `Function`, `Enum`, `Class`, `Object` (as `TypeOf<T>`)
  - `Array` of above types (as `ArrayOf<T>`)
  - `AnyOf<T>` of above types
- Validation will be skipped for non-defined value (`null` or `undefined`)
- Validation of `Primitive instances` will be done via direct comparison `===`.
- Validation of `Primitive` and `Function` type will be done via `instanceof`.
- Validation of `Enum` type will be done by checking if value listed in enum.
- Validation of `Object` type  will be done for every property recursively.
  Constraints for `Object` types used in COD:
  - every property must be defined
- Validation of `Class` type will be done by it's instantiation and then as `Object` type.
- Validation of `Array` type will be done for each value as described above.
  If any element in array non-defined then validation will fail.
- Validation of `AnyOf` type will be done with each element and value as described above
and fail if every validation on every element failed.

#### Configuration Errors

When `InvalidConfigurationError` will be thrown we will capture it.

We will provide `ErrorStrategy` that will deal with every `InvalidConfigurationError`.

We will define the following strategies:
- `ThrowErrorStrategy` _(default)_ - will simply re-throw the error forcing the whole process to stop and print error to the console.
- `SuppressErrorStrategy` - will not do anything thus suppressing any error.

Users will be able to define their own strategy and provide it.

### Configuration Options Decorators (COD):

Every COD will be prefixed with word `Option` for easier recognition.

#### `@OptionRequired`
 ```ts
@OptionRequired()
```
> - Instructs that specific config option is required.
> - Validation will fail if value is set to `null` or `undefined`.

#### `@OptionType`
```ts
@OptionType(type: any | any[] | AnyOf<any>)
```
> - Provide custom runtime type of the option if default `design:type` metadata is not enough.
> - `type` cannot be `Interface` since they are removed from the runtime.
> - It will override default type from `design:type` metadata.

#### `@OptionRange`
```ts
@OptionRange(min: number, max: number, step?: number)
```
> - Instructs that specific config option is a range between `min` and `max`. It's runtime type will be set to `Number` overriding other instructions.
> - `step` will be used to control the slider in the GUI for configuration which by default is `1`.
> - Validation will fail if value is beyond the min/max range.

#### `@OptionAllowedValues`
```ts
@OptionAllowedValues<T>(values: T[])
```
> - Instructs that specific config option value must be restricted to the specified list. For arrays will validate it's elements.
> - Validation will fail if value is not one of specified in `values`.
> - It will set type to `AnyOf<T>`.

#### `@OptionInteger`
```ts
@OptionInteger()
```
> - Instructs that specific config option value must be an integer.
> - Validation will fail if value is not of type `number` or is not Integer.

#### `@Option`
```ts
@Option(options: Options)
```
> - Shortcut to setup every possible option in one decorator.
> - `Options` here will allow to set properties like `required`, `range`, `integer` etc.

## Prior Art

We will leverage `io-ts` for performing validation/decoding.

## Links

- Migrated from [#25](https://github.com/orchestratora/orchestrator/issues/25)
- Implemented in [#26](https://github.com/orchestratora/orchestrator/pull/26)
