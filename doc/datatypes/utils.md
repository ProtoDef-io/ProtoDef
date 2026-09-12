## Utils

### **buffer** ({ countType: Type, ?count: Countable, ?rest: Boolean })
Arguments:
* countType : the type of the length prefix
* count : optional (either count or countType), a reference to the field counting the elements, or a fixed size (an integer)
* rest : optional (either rest or count/countType), represent rest bytes as-is

Represents a raw bytes with count prefix/field or without it.

Example: An buffer prefixed by a varint length.
```json
[
    "buffer",
    { "countType": "varint" }
]
```

Example of value: `Buffer <01 02 03>` / `Buffer <fe ed 00 ff>`

### **bitfield** ([ { name: String, size: Integer, signed: Boolean } ])
Arguments:
* [array] : a field
* * name : the name of field
* * size : the size in bits
* * signed : is value signed

Represents a list of value with sizes that are not a multiple of 8bits.
The sum of the sizes must be a multiple of 8.

Example:

3 values, x, y and z with sizes in bits : 26, 12, 26. Notice that 26+12+26=64.
```json
[
  "bitfield",
  [
    { "name": "x", "size": 26, "signed": true },
    { "name": "y", "size": 12, "signed": true },
    { "name": "z", "size": 26, "signed": true }
  ]
]
```

Example of value: `{"x": 10, "y": 10, "z": 10}`

### **mapper** ({ type: Type, mappings: { [String]: Any, ... } })
Arguments:
* type : the type of the input
* mappings : a mappings object

Maps string to a values.

Example:

Maps a byte to a string, 1 to "byte", 2 to "short", 3 to "int", 4 to "long".
```json
[
  "mapper",
  {
    "type": "i8",
    "mappings": {
      "1": "byte",
      "2": "short",
      "3": "int",
      "4": "long"
    }
  }
]
```
Example of value: `"int"`

### **bitflags** ([ { type: string, flags: object | array, big?: boolean, shift?: number } ])
Arguments:
* type : The underlying integer type (eg varint, lu32).
* flags : Either an array of flag values from LSB to MSB, or an object containing a mappng of valueName => bitMask.
* big : 64+ bits. In langauges like javascript (where all numbers are 64-bit floating points), special data types may have to be used for integers greater than 32 bits, so this must be set to true if the `type` is using the special data type.
* shift : Specify if flags is an object and holds bit positions as values opposed to a bitmask.

Represents boolean flags packed into an integer. Similar to bitfields, but only intended for enumerated boolean flags (each flag occupies 1 bit), and supports arbitrary underlying integer types.

Example:

```json
[
  "bitflags",
  {
    "type": "lu32",
    "flags": ["onGround", "inAir"]
  }
]
```

or 
```yaml
[
  "bitflags",
  {
    "type": "lu32",
    "big": true,
    "flags": {
      "onGround": 0b1,
      "inAir": 0b10
    }
  }
]
```

Example of value to pass when writing: `{"flags": { "onGround": true, "inAir": false } }`. Likewise when reading you will get a similar object back, with a extra `_value` field holding the raw integer.


### **pstring** ({ countType: Type, ?count: Countable })
Arguments:
* countType : the type of the length prefix
* count : optional (either count or countType), a reference to the field counting the elements, or a fixed size (an integer)
* encoding: the encoding of the string, UTF-8 by default

Represents a string.

Example: A string length prefixed by a varint.
```json
[
  "pstring", { "countType": "varint", "encoding": "utf-8" }
]
```
Example of value: `"my string"`

### **hash** ({ alg: String, type: Type, body: Type })
Arguments:
* alg : the hash algorithm, currently only `crc32c`
* type : the type the hash is written and read as
* body : the type the value is serialized as before being hashed

Represents a hash of a value instead of the value itself: writing serializes the value as `body`, hashes the bytes and writes the hash as `type`; reading reads `type` and yields the hash. The value is not recoverable from the buffer, so the read side sees only the hash.

`alg` is an explicit list rather than whatever hashes the implementation's platform happens to offer, so that a protocol using `hash` means the same thing in every implementation:

| alg | digest |
| --- | --- |
| `crc32c` | a 4 byte unsigned integer (CRC-32C, the Castagnoli polynomial, reflected, all-ones init and final xor) |

Because the digest's width is fixed by `alg`, the size of a `hash` field is known without hashing anything. `type` must therefore be a type of constant size, at least as wide as the digest; a variable-length `type` such as `varint` is an error.

A CRC is unsigned. When `type` is signed (for example `i32`) the hash is written in two's complement, so it reads back the way a language with only signed integers would produce it.

Example: a CRC32C of an item component, written as a signed int like Minecraft's `HashedSlot` carries it.
```json
[
  "hash",
  {
    "alg": "crc32c",
    "type": "i32",
    "body": "ItemComponent"
  }
]
```

Example of value: `{ "id": 5, "level": 3 }` (whatever `body` accepts) / reads as `-486237565`
