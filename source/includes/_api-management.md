## API Management

### API Usage

#### Check credit balance

> Request body

```shell
curl -X GET "https://api-service.vanceai.com/web_api/v1/point?api_token=1234567890abcdefg" 
```

> Response Success Example

```json
{
    "code": 200,
    "cscode": 200,
    "data": {
        "max_num": "500.000", // Total Credits
        "used_num": "107.800" // Used Credits
    },
    "ip": "54.103.172.45"
}
```

<p class="endpoint"><code>GET</code> https://api-service.vanceai.com/web_api/v1/point</p>

You can use this endpoint to query your credit balance.

##### Parameters

Field | Type | Description
----- | ---- | -----------
api_token | string | Your API token

<aside class="notice">
Unused credits do not roll-over and will reset at every billing day.
</aside>

#### Credit usage history

> Request body

```shell
curl -X POST "https://api-service.vanceai.com/web_api/v1/points/record" \
     -d 'api_token=1234567890abcdefg' \
     -d 'page=1' \
     -d 'limit=20' \
```

> Response Success Example

```json
{
    "code": 200,
    "cscode": 200,
    "data": {
        "datas": [
            {
                "point_type": "download", // job or download
                "job": "denoise",         //job type
                "info": "denoise_SwJes_0.jpg.jpg", // Image name
                "point": "1.000",         // Credits cost for this job or download
                "balance": "392.200",     // Credit balance
                "created_at": "2021-12-14 04:36:18"
            }
        ],
        "current_page": 1,
        "last_page": 5, 
        "per_page": 20,
        "total": 100
    }
    "ip": "54.103.172.45"
}
```

<p class="endpoint"><code>GET|POST</code> https://api-service.vanceai.com/web_api/v1/points/record</p>

You can use this endpoint to query your credit usage history.

##### Parameters

Field | Type | Description
----- | ---- | -----------
api_token | string | Your API token
page | numeric | [Optional] The xth page of pagination. Default: 1
limit |	numeric | [Optional] The number of items to display per page. Default: 20

### Credit Costs

Each AI feature has its own value. Currently all of the features will cost 0.2 credits when the transform status is `finish`. 

Workflow will cost 0.2 x [steps] credits. If you have a 3-step workflow, then process one image will cost 0.6 credits.

Upload, processing failed or check progress don't charge credits.

Download the result will cost 1 credit, duplicate downloads won't charge credits.
