# springboot-mesos-marathon-keycloak-openldap

## Configure Keycloak Manually

![keycloak](images/keycloak.png)

### Login

- To open `Keycloak` there are two ways

  1. In a terminal, run the commands below and access the link printed from the echo command
     ```
     KEYCLOAK_ADDR="$(curl -s http://localhost:8090/v2/apps/keycloak | jq -r '.app.tasks[0].host'):$(curl -s http://localhost:8090/v2/apps/keycloak | jq '.app.tasks[0].ports[0]')"

     echo "http://$KEYCLOAK_ADDR/auth/admin/"
     ```
  1. Using [`Marathon`](http://localhost:8090)

- The `Keycloak` website credentials
  ```
  Username: admin
  Password: admin
  ```

### Create a new Realm

- Go to top-left corner and hover the mouse over `Master` realm. Click the `Add realm` blue button that will appear
- Set `company-services` to the `Name` field and click `Create` button

### Create a new Client

- On the left menu, click `Clients`
- Click `Create` button
- Set `simple-service` to `Client ID` and click `Save` button
- In the `Settings` tab, set `http://localhost:9080/*` to `Valid Redirect URIs`. (just because it is mandatory as this field is not important for this example)
- On `Advanced Settings`, set `S256` to `Proof Key for Code Exchange Code Challenge Method`
- Click `Save` button
- In `Roles` tab
  - Click `Add Role` button
  - Set `USER` to `Role Name` and click `Save` button

### LDAP Integration

- On the left menu, click `User Federation`
- Select `ldap`
- Select `Other` for `Vendor`
- Set `ldap://openldap` to `Connection URL`
- Click `Test connection` button, to check if the connection is OK
- Set `ou=users,dc=mycompany,dc=com` to `Users DN`
- Set `(gidnumber=500)` to `Custom User LDAP Filter` (filter just developers)
- Set `cn=admin,dc=mycompany,dc=com` to `Bind DN`
- Set `admin` to `Bind Credential`
- Click `Test authentication` button, to check if the authentication is OK
- Click `Save` button
- Click `Synchronize all users` button

### Configure users imported

- On the left menu, click `Users`
- Click `View all users` button. 3 users should be shown
- Edit user `bgates` by clicking on its `ID` or `Edit` button
- In `Role Mappings` tab
    - Select `simple-service` in `Client Roles` combo-box
    - Select `USER` role present in `Available Roles` and click `Add selected`
    - `bgates` has now `USER` role as one of his `Assigned Roles`
- Do the same for the user `sjobs`
- Let's leave `mcuban` without `USER` role
