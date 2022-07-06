# Lazy loading components

## Summary

Allow to lazy load components in runtime and render them as usual dynamic components.

## Motivation

Currently Orchestrator supports components to be provided via DI which limits it to build-time mostly.
However it is benefitial to be able to load components on demand whenewer they are required to be rendered in the UI to reduce initial bundle size of the application.

## Detailed Explanation

To support lazy loading usecase the following changes have to be done:

1. Allow to register dynamic components by providing a function that returns Promise of an actual component
2. Adjust `orc-render-item` component to await for a lazy component to be loaded before rendering
3. Allow to add component registries at runtime via `ComponentLocatorService` service so that custom lazy loading techniques may be implemented

## Rationale and Alternatives

The most simplest version for lazy loading would be to just implement item #3 from the section above and let devs to implement custom lazy loading components, however this will make a simple usecase harder to implment and different projects may have different implementations of the same problem - making them less interoperable between each other which is not ideal.

## Implementation

1. Update type definitions of `ComponentMap` and `ComponentRegistry` to support lazy loading by adding function signature that returns the same type it currently holds:

```ts
export type LazyComponentLoader<T extends Type<any> = DefaultDynamicComponent> =
  () => Promise<T>;

export type ComponentOrLoader<T extends Type<any> = DefaultDynamicComponent> =
  | T
  | LazyComponentLoader<T>;

export interface ComponentMap<T extends Type<any> = DefaultDynamicComponent> {
  [k: string]: ComponentOrLoader<T>;
}

export type ComponentRegistry<T extends Type<any> = DefaultDynamicComponent> =
  | ComponentOrLoader<T>[]
  | ComponentMap<T>;
```

Update `ComponentLocatorService` to handle lazy component signatures:

```ts
class ComponentLocatorService {
  resolve<T, C = GetOrchestratorDynamicComponentConfig<T>>(
    component: string | OrchestratorDynamicComponentType<C>,
  ): Promise<OrchestratorDynamicComponentType<C>> | undefined;
}
```

Method `resolve` MUST perform the following actions:

- Check if the component map has entry for requested component
  - If NOT found - eeturn `undefined`
- Check if the component map has lazy loader of the component
  - If true - execute lazy loader and return it's promise
  - Once resolved - replace lazy loader in the component map with a loaded component
  - If rejected - throw an error with details about which component failed to load and original error reason using `ErrorStrategy.handle()` API
- Othrwise return component from the component map as-is

2. Update `orc-render-item`:

   1. Wait for a component to be resolved from `ComponentLocatorService`
   2. Resolve config for a loaded component
   3. Render loaded component

3. Update `ComponentLocatorService` to allow registering new components and expose all loaded/registered components:

```ts
class ComponentLocatorService {
  +registerComponent(component: ComponentOrLoader): void;
  +getComponentNames(): Observable<string[]>;
  +getLoadedComponents(): Observable<OrchestratorDynamicComponentType[]>;
}
```

Method `registerComponent` MUST perform the following actions:

- Extend currently registered component map

We specifically have 2 separate methods for getting components which are Observables so they can update whenever a new component is registered/loaded:

- component names that are registered/loaded `getComponentNames`
- already loaded components `getLoadedComponents`

## Unresolved Questions and Bikeshedding

Open questions:

- What should be/not be rendered while a component is being loaded?
  For example we can have optional InjectionToken that can provide a component for rendering.
- Should we provide a way for custom loader strategy ie. custom service?
