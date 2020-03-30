---
title: SaaS teljesítési API v2 | Azure Piactér
description: Ez a cikk bemutatja, hogyan hozhat létre és kezelhet egy SaaS-ajánlatot az AppSource és az Azure Marketplace-en a társított teljesítési v2 API-k használatával.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/18/2019
ms.author: dsindona
ms.openlocfilehash: ca49418013357ecaae62ea5e91374eaa1cbde59d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275764"
---
# <a name="saas-fulfillment-apis-version-2"></a>SaaS Fulfillment API-k, 2-es verzió 

Ez a cikk az api-kat részletezi, amelyek lehetővé teszik a partnerek számára, hogy eladják SaaS-alkalmazásaikat az AppSource-piactéren és az Azure Marketplace-en. Ezek az API-k az AppSource és az Azure Marketplace-en a tranzakciós SaaS-ajánlatok követelménye.

## <a name="managing-the-saas-subscription-life-cycle"></a>A SaaS-előfizetés életciklusának kezelése

Az Azure SaaS kezeli a SaaS-előfizetések teljes életciklusát. A teljesítési API-kat használja mechanizmusként a tényleges teljesítés, a tervek módosításaés az előfizetés partnerrel való törlésének ösztönzésére. Az ügyfél számlája a Microsoft által fenntartott SaaS-előfizetés állapotától függ. Az alábbi ábra azokat az állapotokat és műveleteket ábrázolja, amelyek az állapotok közötti változásokat hajtják.

