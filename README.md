# @rajatasusual/json-schema-2-ts [![Build Status][build]](https://github.com/rajatasusual/jsonSchema2TS/actions?query=branch%3Amaster+workflow%3ACI) [![npm]](https://www.npmjs.com/package/@rajatasusual/json-schema-2-ts) [![mit]](https://opensource.org/licenses/MIT)

## Forked from https://github.com/bcherny/@rajatasusual/json-schema-2-ts

[build]: https://img.shields.io/github/actions/workflow/status/rajatasusual/jsonSchema2TS/ci.yml?style=flat-square
[npm]: https://img.shields.io/npm/v/@rajatasusual/json-schema-2-ts.svg?style=flat-square
[mit]: https://img.shields.io/npm/l/@rajatasusual/json-schema-2-ts.svg?style=flat-square

> Compile json schema to typescript typings

## Example

Input:
```json
{
  "title": "Example Schema",
  "type": "object",
  "properties": {
    "firstName": {
      "type": "string"
    },
    "lastName": {
      "type": "string"
    },
    "age": {
      "description": "Age in years",
      "type": "integer",
      "minimum": 0
    },
    "hairColor": {
      "enum": ["black", "brown", "blue"],
      "type": "string"
    }
  },
  "additionalProperties": false,
  "required": ["firstName", "lastName"]
}
```

Output:
```ts
class ExampleSchema {
  firstName: string;
  lastName: string;
  /**
   * Age in years
   */
  age?: number;
  hairColor?: "black" | "brown" | "blue";
}

module.exports = ExampleSchema;
```

## Installation

```sh
# Using Yarn:
yarn add @rajatasusual/json-schema-2-ts

# Or, using NPM:
npm install @rajatasusual/json-schema-2-ts --save
```

## Usage

```js
import { compile, compileFromFile } from 'json-schema-2-ts'

// compile from file
compileFromFile('foo.json')
  .then(ts => fs.writeFileSync('foo.d.ts', ts))

// or, compile a JS object
let mySchema = {
  properties: [...]
}
compile(mySchema, 'MySchema')
  .then(ts => ...)
```

## Options

`compileFromFile` and `compile` accept options as their last argument (all keys are optional):

