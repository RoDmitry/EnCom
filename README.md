# EnCom serialization format
## Made to replace old and loved JSON

Encoding Compact, Encoded for Communication

The main goal was to remove extra characters, allow binary data, and speed up parsing of strings.

There is no need to escape characters in strings. And it's capable to work with binary data as is.

Zero-copy for strings and bytes.

To make it even more compact, I encourage you to use keys as short as possible, one char is ideal as 0-9,a-z,A-Z. And if you have removed some value, then just don't use that key anymore (maybe mark it as deprecated somewhere). I guess you would ask: "Should I remember the alphabet to name it?", and the solution might be to name it as: `0-9` then `qwertyuiopasdf..`, so no need to remember the alphabet.

## Examples
### EnCom
```
admins{
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
}
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
            "number": 2,
        },
        {
            "adminGroupID": 4,
            "adminName": "",
            "balance": -2.3,
            "folder": "other",
            "number": 5,
        },
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
#### EnCom
```
1{{1:1 2:5=admin 3:10.4 4:4=main 5:2} {1:4 2:0= 3:-2.3 4:5=other 5:5}} 2:n 3:t 4{1:8=/tools/* 2:9=/static/*} 5:f
```

#### JSON
```
{"1": [{"1": 1, "2": "admin", "3": 10.4, "4": "main", "5": 2}, {"1": 4, "2": "", "3": -2.3, "4": "other", "5": 5}], "2": null, "3": true, "4": {"1": "/tools/*", "2": "/static/*"}, "5": false}
```


## Format specification
Separator - b' ', b'\n', b'\t', b'\r' (or actually it might be any character <= b' ', or less then 0x21)

Structure and array open with `{` and close with `}`

First structure/array don't have them, and if you didn't specify the structure (the layout), and it's not a map (structure), then it will always be an array

If you have an Array of Enums, then Enums inside don't have `{` and `}`

Space after `}` is not necessary

Strings and binary data have length stated (in bytes), like `8=sometext` for strings and `8~somedata` for bytes

To validate the size of string or bytes, you check for the following symbols after it: b'}', b' ', b'\n', b'\t', b'\r' or EOF
