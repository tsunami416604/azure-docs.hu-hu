---
title: SaaS-teljesítési API v2 | Azure piactér
description: Ez a cikk bemutatja, hogyan hozhat létre és kezelhet SaaS-ajánlatokat a AppSource és az Azure Marketplace-en a társított teljesítési v2 API-k használatával.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: 276699b9316a0c4fd428038f2c967bdf934f449c
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69016049"
---
# <a name="saas-fulfillment-apis-version-2"></a>SaaS-teljesítési API-k, 2-es verzió 

Ez a cikk azokat az API-kat ismerteti, amelyek lehetővé teszik partnerei számára, hogy SaaS-alkalmazásaikat eladják a AppSource piactéren és az Azure piactéren. Ezek az API-k a AppSource és az Azure Marketplace-en elérhető, a transacter SaaS-ajánlatok követelményei.

## <a name="managing-the-saas-subscription-life-cycle"></a>Az SaaS-előfizetési életciklus kezelése

Az Azure SaaS a SaaS-előfizetés vásárlásának teljes életciklusát kezeli. A teljesítési API-kat olyan mechanizmusként használja, amely a tényleges teljesítést, a csomagok változásait és az előfizetés törlését hajtja végre a partnerrel. Az ügyfél számlája a Microsoft által fenntartott SaaS-előfizetés állapotától függ. Az alábbi ábrán az állapotok és az állapotok közötti módosításokat meghajtó műveletek láthatók.

