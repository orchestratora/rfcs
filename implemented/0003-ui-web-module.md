# UI Web Module

## Summary

Add components for rendering native browser's components via orchestrator

## Motivation

This is the minimal usable set of components that users will want to display on the page.

## Detailed Explanation

Add package `ui-web` to orchestrator with following components:

- Button
- Label
- Image
- Heading(s)
- Input
- Textarea
- Select

## Rationale and Alternatives

-

## Implementation

Create a module `UiWebModule` that will export all components.

Every component should have it's own module that can be used standalone.

Create next components:

- `UiWebButtonHostComponent`
- `UiWebTextHostComponent`
- `UiWebImageHostComponent`
- `UiWebHeadingHostComponent`
- `UiWebInputHostComponent`
- `UiWebSelectHostComponent`
- `UiWebTextareaHostComponent`

### UiWebButtonHostComponent

- Selector: `orc-ui-web-button-host`
- Config:
```ts
export class UiWebButtonConfig {
  text: string;
  type?: string;
  disabled?: boolean;
  tabindex?: number;
}
```

### UiWebHeadingHostComponent

- Selector: `orc-ui-web-heading-host`
- Config:
```ts
@Injectable()
export class UiWebHeadingConfig {
  text: string;
  level?: number;
}
```

### UiWebImageHostComponent

- Selector: `orc-ui-web-heading-host`
- Config:
```ts
@Injectable()
export class UiWebImageConfig {
  src: string;
  width?: number;
  height?: number;
  alt?: string;
}
```

### UiWebInputHostComponent

- Selector: `orc-ui-web-input-host`
- Config:
```ts
@Injectable()
export class UiWebInputConfig {
  type?: string;
  id?: string;
  label?: string;
  placeholder?: string;
  readonly?: boolean;
  autocomplete?: string;
  name?: string;
  value?: any;
  tabindex?: number;
  disabled?: boolean;
  required?: boolean;
  autofocus?: boolean;
}
```

### UiWebSelectHostComponent

- Selector: `orc-ui-web-select-host`
- Config:
```ts
@Injectable()
export class UiWebSelectConfig {
  options: UiWebSelectOption[];
  multiple?: boolean;
  size?: number;
  name?: string;
  value?: any;
  tabindex?: number;
  disabled?: boolean;
  required?: boolean;
  autofocus?: boolean;
}

export interface UiWebSelectOptionGroup {
  children: (string | UiWebSelectOptionPair)[];
  label?: string;
  disabled?: boolean;
}

export interface UiWebSelectOptionPair {
  label: string;
  value: any;
  disabled?: boolean;
  selected?: boolean;
}

export type UiWebSelectOption = string | UiWebSelectOptionPair | UiWebSelectOptionGroup;
```

### UiWebTextHostComponent

- Selector: `orc-ui-web-text-host`
- Config:
```ts
@Injectable()
export class UiWebTextConfig {
  text: string;
  preserveFormatting?: boolean;
}
```

### UiWebTextareaHostComponent

- Selector: `orc-ui-web-textarea-host`
- Config:
```ts
@Injectable()
export class UiWebTextAreaConfig {
  placeholder?: string;
  readonly?: boolean;
  cols? = 20;
  rows? = 2;
  maxlength?: number;
  minlength?: number;
  spellcheck?: boolean | 'default';
  wrap?: string;
  name?: string;
  value?: any;
  tabindex?: number;
  disabled?: boolean;
  required?: boolean;
  autofocus?: boolean;
}
```

### UiWebTextareaHostComponent

- Selector: `orc-ui-web-textarea-host`
- Config:
```ts
@Injectable()
export class UiWebTextAreaConfig {
  placeholder?: string;
  readonly?: boolean;
  cols? = 20;
  rows? = 2;
  maxlength?: number;
  minlength?: number;
  spellcheck?: boolean | 'default';
  wrap?: string;
  name?: string;
  value?: any;
  tabindex?: number;
  disabled?: boolean;
  required?: boolean;
  autofocus?: boolean;
}
```

## Prior Art

None

## Links

- Migrated from [#11](https://github.com/orchestratora/orchestrator/issues/11)
- Implemented in [#22](https://github.com/orchestratora/orchestrator/pull/22)
