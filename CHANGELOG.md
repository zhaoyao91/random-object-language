### 0.6.1

- make `values` option of `enum` type required and non-empty

### 0.6.0

- change `DNE` to `void`
- change `$type` to `@type`
- add `virtual` option


### 0.5.3

- allow null key of map option of dependant type to match undefined field

### 0.5.2

- make `range` option of `number` type required

### 0.5.1

- make it clear that the depended field must be a FF

### 0.5.0

- remove `FV` concept
- update definition of `FO`, now the leaf field **must** be a `FF`
- `values` of `enum` type must be normal value
- `value` of `map` option of `dependant` type must be FF
- `default` option of `dependant` type must be FF
- add default value for `default` option of `dependant` type

### 0.4.1

- change null type to DNE type

### 0.4.0

- add `FV` concept
- update definition of `FO`, now the leaf field **may** be a `FF`
- `value` of `map` option of `dependant` type is to be `FV`
- `default` option of `dependant` type is to be `FV`
- allow `values` of `enum` type to be `FVs`
