---
title: Terméksablonok az Azure API Management ben | Microsoft dokumentumok
description: Ismerje meg, hogyan szabhatja testre a terméklapok tartalmát az Azure API Management fejlesztői portálon.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 49f9254c-4c5f-4ed4-9c8d-798f44e805ee
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 393563427e936e07315cd44b78cb793d4292b352
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243925"
---
# <a name="product-templates-in-azure-api-management"></a>Terméksablonok az Azure API Management ben

Az Azure API Management lehetővé teszi a fejlesztői portállapok tartalmának testreszabását a tartalmukat konfiguráló sablonok használatával. A [DotLiquid](http://dotliquidmarkup.org/) szintaxis és az Ön által választott szerkesztő , például a [DotLiquid for Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), valamint a megadott honosított [karakterlánc-erőforrások,](api-management-template-resources.md#strings) [a karakterjel-erőforrások](api-management-template-resources.md#glyphs)és az [oldalvezérlők](api-management-page-controls.md)használatával nagy rugalmasságot biztosít az oldalak tartalmának beállításához, ahogy azt megfelelőnek látja ezekkel a sablonokkal.  
  
 Az ebben a szakaszban található sablonok lehetővé teszik a terméklapok tartalmának testreszabását a fejlesztői portálon.  
  
-   [Terméklista](#ProductList)  
  
-   [Termék](#Product)  
  
> [!NOTE]
>  A minta alapértelmezett sablonjai a következő dokumentációban találhatók, de a folyamatos fejlesztések miatt változhatnak. Az élő alapértelmezett sablonokat a fejlesztői portálon megtekintheti a kívánt egyedi sablonokra való navigálással. A sablonok használatáról további információt [az API Management fejlesztői portál testreszabása sablonok használatával című](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/)témakörben talál.  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="product-list"></a><a name="ProductList"></a>Terméklista  
 A **Terméklista** sablon lehetővé teszi a terméklista lap törzsének testreszabását a fejlesztői portálon.  
  
 ![Termékek listája](./media/api-management-product-templates/APIM_ProductsListTemplatePage.png "APIM_ProductsListTemplatePage")  
  
### <a name="default-template"></a>Alapértelmezett sablon  
  
```xml  
<search-control></search-control>  
<div class="row">  
    <div class="col-md-9">  
        <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
    </div>  
</div>  
<div class="row">  
    <div class="col-md-12">  
    {% if products.size > 0 %}  
    <ul class="list-unstyled">  
    {% for product in products %}  
        <li>  
            <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>  
            {{product.description}}  
        </li>     
    {% endfor %}  
    </ul>  
    <paging-control></paging-control>  
    {% else %}  
    {% localized "CommonResources|NoItemsToDisplay" %}  
    {% endif %}  
    </div>  
</div>  
```  
  
### <a name="controls"></a>Vezérlők  
 A `Product list` sablon a következő [lapvezérlőket használhatja](api-management-page-controls.md).  
  
-   [lapozás-vezérlés](api-management-page-controls.md#paging-control)  
  
-   [keresés-ellenőrzés](api-management-page-controls.md#search-control)  
  
### <a name="data-model"></a>Adatmodell  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Lapozás|[Személyhívó](api-management-template-data-model-reference.md#Paging) entitás.|A termékgyűjtés lapozási adatai.|  
|Szűrés|[Szűrő](api-management-template-data-model-reference.md#Filtering) entitás.|A terméklista oldalának szűrési információi.|  
|Termékek|[Termékentitások](api-management-template-data-model-reference.md#Product) gyűjteménye.|Az aktuális felhasználó számára látható termékek.|  
  
### <a name="sample-template-data"></a>Mintasablon adatai  
  
```json  
{  
    "Paging": {  
        "Page": 1,  
        "PageSize": 10,  
        "TotalItemCount": 2,  
        "ShowAll": false,  
        "PageCount": 1  
    },  
    "Filtering": {  
        "Pattern": null,  
        "Placeholder": "Search products"  
    },  
    "Products": [  
        {  
            "Id": "56f9445ffaf7560049060001",  
            "Title": "Starter",  
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
            "Terms": "",  
            "ProductState": 1,  
            "AllowMultipleSubscriptions": false,  
            "MultipleSubscriptionsCount": 1  
        },  
        {  
            "Id": "56f9445ffaf7560049060002",  
            "Title": "Unlimited",  
            "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",  
            "Terms": null,  
            "ProductState": 1,  
            "AllowMultipleSubscriptions": false,  
            "MultipleSubscriptionsCount": 1  
        }  
    ]  
}  
```  
  
##  <a name="product"></a><a name="Product"></a>Termék  
 A **Termék** sablon lehetővé teszi a terméklap törzsének testreszabását a fejlesztői portálon.  
  
 ![Fejlesztői portál terméklapja](./media/api-management-product-templates/APIM_ProductPage.png "APIM_ProductPage")  
  
### <a name="default-template"></a>Alapértelmezett sablon  
  
```xml  
<h2>{{Product.Title}}</h2>  
<p>{{Product.Description}}</p>  
  
{% assign replaceString0 = '{0}' %}  
  
{% if Limits and Limits.size > 0 %}  
<h3>{% localized "ProductDetailsStrings|WebProductsUsageLimitsHeader"%}</h3>  
<ul>  
  {% for limit in Limits %}  
  <li>{{limit.DisplayName}}</li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if apis.size > 0 %}  
<p>  
  <b>  
    {% if apis.size == 1 %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockSingleApisCount" %}{% endcapture %}  
    {% else %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockMultipleApisCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture apisCount %}{{apis.size}}{% endcapture %}  
    {{ apisCountText | replace : replaceString0, apisCount }}  
  </b>  
</p>  
  
<ul>  
  {% for api in Apis %}  
  <li>  
    <a href="/docs/services/{{api.Id}}">{{api.Name}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if subscriptions.size > 0 %}  
<p>  
  <b>  
    {% if subscriptions.size == 1 %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockSingleSubscriptionsCount" %}{% endcapture %}  
    {% else %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockMultipleSubscriptionsCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture subscriptionsCount %}{{subscriptions.size}}{% endcapture %}  
    {{ subscriptionsCountText | replace : replaceString0, subscriptionsCount }}  
  </b>  
</p>  
  
<ul>  
  {% for subscription in subscriptions %}  
  <li>  
    <a href="/developer#{{subscription.Id}}">{{subscription.DisplayName}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
{% if CannotAddBecauseSubscriptionNumberLimitReached %}  
<b>{% localized "ProductDetailsStrings|TextblockSubscriptionLimitReached" %}</b>  
{% elsif CannotAddBecauseMultipleSubscriptionsNotAllowed == false %}  
<subscribe-button></subscribe-button>  
{% endif %}  
```  
  
### <a name="controls"></a>Vezérlők  
 A `Product list` sablon a következő [lapvezérlőket használhatja](api-management-page-controls.md).  
  
-   [feliratkozás-gomb](api-management-page-controls.md#subscribe-button)  
  
### <a name="data-model"></a>Adatmodell  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Product|[Termék](api-management-template-data-model-reference.md#Product)|A megadott termék.|  
|IsDeveloperSubscribed|logikai|Azt jelzi, hogy az aktuális felhasználó előfizetett-e erre a termékre.|  
|Előfizetésállapota|szám|Az előfizetés állapota. A lehetséges állapotok a következők:<br /><br /> -   `0 - suspended`– az előfizetés le van tiltva, és az előfizető nem hívhatja meg a termék API-jait.<br />-   `1 - active`– az előfizetés aktív.<br />-   `2 - expired`– az előfizetés elérte a lejárati dátumot, és inaktiválták.<br />-   `3 - submitted`– az előfizetési kérelmet a fejlesztő nyújtotta be, de még nem hagyták jóvá vagy utasították el.<br />-   `4 - rejected`– az előfizetési kérelmet a rendszergazda megtagadta.<br />-   `5 - cancelled`– az előfizetést a fejlesztő vagy a rendszergazda lemondta.|  
|Korlátok|tömb|Ez a tulajdonság elavult, és nem használható.|  
|Delegáltelőfizetés engedélyezve|logikai|Azt jelzi, hogy engedélyezve van-e a [delegálás](https://azure.microsoft.com/documentation/articles/api-management-howto-setup-delegation/) ehhez az előfizetéshez.|  
|DededededSubscriptionUrl|sztring|Ha a delegálás engedélyezve van, a delegált előfizetés URL-címe.|  
|Megállapodás született|logikai|Ha a terméknek vannak feltételei, akkor az aktuális felhasználó elfogadta-e a feltételeket.|  
|Előfizetések|Az [Előfizetés összesítő](api-management-template-data-model-reference.md#SubscriptionSummary) entitásainak gyűjteménye.|A termék előfizetései.|  
|Api|[API-entitások](api-management-template-data-model-reference.md#API) gyűjteménye.|A termék API-jait.|  
|Nem adható hozzáBecausesubscriptionnumberlimitreached|logikai|Azt jelzi, hogy az aktuális felhasználó jogosult-e feliratkozni erre a termékre az előfizetési korlát tekintetében.|  
|Nem adható hozzá, merttöbbelőfizetésnem engedélyezett|logikai|Azt jelzi, hogy az aktuális felhasználó jogosult-e feliratkozni erre a termékre a több előfizetés engedélyezett vagy sem történő elérése tekintetében.|  
  
### <a name="sample-template-data"></a>Mintasablon adatai  
  
```json  
{  
    "Product": {  
        "Id": "56f9445ffaf7560049060001",  
        "Title": "Starter",  
        "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
        "Terms": "",  
        "ProductState": 1,  
        "AllowMultipleSubscriptions": false,  
        "MultipleSubscriptionsCount": 1  
    },  
    "IsDeveloperSubscribed": true,  
    "SubscriptionState": 1,  
    "Limits": [],  
    "DelegatedSubscriptionEnabled": false,  
    "DelegatedSubscriptionUrl": null,  
    "IsAgreed": false,  
    "Subscriptions": [  
        {  
            "Id": "56f9445ffaf7560049070001",  
            "DisplayName": "Starter  (default)"  
        }  
    ],  
    "Apis": [  
        {  
            "id": "56f9445ffaf7560049040001",  
            "name": "Echo API",  
            "description": null,  
            "serviceUrl": "http://echoapi.cloudapp.net/api",  
            "path": "echo",  
            "protocols": [  
                2  
            ],  
            "authenticationSettings": null,  
            "subscriptionKeyParameterNames": null  
        }  
    ],  
    "CannotAddBecauseSubscriptionNumberLimitReached": false,  
    "CannotAddBecauseMultipleSubscriptionsNotAllowed": true  
}  
```

## <a name="next-steps"></a>További lépések
A sablonok használatáról további információt [az API Management fejlesztői portál testreszabása sablonok használatával című](api-management-developer-portal-templates.md)témakörben talál.
