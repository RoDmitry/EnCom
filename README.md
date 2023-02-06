# EnCom serialization format
## Made to replace old and loved JSON

Encoding Compact, Encoded for Communication

The main goal was to remove extra characters, allow binary data, and speed up parsing of strings.

There is no need to escape characters in strings. And it's capable to work with binary data as is.

Zero-copy for string or bytes.

## Examples
### EnCom
```
web-app{
    servlet{
        {
            init-param{
                adminGroupID:4
                betaServer:1
                dataLog:1
                dataLogMaxSize:0=
                fileTransferFolder:18=fileTransferFolder
                log:1
            }
            servlet-name:10=cofaxTools
        }
        {
            init-param{
                cacheTemplatesStore:n
                cacheTemplatesTrack:100
                configGlossary:poweredBy:5=Cofax
                dataStoreMaxConns:100
                dataStoreName:5=cofax
                maxUrlLength:500
                templateOverridePath:0=
                templatePath:9=templates
                useDataStore:1
                useJSP:0
            }
            servlet-class:24=org.cofax.cds.CDSServlet
            servlet-name:8=cofaxCDS
        }
        {
            init-param{
                mailHost:5=mail1
                mailHostOverride:5=mail2
            }
            servlet-name:10=cofaxEmail
        }
    }
    servlet-mapping{
        cofaxTools:8=/tools/*
        fileServlet:9=/static/*
    }
    taglib{
        taglib-location:23=/WEB-INF/tlds/cofax.tld
    }
}
```

### JSON
```
{
    "web-app": {
        "servlet": [
            {
                "init-param": {
                    "adminGroupID": 4,
                    "betaServer": true,
                    "dataLog": 1,
                    "dataLogMaxSize": "",
                    "fileTransferFolder": "fileTransferFolder",
                    "log": 1
                },
                "servlet-name": "cofaxTools"
            },
            {
                "init-param": {
                    "cacheTemplatesStore": null,
                    "cacheTemplatesTrack": 100,
                    "configGlossary:poweredBy": "Cofax",
                    "dataStoreMaxConns": 100,
                    "dataStoreName": "cofax",
                    "maxUrlLength": 500,
                    "templateOverridePath": "",
                    "templatePath": "templates",
                    "useDataStore": true,
                    "useJSP": false
                },
                "servlet-class": "org.cofax.cds.CDSServlet",
                "servlet-name": "cofaxCDS"
            },
            {
                "init-param": {
                    "mailHost": "mail1",
                    "mailHostOverride": "mail2"
                },
                "servlet-name": "cofaxEmail"
            },
        ],
        "servlet-mapping": {
            "cofaxTools": "/tools/*",
            "fileServlet": "/static/*"
        },
        "taglib": {
            "taglib-location": "/WEB-INF/tlds/cofax.tld"
        }
    }
}
```

## Format specification
Separator - space (actually it might be any character <= b' ', or less then 0x21)

Structure and array open with `{` and close with `}`

First structure/array don't have them, and if you don't specify the structure, and it's not a map (structure), then it will always be an array

If you have an Array of Enums, then Enums inside don't have `{` and `}`

Space after `}` is not necessary

Strings and binary data have length stated (in bytes), like `8=sometext` for strings and `8~somedata` for bytes
