# Protocol

ProtoDef defines a protocol json format. It organizes types in namespaces.

Example:
```json
{
  "types": {
    "pstring": "native",
    "varint": "native"
  },
  "namespace1": {
    "mytype": [
      "pstring",
      "varint"
    ],
    "namespace2": {
      "packet": "mytype"
    }
  }
}
```

## **types** : { [String]: Type | "native", ... }
Arguments:
* [object] : a type definition
* * [key] : the name
* * [value] : the type (or `"native"` if implemented)
* * * 0 : name of used type
* * * 1 : options of used type

## **[String]** : { [String]: Type | [namespace], ... }
Arguments:
* [object] : A namespace object
* * [key] : type name
* * [value] : namespace or type (see above) definition
