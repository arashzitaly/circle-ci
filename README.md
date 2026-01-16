# CircleCI CI/CD Lab — AWS Serverless Deploy

This repository is a hands-on CI/CD lab built while following the **LinkedIn Learning** course section **02_02 CircleCI**.  
Goal: implement a working CircleCI pipeline that builds and deploys a sample application to **AWS (serverless/Lambda)** using infrastructure outputs from **CloudFormation**.

---

## What this project demonstrates

- ✅ A real **CircleCI pipeline** with jobs + workflows
- ✅ **Environment-based deployments** (staging / production pattern)
- ✅ **Secure secrets handling** using CircleCI **Project Environment Variables**
- ✅ Deployment automation to **AWS Lambda** via **AWS CLI**
- ✅ Troubleshooting and fixing common CI/CD issues (credentials + variable naming)

---

## Pipeline overview

CircleCI runs an automated workflow that:

1. Checks out the repository
2. Runs build/test steps (if present)
3. Deploys to AWS using a Makefile target:
   - `make deploy FUNCTION=$STAGING_FUNCTION_NAME PLATFORM="CircleCI" VERSION=$CIRCLE_SHA1 BUILD_NUMBER=$CIRCLE_BUILD_NUM`

---

## Required CircleCI Environment Variables

Set these in:  
**CircleCI → Project Settings → Environment Variables**

### AWS Authentication (required)
These must match AWS CLI expected names exactly:

- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`
- `AWS_DEFAULT_REGION` (or `AWS_REGION`)
- `AWS_SESSION_TOKEN` (only if you use temporary credentials)

### Deployment configuration (required)
These must match what the pipeline/Makefile expects:

- `STAGING_FUNCTION_NAME`
- `PRODUCTION_FUNCTION_NAME` (if production deploy exists)
- `STAGING_URL` (if referenced)
- `PRODUCTION_URL` (if referenced)

> Values come from the **CloudFormation Stack Outputs** created by the lab.

---

## Common issues I fixed during the lab

### 1) AWS CLI: “Unable to locate credentials”
Cause: credentials were set with incorrect variable names (e.g., `AwsAccessKeyId`)  
Fix: use AWS CLI standard env vars (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, etc.)

### 2) Lambda deploy: “FunctionName value: 0”
Cause: `STAGING_FUNCTION_NAME` was missing or empty due to wrong variable naming  
Fix: set `STAGING_FUNCTION_NAME` exactly (uppercase + underscores)

---

## How to run locally (optional)

If you have AWS CLI configured locally and the project supports it:

```bash
make deploy FUNCTION=<your-lambda-function-name> PLATFORM="local" VERSION="dev" BUILD_NUMBER="0"
