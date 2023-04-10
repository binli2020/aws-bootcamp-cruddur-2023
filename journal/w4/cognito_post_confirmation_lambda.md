Create a AWS lambda function

![image](https://user-images.githubusercontent.com/71969513/230805668-afc1b22b-ef02-4860-b4d2-aa1b06206a24.png)

Paste the codes to the lambda function

```python
import os
import psycopg2

def lambda_handler(event, context):
    user = event['request']['userAttributes']
    print("***** USER *****: ")
    print(user)
    user_display_name = user["name"]
    user_handle = user["preferred_username"]
    user_email = user["email"]
    user_cognito_id = user["sub"]
    try:
        conn = psycopg2.connect(os.getenv('CONNECTION_URL'))
        cur = conn.cursor()
        sql = f"""
        INSERT INTO users (display_name, handle, email, cognito_user_id) VALUES({user_display_name}, {user_handle}, {user_email}, {user_cognito_id})
        """
        cur.execute(sql)
        conn.commit() 

    except (Exception, psycopg2.DatabaseError) as error:
        print(error)
        
    finally:
        if conn is not None:
            cur.close()
            conn.close()
            print('Database connection closed.')

    return event
```

Add environment variable

![image](https://user-images.githubusercontent.com/71969513/230811659-da2dd966-dfe2-4ae7-a42a-00030803167e.png)
