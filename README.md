<div>
  <h1 align= center>    
  <img src= https://floci.io/floci/assets/floci.svg>
</p>
  </h1>
<p align="center">
  <strong>Light, fluffy, and always free</strong><br />
  No account. No auth token. No feature gates. Just <code>docker compose up</code>.
</p>

<p align="center">
  <a href="https://github.com/floci-io/floci/releases/latest"><img src="https://img.shields.io/github/v/release/floci-io/floci?label=latest%20release&color=blue" alt="Latest Release"></a>
  <a href="https://github.com/floci-io/floci/actions/workflows/release.yml"><img src="https://img.shields.io/github/actions/workflow/status/floci-io/floci/release.yml?label=build" alt="Build Status"></a>
  <a href="https://hub.docker.com/r/floci/floci"><img src="https://img.shields.io/docker/pulls/floci/floci?label=docker%20pulls" alt="Docker Pulls"></a>
  <a href="https://hub.docker.com/r/floci/floci"><img src="https://img.shields.io/docker/image-size/floci/floci/latest?label=image%20size" alt="Docker Image Size"></a>
  <a href="https://opensource.org/licenses/MIT"><img src="https://img.shields.io/badge/license-MIT-green" alt="License: MIT"></a>
  <a href="https://github.com/floci-io/floci/stargazers"><img src="https://img.shields.io/github/stars/floci-io/floci?style=flat" alt="GitHub Stars"></a>
</p>

</div>


### Local AWS Emulator for Windows

Develop, test, and validate AWS workflows locally without creating real AWS resources.

Floci emulates AWS services on your machine, allowing developers to build and test cloud applications faster, cheaper, and safer during development and CI/CD workflows.

---

## Features

* Local AWS service emulation
* No AWS account required for testing
* Compatible with AWS CLI
* Docker support
* Native Floci CLI support
* Persistent local storage
* Fast local development workflow
* Ideal for CI/CD pipelines and automated testing

---

## Table of Contents
* Overview
* Prerequisites
* Installation

  * Docker Installation
  * Floci CLI Installation
  * AWS CLI Configuration
* Quick Start

  * Amazon S3
  * Amazon SQS
  * Amazon EC2 (Simulated)
* Persistence with Docker Compose
* Troubleshooting
* Command Reference
* License

---

# Overview

Floci is a local AWS emulator that allows developers to simulate AWS services directly on their machine.

Instead of deploying resources to AWS during development, you can test locally using the same APIs and AWS CLI commands.

Typical use cases:

* Local application development
* Integration testing
* CI/CD validation
* Learning AWS services
* Offline cloud experimentation

---

# Prerequisites

Before getting started, ensure the following tools are installed.

| Requirement    | Description                        |
| -------------- | ---------------------------------- |
| Windows 10/11  | Administrative access recommended  |
| Docker Desktop | Required for Docker deployment     |
| PowerShell     | Windows PowerShell or PowerShell 7 |
| AWS CLI        | Used to communicate with Floci     |
| Python & pip   | Optional for AWS CLI installation  |

---

# Installation

## Option 1: Run with Docker

### Pull the Floci Image

```powershell
docker pull floci/floci
```

### Start Floci

```powershell
docker run -d --name floci -p 4566:4566 floci/floci
```

### Verify Container Status

```powershell
docker ps
```

Expected output should include:

```text
floci/floci
0.0.0.0:4566->4566/tcp
```

---

### Using a Different Port

If port `4566` is already occupied:

```powershell
docker run -d --name floci -p 4570:4566 floci/floci
```

Access Floci using:

```text
http://localhost:4570
```

---

## Option 2: Install Using Floci CLI

### Install Floci

```powershell
iwr https://floci.io/install.ps1 | iex
```

---

### Add Floci to PATH

Current session:

```powershell
$env:Path += ";C:\Users\<YourUser>\AppData\Local\floci\bin"
```

Replace:

```text
<YourUser>
```

with your Windows username.

---

### Make PATH Permanent

1. Open **System Properties**
2. Navigate to:

```text
Advanced System Settings
→ Environment Variables
→ User Variables
→ Path
```

3. Add:

```text
C:\Users\<YourUser>\AppData\Local\floci\bin
```

4. Restart PowerShell

---

### Start Floci

```powershell
floci start
```

---

### Verify Installation

```powershell
floci doctor
```

---

### Stop Floci

```powershell
floci stop
```

---

# AWS CLI Configuration

AWS CLI requires credentials and a region even when communicating with a local emulator.

Use dummy values.

---

## Configure AWS CLI

```powershell
aws configure
```

Provide the following values:

```text
AWS Access Key ID     : test
AWS Secret Access Key : test
Default Region Name   : us-east-1
Output Format         : json
```

---

## Optional Environment Variable

Set the endpoint once per PowerShell session:

```powershell
$env:AWS_ENDPOINT_URL="http://localhost:4566"
```

---

## Verify Connection

```powershell
aws s3 ls --endpoint-url http://localhost:4566
```

---

# Quick Start Examples

## Amazon S3

Create a bucket, upload a file, and list objects.

