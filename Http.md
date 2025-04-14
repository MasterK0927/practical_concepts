# INTRODUCTION

- HTTP is the foundation of Data communication on the web. It's an application layer protocol built on top of TCP/IP.
- Key features of HTTP are:
    - Text-based (HTTP/1.1): HTTP messages are human-readable, with HTTP/2 and HTTP/3 it became binary, allowing for more efficient data transmission.
    - Stateless: Each request is independent, and the server does not retain any information about previous requests.
    - Request-Response Based: The client sends a request, and the server responds with the requested resource or an error message.
    - Works over TCP: HTTP typically uses TCP port 80 for communication, while HTTPS (HTTP Secure) uses port 443.
- So the two key features of HTTP are its statelessness and its request-response model.
    
## **Stateless:**
- No memory of past interaction
- Every request is treated as a new unrelated request
- Each request contains all the information needed to understand and process it
### *Advantages:*
- Server does not need to store session information
- Stateless Servers can be scaled easily or swapped w/o worrying about session data
- If a server crashes, it does not affect the client
- No need to manage the complex state on the server
### *Disadvantages:*
- Cannot maintain user sessions or track user state
- Requires additional mechanisms (like cookies, sessions, or tokens) to maintain state across requests
- Increased data transfer: Each request must contain all the information needed to process it, which can lead to larger request sizes
### *Working:*
- Client sends all the information to the server in every request
- Server processes the request based on what was sent
- After sending the request server forgets everything about the request
### *Standards:*
- REST APIs are designed to be stateless
- JWT tokens are used for stateless authentication
- Caching is used for boosting the performance

``` Rust
use actix_web::{web, App, HttpResponse, HttpServer};

async fn hello() -> HttpResponse {
    HttpResponse::Ok().body("Hello")
}

#[actix_web::main]
async fn main() -> std::io::Result<()> {
    HttpServer::new(|| {
        App::new().route("/", web::get().to("Hello"))
    })
    .bind("127.0.0.1:8000")?
    .run()
    .await
}
```

## **Client-Server**
- Way of designing systems, where two entities work together to achieve a common goal
- Client: The entity that requests a service or resource
- Server: The entity that provides the service or resource
### *Working:*
- Client sends a request to the server
- Server processes the request
- Server sends a response back to the client
- Client processes the response
### *Good Practices:*
- Keep client's job separate from server's job
- Design the server to handle more traffic by adding more machines
- Handle sensitivte data on the server side
- Use clear, consistent APIs, so clients can easily understand how to interact with the server
- Use caching to improve performance
- Use load balancing to distribute traffic across multiple servers
- Use versioning to ensure backward compatibility
- Use HTTPS to secure communication between client and server
- Use authentication and authorization to protect sensitive data
- Use logging and monitoring to track performance and errors
### *Bad Practices:*
- Tight Coupling: Don't make Client and server are too dependent on each other, changes should be easy to make
- Overloading the client: Don't make the client do too much work, keep it simple. Let the server do the heavy lifting
- Ignoring security: Don't ignore security, always use HTTPS and secure your APIs. Always validate data from the client don't trust it blindly
- Not handling errors: Don't ignore errors, always handle them gracefully and provide meaningful error messages
- Not considering performance: Don't ignore performance, always optimize your APIs for speed and efficiency
- Not documenting your APIs: Don't ignore documentation, always document your APIs so clients can easily understand how to use them
- Not testing your APIs: Don't ignore testing, always test your APIs to ensure they work as expected
- Not considering scalability: Don't ignore scalability, always design your APIs to handle more traffic by adding more machines
- Not considering versioning: Don't ignore versioning, always version your APIs to ensure backward compatibility
### *Standards:*
- Big servers often split into small, independent services called microservices
- Microservices communicate with each other using APIs
- Microservices are designed to be stateless, so they can be easily scaled and swapped
- Microservices are designed to be loosely coupled, so they can be easily changed without affecting other services
- Microservices are designed to be independently deployable, so they can be easily deployed and updated without affecting other services
- They communicate with each other over the protocols like TCP/IP, HTTP, and WebSockets
- Sockets are also based on the client-server model, where one socket acts as a client and the other as a server
- Web uses HTTP to handle requests and responses.

```Rust
use std::net::{TcpListener, TcpStream};
use std::io::{Read, Write};

fn handle_client(mut stream: TcpStream) {
    let mut buffer = [0; 1024];
    stream.read(&mut buffer).unwrap();
    println!("Request: {}", String::from_utf8_lossy(&buffer[..]));
    let response = "HTTP/1.1 200 OK\r\n\r\nHello, World!";
    stream.write(response.as_bytes()).unwrap();
    stream.flush().unwrap();
}

fn main() {
    let listener = TcpListener::bind("127.0.0.1:8000").unwrap();
    // Accept incoming connections
    for stream in listener.incoming() {
        match stream {
            Ok(stream) => {
                handle_client(stream);
            }
            Err(e) => {
                eprintln!("Error: {}", e);
            }
        }
    }
}
```

## HTTP Evolution

### *HTTP/0.9:*
- The first version of HTTP, introduced in 1991
- Designed for simplicity and speed
- Supported only GET requests, to retrieve HTML documents
- No headers, no status codes, no methods
- Only supported plain text responses

### *HTTP/1.0:*
- Introduced in 1996
- Added support for headers, status codes, and methods (GET, POST, HEAD)
- Supported multiple content types (HTML, images, etc.)
- Limitation: Each request required a new TCP connection, leading to increased latency and resource usage

### *HTTP/1.1:*
- Introduced in 1999
- Added support for additional methods (PUT, DELETE, OPTIONS, TRACE, CONNECT)
- Introduced the concept of chunked transfer encoding, allowing large responses to be sent in smaller chunks
- Support for virtual hosting, allowing multiple domains to be served from a single IP address
- Persistent connections: Multiple requests can be sent over a single TCP connection, reducing latency and resource usage
- Added support for caching, allowing clients to cache responses and reduce server load
- Added support for content negotiation, allowing clients to request different content types based on their capabilities
- Added support for compression, allowing responses to be compressed to reduce size
- Limitation: As websites became more complex, HTTP/1.1 struggled to keep up with the increasing demand for speed and efficiency. Several Performance issues were identified, such as head-of-line blocking, where a single slow request could block all subsequent requests on the same connection.

