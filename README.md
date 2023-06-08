# 1 Introduction

Keycloak based access control layer solution with APISIX was designed to replace and give an alternative for Umbrella and Keyrock based solution.

# 2 Configurations

Deployment is via GitLab CI/CD pipeline. 

Deployment follows similar pattern like other components, Operator with Helm charts and substituting values to template files.
We manually configured a client in Keycloack, and then imported that using Keycloak’s import mechanism.
It’s recommended to follow this pattern, or then make regular backups of the database (postgres).
User creation and management can be done via CI/CD pipe, but this is quite cumbersome and recommend manual approach, if there are not too many user requests.

# 2.1 Intergration with APISIX

APISIX is configured to use [authz-keycloak](https://apisix.apache.org/docs/apisix/2.15/plugins/authz-keycloak/) plugin. This allows us to user Keycloak as and IDM with APISIX. 

# 3 User Management

There is one test users created in the CI/CD pipeline, and the repository has a test script, which can be used to test the deployment.

Users are managed by admin user. [Self service features](https://www.keycloak.org/docs/latest/server_admin/#proc-allow-user-to-delete-account_server_administration_guide) offered by Keycloak are not enabled.

# 4 Getting an access token

In order to get an access token, Keycloak provides a token endpoint:

```
curl --location --request POST 'https://keycloak.staging.odala.kiel.de/realms/apisix-realm/protocol/openid-connect/token' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'client_id=scorpio-client' \
--data-urlencode 'username=scorpiowriter' \
--data-urlencode 'password=xxx' \
--data-urlencode 'grant_type=password' \
--data-urlencode 'client_secret=xxx'
```
After that you can make a request towards Scorpio context broker:

```
curl --location --request GET 'https://scorpio-apisix.staging.odala.kiel.de/ngsi-ld/v1/entities/?type=TrafficFlowObserved' \
--header 'Authorization: Bearer eyJ...3A' \
--header 'Link: https://schema.lab.fiware.org/ld/context; rel=http://www.w3.org/ns/json-ld#context; type=application/ld+json'
```
and receive a response like:

```
[
    {
        "id": "urn:ngsi-ld:TrafficFlowObserved:61bb15be345c06d77f32e528",
        "type": "TrafficFlowObserved",
        "description": {
            "type": "Property",
            "value": "MQ13"
        },
        "address": {
            "type": "Property",
            "value": {
                "type": "PostalAddress",
                "streetAddress": "Alte Lu
                .
                .
                .
```
As an admin, you have access to the client application which holds the secrets needed for the requests. They also need to be in the CI/CD pipeline inorder the deployment to work.

# 5 Known issues / to be developed


Components- Keycloak

for the ODALA project.

© 2022 Contrasec Oy

License EUPL 1.2

![](https://ec.europa.eu/inea/sites/default/files/ceflogos/en_horizontal_cef_logo_2.png)
