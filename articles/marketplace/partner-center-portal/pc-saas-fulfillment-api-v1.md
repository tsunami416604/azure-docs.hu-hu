---
title: SaaS-teljesítési API-k v1 | Azure piactér
description: A cikk azt ismerteti, hogyan hozhat létre és kezelhet SaaS-ajánlatokat az Azure Marketplace-en a társított teljesítési v1 API-k használatával.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 05/23/2019
ms.author: evansma
ROBOTS: NOINDEX
ms.openlocfilehash: 99dd6db7003e0358ddde2438f6897cd767932227
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73816568"
---
# <a name="saas-fulfillment-apis-version-1-deprecated"></a>SaaS-teljesítési API-k 1-es verziója (elavult)

Ez a cikk bemutatja, hogyan hozhat létre SaaS-ajánlatokat API-kkal. A REST metódusokból és végpontokból álló API-k azért szükségesek, hogy engedélyezze az előfizetéseket az SaaS-ajánlathoz, ha az Azure-t választotta.  

> [!WARNING]
> A SaaS-teljesítési API ezen kezdeti verziója elavult; Ehelyett használja a [SaaS-beteljesülés API v2](./pc-saas-fulfillment-api-v2.md)-t.  Az API ezen kezdeti-verziója jelenleg csak a meglévő közzétevők kiszolgálására van fenntartva. 

A következő API-k segítenek a SaaS-szolgáltatás Azure-beli integrálásában:

-   Feloldás
-   Feliratkozás
-   Konvertálás
-   Leiratkozás


## <a name="api-methods-and-endpoints"></a>API-metódusok és-végpontok

A következő szakaszok ismertetik a SaaS-ajánlat előfizetésének engedélyezéséhez elérhető API-metódusokat és-végpontokat.


### <a name="marketplace-api-endpoint-and-api-version"></a>Marketplace API-végpont és API-verzió

Az Azure Marketplace API végpontja `https://marketplaceapi.microsoft.com`.

A jelenlegi API-verzió a `api-version=2017-04-15`.


### <a name="resolve-subscription"></a>Előfizetés feloldása

A feloldási végponton végrehajtandó művelet lehetővé teszi a felhasználók számára, hogy egy állandó erőforrás-AZONOSÍTÓval oldják fel a Piactéri jogkivonatot.  Az erőforrás-azonosító az SAAS-előfizetés egyedi azonosítója. 

Amikor egy felhasználó átirányítja egy ISV webhelyére, az URL-cím tartalmaz egy tokent a lekérdezési paraméterekben. Az ISV-nak ezt a tokent kell használnia, és el kell végeznie a feloldási kérést. A válasz tartalmazza az egyedi SAAS-előfizetés AZONOSÍTÓját, nevét, ajánlatának AZONOSÍTÓját, és megtervezi az erőforrást. Ez a jogkivonat csak egy órára érvényes.

*Kérés*

**POST**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=2017-04-15**

|  **Paraméter neve** |     **Leírás**                                      |
|  ------------------ |     ---------------------------------------------------- |
|  API-Version        |  A kérelemhez használni kívánt művelet verziója.   |
|  |  |


*Fejlécek*

| **Fejléc kulcsa**     | **Szükséges** | **Leírás**                                                                                                                                                                                                                  |
|--------------------|--------------|-----------------------------------------------------------|
| x-MS-kérelemazonosító     | Nem           | Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
| x-MS-correlationId | Nem           | Egyedi karakterlánc-érték a művelethez az ügyfélen. Ez a mező az ügyfél-művelet összes eseményét korrelálja a kiszolgálóoldali eseményekkel. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
| Content-Type       | Igen          | `application/json`                                        |
| engedély      | Igen          | A JSON webes jogkivonat (JWT) tulajdonosi jogkivonata.                    |
| x-MS-Marketplace-token| Igen| Az URL-cím jogkivonat-lekérdezési paramétere, ha a felhasználó az Azure-ból származó SaaS ISV-webhelyre van átirányítva. **Megjegyzés:** Ez a jogkivonat csak 1 órára érvényes. Emellett az URL-cím dekódolja a jogkivonat értékét a böngészőből a használat előtt.|
|  |  |  |
  