### *HTTP/2:*
- Introduced in 2015
- A major revision of the HTTP protocol, designed to address the limitations of HTTP/1.1
- Uses a binary format instead of text, allowing for more efficient parsing and processing
- Introduced multiplexing, allowing multiple requests and responses to be sent over a single TCP connection simultaneously, eliminating head-of-line blocking
- Introduced header compression, reducing the size of headers and improving performance
- Introduced server push, allowing servers to send resources to clients before they are requested
- Introduced prioritization, allowing clients to specify the priority of requests, enabling more efficient resource allocation
- Limitation: Still relies on TCP, which can introduce latency and performance issues in certain network conditions. Also, not all browsers and servers support HTTP/2, leading to compatibility issues.

### *HTTP/3:*
- Introduced in 2020
- A major revision of the HTTP protocol, designed to address the limitations of HTTP/2
- Uses QUIC (Quick UDP Internet Connections) as the transport layer, instead of TCP
- QUIC is a UDP-based protocol that provides low-latency, secure connections
- QUIC eliminates head-of-line blocking by allowing multiple streams of data to be sent simultaneously over a single connection
- QUIC provides built-in encryption, improving security and performance
- QUIC provides better support for mobile devices and unreliable networks, improving performance in challenging conditions
- Limitation: Still in the early stages of adoption, and not all browsers and servers support HTTP/3. Some network devices may not support QUIC, leading to compatibility issues.

## HTTP Messages
- There are two types of HTTP messages: requests and responses
- Each message consists of a start line, headers, and an optional body
### *Request Message:*
- Sent by the client to the server
- Consists of a request line, headers, and an optional body
- *Request Line:*
    - Contains the HTTP method (GET, POST, PUT, DELETE, etc.), the requested resource (URL), and the HTTP version
    - Example: `GET /index.html HTTP/1.1`
- *Headers:*
    - Provide additional information about the request
    - Example: `Host: www.example.com`
    - Example: `User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3`
    - Example: `Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8`
    - Example: `Accept-Language: en-US,en;q=0.5`
    - Example: `Accept-Encoding: gzip, deflate, br`
- *Body:*
    - Optional, used for sending data to the server (e.g., form data)
    - Example: `name=John&age=30`
### *Response Message:*
- Sent by the server to the client
- Consists of a status line, headers, and an optional body
- *Status Line:*
    - Contains the HTTP version, status code, and reason phrase
    - Example: `HTTP/1.1 200 OK`
- *Headers:*
    - Provide additional information about the response
    - Example: `Content-Type: text/html`
    - Example: `Content-Length: 1234`
    - Example: `Server: Apache/2.4.41 (Ubuntu)`
    - Example: `Date: Wed, 21 Oct 2015 07:28:00 GMT`
    - Example: `Cache-Control: no-cache`
    - Example: `Set-Cookie: sessionId=abc123; HttpOnly`
- *Body:*
    - Optional, contains the requested resource (e.g., HTML document, image, etc.)
    - Example: `<html><body>Hello, World!</body></html>`
    - Example: `{"name": "John", "age": 30}`
    - Example: `{"error": "Not Found"}`

## HTTP Headers
- They are key-value pairs sent in HTTP messages
- They provide additional information about the request or response
- We can't send them directly in the url, and explicitly need a header field, because we need a rough estimate about the connection, server before sending the request/response
- HTTP headers can include information such as content type, content length, and authentication tokens.
- They can also be used to control caching, compression, and other aspects of the request/response process.
### *Types of HTTP Headers:*
- *Request Headers:*
    - Sent by the client to the server
    - Provide additional information about the request
    - `User-Agent`: Identifies the client software making the request
    - `Accept`: Specifies the media types that the client is willing to accept
    - `Authorization`: Contains credentials for authenticating the client with the server
    - `Cookie`: Contains stored cookies sent by the client to the server
- *General Headers:*
    - Provide information about the message itself
    - `Date`: The date and time the message was sent
    - `Connection`: Controls whether the connection should be kept alive or closed after the message is sent
- *Representation Headers:*
    - Provide information about the representation of the resource
    - `Content-Type`: The media type of the resource (e.g., text/html, application/json)
    - `Content-Length`: The size of the message body in bytes
    - `Content-Encoding`: The encoding used to compress the message body (e.g., gzip, deflate)
    - `Content-Language`: The language of the resource (e.g., en-US, fr-FR)
    - `Content-Disposition`: Indicates if the content should be displayed inline or as an attachment
    - `ETag`: A unique identifier for a specific version of a resource, used for caching and conditional requests
- *Response Headers:*
    - Sent by the server to the client
    - Provide additional information about the response
    - `Location`: Used in redirection, specifies the URL to redirect to
    - `Set-Cookie`: Used to set cookies on the client
    - `Server`: Identifies the server software handling the request
    - `WWW-Authenticate`: Used in authentication challenges, specifies the authentication method required
- *Entity Headers:*
    - Provide information about the resource itself
    - `Last-Modified`: The date and time the resource was last modified
    - `Expires`: The date and time the resource is considered stale
    - `Cache-Control`: Directives for caching mechanisms in both requests and responses
    - `Pragma`: Used for backward compatibility with HTTP/1.0 caches
- *Custom Headers:*
    - User-defined headers that can be used to send additional information
    - Example: `X-Requested-With: XMLHttpRequest`
    - Example: `X-Frame-Options: DENY`
    - Example: `X-Content-Type-Options: nosniff`
    - Example: `X-XSS-Protection: 1; mode=block`
    - Example: `X-Powered-By: Express`
- *Security Headers:*
    - Used to enhance security and protect against common vulnerabilities
    - `Strict-Transport-Security`: Enforces secure (HTTPS) connections to the server
    - `Content-Security-Policy`: Prevents cross-site scripting (XSS) attacks by controlling which resources can be loaded
    - `X-Content-Type-Options`: Prevents MIME type sniffing by instructing browsers to strictly follow the declared content type
    - `X-Frame-Options`: Protects against clickjacking attacks by controlling whether the page can be displayed in a frame or iframe
    - `X-XSS-Protection`: Enables cross-site scripting (XSS) filtering in browsers
    - `Referrer-Policy`: Controls the amount of referrer information sent with requests
    - `Feature-Policy`: Controls which features and APIs can be used in the browser
