# How to change a query string to body in Azure API Management

Sometimes an API is exposed in a way that may not conform to current standards. API Management allows for exposing and API differently than it is implemented on the back-end. I needed to take a query string integer list in a GET request and ship it out in the body of a POST request.

# API Management Setup

API managment had a query string parameter called ids (which is a comma-separated list of integers) and another list called other-ids of the same type, names have been changed (duh). 

In the inbound policy area of the code editor I added ```<set-method></set-method>``` to switch to a POST and   ```<set-body></set-body>``` to pull the query string into the body.

```xml
<policies>
    <inbound>
        <!-- items removed for brevity -->
        <set-method id="apim-generated-policy">POST</set-method>
        <set-body>
        {
            "ids": [{{context.Request.Url.Query['ids'] | split: ","}}],
            "otherIds": [{{context.Request.Url.Query['other-ids'] | split: ","}}]
        }
        </set-body>
    </inbound>
</policies>
```