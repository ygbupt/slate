# Errors

> Response Error Example

```json
{
    "code": 10001, 
    "msg": "Illegal Parameter"
}
```

> OR

```json
{
    "code": 30001, 
    "msg": {    
           "api_token": [
                 "The api token field is required."
            ]
     }
}
```

The VanceAI API uses the following error codes:


Error Code | Meaning
---------- | -------
10001 | Illegal parameter
10010 | Internal error
10011 | File doesn't exist OR Invalid download link
10012 | Resolution or file size exceeds the limits.
10013 | jparam parse error
10014 | Job failed and exited for unexpected reason
30001 | Invalid API token
30004 | Insufficient credits
