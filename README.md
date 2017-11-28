# aws-lambda-multipart-parser
## Introduction
Support of multipart/form-data requests is a relatively new feature for AWS Lambdas. 
Although, there is such feature, majority of libraries for parsing multipart/form-data requests is based on server technology, which can't be used in case of AWS Lambdas. That's why, AWS Lambda specific multipart/form-data parser was created.
## Steps for integrating multipart/form-data to your lambda
### 1.Create project with serverless framework.
You can find all necessary information there:
  - at official github page: https://github.com/serverless/serverless
  - nice youtube playlist about serverless: https://www.youtube.com/watch?v=lUTGk64jppM&list=PLzvRQMJ9HDiT5b4OsmIBiMbsPjfp4kfg3
### 2. Add configuration for your AWS Lambda.
Go to serverless.yml file and add configurations for your AWS Lambda:
  ```
   method: POST
   integration: LAMBDA
  ```
`method: POST` - because of we are receiving multipart/form-data request.
`integration: LAMBDA` - without that I constanly got 502 Error: Bad Gateway, but I don't know why.
### 3. Deploy a draft of your function
Execute command `sls deploy` in AWS Lambda folder. (for more information look for sources at step 1)
### 4. Configure API Gateway at AWS Console.
1. Go to API Gateway.
2. Select your API in API Gateway interface
![capture](https://user-images.githubusercontent.com/20258226/33311015-d7901538-d423-11e7-84d8-73b8cff51b42.PNG)
3. Go to Binary Support
![capture](https://user-images.githubusercontent.com/20258226/33311137-21b6006e-d424-11e7-95ad-52c4883ee106.PNG)
4. Add binary support for multipart/form-data content type
![capture](https://user-images.githubusercontent.com/20258226/33311182-4540cf8c-d424-11e7-8a81-df23d6f52c6e.PNG)
5. Go to Resources -> POST method of your API -> Integration Request
![capture](https://user-images.githubusercontent.com/20258226/33311472-324188d0-d425-11e7-9930-86fbad3db181.PNG)
6. Check Use Lambda Proxy Integration
![capture](https://user-images.githubusercontent.com/20258226/33311533-57ea733a-d425-11e7-8297-485045c8959e.PNG)
7. Deploy your API changes
![capture](https://user-images.githubusercontent.com/20258226/33311608-9cf019a8-d425-11e7-881f-874577a6c4e2.PNG)
### 5. You're ready to use the library.
1. Import aws-lambda-multipart-parser with `npm install --save aws-lambda-multipart-parser` command.
2. Require it in file `const multipart = require('aws-lambda-multipart-parser');`.
3. Pass your event object to parse function like that `multipart.parse(event)`. Parse function will return object representing the request body.
### 6. Request body object
```
{
    "file": {
        "type": "file",
        "filename": "lorem.txt",
        "contentType": "text/plain",
        "content": {
            "type": "Buffer",
            "data": [ ... byte array ... ]
        }
    },
    "field": "value"
}
```
All fields are represented in request body object as a key-value pair.
All files are represented as an object with these fields:
  - __type__ - indicates that it's a file
  - __filename__ - name of uploaded file (the first one)
