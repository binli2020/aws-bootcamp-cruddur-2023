Use this command to create a RDS instance on AWS

```sh
aws rds create-db-instance \
  --db-instance-identifier cruddur-db-instance \
  --db-instance-class db.t3.micro \
  --engine postgres \
  --engine-version  14.6 \
  --master-username root \
  --master-user-password <password> \
  --allocated-storage 20 \
  --availability-zone ap-southeast-2a  \
  --backup-retention-period 0 \
  --port 5432 \
  --no-multi-az \
  --db-name cruddur \
  --storage-type gp2 \
  --publicly-accessible \
  --storage-encrypted \
  --enable-performance-insights \
  --performance-insights-retention-period 7 \
  --no-deletion-protection
  ```

From AWS RDS, it shows a database instance is created.
![image](https://user-images.githubusercontent.com/71969513/229674360-11299c6f-149d-4ac7-aacb-1b8e6365a7db.png)
