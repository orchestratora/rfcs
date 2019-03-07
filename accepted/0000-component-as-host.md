# Expose internal component as host

## Summary

Allow dynamic components to expose internal element so all attributes, id and classes will apply directly to them instead of the host element.

## Motivation

It will allow to reduce boilerplate in configuration of dynamic components because standard attributes and properties
will be automatically redirected to correct internal component in template.

## Detailed Explanation

This will be useful when, for example, you have button in template and you want to allow to set attributes directly on it:

```html
<button orcExposeAsHost>{config.text}</button>
```

In this case directive `orcExposeAsHost` will make button element as `host` component and attributes from config will be set directly on it.

## Rationale and Alternatives

- Wiring configuration for default attributes manually to components.

## Implementation

Create directive named `orcExposeAsHost` that will take host component and expose it as host component.

Then `RenderItemComponent` will grab it and apply directives and attributes on it instead of real host component.

## Prior Art

None

## Unresolved Questions and Bikeshedding

- It's unclear how to get internal component as host before rendering real host component because all attributes already will be applied by that time

- It requires support from the upstream lib ng-dynamic-component to be able to provide custom ComponentInjector for different directives on dynamic components.
