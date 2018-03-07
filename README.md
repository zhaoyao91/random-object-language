# Random Object Language

- Version：0.6.0

## Introduction

Using a schema to define a random object, limit the value range of each fields, and guide the building process.

## Example

```json
{
  "field_1": {
    "@type": "assigned",
    "value": "some concrete value"
  },
  "field_2": {
    "@type": "number",
    "integer": true,
    "range": {"gt": 1, "lte": 10}
  },
  "field_3": {
    "field_3_1": {
      "@type": "enum",
      "values": [1, 3, "Bob", {"name": "Alice"}],
      "weights": [2, 2, 1, 4]
    }
  },
  "field_4": {
    "@type": "dependant",
    "dependsOn": ["field_2", "field_3.field_3_1"],
    "map": [
      [1, 1, {"@type": "number", "range": {"gte": 2, "lt": 9}}],
      [2, 2, {"@type": "enum", "values": ["a", "b", "c"]}],
      [3, "Bob", {"@type": "assigned","value": "God"}],
      [4, {"name": "Alice"}, {"@type": "enum", "values": ["Boy", "Girl"]}]
    ],
    "default": {"@type": "void"}
  }
}
```

## Definition

### Base

JSON

### Free Object (FO)

A *Free Object* is a JSON object where the value of any leaf field **must** be a *Free Field*. 

e.g.

```
{
  "field_1": FF,
  "field_2": {
    field_2_1: FF,
    ...
  },
  ...
}
```

### Free Field (FF)

A *Free Field* is a JSON object which contains a special `@type` field and other option fields required by the type.

e.g.

```
{
  "@type": ...,
  "option_1": ...,
  "option_2": ...,
  ...
}
```

### Types

#### "void"

Special type which means this field does not exist.

#### "assigned"

If a field is of this type, the value should be pre-assigned.

##### options:

- **value: any** - the pre-assigned value.

#### "number"

Provide a number for this field.

##### options

- **integer?: bool = false** - if true, the number should be an integer.
- **range: object** - specify the range of the number, e.g. `{gt: 1, lt: 10}`. you must limit both the lower and upper 
boundaries. available limiters are:
  - **gt** - greater than
  - **gte** - greater than or equal
  - **lt** - less than
  - **lte** - less than or equal
  
#### "enum"

Select a value from given values.

##### options

- **values?: array** - all available values.
- **weights?: array of number** - weights of corresponding values.

**notes**
 
- the default weight is 1.

#### "dependant"

The value of this field depends on other field(s).

##### options

- **dependsOn: array of string** - the other FFs this field depends on. use dot format to reference a deep path. e.g. 
`[outer.inner]`.
- **map: array of [key1, key2, ..., value]** - decide the actual FF based on the actual values of the depended fields.
the sub arrays each have n+1 items: the first n items, the map keys, are the values of depended fields; the last one is
 the expected FF. 
- **default?: object** - the default FF if the depended field value failed to match any item in the map. the default 
value is `{"@type": "void"}`.  

**notes**

- if the map key is an object, it will be compared deeply.
- if the map key is null, it will match both null and undefined field.

### Common Options

- **virtual?=: bool = false** - if true, this field will not be present in the generated object. 

## License

MIT
