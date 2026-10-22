# Errors

<aside class="notice">
In addition to the HTTP status, each response provides an EPP "code" attribute and a human-readable "message". The tables below distinguish HTTP statuses from automatic-renewal result codes.
</aside>

The REPP API uses the following error codes:


Error Code | Meaning
---------- | -------
400 | Bad Request -- Your request is invalid.
401 | Unauthorized -- Your API key is wrong.
403 | Forbidden -- The requested object is hidden for administrators only.
404 | Not Found -- The specified object could not be found.
405 | Method Not Allowed -- You tried to access a object with an invalid method.
406 | Not Acceptable -- You requested a format that isn't json.
410 | Gone -- The requested object has been removed from our servers.
418 | I'm a teapot.
429 | Too Many Requests -- You're requesting too many objects! Slow down!
500 | Internal Server Error -- We had a problem with our server. Try again later.
503 | Service Unavailable -- We're temporarily offline for maintenance. Please try again later.

## Automatic renewal result codes

These are EPP result codes in the response body, not HTTP statuses. They apply to automatic-renewal parameters on domain create/update and to the dedicated automatic-renewal endpoint.

Code | Meaning in automatic-renewal commands
---- | -----------------------------------
2005 | Invalid boolean. REPP accepts only `true`, `false`, `"true"`, and `"false"`.
2201 | Authorization error: only the sponsoring registrar may manage the preference or reverse a cycle.
2303 | The cycle ID is unknown or belongs to another domain.
2304 | The cycle cannot be reversed: it is not in paid grace (including after a completed registrant change or transfer), or the cancellation deadline has been reached. An already reversed cycle instead returns idempotent success.
2306 | Invalid parameter combination or unavailable operation: automatic renewal is not available when enabling, enabling was combined with a reversal cycle ID, or a reversal was requested on domain create.

A failed reversal leaves the preference, expiry and account balance unchanged. If reversal is part of a domain update, the entire update rolls back. The cancellation deadline is exclusive: a new reversal at or after that timestamp fails with code `2304`. Renewal retries occur once a day and stop at this deadline. An unpaid cycle then becomes terminal state `failed` and sends exactly one `renewalFailed` poll message; the preference remains enabled for future expiries. A later top-up or re-enable does not reopen that cycle, and enabling at or after the deadline does not renew the current expiry.
