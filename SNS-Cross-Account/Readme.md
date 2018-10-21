CloudFormation does not support SNS subscription cross-account.

The problem is that the SNS topic resource does not have any property neither any resource in CloudFormation to achieve this step needed before stablish the subscription as per the documentation:

“…
From account A, grant permission to account B to subscribe to the topic:

$ aws sns add-permission — label lambda-access — aws-account-id 12345678901B \
 — topic-arn arn:aws:sns:us-east-2:12345678901A:lambda-x-account \
 — action-name Subscribe ListSubscriptionsByTopic Receive — profile accountA …”

Basically this proof of concept works out of the box and deploys in an Account A a SNS topic and in Account B a lambda function that subscribes to the SNS topic in Account A based on the steps described in the documentation that require permissions to be added on both sides an set the subscription trigger.

Afterwards, we can scale up from by adding more stacks for different accounts containing lambdas that will subscribe automatically to the SNS topic.

We will define the following components on each account stack in order to achieve the above.

1.- Stack Account A (SNS Topic)

Parameters:

-Name of the S3 bucket we will create

-Account Number where the lambda needs to subscribe to the SNS Topic is.

Resources:

-SNS topic

-IAM role to grant SNS permissions on other accounts.

-S3 bucket with bucket policy that allows read permissions to account B.

-A Lambda function and a Lambda Role that pushes SNS topic in S3 bucket file

-A Custom Resource which invokes a lambda function that pushes sns topic arn value in s3 bucket file.

2.-Stack Account B (lambda and sns subscription )

Parameters:

-Name of the S3 bucket (same previously created in the source account)

Resources:

-Lambda function to subscribe to the SNS topic

-Custom resource that reads the files Account A uploaded (SNS topic arn) from account A S3 bucket and creates the subscription “assuming role” within the function after adding the permissions to the sns topic. We play with different sessions to perform an action on Account A and B from the same lambda.

-I create the lambda permission resource too . It will allow SNS to invoke the lambda function.

The approach showed regarding writing the values in S3 alleviates the lack of cross-account import/export support in CloudFormation. For example,
if you have 6 sns topics you could write their arn to an S3 file and then from the other stack read them all.


