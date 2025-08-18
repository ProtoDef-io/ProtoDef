# Extras Datatypes

This document describes the extra datatypes available in ProtoDef that provide advanced functionality for specific use cases.

## loop

The `loop` datatype reads a sequence of elements until a terminator or end of buffer is reached.

### Parameters

- `type` - The datatype of each element in the loop
- `nt` (null terminator) - Optional terminator value. Can be:
  - `null` - Read until end of buffer
  - A number - Read until this byte value is encountered (terminator is consumed)

### Usage Examples

#### Loop without terminator (read until EOF)
```javascript
const proto = new ProtoDef()

// Define a protocol that reads i8 values until end of buffer
proto.addType('numbers', ['loop', { type: 'i8', nt: null }])

// Parse a buffer containing [1, 2, 3, 4, 5]
const buffer = Buffer.from([1, 2, 3, 4, 5])
const result = proto.parsePacketBuffer('numbers', buffer)
console.log(result.data) // [1, 2, 3, 4, 5]
```

#### Loop with terminator
```javascript
const proto = new ProtoDef()

// Define a protocol that reads i8 values until 0 is encountered
proto.addType('terminated_numbers', ['loop', { type: 'i8', nt: 0 }])

// Parse a buffer containing [1, 2, 3, 0, 99] - 99 won't be read
const buffer = Buffer.from([1, 2, 3, 0, 99])
const result = proto.parsePacketBuffer('terminated_numbers', buffer)
console.log(result.data) // [1, 2, 3]
```

#### Loop with complex types
```javascript
const proto = new ProtoDef()

// Define a container type
proto.addType('point', ['container', [
  { name: 'x', type: 'i16' },
  { name: 'y', type: 'i16' }
]])

// Define a loop of points until end of buffer
proto.addType('points', ['loop', { type: 'point', nt: null }])

// Parse multiple points
const buffer = Buffer.from([0, 10, 0, 20, 0, 30, 0, 40]) // 4 bytes per point
const result = proto.parsePacketBuffer('points', buffer)
console.log(result.data) // [{ x: 10, y: 20 }, { x: 30, y: 40 }]
```

### Writing with loop
```javascript
const proto = new ProtoDef()
proto.addType('numbers', ['loop', { type: 'i8', nt: 0 }])

// Write an array with terminator
const data = [1, 2, 3, 4, 5]
const buffer = proto.createPacketBuffer('numbers', data)
console.log(buffer) // Buffer containing [1, 2, 3, 4, 5, 0]
```

## restBuffer

The `restBuffer` datatype reads all remaining bytes in the buffer and returns them as a Buffer object.

### Parameters

None - `restBuffer` takes no parameters.

### Usage Examples

#### Basic restBuffer usage
```javascript
const proto = new ProtoDef()

// Define a protocol with a header and remaining data
proto.addType('packet', ['container', [
  { name: 'header', type: 'u8' },
  { name: 'payload', type: 'restBuffer' }
]])

// Parse a buffer where first byte is header, rest is payload
const buffer = Buffer.from([42, 0x48, 0x65, 0x6c, 0x6c, 0x6f]) // 42 + "Hello"
const result = proto.parsePacketBuffer('packet', buffer)
console.log(result.data.header) // 42
console.log(result.data.payload) // Buffer containing [0x48, 0x65, 0x6c, 0x6c, 0x6f]
console.log(result.data.payload.toString()) // "Hello"
```

#### Protocol with multiple fields and restBuffer
```javascript
const proto = new ProtoDef()

proto.addType('message', ['container', [
  { name: 'version', type: 'u8' },
  { name: 'type', type: 'u8' },
  { name: 'length', type: 'u16' },
  { name: 'data', type: 'restBuffer' }
]])

const buffer = Buffer.from([1, 2, 0, 10, ...Buffer.from('Binary data here')])
const result = proto.parsePacketBuffer('message', buffer)
console.log(result.data)
/*
{
  version: 1,
  type: 2,
  length: 10,
  data: Buffer containing "Binary data here"
}
*/
```

#### Writing with restBuffer
```javascript
const proto = new ProtoDef()
proto.addType('simple', ['container', [
  { name: 'id', type: 'u8' },
  { name: 'payload', type: 'restBuffer' }
]])

const data = {
  id: 123,
  payload: Buffer.from('Some binary payload data')
}

const buffer = proto.createPacketBuffer('simple', data)
// Buffer will contain: [123, ...payload bytes]
```

## Use Cases

### loop
- **Variable-length arrays without length prefix**: When the array length isn't known ahead of time
- **Null-terminated lists**: Similar to C-style null-terminated strings but for any data type
- **Streaming data**: Reading elements until a sentinel value or end of stream
- **Protocol parsing**: Reading repeated elements until a stop condition

### restBuffer
- **Flexible payload handling**: When you need to capture all remaining data as binary
- **Nested protocol parsing**: Pass the remaining buffer to another parser
- **Variable-length data**: When the remaining data length is implicit
- **Raw data preservation**: Keeping binary data intact for further processing

## Performance Considerations

- `loop` reads elements one by one, so it may be slower than fixed-size arrays for large datasets
- `restBuffer` is very efficient as it just slices the remaining buffer
- Both types are fully supported in compiled mode for optimal performance
- Use `loop` with terminators when possible to avoid reading the entire buffer unnecessarily
