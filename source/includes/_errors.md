# Errors

DUPR uses conventional HTTP response codes to indicate the success or failure of an API request. In general: Codes in the <code>2xx</code> range indicate success. Codes in the <code>4xx</code> range indicate an error that failed given the information provided (e.g., a required parameter was omitted, a charge failed, etc.). Codes in the <code>5xx</code> range indicate an error with DUPR's servers (these are rare).

Some 4xx errors that could be handled programmatically (e.g., a token is expired).

The DUPR API uses the following error codes:

Error Code | Meaning
---------- | -------
400 | Bad Request -- Your request is invalid.
401 | Unauthorized -- Your API credentials are wrong or expired.
403 | Forbidden -- The requested is hidden for administrators only.
404 | Not Found -- The specified resource could not be found.
405 | Method Not Allowed -- You tried to access a resource with an invalid method.
406 | Not Acceptable -- You requested a format that isn't json.
410 | Gone -- The resource requested has been removed from our servers.
429 | Too Many Requests -- You're requesting too many resources! Slow down!
500 | Internal Server Error -- We had a problem with our server. Try again later.
503 | Service Unavailable -- We're temporarily offline for maintenance. Please try again later.