![SaaS-előfizetések életciklusának állapota](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>SaaS-előfizetés állapota

A következő táblázat az SaaS-előfizetések kiépítési állapotait sorolja fel, beleértve az egyes (ha alkalmazható) leírását és sorszámát. 

#### <a name="provisioning"></a>Kiépítés

Amikor egy ügyfél megvásárolja a vásárlást, a partner megkapja ezt az információt egy olyan ügyfél-interaktív weblap engedélyezési kódjában, amely URL-paramétert használ. Ilyen például `https://contoso.com/signup?token=..`az, hogy a Kezdőlap URL-címe a partner `https://contoso.com/signup`Centerben. Az engedélyezési kód érvényesíthető és kicserélhető a kiépítési szolgáltatás részleteire az API feloldása lehetőség meghívásával.  Ha egy SaaS-szolgáltatás befejezi az üzembe helyezést, az aktiválási hívást küld, amely jelzi, hogy a teljesítés befejeződött, és az ügyfél számlázása is megtörténik. 

Az alábbi ábrán egy kiépítési forgatókönyvhöz tartozó API-hívások sora látható.  

![API-hívások SaaS-szolgáltatás létesítéséhez](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Kiépítve

Ez az állapot a kiépített szolgáltatás állandó állapota.

##### <a name="provisioning-for-update"></a>Kiépítés a frissítéshez 

Ez az állapot azt jelzi, hogy egy meglévő szolgáltatás frissítése függőben van. Ezt a frissítést az ügyfél kezdeményezheti, akár a piactéren, akár az SaaS szolgáltatásban (csak a közvetlen ügyfelek által lebonyolított tranzakciók esetében).

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>Kiépítés a frissítéshez (ha a piactéren kezdeményezik)

A következő ábra azokat a műveleteket mutatja be, amikor egy frissítést kezdeményeznek a piactéren.

![API-hívások a piactéren való frissítés kezdeményezése esetén](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>Kiépítés a frissítéshez (ha az SaaS szolgáltatásból kezdeményezik)

A következő ábra azokat a műveleteket mutatja be, amikor egy frissítést kezdeményeznek a SaaS szolgáltatásból. (A webhook-hívást a SaaS szolgáltatás által kezdeményezett előfizetés egy frissítése váltja fel.) 

![API-hívások, ha a frissítés az SaaS szolgáltatásból indul el](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Felfüggesztve

Ez az állapot azt jelzi, hogy az ügyfél fizetése nem érkezett meg. A szabályzat szerint az ügyfél türelmi időszakot biztosít az előfizetés megszakítása előtt. Ha egy előfizetés ebben az állapotban van: 

- Partnerként dönthet úgy, hogy lecsökkenti vagy letiltja a felhasználó hozzáférését a szolgáltatáshoz.
- Az előfizetést helyreállítható állapotba kell helyezni, amely a teljes funkcionalitást visszaállíthatja az adatvesztés vagy a beállítások elvesztése nélkül. 
- A rendszer az előfizetésre vonatkozó visszavonási kérelmet vár a teljesítési API-kon vagy egy, a türelmi időszak végén található kiépítési kérésen keresztül. 

#### <a name="unsubscribed"></a>Leiratkozva 

Az előfizetések ezt az állapotot a kifejezett vásárlói kérelemre vagy illetékek fizetésére reagálva érik el. A partnertől érkező elvárás az, hogy az ügyfél adatait bizonyos számú napig megőrzi a rendszer a helyreállításhoz, majd törli azt. 


## <a name="api-reference"></a>API-referencia

Ez a szakasz a SaaS- *előfizetés API* -ját és az *Operations API*-t dokumentálja.  A 2 `2018-08-31`. verziójú `api-version` API-k paraméterének értéke.  


### <a name="parameter-and-entity-definitions"></a>Paraméterek és entitások definíciói

A következő táblázat a teljesítési API-k által használt általános paraméterek és entitások definícióit sorolja fel.

|     Entitás/paraméter     |     Meghatározás                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | Egy SaaS-erőforrás GUID-azonosítója.  |
| `name`                   | Az erőforráshoz az ügyfél által megadott rövid név. |
| `publisherId`            | Egyedi karakterlánc-azonosító minden közzétevőhöz (például: contoso). |
| `offerId`                | Minden ajánlat egyedi karakterlánc-azonosítója (például: "offer1").  |
| `planId`                 | Minden csomag/SKU egyedi karakterlánc-azonosítója (például: "Silver"). |
| `operationId`            | Egy adott művelet GUID-azonosítója.  |
|  `action`                | Az `unsubscribe` `suspend`erőforráson `changePlan` `changeQuantity`végrehajtandó művelet, vagy,,, vagy, `transfer`,. `reinstate`  |
|   |   |

A globálisan egyedi azonosítók ([GUID azonosítók](https://en.wikipedia.org/wiki/Universally_unique_identifier)) a jellemzően automatikusan generált 128 bites (32-hexadecimális) számok. 

#### <a name="resolve-a-subscription"></a>Előfizetés feloldása 

A feloldási végpont lehetővé teszi, hogy a közzétevő a Piactéri tokent egy állandó erőforrás-AZONOSÍTÓra oldja fel. Az erőforrás-azonosító az SaaS-előfizetés egyedi azonosítója. Amikor egy felhasználó átirányítja egy partner webhelyére, az URL-cím tartalmaz egy tokent a lekérdezési paraméterekben. A partnernek a tokent kell használnia, és el kell végeznie a feloldását. A válasz tartalmazza az egyedi SaaS-előfizetés AZONOSÍTÓját, nevét, ajánlatának AZONOSÍTÓját, és megtervezi az erőforrást. Ez a jogkivonat csak egy órára érvényes. 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Közzététel<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A kérelemhez használni kívánt művelet verziója.  |

*Kérelem fejlécei:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |  Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
|  x-ms-correlationid |  Egyedi karakterlánc-érték a művelethez az ügyfélen. Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
|  authorization     |  A [JSON webes jogkivonat (JWT) tulajdonosi](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)jogkivonatának beolvasása. Például: "`Bearer <access_token>`". |
|  x-ms-marketplace-token  |  Az URL-cím jogkivonat-lekérdezési paramétere, ha a felhasználó átirányítja a SaaS-partner webhelyére az Azure- `https://contoso.com/signup?token=..`ból (például:). *Megjegyzés:* Az URL-cím dekódolja a jogkivonat értékét a böngészőből a használat előtt.  |

*Reagálási kódok:*

Kód: 200<br>
Az átlátszatlan token feloldása SaaS-előfizetéshez. Válasz törzse:
 

```json
{
    "id": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

Kód: 400<br>
Hibás kérelem. x-MS-Marketplace-token hiányzik, helytelenül formázott vagy lejárt.

Kód: 403<br>
Nem engedélyezett. A hitelesítési jogkivonat nem lett megadva vagy érvénytelen, vagy a kérelem olyan akvizícióhoz próbál hozzáférni, amely nem tartozik az aktuális közzétevőhöz.

Kód: 404<br>
Nem található.

Kód: 500<br>
Belső kiszolgálóhiba.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

### <a name="subscription-api"></a>Előfizetés API

Az előfizetési API a következő HTTPS-műveleteket támogatja: **Get**, **post**, **patch**és **delete**.


#### <a name="list-subscriptions"></a>Előfizetések listázása

Felsorolja a közzétevőhöz tartozó összes SaaS-előfizetést.

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Beolvasás<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  A kérelemhez használni kívánt művelet verziója.  |

*Kérelem fejlécei:*

|                    |                   |
|  ---------------   |  ---------------  |
| Content-Type       |  `application/json`  |
| x-ms-requestid     |  Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
| x-ms-correlationid |  Egyedi karakterlánc-érték a művelethez az ügyfélen. Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
| authorization      |  A [JSON webes jogkivonat (JWT) tulajdonosi](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)jogkivonatának beolvasása. Például: "`Bearer <access_token>`".  |

*Reagálási kódok:*

Kód: 200 <br/>
Lekéri a közzétevőt és a hozzá tartozó előfizetéseket a közzétevő összes ajánlatához a hitelesítési jogkivonat alapján.
Válasz hasznos adatai:<br>

```json
{
  [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
            "term": {
                "startDate": "2019-05-31",
                "endDate": "2019-06-29",
                "termUnit": "P1M"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "isFreeTrial": "true", // true – the customer subscription is currently in free trial, false – the customer subscription is not currently in free trial.
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

A folytatási token csak akkor jelenik meg, ha a lekérdezni kívánt csomagok további "lapjai" vannak. 

Kód: 403 <br>
Nem engedélyezett. A hitelesítési jogkivonat nem lett megadva vagy érvénytelen, vagy a kérelem olyan akvizícióhoz próbál hozzáférni, amely nem tartozik az aktuális közzétevőhöz. 

Kód: 500<br>
Belső kiszolgálóhiba.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="get-subscription"></a>Előfizetés beolvasása

Lekéri a megadott SaaS-előfizetést. Ezzel a hívással kérheti le a licencek adatait, és megtervezheti az információkat.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Beolvasás<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   Azon SaaS-előfizetés egyedi azonosítója, amelyet a jogkivonat feloldása után szereztek fel az API-n keresztül.   |
|  ApiVersion        |   A kérelemhez használni kívánt művelet verziója.   |

*Kérelem fejlécei:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-ms-requestid    |  Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
|  x-ms-correlationid |  Egyedi karakterlánc-érték a művelethez az ügyfélen. Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
|  authorization     |  A [JSON webes jogkivonat (JWT) tulajdonosi](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)jogkivonatának beolvasása. Például: "`Bearer <access_token>`".  |

*Reagálási kódok:*

Kód: 200<br>
Az SaaS-előfizetés beolvasása az azonosítóból. Válasz hasznos adatai:<br>

```json
Response Body:
{ 
        "id":"",
        "name":"Contoso Cloud Solution",
        "publisherId": "contoso",
        "offerId": "offer1",
        "planId": "silver",
        "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "isFreeTrial": "true", // true – customer subscription is currently in free trial, false – customer subscription is not currently in free trial.
        "status": "Subscribed", // Indicates the status of the operation.
          "term": { //This gives the free trial term start and end date
            "startDate": "2019-05-31",
            "endDate": "2019-06-29",
            "termUnit": "P1M" //where P1M: Monthly, P1Y: Yearly 
        },
}
```

Kód: 403<br>
Nem engedélyezett. A hitelesítési jogkivonat nem lett megadva vagy érvénytelen, vagy a kérelem olyan akvizícióhoz próbál hozzáférni, amely nem tartozik az aktuális közzétevőhöz.

Kód: 404<br>
Nem található.<br> 

Kód: 500<br>
Belső kiszolgálóhiba.<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>Elérhető csomagok listázása

Ezzel a hívással megtudhatja, hogy vannak-e olyan magán-vagy nyilvános ajánlatok az aktuális közzétevőhöz.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Beolvasás<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   A kérelemhez használni kívánt művelet verziója.  |

*Kérelem fejlécei:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-ms-requestid   |   Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
|  x-ms-correlationid  | Egyedi karakterlánc-érték a művelethez az ügyfélen. Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
|  authorization     |  A [JSON webes jogkivonat (JWT) tulajdonosi](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)jogkivonatának beolvasása.  Például: "`Bearer <access_token>`". |

*Reagálási kódok:*

Kód: 200<br>
Lekéri az ügyfél számára elérhető csomagok listáját. Válasz törzse:

```json
{
    "plans": [{
        "planId": "Platinum001",
        "displayName": "Private platinum plan for Contoso",
        "isPrivate": true
    }]
}
```

Kód: 404<br>
Nem található.<br> 

Kód: 403<br>
Nem engedélyezett. A hitelesítési jogkivonat nem lett megadva vagy érvénytelen, vagy a kérelem olyan akvizícióhoz próbál hozzáférni, amely nem tartozik az aktuális közzétevőhöz. <br> 

Kód: 500<br>
Belső kiszolgálóhiba.<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>Előfizetés aktiválása

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Közzététel<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A kérelemhez használni kívánt művelet verziója.  |
| subscriptionId     | A jogkivonat feloldása után az API feloldása után beszerzett SaaS-előfizetés egyedi azonosítója.  |

*Kérelem fejlécei:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-ms-requestid    | Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
|  x-ms-correlationid  | Egyedi karakterlánc-érték a művelethez az ügyfélen. Ez a karakterlánc a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
|  authorization     |  A [JSON webes jogkivonat (JWT) tulajdonosi](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)jogkivonatának beolvasása.  Például: "`Bearer <access_token>`". |

*Kérelem tartalma:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*Reagálási kódok:*

Kód: 200<br>
Aktiválja az előfizetést.<br>

Kód: 400<br>
Hibás kérelem: érvényesítési hibák.

Kód: 403<br>
Nem engedélyezett. A hitelesítési jogkivonat nem lett megadva vagy érvénytelen, vagy a kérelem olyan akvizícióhoz próbál hozzáférni, amely nem tartozik az aktuális közzétevőhöz.

Kód: 404<br>
Nem található.

Kód: 500<br>
Belső kiszolgálóhiba.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-plan-on-the-subscription"></a>Az előfizetés tervének módosítása

Frissítse a csomagot az előfizetésben.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Javítás<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A kérelemhez használni kívánt művelet verziója.  |
| subscriptionId     | A jogkivonat feloldása után az API feloldása után beszerzett SaaS-előfizetés egyedi azonosítója.  |

*Kérelem fejlécei:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
|  x-ms-correlationid  |  Egyedi karakterlánc-érték a művelethez az ügyfélen. Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.    |
| authorization      |  A [JSON webes jogkivonat (JWT) tulajdonosi](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)jogkivonatának beolvasása.  Például: "`Bearer <access_token>`".  |

*Kérelem tartalma:*

```json
Request Body:
{
    "planId": "gold"
}
```

*Kérelem fejlécei:*

|                    |                   |
|  ---------------   |  ---------------  |
| Művelet – hely | Egy erőforrásra mutató hivatkozás a művelet állapotának lekéréséhez.   |

*Reagálási kódok:*

Kód: 202<br>
A terv módosítására irányuló kérelmet elfogadták. A partnernek a művelet helye alapján kell lekérdezni a sikerességet vagy a hibát. <br>

Kód: 400<br>
Hibás kérelem: érvényesítési hibák.

Kód: 403<br>
Nem engedélyezett. A hitelesítési jogkivonat nem lett megadva vagy érvénytelen, vagy a kérelem olyan akvizícióhoz próbál hozzáférni, amely nem tartozik az aktuális közzétevőhöz.

Kód: 404<br>
Nem található.

Kód: 500<br>
Belső kiszolgálóhiba.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>Egyszerre csak egy csomagot vagy mennyiséget lehet kijavítani, mindkettőt nem. A **frissítéssel** rendelkező előfizetések módosítása nem `allowedCustomerOperations`található.

#### <a name="change-the-quantity-on-the-subscription"></a>Az előfizetés mennyiségének módosítása

Frissítse az előfizetés mennyiségét.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Javítás<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A kérelemhez használni kívánt művelet verziója.  |
| subscriptionId     | A jogkivonat feloldása után az API feloldása után beszerzett SaaS-előfizetés egyedi azonosítója.  |

*Kérelem fejlécei:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
|  x-ms-correlationid  |  Egyedi karakterlánc-érték a művelethez az ügyfélen. Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.    |
| authorization      |  A [JSON webes jogkivonat (JWT) tulajdonosi](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)jogkivonatának beolvasása.  Például: "`Bearer <access_token>`".  |

*Kérelem tartalma:*

```json
Request Body:
{
    "quantity": 5
}
```

*Kérelem fejlécei:*

|                    |                   |
|  ---------------   |  ---------------  |
| Művelet – hely | Egy erőforrásra mutató hivatkozás a művelet állapotának lekéréséhez.   |

*Reagálási kódok:*

Kód: 202<br>
A mennyiség módosítására vonatkozó kérés el lett fogadva. A partnernek a művelet helye alapján kell lekérdezni a sikerességet vagy a hibát. <br>

Kód: 400<br>
Hibás kérelem: érvényesítési hibák.


Kód: 403<br>
Nem engedélyezett. A hitelesítési jogkivonat nem lett megadva vagy érvénytelen, vagy a kérelem olyan akvizícióhoz próbál hozzáférni, amely nem tartozik az aktuális közzétevőhöz.

Kód: 404<br>
Nem található.

Kód: 500<br>
Belső kiszolgálóhiba.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>Egyszerre csak egy csomagot vagy mennyiséget lehet kijavítani, mindkettőt nem. A **frissítéssel** rendelkező előfizetések módosítása nem `allowedCustomerOperations`található.

#### <a name="delete-a-subscription"></a>Előfizetés törlése

Leiratkozás és a megadott előfizetés törlése.

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionid-api-versionapiversion"></a>Törlés<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A kérelemhez használni kívánt művelet verziója.  |
| subscriptionId     | A jogkivonat feloldása után az API feloldása után beszerzett SaaS-előfizetés egyedi azonosítója.  |

*Kérelem fejlécei:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |   Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.   |
|  x-ms-correlationid  |  Egyedi karakterlánc-érték a művelethez az ügyfélen. Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.   |
|  authorization     |  A [JSON webes jogkivonat (JWT) tulajdonosi](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)jogkivonatának beolvasása.  Például: "`Bearer <access_token>`".  |

*Reagálási kódok:*

Kód: 202<br>
A partner egy SaaS-előfizetés lemondására irányuló hívást kezdeményezett.<br>

Kód: 400<br>
Törlés nem `allowedCustomerOperations`a (z) **törlésével** rendelkező előfizetésre.

Kód: 403<br>
Nem engedélyezett. A hitelesítési jogkivonat nem lett megadva vagy érvénytelen, vagy a kérelem olyan akvizícióhoz próbál hozzáférni, amely nem tartozik az aktuális közzétevőhöz.

Kód: 404<br>
Nem található.

Kód: 500<br>
Belső kiszolgálóhiba.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```


### <a name="operations-api"></a>Operatív API

Az operatív API a következő javítási és lekérési műveleteket támogatja.

#### <a name="list-outstanding-operations"></a>Függőben lévő műveletek listázása 

Felsorolja az aktuális közzétevőre vonatkozó függőben lévő műveleteket. 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Beolvasás<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   A kérelemhez használni kívánt művelet verziója.                |
| subscriptionId     | A jogkivonat feloldása után az API feloldása után beszerzett SaaS-előfizetés egyedi azonosítója.  |

*Kérelem fejlécei:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |  Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
|  x-ms-correlationid |  Egyedi karakterlánc-érték a művelethez az ügyfélen. Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
|  authorization     |  A [JSON webes jogkivonat (JWT) tulajdonosi](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)jogkivonatának beolvasása.  Például: "`Bearer <access_token>`".  |

*Reagálási kódok:*

Kód: 200<br> A függőben lévő műveletek listájának beolvasása egy előfizetésen. Válasz hasznos adatai:

```json
[{
    "id": "<guid>",  
    "activityId": "<guid>",
    "subscriptionId": "<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",  
    "status": "NotStarted"  
}]
```


Kód: 400<br>
Hibás kérelem: érvényesítési hibák.

Kód: 403<br>
Nem engedélyezett. A hitelesítési jogkivonat nem lett megadva vagy érvénytelen, vagy a kérelem olyan akvizícióhoz próbál hozzáférni, amely nem tartozik az aktuális közzétevőhöz.

Kód: 404<br>
Nem található.

Kód: 500<br>
Belső kiszolgálóhiba.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>Művelet állapotának beolvasása

Lehetővé teszi a közzétevő számára a megadott `subscribe`aktivált aszinkron művelet ( `unsubscribe` `changePlan`például,, vagy `changeQuantity`) állapotának nyomon követését.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Beolvasás<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A kérelemhez használni kívánt művelet verziója.  |

*Kérelem fejlécei:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`   |
|  x-ms-requestid    |   Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
|  x-ms-correlationid |  Egyedi karakterlánc-érték a művelethez az ügyfélen. Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
|  authorization     |  A [JSON webes jogkivonat (JWT) tulajdonosi](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)jogkivonatának beolvasása. Például: "`Bearer <access_token>`".  |

*Reagálási kódok:*<br>

Kód: 200<br> Lekéri a megadott függőben lévő SaaS-műveletet. Válasz hasznos adatai:

```json
Response body:
{
    "id  ": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",
    "status": "NotStarted"
}

```

Kód: 400<br>
Hibás kérelem: érvényesítési hibák.

Kód: 403<br>
Nem engedélyezett. A hitelesítési jogkivonat nem lett megadva vagy érvénytelen, vagy a kérelem olyan akvizícióhoz próbál hozzáférni, amely nem tartozik az aktuális közzétevőhöz.
 
Kód: 404<br>
Nem található.

Kód: 500<br> Belső kiszolgálóhiba.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>Művelet állapotának frissítése

Egy művelet állapotának frissítése a megadott értékekkel való sikeres vagy sikertelenség jelzéséhez.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Javítás<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  A kérelemhez használni kívánt művelet verziója.  |
| subscriptionId     | A jogkivonat feloldása után az API feloldása után beszerzett SaaS-előfizetés egyedi azonosítója.  |
|  operationId       | Az éppen befejezett művelet. |

*Kérelem fejlécei:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     | `application/json`   |
|   x-ms-requestid   |   Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
|  x-ms-correlationid |  Egyedi karakterlánc-érték a művelethez az ügyfélen. Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
|  authorization     |  A [JSON webes jogkivonat (JWT) tulajdonosi](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)jogkivonatának beolvasása.  Például: "`Bearer <access_token>`".  |

*Kérelem tartalma:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*Reagálási kódok:*

Kód: 200<br> Egy műveletnek a partner oldalon való befejezését jelző hívás. Ez a válasz például jelezheti az ülések vagy a csomagok változását.

Kód: 400<br>
Hibás kérelem: érvényesítési hibák.

Kód: 403<br>
Nem engedélyezett. A hitelesítési jogkivonat nem lett megadva vagy érvénytelen, vagy a kérelem olyan akvizícióhoz próbál hozzáférni, amely nem tartozik az aktuális közzétevőhöz.

Kód: 404<br>
Nem található.

Kód: 409<br>
Ütközés. Egy újabb tranzakció például már teljesül.

Kód: 500<br> Belső kiszolgálóhiba.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="implementing-a-webhook-on-the-saas-service"></a>Webhook megvalósítása az SaaS szolgáltatásban

A közzétevőnek be kell vezetnie egy webhookot ebben az SaaS-szolgáltatásban, hogy proaktívan értesítse a felhasználókat a szolgáltatás változásairól. Az SaaS szolgáltatásnak meg kell hívnia az operatív API-t az ellenőrzéshez és az engedélyezéshez, mielőtt lépéseket hozna a webhook értesítésére.

A biztonságos kommunikáció biztosítása érdekében a Microsoft a hívás részeként tartalmazza a Azure Active Directory JWT tokent az engedélyezési fejlécben. Az SaaS-szolgáltatókat arra javasoljuk, hogy ellenőrizze a JWT tokent a [Microsoft Identity platform hozzáférési](https://docs.microsoft.com/azure/active-directory/develop/access-tokens) jogkivonatok című cikkben leírtak szerint, hogy csak az érvényes hívásokat fogadják el.

```json
{
  "id": "<this is a GUID operation id, you can call operations API with this to get status>",
  "activityId": "<this is a Guid correlation id>",
  "subscriptionId": "<Guid to uniquely identify this resource>",
  "publisherId": "<this is the publisher’s name>",
  "offerId": "<this is the offer name>",
  "planId": "<this is the plan id>",
  "quantity": "<the number of seats, will be null if not per-seat saas offer>",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Unsubscribe",
  "status": "NotStarted"  

}
```
Ahol a művelet a következők egyike lehet: 
- `unsubscribe`(az erőforrás törlése után)
- `changePlan`(a változási terv műveletének befejezése után)
- `changeQuantity`(ha a mennyiség módosítása művelet befejeződött)
- `suspend`(ha az erőforrást felfüggesztették)
- `reinstate`(ha a felfüggesztés után visszaállította az erőforrást)

Ahol az állapot a következők egyike lehet: 
- **NotStarted** <br>
 - **Folyamatban** <br>
- **Sikerült** <br>
- **Sikertelen** <br>
- **Ütközés** <br>

Egy webhook-értesítésben a végrehajtható állapotok sikeresek és sikertelenek **voltak**. A művelet életciklusa a **NotStarted** -ből olyan terminál-állapotba történik, mint a **sikeres**, **sikertelen**vagy **ütközés**. Ha **NotStarted** vagy inaktivitást kap, folytassa az állapot lekérése az API-val, amíg a művelet el nem éri a terminál állapotát a művelet megkezdése előtt. 

## <a name="mock-apis"></a>Modell API-k

A modell API-k segítségével megkezdheti a fejlesztést, különösen a prototípusokat, valamint a projektek tesztelését. 

Gazdagép végpontja `https://marketplaceapi.microsoft.com/api` : (nincs szükség hitelesítésre)<br/>
API verziója:`2018-09-15`<br/>
Minta URI-ja:`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

Az API-végpont elérési útjai ugyanazok, mint a modell és a valós API-k esetében, de az API-verziók eltérőek. A verzió `2018-09-15` a modell verziója és `2018-08-31` az éles verzió. 

Az ebben a cikkben szereplő API-hívások az ál-gazdagép végpontján hozhatók létre. Általánosságban elmondható, hogy a rendszer válaszként visszaküldi az adatok mintáját. A frissítési előfizetés módszereinek meghívása az ál API-ban mindig a 500-as értéket kell visszaadnia. 

## <a name="next-steps"></a>További lépések

A fejlesztők a [Cloud Partner Portal REST API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)-k használatával programozott módon is lekérhetik és kezelhetik a munkaterheléseket, az ajánlatokat és a közzétevői profilokat.
