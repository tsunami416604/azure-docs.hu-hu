---
title: SaaS-teljesítési API V2 – az Azure Marketplace-en |} A Microsoft Docs
description: Ismerteti, hogyan hozhat létre egy SaaS-ajánlat az Azure piactéren, a társított teljesítése V2 API-k használatával.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: pbutlerm
ms.openlocfilehash: 7e1fa82e71738ac113bfe748a7117d0c3a10b14b
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57318760"
---
# <a name="saas-fulfillment-api-version-2"></a>SaaS teljesítése API-Version 2

Ez a cikk részletesen, amely lehetővé teszi a független szoftvergyártók (ISV) integrálása az SaaS-alkalmazások számára az API az Azure Piactérrel. Ez az API lehetővé teszi, hogy minden engedélyezett kereskedelmi csatornán részt ISV-alkalmazások: a közvetlen partner által irányított (viszonteladói) és a mező által vezetett.  Az API-t annak listaelem transactable SaaS kínál az Azure Marketplace-en.


## <a name="managing-the-saas-subscription-lifecycle"></a>Az SaaS-előfizetési életciklus kezelése

Microsoft SaaS Service egy SaaS-előfizetés megvásárlása teljes életciklusát felügyeli, és a teljesítése API-t használja, egy mechanizmust, amely a tényleges teljesítése meghajtó módosításait a csomagok és az előfizetést, az ISV-törlése. Az ügyfél, amely a Microsoft fenntartja az SaaS-előfizetés állapota alapján számítjuk fel. Az alábbi ábra bemutatja az állapotok és a műveletek, amelyek a módosítások meghajtó állapotok között.

![SaaS-előfizetési életciklusa állapotok](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>Az SaaS-előfizetés állapotok

A következő táblázat felsorolja a kiépítési állapotok SaaS-előfizetéssel, többek között a leírása és sorrendje diagram egyes (ha van). 

#### <a name="provisioning"></a>Kiépítés

Ha egy ügyfél kezdeményezi a beszerzés, a független Szoftvergyártók fogadása ezeket az adatokat egy AuthCode weblapon ügyfél interaktív egy URL-cím paraméter használatával. A AuthCode is érvényesítése és a részletes úgy kell létrehozni, mit kell cserélni.  Végeztével a SaaS-szolgáltatás kiépítése, küld egy aktiválás hívás, hogy jelezze, hogy a teljesítése befejeződött, és az ügyfél is számlázzuk.  Az alábbi ábrán látható az üzembe helyezési forgatókönyv esetén az API-hívások sorrendjét.  

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

#### <a name="unsubscribed"></a>Leiratkozva 

Előfizetések ebben az állapotban egy explicit ügyfélkérés vagy adott válaszként díjak megfizetése nem érhető el. A független Szoftvergyártók az elvárás, hogy az ügyfél adatainak őrizzük meg helyreállítási X napnál minimum kérésre és majd törli. 

## <a name="api-reference"></a>API-referencia

Ez a szakasz a SaaS-dokumentumok *előfizetés API* és *műveleti API*.


### <a name="subscription-api"></a>Előfizetés API

Az előfizetés API a következő HTTPS műveleteket támogatja: **Első**, **Post**, **javítás**, és **törlése**.

#### <a name="list-subscriptions"></a>Előfizetések listázása

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
| Engedélyezési      |  A JSON webes jogkivonat (JWT) tulajdonosi jogkivonatot.  |

*Válaszkódot:*

Kód: 200<br>
A közzétevő és a megfelelő előfizetések a kiadó, amelyek alapján a hitelesítési jogkivonat lekérése.<br> Válasz tartalma:<br>

```json
{
  "subscriptions": [
      {
          "id": "",
          "name": "CloudEndure for Production use",
          "publisherId": "cloudendure",
          "offerId": "ce-dr-tier2",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "cc906b16-1991-4b6d-a5a4-34c66a5202d7"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "0396833b-87bf-4f31-b81c-c67f88973512"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "status": "Subscribed" // Indicates the status of the operation. [Provisioning, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

Kód: 403 <br>
Nem engedélyezett. A hitelesítési token nem lett megadva, az érvénytelen, vagy a kérelem próbál hozzáférni egy beszerzési, amely az aktuális felhasználó nem tartozik. 

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
|  Engedélyezési     |  JSON webes jogkivonat (JWT) tulajdonosi jogkivonat  |

*Válaszkódot:*

Kód: 200<br>
Lekérdezi a saas-előfizetés azonosítója<br> Válasz tartalma:<br>

```json
Response Body:
{ 
        "id":"",
        "name":"CloudEndure for Production use",
        "publisherId": "cloudendure",
        "offerId": "ce-dr-tier2",
        "planId": "silver",
        "quantity": "10"",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "cc906b16-1991-4b6d-a5a4-34c66a5202d7"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "0396833b-87bf-4f31-b81c-c67f88973512"
          },
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "status": "Subscribed", // Indicates the status of the operation.
}
```

Kód: 404<br>
Nem található<br> 

Kód: 403<br>
Nem engedélyezett. A hitelesítési token nem lett megadva, az érvénytelen, vagy a kérelem próbál hozzáférni egy beszerzési, amely az aktuális felhasználó nem tartozik.

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

Ez a hívás segítségével ismerje meg, hogy vannak-e az aktuális felhasználó minden olyan privát és nyilvános ajánlatokat.

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
|  Engedélyezési     |  JSON webes jogkivonat (JWT) tulajdonosi jogkivonat |

*Válaszkódot:*

Kód: 200<br>
Elérhető csomagok listájának lekérése az ügyfél.<br>

```json
Response Body:
[{
    "planId": "silver",
    "displayName": "Silver",
    "isPrivate": false
},
{
    "planId": "silver-private",
    "displayName": "Silver-private",
    "isPrivate": true
}]
```

Kód: 404<br>
Nem található<br> 

Kód: 403<br>
Nem engedélyezett. A hitelesítési token nem lett megadva, érvénytelen vagy a kérelem próbál hozzáférni egy beszerzési, amely az aktuális felhasználó nem tartozik. <br> 

Kód: 500<br>
Belső kiszolgálóhiba<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="resolve-a-subscription"></a>Oldja meg az előfizetés 

A feloldás végpontot lehetővé teszi a felhasználók számára a Marketplace-en jogkivonat feloldani egy állandó erőforrás-azonosítója. Az erőforrás-azonosítója a SAAS-előfizetéshez tartozó egyedi azonosítója.  Független Szoftverszállító webhelyre a rendszer átirányítja a felhasználót, ha az URL-címet tartalmaz egy jogkivonatot a lekérdezési paraméterek. A független Szoftvergyártók várhatóan használja ezt a jogkivonatot, és kérheti oldható meg. A válasz egyedi SAAS előfizetés-azonosító, nevét, Ajánlatazonosító, és az erőforrás tervezése tartalmaz. Ez a token érvényességét csak egy óránál. 

**Bejegyzés:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`**

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
|  Engedélyezési     |  JSON webes jogkivonat (JWT) tulajdonosi jogkivonat  |
|  x-ms-marketplace-token  |  Token lekérdezési paraméter az URL-címben, amikor a felhasználó a rendszer átirányítja az SaaS-ISV webhelyet az Azure-ból. *Megjegyzés:* Az URL-cím használat előtt a böngészőből a token érték dekódolása. |

