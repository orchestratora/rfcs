# Clean dynamic rendering

## Summary

Dynamic components MUST be rendered without any extra html/components wrapping them and/or being around them.

## Motivation

Currently all dynamic componentns in orchestrator are rendered via `ndc-dynamic` and `orc-render-item` components which by itself pollute DOM tree by wrapping actual dynamic components.

This usually does not create any issues (except for the increased DOM elements count) unless rendering some special components that are sensetive to it's children content such as `select` html tag which requires direct `option` html tags as it's children content to properly render options.

And so such scenarios will break traditional nested dynamic rendering via `items` input and so far require a custom configuration to render children content manually bypassing our dynamic components rendering pipeline.

## Detailed Explanation

We need to change dynamic components rendering mechanism by switching from components to directives:

- `orc-render-item` component -> `[orcRenderItem]` directive

This on it's own is a breaking change as we will change `orc-render-item` component which is part of public API of orchestrator so we should do the switch in 2 phases:

1. Introduce new `[orcRenderItem]` directive and deprecate existing `orc-render-item` component and refactor all usages within orchestrator
2. For next major version - remove deprecated `orc-render-item` component

## Rationale and Alternatives

One option is to keep rendering as is and in edge cases require custom rendering techniques (such as `html` and `text` config props of `html-tag` component). This is going to bring inconsistencies between different dynamic components configurations and also limit dynamic rendering capabilities of orchestrator as a whole thus it's not the best option and this RFC tries to mitigate it.

## Implementation

Deprecate `orc-render-item` component in `core` package.

Create new `[orcRenderItem]` directive in `core` package which reflects the same API of `orc-render-item` component.

Refactor all other places where `orc-render-item` component is being used by replacing it with `[orcRenderItem]` directive.

All the changes for any packages MUST be of semver patch type since this change is considered a "fix" the dynamic rendering.

For the next scheduled major release make sure to remove all deprecated API, including `orc-render-item` component.

## Prior Art

None

## Unresolved Questions and Bikeshedding

Since `[orcRenderItem]` is a directive - it does not have a template and as such it's impossible to levarage `[ngComponentOutlet]` directive and `ng-dynamic-component` library in it's current state as it's primary API is declarative template based rendering so another approach must be taken.

There is a service [`IoService`](https://github.com/gund/ng-dynamic-component/blob/544ed23a384b8ddbe933dc7bbbac75aa07eee0f4/projects/ng-dynamic-component/src/lib/io/io.service.ts#L36) available in `ng-dynamic-component` for tracking inputs/outputs which may be useful for this implementation.
