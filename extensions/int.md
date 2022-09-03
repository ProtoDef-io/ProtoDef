## int
* Author - [Saiv46](https://github.com/saiv46)
* Date - March 2020

### Implementations
- NodeJS: [node-protodef-neo](https://github.com/saiv46/node-protodef-neo) (builtin)

## Specification

### **int** ({ size: Integer })
### **lint** ({ size: Integer })
Arguments:
* size : fixed size in bytes

Represents a unsigned integer using `size` bytes.
`lint` represents a unsigned little-endian integer.

Example:
```json
[
  "int",
  { "size": "3" }
]
```

Example of value: `65535` (size = 2) / `16777215` (size = 3)