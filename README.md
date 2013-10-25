# Unirest for Objective-C [![Build Status](https://api.travis-ci.org/Mashape/unirest-obj-c.png)](https://travis-ci.org/Mashape/unirest-obj-c)

Unirest is a set of lightweight HTTP libraries available in multiple languages.

## Installing
<a href="https://github.com/Mashape/unirest-obj-c/archive/master.zip">Download</a> the Objective-C Unirest Library from <a href="https://github.com/Mashape/unirest-obj-c">GitHub</a> (or clone the repo) and import the folder into your project. You can also install Unirest-obj-c with [CocoaPods](http://cocoapods.org/).

The Unirest-Obj-C client library requires ARC (Automatic Reference Counting) to be enabled in your Xcode project. To enable ARC select your project or target and then go to Build Settings and under the section Apple LLVM compiler 3.0 - Language you will see the option Objective-C Automatic Reference Counting:

<img src="http://unirest.io/img/arc-enable.png" alt="Enable ARC in Xcode"/>

For existing projects, fortunately Xcode offers a tool to convert existing code to ARC, which is available at Edit -> Refactor  -> Convert to Objective-C ARC

## Creating Request
So you're probably wondering how using Unirest makes creating requests in Objective-C easier, let's look at a working example:

```objective-c
NSDictionary* headers = @{@"accept": @"application/json"};
NSDictionary* parameters = @{@"parameter": @"value", @"foo": @"bar"};

UNIHTTPJsonResponse* response = [[UNIRest post:^(UNISimpleRequest* request) {
  [request setUrl:@"http://httpbin.org/post"];
  [request setHeaders:headers];
  [request setParameters:parameters];
}] asJson];
```
    
Just like in the Unirest Java library the Objective-C library supports multiple response types given as the last parameter. In the example above we use `asJson` to get a JSON response, likewise there are `asBinary` and `asString` for responses of other nature such as file data and hypermedia responses.

## Asynchronous Requests
For non-blocking requests you will want to make an asychronous request to keep your application going while data is fetched or updated in the background, doing so with unirest is extremely easy with barely any code change from the previous example:

```objective-c
NSDictionary* headers = @{@"accept": @"application/json"};
NSDictionary* parameters = @{@"parameter": @"value", @"foo": @"bar"};

[[UNIRest post:^(UNISimpleRequest* request) {
  [request setUrl:@"http://httpbin.org/post"];
  [request setHeaders:headers];
  [request setParameters:parameters];
}] asJsonAsync:^(UNIHTTPJsonResponse* response) {
  // This is the asyncronous callback block
  NSInteger* code = [response code];
  NSDictionary* responseHeaders = [response headers];
  UNIJsonNode* body = [response body];
  NSData* rawBody = [response rawBody];
}];
```

## File Uploads
Transferring files through request with unirest in Objective-C can be done by creating a `NSURL` object and passing it along as a parameter value with a `UNISimpleRequest` like so:

```objective-c
NSDictionary* headers = @{@"accept": @"application/json"};
NSURL* file = nil;
NSDictionary* parameters = @{@"parameter": @"value", @"file": file};

UNIHTTPJsonResponse* response = [[UNIRest post:^(UNISimpleRequest* request) {
  [request setUrl:@"http://httpbin.org/post"];
  [request setHeaders:headers];
  [request setParameters:parameters];
}] asJson];
```
 
## Custom Entity Body
To send a custom body such as JSON simply serialize your data utilizing the `NSJSONSerialization` with a `BodyRequest` and `[method]Entity` instead of just `[method]` block like so:

```objective-c
NSDictionary* headers = @{@"accept": @"application/json"};
NSDictionary* parameters = @{@"parameter": @"value", @"foo": @"bar"};

UNIHTTPJsonResponse* response = [[UNIRest postEntity:^(UNIBodyRequest* request) {
  [request setUrl:@"http://httpbin.org/post"];
  [request setHeaders:headers];
  // Converting NSDictionary to JSON:
  [request setBody:[NSJSONSerialization dataWithJSONObject:headers options:0 error:nil]];
}] asJson];
```

# Request
The Objective-C unirest library uses configuration blocks of type UNISimpleRequest and UNIBodyRequest to configure the URL, Headers, and Parameters / Body of the request.

```objective-c
+(UNIHTTPRequest*) get:(void (^)(UNISimpleRequestBlock*)) config;

+(UNIHTTPRequestWithBody*) post:(void (^)(UNISimpleRequestBlock*)) config;
+(UNIHTTPRequestWithBody*) postEntity:(void (^)(UNIBodyRequestBlock*)) config;

+(UNIHTTPRequestWithBody*) put:(void (^)(UNISimpleRequestBlock*)) config;
+(UNIHTTPRequestWithBody*) putEntity:(void (^)(UNIBodyRequestBlock*)) config;

+(UNIHTTPRequestWithBody*) patch:(void (^)(UNISimpleRequestBlock*)) config;
+(UNIHTTPRequestWithBody*) patchEntity:(void (^)(UNIBodyRequestBlock*)) config;

+(UNIHTTPRequestWithBody*) delete:(void (^)(UNISimpleRequestBlock*)) config;
+(UNIHTTPRequestWithBody*) deleteEntity:(void (^)(UNIBodyRequestBlock*)) config;
```

- `UNIHTTPRequest` `[UNIRest get:` `(void (^)(UNISimpleRequestBlock*))] config;`  
  
  Sends equivalent request with method type to given URL
- `UNIHTTPRequestWithBody` `[UNIRest (post|postEntity):` `(void (^)(UNISimpleRequestBlock|UNIBodyRequestBlock)(*))] config;`  
  
  Sends equivalent request with method type to given URL
- `UNIHTTPRequestWithBody` `[UNIRest (put|putEntity):` `(void (^)(UNISimpleRequestBlock|UNIBodyRequestBlock)(*))] config;`  
  
  Sends equivalent request with method type to given URL
- `UNIHTTPRequestWithBody` `[UNIRest (patch|patchEntity):` `(void (^)(UNISimpleRequestBlock|UNIBodyRequestBlock)(*))] config;`  
  
  Sends equivalent request with method type to given URL
- `UNIHTTPRequestWithBody` `[UNIRest (delete|deleteEntity):` `(void (^)(UNISimpleRequestBlock|UNIBodyRequestBlock)(*))] config;`
  
  Sends equivalent request with method type to given URL

# Response
The `UNIHTTPRequest` and `UNIHTTPRequestWithBody` can then be executed by calling one of:

```objective-c
-(UNIHTTPStringResponse*) asString;
-(void) asStringAsync:(void (^)(UNIHTTPStringResponse*)) response;

-(UNIHTTPBinaryResponse*) asBinary;
-(void) asBinaryAsync:(void (^)(UNIHTTPBinaryResponse*)) response;

-(UNIHTTPJsonResponse*) asJson;
-(void) asJsonAsync:(void (^)(UNIHTTPJsonResponse*)) response;
```

- `-(UNIHTTPStringResponse*)` `asString;`  
  
  Blocking request call with response returned as string for Hypermedia APIs or other.
- `-(void)` `asStringAsync:` `(void (^)(UNIHTTPStringResponse*)) response;`  
  
  Asynchronous request call with response returned as string for Hypermedia APIs or other.
- `-(UNIHTTPBinaryResponse*)` `asBinary;`  
  
  Blocking request call with response returned as binary output for files and other media.
- `-(void)` `asBinaryAsync:` `(void (^)(UNIHTTPBinaryResponse*)) response;`  
  
  Asynchronous request call with response returned as binary output for files and other media.
- `-(UNIHTTPJsonResponse*)` `asJson;`  
  
  Blocking request call with response returned as JSON.
- `-(void)` `asJsonAsync:` `(void (^)(UNIHTTPJsonResponse*)) response;`  
  
  Asynchronous request call with response returned as JSON.