### Create Bucket

```powershell
aws s3 mb s3://my-bucket --endpoint-url http://localhost:4566
```

---

### Upload File

```powershell
"hello floci" | Out-File -Encoding utf8 test.txt

aws s3 cp test.txt s3://my-bucket/ --endpoint-url http://localhost:4566
```

---

### List Objects

```powershell
aws s3 ls s3://my-bucket/ --endpoint-url http://localhost:4566
```

---

## Amazon SQS

Create a queue and exchange messages.

### Create Queue

```powershell
aws sqs create-queue `
  --queue-name my-queue `
  --endpoint-url http://localhost:4566
```

---

### Retrieve Queue URL

```powershell
$queueUrl = (
  aws sqs get-queue-url `
  --queue-name my-queue `
  --endpoint-url http://localhost:4566 `
  --output text
)
```

---

### Send Message

```powershell
aws sqs send-message `
  --queue-url $queueUrl `
  --message-body "hello" `
  --endpoint-url http://localhost:4566
```

---

### Receive Message

```powershell
aws sqs receive-message `
  --queue-url $queueUrl `
  --endpoint-url http://localhost:4566
```

---

## Amazon EC2 (Simulated)

Floci simulates EC2 API operations without creating actual virtual machines.

---

### Create Security Group

```powershell
aws ec2 create-security-group `
  --group-name my-sg `
  --description "My Security Group" `
  --endpoint-url http://localhost:4566
```

---

### Allow SSH Access

```powershell
aws ec2 authorize-security-group-ingress `
  --group-name my-sg `
  --protocol tcp `
  --port 22 `
  --cidr 0.0.0.0/0 `
  --endpoint-url http://localhost:4566
```

---

### Launch Instance (Simulated)

```powershell
aws ec2 run-instances `
  --image-id ami-12345678 `
  --count 1 `
  --instance-type t2.micro `
  --security-groups my-sg `
  --endpoint-url http://localhost:4566
```

---

### List Instances

```powershell
aws ec2 describe-instances `
  --endpoint-url http://localhost:4566
```

---

> **Important**
>
> Floci does not create real EC2 instances. All resources are simulated for testing purposes.

---

# Persistence Using Docker Compose

Use Docker Compose to preserve state between container restarts.

---

## docker-compose.yml

```yaml
version: "3.8"

services:
  floci:
    image: floci/floci
    container_name: floci

    ports:
      - "4566:4566"

    volumes:
      - ./data:/var/lib/floci
```

---

## Start Services

```powershell
docker-compose up -d
```

---

## View Logs

```powershell
docker-compose logs -f floci
```

---

## Stop Services

```powershell
docker-compose down
```

---

### Data Persistence

All Floci resources are stored in:

```text
./data
```

This includes:

* S3 buckets
* SQS queues
* Simulated EC2 resources
* Other supported service state

---

# Troubleshooting

## Port 4566 Already In Use

### Error

```text
Bind for 0.0.0.0:4566 failed:
port is already allocated
```

### Resolution

```powershell
docker ps
docker stop <container_id>
docker rm -f <container_id>
```

Or use another port:

```powershell
docker run -d --name floci -p 4570:4566 floci/floci
```

---

## PowerShell Parsing Errors

### Error

```text
Missing expression after unary operator '--'
```

### Resolution

Ensure flags are used inside an AWS command:

```powershell
aws s3 ls --endpoint-url http://localhost:4566
```

---

## Floci Command Not Found

### Error

```text
'floci' is not recognized
```

### Resolution

Add Floci to your system PATH and restart PowerShell.

---

## AWS CLI Credential Errors

### Resolution

Run:

```powershell
aws configure
```

Use dummy credentials and always provide:

```powershell
--endpoint-url http://localhost:4566
```

---

## EC2 Response Parsing Issues

Some advanced EC2 APIs may not be fully emulated.

Recommended commands:

```powershell
aws ec2 describe-instances
aws ec2 run-instances
```

---

## Can I SSH Into a Floci EC2 Instance?

No.

Floci simulates AWS API behavior only.

It does not provision actual virtual machines.

---

# Command Reference

## Docker

```powershell
docker run -d --name floci -p 4566:4566 floci/floci

docker ps

docker logs -f floci

docker stop floci

docker rm -f floci
```

---

## Floci CLI

```powershell
floci start

floci doctor

floci stop
```

---

## AWS CLI Examples

```powershell
aws s3 mb s3://my-bucket --endpoint-url http://localhost:4566

aws s3 cp test.txt s3://my-bucket/ --endpoint-url http://localhost:4566

aws sqs create-queue --queue-name my-queue --endpoint-url http://localhost:4566

aws ec2 describe-instances --endpoint-url http://localhost:4566
```

---

# License

This project documentation is provided as-is for educational, development, and internal usage.

You are free to modify and redistribute this document for your own projects.

---

## Contributing

Contributions, improvements, bug reports, and feature suggestions are welcome.

1. Fork the repository
2. Create a feature branch
3. Commit your changes
4. Submit a pull request

---

**Happy Local AWS Development with Floci 🚀**