*Válaszkódot:*

Kód: 200<br>
Oldja fel a átlátszatlan token SaaS-előfizetésre.<br>

```json
Response body:
{
    "subscriptionId": "cd9c6a3a-7576-49f2-b27e-1e5136e57f45",  
    "subscriptionName": "My Saas application",
    "offerId": "ce-dr-tier2",
    "planId": "silver",
    "quantity": "20",
    "operationId": " be750acb-00aa-4a02-86bc-476cbe66d7fa"  
}
```

Kód: 404<br>
Nem található

Kód: 400<br>
Hibás kérelem-ellenőrzési hibák

Kód: 403<br>
Nem engedélyezett. A hitelesítési token nem lett megadva, az érvénytelen, vagy a kérelem próbál hozzáférni egy beszerzési, amely az aktuális felhasználó nem tartozik.

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
|  x-ms-correlationid  | Egyedi karakterlánc értéke a művelethez az ügyfélen. Ez utal. a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.  |
|  Engedélyezési     |  JSON webes jogkivonat (JWT) tulajdonosi jogkivonat |

*A kérelem:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*Válaszkódot:*

Kód: 202<br>
Aktiválja az előfizetést.<br>

Kód: 404<br>
Nem található

Kód: 400<br>
Hibás kérelem-ellenőrzési hibák

Kód: 403<br>
Nem engedélyezett. A hitelesítési token nem lett megadva, az érvénytelen, vagy a kérelem próbál hozzáférni egy beszerzési, amely az aktuális felhasználó nem tartozik.

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

#### <a name="update-a-subscription"></a>Előfizetés frissítése

Frissítés, vagy módosítsa a megadott értékekkel előfizetési csomagot.

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
| Engedélyezési      |  A JSON webes jogkivonat (JWT) tulajdonosi jogkivonatot.  |

*Kérelem hasznos adatai:*

```json
Request Body:
{
    "planId": "gold",
    "quantity": ""
}
```

*Kérelem fejlécei:*

|                    |                   |
|  ---------------   |  ---------------  |
| A művelet-helye | A műveleti állapotának beolvasása az erőforrás csatolása.   |

*Válaszkódot:*

Kód: 202<br>
ISV indítja el, egy csomag módosítása vagy egy mennyiségének módosítása. <br>

Kód: 404<br>
Nem található

Kód: 400<br>
Hibás kérelem-ellenőrzési hibák.

>[!Note]
>Csak egy csomagot vagy a mennyiség egyszerre javított, mindkettő nem lehet. Az egy adott előfizetés szerkesztése **frissítés** nem szerepel a `allowedCustomerOperations`.

Kód: 403<br>
Nem engedélyezett. A hitelesítési token nem lett megadva, az érvénytelen, vagy a kérelem próbál hozzáférni egy beszerzési, amely az aktuális felhasználó nem tartozik.

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

