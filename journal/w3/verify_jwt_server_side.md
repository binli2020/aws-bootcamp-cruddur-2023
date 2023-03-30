
# Send the JWT in header in API request

The JWT is stored in the browser's local storage during signning in.

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


# Get the JWT from API request in server side

```py
from flask import request

@app.route("/api/activities/home", methods=['GET'])
def data_home():
  jwt_token = request.headers.get("Authorization")
  app.logger.debug(f"Auth token: {jwt_token}")
```

# Verify the JWT token