*Válasz törzse*

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
| id                 | Sztring        | Az SaaS-előfizetés azonosítója.          |
| subscriptionName| Sztring| Az Azure-beli felhasználó által az SaaS-szolgáltatásra való feliratkozáskor beállított SaaS-előfizetés neve.|
| OfferId            | Sztring        | Az ajánlat azonosítója, amelyhez a felhasználó előfizetett. |
| planId             | Sztring        | A felhasználó által előfizetett csomag azonosítója.  |
|  |  |  |


*Reagálási kódok*

| **HTTP-állapotkód** | **Hibakód**     | **Leírás**                                                                         |
|----------------------|--------------------| --------------------------------------------------------------------------------------- |
| 200                  | `OK`                 | A jogkivonat feloldása sikerült.                                                            |
| 400                  | `BadRequest`         | A kötelező fejlécek hiányoznak vagy a megadott API-verzió érvénytelen. Nem sikerült feloldani a tokent, mert vagy a jogkivonat helytelen formátumú vagy lejárt (a jogkivonat csak 1 órára érvényes.) |
| 403                  | `Forbidden`          | A hívó nem jogosult a művelet végrehajtására.                                 |
| 429                  | `RequestThrottleId`  | A szolgáltatás foglalt feldolgozási kérelmeket használ, később próbálkozzon újra.                                |
| 503                  | `ServiceUnavailable` | A szolgáltatás átmenetileg nem érhető el, próbálkozzon újra később.                                        |
|  |  |  |


*Válasz fejlécei*

| **Fejléc kulcsa**     | **Szükséges** | **Leírás**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-kérelemazonosító     | Igen          | Az ügyféltől érkezett kérelem-azonosító.                                                                   |
| x-MS-correlationId | Igen          | A korrelációs azonosító, ha az ügyfél átadja, ellenkező esetben ez az érték a kiszolgálói korrelációs azonosító.                   |
| x-MS-tevékenységazonosító    | Igen          | Egyedi karakterlánc-érték a kérésnek a szolgáltatásból való nyomon követéséhez. Ezt az azonosítót használja a rendszer bármilyen egyeztetéshez. |
| Újrapróbálkozás ennyivel        | Nem           | Ez az érték csak 429-válaszra van beállítva.                                                                   |
|  |  |  |


### <a name="subscribe"></a>Feliratkozás

Az előfizetés-végpont lehetővé teszi a felhasználók számára, hogy egy adott csomag esetében egy SaaS-szolgáltatáshoz indítsanak előfizetést, és lehetővé tegyék a számlázást a kereskedelmi rendszeren.

**PUT**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{subscriptionId}* ? API-Version = 2017-04-15**

| **Paraméter neve**  | **Leírás**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Az SaaS-előfizetés egyedi azonosítója, amelyet a rendszer a jogkivonat feloldása után szerez be az API-n keresztül.                              |
| API-Version         | A kérelemhez használni kívánt művelet verziója. |
|  |  |

*Fejlécek*

|  **Fejléc kulcsa**        | **Szükséges** |  **Leírás**                                                  |
| ------------------     | ------------ | --------------------------------------------------------------------------------------- |
| x-MS-kérelemazonosító         |   Nem         | Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval. Ha ez nincs megadva, a rendszer létrehoz egy-t, és megadja a válasz fejléceit. |
| x-MS-correlationId     |   Nem         | Egyedi karakterlánc-érték a művelethez az ügyfélen. Ez az érték az ügyfél-művelet összes eseményének korrelációja a kiszolgálói oldalon lévő eseményekkel. Ha ez nincs megadva, a rendszer létrehoz egy-t, és megadja a válasz fejléceit. |
| If-Match/If-None-Match |   Nem         |   Erős érvényesítő ETag értéke.                                                          |
| Content-Type           |   Igen        |    `application/json`                                                                   |
|  engedély         |   Igen        |    A JSON webes jogkivonat (JWT) tulajdonosi jogkivonata.                                               |
| x-MS-Marketplace-Session-Mode| Nem | A száraz futtatási mód engedélyezése a SaaS-ajánlatra való feliratkozáskor. Ha be van állítva, a rendszer nem számítja fel az előfizetést. Ez az ISV-tesztelési forgatókönyvek esetében hasznos. Állítsa be a **"tesztfuttatási"** értékre.|
|  |  |  |

