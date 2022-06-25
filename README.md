# iAppLX MSRA for nacos

This iApp is an example of MSRA for nacos, including an audit processor.  

## Build (requires rpmbuild)

    $ npm run build

Build output is an RPM package
## Using IAppLX from BIG-IP UI
If you are using BIG-IP, install f5-iapplx-msra-nacos RPM package using iApps->Package Management LX->Import screen. To create an application, use iApps-> Templates LX -> Application Services -> Applications LX -> Create screen. Default IApp LX UI will be rendered based on the input properties specified in basic pool IAppLX.

## Using IAppLX from REST API to configure BIG-IP

Using the REST API to work with BIG-IP with f5-iapplx-msra-nacos IAppLX package installed. 

Create an Application LX block with all inputProperties as shown below.
Save the JSON to block.json and use it in the curl call. Refer to the clouddoc link for more detail: https://clouddocs.f5.com/products/iapp/iapp-lx/tmos-14_0/iapplx_ops_tutorials/creating_iappslx_with_rest.html .

```json
{
  "name": "msranacos",
  "inputProperties": [
    {
      "id": "nacosEndpoint",
      "type": "STRING",
      "value": "http://1.1.1.1:8848",
      "metaData": {
        "description": "Nacos endpoint list, eg. http://1.1.1.1:8848 or https://1.1.1.2:8848",
        "displayName": "Nacos endpoints",
        "isRequired": true
      }
    },
    {
      "id": "nacosUserName",
      "type": "STRING",
      "value": "nacos",
      "metaData": {
        "description": "Nacos username",
        "displayName": "Nacos Username",
        "isRequired": false
      }
    },
    {
      "id": "nacosPassword",
      "type": "STRING",
      "value": "nacos",
      "metaData": {
        "description": "Nacos password",
        "displayName": "Nacos Password",
        "isRequired": false
      }
    },
    {
      "id": "namespaceId",
      "type": "STRING",
      "value": "public",
      "metaData": {
        "description": "The namespace ID in Nacos, public by default.",
        "displayName": "namespace ID in Nacos",
        "isRequired": false
      }
    },
    {
      "id": "groupName",
      "type": "STRING",
      "value": "DEFAULT_GROUP",
      "metaData": {
        "description": "The group name in Nacos, DEFAULT_GROUP by default.",
        "displayName": "Group name in Nacos",
        "isRequired": false
      }
    },
    {
      "id": "clusterName",
      "type": "STRING",
      "value": "DEFAULT",
      "metaData": {
        "description": "The cluster name in Nacos, DEFAULT by default.",
        "displayName": "Cluster name in Nacos",
        "isRequired": false
      }
    },
    {
      "id": "serviceName",
      "type": "STRING",
      "value": "msra-service166-onf5",
      "metaData": {
        "description": "Service name to be registered, which is the virtual name in F5",
        "displayName": "Service Name in registry",
        "isRequired": true
      }
    },
    {
      "id": "ipAddr",
      "type": "STRING",
      "value": "10.1.10.166",
      "metaData": {
        "description": "IP address to be registered",
        "displayName": "IP address",
        "isRequired": true
      }
    },
    {
      "id": "port",
      "type": "NUMBER",
      "value": 8080,
      "metaData": {
        "description": "port to be registered",
        "displayName": "Port",
        "isRequired": true
      }
    }
  ],
  "dataProperties": [
    {
      "id": "pollInterval",
      "type": "NUMBER",
      "value": 30,
      "metaData": {
        "description": "Interval of polling from BIG-IP to registry, 30s by default.",
        "displayName": "Polling Invertal",
        "isRequired": false
      }
    }
  ],
  "configurationProcessorReference": {
    "link": "https://localhost/mgmt/shared/iapp/processors/msranacosConfig"
  },
  "configProcessorTimeoutSeconds": 30,
  "statsProcessorTimeoutSeconds": 15,
  "configProcessorAffinity": {
    "processorPolicy": "LOAD_BALANCED",
    "affinityProcessorReference": {
      "link": "https://localhost/mgmt/shared/iapp/processors/affinity/load-balanced"
    }
  },
  "auditProcessorReference": {
    "link": "https://localhost/mgmt/shared/iapp/processors/msranacosEnforceConfiguredAudit"
  },
  "audit": {
    "intervalSeconds": 60,
    "policy": "ENFORCE_CONFIGURED"
  },
  "state": "TEMPLATE"
}
```

Post the block via RESR API using curl. 
```bash
curl -sk -X POST -d @block.json https://bigip_mgmt_ip:8443/mgmt/shared/iapp/blocks
```
