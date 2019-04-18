---
title: SaaS-teljesítési API V1 - az Azure Marketplace-en |} A Microsoft Docs
description: Ismerteti, hogyan hozhat létre egy SaaS-ajánlat az Azure piactéren, a társított teljesítése V1 API-k használatával.
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
ms.topic: reference
ms.date: 03/28/2019
ms.author: pbutlerm
ROBOTS: NOINDEX
ms.openlocfilehash: 4908233280c69a37ea470eed2ef077cb220a7930
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59009734"
---
# <a name="saas-fulfillment-apis-version-1--deprecated"></a>SaaS teljesítése API-k (elavult) 1-es verzió

Ez a cikk bemutatja, hogyan hozhat létre egy SaaS-ajánlatok API-kkal. Az API-k, a REST-módszerek és a végpontok, ha a kiválasztott Azure-on keresztül kell árul, amely lehetővé teszi, hogy az SaaS-ajánlat előfizetéseinek szükségesek.  

> [!WARNING]
> A kezdeti SaaS teljesítése API-verzió elavult; Ehelyett használjon [SaaS teljesítése API V2](./cpp-saas-fulfillment-api-v2.md).  Ez az API jelenleg tartják fenn csak a létező gyártók kiszolgálása érdekében. 

A következő API-kat a SaaS-szolgáltatás integrációját az Azure-ral áll rendelkezésre:

-   Feloldás
-   Feliratkozás
-   Konvertálás
-   Leiratkozás


## <a name="api-methods-and-endpoints"></a>API-metódusai és végpontok

A következő szakaszok ismertetik az API-metódusai és a egy SaaS-ajánlat előfizetéseinek engedélyezése végpontok.


### <a name="marketplace-api-endpoint-and-api-version"></a>Piactér API végpontot és API-verzió

Az Azure Marketplace-en API-végpont `https://marketplaceapi.microsoft.com`.

Az API-verzió `api-version=2017-04-15`.


### <a name="resolve-subscription"></a>Oldja meg az előfizetés

A POST művelet oldja meg a végpont lehetővé teszi a felhasználóknak a Marketplace-en jogkivonat feloldani egy állandó erőforrás-azonosítója.  Az erőforrás-azonosítója a SAAS-előfizetéshez tartozó egyedi azonosítója. 

Független Szoftverszállító webhelyre a rendszer átirányítja a felhasználót, ha az URL-címet tartalmaz egy jogkivonatot a lekérdezési paraméterek. A független Szoftvergyártók várhatóan használja ezt a jogkivonatot, és kérheti oldható meg. A válasz egyedi SAAS előfizetés-azonosító, nevét, Ajánlatazonosító, és az erőforrás tervezése tartalmaz. Ez a token érvényességét csak egy óránál.

*Kérés*

**POST**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=2017-04-15**

|  **Paraméter neve** |     **Leírás**                                      |
|  ------------------ |     ---------------------------------------------------- |
|  API-verzió        |  A művelet ehhez a kérelemhez használt verziója.   |
|  |  |


*Fejlécek*

| **Fejléc kulcs**     | **Kötelező** | **Leírás**                                                                                                                                                                                                                  |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Nem           | A kérelem követési az ügyfélről, lehetőleg a GUID egyedi karakterlánc-érték. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.  |
| x-ms-correlationid | Nem           | A műveletet az ügyfél egyedi karakterlánc-érték. Ez utal. a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott. |
| Content-type       | Igen          | `application/json`                                        |
| Engedélyezési      | Igen          | A JSON webes jogkivonat (JWT) tulajdonosi jogkivonatot.                    |
| x-ms-marketplace-token| Igen| A token lekérdezési paraméter, amikor a felhasználó az Azure-ból SaaS ISV webhelyre átirányítási URL-címét. **Megjegyzés:** Ez a token csak a érvényes 1 óra. Ezenkívül URL-CÍMÉT a böngészőben a token értékét használat előtt dekódol.|
|  |  |  |
  

*Választörzs*

``` json
{
    "id": "",
    "subscriptionName": "",
    "offerId": "",
    "planId": "",
}
```