*Törzs*

``` json
{
    "lanId": "",
}
```

| **Elem neve** | **Adattípus** | **Leírás**                      |
|------------------|---------------|--------------------------------------|
| planId           | Szükséges Karakterlánc        | Az SaaS-szolgáltatás felhasználójának megtervezése az előfizetés.  |
|  |  |  |

*Reagálási kódok*

| **HTTP-állapotkód** | **Hibakód**     | **Leírás**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Az SaaS-előfizetés aktiválása egy adott csomagra vonatkozóan érkezett.                   |
| 400                  | `BadRequest`         | A kötelező fejlécek hiányoznak, vagy a JSON törzse helytelen formátumú. |
| 403                  | `Forbidden`          | A hívó nem jogosult a művelet végrehajtására.                   |
| 404                  | `NotFound`           | Az előfizetés nem található a megadott AZONOSÍTÓval                                  |
| 409                  | `Conflict`           | Egy másik művelet van folyamatban az előfizetésben.                     |
| 429                  | `RequestThrottleId`  | A szolgáltatás foglalt feldolgozási kérelmeket használ, később próbálkozzon újra.                  |
| 503                  | `ServiceUnavailable` | A szolgáltatás átmenetileg nem érhető el, próbálkozzon újra később.                          |
|  |  |  |

202-válasz esetén kövesse a kérési művelet állapotát a művelet – hely fejlécben. A hitelesítés ugyanaz, mint a piactér más API-jai.

*Válasz fejlécei*

| **Fejléc kulcsa**     | **Szükséges** | **Leírás**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-kérelemazonosító     | Igen          | Az ügyféltől érkezett kérelem-azonosító.                                                                   |
| x-MS-correlationId | Igen          | A korrelációs azonosító, ha az ügyfél átadja, ellenkező esetben ez az érték a kiszolgálói korrelációs azonosító.                   |
| x-MS-tevékenységazonosító    | Igen          | Egyedi karakterlánc-érték a kérésnek a szolgáltatásból való nyomon követéséhez. A rendszer ezt az értéket használja bármilyen egyeztetéshez. |
| Újrapróbálkozás ennyivel        | Igen          | Az az időtartam, amellyel az ügyfél megtekintheti az állapotát.                                                       |
| Művelet – hely | Igen          | Egy erőforrásra mutató hivatkozás a művelet állapotának lekéréséhez.                                                        |
|  |  |  |

### <a name="change-plan-endpoint"></a>Terv módosítása végpont

A módosítási végpont lehetővé teszi a felhasználó számára, hogy a jelenleg előfizetett tervet egy új csomagra alakítsa át.

**JAVÍTÁS**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{subscriptionId}* ? API-Version = 2017-04-15**

| **Paraméter neve**  | **Leírás**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | SaaS-előfizetés azonosítója.                              |
| API-Version         | A kérelemhez használni kívánt művelet verziója. |
|  |  |

*Fejlécek*

