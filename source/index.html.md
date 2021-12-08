---
title: Vance AI API Document

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - ruby
  - python
  - javascript
  - php

toc_footers:
  - <a href='https://vanceai.com/sign-in/?method=sign_up'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: API Documents shows you information about Vance AI API. Learn more to integrate our AI image processing features into your app or workflow.
---

# Introduction

Vance AI provides a suite of simple and flexible API endpoints to allow you to integrate our AI image processing features into your app or workflow.

# Authentication

The Vance AI API requires authentication via an API token generated within your Vance AI account. You can find the API token in the tab of [API](https://vanceai.com/my-account/api/) on "My Account" page.

All of the methods in the Vance AI API require authentication using an `api_token`.

<aside class="notice">
### Do not expose your API token publicly

The api_token should not be exposed publicly, such as within your software /application or on your website, as this allows users access to any authenticated APIs documented here, and will cost your credits.
</aside>

# General

Response Success Example

> HTTP/1.1 200 OK

```json
{
    "code":200,
    "data":{
        "uid":"a56b7626da85dcd0fc68b91bc49b725d"
    }
}
```

Response Error Example

> HTTP/1.1 200 OK

```json
{
    "code": 10001, 
    "msg": "Illegal Parameter"
}
```

> OR

> HTTP/1.1 200 OK
```json
{
    "code": 10001, 
    "msg": {    
           "api_token": [
                 "The api token field is required."
            ]
     }
}
```

## Upload

`POST` https://api-service.vanceai.com/web_api/v1/upload

### Parameters

Field | Type | Description
----- | ---- | -----------
api_token | string | Your API token
file | file | Image file object
job | string | Optional for AI features. Example: 'ai' for AI features and 'compress' for Compressor.

### Error Codes:

Code | Description
---- | -----------
10001 | Illegal parameter
10010 | Internal error
30001 | Invalid API token
30004 | Limit Exceed

```php
post("https://api-service.vanceai.com/web_api/v1/upload", [
  'multipart' => [
    [
        'name' => 'api_token',
        'contents' => '{{YOUR_API_KEY}}',
    ],
    [
        'name' => 'file',
        'contents' => fopen('{{YOUR_FILE_PATH}}', 'r'),
    ]
  ]
]);

echo $res->getBody()->getContents();

```
```python
# Requires "requests" to be installed (see python-requests.org)
import requests
response = requests.post(
    'https://api-servie.vanceai.com/web_api/v1/upload',
    files={'file': open('{SOURCE_IMAGE_FILE_PATH}', 'rb')},
    data={'api_token': '{YOUR_API_TOKEN}'},
)
r = response.json()
if r['code'] == 200:
    print('uid:', r['data']['uid'])
else:
    print("Error:", r['code'], r['msg'])
```

> Response Success Example:

```json
{
    "code": 200,
    "data": {
        "uid": "b0c5b210dd1a698d0554bca3d1bdd7b4" // ID of Uploaded file
    }
}
```

## Transform

`POST` https://api-service.vanceai.com/web_api/v1/transform

### Parameters

Field | Type | Description
----- | ---- | -----------
api_token | string | Your API token
uid | string | ID of uploaded file
jconfig | string | Transform parameter in json format (Required)

### Error Codes:

Code | Description
---- | -----------
10001 | Illegal parameter
10010 | Internal error
10011 | File doesn't exist
10012 | Job exceeds limitation
10013 | jparam parse error
10014 | Job failed and exited for unexpected reason

```php
$http_client = new \GuzzleHttp\Client([
  'base_uri' => 'https://api-service.vanceai.com',
  'verify'  => false,
]);

$json_string = file_get_contents("{{enlarge.json}}");
$json_string = trim($json_string,chr(239).chr(187).chr(191));

$response = $http_client->request('POST','/web_api/v1/transform', [
    'form_params' => [
        'api_token' =>  '{YOUR_API_TOKEN}',
        'uid' => 'c6b3ae488e47a4e9495a1e5ed6d0c401',
        'jconfig' => $json_string,
    ]
]);

echo $response->getBody()->getContents();
```

```python
import requests
import json

jparam={}
with open(json_path, 'rb') as f:
    jparam = json.load(f)
    
data={
    'uid': '{uid_getted_from_upload_api}',
    'jconfig': json.dumps(jparam),
    'api_token': '{YOUR_API_TOKEN}'
    }
response = requests.post(
    'https://api-service.vanceai.com/web_api/v1/transform',
     data)
r = response.json()
if r['code'] == 200:
    print('uid:', r['data']['trans_id'])

```

> Response Success Example:
```json
{
    "code": 200,
    "data": {
        "trans_id": "fe854a3b20237f70d1efb757b6aa0cc4", //Transform Job ID
        "status": "finish", //waiting, finish, fatal, process
        "content": "finished, process time = 0.34204" //Processing details
    }
}
```

## Progress

`GET|POST` https://api-service.vanceai.com/web_api/v1/progress

### Parameters

Field | Type | Description
----- | ---- | -----------
api_token | string | Your API token
trans_id | string | ID in transform response

### Error Codes:

Code | Description
---- | -----------
10001 | Illegal parameter
10010 | Internal error

> Response Success Example:
```json
{
    "code": 200,
    "data": {
        "status": "finish", //waiting, finish, fatal, process
        "content": "[2021-07-06 02:40:10,034.034] INFO: inference finished!\n"
    }
}
```

## Download

`GET|POST` https://api-service.vanceai.com/web_api/v1/download

### Parameters

Field | Type | Description
----- | ---- | -----------
api_token | string | Your API token
trans_id | string | ID in transform response

### Error Codes:

Code | Description
---- | -----------
10001 | Illegal parameter
10010 | Internal error
10011 | Invalid download link

```php
$remoteFileUrl = 'https://api-service.vanceai.com/web_api/v1/download?trans_id={trans_id_getted_from_transform_api}&api_token={YOUR_API_TOKEN}';

$ch = curl_init();
$timeout = 60;
curl_setopt($ch, CURLOPT_URL, $remoteFileUrl);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
curl_setopt($ch, CURLOPT_CONNECTTIMEOUT, $timeout);
$img = curl_exec($ch);
curl_close($ch);

$fp2 = @fopen('{{save_to_file_path}}', 'a');
fwrite($fp2, $img);
fclose($fp2);
```

```python
remoteFileUrl = 'https://api-service.vanceai.com/web_api/v1/download?trans_id={trans_id_getted_from_transform_api}&api_token={YOUR_API_TOKEN}';
dst_path = 'C:/demo.jpg'

response = requests.get(remoteFileUrl, stream=True)

f = open(dst_path, "wb")
for chunk in response.iter_content(chunk_size=512):
    if chunk:
        f.write(chunk)
f.close()
```

## Config Files

Download the [Config Files](https://c.vanceai.com/config/infer_api/API_JSON_CONFIG.zip) here.