| **Paraméter neve** | **Adattípus** | **Leírás**                       |
|--------------------|---------------|---------------------------------------|
| id                 | String        | Az SaaS-előfizetés azonosítója.          |
| subscriptionName| String| Az SaaS-előfizetés beállítása az Azure-ban a felhasználó a SaaS-szolgáltatásra előfizető neve.|
| Ajánlat azonosítója            | String        | Ajánlat azonosítója, amelyre a felhasználó előfizetett. |
| planId             | String        | Tervezze meg, hogy a felhasználó előfizetett azonosítója.  |
|  |  |  |


*Válaszkódok*

| **HTTP-állapotkód** | **Hibakód:**     | **Leírás**                                                                         |
|----------------------|--------------------| --------------------------------------------------------------------------------------- |
| 200                  | `OK`                 | A jogkivonat sikeresen feloldva.                                                            |
| 400                  | `BadRequest`         | Vagy kötelező fejléc hiányzik, vagy a megadott api-verziója érvénytelen. Nem sikerült feloldani a jogkivonatot, mert helytelen formátumúak vagy lejártak vagy a token (a token érvényességét csak 1 órán keresztül létrehozott). |
| 403                  | `Forbidden`          | A hívónak nincs engedélye e művelet elvégzéséhez.                                 |
| 429                  | `RequestThrottleId`  | Szolgáltatás egy foglalt feldolgozási kérelmek, próbálkozzon újra később.                                |
| 503                  | `ServiceUnavailable` | Szolgáltatás van lefelé átmenetileg, próbálkozzon újra később.                                        |
|  |  |  |


*Válaszfejlécek*

| **Fejléc kulcs**     | **Kötelező** | **Leírás**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Igen          | Kérés azonosítója, az ügyféltől kapott.                                                                   |
| x-ms-correlationid | Igen          | Korrelációs Azonosítót, ha az ügyfél által átadott ellenkező esetben ezt az értéket, a korrelációs azonosítóját.                   |
| x-ms-activityid    | Igen          | A szolgáltatás a kérelem követési egyedi karakterlánc-érték. Ez az azonosító bármelyik egyeztetések szolgál. |
| Retry-After        | Nem           | Ez az érték csak egy 429 válaszra van beállítva.                                                                   |
|  |  |  |


### <a name="subscribe"></a>Feliratkozás

Az előfizetési végpont lehetővé teszi, hogy a felhasználók számára egy egy SaaS-szolgáltatás egy adott csomag előfizetésének indítása és engedélyezése a számlázás a kereskedelmi rendszerben.

**PUT**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Paraméter neve**  | **Leírás**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Egyedi azonosítója az SaaS-előfizetés, hogy a jogkivonat feloldása API-n keresztül megoldása után.                              |
| API-verzió         | A művelet ehhez a kérelemhez használt verziója. |
|  |  |

*Fejlécek*

|  **Fejléc kulcs**        | **Kötelező** |  **Leírás**                                                  |
| ------------------     | ------------ | --------------------------------------------------------------------------------------- |
| x-ms-requestid         |   Nem         | A kérelem követési az ügyfélről, lehetőleg a GUID egyedi karakterlánc-érték. Ha nincs megadva, az egyik létrehozott és a válaszfejlécek megadott. |
| x-ms-correlationid     |   Nem         | A műveletet az ügyfél egyedi karakterlánc-érték. Ez az érték használatával történik a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye van. Ha nincs megadva, az egyik létrehozott és a válaszfejlécek megadott. |
| If-Match/If-None-Match |   Nem         |   Erős érvényesítő ETag-érték.                                                          |
| content-type           |   Igen        |    `application/json`                                                                   |
|  Engedélyezési         |   Igen        |    A JSON webes jogkivonat (JWT) tulajdonosi jogkivonatot.                                               |
| x-ms-marketplace-session-mode| Nem | Ez a jelző azt tesztfuttatás módban ahhoz, hogy egy SaaS-ajánlat feliratkozás során. Ha a beállítása, az előfizetés nem számítunk fel. Ez akkor hasznos, ISV-k olyan forgatókönyvek tesztelése esetében. Állítsa be azt **"tesztfuttatási hiba:**|
|  |  |  |

*Törzs*

``` json
{
    "lanId": "",
}
```

| **Elem neve** | **Adattípus** | **Leírás**                      |
|------------------|---------------|--------------------------------------|
| planId           | (Kötelező) Karakterlánc        | Tervezze meg a SaaS-szolgáltatás felhasználói előfizetés azonosítója.  |
|  |  |  |

*Válaszkódok*

