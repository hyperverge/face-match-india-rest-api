# HyperVerge Face Match API India - Documentation

## Overview

This documentation describes the Face Match API v1 for India. The postman collection can be found at this [link](https://www.getpostman.com/collections/d52f1ff543f6995a97a8).

## Table Of Contents

- [HyperVerge Face Match API India - Documentation](#hyperverge-face-match-api-india-documentation)
	- [Overview](#overview)
	- [Schema](#schema)
	- [Endpoint](#endpoint)
	- [Authentication](#authentication)
	- [Media Types](#media-types)
	- [Sample Code](#sample-code)
	- [API Call Structure](#api-call-structure)
	- [Response Structure](#response-structure)
	- [Optional parameters](#optional-parameters)

## Schema

Currently, HTTP and HTTPS are supported. All data is received as JSON, and all image uploads are to be performed as form-data (POST request).
It is recommended that HTTPS is used for all API calls. For HTTPS, only TLS v1.2 is supported to ensure better security.

## Endpoint
A `GET` request can be issued to the root endpoint to check for successful connection. The `plain/text` reponse of `Hello!` should be received.
```
https://ind-faceid.hyperverge.co/v1/photo/verifyPair
```

## Authentication

- Currently, an appId, appKey combination is passed in the request header. The appId and appKey are provided on request by the HyperVerge team. If you would like to try the API, please reach out to contact@hyperverge.co

- On failed attempt with invalid credentials or unauthorized access the HTTP response would have a status code : 401

- Please do not expose the appId and appKey on browser applications. In case of a browser application, set up the API calls from the server side.

## Media Types

Currently, `jpeg, png and tiff` images are supported by the API.

## Sample Code

    curl -X POST https://ind-faceid.hyperverge.co/v1/photo/verifyPair \
		  -H 'appid: xxx' \
		  -H 'appkey: yyyy' \
		  -H 'content-type: multipart/form-data;\
		  -F 'selfie=@image_1_path.png' \
		  -F 'id=@image_2_path.png'
    
## API Call Structure

The API call is used to determine if 2 face images belong to the same person

* **Endpoint**
   https://ind-faceid.hyperverge.co/v1/photo/verifyPair

* **Method:**
    `POST`

* **Header**
	- content-type : 'formdata'
	- appid
	- appkey

* **Request Body**

	- To match user photo with the face in an ID card:
		- selfie - jpeg, png or tiff image
		- id - jpeg, png or tiff image
   
   - To match one user photo with another user photo:
	   - selfie - jpeg, png or tiff image
	   - selfie2 - jpeg, png or tiff image

## Response Structure

* **Success Response:**
If the API call was successful and face match was performed, the response would have the following schema:

```
  {
    "status" : "success",
    "statusCode" : "200",
    "result" : {
      "match" : <"yes"/"no">,
      "match-score" : <0-100>
      "to-be-reviewed": <"yes"/"no">,
    }
  }
```

* **Error Response:**

	* **Face detection failed**:
		* Face not detected in selfie for a 'Selfie - ID match' API Call

			```
			{
			    "status": "failure",
			    "statusCode": "400",
			    "error": "Face not detected in Selfie image"
			}
			```
		* Face not detected in ID for a 'Selfie - ID match' API Call
			```
			{
			    "status": "failure",
			    "statusCode": "400",
			    "error": "Face not detected in ID image"
			}
			```
		* Face not detected in one of the selfies in 'Selfie - Selfie match' API Call
			```
			{
			    "status": "failure",
			    "statusCode": "400",
			    "error": "Face not detected in one or more images"
			}
			```


	- **Request Errors**
	Please note that these errors are applicable only for API integration and don't happen when the SDK is used.
	
		- One of the images is missing

				{
				  "status": "failure",
				  "statusCode": "400",
				  "error": "Missing / Wrong combination of parameters. Valid Parameter combinations - (image1, image2, type) or (selfie, selfie2) or (selfie, id) or (selfie, idFaceString)"
				}	
	
		- Larger than allowed image input

				{
				  "status": "failure",
				  "statusCode": "400",
				  "error": "image size cannot be greater than 6MB"
				}

	- **Server Errors** 
We try our best to avoid these errors, but if by chance they do occur the response code will be 5xx.
		
		- Form parse error or bad form-data. If the following happens, please make sure the form data is correctly set.
		 
				{
			       "status": "failure",
			       "statusCode": "500",
			       "error": "upload error"
				}

All error messages follow the same syntax with the statusCode, status and error(string) being part of the body.

## Optional parameters

Following are optional parameters that could be set as part of the request body.

| parameter | Body or Header | value| default| description |
|---|---|:---:|:---:|---|
| referenceId | Header |String|null|`referenceId` is an identifier for the API call and is echoed back by the server in the response. This referenceId can be used to create logical grouping of single/multiple API calls based on business logic of the consumer of API. HyperVerge's QC dashboard can be used to fetch logs grouped by the Reference ID|
| uuid | Header | String | null | This is an advanced feature designed to prevent tampering of response by a man in the middle. If `uuid` is set, the response will include an `X-Response-Signature` header which will have a checksum of the response body signed with the `uuid` and a private key|
| enableDashboard| Body | "yes"/"no" | "no" | This will give access to an exclusive dashboard built by HyperVerge for Quality Check and debugging purpose. This dashboard will enable realtime usage monitoring and can be used to pin-point integration issues in POC/production(if any) |
|allowMultipleFaces|Body|"yes"/"no"|"no"|An error is returned when multiple faces are detected in the selfie image

For more details regarding any of the above features, please contact your POC from HyperVerge.
