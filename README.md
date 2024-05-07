# Understanding CORS and How Requests Stack Up When Using a Separate Domain for Your Site's API

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

aws s3 cp ./ui/index.html s3://my-bucket/index.html