| **HTTP-állapotkód** | **Hibakód:**     | **Leírás**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | SaaS-előfizetés aktiválási egy adott csomag érkezett.                   |
| 400                  | `BadRequest`         | Vagy kötelező fejléc hiányzik, vagy a JSON törzsében helytelen formátumú. |
| 403                  | `Forbidden`          | A hívónak nincs engedélye e művelet elvégzéséhez.                   |
| 404                  | `NotFound`           | Nem található a megadott azonosítójú előfizetés                                  |
| 409                  | `Conflict`           | Egy másik művelet van folyamatban van az előfizetésben.                     |
| 429                  | `RequestThrottleId`  | Szolgáltatás egy foglalt feldolgozási kérelmek, próbálkozzon újra később.                  |
| 503                  | `ServiceUnavailable` | Szolgáltatás van lefelé átmenetileg, próbálkozzon újra később.                          |
|  |  |  |

202-es választ nyomon követheti a kért művelet állapotát az "A művelet-location" fejléc. A hitelesítés megegyezik más Marketplace API-k formájában.

*Válaszfejlécek*

| **Fejléc kulcs**     | **Kötelező** | **Leírás**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Igen          | Kérés azonosítója, az ügyféltől kapott.                                                                   |
| x-ms-correlationid | Igen          | Korrelációs Azonosítót, ha az ügyfél által átadott ellenkező esetben ezt az értéket, a korrelációs azonosítóját.                   |
| x-ms-activityid    | Igen          | A szolgáltatás a kérelem követési egyedi karakterlánc-érték. Ez az érték minden egyeztetések szolgál. |
| Retry-After        | Igen          | Melyik ügyfél időköz ellenőrizheti.                                                       |
| A művelet-helye | Igen          | Hivatkozás az erőforrás műveleti állapotának beolvasása.                                                        |
|  |  |  |

### <a name="change-plan-endpoint"></a>Módosítás terv végpont

A módosítás végpont lehetővé teszi, hogy a felhasználó az aktuálisan előfizetett csomag átalakítása egy új tervet.

**PATCH**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Paraméter neve**  | **Leírás**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID az SaaS-előfizetés.                              |
| API-verzió         | A művelet ehhez a kérelemhez használt verziója. |
|  |  |

*Fejlécek*

| **Fejléc kulcs**          | **Kötelező** | **Leírás**                                                                                                                                                                                                                  |
|-------------------------|--------------|---------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid          | Nem           | Az ügyfél a kérés követési egyedi karakterlánc-érték. Javasoljuk, egy GUID Azonosítót. Ha nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.   |
| x-ms-correlationid      | Nem           | A műveletet az ügyfél egyedi karakterlánc-érték. Ez az érték használatával történik a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye van. Ha nincs megadva, az egyik létrehozott és a válaszfejlécek megadott. |
| If-Match /If-None-Match | Nem           | Erős érvényesítő ETag-érték.                              |
| content-type            | Igen          | `application/json`                                        |
| Engedélyezési           | Igen          | A JSON webes jogkivonat (JWT) tulajdonosi jogkivonatot.                    |
|  |  |  |

*Törzs*

```json
{
    "planId": ""
}
```

|  **Elem neve** |  **Adattípus**  | **Leírás**                              |
|  ---------------- | -------------   | --------------------------------------       |
|  planId           |  (Kötelező) Karakterlánc         | Tervezze meg a SaaS-szolgáltatás felhasználói előfizetés azonosítója.          |
|  |  |  |

*Válaszkódok*

| **HTTP-állapotkód** | **Hibakód:**     | **Leírás**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | SaaS-előfizetés aktiválási egy adott csomag érkezett.                   |
| 400                  | `BadRequest`         | Vagy kötelező fejléc hiányzik, vagy a JSON törzsében helytelen formátumú. |
| 403                  | `Forbidden`          | A hívónak nincs engedélye e művelet elvégzéséhez.                   |
| 404                  | `NotFound`           | Nem található a megadott azonosítójú előfizetés                                  |
| 409                  | `Conflict`           | Egy másik művelet van folyamatban van az előfizetésben.                     |
| 429                  | `RequestThrottleId`  | Szolgáltatás egy foglalt feldolgozási kérelmek, próbálkozzon újra később.                  |
| 503                  | `ServiceUnavailable` | Szolgáltatás van lefelé átmenetileg, próbálkozzon újra később.                          |
|  |  |  |

