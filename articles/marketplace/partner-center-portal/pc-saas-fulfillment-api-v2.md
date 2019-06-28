---
title: SaaS API teljesítése v2 |} Az Azure Marketplace-en
description: Ez a cikk azt ismerteti, hogyan hozhat létre és kezelhet egy SaaS-ajánlat az appsource-ban és az Azure Marketplace-en a társított teljesítése használatával v2 API-k.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: ecee1669c29d7b298741f9e5521de03da6dd7e3b
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331632"
---
# <a name="saas-fulfillment-apis-version-2"></a>SaaS-teljesítési API-k, 2-es verzió 

Ez a cikk részletesen az API-k, amelyek lehetővé teszik, hogy azok SaaS alkalmazásokat az AppSource piactérről, és az Azure Marketplace-en a partnerek. Ezen API-k, transactable SaaS követelmény kínál az appsource-ban és az Azure Marketplace-en.

## <a name="managing-the-saas-subscription-life-cycle"></a>Az SaaS-előfizetési életciklus kezelése

Azure SaaS egy SaaS-előfizetés megvásárlása teljes életciklusát felügyeli. Azt mechanizmusként, a tényleges teljesítése érdekében használja az API-k teljesítési, csomagok és az előfizetés, a partnerrel való törlését változik. Az ügyfél számlázási alapul, amely a Microsoft fenntartja az SaaS-előfizetés állapotát. Az alábbi ábra bemutatja az állapotok és a műveletek, amelyek a módosítások meghajtó állapotok között.