- *CORS Headers:*
    - Used to control cross-origin resource sharing
    - `Access-Control-Allow-Origin`: Specifies which origins are allowed to access the resource
    - `Access-Control-Allow-Methods`: Specifies which HTTP methods are allowed for cross-origin requests
    - `Access-Control-Allow-Headers`: Specifies which headers are allowed in cross-origin requests
    - `Access-Control-Allow-Credentials`: Indicates whether credentials (cookies, authorization headers) are allowed in cross-origin requests
    - `Access-Control-Max-Age`: Specifies how long the results of a preflight request can be cached
    - `Access-Control-Expose-Headers`: Specifies which headers are safe to expose to the client
- *Caching Headers:*
    - Used to control caching behavior
    - `Cache-Control`: Directives for caching mechanisms in both requests and responses
    - `Expires`: The date and time the resource is considered stale
    - `ETag`: A unique identifier for a specific version of a resource, used for caching and conditional requests
    - `If-None-Match`: Used in conditional requests to check if the resource has changed since the last request
    - `If-Modified-Since`: Used in conditional requests to check if the resource has been modified since the specified date
    - `Last-Modified`: The date and time the resource was last modified
    - `Vary`: Indicates which request headers should be considered when caching the response
- *Debugging Headers:*
    - Used for debugging and troubleshooting
    - `X-Debug-Info`: Custom header for debugging information
    - `X-Request-ID`: Unique identifier for tracking requests
    - `X-Response-Time`: Time taken to process the request
    - `X-Forwarded-For`: Original client IP address when behind a proxy or load balancer
    - `X-Forwarded-Proto`: Original protocol (HTTP or HTTPS) used by the client
    - `X-Forwarded-Host`: Original host requested by the client
    - `X-Forwarded-Port`: Original port used by the client
- *Performance Headers:*
    - Used to improve performance and reduce latency
    - `Accept-Encoding`: Specifies the content encodings that the client can accept (e.g., gzip, deflate)
    - `Connection`: Controls whether the connection should be kept alive or closed after the message is sent
    - `Keep-Alive`: Specifies parameters for persistent connections
    - `TE`: Indicates the transfer encodings the client is willing to accept
    - `Upgrade`: Indicates that the client wants to upgrade to a different protocol (e.g., WebSocket)
    - `Via`: Indicates intermediate protocols and recipients between the user agent and the server
    - `Warning`: Provides additional information about the message, such as potential issues or warnings
- *Example:*
```HTTP
GET /index.html HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
```
```HTTP
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 1234
Server: Apache/2.4.41 (Ubuntu)
Date: Wed, 21 Oct 2015 07:28:00 GMT
Cache-Control: no-cache
Set-Cookie: sessionId=abc123; HttpOnly
```
```HTTP
HTTP/1.1 404 Not Found
Content-Type: application/json
Content-Length: 45
{"error": "Resource not found"}
```
```HTTP
HTTP/1.1 500 Internal Server Error
Content-Type: application/json
Content-Length: 45
{"error": "Internal server error"}
```
```HTTP
HTTP/1.1 302 Found
Location: https://www.example.com/new-page
```
```HTTP
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Basic realm="Access to the staging site"
```

There are two main ideas, when we talk about HTTP headers:
- *Extensibility*: HTTP headers are extensible, meaning that new headers can be added to the protocol without breaking existing functionality. This allows for the introduction of new features and capabilities without requiring a complete overhaul of the protocol.
- *Remote control*: HTTP headers can be used to control various aspects of the request and response process, such as caching, authentication, and content negotiation. This allows for greater flexibility and control over how resources are accessed and served.
### *Good Practices:*
- Use semantic headers like `Content-Type`, `Content-Length`, and `Cache-Control` to provide clear information about the resource
- Security first approach should be there, which means always use HTTPS and secure your APIs, set `Secure` and `HttpOnly` flags for cookies, and use security headers like `Content-Security-Policy`, `X-Content-Type-Options`, and `X-XSS-Protection`
- Use `Vary` header to indicate which request headers should be considered when caching the response
- Use `ETag` and `Last-Modified` headers for caching and conditional requests
- Use `Accept` and `Content-Type` headers for content negotiation
- Use `Cache-Control` and `Expires` headers to control caching behavior
- Use `X-Forwarded-*` headers to pass information about the original client when behind a proxy or load balancer
- Use `X-Request-ID` header to track requests and responses for debugging and troubleshooting
- Use `X-Frame-Options` header to protect against clickjacking attacks
- Use `X-XSS-Protection` header to enable cross-site scripting (XSS) filtering in browsers
- Use `X-Content-Type-Options` header to prevent MIME type sniffing
- Use `X-Powered-By` header to indicate the server technology used (optional, can be omitted for security reasons)
- Use `X-Content-Security-Policy` header to prevent cross-site scripting (XSS) attacks by controlling which resources can be loaded
- Use `X-Download-Options` header to prevent Internet Explorer from executing downloads in the site's context
- Use `X-Permitted-Cross-Domain-Policies` header to control cross-domain policies for Adobe Flash and Adobe Acrobat
- Use `X-DNS-Prefetch-Control` header to control DNS prefetching in browsers
### *Bad Practices:*
- Not setting the `Content-Type` header, which can lead to incorrect interpretation of the response
- Overloading Headers with too much information, making them difficult to read and understand
- Insecure Cookies: Not setting the `Secure` and `HttpOnly` flags for cookies, which can lead to security vulnerabilities
- Ignoring Security Headers: Not using security headers like `Content-Security-Policy`, `X-Content-Type-Options`, and `X-XSS-Protection`
- Using Deprecated Headers: Using deprecated headers like `X-Powered-By` and `X-Frame-Options`, which can lead to security vulnerabilities
- Not Using Caching Headers: Not using caching headers like `Cache-Control` and `Expires`, which can lead to performance issues
- Not Using Compression: Not using compression headers like `Accept-Encoding`, which can lead to larger response sizes and slower performance
- Not Using Conditional Requests: Not using conditional request headers like `If-None-Match` and `If-Modified-Since`, which can lead to unnecessary data transfer
- Allowing * for `Access-Control-Allow-Origin`, which can lead to security vulnerabilities
- Not Using CORS Headers: Not using CORS headers like `Access-Control-Allow-Origin`, `Access-Control-Allow-Methods`, and `Access-Control-Allow-Headers`, which can lead to security vulnerabilities
- Exposing server information in headers like `Server`, which can lead to security vulnerabilities
- Overusing cookies without proper security flags, which can lead to vulnerabilities

