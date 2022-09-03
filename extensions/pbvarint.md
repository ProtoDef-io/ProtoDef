## pbvarint
* Author - [rom1504](https://github.com/rom1504)
* Date - September 2016
* **Currently in standard specification as [`varint`](https://github.com/ProtoDef-io/ProtoDef/blob/master/doc/datatypes/numeric.md#varint--)**

### Implementations
- NodeJS: [node-protodef](https://github.com/protodef-io/node-protodef) (builtin)
- NodeJS: [node-protodef-neo](https://github.com/saiv46/node-protodef-neo) (builtin)

## Specification

### **pbvarint** ( )
Arguments: None

[Protobuf](https://developers.google.com/protocol-buffers/docs/encoding#varints)-compatible representation for variable-length integers using one or more bytes.

Example of value: `300` (size is 2 bytes)