#### <a name="delete-a-subscription"></a>Előfizetés törlése

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
|  Engedélyezési     |  A JSON webes jogkivonat (JWT) tulajdonosi jogkivonatot.   |

*Válaszkódot:*

Kód: 200<br>
ISV által kezdeményezett hívás jelzésére leiratkozási egy adott SaaS-előfizetés.<br>

Kód: 404<br>
Nem található

Kód: 400<br>
Az egy adott előfizetés törlése **törlése** nem `allowedCustomerOperations`.

Kód: 403<br>
Nem engedélyezett. A hitelesítési token nem lett megadva, az érvénytelen, vagy a kérelem próbál hozzáférni egy beszerzési, amely az aktuális felhasználó nem tartozik.

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


#### <a name="update-a-subscription"></a>Előfizetés frissítése

Előfizetés frissítése a megadott értékeket.

**Javítás:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operation/<operationId>?api-version=<ApiVersion>`**

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
|  Engedélyezési     |  A JSON webes jogkivonat (JWT) tulajdonosi jogkivonatot.  |

*Kérelem hasznos adatai:*

```json
{
    "planId": "",
    "quantity": "",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}
```

*Válaszkódot:*

Kód: 200<br> Hívja meg a független Szoftverszállító oldalon művelet befejezéséről tájékoztatja. Ez lehet például a munkaállomások és tervek módosítása.

Kód: 404<br>
Nem található

Kód: 400<br>
Hibás kérelem-ellenőrzési hibák

Kód: 403<br>
Nem engedélyezett. A hitelesítési token nem lett megadva, érvénytelen vagy a kérelem próbál hozzáférni egy beszerzési, amely az aktuális felhasználó nem tartozik.

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

#### <a name="list-outstanding-operations"></a>Függőben lévő műveletek listázása 

Az aktuális felhasználó a szálankénti függőben lévő műveletek listája. 

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
|  Engedélyezési     |  A JSON webes jogkivonat (JWT) tulajdonosi jogkivonatot.  |

*Válaszkódot:*

Kód: 200<br> Függőben lévő előfizetés műveletek listájának beolvasása.<br>
Válasz tartalma:

```json
[{
    "id": "be750acb-00aa-4a02-86bc-476cbe66d7fa",  
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId": "cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "offerId": "ce-dr-tier2",
    "publisherId": "cloudendure",  
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
Nem engedélyezett. A hitelesítési token nem lett megadva, az érvénytelen, vagy a kérelem próbál hozzáférni egy beszerzési, amely az aktuális felhasználó nem tartozik.

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

Lehetővé teszi a felhasználó egy aktivált aszinkron művelet (az előfizetés/Unsubscribe/csomag módosítása) állapotának nyomon követését.

**Kérje le:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`**

*Lekérdezési paraméterek:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A művelet ehhez a kérelemhez használt verziója.  |

*Kérelem fejlécei:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  ` application/json`   |
|  x-ms-requestid    |   A kérelem követési az ügyfélről, lehetőleg a GUID egyedi karakterlánc-érték. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.  |
|  x-ms-correlationid |  A műveletet az ügyfél egyedi karakterlánc-érték. Ez a paraméter utal. a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.  |
|  Engedélyezési     | A JSON webes jogkivonat (JWT) tulajdonosi jogkivonatot.  |

*Válaszkódot:* Kód: 200<br> Az összes függőben lévő SaaS-műveletek listájának beolvasása<br>
Válasz tartalma:

```json
Response body:
[{
    "id  ": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId":"cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "offerId": "ce-dr-tier2",
    "publisherId": "cloudendure",  
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
Nem engedélyezett. A hitelesítési token nem lett megadva, az érvénytelen, vagy a kérelem próbál hozzáférni egy beszerzési, amely az aktuális felhasználó nem tartozik.
 
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
    "operationId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId":"cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "offerId": "ce-dr-tier2",
    "publisherId": "cloudendure",
    "planId": "silver",
    "quantity": "20"  ,
    "action": "Activate",   // Activate/Delete/Suspend/Reinstate/Change[new]  
    "timeStamp": "2018-12-01T00:00:00"
}

```

<!-- Review following, might not be needed when this publishes -->


## <a name="mock-api"></a>API utánzása

Utánzatként funkcionáló Jaink használatával segítséget nyújtanak fejlesztési feladatokhoz, különösen a prototípus-készítéshez és a tesztelési projekteket használatának első lépései. 

Gazdagép-végpont: https://marketplaceapi.microsoft.com/api API-verzió: 2018-09-15 nincs hitelesítés szükséges a minta Uri: https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15

Ebben a cikkben az API-hívások bármelyikét a utánzatként funkcionáló gazdagép-végpontra is végezhető. Várható utánzatként funkcionáló adatok vissza válaszként.


## <a name="next-steps"></a>További lépések

Szoftveresen is lekérheti a fejlesztők és a számítási feladatok, az ajánlatok és a közzétevő adatkezelési profilok használatával a [Cloud Partner Portal REST API-k](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
