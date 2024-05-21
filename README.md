# Use AWS API Gateway to avoid Stacking CORS Requests

Cross-Origin Resource Sharing (CORS) is a mechanism that allows many resources (e.g., fonts, JavaScript, etc.) on a web 
page to be requested from another domain outside the domain from which the resource originated. If you're developing a 
web application with the API hosted on a separate domain, understanding CORS is crucial to ensure that your application 
functions correctly and securely. This article will delve into the intricacies of CORS and how requests stack up when 
using a separate domain for your site's API.

### Understanding CORS

CORS is a standard implemented by web browsers that allows servers to specify which origins are permitted to access 
their resources. This mechanism is essential for preventing unauthorized access to resources, thereby enhancing the 
security of web applications.

Before CORS, web browsers implemented the same-origin policy (SOP), which restricted web pages from making requests to 
a different domain. With the advent of APIs and the need for interoperability between web services, CORS was introduced 
to relax the SOP, allowing secure cross-origin data transfers.

### How CORS Works

When a web page makes a request to a different domain, the browser first sends a preflight request using the OPTIONS 
HTTP method. This request contains the origin of the initial request and the HTTP method and headers that will be used.

The server can then decide whether to allow or deny the request by examining the preflight request. If the server 
approves the request, it sends an HTTP response with the Access-Control-Allow-Origin header set to the origin of the 
request or a wildcard (*) to allow all origins. The server can also specify other headers that it allows in the response, 
such as Access-Control-Allow-Methods and Access-Control-Allow-Headers.

If the server denies the request, it sends an HTTP response with an appropriate status code, and the browser blocks the 
actual request from being sent.

### Requests Stack Up with Separate Domain for API

When a web application makes requests to an API that is hosted on a separate domain, the browser's same-origin policy 
enforces a security protocol known as Cross-Origin Resource Sharing (CORS). This protocol involves additional HTTP requests, which can cause the requests to "stack up" and potentially impact performance.

1. The web application sends a request to the API's endpoint on a different domain.
2. As the request targets a different domain, the browser first sends a preflight request using the OPTIONS HTTP method, as mentioned earlier.
3. The server, hosting the API, receives the preflight request and examines its headers to determine if the request should be allowed or not.
4. If the server approves the request, it sends an HTTP response containing appropriate Access-Control headers, such as Access-Control-Allow-Origin, Access-Control-Allow-Methods, and Access-Control-Allow-Headers.
5. The browser receives the server's response to the preflight request and, upon confirming that the actual request is allowed, sends the initial request to the API's endpoint.
6. The server processes the actual request and sends a response back to the web application.
7. The web application receives the server's response and processes the data accordingly.

This process involves multiple round trips between the client and the server, which can cause requests to stack up and potentially lead to performance issues, especially when dealing with multiple API requests in a short period. To mitigate this issue, developers can consider implementing techniques such as caching, reducing the number of API requests, or using a proxy server to bring the API under the same domain as the web application.

## Solution

We can use Amazon Web Services (AWS) to host a web application and its corresponding API under the same domain. By doing so, we 
can improve performance and avoid Cross-Origin Resource Sharing (CORS), which can lead to stacked requests and 
potential performance issues. In this example, we will use AWS Lambda, Amazon API Gateway, and Amazon S3 to host a simple
web application and its API under the same domain. It uses cloud formation to deploy the resources and the AWS SAM CLI to
deploy the application.

# Deploy using the SAM CLI
## Clone/fork the repository
Clone or fork this repository and push it to your own GitHub account.

The AWS SAM CLI is an extension of the AWS CLI that adds functionality for building and testing Lambda applications. It uses Docker to run your functions in an Amazon Linux environment that matches Lambda. It can also emulate your application's build environment and API.

To use the AWS SAM CLI, you need the following tools:

* AWS SAM CLI - [Install the AWS SAM CLI](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-install.html).
* Node.js - [Install Node.js 18](https://nodejs.org/en/), including the npm package management tool.
* Docker - [Install Docker community edition](https://hub.docker.com/search/?type=edition&offering=community).

To build and deploy your application for the first time, run the following in your shell:

```bash
sam build && sam deploy --guided
```

The first command will build the source of your application. The second command will package and deploy your application to AWS, with a series of prompts:

* **Stack Name**: The name of the stack to deploy to CloudFormation. This should be unique to your account and region, and a good starting point would be something matching your project name.
* **BucketName**: The name of the bucket to upload the hosted ui to. This should be unique to your account and region, and a good starting point would be something matching your project name.
* **DomainName**: The domain name you want to use for the API Gateway endpoint. This should be a domain you own and have configured in Route 53.
* **HostedZoneId**: The Route 53 Hosted Zone ID for the domain you want to use. This is the ID of the hosted zone in Route 53 that you want to use for the domain name you provided.
* **Confirm changes before deploy**: If set to yes, any change sets will be shown to you before execution for manual review. If set to no, the AWS SAM CLI will automatically deploy application changes.
* **Allow SAM CLI IAM role creation**: Many AWS SAM templates, including this example, create AWS IAM roles required for the AWS Lambda function(s) included to access AWS services. By default, these are scoped down to minimum required permissions. To deploy an AWS CloudFormation stack which creates or modifies IAM roles, the `CAPABILITY_IAM` value for `capabilities` must be provided. If permission isn't provided through this prompt, to deploy this example you must explicitly pass `--capabilities CAPABILITY_IAM` to the `sam deploy` command.
* **Save arguments to samconfig.toml**: If set to yes, your choices will be saved to a configuration file inside the project, so that in the future you can just re-run `sam deploy` without parameters to deploy changes to your application.

The API Gateway endpoints API will be displayed in the outputs when the deployment is complete.

Final step is to upload the index.html file to the S3 bucket created in the deployment. You can do this with the following command:
```bash
aws s3 cp ./index.html s3://<BucketName>/index.html
```

### Testing the application

You can test the application by opening your browser and navigating to the domain name you provided in the deployment. 
You should see a simple web page with a button that makes a request to the API Gateway endpoint. 
If everything is set up correctly, you should see a response from the API displayed on the web page.

### Cleaning up
To remove all resources created by the AWS SAM CLI, you can run the following command:

```bash
sam delete
```

# Deploy in your account using the included GitHub workflows
## Clone/fork the repository
Clone or fork this repository and push it to your own GitHub account.

## Setup GitHub environment
1. Create an GitHub environment named *sandbox*
1. Add your Pipeline Execution Role (PIPELINE_EXECUTION_ROLE), CloudFormation Execution Role (CLOUDFORMATION_EXECUTION_ROLE) and a target S3 bucket name for the artifacts (ARTIFACTS_BUCKET_NAME) as secrets. Here is an explanation by [Chris Ebert](https://twitter.com/realchrisebert) on how to set this up.

## Run Deployment
In the Actions in GitHub you can select the *Deploy to Sandbox* Workflow. There will be a button to *Run workflow* where you can now select the branch you wish to deploy.