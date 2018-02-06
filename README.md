# Random Object Language

- Version：0.1.0

## Introduction

Using a schema to define a random object, limit the value range of each fields, and guide the building process.

## Example

```json
{
  "field_1": {
    "$type": "assigned",
    "value": "some concrete value"
  },
  "field_2": {
    "$type": "number",
    "integer": true,
    "range": {"gt": 1, "lte": 10}
  },
  "field_3": {
    "field_3_1": {
      "$type": "enum",
      "values": [1, 3, "Bob", {"name": "Alice"}],
      "weights": [2, 2, 1, 4]
    },
    "field_3_2": {
      "$type": "enum",
      "valuesRef": "xxx",
      "weightsRef": "yyy"
    }
  },
  "field_4": {
    "$type": "dependant",
    "dependsOn": "field_3.field_3_1",
    "map": [
      [1, {"$type": "number", "range": {"gte": 2, "lt": 9}}],
      [2, {"$type": "enum", "values": ["a", "b", "c"]}],
      ["Bob", {"$type": "enum", "valuesRef": "xxx"}],
      [{"name": "Alice"}, {"$type": null}] 
    ],
    "default": {"$type": null}
  }
}
```

## Definition

### Base

JSON

### Free Object (FO)

A *Free Object* is a JSON object where the value of any leaf field must be a *Free Field*. 

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

### Free Field

A *Free Field* is a JSON object which contains a special `$type` field and other option fields required by the type.

e.g.

```
{
  "$type": ...,
  "option_1": ...,
  "option_2": ...,
  ...
}
```

### Types

#### null

Special type which means this field does not exist.

#### "assigned"

If a field is of this type, the value should be pre-assigned.

##### options:

- **value: any** - the pre-assigned value.

#### "number"

Provide a number for this field.

##### options

- **integer?: bool = false** - if true, the number should be an integer.
- **range?: object** - specify the range of the number, e.g. `{gt: 1, lt: 10}`. available limiters are:
  - **gt** - greater than
  - **gte** - greater than or equal
  - **lt** - less than
  - **lte** - less than or equal
  
#### "enum"

Select a value from given set.

##### options

- **values?: array** - all available values.
- **valuesRef?: any** - specify the reference of the collection of all available values. syntax should be defined by 
the implementation.
- **weights?: array of number** - weights of corresponding values.
- **weightsRef?: any** - specify the reference of the collection of weights. syntax should be defined by the 
implementation.

Note:
 
- there must be exact one of `values` or `valuesRef`.
- `weights` and `weightsRef` are exclusive.
- the default value of weight is 1.

#### "dependant"

The value of this field depends on other field.

##### options

- **dependsOn: string** - the other field this field depends on. use dot format to reference a deep field. e.g. 
`outer.inner`.
- **map: array of [key, value]** - decide the actual free field based on the actual value of the depended field.
the sub arrays each have 2 items: the first one, the map key, is the value of depended field; the second one, the map 
value, is the expected free field. 
- **default: object** - the default free field if the depended field value failed to match any item of the map.  

Note:

- if the map key is an object, it will be compared deeply.

## MIT