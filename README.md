# Serverless-Notifier-Workflow

## Overview

This repository contains a demonstration of a serverless application built using AWS services such as S3, API Gateway, Lambda, Step Functions, SNS, and SES. The application is designed to facilitate a notification workflow, allowing users to send emails triggered by certain events.

## Functionality

- **SES Configuration (STAGE 1):** Configures Amazon Simple Email Service (SES) to enable email functionality.
- **Email Lambda Function (STAGE 2):** Integrates a Lambda function to utilize SES for sending emails within the serverless application.
- **State Machine Implementation (STAGE 3):** Implements and configures a state machine, serving as the core component orchestrating the workflow.
- **API Gateway Setup (STAGE 4):** Establishes an API Gateway along with API endpoints and corresponding Lambda functions to handle incoming requests.
- **Frontend Application (STAGE 5):** Develops a static frontend application to interact with the serverless backend, enabling users to trigger notifications and test functionality.
- **Cleanup Procedure (STAGE 6):** Provides instructions for cleaning up resources and maintaining account hygiene post-application usage.

## Usage

- **Prerequisites:** Ensure you have an AWS account set up and necessary permissions to create and manage resources.
- **Installation:** Clone the repository and follow the setup instructions in the README file.
- **Deployment:** Deploy the serverless application using the provided deployment scripts or manually configure resources using AWS Management Console.
- **Testing:** Utilize the frontend application to trigger notifications and observe the workflow in action.
- **Cleanup:** Follow the provided cleanup instructions to remove resources and avoid unnecessary charges.

## `api_lambda.py`

```python
import boto3, json, os, decimal

SM_ARN = 'YOUR_STATEMACHINE_ARN'

sm = boto3.client('stepfunctions')

def lambda_handler(event, context):
    # Print event data to logs .. 
    print("Received event: " + json.dumps(event))

    # Load data coming from APIGateway
    data = json.loads(event['body'])
    data['waitSeconds'] = int(data['waitSeconds'])
    
    # Sanity check that all of the parameters we need have come through from API gateway
    # Mixture of optional and mandatory ones
    checks = []
    checks.append('waitSeconds' in data)
    checks.append(type(data['waitSeconds']) == int)
    checks.append('message' in data)

    # if any checks fail, return error to API Gateway to return to client
    if False in checks:
        response = {
            "statusCode": 400,
            "headers": {"Access-Control-Allow-Origin":"*"},
            "body": json.dumps( { "Status": "Success", "Reason": "Input failed validation" }, cls=DecimalEncoder )
        }
    # If none, start the state machine execution and inform client of 2XX success :)
    else: 
        sm.start_execution( stateMachineArn=SM_ARN, input=json.dumps(data, cls=DecimalEncoder) )
        response = {
            "statusCode": 200,
            "headers": {"Access-Control-Allow-Origin":"*"},
            "body": json.dumps( {"Status": "Success"}, cls=DecimalEncoder )
        }
    return response

# This is a workaround for: http://bugs.python.org/issue16535
# Solution discussed on this thread https://stackoverflow.com/questions/11942364/typeerror-integer-is-not-json-serializable-when-serializing-json-in-python
# https://stackoverflow.com/questions/1960516/python-json-serialize-a-decimal-object
# Credit goes to the group :)
class DecimalEncoder(json.JSONEncoder):
    def default(self, obj):
        if isinstance(obj, decimal.Decimal):
            return int(obj)
        return super(DecimalEncoder, self).default(obj)

 **Functionality**:
  - This code defines a Lambda function named `lambda_handler`, which serves as a backend function for a serverless application.
  - It interacts with a Step Functions state machine and handles requests from API Gateway.

- **Data Handling**:
  - The function receives an event and context as input parameters, representing the event data and execution context, respectively.
  - It prints the event data to the logs for debugging purposes.
  - It extracts data from the event body received from API Gateway and converts specific fields to the appropriate data types.

- **Input Validation**:
  - The code performs sanity checks on the received data to ensure that all necessary parameters are present and have the correct data types.
  - Checks include verifying the presence and type of 'waitSeconds' and 'message' parameters.

- **Response Handling**:
  - If the input fails validation, the function constructs an error response with a status code of 400 and a message indicating the reason for validation failure.
  - If the input passes validation, the function starts the execution of the specified Step Functions state machine and constructs a success response with a status code of 200.

- **Decimal Object Workaround**:
  - The code includes a workaround for an issue related to JSON serialization of Decimal objects in Python.
  - This workaround ensures proper serialization of Decimal objects by converting them to integers before JSON serialization.

- **Credit and References**:
  - Credit is given to a group for discussing and providing solutions related to the Decimal object serialization issue.
  - Links to relevant discussions and resources on Stack Overflow are provided for reference.

## `serverless.js`

```javascript

var API_ENDPOINT = 'https://nkfi4o1jse.execute-api.us-east-1.amazonaws.com/prod/petcuddleotron';

var errorDiv = document.getElementById('error-message')
var successDiv = document.getElementById('success-message')
var resultsDiv = document.getElementById('results-message')

function waitSecondsValue() { return document.getElementById('waitSeconds').value }
function messageValue() { return document.getElementById('message').value }
function emailValue() { return document.getElementById('email').value }

function clearNotifications() {
    errorDiv.textContent = '';
    resultsDiv.textContent = '';
    successDiv.textContent = '';
}

document.getElementById('emailButton').addEventListener('click', function(e) { sendData(e, 'email'); });

function sendData (e, pref) {
    e.preventDefault()
    clearNotifications()
    fetch(API_ENDPOINT, {
        headers:{
            "Content-type": "application/json"
        },
        method: 'POST',
        body: JSON.stringify({
            waitSeconds: waitSecondsValue(),
            message: messageValue(),
            email: emailValue()
        }),
        mode: 'cors'
    })
    .then((resp) => resp.json())
    .then(function(data) {
        console.log(data)
        successDiv.textContent = 'Submitted. But check the result below!';
        resultsDiv.textContent = JSON.stringify(data);
    })
    .catch(function(err) {
        errorDiv.textContent = 'Oops! Error Error:\n' + err.toString();
        console.log(err)
    });
};


**Functionality**:
  - This JavaScript code handles user interaction on a web page and sends data to an AWS API Gateway endpoint.

- **Endpoint Configuration**:
  - It defines the endpoint URL for the API Gateway as `API_ENDPOINT`.

- **DOM Elements**:
  - It identifies HTML elements for displaying error messages (`errorDiv`), success messages (`successDiv`), and results (`resultsDiv`).

- **Input Retrieval**:
  - Functions like `waitSecondsValue()`, `messageValue()`, and `emailValue()` retrieve values entered by the user from corresponding input fields on the webpage.

- **Notification Clearing**:
  - The `clearNotifications()` function clears any existing error, success, or result messages from the webpage.

- **Event Listener**:
  - It adds an event listener to a button with the ID 'emailButton' to trigger sending data when clicked.

- **Data Sending**:
  - When the 'emailButton' is clicked, it prevents the default form submission action, clears any existing notifications, and sends a POST request to the API endpoint.
  - It sends JSON-formatted data including 'waitSeconds', 'message', and 'email' values retrieved from input fields.

- **Response Handling**:
  - It processes the response from the API endpoint:
    - If successful, it displays a success message and the received data.
    - If an error occurs, it displays an error message.

- **Error Handling**:
  - It catches and logs any errors that occur during the data sending process.

