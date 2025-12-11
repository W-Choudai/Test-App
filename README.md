 
# Test App - Node.js with AWS Deployment

A full-stack web application built with Node.js, connected to a PostgreSQL database on AWS RDS, and deployed using AWS Elastic Beanstalk.

## ✨ Features

- RESTful API with Express.js
- PostgreSQL database integration
- User management (Create, Read)
- Deployed on AWS Elastic Beanstalk
- Cloud database with AWS RDS
- Responsive web interface

## 🛠 Tech Stack

- **Backend:** Node.js, Express.js
- **Database:** PostgreSQL (AWS RDS)
- **Deployment:** AWS Elastic Beanstalk
- **Version Control:** Git, GitHub

## 📦 Prerequisites

Before you begin, ensure you have the following installed:

- Node.js (v20.x or higher)
- npm
- PostgreSQL client 
- AWS CLI
- Elastic Beanstalk CLI 
- Git

## 🗄 Database Setup

### Creating the Database on AWS RDS

1. **Create RDS Instance:**
   - Go to AWS RDS Console
   - Click "Create database"
   - Choose PostgreSQL
   - Select Free tier template
   - Set master username: `postgres`
   - Set master password
   - Configure VPC and security groups
   - Make note of the endpoint

2. **Configure Security Group:**
   - Go to RDS instance → Connectivity & security
   - Click on VPC security group
   - Edit inbound rules
   - Add rule: Type = PostgreSQL, Port = 5432, Source = Your IP or 0.0.0.0/0

3. **Create the users table:**

Connect to your RDS instance:

```bash
psql -h your-rds-endpoint.rds.amazonaws.com -U postgres -d postgres
```

Run this SQL:

```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Insert Sample Data

```sql
INSERT INTO users (username, email) VALUES
('Lily', 'lily@email.com'),
('Skippy', 'skippy@email.com');
```

## ☁️ Deployment to AWS

### Prerequisites for AWS Deployment

1. **Install EB CLI:**

```bash
pip install awsebcli
```

2. **Configure AWS credentials:**

```bash
aws configure
```

### Deployment Steps

1. **Initialize Elastic Beanstalk:**

```bash
eb init
```

Select:
- Region: `ap-southeast-1` (Singapore)
- Application name: `myapp`
- Platform: Node.js
- Platform branch: Node.js 20

2. **Create Environment:**

```bash
eb create myapp-env --single
```

The `--single` flag creates a single-instance environment (no load balancer).

3. **Set Environment Variables:**

```bash
eb setenv DB_HOST=your-rds-endpoint.rds.amazonaws.com DB_PORT=5432 DB_NAME=postgres DB_USER=postgres DB_PASSWORD=your_password
```

4. **Deploy Application:**

```bash
eb deploy
```

5. **Open Application:**

```bash
eb open
```

### Configure RDS Security for Elastic Beanstalk

1. Get EB security group ID:
   - Go to EC2 Console → Instances
   - Find your EB instance
   - Copy the Security Group ID

2. Add to RDS security group:
   - Go to RDS Console → Your database
   - Click VPC security group
   - Edit inbound rules
   - Add rule: Type = PostgreSQL, Port = 5432, Source = EB security group ID

## 📡 API Endpoints

### GET /

- **Description:** Homepage
- **Response:** HTML page

### GET /users

- **Description:** Get all users
- **Response:**

```json
[
  {
    "id": 1,
    "username": "Lily",
    "email": "lily@email.com",
    "created_at": "2025-12-10T18:01:46.304Z"
  }
]
```

## 🔐 Environment Variables

Required environment variables:

| Variable | Description | Example |
|----------|-------------|---------|
| `DB_HOST` | PostgreSQL host | `myapp-db.xxx.rds.amazonaws.com` |
| `DB_PORT` | PostgreSQL port | `5432` |
| `DB_NAME` | Database name | `postgres` |
| `DB_USER` | Database user | `postgres` |
| `DB_PASSWORD` | Database password | `your_password` |
| `PORT` | Application port | `8080` (set by EB) |

## 🛡️ Important Notes

### SSL Connection

The database connection requires SSL for AWS RDS:

```javascript
const pool = new Pool({
  host: process.env.DB_HOST,
  port: process.env.DB_PORT,
  database: process.env.DB_NAME,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD,
  ssl: {
    rejectUnauthorized: false
  }
});
```


## 📄 Other Notes

- This project is for educational purposes.
- Built as part of AWS cloud deployment learning
- Deployed on December 10-11, 2025