![SaaS előfizetés életciklus-állapotok](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>SaaS-előfizetés államai

Az alábbi táblázat egy SaaS-előfizetés létesítési állapotait sorolja fel, beleértve az egyes szolgáltatások leírását és szekvenciadiagramját (ha van ilyen). 

#### <a name="provisioning"></a>Kiépítés

Amikor egy ügyfél vásárlást kezdeményez, a partner ezt az információt egy URL-paramétert használó, ügyfél-interaktív weboldal engedélyezési kódjában kapja meg. Ilyen például a `https://contoso.com/signup?token=..`céloldal URL-címe `https://contoso.com/signup`a Partnerközpontban. Az engedélyezési kód érvényesíthető, és a kiépítési szolgáltatás részleteiért a Resolve API hívásával cserélhető.  Amikor egy SaaS-szolgáltatás befejezi a kiépítést, egy aktiválási hívást küld annak jelzésére, hogy a teljesítés befejeződött, és az ügyfél számlázható. 

Az alábbi ábrán az API-hívások sorozata létesítési forgatókönyv.  

![Az API saas-szolgáltatás kiépítését kéri](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Kiépítve

Ez az állapot egy kiépített szolgáltatás állandósult állapota.

##### <a name="provisioning-for-update"></a>Kiépítés frissítéshez 

Ez az állapot azt jelenti, hogy egy meglévő szolgáltatás frissítése függőben van. Az ilyen frissítést az ügyfél kezdeményezheti, akár a piactérről, akár a SaaS szolgáltatásból (csak közvetlenül az ügyfélhez történő tranzakciók esetén).

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>Frissítés kiépítése (amikor a piactérről kezdeményezett)

Az alábbi ábra a műveletek sorrendjét mutatja be, amikor egy frissítést kezdeményez nek a piactérről.

![API-hívások, amikor a frissítés a piactérről van kezdeményezve](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>Frissítés kiépítése (ha a SaaS-szolgáltatásból kezdeményezett)

Az alábbi ábrán látható, hogy a saas-szolgáltatásból frissítést kezdeményeznek-e. (A webhook-hívást a SaaS-szolgáltatás által kezdeményezett előfizetés frissítése váltja fel.) 

![API-hívások, amikor a frissítés a SaaS-szolgáltatásból történik](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Felfüggesztve

Ez az állapot azt jelzi, hogy a vevő kifizetése nem érkezett meg. A szabályzat szerint türelmi időszakot biztosítunk az ügyfélnek az előfizetés lemondása előtt. Ha egy előfizetés ebben az állapotban van: 

- Partnerként dönthet úgy, hogy gyengíti vagy letiltja a felhasználó hozzáférését a szolgáltatáshoz.
- Az előfizetést helyreállítható állapotban kell tartani, amely az adatok vagy beállítások elvesztése nélkül vissza tudja állítani a teljes funkcionalitást. 
- Elvárják, hogy a teljesítési API-k on keresztül visszaállítsa az előfizetés visszaállítási kérelmét, vagy a türelmi időszak végén egy de-provisioning kérelmet. 

#### <a name="unsubscribed"></a>Leiratkozott 

Az előfizetések ezt az állapotot kifejezett ügyfélkérésre vagy illetékek meg nem fizetésére válaszul érik el. A partner elvárása az, hogy az ügyfél adatait kérésre bizonyos számú napig megőrizzék, majd töröljék. 


## <a name="api-reference"></a>API-referencia

Ez a szakasz a SaaS *subscription API-t* és *az Operations API-t dokumentálja.*  A 2-es verziójú API-k `api-version` paraméterének értéke . `2018-08-31`  


### <a name="parameter-and-entity-definitions"></a>Paraméter- és entitásdefiníciók

Az alábbi táblázat a teljesítési API-k által használt közös paraméterek és entitások definícióit sorolja fel.

|     Entitás/paraméter     |     Meghatározás                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | Egy SaaS-erőforrás GUID-azonosítója.  |
| `name`                   | Az erőforrásnak a vevő által megadott rövid név. |
| `publisherId`            | Az egyes közzétevők egyedi karakterlánc-azonosítója (például: "contoso"). |
| `offerId`                | Az egyes ajánlathoz egyedi karakterlánc-azonosító (például "offer1").  |
| `planId`                 | Az egyes tervekhez/termékváltozatokhoz egyedi karakterlánc-azonosító (például "ezüst"). |
| `operationId`            | Egy adott művelet GUID azonosítója.  |
|  `action`                | Az erőforráson végrehajtott művelet, `Unsubscribe` `Suspend`, `Reinstate`, `ChangePlan` `ChangeQuantity`, `Transfer`vagy , , . |
|   |   |

A globálisan egyedi azonosítók ([GUID-ok](https://en.wikipedia.org/wiki/Universally_unique_identifier)) 128 bites (32 hexadecimális) számok, amelyek általában automatikusan generálódnak. 

#### <a name="resolve-a-subscription"></a>Előfizetés feloldása 

A feloldási végpont lehetővé teszi, hogy a közzétevő feloldja a piactéri jogkivonatot egy állandó erőforrás-azonosítóra. Az erőforrás-azonosító egy SaaS-előfizetés egyedi azonosítója. Amikor egy felhasználót átirányítanak egy partner webhelyére, az URL-cím tartalmaz egy jogkivonatot a lekérdezési paraméterekben. A partnernek használnia kell ezt a jogkivonatot, és kérelmet kell benyújtania a probléma megoldásához. A válasz tartalmazza az egyedi SaaS-előfizetés-azonosítót, nevet, ajánlatazonosítót és az erőforrás tervét. Ez a jogkivonat csak egy óráig érvényes. 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Közzététel<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A kérelemhez használandó művelet verziója.  |

*Fejlécek kérése:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |  Egyedi karakterlánc-érték az ügyféltől érkező kérés nyomon követéséhez, lehetőleg GUID. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és a válaszfejlécekben megadja. |
|  x-ms-correlationid |  Az ügyfélen végzett művelet egyedi karakterlánc-értéke. Ez a paraméter korrelálja az ügyfélműveletből származó összes eseményt a kiszolgálói oldalon lévő eseményekkel. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és a válaszfejlécekben megadja.  |
|  engedélyezés     |  [JSON webes token (JWT) tulajdonosi token beszerezni.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) Például:`Bearer <access_token>`" ". |
|  x-ms-piactér-token  |  A token lekérdezési paraméter az URL-címben, amikor a felhasználó átirányítja a SaaS-partner webhelyére az Azure-ból (például: `https://contoso.com/signup?token=..`). *Megjegyzés:* Az URL-cím használat előtt dekódolja a böngésző tokenértékét.  |

*Válaszkódok:*

Kód: 200<br>
Feloldja az átlátszatlan jogkivonatot egy SaaS-előfizetés. Válasz szerv:
 

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
Rossz kérés. x-ms-marketplace-token hiányzik, hibásan formázott vagy lejárt.

Kód: 403<br>
Jogosulatlan. A hitelesítési jogkivonat nem lett megadva, vagy érvénytelen, vagy a kérelem olyan akvizícióhoz próbál hozzáférni, amely nem tartozik az aktuális közzétevőhöz.

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

### <a name="subscription-api"></a>Előfizetéses API

Az előfizetéses API a következő HTTPS-műveleteket támogatja: **Get**, **Post**, **Patch**és **Delete**.


#### <a name="list-subscriptions"></a>Előfizetések listázása

A közzétevő összes SaaS-előfizetésének listázása.

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Lekérés<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  A kérelemhez használandó művelet verziója.  |

*Fejlécek kérése:*

|                    |                   |
|  ---------------   |  ---------------  |
| Content-Type       |  `application/json`  |
| x-ms-requestid     |  Egyedi karakterlánc-érték az ügyféltől érkező kérés nyomon követéséhez, lehetőleg GUID. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és a válaszfejlécekben megadja. |
| x-ms-correlationid |  Az ügyfélen végzett művelet egyedi karakterlánc-értéke. Ez a paraméter korrelálja az ügyfélműveletből származó összes eseményt a kiszolgálói oldalon lévő eseményekkel. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és a válaszfejlécekben megadja.  |
| engedélyezés      |  [JSON webes token (JWT) tulajdonosi token beszerezni.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) Például:`Bearer <access_token>`" ".  |

*Válaszkódok:*

Kód: 200 <br/>
Lekéri a közzétevő és a megfelelő előfizetések a közzétevő ajánlatai, a hitelesítési jogkivonat alapján.

>[!Note]
>[A mock API-kat](#mock-apis) az ajánlat első fejlesztésekor használja a program, míg a valódi API-kat az ajánlat tényleges közzétételekor kell használni.  A valódi API-k és a mock API-k a kód első sorában különböznek.  A valódi API-ban `subscription` van a szakasz, míg ez a szakasz nem létezik a mock API.In the real API there is the section, while this section doesn't exist for mock API.

Válasz hasznos adata a hamis API-hoz:<br>

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
          "isFreeTrial": "true", // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial.
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```
És az igazi API:And for real API: <br>

```json
{
  "subscriptions": [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant, object id and email address for which SaaS subscription is purchased.
              "emailId": "<email>",
              "objectId": "<guid>",                     
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant, object id and email address that purchased the SaaS subscription. These could be different for reseller scenario
              "emailId": "<email>",
              "objectId": "<guid>",                      
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
          "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial.(optional field - default false)
          "isTest": false, //indicating whether the current subscription is a test asset
          "sandboxType": "None", // Possible Values: None, Csp (Csp sandbox purchase)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "@nextLink": ""
}
```
A folytatási token csak akkor lesz jelen, ha további "oldalak" vannak a tervek visszakeresésére. 

Kód: 403 <br>
Jogosulatlan. A hitelesítési jogkivonat nem lett megadva, vagy érvénytelen, vagy a kérelem olyan akvizícióhoz próbál hozzáférni, amely nem tartozik az aktuális közzétevőhöz. 

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

#### <a name="get-subscription"></a>Előfizetés beszereznie

Leveszi a megadott SaaS-előfizetést. Ezzel a hívással licencinformációkat és tervinformációkat kaphat.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Lekérés<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   A SaaS-előfizetés egyedi azonosítója, amely a jogkivonat feloldása után érhető el a Feloldás API-n keresztül.   |
|  ApiVersion        |   A kérelemhez használandó művelet verziója.   |

*Fejlécek kérése:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-ms-requestid    |  Egyedi karakterlánc-érték az ügyféltől érkező kérés nyomon követéséhez, lehetőleg GUID. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és a válaszfejlécekben megadja. |
|  x-ms-correlationid |  Az ügyfélen végzett művelet egyedi karakterlánc-értéke. Ez a paraméter korrelálja az ügyfélműveletből származó összes eseményt a kiszolgálói oldalon lévő eseményekkel. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és a válaszfejlécekben megadja.  |
|  engedélyezés     |  [JSON webes token (JWT) tulajdonosi token beszerezni.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) Például:`Bearer <access_token>`" ".  |

*Válaszkódok:*

Kód: 200<br>
Leveszi a SaaS-előfizetést az azonosítóból. Válasz hasznos teher:<br>

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
        "isFreeTrial": "true", // true - customer subscription is currently in free trial, false - customer subscription is not currently in free trial.
        "status": "Subscribed", // Indicates the status of the operation.
          "term": { //This gives the free trial term start and end date
            "startDate": "2019-05-31",
            "endDate": "2019-06-29",
            "termUnit": "P1M" //where P1M: Monthly, P1Y: Yearly 
        },
}
```

Kód: 403<br>
Jogosulatlan. A hitelesítési jogkivonat nem lett megadva, vagy érvénytelen, vagy a kérelem olyan akvizícióhoz próbál hozzáférni, amely nem tartozik az aktuális közzétevőhöz.

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

#### <a name="list-available-plans"></a>Elérhető csomagok listája

Ezzel a hívással megtudhatja, hogy vannak-e privát vagy nyilvános ajánlatok az aktuális közzétevőhöz.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Lekérés<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   A kérelemhez használandó művelet verziója.  |

*Fejlécek kérése:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-ms-requestid   |   Egyedi karakterlánc-érték az ügyféltől érkező kérés nyomon követéséhez, lehetőleg GUID. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és a válaszfejlécekben megadja. |
|  x-ms-correlationid  | Az ügyfélen végzett művelet egyedi karakterlánc-értéke. Ez a paraméter korrelálja az ügyfélműveletből származó összes eseményt a kiszolgálói oldalon lévő eseményekkel. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és a válaszfejlécekben megadja. |
|  engedélyezés     |  [JSON webes token (JWT) tulajdonosi token beszerezni.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  Például:`Bearer <access_token>`" ". |

*Válaszkódok:*

Kód: 200<br>
Egy ügyfél számára elérhető csomagok listájának lekéri. Válasz szerv:

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
Jogosulatlan. A hitelesítési jogkivonat nem lett megadva, vagy érvénytelen, vagy a kérelem olyan akvizícióhoz próbál hozzáférni, amely nem tartozik az aktuális közzétevőhöz. <br> 

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
|  ApiVersion        |  A kérelemhez használandó művelet verziója.  |
| subscriptionId     | A SaaS-előfizetés egyedi azonosítója, amely a jogkivonat feloldása után a Feloldási API használatával kapott.  |

*Fejlécek kérése:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-ms-requestid    | Egyedi karakterlánc-érték az ügyféltől érkező kérés nyomon követéséhez, lehetőleg GUID. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és a válaszfejlécekben megadja.  |
|  x-ms-correlationid  | Az ügyfélen végzett művelet egyedi karakterlánc-értéke. Ez a karakterlánc korrelálja az ügyfélműveletből származó összes eseményt a kiszolgálói oldalon lévő eseményekkel. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és a válaszfejlécekben megadja.  |
|  engedélyezés     |  [JSON webes token (JWT) tulajdonosi token beszerezni.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  Például:`Bearer <access_token>`" ". |

*Hasznos adat kérése:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*Válaszkódok:*

Kód: 200<br>
Aktiválja az előfizetést.<br>

Kód: 400<br>
Hibás kérés: érvényesítési hibák.

Kód: 403<br>
Jogosulatlan. A hitelesítési jogkivonat nem lett megadva, vagy érvénytelen, vagy a kérelem olyan akvizícióhoz próbál hozzáférni, amely nem tartozik az aktuális közzétevőhöz.

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

#### <a name="change-the-plan-on-the-subscription"></a>Az előfizetés csomagjának módosítása

Frissítse a csomagot az előfizetésen.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A kérelemhez használandó művelet verziója.  |
| subscriptionId     | A SaaS-előfizetés egyedi azonosítója, amely a jogkivonat feloldása után a Feloldási API használatával kapott.  |

*Fejlécek kérése:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Egyedi karakterlánc-érték az ügyféltől érkező kérés nyomon követéséhez, lehetőleg GUID. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és a válaszfejlécekben megadja.  |
|  x-ms-correlationid  |  Az ügyfélen végzett művelet egyedi karakterlánc-értéke. Ez a paraméter korrelálja az ügyfélműveletből származó összes eseményt a kiszolgálói oldalon lévő eseményekkel. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és a válaszfejlécekben megadja.    |
| engedélyezés      |  [JSON webes token (JWT) tulajdonosi token beszerezni.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  Például:`Bearer <access_token>`" ".  |

*Hasznos adat kérése:*

```json
Request Body:
{
    "planId": "gold"
}
```

*Fejlécek kérése:*

|                    |                   |
|  ---------------   |  ---------------  |
| Művelet-hely | A művelet állapotának leválasztásához egy erőforrásra mutató hivatkozás.   |

*Válaszkódok:*

Kód: 202<br>
A terv módosítására vonatkozó kérés tanuskó immár elfogadásra került. A partnernek le kell hallgatnia a művelet helyét a sikeres vagy sikertelen ség meghatározásához. <br>

Kód: 400<br>
Hibás kérés: érvényesítési hibák.

Kód: 403<br>
Jogosulatlan. A hitelesítési jogkivonat nem lett megadva, vagy érvénytelen, vagy a kérelem olyan akvizícióhoz próbál hozzáférni, amely nem tartozik az aktuális közzétevőhöz.

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
>Egyszerre csak egy tervet vagy mennyiséget lehet foltozni, mindkettőt nem. A **Frissítés** szolgáltatással rendelkező előfizetés `allowedCustomerOperations`szerkesztései nincsenek benne.

#### <a name="change-the-quantity-on-the-subscription"></a>Az előfizetés ben szereplő mennyiség módosítása

Frissítse az előfizetésben szereplő mennyiséget.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Javítás:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A kérelemhez használandó művelet verziója.  |
| subscriptionId     | A SaaS-előfizetés egyedi azonosítója, amely a jogkivonat feloldása után a Feloldási API használatával kapott.  |

*Fejlécek kérése:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Egyedi karakterlánc-érték az ügyféltől érkező kérés nyomon követéséhez, lehetőleg GUID. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és a válaszfejlécekben megadja.  |
|  x-ms-correlationid  |  Az ügyfélen végzett művelet egyedi karakterlánc-értéke. Ez a paraméter korrelálja az ügyfélműveletből származó összes eseményt a kiszolgálói oldalon lévő eseményekkel. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és a válaszfejlécekben megadja.    |
| engedélyezés      |  [JSON webes token (JWT) tulajdonosi token beszerezni.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  Például:`Bearer <access_token>`" ".  |

*Hasznos adat kérése:*

```json
Request Body:
{
    "quantity": 5
}
```

*Fejlécek kérése:*

|                    |                   |
|  ---------------   |  ---------------  |
| Művelet-hely | Hivatkozás egy erőforrásra a művelet állapotának leválasztásához.   |

*Válaszkódok:*

Kód: 202<br>
A mennyiség módosítására vonatkozó kérés tanuskó immár elfogadásra került. A partnernek le kell hallgatnia a művelet helyét a sikeres vagy sikertelen ség meghatározásához. <br>

Kód: 400<br>
Hibás kérés: érvényesítési hibák.


Kód: 403<br>
Jogosulatlan. A hitelesítési jogkivonat nem lett megadva, vagy érvénytelen, vagy a kérelem olyan akvizícióhoz próbál hozzáférni, amely nem tartozik az aktuális közzétevőhöz.

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
>Egyszerre csak egy tervet vagy mennyiséget lehet foltozni, mindkettőt nem. A **Frissítés** szolgáltatással rendelkező előfizetés `allowedCustomerOperations`szerkesztései nincsenek benne.

#### <a name="delete-a-subscription"></a>Előfizetés törlése

Leiratkozhat és törölheti a megadott előfizetést.

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Törlés<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A kérelemhez használandó művelet verziója.  |
| subscriptionId     | A SaaS-előfizetés egyedi azonosítója, amely a jogkivonat feloldása után a Feloldási API használatával kapott.  |

*Fejlécek kérése:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |   Egyedi karakterlánc-érték az ügyféltől érkező kérés nyomon követéséhez, lehetőleg GUID. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és a válaszfejlécekben megadja.   |
|  x-ms-correlationid  |  Az ügyfélen végzett művelet egyedi karakterlánc-értéke. Ez a paraméter korrelálja az ügyfélműveletből származó összes eseményt a kiszolgálói oldalon lévő eseményekkel. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és a válaszfejlécekben megadja.   |
|  engedélyezés     |  [JSON webes token (JWT) tulajdonosi token beszerezni.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  Például:`Bearer <access_token>`" ".  |

*Válaszkódok:*

Kód: 202<br>
A partner hívást kezdeményezett egy SaaS-előfizetés leiratkozására.<br>

Kód: 400<br>
Törlés olyan előfizetésen, `allowedCustomerOperations` **amelyen** a Törlés nincs a ban.

Kód: 403<br>
Jogosulatlan. A hitelesítési jogkivonat nem lett megadva, vagy érvénytelen, vagy a kérelem olyan akvizícióhoz próbál hozzáférni, amely nem tartozik az aktuális közzétevőhöz.

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


### <a name="operations-api"></a>Műveletek API

A műveletek API támogatja a következő Patch és get műveleteket.

#### <a name="list-outstanding-operations"></a>Függőben lévő műveletek listája 

Az aktuális közzétevő függőben lévő műveleteinek listája. 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Lekérés<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   A kérelemhez használandó művelet verziója.                |
| subscriptionId     | A SaaS-előfizetés egyedi azonosítója, amely a jogkivonat feloldása után a Feloldási API használatával kapott.  |

*Fejlécek kérése:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |  Egyedi karakterlánc-érték az ügyféltől érkező kérés nyomon követéséhez, lehetőleg GUID. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és a válaszfejlécekben megadja.  |
|  x-ms-correlationid |  Az ügyfélen végzett művelet egyedi karakterlánc-értéke. Ez a paraméter korrelálja az ügyfélműveletből származó összes eseményt a kiszolgálói oldalon lévő eseményekkel. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és a válaszfejlécekben megadja.  |
|  engedélyezés     |  [JSON webes token (JWT) tulajdonosi token beszerezni.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  Például:`Bearer <access_token>`" ".  |

*Válaszkódok:*

Kód: 200<br> Leképezi az előfizetés függőben lévő műveleteinek listáját. Válasz hasznos teher:

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
Hibás kérés: érvényesítési hibák.

Kód: 403<br>
Jogosulatlan. A hitelesítési jogkivonat nem lett megadva, vagy érvénytelen, vagy a kérelem olyan akvizícióhoz próbál hozzáférni, amely nem tartozik az aktuális közzétevőhöz.

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

#### <a name="get-operation-status"></a>A művelet állapotának beírása

Lehetővé teszi, hogy a közzétevő nyomon kövesse a `Subscribe`megadott `Unsubscribe` `ChangePlan`aktivált `ChangeQuantity`aszinkron művelet (például , , , vagy ) állapotát.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Lekérés<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A kérelemhez használandó művelet verziója.  |

*Fejlécek kérése:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`   |
|  x-ms-requestid    |   Egyedi karakterlánc-érték az ügyféltől érkező kérés nyomon követéséhez, lehetőleg GUID. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és a válaszfejlécekben megadja.  |
|  x-ms-correlationid |  Az ügyfélen végzett művelet egyedi karakterlánc-értéke. Ez a paraméter korrelálja az ügyfélműveletből származó összes eseményt a kiszolgálói oldalon lévő eseményekkel. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és a válaszfejlécekben megadja.  |
|  engedélyezés     |  [JSON webes token (JWT) tulajdonosi token beszerezni.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) Például:`Bearer <access_token>`" ".  |

*Válaszkódok:*<br>

Kód: 200<br> Leképezi a megadott függőben lévő SaaS-műveletet. Válasz hasznos teher:

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
Hibás kérés: érvényesítési hibák.

Kód: 403<br>
Jogosulatlan. A hitelesítési jogkivonat nem lett megadva, vagy érvénytelen, vagy a kérelem olyan akvizícióhoz próbál hozzáférni, amely nem tartozik az aktuális közzétevőhöz.
 
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

Frissítse egy művelet állapotát, hogy jelezze a megadott értékek sikeres vagy sikertelen teljesítését.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Patch<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  A kérelemhez használandó művelet verziója.  |
| subscriptionId     | A SaaS-előfizetés egyedi azonosítója, amely a jogkivonat feloldása után a Feloldási API használatával kapott.  |
|  operationId       | A művelet, ami folyamatban van. |

*Fejlécek kérése:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     | `application/json`   |
|   x-ms-requestid   |   Egyedi karakterlánc-érték az ügyféltől érkező kérés nyomon követéséhez, lehetőleg GUID. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és a válaszfejlécekben megadja. |
|  x-ms-correlationid |  Az ügyfélen végzett művelet egyedi karakterlánc-értéke. Ez a paraméter korrelálja az ügyfélműveletből származó összes eseményt a kiszolgálói oldalon lévő eseményekkel. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és a válaszfejlécekben megadja. |
|  engedélyezés     |  [JSON webes token (JWT) tulajdonosi token beszerezni.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  Például:`Bearer <access_token>`" ".  |

*Hasznos adat kérése:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*Válaszkódok:*

Kód: 200<br> A partneroldalon végzett művelet befejezéséről szóló felhívás. Ez a válasz például jelezheti az ülőhelyek vagy tervek megváltoztatását.

Kód: 400<br>
Hibás kérés: érvényesítési hibák.

Kód: 403<br>
Jogosulatlan. A hitelesítési jogkivonat nem lett megadva, vagy érvénytelen, vagy a kérelem olyan akvizícióhoz próbál hozzáférni, amely nem tartozik az aktuális közzétevőhöz.

Kód: 404<br>
Nem található.

Kód: 409<br>
Konfliktus. Például egy újabb tranzakció már teljesült.

Kód: 500<br> Belső kiszolgálóhiba.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="implementing-a-webhook-on-the-saas-service"></a>Webhook megvalósítása a SaaS szolgáltatáson

A közzétevőnek egy webhookot kell megvalósítania ebben a SaaS-szolgáltatásban, hogy proaktív módon értesítse a felhasználókat a szolgáltatás változásairól. A SaaS-szolgáltatás várhatóan meghívja az operations API-t, hogy ellenőrizze és engedélyezze, mielőtt egy műveletet a webhook értesítés.


```json
{
  "id": "<this is a GUID operation id, you can call operations API with this to get status>",
  "activityId": "<this is a Guid correlation id>",
  "subscriptionId": "<Guid to uniquely identify this resource>",
  "publisherId": "<this is the publisher's name>",
  "offerId": "<this is the offer name>",
  "planId": "<this is the plan id>",
  "quantity": "<the number of seats, will be null if not per-seat saas offer>",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Unsubscribe",
  "status": "NotStarted"  

}
```
Amennyiben a cselekvés a következők egyike lehet: 
- `Unsubscribe`(ha az erőforrást törölték)
- `ChangePlan`(ha a változásterv-művelet befejeződött)
- `ChangeQuantity`(ha a változási mennyiség művelet befejeződött)
- `Suspend`(ha az erőforrást felfüggesztették)
- `Reinstate`(ha az erőforrást a felfüggesztés után visszaállították)

Ahol az állapot a következők egyike lehet: 
- **Nincs beindítva** <br>
 - **Folyamatban** <br>
- **Sikeres** <br>
- **Nem sikerült** <br>
- **Konfliktus** <br>

A webhook-értesítésben a végrehajtható állapotok **sikeresek** és **sikertelenek.** Egy művelet életciklusa a **NotStarted** -tól a terminál állapotig , például **Sikeres**, **Sikertelen**vagy **Ütközés**. Ha **notstarted** vagy **inprogress -t**kap, folytassa az állapot kéréséhez a GET API-n keresztül, amíg a művelet el nem éri a terminálállapotot a művelet megkezdése előtt. 

## <a name="mock-apis"></a>Mock API-k

Ál-API-inkkal segítheta fejlesztés, különösen a prototípusok készítése és a projektek tesztelése. 

Állomásvégpont: `https://marketplaceapi.microsoft.com/api` (nincs szükség hitelesítésre)<br/>
API-verzió:`2018-09-15`<br/>
URI-minta:`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

Az API-végpont elérési útjai ugyanazok a mock és a valós API-k között, de az API-verziók eltérőek. A verzió `2018-09-15` a modell `2018-08-31` verzióra és a gyártási verzióra. 

A cikkben szereplő API-hívások bármelyike a modell gazdagép végpontjára is elvégezhető. Általában elvárják, hogy a modell adatokat válaszként visszakapja. A modell API-n a frissítési előfizetési módszerek hívásai mindig 500-at adnak vissza. 

## <a name="next-steps"></a>További lépések

A fejlesztők a [Cloud Partner Portal REST API-jainak](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)használatával programozott módon is lekérhetik és kezelhetik a számítási feladatokat, ajánlatokat és közzétevői profilokat.
