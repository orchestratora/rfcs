# Auto-merge default configurations for dynamic components

## Summary

Automatically merge default component configuration with user-provided one.

## Motivation

Most of the dynamic components will have it's own configuration and some of those will have default values.

It will be easier if orchestrator will take care of merging default configuration with user-provided configuration.

## Detailed Explanation

Every dynamic component should be able to link it's configuration with itself so orchestrator can process it and automatically
merge with user-provided configuration at runtime and then provide result to dynamic component.

## Rationale and Alternatives

-

## Implementation

Create `@DynamicComponent` decorator which will accept object with configuration:

```ts
@Component({...})
@DynamicComponent({ config: LayoutFlatConfig })
export class LayoutFlatHostComponent implements
  OrchestratorDynamicComponent<LayoutFlatConfig> {}
```

Before passing configuration to dynamic component `RenderItemComponent` must merge default configuration with user-provided
via `ConfigurationService`.

## Prior Art

None

