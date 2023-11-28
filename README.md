# API_Gateway_Lambda_DynamoDB
Build an API Gateway with Lambda and DynamoDB

I. Create a lambda role and function:

1. Create a lambda role with permission to DynamoDB and CloudWatch Logs.

<img width="1259" alt="lambda_role" src="https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/assets/74584964/f19c8839-9f38-441f-a79e-42d207060a81">

<img width="1271" alt="lambda" src="https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/assets/74584964/3983f56b-48ed-4e29-ba1d-8e5a671c648d">



3. Create the lambda function:

create the lambda function name "apigateway_lambda_function," runtime with Python latest version, with execution role choose the lambda role created previously
   
   <img width="1262" alt="create_lambda_function1" src="https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/assets/74584964/897dd9a0-0d6d-4cd9-bb0a-766b775e2758">


   <img width="1256" alt="create_lambda_function2" src="https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/assets/74584964/f30d6089-793b-4d81-ae34-2e5638544936">


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



5. Test Lambda Function
   
    <img width="1277" alt="test_event" src="https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/assets/74584964/54210384-7008-4a4a-a6bd-77f25d4a7524">

    <img width="1274" alt="create_test_event" src="https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/assets/74584964/8c311a39-eee6-4d0f-89d8-ac66a580cf89">


    <img width="1267" alt="test_lambda" src="https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/assets/74584964/1e2c9783-d3ab-4d92-adba-4fd5b8949029">



    With status 200, meaning the lambda function is working. 



II. Create DynamoDB Table

Create the DynamoDB table that the Lambda function uses, with table name "apigate_table," partition key as "table_id," with default settings.

 <img width="1261" alt="table_name" src="https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/assets/74584964/b2576f07-cbaf-4001-8ed7-ef378a01fde3">




III. Create API

1.create a rest API call "lambda_DynamoDB_ops" 

<img width="1268" alt="create_rest_api" src="https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/assets/74584964/82042e3e-007a-46f5-a970-042824e371fc">



2. add resource call "DynamoDBManager" with path "/"
   
<img width="1277" alt="create_resource" src="https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/assets/74584964/236a587a-ed24-4f94-840a-cba527be6490">



3. Let's create a POST Method for our API, select the lambda function we create
   
<img width="1261" alt="create_method" src="https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/assets/74584964/d49efd5e-64c4-4a79-9b19-72a406fd9f92">


   <img width="1261" alt="add_lambda" src="https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/assets/74584964/c9f35527-e7af-41e6-9add-c2be5bc35694">




5. Deploy the API
   
Select a new stage and give it a name, then deploy the API
   
![deploy](https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/assets/74584964/367bdebb-b989-4d8a-98da-5e097ecbcb39)




6. Running the solution
   
To execute our API from the local machine, we are going to use the Curl command. You can choose to use Postman


curl -X POST -d "{\"operation\":\"create\",\"tableName\":\"apigate_table\",\"payload\":{\"Item\":{\"table_id\":\"1\",\"name\":\"Bob\"}}}" https://$API.execute-api.$REGION.amazonaws.com/production/DynamoDBManager



To validate that the item is indeed inserted into the DynamoDB table


<img width="1271" alt="list_of_items" src="https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/assets/74584964/a6147cb5-040d-4c34-afd3-623f1d90423c">



Add a second item.

<img width="570" alt="add_second_item" src="https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/assets/74584964/be06d853-fdfb-475c-8431-7a938426fc1e">



<img width="1271" alt="list_of_items" src="https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/assets/74584964/08d028bc-42aa-4fe1-ae2c-a0124a2c6f12">



List the items via curl.

{
    "operation": "list",
    "tableName": "apigate_table",
    "payload": {
    }
}


<img width="1192" alt="list_all_items" src="https://github.com/gakas14/API_Gateway_Lambda_DynamoDb/assets/74584964/9ff11778-7d2f-44df-850a-e4bd16c83800">


