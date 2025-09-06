# ruler-factory

A flexible, chainable validation rule factory for TypeScript/JavaScript.

## Features

- Chainable API for building complex validation rules
- Supports string, number, array, boolean, object, symbol, bigint, null, undefined
- Customizable error messages
- Easy to extend and integrate
- TypeScript support

## Installation

```bash
pnpm add ruler-factory
# or
npm install ruler-factory
# or
yarn add ruler-factory
```

## Integration with UI frameworks

### Varlet UI

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { rulerFactory } from 'ruler-factory'

const ruler = rulerFactory((validator) => {
  return (value) => {
    const e = validator(value)

    return e ? e.message : true
  }
})

const model = ref({
  name: '',
  email: '',
})
</script>

<template>
  <var-form>
    <var-input
      v-model="model.name"
      placeholder="Name"
      :rules="ruler().required('Required').min(2, 'Wrong length').done()"
    />
    <var-input
      v-model="model.age"
      placeholder="Email"
      :rules="ruler().email('Must be email format').done()"
    />
  </var-form>
</template>
```

### Naive UI

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { rulerFactory } from 'ruler-factory'
import type { FormItemRule } from 'naive-ui'

const ruler = rulerFactory<FormItemRule>((validator, params = {}) => ({
  trigger: ['blur', 'change', 'input'],
  validator: (_, value) => validator(value),
  ...params,
}))

const model = ref({
  name: '',
  age: 20,
})
</script>

<template>
  <n-form :model>
    <n-form-item
      path="name"
      label="Name"
      :rule="ruler().required('Required').min(2, 'Wrong length').done()"
    >
      <n-input v-model:value="model.name" />
    </n-form-item>
    <n-form-item
      path="age"
      label="Age"
      :rule="ruler().number().required('Required').negative('Must be negative').done()"
    >
      <n-input-number v-model:value="model.age" />
    </n-form-item>
  </n-form>
</template>
```

### Element Plus

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { rulerFactory } from 'ruler-factory'
import type { FormItemRule } from 'element-plus'

const ruler = rulerFactory<FormItemRule>((validator, params) => ({
  validator(_, value, callback) {
    const e = validator(value)

    e ? callback(e) : callback()
  },
  trigger: ['blur', 'change', 'input'],
  ...params,
}))

const model = ref({
  name: '',
  email: '',
})
</script>

<template>
  <el-form :model>
    <el-form-item
      prop="name"
      label="Name"
      :rules="ruler().required('Required').done()"
    >
      <el-input v-model="model.name" />
    </el-form-item>
    <el-form-item
      prop="email"
      label="Email"
      :rules="ruler().email('Must be email format').done()"
    >
      <el-input v-model="model.email" />
    </el-form-item>
  </el-form>
</template>
```

### Custom API

Take `Naive UI` as an example

```ts
import { FormItemRule } from 'naive-ui'
import { rulerFactory, RulerFactoryMessage } from 'ruler-factory'

customRuler().ip('ip format error').done()

function customRuler() {
  const ruler = rulerFactory<FormItemRule>((validator, params = {}) => ({
    trigger: ['blur', 'change', 'input'],
    validator: (_, value) => validator(value),
    ...params,
  }))

  const ctx = ruler()

  function ip(message: RulerFactoryMessage, params?: FormItemRule) {
    ctx.addRule((value) => {
      // Implement isString and isIP by yourself
      if (!isString(value) || !isIP(value)) {
        return new Error(ctx.getMessage(message))
      }
    }, params)

    return ctx
  }

  Object.assign(ctx, { ip })

  return ctx as typeof ctx & { ip: typeof ip }
}
```

## API

### Types Validation

- `.string(message?, params?)`
- `.number(message?, params?)`
- `.array(message?, params?)`
- `.boolean(message?, params?)`
- `.object(message?, params?)`
- `.symbol(message?, params?)`
- `.bigint(message?, params?)`
- `.null(message?, params?)`
- `.undefined(message?, params?)`
- `.true(message?, params?)`
- `.false(message?, params?)`

### Non Empty Validation

- `.required(message)`

### String Validation

- `.min(value, message, params?)`
- `.max(value, message, params?)`
- `.length(value, message, params?)`
- `.regex(regexp, message, params?)`
- `.startsWith(value, message, params?)`
- `.endsWith(value, message, params?)`
- `.includes(value, message, params?)`
- `.uppercase(message, params?)`
- `.lowercase(message, params?)`
- `.email(message, params?)`

### Number Validation

- `.number().min(value, message, params?)`
- `.number().max(value, message, params?)`
- `.number().gt(value, message, params?)`
- `.number().gte(value, message, params?)` alias .min
- `.number().lt(value, message, params?)`
- `.number().lte(value, message, params?)` alias .max
- `.number().positive(value, message, params?)`
- `.number().negative(value, message, params?)`

### Bigint Validation

- `.bigint().min(value, message, params?)`
- `.bigint().max(value, message, params?)`
- `.bigint().gt(value, message, params?)`
- `.bigint().gte(value, message, params?)` alias .min
- `.bigint().lt(value, message, params?)`
- `.bigint().lte(value, message, params?)` alias .max
- `.bigint().positive(value, message, params?)`
- `.bigint().negative(value, message, params?)`

### Array Validation

- `.array().min(value, message, params?)`
- `.array().max(value, message, params?)`
- `.array().length(value, message, params?)`
- `.array().includes(value, message, params?)`

### Predicate Validation

- `.is(fn, message, params?)`
- `.not(fn, message, params?)`

### Complete rule building

- `.done()`

### Custom Rule

- `.addRule(validator)`

### Value Transformer

- `.trim()`
- `.toLowerCase()`
- `.toUpperCase()`
- `.transform(fn)`

## License

MIT

## Links

- [GitHub Repository](https://github.com/varletjs/ruler-factory)
- [Issues](https://github.com/varletjs/ruler-factory/issues)

## Inspired By

[`zod`](https://zod.dev/)
[`yup`](https://github.com/jquense/yup)
