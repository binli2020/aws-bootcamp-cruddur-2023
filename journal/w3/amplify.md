# What is AWS Amplify
[Refer to the AWS web page](https://aws.amazon.com/amplify/)


# Why we need to use AWS Amplify
The only way that we can use Cognito client-side is by using the Amplify JavaScript library.
  
# Authentication with Amplify
[Document](https://docs.amplify.aws/lib/auth/emailpassword/q/platform/js/)

## Install AWS Amplify Library
```sh
npm install aws-amplify --save
```

The `--save` option records the new installed library into the `package.json` file.

## Configure Amplify

The experience using Amplify UI was not great according to Andrew, that's why we are building complete custom pages.

We need to hook up our cognito pool to our code in the `App.js`

```js
import { Amplify } from 'aws-amplify';

Amplify.configure({
  "AWS_PROJECT_REGION": process.env.REACT_AWS_PROJECT_REGION,
  "aws_cognito_region": process.env.REACT_APP_AWS_COGNITO_REGION,
  "aws_user_pools_id": process.env.REACT_APP_AWS_USER_POOLS_ID,
  "aws_user_pools_web_client_id": process.env.REACT_APP_CLIENT_ID,
  "oauth": {},
  Auth: {
    // We are not using an Identity Pool
    // identityPoolId: process.env.REACT_APP_IDENTITY_POOL_ID, // REQUIRED - Amazon Cognito Identity Pool ID
    region: process.env.REACT_AWS_PROJECT_REGION,           // REQUIRED - Amazon Cognito Region
    userPoolId: process.env.REACT_APP_AWS_USER_POOLS_ID,         // OPTIONAL - Amazon Cognito User Pool ID
    userPoolWebClientId: process.env.REACT_APP_AWS_USER_POOLS_WEB_CLIENT_ID,   // OPTIONAL - Amazon Cognito Web Client ID (26-char alphanumeric string)
  }
});
```

Set related environment variables in `frontend-react-js` service in the `docker-compose.yml`
```sh
  frontend-react-js:
    environment:
      ... ...
      REACT_APP_AWS_PROJECT_REGION: "${AWS_DEFAULT_REGION}"
      REACT_APP_AWS_COGNITO_REGION: "${AWS_DEFAULT_REGION}"
      REACT_APP_AWS_USER_POOLS_ID: "..."
      REACT_APP_CLIENT_ID: "..."
```
