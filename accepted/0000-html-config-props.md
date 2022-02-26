# Set HTML properties from configuration

## Summary

Allow to set properties of `orc-html-tag` component from `html-tag` package via it's configuration.

## Motivation

There is no way to set properties of HTML element via configuration at the moment - only
[it's attributes](https://github.com/orchestratora/orchestrator/blob/8eb225cafa10ae71c5d5c0c00cfa0376459f90d9/libs/html-tag/src/lib/html-tag/html-tag-config.ts#L14)
which is limited to strings only.

It will be useful to allow to set properties of the HTML element which will exand the values
allowed to be set on the element to any JS objects.

## Detailed Explanation

Add new property called `properties` to the
[HtmlTagConfig](https://github.com/orchestratora/orchestrator/blob/8eb225cafa10ae71c5d5c0c00cfa0376459f90d9/libs/html-tag/src/lib/html-tag/html-tag-config.ts#L6)
wich will set given props on the rendered HTML element.

## Rationale and Alternatives

It's possible to set props manually by getting rendered HTML element from the `orc-html-tag`
component API via `HtmlTagComponent.getElement()` but requires imperative code style and
usage of some callback hook which would be called when the component is rendered which does
not exist in the Orchestrator API at the moment.

We could also add some sort of hooks API to Orchestrator that will allow to run arbitraty
JS code during lifecycle of component to enable imerative use-cases
(ex. `created`, `destoyed`, etc.).

## Implementation

- Extend the [HtmlTagConfig](https://github.com/orchestratora/orchestrator/blob/8eb225cafa10ae71c5d5c0c00cfa0376459f90d9/libs/html-tag/src/lib/html-tag/html-tag-config.ts#L6):

  ```ts
  import { record, string, unknown } from 'io-ts';
  import { OptionTypeFactory } from '@orchestrator/core';

  class HtmlTagConfig {
    @OptionTypeFactory(() => record(string, unknown))
    +attributes?: Record<string, unknown>;
  }
  ```

- In the [HtmlTagComponent](https://github.com/orchestratora/orchestrator/blob/8eb225cafa10ae71c5d5c0c00cfa0376459f90d9/libs/html-tag/src/lib/html-tag/html-tag.component.ts#L28)
  whenewer config changes set the properties of rendered HTML element using
  `Renderer3.setProperty()` API.
  [`KeyValueDiffers`](https://angular.io/api/core/KeyValueDiffers) MAY be used to improve performance of the properties addition/changes/removal on the HTML element.

## Prior Art

None
