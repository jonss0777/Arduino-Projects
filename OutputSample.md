Connected to server(script.google.com)

-Send POST 
-payload: {"command":"appendRow","sheet_name":"Sheet1","values":"demo,1,2,2,3"}

-POST Response:
-HTTP/1.1 302 Moved Temporarily
-Location: https://script.googleusercontent.com/macros/...

Disconnect from server(script.google.com)

Handle Response(Redirect): // calls getHttp

Connected to server(script.googleusercontent.com)

-Redirect URL:
-https://script.googleusercontent.com/macros/...

-GET response:
-Success

Disconnect from server(script.googleusercontent.com)

