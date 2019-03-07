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

## Prior Art

None

## Unresolved Questions and Bikeshedding

- We need to agree how json configuration will be exposed to user. It can be done via output that is triggered when user clicks on `Generate` button or configuration is emitted every time it's changed.
This can also be configurable via DI token.
