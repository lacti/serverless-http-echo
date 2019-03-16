# serverless-http-echo

Hello world from AWS Serverless (feat. [serverless](https://github.com/serverless/serverless/)).

## Rationale

I want to build more services with [AWS Serverless model](https://aws.amazon.com/serverless/sam/) because I want to make a service with its backend but I don't want to server costs if there is no request from user.

This is a very basic example that how to build up a simple serverless application written by NodeJS and [serverless framework](https://github.com/serverless/serverless/).

## Prerequisites

I don't like this way, but it can be convenience if you install serverless framework globally.

```bash
npm i -g serverless
```

## Instruction

### Initialize.

Create a directory for our service and initialize with serverless command.

```bash
$ mkdir serverless-http-echo
$ cd serverless-http-echo
$ serverless create --name serverless-http-echo --template aws-nodejs-typescript
```

Yes, I love TypeScript ;)

Then we can see these files.

- .gitignore
- handler.ts
- package.json
- serverless.yml
- tsconfig.json
- webpack.config.json

If we don't want to more complex works, we can concentrate only two files `handler.ts` and `serverless.yml`.

#### `serverless.yml`

```yml
service:
  name: serverless-http-echo

plugins:
  - serverless-webpack

provider:
  name: aws
  runtime: nodejs8.10

functions:
  hello:
    handler: handler.hello
    events:
      - http:
          method: get
          path: hello
```

If we want to more API, add some functions and chain [events](https://serverless.com/framework/docs/providers/aws/events/) in this file. But it is not today, and not in this repository.

#### `handler.ts`

```ts
import { APIGatewayProxyHandler } from 'aws-lambda';
import 'source-map-support/register';

export const hello: APIGatewayProxyHandler = async (event, _context) => {
  return {
    statusCode: 200,
    body: JSON.stringify({
      message: 'Go Serverless Webpack (Typescript) v1.0! Your function executed successfully!',
      input: event,
    }),
  };
}
```

It already has a code that echoes its `event` that is an event from `AWS API Gateway` because it set at `serverless.yml` file.

So it's over ;)

### Edit somethings.

I want to give more things about me to `package.json`.

And it is actually meaningless, but I fix a bug that a `main` file is `handler.ts` instead of `handler.js` because it is a TypeScript project.

📄 package.json
```diff
  {
    "name": "serverless-http-echo",
    "version": "1.0.0",
-   "description": "Serverless webpack example using Typescript",
-   "main": "handler.js",
+   "description": "Echo API Gateway's request with AWS Serverless",
+   "main": "handler.ts",
    "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1"
    },
      "typescript": "^3.2.4",
      "webpack": "^4.29.0"
    },
-   "author": "The serverless webpack authors (https://github.com/elastic-coders/serverless-webpack)",
+   "author": "lactrious@gmail.com",
    "license": "MIT"
- }+ }
```

And give a region to `serverless.yml` to avoid to deploy a service into [`us-east-1` region](https://serverless.com/framework/docs/providers/aws/guide/deploying#tips).

📄 serverless.yml
```diff
  provider:
    name: aws
    runtime: nodejs8.10
+   region: ap-northeast-2
  
  functions:
    hello:
```

### Deploy

Ok, we are ready to deploy. Please set [aws credential configurations](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html) before deploying. If there is a profile to deploy, we can use a [`--aws--profile` parameter](https://serverless.com/framework/docs/providers/aws/guide/credentials#using-the-aws-profile-option) of `serverless cli`.

```bash
$ serverless deploy
```

That's all. It is deadly simple :)

```
Serverless: Bundling with Webpack...
Time: 1480ms
Built at: 2019-03-16 17:06:09
Serverless: Packaging service...
Serverless: Uploading CloudFormation file to S3...
Serverless: Uploading artifacts...
Serverless: Uploading service serverless-http-echo.zip file to S3 (68.71 KB)...
Serverless: Validating template...
Serverless: Updating Stack...
Serverless: Checking Stack update progress...
..............................
Serverless: Stack update finished...
Service Information
service: serverless-http-echo
stage: dev
region: ap-northeast-2
stack: serverless-http-echo-dev
resources: 10
api keys:
  None
endpoints:
  GET - https://0000000000.execute-api.ap-northeast-2.amazonaws.com/dev/hello
functions:
  hello: serverless-http-echo-dev-hello
layers:
  None
```

Using `curl`, check if it works correctly.

```
$ curl https://0000000000.execute-api.ap-northeast-2.amazonaws.com/dev/hello
{"message":"Go Serverless Webpack (Typescript) v1.0! Your function executed successfully!","input":{"resource":"/hello","path":"/hello",...omitted...}}
```

If we want to see the deployed information, just use `info` command.

```bash
$ serverless info
Service Information
service: serverless-http-echo
stage: dev
...omitted...
```

So now, we can deploy some functions like RESTful API via the serverless world. :)

---

Powered by [git-tutor](https://github.com/lesnitsky/git-tutor).