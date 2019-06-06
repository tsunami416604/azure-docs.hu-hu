---
title: SaaS API teljesítése v2 |} Az Azure Marketplace-en
description: Azt ismerteti, hogyan hozhat létre és kezelhet egy SaaS-ajánlat az appsource-ban és az Azure Marketplace-en a társított teljesítése használatával v2 API-k.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: 1aba0ab7083c437210166d2d5a2d77e7a657afe9
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474578"
---
# <a name="saas-fulfillment-apis-version-2"></a>SaaS teljesítése API 2-es verzió 

Ez a cikk ismerteti az API-t, amely lehetővé teszi a független szoftvergyártók (ISV), hogy az SaaS-alkalmazásokat az appsource-ban és az Azure Marketplace-en. Ez az API-ját transactable SaaS követelmény kínál az appsource-ban és az Azure Marketplace-en.

## <a name="managing-the-saas-subscription-lifecycle"></a>Az SaaS-előfizetési életciklus kezelése

Microsoft SaaS Service egy SaaS-előfizetés megvásárlása teljes életciklusát felügyeli, és a teljesítése API-t használja, egy mechanizmust, amely a tényleges teljesítése meghajtó módosításait a csomagok és az előfizetést, az ISV-törlése. Az ügyfél, amely a Microsoft fenntartja az SaaS-előfizetés állapota alapján számítjuk fel. Az alábbi ábra bemutatja az állapotok és a műveletek, amelyek a módosítások meghajtó állapotok között.

