
# Verify Cognito JWT in a Flask Application

(This content is included in [my blog](https://binli.hashnode.dev/verify-cognito-jwt-in-a-flask-application))

Send Cognito JWT from React client side and verify it on the Flask Server Side 

# Send the Cognito JWT in header in API request

The JWT is stored in the browser's local storage during sign-in.

```js
Auth.signIn(email, password)
  .then(user => {
    console.log('user', user)
    localStorage.setItem("access_token", user.signInUserSession.accessToken.jwtToken)
    window.location.href = "/"
  })
```

Get the JWT from the local storage of the browser, and add it as value of `Authorization` parameter in the `headers` of the API request.

```js
  const backend_url = `${process.env.REACT_APP_BACKEND_URL}/api/activities/home`
  const res = await fetch(backend_url, {
    headers: {
      Authorization: `Bearer ${localStorage.getItem("access_token")}`
    },
    method: "GET"
  });
```


# Get the JWT from the API request in the server side

```py
from flask import request

@app.route("/api/activities/home", methods=['GET'])
def data_home():
  jwt_token = request.headers.get("Authorization")
  app.logger.debug(f"Auth token: {jwt_token}")
```

# Verify the Cognito JWT token on the server side

## Use the codes in [another repo Flask-AWSCognito](https://github.com/cgauge/Flask-AWSCognito)

Did some changes to make the codes usable in this repo. The new codes are list here.
```py
import time
import requests
from jose import jwk, jwt
from jose.exceptions import JOSEError
from jose.utils import base64url_decode


class FlaskAWSCognitoError(Exception):
    pass


class TokenVerifyError(Exception):
    pass


class CognitoJwtToken:
    def __init__(self, user_pool_id, user_pool_client_id, region, request_client=None):
        self.region = region
        if not self.region:
            raise FlaskAWSCognitoError("No AWS region provided")
        self.user_pool_id = user_pool_id
        self.user_pool_client_id = user_pool_client_id
        self.claims = None
        if not request_client:
            self.request_client = requests.get
        else:
            self.request_client = request_client
        self._load_jwk_keys()

    @classmethod
    def extract_access_token(self, auth_header):
        access_token = auth_header
        if auth_header and " " in auth_header:
            _, access_token = auth_header.split()
        return access_token

    def _load_jwk_keys(self):
        keys_url = f"https://cognito-idp.{self.region}.amazonaws.com/{self.user_pool_id}/.well-known/jwks.json"
        try:
            response = self.request_client(keys_url)
            self.jwk_keys = response.json()["keys"]
        except requests.exceptions.RequestException as e:
            raise FlaskAWSCognitoError(str(e)) from e

    @staticmethod
    def _extract_headers(token):
        try:
            headers = jwt.get_unverified_headers(token)
            return headers
        except JOSEError as e:
            raise TokenVerifyError(str(e)) from e

    def _find_pkey(self, headers):
        kid = headers["kid"]
        # search for the kid in the downloaded public keys
        key_index = -1
        for i in range(len(self.jwk_keys)):
            if kid == self.jwk_keys[i]["kid"]:
                key_index = i
                break
        if key_index == -1:
            raise TokenVerifyError("Public key not found in jwks.json")
        return self.jwk_keys[key_index]

    @staticmethod
    def _verify_signature(token, pkey_data):
        try:
            # construct the public key
            public_key = jwk.construct(pkey_data)
        except JOSEError as e:
            raise TokenVerifyError(str(e)) from e
        # get the last two sections of the token,
        # message and signature (encoded in base64)
        message, encoded_signature = str(token).rsplit(".", 1)
        # decode the signature
        decoded_signature = base64url_decode(encoded_signature.encode("utf-8"))
        # verify the signature
        if not public_key.verify(message.encode("utf8"), decoded_signature):
            raise TokenVerifyError("Signature verification failed")

    @staticmethod
    def _extract_claims(token):
        try:
            claims = jwt.get_unverified_claims(token)
            return claims
        except JOSEError as e:
            raise TokenVerifyError(str(e)) from e

    @staticmethod
    def _check_expiration(claims, current_time):
        if not current_time:
            current_time = time.time()
        if current_time > claims["exp"]:
            raise TokenVerifyError("Token is expired")  # probably another exception

    def _check_audience(self, claims):
        # and the Audience  (use claims['client_id'] if verifying an access token)
        audience = claims["aud"] if "aud" in claims else claims["client_id"]
        if audience != self.user_pool_client_id:
            raise TokenVerifyError("Token was not issued for this audience")

    def verify(self, token, current_time=None):
        """ https://github.com/awslabs/aws-support-tools/blob/master/Cognito/decode-verify-jwt/decode-verify-jwt.py """
        if not token:
            raise TokenVerifyError("No token provided")

        headers = self._extract_headers(token)
        pkey_data = self._find_pkey(headers)
        self._verify_signature(token, pkey_data)

        claims = self._extract_claims(token)
        self._check_expiration(claims, current_time)
        self._check_audience(claims)

        self.claims = claims

        return claims
```

## Call the code to verify JWT on the server side
Need to install a library `python-jose`. Add this line in `requirements.txt`

```sh
python-jose
```

Call the code to verify JWT.
```py
# For Cognito JWT token
from lib.cognito_jwt_token import CognitoJwtToken, TokenVerifyError

jwt_service = CognitoJwtToken(
  user_pool_id = os.getenv("AWS_COGNITO_USER_POOL_ID"),
  user_pool_client_id = os.getenv("AWS_COGNITO_USER_POOL_CLIENT_ID"),
  region = os.getenv("AWS_DEFAULT_REGION")
)

@app.route("/api/activities/home", methods=['GET'])
def data_home():
  auth_header = request.headers.get("Authorization")

  access_token = CognitoJwtToken.extract_access_token(auth_header)
  try:
    claims = jwt_service.verify(access_token)
    app.logger.debug(f"Authenticated")
    app.logger.debug(f"Claims: {claims}")
    data = HomeActivities.run(LOGGER, claims["username"])
  except TokenVerifyError as e:
    app.logger.debug(f"Unauthenticated")
    data = HomeActivities.run(LOGGER)

  return data, 200
```

Only when the JWT is verified successfully, the username in the claims is passed into `HomeActivities.run`. This is the way in the app to distinguish if the user is authenticated.
