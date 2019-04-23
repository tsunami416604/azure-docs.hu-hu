---
title: A termék sablonok az Azure API Management |} A Microsoft Docs
description: Ismerje meg, hogyan szabhatja testre az Azure API Management fejlesztői portálon termék oldalainak tartalmát.
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
ms.openlocfilehash: 14090e21fb7c6ca07fe63220ffd1d44d483ac869
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52443627"
---
# <a name="product-templates-in-azure-api-management"></a>A termék sablonok az Azure API Management szolgáltatásban

Az Azure API Management lehetővé teszi a fejlesztői portál oldalainak konfigurálása a tartalom-sablonok használatával tartalmának testreszabása. Használatával [DotLiquid](http://dotliquidmarkup.org/) szintaxist és a szerkesztő szerkesztőprogramban, például [DotLiquid tervezők számára](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), és a egy megadott készlete honosított [karakterlánc-erőforrások](api-management-template-resources.md#strings), [szimbóluma erőforrások](api-management-template-resources.md#glyphs), és [vezérlők lapon](api-management-page-controls.md), konfigurálhatja a lapok tartalmát, igény szerint ezek a sablonok használatával nagy rugalmasságot biztosít.  
  
 Ebben a szakaszban a sablonok lehetővé teszik a termék a fejlesztői portál oldalainak tartalmát testreszabása.  
  
-   [Termékek listáját](#ProductList)  
  
-   [A termék](#Product)  
  
> [!NOTE]
>  Alapértelmezett mintasablonokat a következő dokumentációban szerepelnek, de módosulhatnak, folyamatos fejlesztései miatt. A fejlesztői portálon az élő alapértelmezett sablont a kívánt egyéni sablonokat lépve tekintheti meg. Sablonok használatának ismertetését lásd: [testreszabása sablonok használatával, az API Management fejlesztői portálon](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="ProductList"></a> Termékek listáját  
 A **termékek listáját** a sablon lehetővé teszi, hogy a törzse a termék-lista oldalára, a fejlesztői portál testreszabása.  
  
 ![Termékek listájának](./media/api-management-product-templates/APIM_ProductsListTemplatePage.png "APIM_ProductsListTemplatePage")  
  
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
  
-   [Keresés-vezérlő](api-management-page-controls.md#search-control)  
  
### <a name="data-model"></a>Adatmodell  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Paging|[Lapozás](api-management-template-data-model-reference.md#Paging) entitás.|A termékek gyűjtemény lapozási adatait.|  
|Filtering|[Szűrés](api-management-template-data-model-reference.md#Filtering) entitás.|A termékek-lista oldalára kapcsolatos információkat.|  
|Products|A gyűjtemény [termék](api-management-template-data-model-reference.md#Product) entitásokat.|Az aktuális felhasználó számára látható termékeket.|  
  
### <a name="sample-template-data"></a>Mintaadatok sablon  
  
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
  
##  <a name="Product"></a> A termék  
 A **termék** a sablon lehetővé teszi, hogy a törzse a termék oldalát a fejlesztői portál testreszabása.  
  
 ![Fejlesztői portál termékoldalán](./media/api-management-product-templates/APIM_ProductPage.png "APIM_ProductPage")  
  
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
  
-   [előfizetés button](api-management-page-controls.md#subscribe-button)  
  
### <a name="data-model"></a>Adatmodell  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Product|[A termék](api-management-template-data-model-reference.md#Product)|A megadott termék.|  
|IsDeveloperSubscribed|logikai|Hogy az aktuális felhasználó a termékre elő van fizetve.|  
|SubscriptionState|szám|Az előfizetés állapotát. Lehetséges állapotok a következők:<br /><br /> -   `0 - suspended` – az előfizetés le van tiltva, és az előfizető nem hívható meg a termék minden API-k.<br />-   `1 - active` – az előfizetés nem aktív.<br />-   `2 - expired` – az előfizetés elérte a lejárati dátum és az inaktiválva lett.<br />-   `3 - submitted` – az előfizetési kérést, fejlesztője által lett végrehajtva, de még nincs jóváhagyták vagy elutasították.<br />-   `4 - rejected` – az előfizetés kérelem el lett utasítva, egy rendszergazda.<br />-   `5 - cancelled` – az előfizetés a fejlesztői vagy a rendszergazda megszakította.|  
|Limits|tömb|Ez a tulajdonság elavult, és nem használható.|  
|DelegatedSubscriptionEnabled|logikai|E [delegálás](https://azure.microsoft.com/documentation/articles/api-management-howto-setup-delegation/) ehhez az előfizetéshez engedélyezve van.|  
|DelegatedSubscriptionUrl|sztring|Ha a delegálás engedélyezve van, a meghatalmazott előfizetés URL-címe.|  
|IsAgreed|logikai|Ha a termék használati, hogy az aktuális felhasználó elfogadta a feltételeket.|  
|Subscriptions|A gyűjtemény [előfizetés összefoglalás](api-management-template-data-model-reference.md#SubscriptionSummary) entitásokat.|Az előfizetések a termékhez.|  
|Apis|A gyűjtemény [API](api-management-template-data-model-reference.md#API) entitásokat.|A termék API-k.|  
|CannotAddBecauseSubscriptionNumberLimitReached|logikai|Az aktuális felhasználó-e előfizetni az előfizetésre vonatkozó korlát tekintetében a termék jogosult.|  
|CannotAddBecauseMultipleSubscriptionsNotAllowed|logikai|Az aktuális felhasználó-e jogosult az előfizetés a termékre, vagy nem engedélyezett több előfizetéssel kapcsolatban.|  
  
### <a name="sample-template-data"></a>Mintaadatok sablon  
  
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
Sablonok használatának ismertetését lásd: [testreszabása sablonok használatával, az API Management fejlesztői portálon](api-management-developer-portal-templates.md).