| **Fejléc kulcsa**          | **Szükséges** | **Leírás**                                                                                                                                                                                                                  |
|-------------------------|--------------|---------------------------------------------------------------------------------------------------------------------|
| x-MS-kérelemazonosító          | Nem           | Egyedi karakterlánc-érték az ügyféltől érkező kérés nyomon követéséhez. GUID-azonosítók ajánlása. Ha ez nincs megadva, a rendszer létrehoz egy-t, és megadja a válasz fejléceit.   |
| x-MS-correlationId      | Nem           | Egyedi karakterlánc-érték a művelethez az ügyfélen. Ez az érték az ügyfél-művelet összes eseményének korrelációja a kiszolgálói oldalon lévő eseményekkel. Ha ez nincs megadva, a rendszer létrehoz egy-t, és megadja a válasz fejléceit. |
| If-Match/If-None-Match | Nem           | Erős érvényesítő ETag értéke.                              |
| Content-Type            | Igen          | `application/json`                                        |
| engedély           | Igen          | A JSON webes jogkivonat (JWT) tulajdonosi jogkivonata.                    |
|  |  |  |

*Törzs*

```json
{
    "planId": ""
}
```

|  **Elem neve** |  **Adattípus**  | **Leírás**                              |
|  ---------------- | -------------   | --------------------------------------       |
|  planId           |  Szükséges Karakterlánc         | Az SaaS-szolgáltatás felhasználójának megtervezése az előfizetés.          |
|  |  |  |

*Reagálási kódok*

| **HTTP-állapotkód** | **Hibakód**     | **Leírás**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Az SaaS-előfizetés aktiválása egy adott csomagra vonatkozóan érkezett.                   |
| 400                  | `BadRequest`         | A kötelező fejlécek hiányoznak, vagy a JSON törzse helytelen formátumú. |
| 403                  | `Forbidden`          | A hívó nem jogosult a művelet végrehajtására.                   |
| 404                  | `NotFound`           | Az előfizetés nem található a megadott AZONOSÍTÓval                                  |
| 409                  | `Conflict`           | Egy másik művelet van folyamatban az előfizetésben.                     |
| 429                  | `RequestThrottleId`  | A szolgáltatás foglalt feldolgozási kérelmeket használ, később próbálkozzon újra.                  |
| 503                  | `ServiceUnavailable` | A szolgáltatás átmenetileg nem érhető el, próbálkozzon újra később.                          |
|  |  |  |

*Válasz fejlécei*

| **Fejléc kulcsa**     | **Szükséges** | **Leírás**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-kérelemazonosító     | Igen          | Az ügyféltől érkezett kérelem-azonosító.                                                                   |
| x-MS-correlationId | Igen          | A korrelációs azonosító, ha az ügyfél átadja, ellenkező esetben ez az érték a kiszolgálói korrelációs azonosító.                   |
| x-MS-tevékenységazonosító    | Igen          | Egyedi karakterlánc-érték a kérésnek a szolgáltatásból való nyomon követéséhez. A rendszer ezt az értéket használja bármilyen egyeztetéshez. |
| Újrapróbálkozás ennyivel        | Igen          | Az az időtartam, amellyel az ügyfél megtekintheti az állapotát.                                                       |
| Művelet – hely | Igen          | Egy erőforrásra mutató hivatkozás a művelet állapotának lekéréséhez.                                                        |
|  |  |  |

### <a name="delete-subscription"></a>Előfizetés törlése

A feliratkozási végpont törlés művelete lehetővé teszi a felhasználó számára egy adott AZONOSÍTÓJÚ előfizetés törlését.

*Kérés*

**DELETE**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{subscriptionId}* ? API-Version = 2017-04-15**

| **Paraméter neve**  | **Leírás**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | SaaS-előfizetés azonosítója.                              |
| API-Version         | A kérelemhez használni kívánt művelet verziója. |
|  |  |

*Fejlécek*

