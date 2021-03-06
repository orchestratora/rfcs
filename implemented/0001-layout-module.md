# Layout Module

## Summary

Create `LayoutModule` with `LayoutFlatComponent` exported from it.

## Motivation

Allow to render multiple items in place of one item from configuration.

## Detailed Explanation

In order to provide a user configurable layout of `RenderItem[]` we should provide a `LayoutModule` that exports wrappers.

## Rationale and Alternatives

I suggest that in order to complete this as fast a possible, we integrate these wrapper using `@angular/flex-layout` and exposing basic configurations  for each wrapper.

## Implementation

**LayoutFlatComponent**

The flat layout should wrap `n` amount of `OrchestratorConfigItem` instances and emit an event when all the instances have been rendered.

Configuration for `LayoutFlatComponent`:

```typescript
import { Injectable } from '@angular/core';

@Injectable()
export class LayoutFlatConfig {
  wrap: string;
  direction: string;
  alignItems: string;
  alignContent: string;
}
```

Apart from the configuration `Input`s, the component should also take in items to be rendered `items: ReadonlyArray<OrchestratorConfigItem<any>>;`,
should have an event emitter for when the items are rendered and an even emitter for everytime a new item is added into the view.

## Prior Art

None

## Links

- Migrated from [#8](https://github.com/orchestratora/orchestrator/issues/8)
- Implemented in [#17](https://github.com/orchestratora/orchestrator/pull/17)
