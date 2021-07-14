# Project Execution

Writing my own notes during the execution of the project. 


## Requirements

- usql: https://github.com/xo/usql
- node
- ionic: npm install -g @ionic/cli
- docker
- aws: create user with admin rights
- kubectl


### Create an user with admin rights

- Go to IAM page and create an user with name `Admin-Udacity`
- Grant Programmatic Access
- Grant `AdministratorAccess`
- Download credentials as CSV   
- Configure the user with `aws configure` then enter the credentials and specify the default region `ap-southeast-1`

Can run some command like the following to verify that the setup is succesfull.

```sh
aws iam get-user
or 
aws s3 ls
```

### Create s3 bucket

- name: `udgram-dev-784161089143`

Add the follwoing policy to the bucket:
```json
{
"Version":"2012-10-17",
"Statement":[
  {
      "Sid":"Stmt1625306057759",
      "Principal":"*",
      "Action":"s3:*",
      "Effect":"Allow",
      "Resource":"arn:aws:s3:::udgram-dev-784161089143"
  }
]
}
```

Add the following CORS policy:
```json
[
 {
     "AllowedHeaders":[
         "*"
     ],
     "AllowedMethods":[
         "POST",
         "GET",
         "PUT",
         "DELETE",
         "HEAD"
     ],
     "AllowedOrigins":[
         "*"
     ],
     "ExposeHeaders":[

     ]
 }
]
```

After this the public acces can be disabled. 


### Setup RDS

We can rely on the default settings of easy-setup. During the development we can enable the connections from anywere using the inbound rulers. Ensure that the public access is turned on. 

We can connect to the database usin `psql`:
```
psql -h udgram.ctkbnauge1dw.ap-southeast-1.rds.amazonaws.com -U postgres postgres
```

### Set up backend

Notthing special:
```
npm update --save
npm audit fix
npm install 
```

At first I had issues, cause the database migrator got stuck so I used the following to install the correct version of node:
```
nvm install 11.15.0
nvm list // check ur versions
nvm use 11.15.0
node -v
```

### Setup Frontend

Run:
```
    npm update --   save
    npm audit fix
    npm install .
```

Again had some issues with the node version, so I used nvm to swithc. 
After I ran the commands still some errors shown.

Then I ran: 
```
ionic build
```

Still having compatibility issues. Better to try to run it with docker..

For solving the issues I:
- installed node 12.18.02
- run npm clean-install

After that it started correctly. 

