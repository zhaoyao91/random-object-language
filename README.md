# Random Object Language

- Version：0.4.0

## Introduction

Using a schema to define a random object, limit the value range of each fields, and guide the building process.

## Example

```json
{
  "field_0": "some concrete value",
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
    }
  },
  "field_4": {
    "$type": "dependant",
    "dependsOn": ["field_2", "field_3.field_3_1"],
    "map": [
      [1, 1, {"$type": "number", "range": {"gte": 2, "lt": 9}}],
      [2, 2, {"$type": "enum", "values": ["a", "b", "c"]}],
      [3, "Bob", "God"],
      [4, {"name": "Alice"}, {"field_4_1": {"$type": "enum", "values": ["Boy", "Girl"]}}]
    ],
    "default": {"$type": null}
  }
}
```

## Definition

### Base

JSON

### Free Value (FV)

A plain JSON value or FO or FF.

### Free Object (FO)

A *Free Object* is a JSON object where the value of any leaf field **maybe** be a *Free Field*. 

e.g.

```
{
  "field_0": "some value",
  "field_1": FF,
  "field_2": {
    field_2_1: FF,
    ...
  },
  ...
}
```

### Free Field (FF)

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

**notes**

- the value is always treated as a JSON value, even it is like a FF or FO. this ensures that a assigned field is a 
terminated field, thus allowing you set a FF-like or FO-like object as a value. 

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

- **values?: array** - all available FVs.
- **weights?: array of number** - weights of corresponding values.

**notes**
 
- the default weight is 1.

#### "dependant"

The value of this field depends on other field(s).

##### options

- **dependsOn: array of string** - the other fields this field depends on. use dot format to reference a deep field. e.g. 
`outer.inner`.
- **map: array of [key1, key2, ..., value]** - decide the actual FV based on the actual values of the depended fields.
the sub arrays each have n+1 items: the first n items, the map keys, are the values of depended fields; the last one, the map 
value, is the expected FV. 
- **default: object** - the default FV if the depended field value failed to match any item in the map.  

**notes**

- if the map key is an object, it will be compared deeply.

## License

MIT