## HTTP Methods
- HTTP defines several methods (also known as verbs) that indicate the desired action to be performed on a resource
- HTTP methods are:
    - GET: Retrieve a resource
    - POST: Submit data to be processed
    - PUT: Update a resource
    - DELETE: Remove a resource
    - PATCH: Partially update a resource
    - HEAD: Retrieve only the headers of a resource
    - OPTIONS: Retrieve the supported HTTP methods for a resource
    - TRACE: Echo the received request for diagnostic purposes
    - CONNECT: Establish a tunnel to the server
    - COPY: Create a copy of a resource
    - LINK: Create a link between two resources
    - UNLINK: Remove a link between two resources
    - PURGE: Remove a cached resource
    - LOCK: Lock a resource for exclusive access
    - UNLOCK: Unlock a resource
    - PROPFIND: Retrieve properties of a resource
    - PROPPATCH: Update properties of a resource
    - MKCOL: Create a new collection (directory)
    - MOVE: Move a resource to a new location
    - REPORT: Retrieve a report on a resource
    - VERSION-CONTROL: Create a new version of a resource
    - CHECKOUT: Retrieve a specific version of a resource
    - CHECKIN: Check in a new version of a resource
    - LABEL: Assign a label to a version of a resource
    - MERGE: Merge changes from one resource to another
    - ACL: Set access control lists for a resource
    - SEARCH: Search for resources based on specific criteria
    - MKACTIVITY: Create a new activity for a resource
    - BASELINE-CONTROL: Manage baselines for a resource
    - MKWORKSPACE: Create a new workspace for a resource
    - REPORT: Generate a report on a resource
    - ACL: Set access control lists for a resource
    - BIND: Bind a resource to a new URI
    - UNBIND: Unbind a resource from a URI
    - REBIND: Rebind a resource to a new URI
    - REFRESH: Refresh a resource
    - NOTIFY: Notify a resource of changes
    - SUBSCRIBE: Subscribe to changes in a resource
    - UNSUBSCRIBE: Unsubscribe from changes in a resources
- Most commonly used methods are GET, POST, PUT, DELETE, and PATCH
- Each method has its own semantics and is used for different purposes
### *GET:*
- Used to retrieve a resource from the server
- Safe and idempotent: Does not modify the resource and can be repeated without side effects
- Can include query parameters in the URL to filter or modify the request
- Example: `GET /api/users?age=30&gender=male`
- Example: `GET /api/users/123`
- Example: `GET /api/products?category=electronics`
- Example: `GET /api/search?q=rust`
- Example: `GET /api/posts?author=john`
- Example: `GET /api/comments?postId=456`
### *POST:*
- Used to submit data to the server for processing
- Not safe or idempotent: Can modify the resource and may have side effects
- Typically used to create a new resource or submit a form
- Has a request body containing the data to be submitted
- Example: `POST /api/users`
- Example: `POST /api/products`
- Example: `POST /api/login`
- Example: `POST /api/register`
### *PUT:*
- Used to update an existing resource on the server
- Not safe but idempotent: Can modify the resource but can be repeated without side effects
- Typically used to replace the entire resource with the new data
- Has a request body containing the updated data
- Example: `PUT /api/users/123`
- Example: `PUT /api/products/456`
- Example: `PUT /api/posts/789`
- Example: `PUT /api/comments/101112`
### *DELETE:*
- Used to remove a resource from the server
- Not safe or idempotent: Can modify the resource and may have side effects
- Typically used to delete a resource
- Example: `DELETE /api/users/123`
- Example: `DELETE /api/products/456`
- Example: `DELETE /api/posts/789`
- Example: `DELETE /api/comments/101112`
### *PATCH:*
- Used to partially update an existing resource on the server
- Not safe but idempotent: Can modify the resource but can be repeated without side effects
- Typically used to update specific fields of a resource
- Has a request body containing the updated data
- Example: `PATCH /api/users/123`
- Example: `PATCH /api/products/456`
- Example: `PATCH /api/posts/789`
- Example: `PATCH /api/comments/101112`
### *HEAD:*
- Used to retrieve only the headers of a resource
- Safe and idempotent: Does not modify the resource and can be repeated without side effects
- Typically used to check if a resource exists or to retrieve metadata
- Example: `HEAD /api/users/123`
- Example: `HEAD /api/products/456`
- Example: `HEAD /api/posts/789`
- Example: `HEAD /api/comments/101112`
### *OPTIONS:*
- Used to retrieve the supported HTTP methods for a resource
- Safe and idempotent: Does not modify the resource and can be repeated without side effects
- Typically used to check the capabilities of a resource or server
- Example: `OPTIONS /api/users`
- Example: `OPTIONS /api/products`
- Example: `OPTIONS /api/posts`
- Example: `OPTIONS /api/comments`

## Flow of CORS
- CORS (Cross-Origin Resource Sharing) is a security feature implemented by web browsers to prevent cross-origin requests from being made without the server's permission
- It allows servers to specify which origins are allowed to access their resources
- CORS is implemented using HTTP headers, which are sent in both the request and response messages
- CORS can be handled with 2 type of requests:
    - Simple Requests: Requests that meet certain criteria and do not require a preflight request
    - Preflight Requests: Requests that require a preflight request to check if the server allows the cross-origin request
### *Simple Requests:*
- Simple requests are requests that meet the following criteria:
    - The request method is one of the following: GET, POST, or HEAD
    - The request headers are limited to a specific set of headers (Accept, Accept-Language, Content-Language, Content-Type)
    - The Content-Type header can only be one of the following: application/x-www-form-urlencoded, multipart/form-data, or text/plain
