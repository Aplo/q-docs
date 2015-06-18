# Errors

```json
{
"status": "error",
"message": "Permission denied"
}
```

Every request to Q will have a `status` key in the response body. All requests that generate a `200` status code will have the `status` set to `ok`. __When an error occurs__ there will be an additional property set on the response body called `message` which will detail what the cause of the error is.

<aside class="notice">
  Users often ask if Q will alert them to an invalid signature. It's near impossible to tell what the intent of a user was when constructing a request signature therefore Q will only throw 401 errors or 403 errors when your client ID is invalid or your request signature is invalid respectively.
</aside>

The Q API uses the following error codes:


Error Code | Message | Meaning
---------- | ------- | -------
400 | Your request was malformed | Malformed request. Usually caused by invalid JSON
401 | Authentication failure | API key is missing
403 | Permission denied | API key or signature is incorrect. (Pro tip: This may be caused by missing/incorrect Content-Length or Content-Type headers)
404 | Resource not found | If you don't know what a 404 means... (Hint: Incorrect URL)
500 | Unexpected error encountered | We had a problem with our server. Try again later or differently. Please report these if you get them.
503 | Service Unavailable | We're temporarially offline for maintanance. Please try again later. Rarely happens.
