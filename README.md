# EnCom - compact human-readable serialization format

*Encoding Compact, Encoded for Communication*

The main goal was to remove extra characters, allow binary data, and speed up parsing of strings.

There is no need to escape characters in strings. And it's capable to work with binary data as is.

The design downside is that it's not allowed to have keys with `:` or separator chars (any character <= ' '), where Json allows any string.

Zero-copy for strings and binary bytes.

## Examples
### EnCom
```
admins[
    {
        adminGroupID:1
        adminName:5=admin
        balance:10.4
        folder:4=main
        number:2
    }
    {
        adminGroupID:4
        adminName:0=
        balance:-2.3
        folder:5=other
        number:5
    }
]
cacheFolder:n
isGood:t
mapping{
    files:9=/static/*
    tools:8=/tools/*
}
useJSON:f
```

### JSON
```
{
    "admins": [
        {
            "adminGroupID": 1,
            "adminName": "admin",
            "balance": 10.4,
            "folder": "main",
            "number": 2
        },
        {
            "adminGroupID": 4,
            "adminName": "",
            "balance": -2.3,
            "folder": "other",
            "number": 5
        }
    ],
    "cacheFolder": null,
    "isGood": true,
    "mapping": {
        "files": "/static/*",
        "tools": "/tools/*"
    },
    "useJSON": false
}
```

### Compact
In this example, to make it even more compact, I've used short (one char long) keys:

#### EnCom
```
1[{1:1 2:5=admin 3:10.4 4:4=main 5:2}{1:4 2:0= 3:-2.3 4:5=other 5:5}] 2:n 3:t 4{1:9=/static/* 2:8=/tools/*} 5:f
```
111 characters (bytes)

#### JSON
```
{"1":[{"1":1,"2":"admin","3":10.4,"4":"main","5":2},{"1":4,"2":"","3":-2.3,"4":"other","5":5}],"2":null,"3":true,"4":{"1":"/static/*","2":"/tools/*"},"5":false}
```
160 characters (bytes)


## Format specification
Separators: ' ', '\n', '\t', '\r' (or actually it might be any character <= ' ', or less then 0x21).

Structure open with `{` and close with `}`.

Array open with `[` and close with `]`.

First structure/array don't have them, and if you didn't specify the structure (the layout), and it's not a map (structure), then it will always be an array.

If you have an Array of Enums, then Enums inside don't have `{` and `}`.

Space after `}` or `]` is not necessary.

Strings and binary data have length stated (in bytes), like `8=sometext` for strings and `8~somedata` for binary.

To validate the size of a string or bytes, you check for the following symbols after it: '}', ' ', '\n', '\t', '\r' or EOF.