- If a request meets these criteria, it is considered a simple request and does not require a preflight request
- Example of a simple request:
```HTTP
GET /api/users HTTP/1.1
Host: www.example.com
Origin: https://www.client.com
Accept: application/json
```
```HTTP
HTTP/1.1 200 OK
Access-Control-Allow-Origin: https://www.client.com
Content-Type: application/json
```
```HTTP
{"name": "John", "age": 30}
```
```HTTP
HTTP/1.1 200 OK
Access-Control-Allow-Origin: https://www.client.com
Content-Type: application/json
```
```HTTP
{"name": "John", "age": 30}
```
### *Preflight Requests:*
- Preflight requests are requests that do not meet the criteria for simple requests and require a preflight request to check if the server allows the cross-origin request
- A preflight request is an OPTIONS request sent to the server before the actual request
- The preflight request includes the following headers:
    - Origin: The origin of the request
    - Access-Control-Request-Method: The HTTP method of the actual request
    - Access-Control-Request-Headers: The headers of the actual request
- The server responds to the preflight request with the following headers:
    - Access-Control-Allow-Origin: The origin of the request
    - Access-Control-Allow-Methods: The HTTP methods allowed for the resource
    - Access-Control-Allow-Headers: The headers allowed for the resource
    - Access-Control-Max-Age: The maximum time the preflight response can be cached
- We generally use preflight requests for PUT, DELETE, and PATCH methods
- If request has content-type other than application/x-www-form-urlencoded, multipart/form-data, or text/plain, it will also trigger a preflight request
- So, it indicates that we generaly deal with preflighed requests, as most of the time we deal with JSON data
- Example of a preflight request:
```HTTP
OPTIONS /api/users HTTP/1.1
Host: www.example.com
Origin: https://www.client.com
Access-Control-Request-Method: POST
Access-Control-Request-Headers: Content-Type
```
```HTTP
HTTP/1.1 200 OK
Access-Control-Allow-Origin: https://www.client.com
Access-Control-Allow-Methods: GET, POST, PUT, DELETE
Access-Control-Allow-Headers: Content-Type
Access-Control-Max-Age: 3600
```
```HTTP
POST /api/users HTTP/1.1
Host: www.example.com
Origin: https://www.client.com
Content-Type: application/json
```
```HTTP
HTTP/1.1 200 OK
Access-Control-Allow-Origin: https://www.client.com
Content-Type: application/json
```
```HTTP
{"name": "John", "age": 30}
```
### *Good Practices:*
- Use CORS to control access to resources from different origins
- Use the `Access-Control-Allow-Origin` header to specify which origins are allowed to access the resource
- Use the `Access-Control-Allow-Methods` header to specify which HTTP methods are allowed for the resource
- Use the `Access-Control-Allow-Headers` header to specify which headers are allowed for the resource
- Use the `Access-Control-Max-Age` header to specify how long the preflight response can be cached
- Use the `Access-Control-Allow-Credentials` header to specify whether credentials (cookies, authorization headers) are allowed in cross-origin requests
- Use the `Access-Control-Expose-Headers` header to specify which headers are safe to expose to the client
- Use the `Access-Control-Allow-Private-Network` header to specify whether private network requests are allowed
### *Bad Practices:*
- Allowing all origins with `Access-Control-Allow-Origin: *`, which can lead to security vulnerabilities
- Not using CORS headers, which can lead to security vulnerabilities
- Not using preflight requests for non-simple requests, which can lead to security vulnerabilities
- Not validating the `Origin` header, which can lead to security vulnerabilities
- Not using HTTPS, which can lead to security vulnerabilities
- Not using secure cookies, which can lead to security 

## Response Status Codes
- HTTP status codes are three-digit numbers returned by the server in response to a client's request
- They indicate the outcome of the request and provide information about the status of the resource
- Status codes are divided into five categories:
    - 1xx: Informational
    - 2xx: Success
    - 3xx: Redirection
    - 4xx: Client Error
    - 5xx: Server Error
### *1xx: Informational:*
- Indicates that the request was received and is being processed
- These status codes are rarely used in practice
- Example: `100 Continue`: Indicates that the server has received the request headers and the client should proceed with sending the request body
- Example: `101 Switching Protocols`: Indicates that the server is switching to a different protocol as requested by the client
- Example: `102 Processing`: Indicates that the server is processing the request but has not yet completed it
- Example: `103 Early Hints`: Indicates that the server is sending preliminary headers before the final response
- Example: `199 Miscellaneous Persistent Warning`: Indicates that the server is sending a warning message
### *2xx: Success:*
- Indicates that the request was successful and the server has returned the requested resource
- **200(OK):** Indicates that the request has succeeded and the server has returned the requested data.
- **201(CREATED):** Indicates that the request has succeeded and a new resource has been created
- **204(NO CONTENT):** Indicates that the request has succeeded but there is no content to return
### *3xx: Redirection:*
- Indicates that the client must take additional action to complete the request
- **301(PERMANENT REDIRECT):** Indicates that the requested resource has been permanently moved to a new URL. For not breaking the flow of the application, we can use this status code
- **302(TEMPORARY REDIRECT):** Indicates that the requested resource resides temporarily under a different URI. The client should continue to use the original URI for future requests
- **304(NOT MODIFIED):** Indicates that the resource has not been modified since the last request, and the client can use the cached version.
### *4xx: Client Error:*
- Indicates that the client has made an error in the request
- **400(BAD REQUEST):** Indicates that the server cannot process the request due to a client error (e.g., malformed request syntax)
- **401(UNAUTHORIZED):** Indicates that the request requires authentication and the client has not provided valid credentials
- **403(FORBIDDEN):** Indicates that the server understands the request but refuses to authorize it
- **404(NOT FOUND):** Indicates that the requested resource could not be found on the server
- **405(METHOD NOT ALLOWED):** Indicates that the request method is not allowed for the requested resource
- **408(REQUEST TIMEOUT):** Indicates that the server timed out waiting for the request
- **409(CONFLICT):** Indicates that the request could not be completed due to a conflict with the current state of the resource
- **429(TOO MANY REQUESTS):** Indicates that the user has sent too many requests in a given amount of time
### *5xx: Server Error:*
- Indicates that the server has encountered an error while processing the request
- **500(INTERNAL SERVER ERROR):** Indicates that the server encountered an unexpected condition that prevented it from fulfilling the request
- **501(NOT IMPLEMENTED):** Indicates that the server does not support the functionality required to fulfill the request
- **502(BAD GATEWAY):** Indicates that the server received an invalid response from an upstream server while processing the request
- **503(SERVICE UNAVAILABLE):** Indicates that the server is currently unable to handle the request due to temporary overload or maintenance
- **504(GATEWAY TIMEOUT):** Indicates that the server did not receive a timely response from an upstream server while processing the request

