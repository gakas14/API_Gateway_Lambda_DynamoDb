# API_Gateway_Lambda_DynamoDB
Build an API Gateway with Lambda and DynamoDB

I. Create a lambda role and function:

1. Create a lambda role with permission to DynamoDB and CloudWatch Logs.
https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/blob/main/api_lambda_dynamodb_img/lambda_role.png

2. Create the lambda function
   create the lambda function name "apigateway_lambda_function," runtime with Python latest version, with execution role choose the lambda role created previously
   
   https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/blob/main/api_lambda_dynamodb_img/create_lambda_function1.png

   https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/blob/main/api_lambda_dynamodb_img/create_lambda_function2.png

   Replace the boilerplate coding with the following code snippet and click "Save"

   Python Code

      import boto3
   
      import json
   
      from __future__ import print_function
      
      print('Loading function')
   
      def lambda_handler(event, context):
       '''Provide an event that contains the following keys:
         - operation: one of the operations in the operations dict below
         - tableName: required for operations that interact with DynamoDB
         - payload: a parameter to pass to the operation being performed
       '''
   
       	operation = event['operation']
       	if 'tableName' in event:
           	dynamo = boto3.resource('dynamodb').Table(event['tableName'])
       	operations = {
           	'create': lambda x: dynamo.put_item(**x),
           	'read': lambda x: dynamo.get_item(**x),
           	'update': lambda x: dynamo.update_item(**x),
           	'delete': lambda x: dynamo.delete_item(**x),
            'list': lambda x: dynamo.scan(**x),
            'echo': lambda x: x,
            'ping': lambda x: 'pong'
       	}
   
       	if operation in operations:
        		return operations[operation](event.get('payload'))
       	else:
           		raise ValueError('Unrecognized operation "{}"'.format(operation))


4. Test Lambda Function
    https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/blob/main/api_lambda_dynamodb_img/test_event.png

    https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/blob/main/api_lambda_dynamodb_img/create_test_event.png

    https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/blob/main/api_lambda_dynamodb_img/test_lambda.png

    With status 200, meaning the lambda function is working. 

II. Create DynamoDB Table
Create the DynamoDB table that the Lambda function uses, with table name "apigate_table," partition key as "table_id," with default settings.
 
https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/blob/main/api_lambda_dynamodb_img/table_name.png

https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/blob/main/api_lambda_dynamodb_img/add_lambda.png

III. Create API
1.create a rest API call "lambda_DynamoDB_ops" 

https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/blob/main/api_lambda_dynamodb_img/create_rest_api.png

2. add resource call "DynamoDBManager" with path "/"
   
https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/blob/main/api_lambda_dynamodb_img/create_resource.png

3. Let's create a POST Method for our API, select the lambda function we create
   https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/blob/main/api_lambda_dynamodb_img/create_method.png

   https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/blob/main/api_lambda_dynamodb_img/add_lambda.png

4. Deploy the API
   select a new stage and give it a name, then deploy the API


5. Running the solution
To execute our API from the local machine, we are going to use the Curl command. You can choose to use Postman

curl -X POST -d "{\"operation\":\"create\",\"tableName\":\"apigate_table\",\"payload\":{\"Item\":{\"table_id\":\"1\",\"name\":\"Bob\"}}}" https://$API.execute-api.$REGION.amazonaws.com/production/DynamoDBManager


To validate that the item is indeed inserted into the DynamoDB table

https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/blob/main/api_lambda_dynamodb_img/create_method.png

Add a second item.

https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/blob/main/api_lambda_dynamodb_img/add_second_item.png

https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/blob/main/api_lambda_dynamodb_img/list_of_items.png

List the items via curl.

{
    "operation": "list",
    "tableName": "apigate_table",
    "payload": {
    }
}
https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/blob/main/api_lambda_dynamodb_img/list_all_items.png

