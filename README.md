# my-service serverless API

Generated using
https://github.com/awslabs/aws-serverless-java-container/
https://github.com/awslabs/aws-serverless-java-container/wiki/Quick-start---Spring-Boot

## Generating this example

This example were generated using following command

```bash
mvn archetype:generate \
 -DgroupId=my.service \
 -DartifactId=my-service \
 -Dversion=1.0-SNAPSHOT \
 -DarchetypeGroupId=com.amazonaws.serverless.archetypes \
 -DarchetypeArtifactId=aws-serverless-springboot2-archetype \
 -DarchetypeVersion=1.3
```

## Running locally

To run function locally use

```bash
pip install --user aws-sam-cli
./gradlew build -x test
sam local start-api --debug --skip-pull-image -s public -t sam.yaml -p 3000 \
    --parameter-overrides ParameterKey=CodeUri,ParameterValue=build/distributions/serverless-java-spring-boot.zip
```

Try API endpoint in terminal or browser

```
curl -s http://127.0.0.1:3000/ping | json_pp
```

## Running in cloud

Deploy to cloud using
```bash
./deploy-to-aws
```

Open URL provided in output, for example: https://uggwhkiale.execute-api.eu-west-1.amazonaws.com/Prod/ping

```bash
curl -s https://uggwhkiale.execute-api.eu-west-1.amazonaws.com/Prod/ping | json_pp
{
   "pong" : "Hello, World!"
}
```

Bench time to run, notice that the first run with the same or more concurrent number of requests takes 6 seconds (i.e. spring boot launch time)
```bash
ab -n 100 -c 20 https://uggwhkiale.execute-api.eu-west-1.amazonaws.com/Prod/ping
```

## Links

  * https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html
  * https://github.com/aws/aws-lambda-java-libs/tree/master/aws-lambda-java-events/src/main/java/com/amazonaws/services/lambda/runtime/events

## Template (FIXME: remove template README)
The my-service project, created with [`aws-serverless-java-container`](https://github.com/awslabs/aws-serverless-java-container).

The starter project defines a simple `/ping` resource that can accept `GET` requests with its tests.

The project folder also includes a `sam.yaml` file. You can use this [SAM](https://github.com/awslabs/serverless-application-model) file to deploy the project to AWS Lambda and Amazon API Gateway or test in local with [SAM Local](https://github.com/awslabs/aws-sam-local).

Using [Maven](https://maven.apache.org/), you can create an AWS Lambda-compatible zip file simply by running the maven package command from the projct folder.
```bash
$ mvn archetype:generate -DartifactId=my-service -DarchetypeGroupId=com.amazonaws.serverless.archetypes -DarchetypeArtifactId=aws-serverless-springboot2-archetype -DarchetypeVersion=1.3 -DgroupId=my.service -Dversion=1.0-SNAPSHOT -Dinteractive=false
$ cd my-service
$ mvn clean package

[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 6.546 s
[INFO] Finished at: 2018-02-15T08:39:33-08:00
[INFO] Final Memory: XXM/XXXM
[INFO] ------------------------------------------------------------------------
```

You can use [AWS SAM Local](https://github.com/awslabs/aws-sam-local) to start your project.

First, install SAM local:

```bash
$ npm install -g aws-sam-local
```

Next, from the project root folder - where the `sam.yaml` file is located - start the API with the SAM Local CLI.

```bash
$ sam local start-api --template sam.yaml

...
Mounting com.amazonaws.serverless.archetypes.StreamLambdaHandler::handleRequest (java8) at http://127.0.0.1:3000/{proxy+} [OPTIONS GET HEAD POST PUT DELETE PATCH]
...
```

Using a new shell, you can send a test ping request to your API:

```bash
$ curl -s http://127.0.0.1:3000/ping | python -m json.tool

{
    "pong": "Hello, World!"
}
``` 

You can use the [AWS CLI](https://aws.amazon.com/cli/) to quickly deploy your application to AWS Lambda and Amazon API Gateway with your SAM template.

You will need an S3 bucket to store the artifacts for deployment. Once you have created the S3 bucket, run the following command from the project's root folder - where the `sam.yaml` file is located:

```
$ aws cloudformation package --template-file sam.yaml --output-template-file output-sam.yaml --s3-bucket <YOUR S3 BUCKET NAME>
Uploading to xxxxxxxxxxxxxxxxxxxxxxxxxx  6464692 / 6464692.0  (100.00%)
Successfully packaged artifacts and wrote output template to file output-sam.yaml.
Execute the following command to deploy the packaged template
aws cloudformation deploy --template-file /your/path/output-sam.yaml --stack-name <YOUR STACK NAME>
```

As the command output suggests, you can now use the cli to deploy the application. Choose a stack name and run the `aws cloudformation deploy` command from the output of the package command.
 
```
$ aws cloudformation deploy --template-file output-sam.yaml --stack-name ServerlessSpringApi --capabilities CAPABILITY_IAM
```

Once the application is deployed, you can describe the stack to show the API endpoint that was created. The endpoint should be the `ServerlessSpringApi` key of the `Outputs` property:

```
$ aws cloudformation describe-stacks --stack-name ServerlessSpringApi
{
    "Stacks": [
        {
            "StackId": "arn:aws:cloudformation:us-west-2:xxxxxxxx:stack/ServerlessSpringApi/xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx", 
            "Description": "AWS Serverless Spring API - com.amazonaws.serverless.archetypes::aws-serverless-springboot2-archetype", 
            "Tags": [], 
            "Outputs": [
                {
                    "Description": "URL for application",
                    "ExportName": "MyServiceApi",
                    "OutputKey": "MyServiceApi",
                    "OutputValue": "https://xxxxxxx.execute-api.us-west-2.amazonaws.com/Prod/ping"
                }
            ], 
            "CreationTime": "2016-12-13T22:59:31.552Z", 
            "Capabilities": [
                "CAPABILITY_IAM"
            ], 
            "StackName": "ServerlessSpringApi", 
            "NotificationARNs": [], 
            "StackStatus": "UPDATE_COMPLETE"
        }
    ]
}

```

Copy the `OutputValue` into a browser or use curl to test your first request:

```bash
$ curl -s https://xxxxxxx.execute-api.us-west-2.amazonaws.com/Prod/ping | python -m json.tool

{
    "pong": "Hello, World!"
}
```