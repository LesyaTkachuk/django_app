# Deploy to Elastic Beanstalk

This project is just example, you can use it in your final project, but be shure that all other requirements are met.

## Create VPC with public and private subnets

Go to **VPC → VPCs → Create VPC**:

- **Resources to create**: `VPC and more`
- **VPC endpoints**: `None`
- **NAT gateway** is paid and is not required here, but recommended for security reasons (and we can use free NAT Instance instead of paid NAT gateway)
- All other settings you can leave as is or customize as you want

> [!NOTE]  
> Load balancer and RDS require **at least 2 subnets in different AZ**!

## Create Security groups for RDS and instances

Go to **VPC → Security → Security groups** or **EC2 → Network & Security → Security Groups**.

Create security groups in your VPC with following rules:

- Security group for EC2 instances:

  - **Type**: `SSH`

    **Source**: `Anywhere-IPv4` or `My IP`

- Security group for RDS instance:

  - **Type**: `PostgreSQL`

    **Source**: choose security group created for EC2 instances

## Create RDS with PostgreSQL in private subnet

Go to **Aurora and RDS → Subnet groups → Create DB subnet group**:

- **VPC**: Choose your VPC
- **Availability Zones**: choose all zones where you have created subnets
- **Subnets**: choose only private subnets

Go to **Aurora and RDS → Databases → Create database**:

- **Choose a database creation method**: `Standard create`
- **Engine type**: `PostgreSQL`
- **Virtual private cloud (VPC)**: choose your VPC
- **DB subnet group**: choose subnet group you have just created
- **Public access**: `No`
- **VPC security group (firewall)**: choose existing security group you created for RDS
- The last **Additional configuration → Database options → Initial database name** fill with your desired name for database which will be used in the `DATABASE_NAME` variable later (or create database manually)

## Create SSH key to securely log in to EC2 instances

**If you already have created key pair, you can skip this step**

Go to **EC2 → Network & Security → Key Pairs → Create key pair**

## Prepare archive for deployment

> [!NOTE]
> Commands below may differ depending on your operating system! `python` may be `python3` or `py`. Example tested on Linux and MacOS only!

Clone this repository

```bash
git clone  https://github.com/LesyaTkachuk/django_app
```

Go to `django_app` directory

```bash
cd django_app
```

Create and activate virtual environment, install dependencies

```bash
python -m venv .venv --prompt django_app
source .venv/bin/activate
pip install -r requirements.txt
```

Generate static resources

```bash
python manage.py collectstatic
```

Create archive

```bash
zip -r ../django_app.zip . -x '.venv/*' '.git/*' '*__pycache__*'
```

## Create application in Elastic Beanstalk

Configure environment

- **Environment tier**: `Web server environment`
- **Platform**: `Python`
- **Platform branch**: `Python 3.13 running on 64bit Amazon Linux 2023`

Configure service access

- **Service role**: choose to create new or use existing `aws-elasticbeanstalk-service-role` (if already exists)
- **EC2 instance profile**: choose to create new or use existing `aws-elasticbeanstalk-ec2-role` (if already exists)
- **EC2 key pair**: choose your key

Set up networking, database, and tags

- **Virtual Private Cloud (VPC)**: choose your VPC
- **Instance settings**:

  |                       |     with NAT gateway     |   without NAT gateway   |
  | :-------------------- | :----------------------: | :---------------------: |
  | **Public IP address** |      `⬜ Activated`      |     `☑️ Activated`      |
  | **Instance subnets**  | **private** subnets only | **public** subnets only |

- **Database** we have already created in previous step so we leave this as is
  Configure instance traffic and scaling
- **Instances**:
  - **EC2 security groups**: choose security group for EC2 instances
- **Capacity**:
  - **Auto scaling group**:
    - **Environment type**: `Load balanced`
- **Load balancer network settings**:
  - **Visibility**: `public`
  - **Load balancer subnets**: choose only public subnets
- **Load Balancer Type**: `Application load balancer`

Configure updates, monitoring, and logging

- **Platform software**:
  - **Environment properties**: create all required environment variables like `DJANGO_SECRET_KEY`, `DJANGO_ALLOWED_HOSTS`, `DATABASE_HOST` and others

## Connect to the instance

Connect to the NAT instance and create `private-key.pem` file with the pem key that was created and chosen at app deployment. For example, run `nano private-key.pem` and insert key value.

Then connect to the app instance using this key:

``` 
ssh -i private-key.pem <your-app-ec2-private-ip>
```

## Create superuser in django_app

Log in to one of instances and create `.env` file with appropriate values

```bash
export DJANGO_SECRET_KEY=
export DATABASE_NAME=
export DATABASE_USERNAME=
export DATABASE_PASSWORD=
export DATABASE_HOST=
```

`DJANGO_SECRET_KEY` could be generated on the site https://djecrety.ir/.
Database variables could be obtained as outputs after running `terraform init` `terraform apply` in `.tf/practical-lesson-3`

Source this file

```bash
source .env
```

Go to app directory, activate virtualenv and run `createsuperuser`

```bash
cd /var/app/current/
source /var/app/venv/*/bin/activate
python manage.py createsuperuser
```
