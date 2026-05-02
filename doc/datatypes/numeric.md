## Numeric

These datatypes represent numbers. Most of them don't take any arguments.
They default to big-endian encoding. To use little-endian, prefix its name with "l".

| Name    | Size in bytes | Example of value    | Also called                  |
| ---     | ---           | ---                 | ---                          |
| i8      | 1             | -125                | byte                         |
| u8      | 1             | 255                 | unsigned byte                |
| i16     | 2             | -32000              | short                        | 
| u16     | 2             | 60000               | unsigned short               |
| i32     | 4             | -2000000000         | int                          |
| u32     | 4             | 3000000000          | unsigned int                 |
| f32     | 4             | 4.5                 | float                        |
| f64     | 8             | 4.5                 | double                       |
| i64     | 8             | 1                   | long                         |
| u64     | 8             | 1                   | unsigned long                |
| varint  | 1-4           | 300                 | int var                      |
| varint64 | 1-8           | 300n                | unsigned long                |
| varint128 | 1-16         | 2 ^ 68              | unsigned __int128            |
| zigzag32 | 1-4           | -100                | signed int var               |
| zigzag64 | 1-8           | -680n               | signed long                  |

### **int** ({ size: Integer, ?minimum: Integer, ?maximum: Integer })
Arguments:
* size : fixed size in bytes
* minimum : optional, the minimum integer value
* maximum : optional, the maximum integer value

Represents a unsigned integer using `size` bytes.

Example:
```json
[
  "int",
  { "size": "3", "minimum": 14, "maximum": 15 }
]
```
Example of value: `65535` (size = 2) / `16777215` (size = 3)

Example:
```json
[
  "int",
  { "size": "1", "minimum": 1, "maximum": 100 }
]
```
Example of value: `1`, `100` (does not match `0` or `101`)

### **varint**  ({ ?minimum: Integer, ?maximum: Integer })
Arguments: None

[Protobuf](https://developers.google.com/protocol-buffers/docs/encoding#varints)-compatible representation for variable-length integers using one or more bytes. Intended for 32-bit unsigned integers, or signed 32-bit integers that have been directly cast to an integer (where the MSB is the sign bit) before encoding.

Example:
```json
"varint"
```

or
```json
[
  "varint"
]
```

or
```json
[
  "varint",
  {}
]
```

or
```json
[
  "varint",
  { "minimum": 1, "maximum": 100 }
]
```
Example of value: `300` (size is 2 bytes)

### **varint64**  ({ ?minimum: Integer, ?maximum: Integer })
Arguments:
* minimum : optional, the minimum integer value
* maximum : optional, the maximum integer value

Same as **varint**, but for 64-bit unsigned integers, or signed 64-bit integers that have been directly cast to an integer (where the MSB is the sign bit) before encoding.

### **varint128**  ({ ?minimum: Integer, ?maximum: Integer })
Arguments:
* minimum : optional, the minimum integer value
* maximum : optional, the maximum integer value

Same as **varint**, but for 128-bit unsigned integers, or signed 128-bit integers that have been directly cast to an integer (where the MSB is the sign bit) before encoding.

### **zigzag32** ({ ?minimum: Integer, ?maximum: Integer })
Arguments:
* minimum : optional, the minimum integer value
* maximum : optional, the maximum integer value

Similar to **varint**, except using [ZigZag encoding](https://protobuf.dev/programming-guides/encoding/#signed-ints) for signed integers. Intended for 32-bit signed numbers.

### **zigzag64** ({ ?minimum: Integer, ?maximum: Integer })
Arguments:
* minimum : optional, the minimum integer value
* maximum : optional, the maximum integer value

Same as **zigzag32**, but for 64-bit signed integers in ZigZag encoding.