| **Fejléc kulcsa**     | **Szükséges** | **Leírás**                                                                                                                                                                                                                  |
|--------------------|--------------| ----------------------------------------------------------|
| x-MS-kérelemazonosító     | Nem           | Egyedi karakterlánc-érték az ügyféltől érkező kérés nyomon követéséhez. GUID-azonosítók ajánlása. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.                                                           |
| x-MS-correlationId | Nem           | Egyedi karakterlánc-érték a művelethez az ügyfélen. Ez az érték az ügyfél-művelet összes eseményének korrelációja a kiszolgálói oldalon lévő eseményekkel. Ha ez nincs megadva, a rendszer létrehoz egy-t, és megadja a válasz fejléceit. |
| engedély      | Igen          | A JSON webes jogkivonat (JWT) tulajdonosi jogkivonata.                    |
|  |  |  |

*Reagálási kódok*

| **HTTP-állapotkód** | **Hibakód**     | **Leírás**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Az SaaS-előfizetés aktiválása egy adott csomagra vonatkozóan érkezett.                   |
| 400                  | `BadRequest`         | A kötelező fejlécek hiányoznak, vagy a JSON törzse helytelen formátumú. |
| 403                  | `Forbidden`          | A hívó nem jogosult a művelet végrehajtására.                   |
| 404                  | `NotFound`           | Az előfizetés nem található a megadott AZONOSÍTÓval                                  |
| 429                  | `RequestThrottleId`  | A szolgáltatás foglalt feldolgozási kérelmeket használ, próbálkozzon újra később.                  |
| 503                  | `ServiceUnavailable` | A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra később.                          |
|  |  |  |

202-válasz esetén kövesse a kérési művelet állapotát a művelet – hely fejlécben. A hitelesítés ugyanaz, mint a piactér más API-jai.

*Válasz fejlécei*

| **Fejléc kulcsa**     | **Szükséges** | **Leírás**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-kérelemazonosító     | Igen          | Az ügyféltől érkezett kérelem-azonosító.                                                                   |
| x-MS-correlationId | Igen          | A korrelációs azonosító, ha az ügyfél továbbítja, ellenkező esetben ez a kiszolgálói korrelációs azonosító.                   |
| x-MS-tevékenységazonosító    | Igen          | Egyedi karakterlánc-érték a kérésnek a szolgáltatásból való nyomon követéséhez. Ez minden egyeztetéshez használatos. |
| Újrapróbálkozás ennyivel        | Igen          | Az az időtartam, amellyel az ügyfél megtekintheti az állapotát.                                                       |
| Művelet – hely | Igen          | Egy erőforrásra mutató hivatkozás a művelet állapotának lekéréséhez.                                                        |
|   |  |  |

### <a name="get-operation-status"></a>Művelet állapotának beolvasása

Ez a végpont lehetővé teszi, hogy a felhasználó nyomon követhesse az aktivált aszinkron művelet állapotát (előfizetés/leiratkozás/módosítási terv).

*Kérés*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/operations/ *{operationId}* ? API-Version = 2017-04-15**

| **Paraméter neve**  | **Leírás**                                       |
|---------------------|-------------------------------------------------------|
| operationId         | Az aktivált művelet egyedi azonosítója.                |
| API-Version         | A kérelemhez használni kívánt művelet verziója. |
|  |  |

*Fejlécek*

| **Fejléc kulcsa**     | **Szükséges** | **Leírás**                                                                                                                                                                                                                  |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------------------------|
| x-MS-kérelemazonosító     | Nem           | Egyedi karakterlánc-érték az ügyféltől érkező kérés nyomon követéséhez. GUID-azonosítók ajánlása. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.   |
| x-MS-correlationId | Nem           | Egyedi karakterlánc-érték a művelethez az ügyfélen. Ez az érték az ügyfél-művelet összes eseményének korrelációja a kiszolgálói oldalon lévő eseményekkel. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
| engedély      | Igen          | A JSON webes jogkivonat (JWT) tulajdonosi jogkivonata.                    |
|  |  |  | 

