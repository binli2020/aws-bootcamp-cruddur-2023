# Conditionally show components based on logged in or logged out

The code changes is in this [commit `e4f2d8efe`](https://github.com/binli2020/aws-bootcamp-cruddur-2023/commit/e4f2d8efe023576bf48be473e2bd80f6c2af488c)

* After creating a new user, the status is `FORCE_CHANGE_PASSWORD`. Then the authentication will be failed. The solution is to set the user password through AWS CLI.

    ```sh
    aws cognito-idp admin-set-user-password --user-pool-id <user-pool-id> --username <user-name> --password <password> --permanent
    ```