## HTTP Caching
- HTTP caching is a mechanism that allows responses to be stored and reused to improve performance and reduce server load
- It reduces latency, saves bandwidth, reduces server's load, and improves user experience
- Caching can be done at various levels, including the client (browser), intermediary caches (proxies), and the server
- Caching can be controlled using HTTP headers, which specify how and when responses should be cached
- **Browser Caching:** This allows browsers to store responses to requests, reducing the need to fetch the same resource multiple times, thus improving load times and reducing server load. Example: Chrome, Firefox
- **Proxy Caching:** This allows intermediary caches (proxies) to store responses to requests, reducing the need to fetch the same resource multiple times from the server. Example: Varnish, Squid
- **Server Caching:** This allows servers to store responses to requests, reducing the need to generate the same response multiple times. Example: Redis, Memcached
- The server tells the client how long to cache the response, and the client can use the cached response until it expires
    1. **Cacheable Responses:** Responses that can be stored and reused by caches. They must include appropriate caching headers to indicate their cacheability. By default all GET and HEAD requests are cacheable, whereas POST, PUT, DELETE, PATCH requests are not cacheable.
    2. **Freshness:** How long a cached response is considered fresh and can be used without revalidation. Freshness is determined by the `max-age` directive in the `Cache-Control` header or the `Expires` header.
    3. **Validation:** When a cached response becomes stale, the client can revalidate it with the server using conditional requests. The server can respond with `304 Not Modified` if the cached response is still valid or return a new response if it has changed. Using `ETag` and `Last-Modified` headers, the client can check if the cached response is still valid.
    4. **Cache-Storage:** Browsers, proxies, or CDNs store cached responses in their cache storage. The cache storage can be managed using the `Cache-Control` and `Expires` headers.
### *Major Caching Headers:*
    1. **Cache-Control:** This header is used to specify caching directives for both requests and responses. It can include directives such as `max-age`, `no-cache`, `no-store`, `must-revalidate`, and `public/private`.
    2. **Expires:** This header specifies the date and time after which the response is considered stale. It is used for backward compatibility with HTTP/1.0 caches.
    3. **ETag:** This header is used to provide a unique identifier for a specific version of a resource. It can be used for conditional requests to check if the cached response is still valid.
    4. **Last-Modified:** This header indicates the date and time the resource was last modified. It can be used for conditional requests to check if the cached response is still valid.
    5. **Vary:** This header indicates which request headers should be considered when caching the response. It is used to prevent caching of different versions of the same resource based on different request headers.
    6. **If-none-match:** This header is used in conditional requests to check if the cached response is still valid based on the ETag value.
### Working of Caching:
- Client requests a resource from the server (GET /api/users)
- Server responds with the resource and includes caching headers (Cache-Control, Expires, ETag)
- Client stores the response in its cache
- If the cached response is still fresh, the client uses it without revalidating with the server
- If the cached response is stale, the client sends a conditional request to the server (GET /api/users with If-None-Match header)
- Client validates the cache using "If-None-Match" header with the ETag value
- Server responds with 304 Not Modified if the cached response is still valid
- If the cached response is no longer valid, the server responds with a new response and updates the cache
- Client updates its cache with the new response
### *Good Practices:*
- Use caching headers to control caching behavior
- Use `Cache-Control` header to specify caching directives
- Use `Expires` header to specify the expiration date and time of the response
- Use `ETag` and `Last-Modified` headers for caching and conditional requests
- Use `Vary` header to indicate which request headers should be considered when caching the response
- Use `Cache-Control: no-cache` for responses that should not be cached
- Use `Cache-Control: no-store` for responses that should not be stored in any cache
- Use `Cache-Control: must-revalidate` for responses that must be revalidated with the server before being used
- Use `Cache-Control: public` for responses that can be cached by any cache
- Use `Cache-Control: private` for responses that can only be cached by the client's browser
- Use `Cache-Control: max-age` to specify the maximum time the response can be cached
- Use `Cache-Control: s-maxage` to specify the maximum time the response can be cached by shared caches (e.g., CDNs)
- Use `Cache-Control: immutable` for responses that will not change over time
- Use `Cache-Control: stale-while-revalidate` to allow the client to use a stale response while revalidating it with the server
- Use `Cache-Control: stale-if-error` to allow the client to use a stale response if the server returns an error
- Use `Cache-Control: no-transform` to prevent intermediaries from modifying the response
### *Bad Practices:*
- Not using caching headers, which can lead to performance issues
- Using `Cache-Control: no-cache` for responses that should be cached
- Using `Cache-Control: no-store` for responses that should be cached
- Using `Cache-Control: must-revalidate` for responses that do not need to be revalidated
- Using `Cache-Control: public` for responses that should not be cached by shared caches
- Using `Cache-Control: private` for responses that should be cached by shared caches
- Using `Cache-Control: max-age` without specifying a value
- Using `Cache-Control: s-maxage` without specifying a value
- Over Caching the dynamic data
- Ignoring validations
- Not busting cache

## HTTP Server impl with caching

### **Rust HTTP server with Actix-Web**
- Actix-Web is a powerful, pragmatic, and extremely fast web framework for Rust
- It is built on top of the Actix actor framework and provides a simple and flexible API for building web applications
- Actix-Web supports asynchronous programming, making it suitable for high-performance applications

