# ProxyKitIssue60Repro

You'll need a recent version of [node.js](https://nodejs.org) installed. You need to `npm install` or you will get an exception because of missing JavaScript libs.

In Visual Studio, set multiple StartUp projects: AureliaWebApp and FailingProxy. Run the apps and wait for them to start. Modify for example [home.html](AureliaWebApp/ClientApp/home/home.html), save the file and see HMR replace the content in AureliaWebApp, unless running over FailingProxy. FailingProxy will eventually throw an exception similar to this:
```
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/2.0 GET https://localhost:44317/dist/__webpack_hmr  
System.Net.Http.HttpClient.ProxyKitClient.LogicalHandler:Information: Start processing HTTP request GET http://localhost:54469/dist/__webpack_hmr
System.Net.Http.HttpClient.ProxyKitClient.ClientHandler:Information: Sending HTTP request GET http://localhost:54469/dist/__webpack_hmr
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:54469/dist/__webpack_hmr  
System.Net.Http.HttpClient.ProxyKitClient.ClientHandler:Information: Received HTTP response after 57.6952ms - OK
System.Net.Http.HttpClient.ProxyKitClient.LogicalHandler:Information: End processing HTTP request after 92.4618ms - OK
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 407907.3611ms 200 text/event-stream; charset=utf-8

.
.
.

Exception thrown: 'System.Threading.Tasks.TaskCanceledException' in System.Private.CoreLib.dll
Exception thrown: 'System.Threading.Tasks.TaskCanceledException' in System.Private.CoreLib.dll
Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware:Error: An unhandled exception has occurred while executing the request.

System.Threading.Tasks.TaskCanceledException: The operation was canceled. ---> System.IO.IOException: Unable to read data from the transport connection: The I/O operation has been aborted because of either a thread exit or an application request. ---> System.Net.Sockets.SocketException: The I/O operation has been aborted because of either a thread exit or an application request
   --- End of inner exception stack trace ---
   at System.Net.Sockets.Socket.AwaitableSocketAsyncEventArgs.ThrowException(SocketError error)
   at System.Net.Sockets.Socket.AwaitableSocketAsyncEventArgs.GetResult(Int16 token)
   at System.Net.Http.HttpConnection.FillAsync()
   at System.Net.Http.HttpConnection.ChunkedEncodingReadStream.CopyToAsyncCore(Stream destination, CancellationToken cancellationToken)
   --- End of inner exception stack trace ---
   at System.Net.Http.HttpConnection.ChunkedEncodingReadStream.CopyToAsyncCore(Stream destination, CancellationToken cancellationToken)
   at ProxyKit.ProxyMiddleware.CopyProxyHttpResponse(HttpContext context, HttpResponseMessage responseMessage) in C:\Users\Jussi\Documents\GitHub\cNeuro\src\ProxyKit\ProxyMiddleware.cs:line 55
   at ProxyKit.ProxyMiddleware.Invoke(HttpContext context) in C:\Users\Jussi\Documents\GitHub\cNeuro\src\ProxyKit\ProxyMiddleware.cs:line 26
   at Microsoft.AspNetCore.Builder.Extensions.MapMiddleware.Invoke(HttpContext context)
   at Microsoft.AspNetCore.Builder.Extensions.MapMiddleware.Invoke(HttpContext context)
   at Microsoft.AspNetCore.Builder.Extensions.MapMiddleware.Invoke(HttpContext context)
   at Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware.Invoke(HttpContext context)
Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware:Warning: The response has already started, the error page middleware will not be executed.
Microsoft.AspNetCore.Server.IIS.Core.IISHttpServer:Error: Connection ID "18086456105130524772", Request ID "80000065-0000-fb00-b63f-84710c7967bb": An unhandled exception was thrown by the application.

System.Threading.Tasks.TaskCanceledException: The operation was canceled. ---> System.IO.IOException: Unable to read data from the transport connection: The I/O operation has been aborted because of either a thread exit or an application request. ---> System.Net.Sockets.SocketException: The I/O operation has been aborted because of either a thread exit or an application request
   --- End of inner exception stack trace ---
   at System.Net.Sockets.Socket.AwaitableSocketAsyncEventArgs.ThrowException(SocketError error)
   at System.Net.Sockets.Socket.AwaitableSocketAsyncEventArgs.GetResult(Int16 token)
   at System.Net.Http.HttpConnection.FillAsync()
   at System.Net.Http.HttpConnection.ChunkedEncodingReadStream.CopyToAsyncCore(Stream destination, CancellationToken cancellationToken)
   --- End of inner exception stack trace ---
   at System.Net.Http.HttpConnection.ChunkedEncodingReadStream.CopyToAsyncCore(Stream destination, CancellationToken cancellationToken)
   at ProxyKit.ProxyMiddleware.CopyProxyHttpResponse(HttpContext context, HttpResponseMessage responseMessage) in C:\Users\Jussi\Documents\GitHub\cNeuro\src\ProxyKit\ProxyMiddleware.cs:line 55
   at ProxyKit.ProxyMiddleware.Invoke(HttpContext context) in C:\Users\Jussi\Documents\GitHub\cNeuro\src\ProxyKit\ProxyMiddleware.cs:line 26
   at Microsoft.AspNetCore.Builder.Extensions.MapMiddleware.Invoke(HttpContext context)
   at Microsoft.AspNetCore.Builder.Extensions.MapMiddleware.Invoke(HttpContext context)
   at Microsoft.AspNetCore.Builder.Extensions.MapMiddleware.Invoke(HttpContext context)
   at Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware.Invoke(HttpContext context)
   at Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware.Invoke(HttpContext context)
   at Microsoft.AspNetCore.Server.IIS.Core.IISHttpContextOfT`1.ProcessRequestAsync()
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 29710.8822ms 200 text/event-stream; charset=utf-8
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 34543.7289ms 200 text/event-stream; charset=utf-8
```

# The fix
This issue is fixed by removing `<AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>` from [FailingProxy.csproj](FailingProxy/FailingProxy.csproj), as demonstrated by the Proxy project that works fine with HMR, see [Proxy.csproj](Proxy/Proxy.csproj).
