# Configure Investigations

Many Graphistry investigation configurations can be set through environment variables (your `data/config/custom.env`), in your `config/pivot-db/config/config.json`, or in the admin panel.

These control aspects including:

* Connector auth and defaults: Splunk, Neo4j, ...
* Layouts
* Ontology: See [main custom ontology docs](configure-ontology.md)
* Custom pivots: See [main custom pivot docs](configure-custom-pivots.md)
* Prepopulated investigation steps

After editing, restart your server, or at least `pivot`.

For broader configuration information, see the [main configuration docs](configure.md). Create [custom investigation templates](templates.md) directly from within the UI.

# Example

Set log level to debug:

Via `.env`:

```
GRAPHISTRY_LOG_LEVEL=DEBUG
```

Via `data/investigations/config/config.json`:
```
{
  "log": {
    "level": "DEBUG"
  }
}
```

After setting these, restart your server:

* Full: `user@server.com : /var/graphistry $ docker-compose stop && docker-compose up -d`
* Pivot: `user@server.com : /var/graphistry $ docker-compose stop nginx pivot && docker-compose up -d`


# Schema

See also: [main ontology docs](configure-ontology.md)

```
{
    "env": {
        "doc": "The applicaton environment.",
        "format": {
            "0": "production",
            "1": "development",
            "2": "test"
        },
        "default": "development",
        "env": "NODE_ENV"
    },
    "host": {
        "doc": "Pivot host name/IP",
        "format": "ipaddress",
        "default": "0.0.0.0",
        "env": "PIVOT_HOST_IP"
    },
    "port": {
        "doc": "Pivot port number",
        "format": "port",
        "default": 8080,
        "arg": "port",
        "env": "PORT"
    },
    "layouts": {
        "network": {
            "ipInternalAcceptList": {
                "doc": "Array of strings and JavaScript regexes for IPs considered internal beyond those in RFC 1918",
                "format": "array",
                "arg": "internal-ips",
                "env": "PIVOT_INTERNAL_IP_ACCEPTLIST"
            }
        },
        "parallelCoordinates": {
            "orders": {
                "doc": "JSON dictionary naming axis column name orders. Defaults to key 'default' if available:\n                  {\n                    default:  [ 'dest_ip', 'src_ip' ],\n                    myOrder1: [ 'src_ip', 'dest_ip', 'time' ]\n                    ... \n                  }",
                "format": "object",
                "default": {},
                "arg": "parallel-coords-axes",
                "env": "GRAPHISTRY_PARALLEL_COORDS_AXES"
            }
        }
    },
    "authentication": {
        "passwordHash": {
            "doc": "Bcrypt hash of the password required to access this service, or unset/empty to disable authentication (default)",
            "format": "string",
            "default": "",
            "arg": "password-hash",
            "env": "PIVOT_PASSWORD_HASH",
            "sensitive": true
        },
        "username": {
            "doc": "The username used to access this service",
            "format": "string",
            "default": "admin",
            "arg": "username",
            "env": "PIVOT_USERNAME"
        }
    },
    "features": {
        "axes": {
            "format": "boolean",
            "default": true
        }
    },
    "systemTemplates": {
        "pivots": {
            "doc": "JSON list of pivots:\n                [{template, name, id, tags: [String],\n                    parameters: [{name, inputType, label, placeholder}]}],\n                    nodes: ?[String],\n                    attributes: ?[String],\n                    encodings: ?{size/icon/color:{<name>: <value>}}]",
            "format": "array",
            "default": {},
            "arg": "pivots",
            "env": "GRAPHISTRY_PIVOTS"
        }
    },
    "ontology": {
        icons: {
            doc: `JSON dictionary from entity type to icon name:
                { "myType": "car", ... }`,
            format: Object,
            default: {},
            arg: 'icons',
            env: 'GRAPHISTRY_ICONS'
        },
        colors: {
            doc: `JSON dictionary from entity type to color hex code:
                { "myType": "#FF0000", ... }`,
            format: Object,
            default: {},
            arg: 'colors',
            env: 'GRAPHISTRY_COLORS'
        },
        sizes: {
            doc: `JSON dictionary from entity type to size integers (1-1000), with Graphistry using 40/80/100/150:
                { "myType": 100, ... }`,
            format: Object,
            default: {},
            arg: 'sizes',
            env: 'GRAPHISTRY_SIZES'
        },        
        products: {
            doc: `JSON list of per-product dictionaries:
              [{"name": "myProduct1",
                ? productIdentifier: {"field1": "value1", ...}, // index selector
                ? fieldsBlacklist: [ "field1", ... ], //exclude from data extraction
                ? attributesBlacklist: [ "field1", ... ], //exclude from entity drilldown
                ? entitiesBlacklist: [ "field1", ... ], //exclude from generated nodes
                ? defaultFields: [ "field1", ... ], //populate dropdowns
                ? desiredEntities: [ "field1", ...], //default nodes
                ? desiredAttributes: [ "field1", ...], //default drilldowns
                ? colTypes: { "col1": "type1", ... } //what node type to generate from a column
            }, ...]`,
            format: Object,
            default: {},
            arg: 'products',
            env: 'GRAPHISTRY_PRODUCTS'
        },
        titles: {
            byType: {
                doc: `JSON dictionary from entity type to field name:
                    { "myType": "myField", ... }`,
                format: Object,
                default: {},
                arg: 'titles_by_type',
                env: 'GRAPHISTRY_TITLES_BY_TYPE'
            },
            byField: {
                doc: `Array of case-sensitive titles to use, highest-priority first. If no matches, use edgeTitle, pointTitle, else the ID.`,
                format: Array,
                default: ['label', 'name', 'title', "Label", "Name", "Title"],
                arg: 'titles_by_field',
                env: 'GRAPHISTRY_TITLES_BY_FIELD'
            }
        }
    },
    "pivotApp": {
        "mountPoint": {
            "doc": "Pivot mount point",
            "format": "string",
            "default": "/pivot",
            "arg": "pivot-mount-point"
        },
        "cachePoint": {
            "doc": "Nginx caching point",
            "format": "string",
            "default": "/cached",
            "arg": "pivot-cache-point"
        },
        "dataDir": {
            "doc": "Directory to store investigation files",
            "format": "string",
            "default": "data",
            "arg": "pivot-data-dir"
        }
    },
    "log": {
        "level": {
            "doc": "Log levels - ['TRACE', 'DEBUG', 'INFO', 'WARN', 'ERROR', 'FATAL']",
            "format": {
                "0": "TRACE",
                "1": "DEBUG",
                "2": "INFO",
                "3": "WARN",
                "4": "ERROR",
                "5": "FATAL"
            },
            "default": "INFO",
            "arg": "log-level",
            "env": "GRAPHISTRY_LOG_LEVEL"
        },
        "file": {
            "doc": "Log so a file intead of standard out",
            "format": "string",
            "arg": "log-file",
            "env": "LOG_FILE"
        },
        "logSource": {
            "doc": "Logs line numbers with debug statements. Bad for Perf.",
            "format": "boolean",
            "default": false,
            "arg": "log-source",
            "env": "LOG_SOURCE"
        }
    },
    "graphistry": {
        "key": {
            "doc": "Graphistry's api key",
            "format": "string",
            "arg": "graphistry-key",
            "env": "GRAPHISTRY_KEY",
            "sensitive": true
        },
        "host": {
            "doc": "The location of Graphistry's Server",
            "format": "string",
            "default": "http://graphistry",
            "arg": "graphistry-host",
            "env": "GRAPHISTRY_HOST"
        }
    },
    "pivots": {
        "show": {
            "doc": "Pivots to show; undefined means all. See load sequence output for available options. Ex: search-splunk-fireeye-botnet-demo,expand-fireeye-botnet-demo",
            "format": "array",
            "arg": "pivots-show",
            "env": "PIVOTS_SHOW"
        },
        "hide": {
            "doc": "Pivots to hide; undefined means none. See load sequence output for available options. Ex: search-splunk-fireeye-botnet-demo,expand-fireeye-botnet-demo",
            "format": "array",
            "arg": "pivots-hide",
            "env": "PIVOTS_HIDE"
        }
    },
    "neo4j": {
        "bolt": {
            "doc": "Neo4j BOLT endpoint, e.g., bolt://...:24786",
            "format": "string",
            "arg": "neo4j-bolt",
            "env": "NEO4J_BOLT"
        },
        "user": {
            "doc": "Neo4j user name",
            "format": "string",
            "arg": "neo4j-user",
            "env": "NEO4J_USER"
        },
        "password": {
            "doc": "Neo4j password",
            "format": "string",
            "arg": "neo4j-password",
            "env": "NEO4J_PASSWORD",
            "sensitive": true
        },
        searchMaxTime: {
            doc: 'Maximum time (in seconds) allowed for executing a Neo4j search query.',
            format: Number,
            default: 20,
            arg: 'neo4j-search-max-time',
            env: 'NEO4J_SEARCH_MAX_TIME'
        },
        metadata: {
            doc: 'Transaction metadata',
            format: Object,
            default: undefined,
            arg: 'neo4j-metadata',
            env: 'NEO4J_METADATA'
        }
    },
    "elasticsearch": {
        "host": {
            "doc": "The hostname of the Elasticsearch Server",
            "format": "string",
            "arg": "es-host",
            "env": "ES_HOST"
        },
        "port": {
            "doc": "Elasticsearch port",
            "format": "number",
            "default": 9200,
            "arg": "es-port",
            "env": "ES_PORT"
        },
        "version": {
            "doc": "Elasticsearch version as major.minor (6.2, 5.6, ...), autodetects by default",
            "format": "string",
            "arg": "es-version",
            "env": "ES_VERSION"
        },
        "protocol": {
            "doc": "HTTP or HTTPS",
            "format": "string",
            "default": "http",
            "arg": "es-protocol",
            "env": "ES_PROTOCOL"
        },
        "auth": {
            "doc": "HTTP credentials -- user:password, or undefined",
            "format": "string",
            "arg": "es-auth",
            "env": "ES_AUTH"
        }
    },
    "vt": {
        "host": {
            "doc": "The VT host, you usually want to leave this alone",
            "format": "string",
            "default": "https://www.virustotal.com"
        },
        "fileReport": {
            "doc": "The file report path, you usually want to leave this alone",
            "format": "string",
            "default": "/vtapi/v2/file/report"
        },
        "key": {
            "doc": "The VT key, you might want one",
            "format": "string",
            "sensitive": false
        }
    },
    "splunk": {
        "key": {
            "doc": "Splunk password",
            "format": "string",
            "default": "admin",
            "arg": "splunk-key",
            "env": "SPLUNK_KEY",
            "sensitive": true
        },
        "user": {
            "doc": "Splunk user name",
            "format": "string",
            "default": "admin",
            "arg": "splunk-user",
            "env": "SPLUNK_USER"
        },
        "host": {
            "doc": "The hostname of the Splunk Server (splunk.example.com)",
            "format": "string",
            "arg": "splunk-host",
            "env": "SPLUNK_HOST"
        },
        "port": {
            "doc": "Splunk API port",
            "format": "number",
            "default": 8089,
            "arg": "splunk-port",
            "env": "SPLUNK_PORT"
        },
        "uiPort": {
            "doc": "Splunk web UI port",
            "format": "number",
            "default": 443,
            "arg": "splunk-web-port",
            "env": "SPLUNK_WEB_PORT"
        },
        "scheme": {
            "doc": "Splunk protocol",
            "format": {
                "0": "http",
                "1": "https"
            },
            "default": "https",
            "arg": "splunk-scheme",
            "env": "SPLUNK_SCHEME"
        },
        "suffix": {
            "doc": "Splunk url suffix, e.g., en-US in mysplunk.com/en-US/app/search",
            "format": "string",
            "default": "/en-US",
            "arg": "suffix",
            "env": "SPLUNK_SUFFIX"
        },
        "jobCacheTimeout": {
            "doc": "Time (in seconds) during which Splunk caches the query results. Set to -1 to disable caching altogether",
            "format": "number",
            "default": 14400,
            "arg": "splunk-cache-timeout",
            "env": "SPLUNK_CACHE_TIMEOUT"
        },
        "searchMaxTime": {
            "doc": "Maximum time (in seconds) allowed for executing a Splunk search query.",
            "format": "number",
            "default": 20,
            "arg": "splunk-search-max-time",
            "env": "SPLUNK_SEARCH_MAX_TIME"
        },
        "other": {
            doc: 'Unsafe: Arbitrary additional paramemters to send to Splunk JavaScript SDK connector initialization',
            format: Object,
            default: {},
            arg: 'splunk-other',
            env: 'SPLUNK_OTHER'
        }
    }
}
```        
