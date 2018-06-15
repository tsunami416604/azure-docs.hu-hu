---
title: A termék sablonok az Azure API Management |} Microsoft Docs
description: Ismerje meg, hogyan szabhatja testre a tartalom az Azure API Management fejlesztői portálján a termék oldalát.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 49f9254c-4c5f-4ed4-9c8d-798f44e805ee
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: dae757231d8f2ff7fcd8e032d941c0fa9f192796
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23835166"
---
# <a name="product-templates-in-azure-api-management"></a>A termék sablonok az Azure API Management
Az Azure API Management lehetővé teszi a tartalom developer portálon lapok használatával konfigurálhatja a tartalom-sablonok testreszabása. Használatával [DotLiquid](http://dotliquidmarkup.org/) szintaxisát és az Ön által választott szerkesztőben, mint [tervezőknek DotLiquid](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), és a megadott készlete honosított [karakterlánc-erőforrások](api-management-template-resources.md#strings), [betűkép-erőforrások](api-management-template-resources.md#glyphs), és [vezérlők lapon](api-management-page-controls.md), konfigurálja a tartalmat, a lapok, ahogyan szeretné ezeket a sablonokat használ nagy rugalmasságot biztosítanak.  
  
 Ebben a szakaszban a sablonok engedélyezi, hogy a tartalom a fejlesztői portálján a termék oldalát testreszabásához.  
  
-   [Termékek listáját](#ProductList)  
  
-   [A termék](#Product)  
  
> [!NOTE]
>  Minta alapértelmezett sablonok az alábbi dokumentáció szerepelnek, de folyamatos fejlesztéseket miatt változhat. Megtekintheti az élő alapértelmezett sablonok a fejlesztői portálra nyissa meg a kívánt egyéni sablonokat. A sablonok használatának kapcsolatos további információkért lásd: [hogyan szabhatja testre a sablonok segítségével az API Management fejlesztői portálján](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  
  
##  <a name="ProductList"></a>Termékek listáját  
 A **termékek listáját** sablon lehetővé teszi a fejlesztői portálra a termék lista lap törzsében testreszabását.  
  
 ![Termékek listáját](./media/api-management-product-templates/APIM_ProductsListTemplatePage.png "APIM_ProductsListTemplatePage")  
  
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
 A `Product list` sablon előfordulhat, hogy használja a következő [vezérlők lapon](api-management-page-controls.md).  
  
-   [lapozófájl-vezérlő](api-management-page-controls.md#paging-control)  
  
-   [keresési-vezérlő](api-management-page-controls.md#search-control)  
  
### <a name="data-model"></a>Adatmodell  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Lapozás|[Lapozás](api-management-template-data-model-reference.md#Paging) entitás.|A termékek gyűjtemény lapozás adatait.|  
|Szűrés|[Szűrés](api-management-template-data-model-reference.md#Filtering) entitás.|A termékek lista lap kapcsolatos információkat.|  
|Termékek|A gyűjtemény [termék](api-management-template-data-model-reference.md#Product) entitásokat.|A termékek, az aktuális felhasználó számára látható.|  
  
### <a name="sample-template-data"></a>Mintaadatokat sablon  
  
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
  
##  <a name="Product"></a>A termék  
 A **termék** sablon lehetővé teszi a termék oldalát a fejlesztői portálra törzsét testreszabását.  
  
 ![Fejlesztői portál termékoldalára](./media/api-management-product-templates/APIM_ProductPage.png "APIM_ProductPage")  
  
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
 A `Product list` sablon előfordulhat, hogy használja a következő [vezérlők lapon](api-management-page-controls.md).  
  
-   [előfizetés gomb](api-management-page-controls.md#subscribe-button)  
  
### <a name="data-model"></a>Adatmodell  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Product|[A termék](api-management-template-data-model-reference.md#Product)|A meghatározott termék.|  
|IsDeveloperSubscribed|Logikai érték|Hogy az aktuális felhasználó számára a termék elő van fizetve.|  
|SubscriptionState|Szám|Az előfizetés állapotát. Lehetséges állapota van:<br /><br /> -   `0 - suspended`– az előfizetés le van tiltva, és az előfizető nem hívható meg a termék bármely API-k.<br />-   `1 - active`– az előfizetés nem aktív.<br />-   `2 - expired`– az előfizetés elérte a lejárat és inaktiváltuk.<br />-   `3 - submitted`– az előfizetési kérelem a fejlesztő történt, de még nem jóváhagyták vagy elutasították.<br />-   `4 - rejected`– az előfizetési kérelem rendszergazda meg lett tagadva.<br />-   `5 - cancelled`– az előfizetés a fejlesztői vagy a rendszergazda megszakította.|  
|Korlátok|A tömb|Ez a tulajdonság elavult, és nem használható.|  
|DelegatedSubscriptionEnabled|Logikai érték|E [delegálás](https://azure.microsoft.com/documentation/articles/api-management-howto-setup-delegation/) engedélyezve van ennél az előfizetésnél.|  
|DelegatedSubscriptionUrl|Karakterlánc|Ha delegálás engedélyezve van, a meghatalmazott előfizetés URL-CÍMÉT.|  
|IsAgreed|Logikai érték|Ha a termék feltételeket, hogy az aktuális felhasználó elfogadta a feltételeket.|  
|Előfizetések|A gyűjtemény [előfizetés összegzés](api-management-template-data-model-reference.md#SubscriptionSummary) entitásokat.|A termék az előfizetést.|  
|API-k|A gyűjtemény [API](api-management-template-data-model-reference.md#API) entitásokat.|Az API-k ennek a terméknek.|  
|CannotAddBecauseSubscriptionNumberLimitReached|Logikai érték|Az aktuális felhasználónak-e előfizetni tekintetében az előfizetési határértéket a termék jogosult.|  
|CannotAddBecauseMultipleSubscriptionsNotAllowed|Logikai érték|Az aktuális felhasználónak-e előfizetni tekintetében több előfizetést, vagy nem megengedett a termék jogosult.|  
  
### <a name="sample-template-data"></a>Mintaadatokat sablon  
  
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

## <a name="next-steps"></a>Következő lépések
A sablonok használatának kapcsolatos további információkért lásd: [hogyan szabhatja testre a sablonok segítségével az API Management fejlesztői portálján](api-management-developer-portal-templates.md).