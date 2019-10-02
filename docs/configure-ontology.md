# Configure Custom Graphistry Ontology

The Graphistry ontology is a set of mappings that guide automatic visualization and simplify data worklows. This document overviews the ontology and how to extend.

## Out-of-the-box ontologies

Graphistry supports out-of-the-box ontologies of common systems:

* Splunk CIM model
* ElasticSearch model
* Various vendor-specific models, such as FireEye HX/NX/iSIGHT and AWS CloudWatch

## The ontology's 4 mappings

The ontology defines:

* For each type, such as `user`:
  * Default icon: string name supported by [Font Awesome 4](https://fontawesome.com/v4.7.0/icons/), such as [user-o](https://fontawesome.com/v4.7.0/icon/user-o)
  * Default color: string hex value, such as `#F00` for red
  * Default size: number, typically between 10 and 200  
* New types:
For the automatic table -> graph transform (aka hypergraph transform), the mapping from _table column names_ to _node entity types_.
  
  
## How to extend the ontology

### Easiest: Ask Graphistry to do it for you! 

Ideally, you can provide representative sample data that has the columns and values of interest, and if a data schema is available, that too.

Ex: For Splunk users wanting support for a new product, provide the output of 
* `search index=some_product | fields * | dedup 20 event_type | head 1000`
* select all columns in the Field Selector
* download the CSV

### New types

For example, to create a new node type `ip`, 

1. Extend `data/investigations/config/config.json`:

```
{
  ...
  ontology: {
      icons: {
         'ip': 'device',
         ...,
      },
      colors: {
          'ip': '#F00',
          ...
      },
      sizes: {
          'ip': 100
      }
   ...
}   
```

2. Restart the pivot service:

```user@server.com:/var/graphistry $ docker-compose stop pivot nginx && docker-compose up -d```

### New hypergraph transforms

The existing ontology may already have all the types you want, but a new data source may have columns that need to be mapped into it.

For example, to recognize `src_ip` and `dest_ip` columns as both generating `ip`-type nodes:

1. Extend `data/investigations/config/config.json`:

```
{
   ...
   ontology: {
       products: [
           {
               name: "my_extension_1",
               colTypes: {
                   "src_ip": "ip",
                    "dest_ip": "ip"
               }
           }
       ]
   }
}
```

2. Restart the pivot service:
```
user@server.com:/var/graphistry $ docker-compose stop pivot nginx && docker-compose up -d
```
         
         
## Testing your ontology

1. Syntax errors: 

Graphistry tries to detect syntax error, and upon one, logs the error and stops. To see what is going on:

`docker ps` <- see if `pivot` is unhealthy or in a restart loop
`docker-compose logs pivot` <- see the precise error message

2. Satisfactory configuration

We recommend creating a Manual Data pivot. For example, to test various `ip` columns, use the following:

* Query: `[ {"src_ip": "10.10.0.0", "dest_ip": "10.10.0.1", "ip": "10.10.0.2"} ]`
* JQ: `.`
* Nodes: `src_ip`, `dest_ip`, `ip`