```rust
use actix_web::{web, App, HttpResponse, HttpServer, Responder};
use serde::{Serialize};
use chrono::{DateTime, Utc};
use sha2::{Digest, Sha256};

#[derive(Serialize)]
struct Resource {
    id: u32,
    content: String,
}

async fn get_resource() -> impl Responder {
    let resource = Resource {
        id: 1,
        content: String::from("Hello, World!"),
    };

    // generating ETag
    let mut hasher = Sha256::new();
    hasher.update(&resource.content);
    let etag = format!("\"{:x}\"", hasher.finalize());

    // last-Modified
    let last_modified: DateTime<Utc> = Utc::now();

    HttpResponse::Ok()
        .content_type("application/json")
        .header("Cache-Control", "public, max-age=3600")
        .header("ETag", etag.clone())
        .header("Last-Modified", last_modified.to_rfc2822())
        .if_true(true, |res| {
            // conditional request handling done here
            // check If-None-Match or If-Modified-Since
            res.status(304);
        })
        .json(resource)
}

#[actix_web::main]
async fn main() -> std::io::Result<()> {
    HttpServer::new(|| {
        App::new()
            .route("/api/resource", web::get().to(get_resource))
    })
    .bind("127.0.0.1:8080")?
    .run()
    .await
}
```

- GET `/api/resource` returns JSON with `Cache-Control`, `ETag`, and `Last-Modified`
- `ETag` is generated by hashing content.
- Simulates `304 Not Modified` response if the resource is not modified

### **Rust HTTP client with reqwest**
- Reqwest is a simple and powerful HTTP client for Rust
- It provides a convenient API for making HTTP requests and handling responses
- Reqwest supports asynchronous programming, making it suitable for high-performance applications

```rust
use reqwest::Error;
use serde::Deserialize;

#[derive(Deserialize, Debug)]
struct Resource {
    id: u32,
    content: String,
}

#[tokio::main]
async fn main() -> Result<(), Error> {
    let client = reqwest::Client::new();

    // First request
    let response = client
        .get("http://127.0.0.1:8080/api/resource")
        .header("Accept", "application/json")
        .send()
        .await?;
    let etag = response.headers().get("ETag").unwrap().to_str().unwrap();
    let last_modified = response.headers().get("Last-Modified").unwrap().to_str().unwrap();
    let resource: Resource = response.json().await?;
    println!("Resource: {:?}", resource);
    println!("ETag: {}, Last-Modified: {}", etag, last_modified);

    // Conditional request
    let response = client
        .get("http://127.0.0.1:8080/api/resource")
        .header("If-None-Match", etag)
        .header("If-Modified-Since", last_modified)
        .send()
        .await?;
    println!("Conditional Status: {}", response.status());
    Ok(())
}
```

- First GET fetches the resource and saves `ETag` and `Last-Modified`
- Second GET uses `If-None-Match` and `If-Modified-Since` headers to check if the resource has changed
- If the resource is unchanged, it receives a `304 Not Modified` response
- If the resource has changed, it receives a new response with updated data

### **C++ HTTP server with Crow**
- Crow is a C++ micro web framework that is easy to use and provides a simple API for building web applications
- It is built on top of Boost and provides support for asynchronous programming
- Crow supports routing, middleware, and JSON serialization

```cpp
#include <crow.h>
#include <nlohmann/json.hpp>
#include <openssl/sha.h>
#include <iomanip>
#include <sstream>

using json = nlohmann::json;

std::string sha256(const std::string& input) {
    unsigned char hash[SHA256_DIGEST_LENGTH];
    SHA256((unsigned char*)input.c_str(), input.length(), hash);
    std::stringstream ss;
    for (int i = 0; i < SHA256_DIGEST_LENGTH; i++) {
        ss << std::hex << std::setw(2) << std::setfill('0') << (int)hash[i];
    }
    return "\"" + ss.str() + "\"";
}

int main() {
    crow::SimpleApp app;

    CROW_ROUTE(app, "/api/resource").methods("GET"_method)([]() {
        json resource = {{"id", 1}, {"content", "Hello, World!"}};
        std::string content = resource.dump();
        std::string etag = sha256(content);
        // static for demo
        // in real cases, it should be dynamic
        // std::string last_modified = resource["last_modified"];
        std::string last_modified = "Sun, 13 Apr 2025 10:00:00 GMT";

        crow::response res(200, content);
        res.set_header("Content-Type", "application/json");
        res.set_header("Cache-Control", "public, max-age=3600");
        res.set_header("ETag", etag);
        res.set_header("Last-Modified", last_modified);
        return res;
    });

    app.port(8080).multithreaded().run();
    return 0;
}
```

- GET `/api/resource` returns JSON with `Cache-Control`, `ETag`, and `Last-Modified`
- `ETag` is computed using SHA-256

### **C++ HTTP Client with libcurl**
- libcurl is a simple and easy-to-use client-side URL transfer library
- It supports various protocols including HTTP, HTTPS, FTP, and more
- libcurl provides a rich set of features for handling requests and responses
```cpp
#include <curl/curl.h>
#include <iostream>

size_t WriteCallback(void* contents, size_t size, size_t nmemb, std::string* s) {
    s->append((char*)contents, size * nmemb);
    return size * nmemb;
}

int main() {
    CURL* curl = curl_easy_init();
    std::string response;

    if (curl) {
        // first request
        curl_easy_setopt(curl, CURLOPT_URL, "http://127.0.0.1:8080/api/resource");
        struct curl_slist* headers = NULL;
        headers = curl_slist_append(headers, "Accept: application/json");
        curl_easy_setopt(curl, CURLOPT_HTTPHEADER, headers);
        curl_easy_setopt(curl, CURLOPT_WRITEFUNCTION, WriteCallback);
        curl_easy_setopt(curl, CURLOPT_WRITEDATA, &response);
        CURLcode res = curl_easy_perform(curl);
        if (res == CURLE_OK) {
            char* etag;
            char* last_modified;
            curl_easy_getinfo(curl, CURLINFO_COOKIELIST, &etag); 
            curl_easy_getinfo(curl, CURLINFO_COOKIELIST, &last_modified);
            std::cout << "Response: " << response << std::endl;
        }
        curl_slist_free_all(headers);
        response.clear();

        // conditional request
        // replace with real ETag and last-Modified values
        headers = curl_slist_append(headers, "If-None-Match: \"abc123\"");
        headers = curl_slist_append(headers, "If-Modified-Since: Sun, 13 Apr 2025 10:00:00 GMT");
        curl_easy_setopt(curl, CURLOPT_HTTPHEADER, headers);
        res = curl_easy_perform(curl);
        if (res == CURLE_OK) {
            long status;
            curl_easy_getinfo(curl, CURLINFO_RESPONSE_CODE, &status);
            std::cout << "Conditional Status: " << status << std::endl;
        }
        curl_slist_free_all(headers);
        curl_easy_cleanup(curl);
    }
    return 0;
}
```

