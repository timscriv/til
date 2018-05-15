# Microsoft.Web/certificates Another certificate exists with same thumbprint {0} at location {1} in the Resource Group {2}.

## tl;dr

Make the certificate in `rg-3` and you can access from both `rg-1` and `rg-2` rather than creating in `rg-1` and `rg-2`.

## Problem 

If you have a setup similar to the figure below and you are trying to add a certificate (i.e. *.example.com) into both Web App 1 and 2 you can end up with an error that looks like `Another certificate exists with same thumbprint xxxxxxxxx at location West US in the Resource Group rg-2`, but when you look in the Resource group no certificate exists.

```
  +----------------+  +----------------+
  |                |  |                |
  |  +---------+   |  | +---------+    |
  |  |Web App 1|   |  | |Web App 2|    |
  |  +---------+   |  | +---------+    |
  |         |      |  |    |           |
  +-------------rg-1  +-------------rg-2
            |            |
            |            |
            |            |
            |            |
  +------------------------------------+
  |         |            |             |
  |  +------v------+  +--v----------+  |
  |  |Server Farm 1|  |Server Farm 2|  |
  |  +-------------+  +-------------+  |
  |                                    |
  +---------------------------------rg-3
```

## Resolution

The problem actually does not stem from rg-2, like the error says, but actually from rg-3. When you add a Microsoft.Web/certificate to a Web App that has a server farm with the ARM template it is actually connecting it to the App Service Plan, so when you try to create the second one it is looking in `rg-3`, finding the certificate (by thumbprint) already exists and is throwing an error.

To fix this, simply move the certificate creation into `rg-3` and now both `rg-1` and `rg-2` can access the cert due to the server farm (App Service Plan)