*Válaszfejlécek*

| **Fejléc kulcs**     | **Kötelező** | **Leírás**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Igen          | Kérés azonosítója, az ügyféltől kapott.                                                                   |
| x-ms-correlationid | Igen          | Korrelációs Azonosítót, ha az ügyfél által átadott ellenkező esetben ezt az értéket, a korrelációs azonosítóját.                   |
| x-ms-activityid    | Igen          | A szolgáltatás a kérelem követési egyedi karakterlánc-érték. Ez az érték minden egyeztetések szolgál. |
| Retry-After        | Igen          | Melyik ügyfél időköz ellenőrizheti.                                                       |
| A művelet-helye | Igen          | Hivatkozás az erőforrás műveleti állapotának beolvasása.                                                        |
|  |  |  |

### <a name="delete-subscription"></a>Előfizetés törlése

Az előfizetési végpont a törlési művelet lehetővé teszi, hogy a felhasználó töröl egy előfizetést a megadott azonosítót.

*Kérés*

**DELETE**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Paraméter neve**  | **Leírás**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID az SaaS-előfizetés.                              |
| API-verzió         | A művelet ehhez a kérelemhez használt verziója. |
|  |  |

*Fejlécek*

| **Fejléc kulcs**     | **Kötelező** | **Leírás**                                                                                                                                                                                                                  |
|--------------------|--------------| ----------------------------------------------------------|
| x-ms-requestid     | Nem           | Az ügyfél a kérés követési egyedi karakterlánc-érték. Javasoljuk, egy GUID Azonosítót. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.                                                           |
| x-ms-correlationid | Nem           | A műveletet az ügyfél egyedi karakterlánc-érték. Ez az érték használatával történik a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye van. Ha nincs megadva, az egyik létrehozott és a válaszfejlécek megadott. |
| Engedélyezési      | Igen          | A JSON webes jogkivonat (JWT) tulajdonosi jogkivonatot.                    |
|  |  |  |

*Válaszkódok*

| **HTTP-állapotkód** | **Hibakód:**     | **Leírás**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | SaaS-előfizetés aktiválási egy adott csomag érkezett.                   |
| 400                  | `BadRequest`         | Vagy kötelező fejléc hiányzik, vagy a JSON törzsében helytelen formátumú. |
| 403                  | `Forbidden`          | A hívónak nincs engedélye e művelet elvégzéséhez.                   |
| 404                  | `NotFound`           | Nem található a megadott azonosítójú előfizetés                                  |
| 429                  | `RequestThrottleId`  | Service foglalt feldolgozási kérelmek, próbálkozzon újra később.                  |
| 503                  | `ServiceUnavailable` | Szolgáltatás ideiglenesen nem működik. Próbálkozzon újra később.                          |
|  |  |  |

202-es választ nyomon követheti a kért művelet állapotát az "A művelet-location" fejléc. A hitelesítés megegyezik más Marketplace API-k formájában.

*Válaszfejlécek*

| **Fejléc kulcs**     | **Kötelező** | **Leírás**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Igen          | Kérés azonosítója, az ügyféltől kapott.                                                                   |
| x-ms-correlationid | Igen          | Korrelációs azonosító által átadott az ügyfelet, egyébként ez-e a korrelációs azonosítóját.                   |
| x-ms-activityid    | Igen          | A szolgáltatás a kérelem követési egyedi karakterlánc-érték. Bármely egyeztetések szolgál. |
| Retry-After        | Igen          | Melyik ügyfél időköz ellenőrizheti.                                                       |
| A művelet-helye | Igen          | Hivatkozás az erőforrás műveleti állapotának beolvasása.                                                        |
|   |  |  |

### <a name="get-operation-status"></a>Műveleti állapotának beolvasása

Ez a végpont lehetővé teszi a felhasználó egy aktivált aszinkron művelet (az előfizetés/Unsubscribe/csomag módosítása) állapotának nyomon követését.

