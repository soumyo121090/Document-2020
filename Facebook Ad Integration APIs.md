# Facebook Ad Integration APIs

## Overview

**Rebid** platform uses  Business Management APIs to create and maintain a business' organic and paid presence on Facebook. Facebook Business Manager API enable **Rebid** to connect business assets to facilitate permission management, create ad accounts and assign credit to buy ads on behalf of another company.

In this document, you can find a list of Facebook APIs that integrate with **Rebid**.

## Get Access Token
An `access_token` is an opaque string that identifies a user, app, or Page and can be used by the app to make graph API calls. When someone connects with an app using Facebook Login and approves the request for permissions, the app obtains an access token that provides temporary, secure access to Facebook APIs. Access tokens are obtained via a number of methods.

To generate an app access token, you need:

* The *App ID*

* The *App Secret*

**Request Body**
```
curl -X GET "https://graph.facebook.com/oauth/access_token
  ?client_id={your-app-id}
  &client_secret={your-app-secret}
  &grant_type=client_credentials"
  ```
  >This call will return an app access token which can be used in place of a user access token to make API calls as noted above.

## Claim Accounts

This API enables **Rebid** to manage ad accounts as a Business Manager with the "Admin" role.
To claim an ad account for your business, provide the ad account ID in the format `act_###`. Send a `POST`:

``` HTTP
curl \

"adaccount_id=act_<AD_ACCOUNT_ID>" \

"access_token=<ACCESS_TOKEN>" \

"https://graph.facebook.com/<API_VERSION>/<BUSINESS_ID>/owned_ad_accounts"
```
If you are an Admin of the ad account, Facebook immediately approve this claim request. Facebook returns `access_status`  set to  `CONFIRMED`.
## Create Ad Accounts
To use this API, a valid business and page setup is mandatory. If you have any incomplete ad accounts or pages unpublished by Facebook, your calls fail.

**Request Body**
```curl \

-F "name=MyAdAccount" \

-F "currency=USD" \

-F "timezone_id=1" \

-F "end_advertiser=<END_ADVERTISER_ID>" \

-F "media_agency=<MEDIA_AGENCY_ID>" \

-F "partner=NONE" \

-F "access_token=<ACCESS_TOKEN>" \

"https://graph.facebook.com/<API_VERSION>/<BUSINESS_ID>/adaccount"
```
**Available fields are**:

| Name | Description | Type | Notes|
| ------- | -------------- | ----|--|
|`name`|Name of the ad account|string|
|`timezone_id`|ID of the timezone|int|
|`currency`|Currency abbrevation used for this ad account|string|
|`partner`|Facebook Business Partner (FBP). Must be a Facebook Page Alias, Facebook Page ID or a Facebook App ID. If unavailable, use NONE or UNFOUND| long or string| Must be a Facebook Page Alias, Facebook Page ID or a Facebook app ID|
|`end_advertiser`|Entity the ads will target. Must be a Business ID. If unavailable, use NONE or UNFOUND| long or string| Must be a Business ID|
|`media_agency`| Agency; this could be your own business. Must be a Facebook Page Alias, Facebook Page ID or a Facebook App ID. If unavailable, use NONE or UNFOUND| long or string| Must be a Facebook Page Alias, Facebook Page ID or a Facebook app ID.|
|`invoice`| If a business has a Business Manager-Owned Normal Credit Line with Facebook, we attach the ad account to that credit line| boolean| set `invoice` to `true` to add existing credit line with new ad account|

> If your ad account doesn't have an advertiser, Media Agency, or Partner, specify `NONE`. If your ad account has an advertiser, Media Agency, or Partner, but they are not on Facebook as a Page or an app, specify `UNFOUND` .

## Campaigns

A campaign is the highest level organizational structure within an ad account and should represent a single objective for an advertiser, for example, to drive page post engagement. Setting the objective of the campaign enforces validation on any ads added to that campaign to ensure they also have the correct objective.

**Create a Campaign** 
```
curl -X POST \
  -F 'name="My First Campaign"' \
  -F 'objective="PAGE_LIKES"' \
  -F 'status="PAUSED"' \
  -F 'special_ad_categories=[]' \
  -F 'access_token=<ACCESS_TOKEN>' \
  https://graph.facebook.com/v14.0/act_<AD_ACCOUNT_ID>/campaigns
  ```
  **Get Campaign Details**
To read the campaigns associated with a specific account, you make a request to the connection of the ad account you are using. By default facebook only return campaigns whose status is `ACTIVE` or `PAUSED`, so you need to specify additional statuses if required:

```
curl -X GET \
  -d 'effective_status=[
       "ACTIVE",
       "PAUSED"
     ]' \
  -d 'fields="name,objective"' \
  -d 'access_token=<ACCESS_TOKEN>' \
  https://graph.facebook.com/v14.0/act_<AD_ACCOUNT_ID>/campaigns
  ```
**Description**

Name| Description|Note
|----|---|---|
|`name`| Unique name of the campaign| |
|`objective`|are actions you want people to make when they see your ad|Example Value: `PAGE_LIKES` or `POST_ENGAGEMENT`|
|status| Current status of the campaign | Possible Values: `ACTIVE`/`PAUSED`|

## Error Codes 

The following are error codes returned by the API:
Error Code| Description
---|---
`-`|Negative-value error codes are internal Facebook errors. Check  `error_subcode`  for the actual failure code.
|`1`|An unknown error occurred
|`1`, subcode  `99`|An unknown error occurred. This error may occur if you set  `level`  to  `adset`  but the correct value should be  `campaign`.
|`4`|Application request limit reached
|`10`|Application does not have permission for this action
|`17`|User request limit reached
|`100`|Invalid parameter
> To learn about more error codes, please go through [Error Codes](https://developers.facebook.com/docs/marketing-api/error-reference)