![SaaS-előfizetési életciklusa állapotok](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>Az SaaS-előfizetés állapotok

A következő táblázat felsorolja a kiépítési állapotok SaaS-előfizetéssel, többek között a leírása és sorrendje diagram egyes (ha van). 

#### <a name="provisioning"></a>Kiépítés

Ha egy ügyfél kezdeményezi a beszerzés, a független Szoftvergyártók ezt az információt a vásárlói interaktív weblap URL-cím paraméter használatával egy hitelesítési kód fogadása. Például: `https://contoso.com/signup?token=..`, ahol a partner center alkotóelemeit oldal URL-címet szolgáltató `https://contoso.com/signup`. A hitelesítési kódot érvényesítése és a részletes mit ki kell építeni a megoldásához API meghívásával kell cserélni.  Végeztével a SaaS-szolgáltatás kiépítése, küld egy aktiválás hívás, hogy jelezze, hogy a teljesítése befejeződött, és az ügyfél is számlázzuk.  Az alábbi ábrán látható az üzembe helyezési forgatókönyv esetén az API-hívások sorrendjét.  

![API-khoz egy SaaS-szolgáltatás kiépítése.](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Kiépítve

Ebben az állapotban a kiépített szolgáltatás stabil állapotban.

#### <a name="provisioning-for-update"></a>Frissítés kiépítés
(a Marketplace-en) 

Ebben az állapotban jelöli egy frissítés egy meglévő szolgáltatás függőben. Ilyen frissítés kezdeményezheti az ügyfél a piactéren vagy a SaaS-szolgáltatás (csak a közvetlen vásárlói tranzakciókra.) Az alábbi ábrán a műveletek egy frissítés elindításakor a marketplace-ről.

![API-hívások frissítés elindításakor a piactérről.](./media/saas-update-api-v2-calls-from-marketplace-a.png)

#### <a name="provisioning-for-update"></a>Frissítés kiépítés  
(az SaaS-szolgáltatás) 

Az alábbi ábrán látható a műveleteket, ha egy frissítés SaaS-szolgáltatás által kezdeményezett. (A webhook hívása az előfizetéshez, az SaaS-szolgáltatás által kezdeményezett frissítés helyébe. 

![API-hívások SaaS-szolgáltatás által kezdeményezett frissítés](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Felfüggesztve

Ez az állapot azt jelzi, hogy a felhasználó fizetési még nem érkeztek. Szabályzat által az ügyfél előtt az előfizetés unfulfilling türelmi időszak lesz elérhető. Ha egy előfizetési ebben az állapotban van: 

- Független szoftverszolgáltatóként csökkentheti a, vagy letiltja a szolgáltatáshoz való hozzáférést a felhasználó választhatja. 
- Az előfizetés, amely képes a beállítások és adatok adatveszteség nélküli összes funkciójának helyreállításához helyreállítható állapotban kell tartani. 
- A türelmi időszak végén érvényességének visszaállítása kérelem ehhez az előfizetéshez a teljesítése API-n keresztül, vagy egy megszüntetéséhez kiépítési kérést beolvasásához várható. 

#### <a name="unsubscribed"></a>Leiratkozott 

Előfizetések ebben az állapotban egy explicit ügyfélkérés vagy adott válaszként díjak megfizetése nem érhető el. A független Szoftvergyártók az elvárás, hogy az ügyfél adatainak őrizzük meg helyreállítási X napnál minimum kérésre és majd törli. 


## <a name="api-reference"></a>API-referencia

Ez a szakasz a SaaS-dokumentumok *előfizetés API* és *műveleti API*.  Értékét a `api-version` 2-es verziójú API-k paramétere `2018-08-31`.  


### <a name="parameter-and-entity-definitions"></a>A paraméter és az entitás-definíciók

Az alábbi táblázat a gyakori paramétereket és teljesítése API-k által használt entitások definícióit.

|     A paraméter/entitás     |     Meghatározás                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | Egy SaaS-erőforrás GUID azonosítója  |
| `name`                   | Az ügyfél által ehhez az erőforráshoz megadott rövid név |
| `publisherId`            | Minden közzétevő, például "contoso" egyedi karakterlánc-azonosító |
| `offerId`                | Ajánlatok, például a "offer1" egyedi azonosító  |
| `planId`                 | Minden csomagot/sku, például "ezüst" egyedi karakterlánc azonosítója |
| `operationId`            | Egy adott művelet GUID azonosítója  |
|  `action`                | A művelet végrehajtása egy erőforrást, vagy `subscribe`, `unsubscribe`, `suspend`, `reinstate`, vagy `changePlan`, `changeQuantity`, `transfer`  |
|   |   |

Globálisan egyedi azonosítóra ([GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)) 128 bites (32 hexadecimális) szám, amely általában automatikusan jönnek létre. 

#### <a name="resolve-a-subscription"></a>Oldja meg az előfizetés 

A feloldás végpontot lehetővé teszi, hogy a közzétevő számára, hogy oldja fel a Marketplace-en jogkivonatot egy állandó erőforrás-azonosítója. Az erőforrás-azonosítója a SAAS-előfizetéshez tartozó egyedi azonosítója.  Független Szoftverszállító webhelyre a rendszer átirányítja a felhasználót, ha az URL-címet tartalmaz egy jogkivonatot a lekérdezési paraméterek. A független Szoftvergyártók várhatóan használja ezt a jogkivonatot, és kérheti oldható meg. A válasz egyedi SAAS előfizetés-azonosító, nevét, Ajánlatazonosító, és az erőforrás tervezése tartalmaz. Ez a token érvényességét csak egy óránál. 

**Bejegyzés:<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`**

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A művelet használata a kérelem verziója  |

*Kérelem fejlécei:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |  Egyedi karakterlánc-érték a kérés követési az ügyfélről, lehetőleg egy GUID Azonosítót. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott. |
|  x-ms-correlationid |  Egyedi karakterlánc értéke a művelethez az ügyfélen. Ez a paraméter utal. a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.  |
|  Engedélyezési     |  [JSON webes jogkivonat (JWT) tulajdonosi jogkivonat beolvasása](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) |
|  x-ms-marketplace-token  |  Az URL-címben, amikor a felhasználó a rendszer átirányítja az SaaS-ISV webhelyet az Azure-ból token lekérdezési paraméter (például: `https://contoso.com/signup?token=..`). *Megjegyzés:* Az URL-cím használat előtt a böngészőből a token érték dekódolása.  |

*Válaszkódot:*

Kód: 200<br>
Oldja fel a átlátszatlan token SaaS-előfizetésre.<br>

```json
Response body:
{
    "subscriptionId": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

Kód: 404<br>
Nem található

Kód: 400<br>
Hibás kérés. x-ms-piactér-token hiányzik, hibás vagy elévült.

Kód: 403<br>
Nem engedélyezett. A hitelesítési token nem lett megadva, az érvénytelen, vagy a kérelem próbál hozzáférni, amely nem tartozik a jelenlegi közzétevő beszerzés.

Kód: 500<br>
Belső kiszolgálóhiba

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

**Kérje le:<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`**

*Lekérdezési paraméterek:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  A művelet ehhez a kérelemhez használt verziója.  |

*Kérelem fejlécei:*

|                    |                   |
|  ---------------   |  ---------------  |
| Content-Type       |  `application/json`  |
| x-ms-requestid     |  Egyedi karakterlánc-érték a kérés követési az ügyfélről, lehetőleg egy GUID Azonosítót. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott. |
| x-ms-correlationid |  Egyedi karakterlánc értéke a művelethez az ügyfélen. Ez a paraméter utal. a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.  |
| Engedélyezési      |  [JSON webes jogkivonat (JWT) tulajdonosi jogkivonat beszerzése.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Válaszkódot:*

Kód: 200 <br/>
A hitelesítési jogkivonat alapján, beszerezheti a közzétevő és a kiadó, amelyek megfelelő előfizetések.<br> Válasz tartalma:<br>

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
          ], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. [Provisioning, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

A folytatási token csak akkor van jelen, ha nincsenek további "lapok" lekérdezni a csomagokat. 

Kód: 403 <br>
Nem engedélyezett. A hitelesítési token nem lett megadva, az érvénytelen, vagy a kérelem próbál hozzáférni, amely nem tartozik a jelenlegi közzétevő beszerzés. 

Kód: 500 Belső kiszolgálóhiba

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

**Kérje le:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`**

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   SaaS-előfizetést, hogy a jogkivonat feloldása API-n keresztül megoldása után egyedi azonosítója   |
|  ApiVersion        |   A művelet használata a kérelem verziója   |

*Kérelem fejlécei:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-ms-requestid    |  Egyedi karakterlánc-érték a kérés követési az ügyfélről, lehetőleg egy GUID Azonosítót. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott. |
|  x-ms-correlationid |  Egyedi karakterlánc értéke a művelethez az ügyfélen. Ez a paraméter utal. a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.  |
|  Engedélyezési     |  [JSON webes jogkivonat (JWT) tulajdonosi jogkivonat beszerzése.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Válaszkódot:*

Kód: 200<br>
Lekérdezi a SaaS-előfizetés azonosítója<br> Válasz tartalma:<br>

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
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "status": "Subscribed", // Indicates the status of the operation.
}
```

Kód: 404<br>
Nem található<br> 

Kód: 403<br>
Nem engedélyezett. A hitelesítési token nem lett megadva, az érvénytelen, vagy a kérelem próbál hozzáférni, amely nem tartozik a jelenlegi közzétevő beszerzés.

Kód: 500<br>
Belső kiszolgálóhiba<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>Elérhető csomagok listája

Ez a hívás segítségével ismerje meg, hogy vannak-e a jelenlegi közzétevő minden olyan privát és nyilvános ajánlatokat.

**Kérje le:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`**

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   A művelet használata a kérelem verziója  |

*Kérelem fejlécei:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-ms-requestid   |   Egyedi karakterlánc-érték a kérés követési az ügyfélről, lehetőleg egy GUID Azonosítót. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott. |
|  x-ms-correlationid  | Egyedi karakterlánc értéke a művelethez az ügyfélen. Ez a paraméter utal. a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott. |
|  Engedélyezési     |  [JSON webes jogkivonat (JWT) tulajdonosi jogkivonat beszerzése.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) |

*Válaszkódot:*

Kód: 200<br>
Elérhető csomagok listájának lekérése az ügyfél.<br>

Válasz törzsében:

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
Nem található<br> 

Kód: 403<br>
Nem engedélyezett. A hitelesítési token nem lett megadva, az érvénytelen, vagy a kérelem próbál hozzáférni, amely nem tartozik a jelenlegi közzétevő beszerzés. <br> 

Kód: 500<br>
Belső kiszolgálóhiba<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>Előfizetés aktiválása

**Bejegyzés:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`**

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A művelet használata a kérelem verziója  |
| subscriptionId     | Az SaaS-előfizetés, hogy a jogkivonat a megoldásához API-val megoldása után egyedi azonosítója  |

*Kérelem fejlécei:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-ms-requestid    | Egyedi karakterlánc-érték a kérés követési az ügyfélről, lehetőleg egy GUID Azonosítót. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.  |
|  x-ms-correlationid  | Egyedi karakterlánc értéke a művelethez az ügyfélen. Ez a karakterlánc utal. a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.  |
|  Engedélyezési     |  [JSON webes jogkivonat (JWT) tulajdonosi jogkivonat beszerzése.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) |

*A kérelem:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*Válaszkódot:*

Kód: 200<br>
Aktiválja az előfizetést.<br>

Kód: 404<br>
Nem található

Kód: 400<br>
Hibás kérelem-ellenőrzési hibák

Kód: 403<br>
Nem engedélyezett. A hitelesítési token nem lett megadva, az érvénytelen, vagy a kérelem próbál hozzáférni, amely nem tartozik a jelenlegi közzétevő beszerzés.

Kód: 500<br>
Belső kiszolgálóhiba

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

**Javítás:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`**

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A művelet ehhez a kérelemhez használt verziója.  |
| subscriptionId     | Az SaaS-előfizetést, hogy a jogkivonat a megoldásához API-val megoldása után egyedi azonosítója.  |

*Kérelem fejlécei:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   A kérelem követési az ügyfélről, lehetőleg a GUID egyedi karakterlánc-érték. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.  |
|  x-ms-correlationid  |  A műveletet az ügyfél egyedi karakterlánc-érték. Ez a paraméter utal. a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.    |
| Engedélyezési      |  [JSON webes jogkivonat (JWT) tulajdonosi jogkivonat beszerzése.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

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
| A művelet-helye | A műveleti állapotának beolvasása az erőforrás csatolása.   |

*Válaszkódot:*

Kód: 202<br>
Csomag módosítására irányuló kérelem el lett fogadva. A független Szoftvergyártók várhatóan lekérdezik a művelet-helyet határozza meg a sikeres vagy sikertelen. <br>

Kód: 404<br>
Nem található

Kód: 400<br>
Hibás kérelem-ellenőrzési hibák.

>[!Note]
>Csak egy csomagot vagy a mennyiség egyszerre javított, mindkettő nem lehet. Az egy adott előfizetés szerkesztése **frissítés** nem szerepel a `allowedCustomerOperations`.

Kód: 403<br>
Nem engedélyezett. A hitelesítési token nem lett megadva, az érvénytelen, vagy a kérelem próbál hozzáférni, amely nem tartozik a jelenlegi közzétevő beszerzés.

Kód: 500<br>
Belső kiszolgálóhiba

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-quantity-on-the-subscription"></a>Az előfizetés mennyiségének módosítása

A mennyiség, az előfizetés frissítése.

**Javítás:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`**

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A művelet ehhez a kérelemhez használt verziója.  |
| subscriptionId     | Az SaaS-előfizetést, hogy a jogkivonat a megoldásához API-val megoldása után egyedi azonosítója.  |

*Kérelem fejlécei:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   A kérelem követési az ügyfélről, lehetőleg a GUID egyedi karakterlánc-érték. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.  |
|  x-ms-correlationid  |  A műveletet az ügyfél egyedi karakterlánc-érték. Ez a paraméter utal. a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.    |
| Engedélyezési      |  [JSON webes jogkivonat (JWT) tulajdonosi jogkivonat beszerzése.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

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
Elfogadva. Mennyiségének módosítása a kérést elfogadták. A független Szoftvergyártók várhatóan lekérdezik a művelet-helyet határozza meg a sikeres vagy sikertelen. <br>

Kód: 404<br>
Nem található

Kód: 400<br>
Hibás kérelem-ellenőrzési hibák.

>[!Note]
>Csak egy csomagot vagy a mennyiség egyszerre javított, mindkettő nem lehet. Az egy adott előfizetés szerkesztése **frissítés** nem szerepel a `allowedCustomerOperations`.

Kód: 403<br>
Nem engedélyezett. A hitelesítési token nem lett megadva, az érvénytelen, vagy a kérelem próbál hozzáférni, amely nem tartozik a jelenlegi közzétevő beszerzés.

Kód: 500<br>
Belső kiszolgálóhiba

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="delete-a-subscription"></a>Töröl egy előfizetést

Előfizetés lemondása, és törölje a megadott előfizetéshez.

**Törlés:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`**

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A művelet ehhez a kérelemhez használt verziója.  |
| subscriptionId     | Az SaaS-előfizetést, hogy a jogkivonat a megoldásához API-val megoldása után egyedi azonosítója.  |

*Kérelem fejlécei:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |   A kérelem követési az ügyfélről, lehetőleg a GUID egyedi karakterlánc-érték. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.   |
|  x-ms-correlationid  |  A műveletet az ügyfél egyedi karakterlánc-érték. Ez a paraméter utal. a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.   |
|  Engedélyezési     |  [JSON webes jogkivonat (JWT) tulajdonosi jogkivonat beszerzése.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Válaszkódot:*

Kód: 202<br>
ISV által kezdeményezett hívás jelzésére leiratkozási egy adott SaaS-előfizetés.<br>

Kód: 404<br>
Nem található

Kód: 400<br>
Az egy adott előfizetés törlése **törlése** nem `allowedCustomerOperations`.

Kód: 403<br>
Nem engedélyezett. A hitelesítési token nem lett megadva, az érvénytelen, vagy a kérelem próbál hozzáférni, amely nem tartozik a jelenlegi közzétevő beszerzés.

Kód: 500<br>
Belső kiszolgálóhiba

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

**Kérje le:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`**

*Lekérdezési paraméterek:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   A művelet ehhez a kérelemhez használt verziója.                |
| subscriptionId     | Az SaaS-előfizetést, hogy a jogkivonat a megoldásához API-val megoldása után egyedi azonosítója.  |

*Kérelem fejlécei:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |  A kérelem követési az ügyfélről, lehetőleg a GUID egyedi karakterlánc-érték. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.  |
|  x-ms-correlationid |  A műveletet az ügyfél egyedi karakterlánc-érték. Ez a paraméter utal. a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.  |
|  Engedélyezési     |  [JSON webes jogkivonat (JWT) tulajdonosi jogkivonat beszerzése.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Válaszkódot:*

Kód: 200<br> Függőben lévő előfizetés műveletek listájának beolvasása.<br>
Válasz tartalma:

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

Kód: 404<br>
Nem található

Kód: 400<br>
Hibás kérelem-ellenőrzési hibák

Kód: 403<br>
Nem engedélyezett. A hitelesítési token nem lett megadva, az érvénytelen, vagy a kérelem próbál hozzáférni, amely nem tartozik a jelenlegi közzétevő beszerzés.

Kód: 500<br>
Belső kiszolgálóhiba

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>Műveleti állapotának beolvasása

Lehetővé teszi, hogy a megadott aktivált aszinkron művelet állapotának nyomon követését a közzétevő (előfizetés / előfizetés lemondása / módosítása tervezése / módosítása mennyiség).

**Kérje le:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`**

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
|  Engedélyezési     |[JSON webes jogkivonat (JWT) tulajdonosi jogkivonat beszerzése.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Válaszkódot:* Kód: 200<br> Függőben lévő SaaS művelet lekérdezi a megadott<br>
Válasz tartalma:

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

Kód: 404<br>
Nem található

Kód: 400<br>
Hibás kérelem-ellenőrzési hibák

Kód: 403<br>
Nem engedélyezett. A hitelesítési token nem lett megadva, az érvénytelen, vagy a kérelem próbál hozzáférni, amely nem tartozik a jelenlegi közzétevő beszerzés.
 
Kód: 500<br> Belső kiszolgálóhiba

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>Egy művelet állapotának frissítése

A művelet a megadott értékekkel sikeres/sikertelen jelezheti állapotának frissítése.

**Javítás:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`**

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  A művelet ehhez a kérelemhez használt verziója.  |
| subscriptionId     | Az SaaS-előfizetést, hogy a jogkivonat a megoldásához API-val megoldása után egyedi azonosítója.  |
|  operationId       | A művelet, amely alatt befejeződött. |

*Kérelem fejlécei:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     | `application/json`   |
|   x-ms-requestid   |   A kérelem követési az ügyfélről, lehetőleg a GUID egyedi karakterlánc-érték. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott. |
|  x-ms-correlationid |  A műveletet az ügyfél egyedi karakterlánc-érték. Ez a paraméter utal. a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott. |
|  Engedélyezési     |  [JSON webes jogkivonat (JWT) tulajdonosi jogkivonat beszerzése.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Kérelem hasznos adatai:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*Válaszkódot:*

Kód: 200<br> Hívja meg a független Szoftverszállító oldalon művelet befejezéséről tájékoztatja. Ez a válasz például a módosítást, a munkaállomások és tervek sikerült jelezni.

Kód: 404<br>
Nem található

Kód: 400<br>
Hibás kérelem-ellenőrzési hibák

Kód: 403<br>
Nem engedélyezett. A hitelesítési token nem lett megadva, az érvénytelen, vagy a kérelem próbál hozzáférni, amely nem tartozik a jelenlegi közzétevő beszerzés.

Kód: 409<br>
Ütközés történt. Ha például egy újabb tranzakció már teljesítette

Kód: 500<br> Belső kiszolgálóhiba

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="webhook-on-the-saas-service"></a>Az SaaS-szolgáltatás a Webhook

A közzétevő meg kell valósítania egy webhook proaktívan értesíti a felhasználókat a szolgáltatás változásaira, a SaaS-szolgáltatás. Az API-t kell lennie a nem hitelesített, és a Microsoft SaaS-szolgáltatás fogja meghívni. Az SaaS-szolgáltatás várhatóan az műveletek érvényesítéséhez és a egy műveletet a webhook értesítésre előtt engedélyezze az API meghívása.

```json
{
  "id": "<this is a Guid operation id, you can call operations API with this to get status>",
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
Ha a művelet lehet ezek egyikét: 
- `Subscribe`, (Ha az erőforrás aktiválva van)
- `Unsubscribe`, (Ha az erőforrás törlése)
- `ChangePlan`, (Amikor befejeződött a terv műveletet)
- `ChangeQuantity`, (Ha a módosítás mennyiség művelet befejeződött),
- `Suspend`, (Ha erőforrás fel lett függesztve)
- `Reinstate`, (Ha erőforrás rendelkezik lett vizsgadíj felfüggesztés után)

Ahol a állapota lehet ezek egyikét: <br>
        -NotStarted, <br>
        -InProgress, <br>
        – A feladat végül sikerült <br>
        – Nem sikerült, <br>
        -Ütközés <br>

Gyakorlatban hasznosítható állapotok a sikeres és sikertelen webhook értesítést. Egy művelet életciklus NotStarted van, például a sikeres/sikertelen/ütközés kerül. Ha nincs elindítva vagy folyamatban van, továbbra is kérelmek GET művelettel API-n keresztül az állapot addig, amíg a művelet feldolgozása előtt műveleteknél véve. 

## <a name="mock-api"></a>API utánzása

Utánzatként funkcionáló Jaink használatával segítséget nyújtanak fejlesztési feladatokhoz, különösen prototípus-készítéshez, használatának első lépései, és a tesztelés projektek. 

Gazdagép-végpont: `https://marketplaceapi.microsoft.com/api` <br/>
API-verzió: `2018-09-15` <br/>
Nincs szükség hitelesítésre. <br/>
Minta Uri: `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

Az API-végpont elérési utak azonosak helyettem és a valós API-kat, de az API-verziókban eltérőek. A 2018-09-15 helyettem és a 2018-08-31 éles verziójának nem. 

Ebben a cikkben az API-hívások bármelyikét a utánzatként funkcionáló gazdagép-végpontra is végezhető. Várható utánzatként funkcionáló adatok vissza válaszként. Felvértezve helyezheti általánosságban véve utánzatként funkcionáló adatok vissza válaszként. A frissítés előfizetés metódusok utánzatként funkcionáló API bármikor visszatérhet az 500-as. 

## <a name="next-steps"></a>További lépések

Szoftveresen is lekérheti a fejlesztők és a számítási feladatok, az ajánlatok és a közzétevő adatkezelési profilok használatával a [Cloud Partner Portal REST API-k](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
