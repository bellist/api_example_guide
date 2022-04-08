# FireMon API Authentication Guide
## Step 1: Retrieve Authorization Token
### Endpoint
```
POST {{base_url}}/securitymanager/api/authentication/login
```
### Request Headers
Key |Value           
----|---
Content-Type | application/json; charset=utf-8
### Request Body
```
{
  "username": "string",
  "password": "string"
}
```
### Example Response
```
{
    "authorized": true,
    "authCode": 0,
    "authStatus": "AUTHORIZED",
    "token": "string",
    "tokenTTL": 1800
}
```
The value for `token` will be used to authenticate future API calls.

## Step 2: Use Authorization Token
### Example Endpoint
This requrest will retrieve ticket JSON.
```
GET {{base_url}}/policyplanner/api/domain/{{domain_id}}/workflow/{{workflow_id}}/packet/{{workflow_packet_id}}
```
### Request Headers
Key |Value           
----|---
Content-Type | application/json; charset=utf-8
X-FM-Auth-Token | `token` value from Step 1
