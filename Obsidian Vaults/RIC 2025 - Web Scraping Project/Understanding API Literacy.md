
## HTTP Requests:
- messages sent by a client to a server requesting a specific resource.
- consists of:
	- request line
	- headers
	- Body (optional)
- an HTTP client send a request to a server in the form of a request message

## HTTP Request Components
1. Request line:  `<Method> <Request-URI> <HTTP-Version>`
2. Zero or more header (General | Request | Entity) fields followed by the CRLF
		1. **CR (Carriage Return)** `\r` or `0x0D` - moves the cursors to the beginning of the line without advancing to the next line
		2. **LF (Line Feed)** `\n` or `0x0D0A` - moves the cursor to the beginning of the line and then down to the next line
3. An empty line (line with nothing before the CRLF) that indicates the end of the header fields
4. an optional message body

### Request-Line
- begins with a method token, followed by the Request-URI, then the protocol version, and then the CRLF. the elements are separated by space (SP) characters.

### Request Method
- indicates the method performed on the resource identified by the given Request-URI. 
- methods are case sensitive and should always be capitalized

Methods supported in HTTP/1.1

| Method  | Description                                                                                                                      |
| ------- | -------------------------------------------------------------------------------------------------------------------------------- |
| GET     | retrieves information from the given server using the given URI. should only retrieve data and should have no effect on the data |
| HEAD    | same as GET but transfers the status line and the header section only                                                            |
| POST    | used to send data to the server (ex. customer information, file upload, etc. using HTML forms)                                   |
| PUT     | replaces all current representations of target resource with uploaded content                                                    |
| DELETE  | removes all current representations of the target resource given by URI                                                          |
| CONNECT | establishes a tunnel to the server identified by a given URI                                                                     |
| OPTIONS | describes the communication options for the target resource                                                                      |
| TRACE   | message loopback test along the path to the target resource                                                                      |

### Request-URI
- Uniform Resource Identifier
- identifies the resource in which the request is applied to
- most commonly used forms to specify a URI:

| Method            | Description                                                                                                                                                                                                                                                                                  |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Asterisk(\*) form | used when an HTTP request doesn't apply to a resource, but rather the server itself. only allowed when the method used doesn't apply to a resource. (ex. `OPTIONS * HTTP/1.1`)                                                                                                               |
| Absolute form     | used when HTTP request is made from a proxy. Proxy is requested to forward the request/service from valid cache and return that response. (ex. `GET https://site.com HTTP/1.1`)                                                                                                              |
| Origin form       | most common form. Used to identify a resource on an origin server or gateway. (ex. A client wising to retrieve a resource directly from the origin server would create a TCP connection to port 80 of the host and send: `GET /tailwind_css/indexhtm HTTP/1.1 Host: www.tutorialspoint.com`) |
| Authority form    | only used with `CONNECT`method                                                                                                                                                                                                                                                               |

### Request Header Fields
- allow the client to pass additional information about the request and the client itself to the server
- act as request modifiers
- list of important fields:
	- Accept-Charset
	- Accept-Encoding
	- Accept-Language
	- Authorization
	- Expect
	- From
	- Host
	- If-Match
	- If-Modified-Since
	- If-None-Match
	- If-Range
	- If-Unmodified-Since
	- Max-Forwards
	- Proxy-Authorization
	- Range
	- Referer
	- TE
	- User-Agent

### Request Message Examples:
`Get /hello.html HTTP/1.1`
`User-Agent: Mozilla/4.0 (compatible; MSIE5.01; Windows NT)`
`Host: www.tutorialspoint.com`
`Accept-Language: en-us`
`Accept-Encoding: gzip, deflate`
`Connection: Keep-Alive`

above is an example of fetching a plain html page from the server. connection is a general-header and the rest of the headers are request headers.

## HTTP Responses
- messages sent by a server replying to a client's request

### HTTP Response Components:
- A status line: `<HTTP-Version> <Status-Code> <Reason-Phrase>`
- Zero or more (General | Response | Entity) fields
- an empty line (ie. a line with nothing before the CRLF) indicating the end of the header fields
- message-body (optional)

### Message Status-Line
- consists of protocol version, numerical status code and its associated textual phrase. elements are separated by space SP characters.

### HTTP Version
- any server supporting HTTP version 1.1 will return:
- `HTTP-Version = HTTP/1.1`

### Status Code
- Status-Code element is a 3 digit integer where the first digit defines the class of the response and the last two digits don't have a categorization role


| Status Code         | Description                                               |
| ------------------- | --------------------------------------------------------- |
| 1XX - Informational | request was received and process continues                |
| 2XX - Success       | action was successful, received, understood, and accepted |
| 3XX - Redirection   | further action must be taken to complete the request      |
| 4XX - Client Error  | request contains incorrect syntax or cannot be fulfilled  |
| 5XX - Server error  | server failed to fulfill a valid request                  |

## Response Header Fields
- response-header fields allow the server to pass additional information about the response which cannot be placed, into the Status-Line. these give more information about the server and about access to the resource identified by the Request-URI
- examples:
	- Accept-Ranges
	- Age
	- ETag
	- Location
	- Proxy-Authenticate
	- Retry-After
	- Server
	- Vary
	- WWW-Authenticate


## HTTP Methods
- specifies the action to be performed on resources

### GET Method
- retrieves data from a web server by specifying parameters in the URL portion of the request
- main method for document retrieval

### HEAD Method
- functionally similar to GET, except the server replies with a response line and header but no entity-body