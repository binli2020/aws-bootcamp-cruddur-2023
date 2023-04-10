# Create a AWS lambda function

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

# Add environment variable

![image](https://user-images.githubusercontent.com/71969513/230811659-da2dd966-dfe2-4ae7-a42a-00030803167e.png)


# Add a layer for the lambda function

![image](https://user-images.githubusercontent.com/71969513/230814057-683dad83-133b-401d-b594-a8d851584210.png)

For the ARN of the layer, refer to [Psycopg2-lambda-layer on github](https://github.com/jetbridge/psycopg2-lambda-layer) to find the ARN of related region.

# Add trigger for the lambda function

In AWS Cognito user pool, under `User pool properties`, add lambda trigger.

![image](https://user-images.githubusercontent.com/71969513/230814576-4734bc2a-648c-40f0-8368-7d8b62386f57.png)

Add lambda trigger

![image](https://user-images.githubusercontent.com/71969513/230814833-93bd85a0-8288-43e5-b533-bb7282c02645.png)

# Connect the lambda function to a VPC

## In IAM, Create a policy to be added to the role

![image](https://user-images.githubusercontent.com/71969513/230820753-dca960e7-cb4e-426c-8ca1-162786330471.png)

![image](https://user-images.githubusercontent.com/71969513/230821152-06ad91a9-0a81-4c27-96d9-1de8cfc2a720.png)

## Add and attach a policy `AWSLambdaVPCAccessExecutionRole` to the role of the lambda function, so that the lambda can be connected to a VPC

![image](https://user-images.githubusercontent.com/71969513/230818679-a76e4b57-bc45-4d80-9504-edf82ff3f41c.png)

![image](https://user-images.githubusercontent.com/71969513/230818797-fe19ea8c-c2ee-450b-8052-2d92c202aabb.png)

Click "Add permissions", then "Attach polacies", then choose `AWSLambdaVPCAccessExecutionRole`.

## Connect the lambda function to a VPC
![image](https://user-images.githubusercontent.com/71969513/230817942-33f0d1f3-8dc5-483b-9ca1-b4381e4299c1.png)

After "Save", the VPC is connected to the lambda.

# Sign up an user in our app

It creates an user in the Cognito user pool, which trigger the lambda function to be called.
But an error log is observed in CloudWatch.
```log
column "email" of relation "users" does not exist
```

This is because the scheme of the database has not been updated. After updating it, the user creation is successful. And we see the user in the Postgres database `users` table.

![image](https://user-images.githubusercontent.com/71969513/230826104-bdce4ae3-0841-4c08-96f6-e2432da5222d.png)

