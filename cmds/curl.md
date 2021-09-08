# Test CORS

Send pre-flight request:
```
curl -H "Origin: http://example.com" -H "Access-Control-Request-Method: POST" -H "Access-Control-Request-Headers: X-Requested-With" -X OPTIONS --verbose "https://lgl.koolearn.com/CloudClass/AppKey/8a2af988536458c301537d7197320004/BusinessSystem/BusinessApp/Evaluate/GetCommitedInfo?sig=AEC945BF1C574A9D0E22E379986CD799"
```
You should get a successful response tha includes “Access-Control-Allow-Origin”, “Access-Control-Allow-Methods”, and “Access-Control-Allow-Headers” headers:
* Access-Control-Allow-Origin: *
* Access-Control-Allow-Methods: GET, POST, OPTIONS
* Access-Control-Allow-Headers: DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Authorization

Note:
* response header "Access-Control-Allow-Methods" should not be "*"

# Send File

Send file to web server
```
curl -XPOST -F 'fileName=1.png' -F 'file=@"/Users/zhaotian/Downloads/1.png"' -F 'width=3024' -F 'height=4032' 'http://127.0.0.1'
```
