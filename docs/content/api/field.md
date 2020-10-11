---
title: Field
description: API reference for the Field component
menuTitle: '<Field />'
order: 1
---

# Field

The `<Field />` component is an extremely flexible component that makes rendering input fields easy and intuitive, By default it renders an HTML `input` tag. So a simple text input can be rendered like this:

```vue
<template>
  <Field name="field" type="text" />
</template>

<script>
import { Field } from 'vee-validate';

export default {
  components: {
    Field,
  },
};
</script>
```

## Rendering Fields

The `Field` component allows you to render practically anything and gives you complete flexibility and control over how your fields are rendered. The `Field` component renders an HTML `input` tag if not specified otherwise. Which can be done in two ways.

### Rendering simple fields with 'as' prop

The `as` prop tells the Field component which tag to render in its place, you can pass any additional attributes like `type="text"` and it will be passed to the rendered `input` tag as well as any listeners and slots.

For example you could render a `select` input like this:

```vue
<Field name="field" as="select">
  <option>Coffee</option>
  <option>Tea</option>
  <option>Coke</option>
</Field>
```

You can also render any globally defined components:

```vue
<Field name="field" as="my-text-field" />
```

<doc-tip>

For validation to work, the rendered tag with `as` prop must conform to the events that the `Field` component listens for, you can view a list of these in the [Validation Behavior](/guide/validation#validation-behavior)

</doc-tip>

### Rendering Complex Fields with Scoped Slots

The `as` prop is very easy to use but also limited as you cannot render a group of markup. Fortunately the `Field` component makes use of the scoped-slots (v-slot) feature to allow you to render complex markup:

```vue
<Field name="password" v-slot="{ field }">
  <input v-bind="field" type="password">
  <p>Hint: Enter a secure password you can remember</p>
</Field>
```

The most crucial part of rendering fields with `v-slot` is that you **must bind the `field` object to your input element/input**, the `field` object contains all the attributes and listeners required for the field to be validated, this is done automatically if you are using the `as` prop.

When using `v-slot` on the `Field` component you no longer have to provide an `as` prop and then it will become a renderless component.

## API Reference

### Props

| Prop            | Type                           | Required/Default | Description                                                                                                          |
| :-------------- | :----------------------------- | :--------------- | :------------------------------------------------------------------------------------------------------------------- |
| as              | `string`                       | `"span"`         | The element to render as a root node, defaults to `input`                                                            |
| name            | `string`                       | Required         | The field's name, must be inside `<Form />`                                                                          |
| rules           | `object \| string \| Function` | `null`           | The field's validation rules                                                                                         |
| validateOnMount | `boolean`                      | `false`          | If true, field will be validated when the component is mounted                                                       |
| bails           | `boolean`                      | `true`           | Stops validating as soon as a rule fails the validation                                                              |
| disabled        | `disabled`                     | `false`          | Disables validation and the field will no longer participate in the parent form state                                |
| label           | `string`                       | `undefined`      | A different string to override the field `name` prop in error messages, useful for display better or formatted names |

### Slots

#### default

The default slot gives you access to the following props:

#### `meta: Record<string, boolean>`

Contains useful information/flags about the field status.

#### `errors: string[]`

An array containing all error messages for the field.

#### `errorMessage: ComputedRef<string | undefined>`

The first error in the `errors` array if available, a handy shortcut to display error messages

#### `disabled: boolean`

A reactive reference to the field's current disabled state, this allows/prevents the field from participating in it's owning form's validation state and doesn't block the submission handlers even if invalid.

```js
const { disabled } = useField('field', value => !!value);

disabled.value; // true or false
```

#### `reset: () => void`

Resets the field's validation state, reverts all `meta` object to their default values and clears out the error messages. Doesn't change the field's value.

```js
const { reset } = useField('field', value => !!value);

// reset the field validation state
reset();
```

#### `validate: () => Promise<{ errors: string[] }>`

Validates the field's current value and returns a promise that resolves with an object containing the error messages emitted by the various rule(s).

```js
const { validate } = useField('field', value => !!value);

// trigger validation
await validate();
```

#### `handleChange: (evt: Event | any) => void`

Updates the field value, and validates the field. Can be used as an event handler to bind on the field. If the passed argument isn't an event object it will be used as the new value for that field.

It sets the following meta flags: `dirty` and `pristine`

#### `handleInput: (evt: Event | any) => void`

Updates the field value, **but does not validate the field**. Can be used as an event handler to bind on the field. If the passed argument isn't an event object it will be used as the new value for that field.

It sets the following meta flags: `dirty` and `pristine`

#### `handleBlur: (evt: Event | any) => void`

Validates the field by default unless explicitly [specified by validation triggers](/guide/validation#customizing-validation-triggers). Can be used as an event handler to bind on the field. If the passed argument isn't an event object it will be used as the new value for that field.

It sets the following meta flags: `touched` and `untouched`

Because this handler doesn't set the field value, it might not report validation correctly if other events are unspecified or disabled.

#### `field`

Contains a few properties that you can use `v-bind` with to get all vee-validate features on that input. The following is a description of the properties

##### `field.value: any`

The field's current value, you can bind it with `value` prop on your inputs to sync both values. Don't use it with `v-model` otherwise your input will freeze.

##### `field.disabled: boolean`

The field's current disabled state, this allows/prevents the field from participating in it's owning form's validation state and doesn't block the submission handlers even if invalid.

##### `field.name: string`

The field name.

##### `field.onBlur: Function[]`

An array containing a few listeners for the `blur` event, it involves updating some meta information and triggers validation by default.

##### `field.onInput: Function[]`

An array containing a few listeners for the `input` event, it involves updating the field value and some meta information.

##### `field.onChange: Function[]`

An array containing a few listeners for the `change` event, it involves updating the field value and triggering validation.