*Válasz törzse*

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
| id                 | Sztring        | A művelet azonosítója.                                                                      |
| status             | Felsorolás          | A művelet állapota, a következők egyike: `In Progress`, `Succeeded`vagy `Failed`.          |
| resourceLocation   | Sztring        | A létrehozott vagy módosított előfizetésre mutató hivatkozás. Ez segíti az ügyfelet a frissített állapot utáni művelet megszerzésében. Ez az érték nincs beállítva `Unsubscribe` műveletekhez. |
| létrehozott            | DateTime      | Művelet létrehozásának időpontja (UTC).                                                           |
| lastModified       | DateTime      | A művelet utolsó frissítése UTC szerint.                                                      |
|  |  |  |

*Reagálási kódok*

| **HTTP-állapotkód** | **Hibakód**     | **Leírás**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | A Get kérelem sikeresen megoldódott, és a törzs tartalmazza a választ.    |
| 400                  | `BadRequest`         | A kötelező fejlécek hiányoznak, vagy a megadott API-verzió érvénytelen. |
| 403                  | `Forbidden`          | A hívó nem jogosult a művelet végrehajtására.                      |
| 404                  | `NotFound`           | Az előfizetés nem található a megadott AZONOSÍTÓval.                                     |
| 429                  | `RequestThrottleId`  | A szolgáltatás foglalt feldolgozási kérelmeket használ, később próbálkozzon újra.                     |
| 503                  | `ServiceUnavailable` | A szolgáltatás átmenetileg nem érhető el, próbálkozzon újra később.                             |
|  |  |  |

*Válasz fejlécei*

| **Fejléc kulcsa**     | **Szükséges** | **Leírás**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-kérelemazonosító     | Igen          | Az ügyféltől érkezett kérelem-azonosító.                                                                   |
| x-MS-correlationId | Igen          | A korrelációs azonosító, ha az ügyfél továbbítja, ellenkező esetben ez a kiszolgálói korrelációs azonosító.                   |
| x-MS-tevékenységazonosító    | Igen          | Egyedi karakterlánc-érték a kérésnek a szolgáltatásból való nyomon követéséhez. Ez minden egyeztetéshez használatos. |
| Újrapróbálkozás ennyivel        | Igen          | Az az időtartam, amellyel az ügyfél megtekintheti az állapotát.                                                       |
|  |  |  |

### <a name="get-subscription"></a>Előfizetés lekérése

A Get művelet az előfizetés-végponton lehetővé teszi, hogy a felhasználó egy adott erőforrás-azonosítóval lekérje az előfizetést.

*Kérés*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{subscriptionId}* ? API-Version = 2017-04-15**

| **Paraméter neve**  | **Leírás**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | SaaS-előfizetés azonosítója.                              |
| API-Version         | A kérelemhez használni kívánt művelet verziója. |
|  |  |

*Fejlécek*

| **Fejléc kulcsa**     | **Szükséges** | **Leírás**                                                                                           |
|--------------------|--------------|-----------------------------------------------------------------------------------------------------------|
| x-MS-kérelemazonosító     | Nem           | Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.                                                           |
| x-MS-correlationId | Nem           | Egyedi karakterlánc-érték a művelethez az ügyfélen. Ez az érték az ügyfél-művelet összes eseményének korrelációja a kiszolgálói oldalon lévő eseményekkel. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
| engedély      | Igen          | A JSON webes jogkivonat (JWT) tulajdonosi jogkivonata.                                                                    |
|  |  |  |

*Válasz törzse*

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
| id                     | Sztring        | SaaS-előfizetési erőforrás azonosítója az Azure-ban.    |
| OfferId                | Sztring        | Az ajánlat azonosítója, amelyhez a felhasználó előfizetett.         |
| planId                 | Sztring        | A felhasználó által előfizetett csomag azonosítója.          |
| saasSubscriptionName   | Sztring        | Az SaaS-előfizetés neve.                |
| saasSubscriptionStatus | Felsorolás          | Művelet állapota.  A következők egyike:  <br/> - `Subscribed`: az előfizetés aktív.  <br/> - `Pending`: a felhasználó hozza létre az erőforrást, de az ISV nem aktiválja.   <br/> - `Unsubscribed`: a felhasználó leiratkozott.   <br/> - `Suspended`: a felhasználó felfüggesztette az előfizetést.   <br/> - `Deactivated`: az Azure-előfizetés fel van függesztve.  |
| létrehozott                | DateTime      | Előfizetés-létrehozási időbélyeg értéke UTC szerint. |
| lastModified           | DateTime      | Az előfizetés módosított időbélyeg-értéke UTC szerint. |
|  |  |  |

