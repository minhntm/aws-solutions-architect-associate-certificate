Serverless Stack: API Gateway, Lambda, DynamoDB, Cognito, SQS, SNS... (things that automatically scale without active adminstration or provision)

## Lambda

- Lambda scales out automatically
- Lambda functions are independent, 1 event = 1 function
- Lambda is serverless
- Lambda functions can trigger other Lambda functions, 1 event can trigger many functions if functions trigger other functions
- AWS X-ray allows you to debug