# Migrate to Etsy’s Open API v3

We are working on exciting updates to Etsy’s Open API, and this guide is meant to provide existing API developers with predictability on launch and sunset timelines, as well as the information needed to migrate to the new version. **This guide assumes you have an existing application and API key registered with the v2 API.** If you don’t, [you can register here](https://www.etsy.com/developers/register).

## Purpose and benefits

Our new Open API will provide the following benefits over v2:

- More accurate and usable reference documentation
- Support for OAuth 2.0
- A more approachable and up-to-date data model for our endpoints
- Better developer support

## Launch stages

These stages in advance of the general release of Open API v3 and the sunset of v2 are meant to give you ample time to migrate, test, ask questions and give feedback about our new API.

  | v3 Alpha | v3 Beta | v3 General Release | v2 Sunset
-- | -- | -- | -- | --
Timeframe | Q1 2021 | Q2 2021 | Q3 2021 | Q4 2021
Functionality | Many endpoints are proposals, callable but not functional | API and docs are feature complete | Full production functionality and stability | Access to v2 endpoints is removed
Purpose | Validate feature and functionality requirements | Solicit feedback to surface bugs and improvements | Release for production use | Retire v2 and set deadline for migration to v3
Access | Documentation is public, endpoint access by approved developers | Any developer can opt-in to access new endpoints | Public | V2 endpoint calls will fail
Data model stability | Significant design changes likely | Design changes possible | Stable design |  

## New reference documentation

We have created a [new API reference documentation site](https://www.etsy.com/openapi/developers) that will replace the current v2 documentation. We designed it to be more usable, complete, and easy to navigate. It will also be easier for our team to keep up-to-date with the functionality of the endpoints themselves, so you can find the information you need quickly and trust that it’s accurate. If you have feedback or suggestions about our documentation, you can [open an issue in Github](https://github.com/etsy/open-api/issues/new/choose).

## Access the new endpoints

If you would like to make requests to the Open API v3 endpoints, please contact [developers@etsy.com](mailto:developers@etsy.com) and provide either your API key or the email address on file with your account, along with a [redirect URL for authentication](https://www.etsy.com/developers/documentation/getting_started/oauth2#section_redirect_urls).

## Give feedback

In addition to our [reference documentation](https://www.etsy.com/openapi/developers), we have published the details of our API specification publicly on Github in the [OpenAPI Specification (OAS) format](https://swagger.io/specification/). If you’d like to report an issue or provide feedback on the API design, please [open an issue in Github](https://github.com/etsy/open-api/issues/new/choose).

## FAQ

### How can I access the new API?

During the Alpha, only approved developers will have access and space is limited. You can reach out to [developers@etsy.com](mailto:developers@etsy.com) to apply for access during this period.

During the Beta, any developer will be able to opt-in to access the API. Details will be forthcoming as we approach that milestone.

### What happens if I don't migrate?

We’ll give you ample time, notice, and support to migrate your app to the new Open API v3. If your app is still making requests to v2 endpoints after the sunset date, those requests will start to fail, which could cause disruption to your application and users.

### How can I get help with my migration?

There’s a lot of helpful documentation available to you, including the [reference docs](https://www.etsy.com/openapi/developers) and this migration guide. If you have a suggestion for something you’d like to see in our new API or documentation, you can [open an issue on Github](https://github.com/etsy/open-api/issues/new/choose). If you suspect you’ve found a bug or are having trouble with your migration, you can contact us at [developers@etsy.com](mailto:developers@etsy.com).

### Are there any changes to API Rate Limits?

 **API rate limits in v3 are the same as v2. Similarly, negotiated rate limits from v2 will remain the same in v3 if you're using the same API Key as v2.** By default, using public (API key-based) authentication, clients are allowed 10,000 requests per 24-hour period, with a limit of 10 queries per second.  For more information on rate limits, see [here](https://www.etsy.com/developers/documentation/getting_started/api_basics#section_rate_limiting). 

### How can I stay informed of changes to the API?

We’ll be communicating often over the course of 2021 about updates, upcoming milestones, and new ways to give feedback and get involved. Make sure that the [contact information](https://www.etsy.com/your/account) on the Etsy account that owns your API application is up-to-date, and that you subscribe to the [Etsy API Google Group](https://groups.google.com/g/etsy-api-v2).

### How can I provide feedback on the API?

If you’d like to report an issue or provide feedback on the API design or documentation, please [open an issue in Github](https://github.com/etsy/open-api/issues/new/choose). Note that by submitting your feedback, we have the right to use it.

Legalese-wise, this means it’s considered non-confidential and non-proprietary to you, and you grant Etsy a non-exclusive, worldwide, royalty-free, irrevocable, sub-licensable, perpetual license to use and publish those ideas and materials for any purpose, without compensation to you.

### Is the OpenAPI open source?

Etsy’s OpenAPI is not open source, but we do want you to have access to the API design and documentation, and we welcome your feedback. Use of the API is subject to Etsy’s [API Terms of Use](https://www.etsy.com/legal/api), along with Etsy’s primary [Terms of Use](https://www.etsy.com/legal/terms-of-use/), [Privacy Policy](https://www.etsy.com/legal/privacy/), and other applicable House Rules.

## Summary of Changes

### Making requests and authenticating

Endpoints are accessible at URLs starting with `https://openapi.etsy.com/v3/application/`. Every request to a v3 endpoint must include an `x-api-key` header, with your API key as the value.

The v3 API supports a new authentication system via OAuth 2.0. Though you can currently make requests to v3 endpoints with your existing OAuth 1.0 tokens, we strongly encourage migrating to OAuth 2.0, as support for OAuth 1.0 tokens will end in when the v2 API is sunset.

You can read more about making requests and setting up your OAuth 2.0 authentication flow [here](https://www.etsy.com/developers/documentation/getting_started/oauth2).

### Pagination

The v3 API will only accept the `limit` and `offset` query parameters for pagination, and reject the `page` parameter that was commonly specified in many v2 endpoints. Also, there will be a maximum limit of 100 for performance reasons, and passing a higher value will force a limit of 100, instead of throwing an error.

### Responses

Responses are no longer universally wrapped in the same v2 standard structure. In v3, endpoints that return lists of resources contain a `count` and `results` key, indicating the number of results in the response and the results themselves.

**Example:**
```
{
     "count": integer,
     "results": [
         { result object }
     ],
}
```

### Standard request parameters

We no longer support the `fields` or `includes` query parameters to return partial resources or additional associated resources in responses. In some cases, we return associated resources by default in v3.

**Example:**

`/v2/listings/:listing_id/inventory?includes=ListingOfferings&fields=products,sku_on_property`

**Becomes:**

`/v3/application/listings/:listing_id/inventory`

In the response, there is a nested `offerings` key returning the ListingOfferings for this ListingInventory resource, and all ListingInventory fields are returned.

### Field visibility

We no longer support field-level visibility in the API. In v2, the same endpoint could return resources with different sets of fields based on which were public or private, but in v3, endpoints either require OAuth 2.0 credentials or only an API Key.

**Example (ListingInventory):**

Field | Visibility Level | Permission Scope
-- | -- | --
products | public | none
price_on_property | private | listings_r

**Becomes:**

The getListingInventory endpoint and therefore the ListingInventory resource and any of its fields cannot be accessed publicly, it requires an oauth2 token with the `listings_r` scope.

### Resource scopes

Instead of path-based scopes to filter lists of resources, we have converted these to query parameter-based filters.

**Example:**

`/v2/listings/active`

**Becomes:**

`/v3/application/listings?state=active`

### Parameter/data types

The parameter types supported in Open API v3 are largely the same as those listed in v2, but will often offer more specificity for their validation criteria in the endpoint documentation, if applicable.

**Example:**

Parameter | Definition
-- | --
lat | number <float> [ -90 .. 90 ]<br>Default: null
who_made<br>*required* | string<br>Enum: "i_did" "someone_else" "collective”

### Endpoints

#### FavoriteListing
Work is in progress to define a schema in v3 for this resource.

#### ShopReceiptShipment

*v2 Resource Name: <a href="https://www.etsy.com/developers/documentation/reference/receiptshipment">ReceiptShipment</a>*

There are no endpoints associated with this resource.

#### Payment

v2 Endpoint | v3 Endpoint | Notes
--- | --- | ---
<a href="https://www.etsy.com/developers/documentation/reference/payment#method_findpayment">findPayment</a> | <a href="https://www.etsy.com/openapi/developers#operation/getPayments">getPayments</a> |
<a href="https://www.etsy.com/developers/documentation/reference/payment#method_findpaymentforledgerentry">findPaymentForLedgerEntry</a> | *Retired* |
<a href="https://www.etsy.com/developers/documentation/reference/payment#method_findshoppaymentbyreceipt">findShopPaymentByReceipt</a> | <a href="https://www.etsy.com/openapi/developers#operation/getShopPaymentByReceiptId">getShopPaymentByReceiptId</a> |
<a href="https://www.etsy.com/developers/documentation/reference/payment#method_findpaymentforpaymentaccountledgerentry">findPaymentForPaymentAccountLedgerEntry</a> | <a href="https://www.etsy.com/openapi/developers#operation/getPaymentAccountLedgerEntryPayments">getPaymentAccountLedgerEntryPayments</a> |

#### ShopListingVariationImage

*v2 Resource Name: <a href="https://www.etsy.com/developers/documentation/reference/listingvariationimage">ListingVariationImage</a>*

v2 Endpoint | v3 Endpoint | Notes
--- | --- | ---
<a href="https://www.etsy.com/developers/documentation/reference/listingvariationimage#method_getvariationimages">getVariationImages</a> | <a href="https://www.etsy.com/openapi/developers#operation/getListingVariationImages">getListingVariationImages</a> |
<a href="https://www.etsy.com/developers/documentation/reference/listingvariationimage#method_updatevariationimages">updateVariationImages</a> | <a href="https://www.etsy.com/openapi/developers#operation/updateVariationImages">updateVariationImages</a> |

#### FeedbackInfo
Work is in progress to define a schema in v3 for this resource.

#### ShippingProfileUpgrade

*v2 Resource Name: <a href="https://www.etsy.com/developers/documentation/reference/shippingupgrade">ShippingUpgrade</a>*

v2 Endpoint | v3 Endpoint | Notes
--- | --- | ---
<a href="https://www.etsy.com/developers/documentation/reference/shippingupgrade#method_getlistingshippingupgrades">getListingShippingUpgrades</a> | *Retired* |
<a href="https://www.etsy.com/developers/documentation/reference/shippingupgrade#method_createlistingshippingupgrade">createListingShippingUpgrade</a> | *Retired* |
<a href="https://www.etsy.com/developers/documentation/reference/shippingupgrade#method_updatelistingshippingupgrade">updateListingShippingUpgrade</a> | *Retired* |
<a href="https://www.etsy.com/developers/documentation/reference/shippingupgrade#method_deletelistingshippingupgrade">deleteListingShippingUpgrade</a> | *Retired* |
<a href="https://www.etsy.com/developers/documentation/reference/shippingupgrade#method_findallshippingtemplateupgrades">findAllShippingTemplateUpgrades</a> | <a href="https://www.etsy.com/openapi/developers#operation/getListingShippingProfileUpgrades">getListingShippingProfileUpgrades</a> |
<a href="https://www.etsy.com/developers/documentation/reference/shippingupgrade#method_createshippingtemplateupgrade">createShippingTemplateUpgrade</a> | <a href="https://www.etsy.com/openapi/developers#operation/createListingShippingProfileUpgrade">createListingShippingProfileUpgrade</a> |
<a href="https://www.etsy.com/developers/documentation/reference/shippingupgrade#method_updateshippingtemplateupgrade">updateShippingTemplateUpgrade</a> | *v3 Endpoint Schema TBD* |
<a href="https://www.etsy.com/developers/documentation/reference/shippingupgrade#method_deleteshippingtemplateupgrade">deleteShippingTemplateUpgrade</a> | *v3 Endpoint Schema TBD* |

#### User

*v2 Resource Name: <a href="https://www.etsy.com/developers/documentation/reference/userprofile">UserProfile</a>*

v2 Endpoint | v3 Endpoint | Notes
--- | --- | ---
<a href="https://www.etsy.com/developers/documentation/reference/userprofile#method_finduserprofile">findUserProfile</a> | *v3 Endpoint Schema TBD* |
<a href="https://www.etsy.com/developers/documentation/reference/userprofile#method_updateuserprofile">updateUserProfile</a> | *Retired* |

#### ShopSection

v2 Endpoint | v3 Endpoint | Notes
--- | --- | ---
<a href="https://www.etsy.com/developers/documentation/reference/shopsection#method_findallshopsections">findAllShopSections</a> | <a href="https://www.etsy.com/openapi/developers#operation/getShopSections">getShopSections</a> |
<a href="https://www.etsy.com/developers/documentation/reference/shopsection#method_createshopsection">createShopSection</a> | <a href="https://www.etsy.com/openapi/developers#operation/createShopSection">createShopSection</a> |
<a href="https://www.etsy.com/developers/documentation/reference/shopsection#method_getshopsection">getShopSection</a> | <a href="https://www.etsy.com/openapi/developers#operation/getShopSections">getShopSections</a> |
<a href="https://www.etsy.com/developers/documentation/reference/shopsection#method_updateshopsection">updateShopSection</a> | *v3 Endpoint Schema TBD* |
<a href="https://www.etsy.com/developers/documentation/reference/shopsection#method_deleteshopsection">deleteShopSection</a> | *v3 Endpoint Schema TBD* |

#### ShopListingFile

*v2 Resource Name: <a href="https://www.etsy.com/developers/documentation/reference/listingfile">ListingFile</a>*

v2 Endpoint | v3 Endpoint | Notes
--- | --- | ---
<a href="https://www.etsy.com/developers/documentation/reference/listingfile#method_findalllistingfiles">findAllListingFiles</a> | <a href="https://www.etsy.com/openapi/developers#operation/getAllListingFiles">getAllListingFiles</a> |
<a href="https://www.etsy.com/developers/documentation/reference/listingfile#method_uploadlistingfile">uploadListingFile</a> | <a href="https://www.etsy.com/openapi/developers#operation/uploadListingFile">uploadListingFile</a> |
<a href="https://www.etsy.com/developers/documentation/reference/listingfile#method_findlistingfile">findListingFile</a> | <a href="https://www.etsy.com/openapi/developers#operation/getListingFile">getListingFile</a> |
<a href="https://www.etsy.com/developers/documentation/reference/listingfile#method_deletelistingfile">deleteListingFile</a> | <a href="https://www.etsy.com/openapi/developers#operation/deleteListingFile">deleteListingFile</a> |

#### PaymentAdjustment

In v3, adjustments are returned by default in Payment responses.

v2 Endpoint | v3 Endpoint | Notes
--- | --- | ---
<a href="https://www.etsy.com/developers/documentation/reference/paymentadjustment#method_findpaymentadjustments">findPaymentAdjustments</a> | *Retired* |
<a href="https://www.etsy.com/developers/documentation/reference/paymentadjustment#method_findpaymentadjustment">findPaymentAdjustment</a> | *Retired* |
<a href="https://www.etsy.com/developers/documentation/reference/paymentadjustment#method_findpaymentadjustmentforledgerentry">findPaymentAdjustmentForLedgerEntry</a> | *Retired* |
<a href="https://www.etsy.com/developers/documentation/reference/paymentadjustment#method_findpaymentadjustmentforpaymentaccountledgerentry">findPaymentAdjustmentForPaymentAccountLedgerEntry</a> | *Retired* |

#### User

v2 Endpoint | v3 Endpoint | Notes
--- | --- | ---
<a href="https://www.etsy.com/developers/documentation/reference/user#method_findallusers">findAllUsers</a> | *Retired* |
<a href="https://www.etsy.com/developers/documentation/reference/user#method_getuser">getUser</a> | <a href="https://www.etsy.com/openapi/developers#operation/getUser">getUser</a> |
<a href="https://www.etsy.com/developers/documentation/reference/user#method_findallusersforteam">findAllUsersForTeam</a> | *Retired* |
<a href="https://www.etsy.com/developers/documentation/reference/user#method_getcirclescontaininguser">getCirclesContainingUser</a> | *Retired* |
<a href="https://www.etsy.com/developers/documentation/reference/user#method_getconnecteduser">getConnectedUser</a> | *Retired* |
<a href="https://www.etsy.com/developers/documentation/reference/user#method_unconnectusers">unconnectUsers</a> | *Retired* |
<a href="https://www.etsy.com/developers/documentation/reference/user#method_getconnectedusers">getConnectedUsers</a> | *Retired* |
<a href="https://www.etsy.com/developers/documentation/reference/user#method_connectusers">connectUsers</a> | *Retired* |

#### ListingPropertyValue

*v2 Resource Name: <a href="https://www.etsy.com/developers/documentation/reference/propertyvalue">PropertyValue</a>*

v2 Endpoint | v3 Endpoint | Notes
--- | --- | ---
<a href="https://www.etsy.com/developers/documentation/reference/propertyvalue#method_getattributes">getAttributes</a> | <a href="https://www.etsy.com/openapi/developers#operation/getListingProperties">getListingProperties</a> |
<a href="https://www.etsy.com/developers/documentation/reference/propertyvalue#method_getattribute">getAttribute</a> | *v3 Endpoint Schema TBD* |
<a href="https://www.etsy.com/developers/documentation/reference/propertyvalue#method_updateattribute">updateAttribute</a> | <a href="https://www.etsy.com/openapi/developers#operation/updateListingProperty">updateListingProperty</a> |
<a href="https://www.etsy.com/developers/documentation/reference/propertyvalue#method_deleteattribute">deleteAttribute</a> | *v3 Endpoint Schema TBD* |

#### ShopReceipt

*v2 Resource Name: <a href="https://www.etsy.com/developers/documentation/reference/receipt">Receipt</a>*

v2 Endpoint | v3 Endpoint | Notes
--- | --- | ---
<a href="https://www.etsy.com/developers/documentation/reference/receipt#method_getshop_receipt2">getShop_Receipt2</a> | <a href="https://www.etsy.com/openapi/developers#operation/getShopReceipt">getShopReceipt</a> |
<a href="https://www.etsy.com/developers/documentation/reference/receipt#method_updatereceipt">updateReceipt</a> | <a href="https://www.etsy.com/openapi/developers#operation/createReceiptShipment">createReceiptShipment</a> |
<a href="https://www.etsy.com/developers/documentation/reference/receipt#method_findallshopreceipts">findAllShopReceipts</a> | <a href="https://www.etsy.com/openapi/developers#operation/getShopReceipts">getShopReceipts</a> |
<a href="https://www.etsy.com/developers/documentation/reference/receipt#method_submittracking">submitTracking</a> | <a href="https://www.etsy.com/openapi/developers#operation/createReceiptShipment">createReceiptShipment</a> |
<a href="https://www.etsy.com/developers/documentation/reference/receipt#method_findallshopreceiptsbystatus">findAllShopReceiptsByStatus</a> | <a href="https://www.etsy.com/openapi/developers#operation/getShopReceipts">getShopReceipts</a> |
<a href="https://www.etsy.com/developers/documentation/reference/receipt#method_searchallshopreceipts">searchAllShopReceipts</a> | *Retired* |
<a href="https://www.etsy.com/developers/documentation/reference/receipt#method_findalluserbuyerreceipts">findAllUserBuyerReceipts</a> | *Retired* |

#### ShopTransaction

*v2 Resource Name: <a href="https://www.etsy.com/developers/documentation/reference/transaction">Transaction</a>*

v2 Endpoint | v3 Endpoint | Notes
--- | --- | ---
<a href="https://www.etsy.com/developers/documentation/reference/transaction#method_getshop_transaction">getShop_Transaction</a> | <a href="https://www.etsy.com/openapi/developers#operation/getShopReceiptTransaction">getShopReceiptTransaction</a> |
<a href="https://www.etsy.com/developers/documentation/reference/transaction#method_findalllistingtransactions">findAllListingTransactions</a> | <a href="https://www.etsy.com/openapi/developers#operation/getShopReceiptTransactionsByListing">getShopReceiptTransactionsByListing</a> |
<a href="https://www.etsy.com/developers/documentation/reference/transaction#method_findallshop_receipt2transactions">findAllShop_Receipt2Transactions</a> | <a href="https://www.etsy.com/openapi/developers#operation/getShopReceiptTransactionsByReceipt">getShopReceiptTransactionsByReceipt</a> |
<a href="https://www.etsy.com/developers/documentation/reference/transaction#method_findallshoptransactions">findAllShopTransactions</a> | <a href="https://www.etsy.com/openapi/developers#operation/getShopReceiptTransactionsByShop">getShopReceiptTransactionsByShop</a> |
<a href="https://www.etsy.com/developers/documentation/reference/transaction#method_findalluserbuyertransactions">findAllUserBuyerTransactions</a> | *Retired* |

#### ListingTranslation

v2 Endpoint | v3 Endpoint | Notes
--- | --- | ---
<a href="https://www.etsy.com/developers/documentation/reference/listingtranslation#method_getlistingtranslation">getListingTranslation</a> | <a href="https://www.etsy.com/openapi/developers#operation/getListingTranslation">getListingTranslation</a> |
<a href="https://www.etsy.com/developers/documentation/reference/listingtranslation#method_createlistingtranslation">createListingTranslation</a> | <a href="https://www.etsy.com/openapi/developers#operation/createListingTranslation">createListingTranslation</a> |
<a href="https://www.etsy.com/developers/documentation/reference/listingtranslation#method_updatelistingtranslation">updateListingTranslation</a> | <a href="https://www.etsy.com/openapi/developers#operation/updateListingTranslation">updateListingTranslation</a> |
<a href="https://www.etsy.com/developers/documentation/reference/listingtranslation#method_deletelistingtranslation">deleteListingTranslation</a> | *Retired* |

#### ListingStyle

*v2 Resource Name: <a href="https://www.etsy.com/developers/documentation/reference/style">Style</a>*

v2 Endpoint | v3 Endpoint | Notes
--- | --- | ---
<a href="https://www.etsy.com/developers/documentation/reference/style#method_findsuggestedstyles">findSuggestedStyles</a> | *v3 Endpoint Schema TBD* |

#### ShopListingInventory

*v2 Resource Name: <a href="https://www.etsy.com/developers/documentation/reference/listinginventory">ListingInventory</a>*

v2 Endpoint | v3 Endpoint | Notes
--- | --- | ---
<a href="https://www.etsy.com/developers/documentation/reference/listinginventory#method_getinventory">getInventory</a> | <a href="https://www.etsy.com/openapi/developers#operation/getListingInventory">getListingInventory</a> |
<a href="https://www.etsy.com/developers/documentation/reference/listinginventory#method_updateinventory">updateInventory</a> | <a href="https://www.etsy.com/openapi/developers#operation/updateListingInventory">updateListingInventory</a> |

#### ShopListingProduct

*v2 Resource Name: <a href="https://www.etsy.com/developers/documentation/reference/listingproduct">ListingProduct</a>*

v2 Endpoint | v3 Endpoint | Notes
--- | --- | ---
<a href="https://www.etsy.com/developers/documentation/reference/listingproduct#method_getproduct">getProduct</a> | <a href="https://www.etsy.com/openapi/developers#operation/getListingProduct">getListingProduct</a> |

#### Shop

v2 Endpoint | v3 Endpoint | Notes
--- | --- | ---
<a href="https://www.etsy.com/developers/documentation/reference/shop#method_findallshops">findAllShops</a> | <a href="https://www.etsy.com/openapi/developers#operation/getShops">getShops</a> |
<a href="https://www.etsy.com/developers/documentation/reference/shop#method_getshop">getShop</a> | <a href="https://www.etsy.com/openapi/developers#operation/getShop">getShop</a> |
<a href="https://www.etsy.com/developers/documentation/reference/shop#method_updateshop">updateShop</a> | <a href="https://www.etsy.com/openapi/developers#operation/updateShop">updateShop</a> |
<a href="https://www.etsy.com/developers/documentation/reference/shop#method_uploadshopbanner">uploadShopBanner</a> | *Retired* |
<a href="https://www.etsy.com/developers/documentation/reference/shop#method_deleteshopbanner">deleteShopBanner</a> | *Retired* |
<a href="https://www.etsy.com/developers/documentation/reference/shop#method_getlistingshop">getListingShop</a> | *Retired* |
<a href="https://www.etsy.com/developers/documentation/reference/shop#method_findallusershops">findAllUserShops</a> | <a href="https://www.etsy.com/openapi/developers#operation/getShopByOwnerUserId">getShopByOwnerUserId</a> |

#### FavoriteUser
Work is in progress to define a schema in v3 for this resource.

#### UserAddress

v2 Endpoint | v3 Endpoint | Notes
--- | --- | ---
<a href="https://www.etsy.com/developers/documentation/reference/useraddress#method_findalluseraddresses">findAllUserAddresses</a> | <a href="https://www.etsy.com/openapi/developers#operation/getUserAddresses">getUserAddresses</a> |
<a href="https://www.etsy.com/developers/documentation/reference/useraddress#method_createuseraddress">createUserAddress</a> | *v3 Endpoint Schema TBD* |
<a href="https://www.etsy.com/developers/documentation/reference/useraddress#method_getuseraddress">getUserAddress</a> | *v3 Endpoint Schema TBD* |
<a href="https://www.etsy.com/developers/documentation/reference/useraddress#method_deleteuseraddress">deleteUserAddress</a> | *v3 Endpoint Schema TBD* |

#### PaymentAccountLedgerEntry

v2 Endpoint | v3 Endpoint | Notes
--- | --- | ---
<a href="https://www.etsy.com/developers/documentation/reference/paymentaccountledgerentry#method_findpaymentaccountentries">findPaymentAccountEntries</a> | <a href="https://www.etsy.com/openapi/developers#operation/getShopPaymentAccountLedgerEntries">getShopPaymentAccountLedgerEntries</a> |

#### ShopListing

*v2 Resource Name: <a href="https://www.etsy.com/developers/documentation/reference/listing">Listing</a>*

v2 Endpoint | v3 Endpoint | Notes
--- | --- | ---
<a href="https://www.etsy.com/developers/documentation/reference/listing#method_createlisting">createListing</a> | <a href="https://www.etsy.com/openapi/developers#operation/createListing">createListing</a> |
<a href="https://www.etsy.com/developers/documentation/reference/listing#method_findallfeaturedlistings">findAllFeaturedListings</a> | *Retired* |
<a href="https://www.etsy.com/developers/documentation/reference/listing#method_getlisting">getListing</a> | <a href="https://www.etsy.com/openapi/developers#operation/getListing">getListing</a> |
<a href="https://www.etsy.com/developers/documentation/reference/listing#method_updatelisting">updateListing</a> | <a href="https://www.etsy.com/openapi/developers#operation/updateListing">updateListing</a> |
<a href="https://www.etsy.com/developers/documentation/reference/listing#method_deletelisting">deleteListing</a> | <a href="https://www.etsy.com/openapi/developers#operation/deleteListing">deleteListing</a> |
<a href="https://www.etsy.com/developers/documentation/reference/listing#method_findalllistingactive">findAllListingActive</a> | <a href="https://www.etsy.com/openapi/developers#operation/findAllListingsActive">findAllListingsActive</a> |
<a href="https://www.etsy.com/developers/documentation/reference/listing#method_getinterestinglistings">getInterestingListings</a> | *Retired* |
<a href="https://www.etsy.com/developers/documentation/reference/listing#method_gettrendinglistings">getTrendingListings</a> | *Retired* |
<a href="https://www.etsy.com/developers/documentation/reference/listing#method_findalllistingsforfeaturedtreasuryid">findAllListingsForFeaturedTreasuryId</a> | *Retired* |
<a href="https://www.etsy.com/developers/documentation/reference/listing#method_findallactivelistingsforfeaturedtreasuryid">findAllActiveListingsForFeaturedTreasuryId</a> | *Retired* |
<a href="https://www.etsy.com/developers/documentation/reference/listing#method_findallcurrentfeaturedlistings">findAllCurrentFeaturedListings</a> | *Retired* |
<a href="https://www.etsy.com/developers/documentation/reference/listing#method_findallreceiptlistings">findAllReceiptListings</a> | *v3 Endpoint Schema TBD* |
<a href="https://www.etsy.com/developers/documentation/reference/listing#method_findallshoplistingsactive">findAllShopListingsActive</a> | <a href="https://www.etsy.com/openapi/developers#operation/getListings">getListings</a> |
<a href="https://www.etsy.com/developers/documentation/reference/listing#method_findallshoplistingsdraft">findAllShopListingsDraft</a> | <a href="https://www.etsy.com/openapi/developers#operation/getListings">getListings</a> |
<a href="https://www.etsy.com/developers/documentation/reference/listing#method_findallshoplistingsexpired">findAllShopListingsExpired</a> | <a href="https://www.etsy.com/openapi/developers#operation/getListings">getListings</a> |
<a href="https://www.etsy.com/developers/documentation/reference/listing#method_getshoplistingexpired">getShopListingExpired</a> | *Retired* |
<a href="https://www.etsy.com/developers/documentation/reference/listing#method_findallshoplistingsfeatured">findAllShopListingsFeatured</a> | *Retired* |
<a href="https://www.etsy.com/developers/documentation/reference/listing#method_findallshoplistingsinactive">findAllShopListingsInactive</a> | <a href="https://www.etsy.com/openapi/developers#operation/getListings">getListings</a> |
<a href="https://www.etsy.com/developers/documentation/reference/listing#method_getshoplistinginactive">getShopListingInactive</a> | *Retired* |
<a href="https://www.etsy.com/developers/documentation/reference/listing#method_findallshopsectionlistings">findAllShopSectionListings</a> | *Retired* |
<a href="https://www.etsy.com/developers/documentation/reference/listing#method_findallshopsectionlistingsactive">findAllShopSectionListingsActive</a> | <a href="https://www.etsy.com/openapi/developers#operation/getListingsByShopSectionId">getListingsByShopSectionId</a> |
<a href="https://www.etsy.com/developers/documentation/reference/listing#method_findallcartlistings">findAllCartListings</a> | *Retired* |

#### TaxonomyNodeProperty
Work is in progress to define a schema in v3 for this resource.

#### ShopListingImage

*v2 Resource Name: <a href="https://www.etsy.com/developers/documentation/reference/listingimage">ListingImage</a>*

v2 Endpoint | v3 Endpoint | Notes
--- | --- | ---
<a href="https://www.etsy.com/developers/documentation/reference/listingimage#method_findalllistingimages">findAllListingImages</a> | <a href="https://www.etsy.com/openapi/developers#operation/getListingImages">getListingImages</a> |
<a href="https://www.etsy.com/developers/documentation/reference/listingimage#method_uploadlistingimage">uploadListingImage</a> | <a href="https://www.etsy.com/openapi/developers#operation/uploadListingImage">uploadListingImage</a> |
<a href="https://www.etsy.com/developers/documentation/reference/listingimage#method_getimage_listing">getImage_Listing</a> | <a href="https://www.etsy.com/openapi/developers#operation/getListingImage">getListingImage</a> |
<a href="https://www.etsy.com/developers/documentation/reference/listingimage#method_deletelistingimage">deleteListingImage</a> | <a href="https://www.etsy.com/openapi/developers#operation/deleteListingImage">deleteListingImage</a> |

#### Taxonomy
Work is in progress to define a schema in v3 for this resource.

#### ShippingProfile

*v2 Resource Name: <a href="https://www.etsy.com/developers/documentation/reference/shippingtemplate">ShippingTemplate</a>*

v2 Endpoint | v3 Endpoint | Notes
--- | --- | ---
<a href="https://www.etsy.com/developers/documentation/reference/shippingtemplate#method_createshippingtemplate">createShippingTemplate</a> | <a href="https://www.etsy.com/openapi/developers#operation/createListingShippingProfile">createListingShippingProfile</a> |
<a href="https://www.etsy.com/developers/documentation/reference/shippingtemplate#method_getshippingtemplate">getShippingTemplate</a> | <a href="https://www.etsy.com/openapi/developers#operation/getListingShippingProfile">getListingShippingProfile</a> |
<a href="https://www.etsy.com/developers/documentation/reference/shippingtemplate#method_updateshippingtemplate">updateShippingTemplate</a> | <a href="https://www.etsy.com/openapi/developers#operation/updateListingShippingProfile">updateListingShippingProfile</a> |
<a href="https://www.etsy.com/developers/documentation/reference/shippingtemplate#method_deleteshippingtemplate">deleteShippingTemplate</a> | <a href="https://www.etsy.com/openapi/developers#operation/deleteListingShippingProfile">deleteListingShippingProfile</a> |
<a href="https://www.etsy.com/developers/documentation/reference/shippingtemplate#method_findallshippingtemplateentries">findAllShippingTemplateEntries</a> | <a href="https://www.etsy.com/openapi/developers#operation/getListingShippingProfileDestinationsByShippingProfile">getListingShippingProfileDestinationsByShippingProfile</a> |
<a href="https://www.etsy.com/developers/documentation/reference/shippingtemplate#method_findallusershippingprofiles">findAllUserShippingProfiles</a> | <a href="https://www.etsy.com/openapi/developers#operation/getListingShippingProfiles">getListingShippingProfiles</a> |

#### ShopListingOffering

*v2 Resource Name: <a href="https://www.etsy.com/developers/documentation/reference/listingoffering">ListingOffering</a>*

v2 Endpoint | v3 Endpoint | Notes
--- | --- | ---
<a href="https://www.etsy.com/developers/documentation/reference/listingoffering#method_getoffering">getOffering</a> | <a href="https://www.etsy.com/openapi/developers#operation/getListingOffering">getListingOffering</a> |

#### ShippingProfileDestination

*v2 Resource Name: <a href="https://www.etsy.com/developers/documentation/reference/shippingtemplateentry">ShippingTemplateEntry</a>*

v2 Endpoint | v3 Endpoint | Notes
--- | --- | ---
<a href="https://www.etsy.com/developers/documentation/reference/shippingtemplateentry#method_createshippingtemplateentry">createShippingTemplateEntry</a> | <a href="https://www.etsy.com/openapi/developers#operation/createListingShippingProfileDestination">createListingShippingProfileDestination</a> |
<a href="https://www.etsy.com/developers/documentation/reference/shippingtemplateentry#method_getshippingtemplateentry">getShippingTemplateEntry</a> | *Retired* |
<a href="https://www.etsy.com/developers/documentation/reference/shippingtemplateentry#method_updateshippingtemplateentry">updateShippingTemplateEntry</a> | <a href="https://www.etsy.com/openapi/developers#operation/updateListingShippingProfileDestination">updateListingShippingProfileDestination</a> |
<a href="https://www.etsy.com/developers/documentation/reference/shippingtemplateentry#method_deleteshippingtemplateentry">deleteShippingTemplateEntry</a> | <a href="https://www.etsy.com/openapi/developers#operation/deleteListingShippingProfileDestination">deleteListingShippingProfileDestination</a> |

#### Money

There are no endpoints associated with this resource.

#### UserFeedback

*v2 Resource Name: <a href="https://www.etsy.com/developers/documentation/reference/feedback">Feedback</a>*

v2 Endpoint | v3 Endpoint | Notes
--- | --- | ---
<a href="https://www.etsy.com/developers/documentation/reference/feedback#method_findalluserfeedbackasauthor">findAllUserFeedbackAsAuthor</a> | <a href="https://www.etsy.com/openapi/developers#operation/getMemberUserFeedback">getMemberUserFeedback</a><br><a href="https://www.etsy.com/openapi/developers#operation/getPublicUserFeedback">getPublicUserFeedback</a> |
<a href="https://www.etsy.com/developers/documentation/reference/feedback#method_findalluserfeedbackasbuyer">findAllUserFeedbackAsBuyer</a> | <a href="https://www.etsy.com/openapi/developers#operation/getMemberUserFeedback">getMemberUserFeedback</a><br><a href="https://www.etsy.com/openapi/developers#operation/getPublicUserFeedback">getPublicUserFeedback</a> |
<a href="https://www.etsy.com/developers/documentation/reference/feedback#method_findalluserfeedbackasseller">findAllUserFeedbackAsSeller</a> | <a href="https://www.etsy.com/openapi/developers#operation/getPublicUserFeedback">getPublicUserFeedback</a><br><a href="https://www.etsy.com/openapi/developers#operation/getShopUserFeedback">getShopUserFeedback</a> |
<a href="https://www.etsy.com/developers/documentation/reference/feedback#method_findalluserfeedbackassubject">findAllUserFeedbackAsSubject</a> | <a href="https://www.etsy.com/openapi/developers#operation/getShopUserFeedback">getShopUserFeedback</a><br><a href="https://www.etsy.com/openapi/developers#operation/getPublicUserFeedback">getPublicUserFeedback</a> |
<a href="https://www.etsy.com/developers/documentation/reference/feedback#method_findallfeedbackfrombuyers">findAllFeedbackFromBuyers</a> | <a href="https://www.etsy.com/openapi/developers#operation/getShopUserFeedback">getShopUserFeedback</a><br><a href="https://www.etsy.com/openapi/developers#operation/getPublicUserFeedback">getPublicUserFeedback</a> |
<a href="https://www.etsy.com/developers/documentation/reference/feedback#method_findallfeedbackfromsellers">findAllFeedbackFromSellers</a> | *Retired* |

### Retired Resources
We are not planning to represent these v2 resources and associated endpoints in Open API v3 at this time. If you have a concern about any of these, [let us know](https://github.com/etsy/open-api).

* <a href="https://www.etsy.com/developers/documentation/reference/treasury">Treasury</a> - This resource was deprecated in v2.
  * <a href="https://www.etsy.com/developers/documentation/reference/treasury#method_findalltreasuries">findAllTreasuries</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/treasury#method_gettreasury">getTreasury</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/treasury#method_deletetreasury">deleteTreasury</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/treasury#method_findallusertreasuries">findAllUserTreasuries</a>
* <a href="https://www.etsy.com/developers/documentation/reference/team">Team</a> - This resource was deprecated in v2.
  * <a href="https://www.etsy.com/developers/documentation/reference/team#method_findallteams">findAllTeams</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/team#method_findteams">findTeams</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/team#method_findallteamsforuser">findAllTeamsForUser</a>
* <a href="https://www.etsy.com/developers/documentation/reference/datatype">DataType</a> - Enum definitions are generated dynamically in v3 documentation.
  * <a href="https://www.etsy.com/developers/documentation/reference/datatype#method_describeoccasionenum">describeOccasionEnum</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/datatype#method_describerecipientenum">describeRecipientEnum</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/datatype#method_describewhenmadeenum">describeWhenMadeEnum</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/datatype#method_describewhomadeenum">describeWhoMadeEnum</a>
* <a href="https://www.etsy.com/developers/documentation/reference/ledger">Ledger</a> - You can retrieve a shop's ledger_id if needed using an endpoint that returns a PaymentAccountLedgerEntry.
  * <a href="https://www.etsy.com/developers/documentation/reference/ledger#method_findledger">findLedger</a>
* <a href="https://www.etsy.com/developers/documentation/reference/paramlist">ParamList</a> - Parameter types are generated dynamically in v3 documentation.
* <a href="https://www.etsy.com/developers/documentation/reference/ledgerentry">LedgerEntry</a> - Deprecated on etsy.com. Use PaymentAccountLedgerEntry.
  * <a href="https://www.etsy.com/developers/documentation/reference/ledgerentry#method_findledgerentries">findLedgerEntries</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/ledgerentry#method_findledgerentry">findLedgerEntry</a>
* <a href="https://www.etsy.com/developers/documentation/reference/region">Region</a> - ShippingProfileDestinations only support countries in v3.
  * <a href="https://www.etsy.com/developers/documentation/reference/region#method_findallregion">findAllRegion</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/region#method_getregion">getRegion</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/region#method_findeligibleregions">findEligibleRegions</a>
* <a href="https://www.etsy.com/developers/documentation/reference/taxonomypropertyvalue">TaxonomyPropertyValue</a>
* <a href="https://www.etsy.com/developers/documentation/reference/shopsectiontranslation">ShopSectionTranslation</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/shopsectiontranslation#method_getshopsectiontranslation">getShopSectionTranslation</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/shopsectiontranslation#method_createshopsectiontranslation">createShopSectionTranslation</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/shopsectiontranslation#method_updateshopsectiontranslation">updateShopSectionTranslation</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/shopsectiontranslation#method_deleteshopsectiontranslation">deleteShopSectionTranslation</a>
* <a href="https://www.etsy.com/developers/documentation/reference/cart">Cart</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/cart#method_getallusercarts">getAllUserCarts</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/cart#method_addtocart">addToCart</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/cart#method_updatecartlistingquantity">updateCartListingQuantity</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/cart#method_removecartlisting">removeCartListing</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/cart#method_getusercart">getUserCart</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/cart#method_updatecart">updateCart</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/cart#method_deletecart">deleteCart</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/cart#method_addandselectshippingforapplepay">addAndSelectShippingForApplePay</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/cart#method_savelistingforlater">saveListingForLater</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/cart#method_getusercartforshop">getUserCartForShop</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/cart#method_createsinglelistingcart">createSingleListingCart</a>
* <a href="https://www.etsy.com/developers/documentation/reference/guest">Guest</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/guest#method_getguest">getGuest</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/guest#method_generateguest">generateGuest</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/guest#method_claimguest">claimGuest</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/guest#method_mergeguest">mergeGuest</a>
* <a href="https://www.etsy.com/developers/documentation/reference/featuredtreasury">FeaturedTreasury</a> - This resource was deprecated in v2.
  * <a href="https://www.etsy.com/developers/documentation/reference/featuredtreasury#method_findallfeaturedtreasuries">findAllFeaturedTreasuries</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/featuredtreasury#method_getfeaturedtreasurybyid">getFeaturedTreasuryById</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/featuredtreasury#method_findallfeaturedtreasuriesbyowner">findAllFeaturedTreasuriesByOwner</a>
* <a href="https://www.etsy.com/developers/documentation/reference/cartlisting">CartListing</a>
* <a href="https://www.etsy.com/developers/documentation/reference/country">Country</a> - We're using ISO country code instead of the Country resource in v3
  * <a href="https://www.etsy.com/developers/documentation/reference/country#method_findallcountry">findAllCountry</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/country#method_getcountry">getCountry</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/country#method_findbyisocode">findByIsoCode</a>
* <a href="https://www.etsy.com/developers/documentation/reference/billingoverview">BillingOverview</a> - This resource was deprecated in v2.
  * <a href="https://www.etsy.com/developers/documentation/reference/billingoverview#method_getuserbillingoverview">getUserBillingOverview</a>
* <a href="https://www.etsy.com/developers/documentation/reference/shippingoption">ShippingOption</a>
* <a href="https://www.etsy.com/developers/documentation/reference/paymenttemplate">PaymentTemplate</a> - Deprecated on etsy.com
  * <a href="https://www.etsy.com/developers/documentation/reference/paymenttemplate#method_findshoppaymenttemplates">findShopPaymentTemplates</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/paymenttemplate#method_createshoppaymenttemplate">createShopPaymentTemplate</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/paymenttemplate#method_updateshoppaymenttemplate">updateShopPaymentTemplate</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/paymenttemplate#method_findalluserpaymenttemplates">findAllUserPaymentTemplates</a>
* <a href="https://www.etsy.com/developers/documentation/reference/shopabout">ShopAbout</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/shopabout#method_getshopabout">getShopAbout</a>
* <a href="https://www.etsy.com/developers/documentation/reference/forumpost">ForumPost</a> - This resource was deprecated in v2.
  * <a href="https://www.etsy.com/developers/documentation/reference/forumpost#method_findtreasurycomments">findTreasuryComments</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/forumpost#method_posttreasurycomment">postTreasuryComment</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/forumpost#method_deletetreasurycomment">deleteTreasuryComment</a>
* <a href="https://www.etsy.com/developers/documentation/reference/structuredpolicies">StructuredPolicies</a>
* <a href="https://www.etsy.com/developers/documentation/reference/taxonomypropertyscale">TaxonomyPropertyScale</a>
* <a href="https://www.etsy.com/developers/documentation/reference/coupon">Coupon</a> - Coupons have been replaced by SellerMarketingPromotions
  * <a href="https://www.etsy.com/developers/documentation/reference/coupon#method_findallshopcoupons">findAllShopCoupons</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/coupon#method_createcoupon">createCoupon</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/coupon#method_findcoupon">findCoupon</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/coupon#method_updatecoupon">updateCoupon</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/coupon#method_deletecoupon">deleteCoupon</a>
* <a href="https://www.etsy.com/developers/documentation/reference/avatar">Avatar</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/avatar#method_uploadavatar">uploadAvatar</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/avatar#method_getavatarimgsrc">getAvatarImgSrc</a>
* <a href="https://www.etsy.com/developers/documentation/reference/apimethod">ApiMethod</a> - This resource was deprecated in v2.
  * <a href="https://www.etsy.com/developers/documentation/reference/apimethod#method_getmethodtable">getMethodTable</a>
* <a href="https://www.etsy.com/developers/documentation/reference/shopaboutmember">ShopAboutMember</a>
* <a href="https://www.etsy.com/developers/documentation/reference/shoptranslation">ShopTranslation</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/shoptranslation#method_getshoptranslation">getShopTranslation</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/shoptranslation#method_createshoptranslation">createShopTranslation</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/shoptranslation#method_updateshoptranslation">updateShopTranslation</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/shoptranslation#method_deleteshoptranslation">deleteShopTranslation</a>
* <a href="https://www.etsy.com/developers/documentation/reference/treasurycounts">TreasuryCounts</a> - This resource was deprecated in v2.
* <a href="https://www.etsy.com/developers/documentation/reference/paymentadjustmentitem">PaymentAdjustmentItem</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/paymentadjustmentitem#method_findpaymentadjustmentitems">findPaymentAdjustmentItems</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/paymentadjustmentitem#method_findpaymentadjustmentitem">findPaymentAdjustmentItem</a>
* <a href="https://www.etsy.com/developers/documentation/reference/shopaboutimage">ShopAboutImage</a>
* <a href="https://www.etsy.com/developers/documentation/reference/billcharge">BillCharge</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/billcharge#method_getuserchargesmetadata">getUserChargesMetadata</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/billcharge#method_findallusercharges">findAllUserCharges</a>
* <a href="https://www.etsy.com/developers/documentation/reference/treasurylisting">TreasuryListing</a> - This resource was deprecated in v2.
  * <a href="https://www.etsy.com/developers/documentation/reference/treasurylisting#method_addtreasurylisting">addTreasuryListing</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/treasurylisting#method_removetreasurylisting">removeTreasuryListing</a>
* <a href="https://www.etsy.com/developers/documentation/reference/billpayment">BillPayment</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/billpayment#method_findalluserpayments">findAllUserPayments</a>
* <a href="https://www.etsy.com/developers/documentation/reference/guestcart">GuestCart</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/guestcart#method_findallguestcarts">findAllGuestCarts</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/guestcart#method_addtoguestcart">addToGuestCart</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/guestcart#method_updateguestcartlistingquantity">updateGuestCartListingQuantity</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/guestcart#method_removeguestcartlisting">removeGuestCartListing</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/guestcart#method_findguestcart">findGuestCart</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/guestcart#method_updateguestcart">updateGuestCart</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/guestcart#method_deleteguestcart">deleteGuestCart</a>
* <a href="https://www.etsy.com/developers/documentation/reference/treasurylistingdata">TreasuryListingData</a> - This resource was deprecated in v2.
* <a href="https://www.etsy.com/developers/documentation/reference/shippinginfo">ShippingInfo</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/shippinginfo#method_createshippinginfo">createShippingInfo</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/shippinginfo#method_getshippinginfo">getShippingInfo</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/shippinginfo#method_updateshippinginfo">updateShippingInfo</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/shippinginfo#method_deleteshippinginfo">deleteShippingInfo</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/shippinginfo#method_findalllistingshippingprofileentries">findAllListingShippingProfileEntries</a> - ShippingProfileEntries are now accessed through ShippingProfiles. Use getListingShippingProfileDestinationsByShippingProfile.
* <a href="https://www.etsy.com/developers/documentation/reference/imagetype">ImageType</a>
  * <a href="https://www.etsy.com/developers/documentation/reference/imagetype#method_listimagetypes">listImageTypes</a>