*Kérés*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/operations/*{operationId}*?api-version=2017-04-15**

| **Paraméter neve**  | **Leírás**                                       |
|---------------------|-------------------------------------------------------|
| operationId         | A művelet által aktivált egyedi azonosítója.                |
| API-verzió         | A művelet ehhez a kérelemhez használt verziója. |
|  |  |

*Fejlécek*

| **Fejléc kulcs**     | **Kötelező** | **Leírás**                                                                                                                                                                                                                  |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Nem           | Az ügyfél a kérés követési egyedi karakterlánc-érték. Javasoljuk, egy GUID Azonosítót. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.   |
| x-ms-correlationid | Nem           | A műveletet az ügyfél egyedi karakterlánc-érték. Ez az érték használatával történik a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye van. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.  |
| Engedélyezési      | Igen          | A JSON webes jogkivonat (JWT) tulajdonosi jogkivonatot.                    |
|  |  |  | 

*Választörzs*

```json
{
    "id": "",
    "status": "",
    "resourceLocation": "",
    "created": "",
    "lastModified": ""
}
```

| **Paraméter neve** | **Adattípus** | **Leírás**                                                                                                                                               |
|--------------------|---------------|-------------------------------------------------------------------------------------------|
| id                 | String        | A művelet azonosítója.                                                                      |
| status             | Enum          | A művelet állapotát, a következők egyikét: `In Progress`, `Succeeded`, vagy `Failed`.          |
| resourceLocation   | String        | Az előfizetést, amelyhez a létrehozott vagy módosított mutató hivatkozás. Ez segít az ügyfél megszerezni a frissítve állapotot post műveletet. Ez az érték nincs beállítva `Unsubscribe` műveleteket. |
| létrehozva            | DateTime      | A művelet létrehozásának időpontja (UTC).                                                           |
| módosítás dátuma       | DateTime      | Utolsó frissítése (UTC), a műveletet.                                                      |
|  |  |  |

*Válaszkódok*

| **HTTP-állapotkód** | **Hibakód:**     | **Leírás**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | A get-kérés sikeresen feloldva, és a válasz törzsében.    |
| 400                  | `BadRequest`         | Vagy kötelező fejléc hiányzik, vagy egy érvénytelen api-Version paraméter lett megadva. |
| 403                  | `Forbidden`          | A hívónak nincs engedélye e művelet elvégzéséhez.                      |
| 404                  | `NotFound`           | Nem található a megadott azonosítójú előfizetés                                     |
| 429                  | `RequestThrottleId`  | Szolgáltatás egy foglalt feldolgozási kérelmek, próbálkozzon újra később.                     |
| 503                  | `ServiceUnavailable` | Szolgáltatás van lefelé átmenetileg, próbálkozzon újra később.                             |
|  |  |  |

*Válaszfejlécek*

| **Fejléc kulcs**     | **Kötelező** | **Leírás**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Igen          | Kérés azonosítója, az ügyféltől kapott.                                                                   |
| x-ms-correlationid | Igen          | Korrelációs azonosító által átadott az ügyfelet, egyébként ez-e a korrelációs azonosítóját.                   |
| x-ms-activityid    | Igen          | A szolgáltatás a kérelem követési egyedi karakterlánc-érték. Bármely egyeztetések szolgál. |
| Retry-After        | Igen          | Melyik ügyfél időköz ellenőrizheti.                                                       |
|  |  |  |

### <a name="get-subscription"></a>Előfizetés beszerzése

A Get műveletet az előfizetési végpont lehetővé teszi, hogy egy felhasználó egy adott erőforrás-azonosítójú előfizetés lekéréséhez.

*Kérés*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Paraméter neve**  | **Leírás**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID az SaaS-előfizetés.                              |
| API-verzió         | A művelet ehhez a kérelemhez használt verziója. |
|  |  |

*Fejlécek*

| **Fejléc kulcs**     | **Kötelező** | **Leírás**                                                                                           |
|--------------------|--------------|-----------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Nem           | A kérelem követési az ügyfélről, lehetőleg a GUID egyedi karakterlánc-érték. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.                                                           |
| x-ms-correlationid | Nem           | A műveletet az ügyfél egyedi karakterlánc-érték. Ez az érték használatával történik a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye van. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott. |
| Engedélyezési      | Igen          | A JSON webes jogkivonat (JWT) tulajdonosi jogkivonatot.                                                                    |
|  |  |  |

*Választörzs*

```json
{
    "id": "",
    "saasSubscriptionName": "",
    "offerId": "",
    "planId": "",
    "saasSubscriptionStatus": "",
    "created": "",
    "lastModified": ""
}
```

| **Paraméter neve**     | **Adattípus** | **Leírás**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | String        | Előfizetés erőforrás azonosítója, SaaS az Azure-ban.    |
| ajánlatazonosító                | String        | Ajánlat azonosítója, amelyre a felhasználó előfizetett.         |
| planId                 | String        | Tervezze meg, hogy a felhasználó előfizetett azonosítója.          |
| saasSubscriptionName   | String        | Az SaaS-előfizetés neve.                |
| saasSubscriptionStatus | Enum          | A művelet állapotát.  A következők egyikét:  <br/> - `Subscribed`: Előfizetés az aktív.  <br/> - `Pending`: Felhasználó létrehozása az erőforrás, de nem aktiválódik, a független szoftvergyártó.   <br/> - `Unsubscribed`: Felhasználó leiratkoztatva rendelkezik.   <br/> - `Suspended`: Felhasználó felfüggesztette az előfizetést.   <br/> - `Deactivated`:  Azure-előfizetés fel van függesztve.  |
| létrehozva                | DateTime      | Előfizetés létrehozása időbélyeg értékét (UTC). |
| módosítás dátuma           | DateTime      | Előfizetés módosítása időbélyegző-érték (UTC). |
|  |  |  |

*Válaszkódok*

| **HTTP-állapotkód** | **Hibakód:**     | **Leírás**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | A get-kérés sikeresen feloldva, és a válasz törzsében.    |
| 400                  | `BadRequest`         | Vagy kötelező fejléc hiányzik, vagy egy érvénytelen api-Version paraméter lett megadva. |
| 403                  | `Forbidden`          | A hívónak nincs engedélye e művelet elvégzéséhez.                      |
| 404                  | `NotFound`           | Nem található a megadott azonosítójú előfizetés                                     |
| 429                  | `RequestThrottleId`  | Szolgáltatás egy foglalt feldolgozási kérelmek, próbálkozzon újra később.                     |
| 503                  | `ServiceUnavailable` | Szolgáltatás van lefelé átmenetileg, próbálkozzon újra később.                             |
|  |  |  |

*Válaszfejlécek*

| **Fejléc kulcs**     | **Kötelező** | **Leírás**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Igen          | Kérés azonosítója, az ügyféltől kapott.                                                                   |
| x-ms-correlationid | Igen          | Korrelációs azonosító által átadott az ügyfelet, egyébként ez-e a korrelációs azonosítóját.                   |
| x-ms-activityid    | Igen          | A szolgáltatás a kérelem követési egyedi karakterlánc-érték. Bármely egyeztetések szolgál. |
| Retry-After        | Nem           | Melyik ügyfél időköz ellenőrizheti.                                                       |
| az eTag               | Igen          | Hivatkozás az erőforrás műveleti állapotának beolvasása.                                                        |
|  |  |  |

### <a name="get-subscriptions"></a>Előfizetések beolvasása

A Get műveletet előfizetések végponton lehetővé teszi, hogy egy felhasználó összes előfizetés összes ajánlatok lekérése az ISV-k.

*Kérés*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2017-04-15**

| **Paraméter neve**  | **Leírás**                                       |
|---------------------|-------------------------------------------------------|
| API-verzió         | A művelet ehhez a kérelemhez használt verziója. |
|  |  |

*Fejlécek*

| **Fejléc kulcs**     | **Kötelező** | **Leírás**                                           |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Nem           | Az ügyfél a kérés követési egyedi karakterlánc-érték. Javasoljuk, egy GUID Azonosítót. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.             |
| x-ms-correlationid | Nem           | A műveletet az ügyfél egyedi karakterlánc-érték. Ez az érték használatával történik a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye van. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott. |
| Engedélyezési      | Igen          | A JSON webes jogkivonat (JWT) tulajdonosi jogkivonatot.                    |
|  |  |  |

*Választörzs*

```json
{
    "id": "",
    "saasSubscriptionName": "",
    "offerId": "",
    "planId": "",
    "saasSubscriptionStatus": "",
    "created": "",
    "lastModified": ""
}
```

| **Paraméter neve**     | **Adattípus** | **Leírás**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | String        | Előfizetés erőforrás azonosítója, SaaS az Azure-ban.    |
| ajánlatazonosító                | String        | Ajánlat azonosítója, amelyre a felhasználó előfizetett.         |
| planId                 | String        | Tervezze meg, hogy a felhasználó előfizetett azonosítója.          |
| saasSubscriptionName   | String        | Az SaaS-előfizetés neve.                |
| saasSubscriptionStatus | Enum          | A művelet állapotát.  A következők egyikét:  <br/> - `Subscribed`: Előfizetés az aktív.  <br/> - `Pending`: Felhasználó létrehozása az erőforrás, de nem aktiválódik, a független szoftvergyártó.   <br/> - `Unsubscribed`: Felhasználó leiratkoztatva rendelkezik.   <br/> - `Suspended`: Felhasználó felfüggesztette az előfizetést.   <br/> - `Deactivated`:  Azure-előfizetés fel van függesztve.  |
| létrehozva                | DateTime      | Előfizetés létrehozása időbélyeg értékét (UTC). |
| módosítás dátuma           | DateTime      | Előfizetés módosítása időbélyegző-érték (UTC). |
|  |  |  |

*Válaszkódok*

| **HTTP-állapotkód** | **Hibakód:**     | **Leírás**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | A get-kérés sikeresen feloldva, és a válasz törzsében.    |
| 400                  | `BadRequest`         | Vagy kötelező fejléc hiányzik, vagy egy érvénytelen api-Version paraméter lett megadva. |
| 403                  | `Forbidden`          | A hívónak nincs engedélye e művelet elvégzéséhez.                      |
| 404                  | `NotFound`           | Nem található a megadott azonosítójú előfizetés                                     |
| 429                  | `RequestThrottleId`  | Service foglalt feldolgozási kérelmek, próbálkozzon újra később.                     |
| 503                  | `ServiceUnavailable` | Szolgáltatás ideiglenesen nem működik. Próbálkozzon újra később.                             |
|  |  |  |

*Válaszfejlécek*

| **Fejléc kulcs**     | **Kötelező** | **Leírás**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Igen          | Kérés azonosítója, az ügyféltől kapott.                                                                   |
| x-ms-correlationid | Igen          | Korrelációs azonosító által átadott az ügyfelet, egyébként ez-e a korrelációs azonosítóját.                   |
| x-ms-activityid    | Igen          | A szolgáltatás a kérelem követési egyedi karakterlánc-érték. Bármely egyeztetések szolgál. |
| Retry-After        | Nem           | Melyik ügyfél időköz ellenőrizheti.                                                       |
|  |  |  |

### <a name="saas-webhook"></a>SaaS Webhook

Egy SaaS-webhook proaktív módon a SaaS-szolgáltatás módosításait értesítésére szolgál. A bejegyzés API kell lennie a nem hitelesített, és a Microsoft szolgáltatás fogja meghívni. Az SaaS-szolgáltatás várhatóan az műveletek érvényesítéséhez, és engedélyezze a webhook értesítésre műveletek végrehajtása előtti API meghívása. 

*Törzs*

``` json
  {
    "id": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId":"cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "action": "Subscribe", // Subscribe/Unsubscribe/ChangePlan
    "operationRequestSource":"Azure",
    "timeStamp":"2018-12-01T00:00:00"
  }
```

| **Paraméter neve**     | **Adattípus** | **Leírás**                               |
|------------------------|---------------|-----------------------------------------------|
| id  | String       | A művelet által aktivált egyedi azonosítója.                |
| Tevékenységazonosító   | String        | A szolgáltatás a kérelem követési egyedi karakterlánc-érték. Bármely egyeztetések szolgál.               |
| subscriptionId                     | String        | Előfizetés erőforrás azonosítója, SaaS az Azure-ban.    |
| ajánlatazonosító                | String        | Ajánlat azonosítója, amelyre a felhasználó előfizetett. Csak a "Frissítés" művelet megadott.        |
| publisherId                | String        | Az SaaS-ajánlat a Gyártóazonosítóval         |
| planId                 | String        | Tervezze meg, hogy a felhasználó előfizetett azonosítója. Csak a "Frissítés" művelet megadott.          |
| action                 | String        | A művelet, amely ezt az értesítést kezdeményezi. A lehetséges értékek - aktiválása, töröl, a felfüggesztés, visszaállítása, frissítés          |
| timeStamp                 | String        | Időbélyeg értékét UTC formátumban, amikor az értesítés lett elindítva.          |
|  |  |  |


## <a name="next-steps"></a>További lépések

Szoftveresen is lekérheti a fejlesztők és a számítási feladatok, az ajánlatok és a közzétevő adatkezelési profilok használatával a [Cloud Partner Portal REST API-k](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