*Reagálási kódok*

| **HTTP-állapotkód** | **Hibakód**     | **Leírás**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | A Get kérelem sikeresen megoldódott, és a törzs tartalmazza a választ.    |
| 400                  | `BadRequest`         | A kötelező fejlécek hiányoznak, vagy a megadott API-verzió érvénytelen. |
| 403                  | `Forbidden`          | A hívó nem jogosult a művelet végrehajtására.                      |
| 404                  | `NotFound`           | Az előfizetés nem található a megadott AZONOSÍTÓval                                     |
| 429                  | `RequestThrottleId`  | A szolgáltatás foglalt feldolgozási kérelmeket használ, később próbálkozzon újra.                     |
| 503                  | `ServiceUnavailable` | A szolgáltatás átmenetileg nem érhető el, próbálkozzon újra később.                             |
|  |  |  |

*Válasz fejlécei*

| **Fejléc kulcsa**     | **Szükséges** | **Leírás**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-kérelemazonosító     | Igen          | Az ügyféltől érkezett kérelem-azonosító.                                                                   |
| x-MS-correlationId | Igen          | A korrelációs azonosító, ha az ügyfél továbbítja, ellenkező esetben ez a kiszolgálói korrelációs azonosító.                   |
| x-MS-tevékenységazonosító    | Igen          | Egyedi karakterlánc-érték a kérésnek a szolgáltatásból való nyomon követéséhez. Ez minden egyeztetéshez használatos. |
| Újrapróbálkozás ennyivel        | Nem           | Az az időtartam, amellyel az ügyfél megtekintheti az állapotát.                                                       |
| eTag               | Igen          | Egy erőforrásra mutató hivatkozás a művelet állapotának lekéréséhez.                                                        |
|  |  |  |

### <a name="get-subscriptions"></a>Előfizetések beolvasása

A Get művelet az előfizetések végponton lehetővé teszi, hogy a felhasználó lekérje az összes ajánlat összes előfizetését az ISV-ból.

*Kérés*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2017-04-15**

| **Paraméter neve**  | **Leírás**                                       |
|---------------------|-------------------------------------------------------|
| API-Version         | A kérelemhez használni kívánt művelet verziója. |
|  |  |

*Fejlécek*

| **Fejléc kulcsa**     | **Szükséges** | **Leírás**                                           |
|--------------------|--------------|-----------------------------------------------------------|
| x-MS-kérelemazonosító     | Nem           | Egyedi karakterlánc-érték az ügyféltől érkező kérés nyomon követéséhez. GUID-azonosítók ajánlása. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.             |
| x-MS-correlationId | Nem           | Egyedi karakterlánc-érték a művelethez az ügyfélen. Ez az érték az ügyfél-művelet összes eseményének korrelációja a kiszolgálói oldalon lévő eseményekkel. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
| engedély      | Igen          | A JSON webes jogkivonat (JWT) tulajdonosi jogkivonata.                    |
|  |  |  |

*Válasz törzse*

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
| id                     | Sztring        | SaaS-előfizetési erőforrás azonosítója az Azure-ban    |
| OfferId                | Sztring        | A felhasználó által előfizetett ajánlat azonosítója         |
| planId                 | Sztring        | A felhasználó által előfizetett csomag azonosítója          |
| saasSubscriptionName   | Sztring        | Az SaaS-előfizetés neve                |
| saasSubscriptionStatus | Felsorolás          | Művelet állapota.  A következők egyike:  <br/> - `Subscribed`: az előfizetés aktív.  <br/> - `Pending`: a felhasználó hozza létre az erőforrást, de az ISV nem aktiválja.   <br/> - `Unsubscribed`: a felhasználó leiratkozott.   <br/> - `Suspended`: a felhasználó felfüggesztette az előfizetést.   <br/> - `Deactivated`: az Azure-előfizetés fel van függesztve.  |
| létrehozott                | DateTime      | Előfizetés-létrehozási időbélyeg értéke UTC szerint |
| lastModified           | DateTime      | Előfizetés módosított időbélyeg-értéke UTC szerint |
|  |  |  |

