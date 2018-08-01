As you know is not possible to keep the stopped tasks history for ECS as they are cleared after a while. As stated for the ListTask API reference “stopped tasks appear in the returned results for at least one hour."

I have created a solution that works by just launching a CloudFormation template and specifying your ECS cluster name.

It will create an ECS Cloudwatch Event along a specific CloudWatch log group specifically for your cluster where all the tasks status changes will be logged in CloudWatch logs by a lambda function that will parse the event JSON Object streamed from CloudWatch Events.

In order to keep this data you could use Dynamodb or ElasticSearch but CloudWatch logs are cheaper and easier to rotate.

From now on you can keep forever or for a number of days you specify the status for your ECS tasks.

