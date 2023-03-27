
After creating a new user, the status is `FORCE_CHANGE_PASSWORD`. Then the authentication will be failed. The solution is to set the user password through AWS CLI.
```sh
aws cognito-idp admin-set-user-password --user-pool-id <user-pool-id> --username <user-name> --password <password> --permanent
```
