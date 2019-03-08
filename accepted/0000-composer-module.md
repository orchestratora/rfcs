# Composer Module

## Summary

Add new library `@orchestrator/composer` that will allow to compose configuration for orchestrator visually via drag-n-drop.

## Motivation

This will allow users to configure their UI visually and generate JSON from that for future use.

## Detailed Explanation

It should expose component `<orc-composer>` that will render all UI controls:

- `<orc-composer-components>` - will render list of all available components ready for dragging
- `<orc-composer-canvas>` - will render area for dropping components and configure them
- `<orc-composer-preview>` - will render currently configured components in live preview

Those 3 components should also be exported to give more fine grained control over UI to end user.

### Example

First import composer module with all components that you want to be available

```ts
import { NgModule } from '@angular/core';
import { OrchestratorCoreModule } from '@orchestrator/core';
import { ComposerModule } from '@orchestrator/composer';
import { UiWebModule } from '@orchestrator/ui-web';

@NgModule({
  imports: [
    OrchestratorCoreModule.forRoot(),
    ComposerModule.withComponents([
      // Custom extra components comes here
    ]),
    UiWebModule.forRoot(), // Extra modules with components also can be used
  ],
})
export class AppModule {}
```

Then render `<orc-composer>` component where you need

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  template: `
    <orc-composer></orc-composer>
  `,
})
export class AppComponent {}
```

## Rationale and Alternatives

We should use already existing library for drag-n-drop support (ex. [`angular-gridster2`](https://github.com/tiberiuzuld/angular-gridster2)).

For UI we can use [Ant Design for Angular](https://github.com/NG-ZORRO/ng-zorro-antd) because it has rich set of high quality components.

## Implementation

Create module `ComposerModule` that will declare and export components:

- `ComposerComponent`
- `ComposerComponentsComponent`
- `ComposerCanvasComponent`
- `ComposerPreviewComponent`

Create private components for internal use:

- `ComposerConfiguratorComponent` - should be used to render configuration controls for specific dynamic component
- `ComposerDroppableComponent` - should be used to accept dragged dynamic components. It will be rendered in empty slots of configuration component and last element in items

Communication between components may happen via inputs/outputs as well as via parent `ComposerComponent`.

Any errors during live preview of configuration must be rendered in toast.

### `ComposerComponent`

- Selector: `orc-composer`
- Will render `ComposerComponentsComponent`, `ComposerCanvasComponent` and `ComposerPreviewComponent` by default
- Or will accept projected template with those components for custom layout

### `ComposerComponentsComponent`

- Selector: `orc-composer-components`
- Will render a list of all currently available dynamic components ready for drag-n-dropping

### `ComposerCanvasComponent`

- Selector: `orc-composer-canvas`
- Will render area for dropping components from `ComposerComponentsComponent` and configure them in the modal window
- It should render `RenderItemComponent` inside with `ComposerDroppableComponent` in appropriate places
- When configuration is updated it should rearrange position of `ComposerDroppableComponent`

### `ComposerPreviewComponent`

- Selector: `orc-composer-preview`
- It will render current json configuration via `orc-orchestrator`

### `ComposerConfiguratorComponent`

- Selector: `orc-composer-configurator`
- It will render configuration controls for dynamic component provided from DI token `CONFIGURATION_COMPONENT`
- When user updates configuration it should emit event `configUpdate` with current config object
- When user submits configuration it should emit event `configReady` with config object
- It should validate configuration to the best it can before allowing to submit the config

### `ComposerDroppableComponent`

- Selector: `orc-composer-droppable`
- It will render droppable rectangular area for accepting dynamic components
- When such component will be dropped it should emit event `componentDropped` with dynamic component

## Prior Art

None

## Unresolved Questions and Bikeshedding

- We need to agree how json configuration will be exposed to user. It can be done via output that is triggered when user clicks on `Generate` button or configuration is emitted every time it's changed.
  This can also be configurable via DI token.
