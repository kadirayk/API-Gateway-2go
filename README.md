# API Gateway 2go

A minimal API Gateway written in golang. 


**API Gateway 2go** seeks for a predefined http header in the http request and forwards it to a desired URL. You can define as much URL paths as you want to be listened and forwarded to.



###Usage
 
- Modify `config.json` for your needs
- Put `config.json` and `main.go` in the same folder
- then: 
```go
$ go run main.go
```

###Configuration

**API Gateway 2go** uses a pretty straightforward configuration file in json format.

- `port` sets the port to be listened.
- You can define as many handlers as you want in `Handlers` list.
- `listenPath` defines the path to be listened.
- `headerName` headerName is the name of the http header that **API Gateway 2go** will look for when deciding where to forward the request. You can give any name you like.
- `DefaultForwardPath` and `ForwardPaths` have similar structures. `DefaultForwardPath` path will be used if the header or a matching condition not found in the http header. 
- `condition` is the value of the defined http header that will help deciding the URL that the request will be forwarded to.
- `path` is the target URL that we want the request to be forwared to.
- `ContentType` is requests content type.
- `BasicAuth` is the field you can enter your username and password if needed, leave it empty if not required.


####Example Configuration

Following json example shows how you can setup multiple url routers with different http headers. First handler will look for a http header named **api_version** and forward the incoming http request to desired versions of the rest api with json content. Second handler will look for a http header named **environment** and forward the request to desired environment of the SOAP web service with xml content.

```javascript
{
	"router": {
		"port": "8081",
		"Handlers": [{
			"listenPath": "/api/GetUsers",
			"headerName": "api_version",
			"DefaultForwardPath": {
				"path": "https://www.example.com/api/v1/GetUsers",
				"ContentType": "application/json",
				"BasicAuth": {
					"Username": "your_username",
					"Password": "your_password"
				}
			},
			"ForwardPaths": [{
				"condition": "1",
				"path": "https://www.example.com/api/v1/GetUsers",
				"ContentType": "application/json",
				"BasicAuth": {
					"Username": "your_username",
					"Password": "your_password"
				}
			}, {
				"condition": "2",
				"path": "https://www.example.com/api/v2/GetUsers",
				"ContentType": "application/json",
				"BasicAuth": {
					"Username": "your_username",
					"Password": "your_password"
				}
			}]
		}, {
			"listenPath": "/service/UserService",
			"headerName": "environment",
			"DefaultForwardPath": {
				"path": "https://test.example.com/service/UserService?WSDL",
				"ContentType": "text/xml",
				"BasicAuth": {
					"Username": "your_username",
					"Password": "your_password"
				}
			},
			"ForwardPaths": [{
				"condition": "test",
				"path": "https://test.example.com/service/UserService?WSDL",
				"ContentType": "text/xml",
				"BasicAuth": {
					"Username": "your_username",
					"Password": "your_password"
				}
			}, {
				"condition": "local",
				"path": "http://localhost:8080/service/UserService?WSDL",
				"ContentType": "text/xml"
			}, {
				"condition": "prod",
				"path": "https://www.example.com/service/UserService?WSDL",
				"ContentType": "text/xml",
				"BasicAuth": {
					"Username": "your_username",
					"Password": "your_password"
				}
			}]
		}]
	}
}
```
