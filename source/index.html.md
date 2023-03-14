---
title: Vance AI API Documentation

language_tabs: # must be one of https://git.io/vQNgJ
  - shell: cURL
  - php: PHP
  - python: Python


toc_footers:
  - <a href='https://vanceai.com/sign-in/?method=sign_up&source=api_docs&redirect=https://vanceai.com/my-account/api/' target="_blank">Sign Up for a Developer API Key</a>

includes:
  - errors
  - api-management
  - configfiles

search: true

code_clipboard: true

meta:
  - name: description
    content: API Documents shows you information about Vance AI API. Learn more to integrate our AI image processing features into your app or workflow.
---

# Introduction

Vance AI provides a suite of simple and flexible API endpoints to allow you to integrate our AI image processing features into your app or workflow.

# Authentication

The Vance AI API requires authentication via an API token generated within your Vance AI account. You can find the API token in the tab of [API & Document](https://vanceai.com/my-account/api/) on "My Account" page.

All of the methods in the Vance AI API require authentication using an `api_token`.

In this document we use  `1234567890abcdefg` as the `api_token`.

<aside class="warning">
<strong>Do not</strong> expose your API token publicly.  
The api_token should not be exposed publicly, such as within your software /application or on your website, as this allows users access to any authenticated APIs documented here, and will cost your credits.
</aside>

# Upload image

> Request body

```shell
curl -X POST "https://api-service.vanceai.com/web_api/v1/upload" \
     -F "api_token=1234567890abcdefg" \
     -F "file=@/Users/vanceai/Downloads/cat.jpg; filename=cat.jpg" \
```

```php
<?php
  require 'vendor/autoload.php'; 
  use GuzzleHttp\Client; 
 
  $http_client = new \GuzzleHttp\Client();

  $res = $http_client->post("https://api-service.vanceai.com/web_api/v1/upload", [
    'multipart' => [
      [
          'name' => 'api_token',
          'contents' => '1234567890abcdefg',
      ],
      [
          'name' => 'file',
          'contents' => fopen('/Users/vanceai/Downloads/cat.jpg', 'r'),
      ]
    ]
  ]);

  echo $res->getBody()->getContents();
?>
```

```python
# Requires "requests" to be installed (see python-requests.org)
import requests
response = requests.post(
    'https://api-service.vanceai.com/web_api/v1/upload',
    files={'file': open('/Users/vanceai/Downloads/cat.jpg', 'rb')},
    data={'api_token': '1234567890abcdefg'},
)
r = response.json()
if r['code'] == 200:
    print('uid:', r['data']['uid'])
```

> Response Success Example:

```json
{
    "code": 200,
    "cscode": 200,
    "data": {
        "uid": "3f8484a52c47b9cc0c34067b5bdf08d3",
        "name": "'cat.png'",
        "thumbnail": "",
        "w": 1200,
        "h": 1199,
        "filesize": 3618416
    },
    "ip": "54.103.172.45"
}
```

<p class="endpoint"><code>POST</code> https://api-service.vanceai.com/web_api/v1/upload</p>

To do any actions, you should upload the image first.  Let's assume we have an image named `cat.jpg` and the full path is `/Users/vanceai/Downloads/cat.jpg`. The dimension of this image is 1200x1200px and the file size is 156KB.

<strong>Demo image</strong>

<img src="/images/cat.jpg" alt="cat.jpg" width="280" />

So now, let's upload this image by calling `upload` endpoint.

Uploading will take some times, that depends on the size of your image and your internet speed.

After uploading, you will get the response content with an `uid`, which will be used in the `transform` to process your image.

### Supported formats

Currently, the supported file formats are `JPG` and `PNG`.

### Upload limits

The file size can not be larger than 10MB and the max resolution is 34 Megapixels (short for MP).  

For example, if the dimension of your image is 7680x4320, then the resolution is 33 MP (33,177,600 pixels), which is less than 34 MP.
 
If your image exceeds the limits, you will get an error response, then you should resize or compress your image and try to call the upload again.

### Parameters

Field | Type | Description
----- | ---- | -----------
api_token | string | Your API token
file | file | Image file object
job | string | Optional for AI features. Example: 'ai' for AI features and 'compress' for Compressor.

# Process image

> Request body

```shell
curl -X POST "https://api-service.vanceai.com/web_api/v1/transform" \
     -d 'api_token=1234567890abcdefg' \
     -d 'uid=eb54870e6a45835c540537f79bc7ce5a' \
     -d 'webhook=https://your-domain/path/to/webhook' \
     -d 'jconfig={
            "job": "enlarge",
            "config": {
              "module": "enlarge",
              "module_params": {
                "model_name": "EnlargeStable",
                "suppress_noise": 26,
                "remove_blur": 26,
                "scale": "2x"
              },
              "out_params": {}
            }
          }'
```

```php
<?php
  $http_client = new \GuzzleHttp\Client([
    'base_uri' => 'https://api-service.vanceai.com',
    'verify'  => false,
  ]);

  $json_string = file_get_contents("/Users/vanceai/Downloads/enlarge.json");
  $json_string = trim($json_string,chr(239).chr(187).chr(191));

  $response = $http_client->request('POST','/web_api/v1/transform', [
      'form_params' => [
          'api_token' =>  '1234567890abcdefg',
          'uid' => 'b0c5b210dd1a698d0554bca3d1bdd7b4',
          'jconfig' => $json_string,
          'webhook' => 'https://your-domain/path/to/webhook',
      ]
  ]);

  echo $response->getBody()->getContents();
?>
```

```python
import requests
import json

json_path = "/Users/vanceai/Downloads/enlarge.json"
jparam={}
with open(json_path, 'rb') as f:
    jparam = json.load(f)
    
data={
    'api_token': '1234567890abcdefg',
    'uid': 'b0c5b210dd1a698d0554bca3d1bdd7b4',
    'jconfig': json.dumps(jparam),
    'webhook': 'https://your-domain/path/to/webhook'
    }
response = requests.post(
    'https://api-service.vanceai.com/web_api/v1/transform',
     data)
r = response.json()
if r['code'] == 200:
    print('trans_id:', r['data']['trans_id'])
```

> Response Success Example:

```json
{
    "code": 200,
    "cscode": 200,
    "data": { 
        "trans_id": "6de4b562d1a01c3d2520608eae929646", //Transform Job ID
        "status": "webhook", // waiting, finish, fatal, process, webhook
    },
    "ip":"54.103.172.45"
}
```

<p class="endpoint"><code>POST</code> https://api-service.vanceai.com/web_api/v1/transform</p>

When we get the `uid` in the first upload step, we can use it to call the `transform` endpoint.

The `transform` needs 3 parameters at least. Besides `api_token` and `uid`, we also need an AI configuration in json format as the value of `jconfig`.

Assume we have an AI enlarger config file stored in `Downloads` folder, so the full path is `/Users/vanceai/Downloads/enlarge.json`.

### Parameters

Field | Type | Description
----- | ---- | -----------
api_token | string | Your API token
uid | string | ID of uploaded file
jconfig | string | Transform parameter in json format
webhook | string | [Optional]The callback url to receive the transfrom result notification.

### Status of job processing

You may get different job status from the transform response. These statuses indicate different processing results of your job.

Status | Description
------ | -----------
finish | The job has finished, now you can download the result with the `trans_id`
waiting | Your job is waiting to be assigned in the queue, all servers are busy, you may need to wait for a while.
fatal | Your job is failed, you can try again or contact us.
process | Your job is still being processed, please wait a secend.
webhook | You see this status, because you provided the webhook url, so it means the processing result will be sent via webhook.
busy | It means your job is in the queue, you may need to call "progress" to query the status in a fixed frequency, such as to query the status ever 2 seconds.

### Webhook

The `webhook` is an optional parameter, which is used to receive message of job processing result. To use webhook, you should set the `webhook` field to point to your webserver in our `transform` request body. This url can be http or https.

If you provide the callback url, then the `transform` will response immediately after calling instead of waiting for the process result.

You will get a `trans_id` from the `transform` response body. Once the job finished, we will send a POST request to the endpoint you specified with `webhook`. You can also query the progress with the `trans_id` by calling the `progress` endpoint.

Example: https://your.domain/path/to/webhook

Note: the protocol must be `https`.

<aside class="notice">
If your server responds with error (status code ≥ 400), we will continue to call your endpoint for 2 times.

If all 3 attampts are failed, then you will not receive any message from us. The only way to check the processing status is by calling <code>progress</code> endpoint.
</aside>

# Check the progress

> Request body

```shell
curl -X POST "https://api-service.vanceai.com/web_api/v1/progress" \
	   -d 'api_token=1234567890abcdefg' \
	   -d 'trans_id=6de4b562d1a01c3d2520608eae929646' \
```

```php
<?php
  $remoteFileUrl = 'https://api-service.vanceai.com/web_api/v1/progress?trans_id=fe854a3b20237f70d1efb757b6aa0cc4&api_token=1234567890abcdefg';

  $ch = curl_init();
  $timeout = 60;
  curl_setopt($ch, CURLOPT_URL, $remoteFileUrl);
  curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
  curl_setopt($ch, CURLOPT_CONNECTTIMEOUT, $timeout);
  $img = curl_exec($ch);
  curl_close($ch);
?>
```

```python
remoteFileUrl = 'https://api-service.vanceai.com/web_api/v1/progress?trans_id=fe854a3b20237f70d1efb757b6aa0cc4&api_token=1234567890abcdefg''
response = requests.get(remoteFileUrl)
r = response.json()
if r['code'] == 200:
    print('status:', r['data']['status'])
```

> Response Success Example:

```json
{
    "code": 200,
    "cscode": 200,
    "data": {
        "status": "finish", // waiting, finish, fatal, process
        "filesize": "16794935"
    },
    "ip": "54.103.172.45"
}
```

<p class="endpoint"><code>GET|POST</code> https://api-service.vanceai.com/web_api/v1/progress</p>

We recommend you to use this endpoint in two situations:

* If you didn't provide the `webhook` url, use `progress` to query the processing status when received the timeout response.

* If you've already provided the `webhook` url, use this endpoint to query the processing status when you can not get message from our server.

### Parameters

Field | Type | Description
----- | ---- | -----------
api_token | string | Your API token
trans_id | string | ID in transform response

# Download result

> Request body

```shell
curl -X POST "https://api-service.vanceai.com/web_api/v1/download" \
     -o /Users/vanceai/Downloads/enlarged-cat.jpg \
     -d 'api_token=1234567890abcdefg' \
     -d 'trans_id=6de4b562d1a01c3d2520608eae929646' \
```

```php
<?php
  $remoteFileUrl = 'https://api-service.vanceai.com/web_api/v1/download?trans_id=fe854a3b20237f70d1efb757b6aa0cc4&api_token=1234567890abcdefg';

  dst_path = '/path/to/save/demo.jpg';

  $ch = curl_init();
  $timeout = 60;
  curl_setopt($ch, CURLOPT_URL, $remoteFileUrl);
  curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
  curl_setopt($ch, CURLOPT_CONNECTTIMEOUT, $timeout);
  $img = curl_exec($ch);
  curl_close($ch);

  $fp2 = @fopen(dst_path, 'a');
  fwrite($fp2, $img);
  fclose($fp2);
?>
```

```python
remoteFileUrl = 'https://api-service.vanceai.com/web_api/v1/download?trans_id=fe854a3b20237f70d1efb757b6aa0cc4&api_token=1234567890abcdefg'
dst_path = '/path/to/save/demo.jpg'

response = requests.get(remoteFileUrl, stream=True)

f = open(dst_path, "wb")
for chunk in response.iter_content(chunk_size=512):
    if chunk:
        f.write(chunk)
f.close()
```

<p class="endpoint"><code>GET|POST</code> https://api-service.vanceai.com/web_api/v1/download</p>

Once the status of the job is `finish`, we should use `download` endpoint to save the result. 

Let download the result to the path of '/Users/vanceai/Downloads/' and named it as 'enlarged-cat.jpg'.

### Parameters

Field | Type | Description
----- | ---- | -----------
api_token | string | Your API token
trans_id | string | ID in transform response

### Comparison of before and after

<img src="/images/comparision.png" alt="AI enlarger Before and After comparision" width="100%" />

As you can see, the `cat.jpg` has been enlarged by 200%, the output dimension is 2400x2400px, but the quality is not lost, even better than before.