- First GET fetches the resource and saves `ETag` and `Last-Modified`
- Second GET uses `If-None-Match` and `If-Modified-Since` headers to check if the resource has changed
- If the resource is unchanged, it receives a `304 Not Modified` response
- If the resource has changed, it receives a new response with updated data

### **TypeScript HTTP server with Express**
```typescript
import express from 'express';
import { createHash } from 'crypto';

const app = express();
app.use(express.json());

app.get('/api/resource', (req, res) => {
    const resource = { id: 1, content: 'Hello, World!' };
    const content = JSON.stringify(resource);
    const etag = `"${createHash('sha256').update(content).digest('hex')}"`;
    const lastModified = new Date().toUTCString();

    if (req.get('If-None-Match') === etag) {
        return res.status(304).end();
    }

    res.set({
        'Content-Type': 'application/json',
        'Cache-Control': 'public, max-age=3600',
        'ETag': etag,
        'Last-Modified': lastModified,
    });
    res.status(200).json(resource);
});

app.listen(8080, () => console.log('Server running on port 8080'));
```

- GET `/api/resource` returns JSON with `Cache-Control`, `ETag`, and `Last-Modified`
- `ETag` is generated by hashing content
- Simulates `304 Not Modified` response if the resource is not modified

### **TypeScript HTTP client with Fetch**
```typescript
async function main() {
    // first request
    const response = await fetch('http://localhost:8080/api/resource', {
        headers: { 'Accept': 'application/json' },
    });
    const etag = response.headers.get('ETag');
    const lastModified = response.headers.get('Last-Modified');
    const resource = await response.json();
    console.log('Resource:', resource);
    console.log('ETag:', etag, 'Last-Modified:', lastModified);

    // conditional request
    const conditionalResponse = await fetch('http://localhost:8080/api/resource', {
        headers: {
            'If-None-Match': etag!,
            'If-Modified-Since': lastModified!,
        },
    });
    console.log('Conditional Status:', conditionalResponse.status);
}

main().catch(console.error);
```

- First GET fetches the resource and saves `ETag` and `Last-Modified`
- Second GET uses `If-None-Match` and `If-Modified-Since` headers to check if the resource has changed
- If the resource is unchanged, it receives a `304 Not Modified` response
- If the resource has changed, it receives a new response with updated data

### **SpringBoot HTTP server**
```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.security.MessageDigest;
import java.time.ZonedDateTime;
import java.time.format.DateTimeFormatter;

@RestController
@RequestMapping("/api/resource")
@SpringBootApplication
public class Main {
    public static void main(String[] args) {
        SpringApplication.run(Main.class, args);
    }

    static class Resource {
        public int id;
        public String content;

        public Resource(int id, String content) {
            this.id = id;
            this.content = content;
        }
    }

    @GetMapping
    public ResponseEntity<Resource> getResource(@RequestHeader(value = "If-None-Match", required = false) String ifNoneMatch) throws Exception {
        Resource resource = new Resource(1, "Hello, World!");
        String content = "{\"id\":1,\"content\":\"Hello, World!\"}";
        String etag = "\"" + MessageDigest.getInstance("SHA-256")
            .digest(content.getBytes())
            .toString() + "\"";
        String lastModified = ZonedDateTime.now().format(DateTimeFormatter.RFC_1123_DATE_TIME);

        if (etag.equals(ifNoneMatch)) {
            return new ResponseEntity<>(null, null, HttpStatus.NOT_MODIFIED);
        }

        HttpHeaders headers = new HttpHeaders();
        headers.add("Content-Type", "application/json");
        headers.add("Cache-Control", "public, max-age=3600");
        headers.add("ETag", etag);
        headers.add("Last-Modified", lastModified);
        return new ResponseEntity<>(resource, headers, HttpStatus.OK);
    }
}
```

- GET `/api/resource` returns JSON with `Cache-Control`, `ETag`, and `Last-Modified`
- `ETag` is generated by hashing content
- Simulates `304 Not Modified` response if the resource is not modified

### **SpringBoot HTTP client with RestTemplate**
```java
import org.springframework.http.*;
import org.springframework.web.client.RestTemplate;

public class Client {
    static class Resource {
        public int id;
        public String content;
    }

    public static void main(String[] args) {
        RestTemplate restTemplate = new RestTemplate();

        // first request
        HttpHeaders headers = new HttpHeaders();
        headers.add("Accept", "application/json");
        HttpEntity<String> entity = new HttpEntity<>(headers);
        ResponseEntity<Resource> response = restTemplate.exchange(
            "http://localhost:8080/api/resource",
            HttpMethod.GET,
            entity,
            Resource.class
        );
        String etag = response.getHeaders().getETag();
        String lastModified = response.getHeaders().getLastModified() > 0
            ? HttpDateFormat.toString(response.getHeaders().getLastModified())
            : "";
        System.out.println("Resource: " + response.getBody().content);
        System.out.println("ETag: " + etag + ", Last-Modified: " + lastModified);

        // conditional request
        headers.add("If-None-Match", etag);
        headers.add("If-Modified-Since", lastModified);
        entity = new HttpEntity<>(headers);
        ResponseEntity<Resource> conditionalResponse = restTemplate.exchange(
            "http://localhost:8080/api/resource",
            HttpMethod.GET,
            entity,
            Resource.class
        );
        System.out.println("Conditional Status: " + conditionalResponse.getStatusCode());
    }
}
```

- First GET fetches the resource and saves `ETag` and `Last-Modified`
- Second GET uses `If-None-Match` and `If-Modified-Since` headers to check if the resource has changed
- If the resource is unchanged, it receives a `304 Not Modified` response
- If the resource has changed, it receives a new response with updated data