*Reagálási kódok*

| **HTTP-állapotkód** | **Hibakód**     | **Leírás**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | A Get kérelem sikeresen megoldódott, és a törzs tartalmazza a választ.    |
| 400                  | `BadRequest`         | A kötelező fejlécek hiányoznak, vagy a megadott API-verzió érvénytelen. |
| 403                  | `Forbidden`          | A hívó nem jogosult a művelet végrehajtására.                      |
| 404                  | `NotFound`           | Az előfizetés nem található a megadott AZONOSÍTÓval                                     |
| 429                  | `RequestThrottleId`  | A szolgáltatás foglalt feldolgozási kérelmeket használ, próbálkozzon újra később.                     |
| 503                  | `ServiceUnavailable` | A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra később.                             |
|  |  |  |

*Válasz fejlécei*

| **Fejléc kulcsa**     | **Szükséges** | **Leírás**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-kérelemazonosító     | Igen          | Az ügyféltől érkezett kérelem-azonosító.                                                                   |
| x-MS-correlationId | Igen          | A korrelációs azonosító, ha az ügyfél továbbítja, ellenkező esetben ez a kiszolgálói korrelációs azonosító.                   |
| x-MS-tevékenységazonosító    | Igen          | Egyedi karakterlánc-érték a kérésnek a szolgáltatásból való nyomon követéséhez. Ez minden egyeztetéshez használatos. |
| Újrapróbálkozás ennyivel        | Nem           | Az az időtartam, amellyel az ügyfél megtekintheti az állapotát.                                                       |
|  |  |  |

### <a name="saas-webhook"></a>SaaS-webhook

SaaS-webhookot használ a változások proaktív módon történő értesítésére az SaaS szolgáltatáshoz. A POST API-nak nem hitelesítettnek kell lennie, és a Microsoft szolgáltatása fogja meghívni. Az SaaS szolgáltatásnak az operatív API-t kell meghívnia az ellenőrzéshez és az engedélyezéshez, mielőtt lépéseket hozna a webhook-értesítésen. 

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
| id  | Sztring       | Az aktivált művelet egyedi azonosítója.                |
| Tevékenységazonosító   | Sztring        | Egyedi karakterlánc-érték a kérésnek a szolgáltatásból való nyomon követéséhez. Ez minden egyeztetéshez használatos.               |
| subscriptionId                     | Sztring        | SaaS-előfizetési erőforrás azonosítója az Azure-ban.    |
| OfferId                | Sztring        | Az ajánlat azonosítója, amelyhez a felhasználó előfizetett. Csak a "frissítés" művelettel biztosítva.        |
| publisherId                | Sztring        | Az SaaS-ajánlat közzétevő-azonosítója         |
| planId                 | Sztring        | A felhasználó által előfizetett csomag azonosítója. Csak a "frissítés" művelettel biztosítva.          |
| action                 | Sztring        | Az értesítést kiváltó művelet. Lehetséges értékek – aktiválás, törlés, felfüggesztés, visszaállítása, frissítés          |
| Időbélyeg                 | Sztring        | Az értesítés aktiválásakor az időbélyeg értéke UTC szerint.          |
|  |  |  |


## <a name="next-steps"></a>További lépések

A fejlesztők a [Cloud Partner Portal REST API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)-k használatával programozott módon is lekérhetik és módosíthatják a munkaterheléseket, az ajánlatokat és a közzétevői profilokat.