| key | type | default | description |
|-|-|-|-|
| additionalProperties | boolean | `true` | Default value for `additionalProperties`, when it is not explicitly set |
| bannerComment | string | `"/* eslint-disable */\n/**\n* This file was automatically generated by @rajatasusual/json-schema-2-ts.\n* DO NOT MODIFY IT BY HAND. Instead, modify the source JSONSchema file,\n* and run @rajatasusual/json-schema-2-ts to regenerate this file.\n*/"` | Disclaimer comment prepended to the top of each generated file |
| cwd | string | `process.cwd()` | Root directory for resolving [`$ref`](https://tools.ietf.org/id/draft-pbryan-zyp-json-ref-03.html)s |
| declareExternallyReferenced | boolean | `true` | Declare external schemas referenced via `$ref`? |
| enableConstEnums | boolean | `true` | Prepend enums with [`const`](https://www.typescriptlang.org/docs/handbook/enums.html#computed-and-constant-members)? |
| format | boolean | `true` | Format code? Set this to `false` to improve performance. |
| ignoreMinAndMaxItems | boolean | `false` | Ignore maxItems and minItems for `array` types, preventing tuples being generated. |
| maxItems | number | `20` | Maximum number of unioned tuples to emit when representing bounded-size array types, before falling back to emitting unbounded arrays. Increase this to improve precision of emitted types, decrease it to improve performance, or set it to `-1` to ignore `maxItems`.
| strictIndexSignatures | boolean | `false` | Append all index signatures with `\| undefined` so that they are strictly typed. |
| style | object | `{ bracketSpacing: false,  printWidth: 120,  semi: true,  singleQuote: false,  tabWidth: 2,  trailingComma: 'none',  useTabs: false }` | A [Prettier](https://prettier.io/docs/en/options.html) configuration |
| unknownAny | boolean | `true` | Use `unknown` instead of `any` where possible |
| unreachableDefinitions | boolean | `false` | Generates code for `$defs` that aren't referenced by the schema. |
| $refOptions | object | `{}` | [$RefParser](https://github.com/BigstickCarpet/json-schema-ref-parser) Options, used when resolving `$ref`s |

## Tests

`npm test`

## Features

- [x] `title` => `interface`
- [x] Primitive types:
  - [x] array
  - [x] homogeneous array
  - [x] boolean
  - [x] integer
  - [x] number
  - [x] null
  - [x] object
  - [x] string
  - [x] homogeneous enum
  - [x] heterogeneous enum
- [x] Non/extensible interfaces
- [ ] Custom JSON-schema extensions
- [x] Nested properties
- [x] Schema definitions
- [x] [Schema references](http://json-schema.org/latest/json-schema-core.html#rfc.section.7.2.2)
- [x] Local (filesystem) schema references
- [x] External (network) schema references
- [x] Add support for running in browser
- [x] default class name
- [x] infer unnamed class name from filename
- [x] `deprecated`
- [x] `allOf` ("intersection")
- [x] `anyOf` ("union")
- [x] `oneOf` (treated like `anyOf`)
- [x] `maxItems` ([eg](https://github.com/tdegrunt/jsonschema/blob/67c0e27ce9542efde0bf43dc1b2a95dd87df43c3/examples/all.js#L166))
- [x] `minItems` ([eg](https://github.com/tdegrunt/jsonschema/blob/67c0e27ce9542efde0bf43dc1b2a95dd87df43c3/examples/all.js#L165))
- [x] `additionalProperties` of type
- [x] `patternProperties` (partial support)
- [x] [`extends`](https://github.com/json-schema/json-schema/wiki/Extends/014e3cd8692250baad70c361dd81f6119ad0f696)
- [x] `required` properties on objects ([eg](https://github.com/tdegrunt/jsonschema/blob/67c0e27ce9542efde0bf43dc1b2a95dd87df43c3/examples/all.js#L130))
- [ ] `validateRequired` ([eg](https://github.com/tdegrunt/jsonschema/blob/67c0e27ce9542efde0bf43dc1b2a95dd87df43c3/examples/all.js#L124))
- [x] literal objects in enum ([eg](https://github.com/tdegrunt/jsonschema/blob/67c0e27ce9542efde0bf43dc1b2a95dd87df43c3/examples/all.js#L236))
- [x] referencing schema by id ([eg](https://github.com/tdegrunt/jsonschema/blob/67c0e27ce9542efde0bf43dc1b2a95dd87df43c3/examples/all.js#L331))
- [x] custom typescript types via `tsType`

## Custom schema properties:

- `tsType`: Overrides the type that's generated from the schema. Useful for forcing a type to `any` or when using non-standard JSON schema extensions ([eg](https://github.com/sokra/@rajatasusual/json-schema-2-ts/blob/f1f40307cf5efa328522bb1c9ae0b0d9e5f367aa/test/e2e/customType.ts)).
- `tsEnumNames`: Overrides the names used for the elements in an enum. Can also be used to create string enums ([eg](https://github.com/johnbillion/wp-json-schemas/blob/647440573e4a675f15880c95fcca513fdf7a2077/schemas/properties/post-status-name.json)).

## Not expressible in TypeScript:

- `dependencies` ([single](https://github.com/tdegrunt/jsonschema/blob/67c0e27ce9542efde0bf43dc1b2a95dd87df43c3/examples/all.js#L261), [multiple](https://github.com/tdegrunt/jsonschema/blob/67c0e27ce9542efde0bf43dc1b2a95dd87df43c3/examples/all.js#L282))
- `divisibleBy` ([eg](https://github.com/tdegrunt/jsonschema/blob/67c0e27ce9542efde0bf43dc1b2a95dd87df43c3/examples/all.js#L185))
- [`format`](https://github.com/json-schema/json-schema/wiki/Format) ([eg](https://github.com/tdegrunt/jsonschema/blob/67c0e27ce9542efde0bf43dc1b2a95dd87df43c3/examples/all.js#L209))
- `multipleOf` ([eg](https://github.com/tdegrunt/jsonschema/blob/67c0e27ce9542efde0bf43dc1b2a95dd87df43c3/examples/all.js#L186))
- `maximum` ([eg](https://github.com/tdegrunt/jsonschema/blob/67c0e27ce9542efde0bf43dc1b2a95dd87df43c3/examples/all.js#L183))
- `minimum` ([eg](https://github.com/tdegrunt/jsonschema/blob/67c0e27ce9542efde0bf43dc1b2a95dd87df43c3/examples/all.js#L182))
- `maxProperties` ([eg](https://github.com/tdegrunt/jsonschema/blob/67c0e27ce9542efde0bf43dc1b2a95dd87df43c3/examples/all.js#L113))
- `minProperties` ([eg](https://github.com/tdegrunt/jsonschema/blob/67c0e27ce9542efde0bf43dc1b2a95dd87df43c3/examples/all.js#L112))
- `not`/`disallow`
- `oneOf` ("xor", use `anyOf` instead)
- `pattern` ([string](https://github.com/tdegrunt/jsonschema/blob/67c0e27ce9542efde0bf43dc1b2a95dd87df43c3/examples/all.js#L203), [regex](https://github.com/tdegrunt/jsonschema/blob/67c0e27ce9542efde0bf43dc1b2a95dd87df43c3/examples/all.js#L207))
- `uniqueItems` ([eg](https://github.com/tdegrunt/jsonschema/blob/67c0e27ce9542efde0bf43dc1b2a95dd87df43c3/examples/all.js#L172))

## FAQ

### @rajatasusual/json-schema-2-ts is crashing on my giant file. What can I do?

Prettier is known to run slowly on really big files. To skip formatting and improve performance, set the `format` option to `false`.

## Further Reading

- JSON-schema spec: https://tools.ietf.org/html/draft-zyp-json-schema-04
- JSON-schema wiki: https://github.com/json-schema/json-schema/wiki
- JSON-schema test suite: https://github.com/json-schema/JSON-Schema-Test-Suite/blob/node
- TypeScript spec: https://github.com/Microsoft/TypeScript/blob/master/doc/spec.md