![SaaS-előfizetési életciklus-állapotok](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>Az SaaS-előfizetés állapotok

A következő táblázat felsorolja a kiépítési állapotok SaaS-előfizetéssel, többek között a leírása és sorrendje diagram egyes (ha van). 

#### <a name="provisioning"></a>Kiépítés

Ha egy ügyfél kezdeményezi a beszerzés, a partner ezeket az adatokat egy URL paramétert használó ügyfél interaktív webhelyen egy engedélyezési kód fogadása. Például `https://contoso.com/signup?token=..`, mivel a Partner Center alkotóelemeit oldal URL-cím `https://contoso.com/signup`. Az engedélyezési kód érvényesítve, és a kiépítési szolgáltatás részleteit a megoldásához API meghívásával átadott is.  Amikor egy SaaS-szolgáltatás befejezi a kiépítés, küld egy aktiválás hívás, hogy jelezze, hogy a teljesítése befejeződött, és az ügyfél számlázható. 

Az alábbi ábrán látható az üzembe helyezési forgatókönyv esetén az API-hívások sorrendjét.  

![API-khoz egy SaaS-szolgáltatás kiépítése](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Kiépítve

Ebben az állapotban a kiépített szolgáltatás stabil állapotban.

##### <a name="provisioning-for-update"></a>Frissítés kiépítés 

Ez az állapot azt jelzi, hogy a meglévő szolgáltatások frissítés függőben. Ennek a frissítésnek a vásárlók a marketplace-ről vagy a SaaS-szolgáltatás (csak a közvetlen ügyfelek tranzakció) kezdeményezhetők.

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>Frissítés kiépítés (Ha ezt a marketplace-ről)

Az alábbi ábrán látható műveletek sorrendjét, a marketplace-ről egy frissítés elindításakor.

![API-hívások, a frissítés elindításakor a marketplace-ről](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>Frissítés kiépítés (Ha ezt a SaaS-szolgáltatás)

Az alábbi ábrán látható a műveleteket, ha egy frissítés kezdeményezi a SaaS-szolgáltatás. (A webhook hívása az előfizetéshez, az SaaS-szolgáltatás által kezdeményezett frissítés helyébe.) 

![A frissítés elindításakor a SaaS-szolgáltatás API-hívások](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Felfüggesztve

Ez az állapot azt jelzi, hogy a felhasználó fizetési még nem érkeztek. Szabályzat által biztosítunk az ügyfél egy türelmi időszakot, az előfizetés lemondása előtt. Ha egy előfizetési ebben az állapotban van: 

- Partnerként csökkentheti a, vagy letiltja a szolgáltatáshoz való hozzáférést a felhasználó választhatja.
- Az előfizetés, amely képes a beállítások és adatok adatveszteség nélküli összes funkciójának helyreállításához helyreállítható állapotban kell tartani. 
- Várható az érvényességének visszaállítása kérelem beolvasni az előfizetés az API-k teljesítési keresztül vagy vonja kiépítési kérést a türelmi időszak végén. 

#### <a name="unsubscribed"></a>Leiratkozott 

Előfizetések érhető el ebben az állapotban explicit ügyfélkérés vagy adóhatóság díjak az adott válaszként. A partnertől elvárás, hogy az ügyfél adatainak helyreállítási kérésre adott számú napig őrzi meg a rendszer, és ezután törölve. 


## <a name="api-reference"></a>API-referencia

Ez a szakasz a SaaS-dokumentumok *előfizetés API* és *műveleti API*.  Értékét a `api-version` 2-es verziójú API-k paramétere `2018-08-31`.  


### <a name="parameter-and-entity-definitions"></a>A paraméter és az entitás-definíciók

Az alábbi táblázat a gyakori paramétereket és teljesítése API-k által használt entitások definícióit.

|     A paraméter/entitás     |     Meghatározás                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | Egy SaaS-erőforrás GUID azonosítója.  |
| `name`                   | Egy rövid nevet, az ügyfél által megadott ehhez az erőforráshoz. |
| `publisherId`            | Minden közzétevő egy egyedi karakterlánc-azonosító (például: "contoso"). |
| `offerId`                | Minden ajánlat esetében egy egyedi karakterlánc-azonosító (például: "offer1").  |
| `planId`                 | Az egyes csomagot/SKU egy egyedi karakterlánc-azonosító (például: "silver"). |
| `operationId`            | Egy adott művelet GUID azonosítója.  |
|  `action`                | A művelet végrehajtása egy erőforrást, vagy `subscribe`, `unsubscribe`, `suspend`, `reinstate`, vagy `changePlan`, `changeQuantity`, `transfer`.  |
|   |   |

Globálisan egyedi azonosítóra ([GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)) 128 bites (32 hexadecimális) szám, amely általában automatikusan jönnek létre. 

#### <a name="resolve-a-subscription"></a>Oldja meg az előfizetés 

A feloldás végpontot lehetővé teszi, hogy a közzétevő számára, hogy oldja fel a Marketplace-en jogkivonatot egy állandó erőforrás-azonosítója. Az erőforrás-azonosítója a SaaS-előfizetéshez tartozó egyedi azonosítója. Amikor egy felhasználó a rendszer átirányítja a partner webhelyére, az URL-címet tartalmaz egy jogkivonatot a lekérdezési paraméterek. A partner a token használatának és a megoldásának kérés kellene szerepelnie. A válasz egyedi SaaS előfizetés-azonosító, nevét, Ajánlatazonosító, és az erőforrás tervezése tartalmaz. Ez a token érvényességét csak egy órán keresztül. 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Post<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A művelet ehhez a kérelemhez használt verziója.  |

*Kérelem fejlécei:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |  A kérelem követési az ügyfélről, lehetőleg a GUID egyedi karakterlánc-érték. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott. |
|  x-ms-correlationid |  A műveletet az ügyfél egyedi karakterlánc-érték. Ez a paraméter utal. a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.  |
|  authorization     |  [JSON webes jogkivonat (JWT) tulajdonosi jogkivonat beszerzése](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). |
|  x-ms-marketplace-token  |  Az URL-címben, amikor a felhasználó a rendszer átirányítja az SaaS-partnerek webhelyet az Azure-ból a token lekérdezési paraméter (például: `https://contoso.com/signup?token=..`). *Megjegyzés:* Az URL-cím használat előtt a böngészőből a token érték dekódolása.  |

*Válaszkódot:*

Kód: 200<br>
Oldja fel a átlátszatlan token SaaS-előfizetésre. Válasz törzsében:
 

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
Hibás kérés. x-ms-piactér-token hiányzik, hibás vagy elévült.

Kód: 403<br>
Nem engedélyezett. A hitelesítési jogkivonat nincs megadva vagy érvénytelen, vagy a kérelem próbál hozzáférni, amely nem tartozik a jelenlegi közzétevő beszerzés.

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

Az előfizetés API a következő HTTPS műveleteket támogatja: **Első**, **Post**, **javítás**, és **törlése**.


#### <a name="list-subscriptions"></a>Az előfizetések listája

A közzétevő SaaS-előfizetések listája.

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Lekérés<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  A művelet ehhez a kérelemhez használt verziója.  |

*Kérelem fejlécei:*

|                    |                   |
|  ---------------   |  ---------------  |
| Content-Type       |  `application/json`  |
| x-ms-requestid     |  A kérelem követési az ügyfélről, lehetőleg a GUID egyedi karakterlánc-érték. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott. |
| x-ms-correlationid |  A műveletet az ügyfél egyedi karakterlánc-érték. Ez a paraméter utal. a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.  |
| authorization      |  [JSON webes jogkivonat (JWT) tulajdonosi jogkivonat beszerzése](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Válaszkódot:*

Kód: 200 <br/>
A közzétevő és a kiadó ajánlatok esetében a hitelesítési jogkivonat alapján megfelelő előfizetések beolvasása.
Válasz tartalma:<br>

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
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

A folytatási token csak akkor, ha nincsenek további "lapok" lekérdezni a csomagokat lesz. 

Kód: 403 <br>
Nem engedélyezett. A hitelesítési jogkivonat nincs megadva vagy érvénytelen, vagy a kérelem próbál hozzáférni, amely nem tartozik a jelenlegi közzétevő beszerzés. 

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

Lekérdezi a megadott SaaS-előfizetés. Ez a hívás használatával licencelési információk lekérése és tervinformációi szerint.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Lekérés<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   Az SaaS-előfizetés, hogy a jogkivonat feloldása API-n keresztül megoldása után egyedi azonosítója.   |
|  ApiVersion        |   A művelet ehhez a kérelemhez használt verziója.   |

*Kérelem fejlécei:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-ms-requestid    |  A kérelem követési az ügyfélről, lehetőleg a GUID egyedi karakterlánc-érték. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott. |
|  x-ms-correlationid |  A műveletet az ügyfél egyedi karakterlánc-érték. Ez a paraméter utal. a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.  |
|  authorization     |  [JSON webes jogkivonat (JWT) tulajdonosi jogkivonat beszerzése](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). |

*Válaszkódot:*

Kód: 200<br>
Az SaaS-előfizetési azonosító beolvasása. Válasz tartalma:<br>

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
        "status": "Subscribed", // Indicates the status of the operation.
}
```

Kód: 403<br>
Nem engedélyezett. A hitelesítési jogkivonat nincs megadva vagy érvénytelen, vagy a kérelem próbál hozzáférni, amely nem tartozik a jelenlegi közzétevő beszerzés.

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

Ez a hívás segítségével ismerje meg, hogy vannak-e a jelenlegi közzétevő bármely nyilvános vagy privát ajánlat.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Lekérés<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   A művelet ehhez a kérelemhez használt verziója.  |

*Kérelem fejlécei:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-ms-requestid   |   A kérelem követési az ügyfélről, lehetőleg a GUID egyedi karakterlánc-érték. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott. |
|  x-ms-correlationid  | A műveletet az ügyfél egyedi karakterlánc-érték. Ez a paraméter utal. a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott. |
|  authorization     |  [JSON webes jogkivonat (JWT) tulajdonosi jogkivonat beszerzése](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). |

*Válaszkódot:*

Kód: 200<br>
Elérhető csomagok listáját az ügyfél kéri le. Válasz törzsében:

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
Nem engedélyezett. A hitelesítési jogkivonat nincs megadva vagy érvénytelen, vagy a kérelem próbál hozzáférni, amely nem tartozik a jelenlegi közzétevő beszerzés. <br> 

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

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Post<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A művelet ehhez a kérelemhez használt verziója.  |
| subscriptionId     | Az SaaS-előfizetés, hogy a megoldásához API-val a token megoldása után egyedi azonosítója.  |

*Kérelem fejlécei:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-ms-requestid    | A kérelem követési az ügyfélről, lehetőleg a GUID egyedi karakterlánc-érték. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.  |
|  x-ms-correlationid  | A műveletet az ügyfél egyedi karakterlánc-érték. Ez a karakterlánc utal. a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.  |
|  authorization     |  [JSON webes jogkivonat (JWT) tulajdonosi jogkivonat beszerzése](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). |

*Kérelem hasznos adatai:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*Válaszkódot:*

Kód: 200<br>
Aktiválja az előfizetést.<br>

Kód: 400<br>
Hibás kérés: az érvényesítési hibák.

Kód: 403<br>
Nem engedélyezett. A hitelesítési jogkivonat nincs megadva vagy érvénytelen, vagy a kérelem próbál hozzáférni, amely nem tartozik a jelenlegi közzétevő beszerzés.

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

#### <a name="change-the-plan-on-the-subscription"></a>A csomag az előfizetés módosításához

A terv az előfizetés frissítése.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Javítás<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A művelet ehhez a kérelemhez használt verziója.  |
| subscriptionId     | Az SaaS-előfizetés, hogy a megoldásához API-val a token megoldása után egyedi azonosítója.  |

*Kérelem fejlécei:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   A kérelem követési az ügyfélről, lehetőleg a GUID egyedi karakterlánc-érték. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.  |
|  x-ms-correlationid  |  A műveletet az ügyfél egyedi karakterlánc-érték. Ez a paraméter utal. a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.    |
| authorization      |  [JSON webes jogkivonat (JWT) tulajdonosi jogkivonat beszerzése](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Kérelem hasznos adatai:*

```json
Request Body:
{
    "planId": "gold"
}
```

*Kérelem fejlécei:*

|                    |                   |
|  ---------------   |  ---------------  |
| A művelet-helye | A hivatkozás egy erőforrást a műveleti állapotának beolvasása.   |

*Válaszkódot:*

Kód: 202<br>
Csomag módosítására irányuló kérelem el lett fogadva. A partner várhatóan lekérdezni egy sikerességének vagy sikertelenségének megállapításához művelet-helyét. <br>

Kód: 400<br>
Hibás kérés: az érvényesítési hibák.

Kód: 403<br>
Nem engedélyezett. A hitelesítési jogkivonat nincs megadva vagy érvénytelen, vagy a kérelem próbál hozzáférni, amely nem tartozik a jelenlegi közzétevő beszerzés.

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
>Csak egy csomagot vagy a mennyiség egyszerre javított, mindkettő nem lehet. Az egy adott előfizetés szerkesztése **frissítés** nem szerepel a `allowedCustomerOperations`.

#### <a name="change-the-quantity-on-the-subscription"></a>Az előfizetés mennyiségének módosítása

A mennyiség, az előfizetés frissítése.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Javítás:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A művelet ehhez a kérelemhez használt verziója.  |
| subscriptionId     | Az SaaS-előfizetés, hogy a megoldásához API-val a token megoldása után egyedi azonosítója.  |

*Kérelem fejlécei:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   A kérelem követési az ügyfélről, lehetőleg a GUID egyedi karakterlánc-érték. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.  |
|  x-ms-correlationid  |  A műveletet az ügyfél egyedi karakterlánc-érték. Ez a paraméter utal. a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.    |
| authorization      |  [JSON webes jogkivonat (JWT) tulajdonosi jogkivonat beszerzése](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Kérelem hasznos adatai:*

```json
Request Body:
{
    "quantity": 5
}
```

*Kérelem fejlécei:*

|                    |                   |
|  ---------------   |  ---------------  |
| A művelet-helye | A műveleti állapotának beolvasása az erőforrás csatolása.   |

*Válaszkódot:*

Kód: 202<br>
Mennyiségének módosítása a kérést elfogadták. A partner várhatóan lekérdezni egy sikerességének vagy sikertelenségének megállapításához művelet-helyét. <br>

Kód: 400<br>
Hibás kérés: az érvényesítési hibák.


Kód: 403<br>
Nem engedélyezett. A hitelesítési jogkivonat nincs megadva vagy érvénytelen, vagy a kérelem próbál hozzáférni, amely nem tartozik a jelenlegi közzétevő beszerzés.

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
>Csak egy csomagot vagy a mennyiség egyszerre javított, mindkettő nem lehet. Az egy adott előfizetés szerkesztése **frissítés** nem szerepel a `allowedCustomerOperations`.

#### <a name="delete-a-subscription"></a>Töröl egy előfizetést

Előfizetés lemondása, és törölje a megadott előfizetéshez.

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionid-api-versionapiversion"></a>Törlés<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A művelet ehhez a kérelemhez használt verziója.  |
| subscriptionId     | Az SaaS-előfizetés, hogy a megoldásához API-val a token megoldása után egyedi azonosítója.  |

*Kérelem fejlécei:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |   A kérelem követési az ügyfélről, lehetőleg a GUID egyedi karakterlánc-érték. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.   |
|  x-ms-correlationid  |  A műveletet az ügyfél egyedi karakterlánc-érték. Ez a paraméter utal. a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.   |
|  authorization     |  [JSON webes jogkivonat (JWT) tulajdonosi jogkivonat beszerzése](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Válaszkódot:*

Kód: 202<br>
A partner egy SaaS-előfizetés leiratkoztatni hívást kezdeményezni.<br>

Kód: 400<br>
Az egy adott előfizetés törlése **törlése** nem `allowedCustomerOperations`.

Kód: 403<br>
Nem engedélyezett. A hitelesítési jogkivonat nincs megadva vagy érvénytelen, vagy a kérelem próbál hozzáférni, amely nem tartozik a jelenlegi közzétevő beszerzés.

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


### <a name="operations-api"></a>Műveleti API

A műveletek API támogatja a következő javítás és a Get műveletek.

#### <a name="list-outstanding-operations"></a>Függőben lévő műveletek listázása 

A jelenlegi közzétevő a szálankénti függőben lévő műveletek listája. 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Lekérés<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   A művelet ehhez a kérelemhez használt verziója.                |
| subscriptionId     | Az SaaS-előfizetés, hogy a megoldásához API-val a token megoldása után egyedi azonosítója.  |

*Kérelem fejlécei:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |  A kérelem követési az ügyfélről, lehetőleg a GUID egyedi karakterlánc-érték. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.  |
|  x-ms-correlationid |  A műveletet az ügyfél egyedi karakterlánc-érték. Ez a paraméter utal. a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.  |
|  authorization     |  [JSON webes jogkivonat (JWT) tulajdonosi jogkivonat beszerzése](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Válaszkódot:*

Kód: 200<br> Függőben lévő előfizetés műveletek listájának beolvasása. Válasz tartalma:

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
Hibás kérés: az érvényesítési hibák.

Kód: 403<br>
Nem engedélyezett. A hitelesítési jogkivonat nincs megadva vagy érvénytelen, vagy a kérelem próbál hozzáférni, amely nem tartozik a jelenlegi közzétevő beszerzés.

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

#### <a name="get-operation-status"></a>Műveleti állapotának beolvasása

Lehetővé teszi, hogy a megadott aktivált aszinkron művelet állapotának nyomon követését a közzétevő (például `subscribe`, `unsubscribe`, `changePlan`, vagy `changeQuantity`).

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Lekérés<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A művelet ehhez a kérelemhez használt verziója.  |

*Kérelem fejlécei:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`   |
|  x-ms-requestid    |   A kérelem követési az ügyfélről, lehetőleg a GUID egyedi karakterlánc-érték. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.  |
|  x-ms-correlationid |  A műveletet az ügyfél egyedi karakterlánc-érték. Ez a paraméter utal. a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.  |
|  authorization     |  [JSON webes jogkivonat (JWT) tulajdonosi jogkivonat beszerzése](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Válaszkódot:*<br>

Kód: 200<br> Függőben lévő SaaS-művelet a megadott beolvasása. Válasz tartalma:

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
Hibás kérés: az érvényesítési hibák.

Kód: 403<br>
Nem engedélyezett. A hitelesítési jogkivonat nincs megadva vagy érvénytelen, vagy a kérelem próbál hozzáférni, amely nem tartozik a jelenlegi közzétevő beszerzés.
 
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
#### <a name="update-the-status-of-an-operation"></a>Egy művelet állapotának frissítése

Frissítse a sikerességet vagy sikertelenséget a megadott értékekkel a művelet állapotát.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Javítás<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  A művelet ehhez a kérelemhez használt verziója.  |
| subscriptionId     | Az SaaS-előfizetés, hogy a megoldásához API-val a token megoldása után egyedi azonosítója.  |
|  operationId       | A művelet, amely alatt befejeződött. |

*Kérelem fejlécei:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     | `application/json`   |
|   x-ms-requestid   |   A kérelem követési az ügyfélről, lehetőleg a GUID egyedi karakterlánc-érték. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott. |
|  x-ms-correlationid |  A műveletet az ügyfél egyedi karakterlánc-érték. Ez a paraméter utal. a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott. |
|  authorization     |  [JSON webes jogkivonat (JWT) tulajdonosi jogkivonat beszerzése](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Kérelem hasznos adatai:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*Válaszkódot:*

Kód: 200<br> A partner oldalon művelet befejezéséről tájékoztatja hívását. Ez a válasz például a módosítást, a munkaállomások vagy tervek sikerült jelezni.

Kód: 400<br>
Hibás kérés: az érvényesítési hibák.

Kód: 403<br>
Nem engedélyezett. A hitelesítési jogkivonat nincs megadva vagy érvénytelen, vagy a kérelem próbál hozzáférni, amely nem tartozik a jelenlegi közzétevő beszerzés.

Kód: 404<br>
Nem található.

Kód: 409<br>
Ütközés történt. Ha például egy újabb tranzakció már teljesítette.

Kód: 500<br> Belső kiszolgálóhiba.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="implementing-a-webhook-on-the-saas-service"></a>Amelyek segítenek a webhook a SaaS-szolgáltatás

A közzétevő meg kell valósítania egy webhook proaktívan értesíti a felhasználókat a szolgáltatás változásaira, a SaaS-szolgáltatás. Az SaaS-szolgáltatás várhatóan az műveletek érvényesítéséhez és a egy műveletet a webhook értesítésre előtt engedélyezze az API meghívása.

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
Ha a művelet lehet a következők egyikét: 
- `subscribe` (ha az erőforrás aktiválva van)
- `unsubscribe` (ha az erőforrás törlése)
- `changePlan` (Ha befejeződött a terv műveletet)
- `changeQuantity` (Ha befejeződött a mennyiség műveletet)
- `suspend` (Ha erőforrás fel lett függesztve)
- `reinstate` (Ha erőforrás rendelkezik lett vizsgadíj felfüggesztés után)

Ha az állapot lehet a következők egyikét: 
- **NotStarted** <br>
 - **InProgress** <br>
- **Succeeded** <br>
- **Nem sikerült** <br>
- **Ütközés** <br>

A webhook értesítést, gyakorlatban hasznosítható állapotok a vagy **sikeres** és **sikertelen**. Van egy művelet életciklusának **NotStarted** , például egy állapotot **sikeres**, **sikertelen**, vagy **ütközés**. Ha **NotStarted** vagy **InProgress**, továbbra is igényelhet az állapot beolvasása API-n keresztül, addig, amíg a művelet feldolgozása műveletek végrehajtása előtti. 

## <a name="mock-apis"></a>API-k utánzása

Utánzatként funkcionáló Jaink használatával segítséget nyújtanak fejlesztési feladatokhoz, különösen prototípus-készítéshez, valamint tesztelési projekteket használatának első lépései. 

Végponti gazdagép: `https://marketplaceapi.microsoft.com/api` (nincs hitelesítés szükséges)<br/>
API-verzió: `2018-09-15`<br/>
URI-minta: `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

Az API-végpont elérési utak azonosak utánzatként funkcionáló és a valós API-kat, de az API-verziókban eltérőek. A verzió `2018-09-15` utánzatként funkcionáló verziójának és `2018-08-31` éles verziójának. 

Ebben a cikkben az API-hívások bármelyikét a utánzatként funkcionáló gazdagép-végpontra is végezhető. Általánosságban véve utánzatként funkcionáló adatok vissza válaszként várható. A frissítés előfizetés metódusok utánzatként funkcionáló API bármikor visszatérhet az 500-as. 

## <a name="next-steps"></a>További lépések

A fejlesztők szoftveresen is lekérése és módosítására a számítási feladatok, az ajánlatok és a közzétevő profilok használatával a [Cloud Partner Portal REST API-k](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
