# Serverless Notifier Workflow

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

## Python Lambda Function (`api_lambda.py`)

### Functionality:

- Defines a Lambda function named `lambda_handler`, serving as the backend function for the serverless application.
- Interacts with a Step Functions state machine and handles requests from API Gateway.

### Data Handling:

- Receives an event and context as input parameters, representing the event data and execution context, respectively.
- Prints the event data to the logs for debugging purposes.
- Extracts data from the event body received from API Gateway and converts specific fields to the appropriate data types.

### Input Validation:

- Performs sanity checks on the received data to ensure that all necessary parameters are present and have the correct data types.
- Checks include verifying the presence and type of 'waitSeconds' and 'message' parameters.

### Response Handling:

- If the input fails validation, constructs an error response with a status code of 400 and a message indicating the reason for validation failure.
- If the input passes validation, starts the execution of the specified Step Functions state machine and constructs a success response with a status code of 200.

### Decimal Object Workaround:

- Includes a workaround for an issue related to JSON serialization of Decimal objects in Python.
- Ensures proper serialization of Decimal objects by converting them to integers before JSON serialization.

### Credit and References:

- Credits a group for discussing and providing solutions related to the Decimal object serialization issue.
- Provides links to relevant discussions and resources on Stack Overflow for reference.

## JavaScript Handler (`serverless.js`)

### Functionality:

- Handles user interaction on a web page and sends data to an AWS API Gateway endpoint.

### Endpoint Configuration:

- Defines the endpoint URL for the API Gateway as `API_ENDPOINT`.

### DOM Elements:

- Identifies HTML elements for displaying error messages (`errorDiv`), success messages (`successDiv`), and results (`resultsDiv`).

### Input Retrieval:

- Functions like `waitSecondsValue()`, `messageValue()`, and `emailValue()` retrieve values entered by the user from corresponding input fields on the webpage.

### Notification Clearing:

- The `clearNotifications()` function clears any existing error, success, or result messages from the webpage.

### Event Listener:

- Adds an event listener to a button with the ID 'emailButton' to trigger sending data when clicked.

### Data Sending:

- When the 'emailButton' is clicked, it prevents the default form submission action, clears any existing notifications, and sends a POST request to the API endpoint.
- Sends JSON-formatted data including 'waitSeconds', 'message', and 'email' values retrieved from input fields.

### Response Handling:

- Processes the response from the API endpoint:
  - If successful, displays a success message and the received data.
  - If an error occurs, displays an error message.

### Error Handling:

- Catches and logs any errors that occur during the data sending process.
  
![Screenshot from 2024-05-03 02-29-13](https://github.com/TarangJ/Serverless-Notifier-Workflow/assets/65700353/811daf11-010a-40be-97b7-ff1d28abbe7c)

![Screenshot from 2024-05-03 02-31-24](https://github.com/TarangJ/Serverless-Notifier-Workflow/assets/65700353/adeb7937-c177-4ec1-b6e9-ea33daf44fb2)
