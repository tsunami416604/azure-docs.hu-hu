---
title: SaaS teljesítési API-k v1 | Azure Piactér
description: Bemutatja, hogyan hozhat létre és kezelhet saas-ajánlatot az Azure Piactéren a kapcsolódó Fulfillment v1 API-k használatával.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 05/23/2019
ms.author: dsindona
ROBOTS: NOINDEX
ms.openlocfilehash: 3ec8373288a2ea5809ee5d349c52c57051586035
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288342"
---
# <a name="saas-fulfillment-apis-version-1-deprecated"></a>SaaS teljesítési API-k 1-es verzió (elavult)

Ez a cikk bemutatja, hogyan hozhat létre egy SaaS-ajánlat API-kat. A REST-metódusokból és végpontokból álló API-k szükségesek az SaaS-ajánlat előfizetéseinek engedélyezéséhez, ha kiválasztotta az Eladás az Azure-on keresztül lehetőséget.  

> [!WARNING]
> A SaaS-teljesítési API kezdeti verziója elavult; ehelyett használja a [SaaS Fulfillment API V2-t.](./pc-saas-fulfillment-api-v2.md)  Az API kezdeti verziója jelenleg csak a meglévő közzétevők kiszolgálása érdekében van karbantartva. 

A következő API-k állnak rendelkezésre a SaaS-szolgáltatás Azure-ral való integrálásához:

-   Feloldás
-   Feliratkozás
-   Konvertálás
-   Leiratkozás


## <a name="api-methods-and-endpoints"></a>API-metódusok és végpontok

A következő szakaszok ismertetik az API-metódusok és a saas-ajánlat előfizetések engedélyezéséhez elérhető végpontok.


### <a name="marketplace-api-endpoint-and-api-version"></a>Marketplace API-végpont és API-verzió

Az Azure Marketplace API `https://marketplaceapi.microsoft.com`végpontja a.

A jelenlegi API-verzió `api-version=2017-04-15`.


### <a name="resolve-subscription"></a>Előfizetés feloldása

Post művelet a végpont feloldása lehetővé teszi a felhasználók számára, hogy feloldja a piactéri jogkivonatot egy állandó erőforrás-azonosító.  Az erőforrás-azonosító a SAAS-előfizetés egyedi azonosítója. 

Amikor egy felhasználót átirányítanak egy független eszközv webhelyére, az URL-cím egy jogkivonatot tartalmaz a lekérdezési paraméterekben. A isv várhatóan ezt a jogkivonatot használja, és a probléma megoldására irányuló kérelmet. A válasz tartalmazza az egyedi SAAS-előfizetés-azonosítót, nevet, ajánlatazonosítót és az erőforrás tervét. Ez a jogkivonat csak egy óráig érvényes.

*Kérés*

**POST**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=2017-04-15**

|  **Paraméter neve** |     **Leírás**                                      |
|  ------------------ |     ---------------------------------------------------- |
|  api-verzió        |  A kérelemhez használandó művelet verziója.   |
|  |  |


*Fejlécek*

| **Fejléckulcs**     | **Szükséges** | **Leírás**                                                                                                                                                                                                                  |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Nem           | Egyedi karakterlánc-érték az ügyféltől érkező kérés nyomon követéséhez, lehetőleg GUID. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és megadja a válaszfejlécekben.  |
| x-ms-correlationid | Nem           | Az ügyfélen végzett művelet egyedi karakterlánc-értéke. Ez a mező az ügyfélműveletből származó összes eseményt összekapcsolja a kiszolgálói oldalon lévő eseményekkel. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és megadja a válaszfejlécekben. |
| Tartalomtípus       | Igen          | `application/json`                                        |
| engedélyezés      | Igen          | A JSON webes jogkivonat (JWT) tulajdonosi token.                    |
| x-ms-piactér-token| Igen| A token lekérdezési paraméter az URL-címben, amikor a felhasználó átirányítja a SaaS ISV webhelyére az Azure-ból. **Megjegyzés:** Ez a jogkivonat csak 1 óráig érvényes. Ezenkívül az URL-cím dekódolja a token értékét a böngészőből, mielőtt használná.|
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
| id                 | Sztring        | A SaaS-előfizetés azonosítója.          |
| subscriptionName| Sztring| A felhasználó által az Azure-ban beállított SaaS-előfizetés neve, miközben a SaaS-szolgáltatásra való feliratkozás közben rendelkezik.|
| OfferId            | Sztring        | Ajánlatazonosító, amelyre a felhasználó előfizetett. |
| planId között             | Sztring        | A csomag azonosítója, amelyre a felhasználó előfizetett.  |
|  |  |  |


*Válaszkódok*

| **HTTP-állapotkód** | **Hibakód**     | **Leírás**                                                                         |
|----------------------|--------------------| --------------------------------------------------------------------------------------- |
| 200                  | `OK`                 | A token sikeresen feloldva.                                                            |
| 400                  | `BadRequest`         | A szükséges fejlécek hiányoznak, vagy érvénytelen API-verzió van megadva. Nem sikerült feloldani a jogkivonatot, mert a jogkivonat hibásan formázott vagy lejárt (a jogkivonat csak 1 óráig érvényes a létrehozás után). |
| 403                  | `Forbidden`          | A hívó nem jogosult a művelet végrehajtására.                                 |
| 429                  | `RequestThrottleId`  | A szolgáltatás a kérelmek feldolgozásával van elfoglalva, majd próbálkozzon újra.                                |
| 503                  | `ServiceUnavailable` | A szolgáltatás átmenetileg leállt, később próbálkozzon újra.                                        |
|  |  |  |


*Válaszfejlécek*

| **Fejléckulcs**     | **Szükséges** | **Leírás**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Igen          | Az ügyféltől kapott kérelemazonosító.                                                                   |
| x-ms-correlationid | Igen          | Korrelációs azonosító, ha az ügyfél átadta, ellenkező esetben ez az érték a kiszolgáló korrelációs azonosítója.                   |
| x-ms-activityid    | Igen          | A szolgáltatástól érkező kérelem nyomon követéséhez egyedi karakterlánc-érték. Ez az azonosító minden egyeztetéshez használható. |
| Újrapróbálkozás utána        | Nem           | Ez az érték csak 429-es válaszra van beállítva.                                                                   |
|  |  |  |


### <a name="subscribe"></a>Feliratkozás

Az előfizetési végpont lehetővé teszi a felhasználók számára, hogy egy adott csomag SaaS-szolgáltatásra való előfizetést indítsanak, és engedélyezze a számlázást a kereskedelmi rendszerben.

**Tesz**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Paraméter neve**  | **Leírás**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | A jogkivonat feloldása API-n keresztül történő feloldása után kapott SaaS-előfizetés egyedi azonosítója.                              |
| api-verzió         | A kérelemhez használandó művelet verziója. |
|  |  |

*Fejlécek*

|  **Fejléckulcs**        | **Szükséges** |  **Leírás**                                                  |
| ------------------     | ------------ | --------------------------------------------------------------------------------------- |
| x-ms-requestid         |   Nem         | Egyedi karakterlánc-érték az ügyféltől érkező kérés nyomon követéséhez, lehetőleg GUID. Ha ez nem biztosított, a válaszfejlécek létrehoznak és megadják az egyiket. |
| x-ms-correlationid     |   Nem         | Az ügyfélen végzett művelet egyedi karakterlánc-értéke. Ez az érték az ügyfélművelet összes eseményének a kiszolgálóoldali eseményekkel való korrelációjára szolgál. Ha ez nem biztosított, a válaszfejlécek létrehoznak és megadják az egyiket. |
| If-Match/If-None-Match |   Nem         |   Erős validator ETag érték.                                                          |
| tartalomtípus           |   Igen        |    `application/json`                                                                   |
|  engedélyezés         |   Igen        |    A JSON webes jogkivonat (JWT) tulajdonosi token.                                               |
| x-ms-piactér-session-mode| Nem | Jelölve a száraz futtatási mód engedélyezéséhez, miközben saas-ajánlatra iratkozik fel. Ha be van állítva, az előfizetés nem kerül felszámolásra. Ez az isv tesztelési forgatókönyvek esetén hasznos. Kérjük, állítsa be a **"dryrun"**|
|  |  |  |

*Törzs*

``` json
{
    "lanId": "",
}
```

| **Elem neve** | **Adattípus** | **Leírás**                      |
|------------------|---------------|--------------------------------------|
| planId között           | (Kötelező) Karakterlánc        | A SaaS-szolgáltatás felhasználójának csomagazonosítója előfizet.  |
|  |  |  |

*Válaszkódok*

| **HTTP-állapotkód** | **Hibakód**     | **Leírás**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Adott csomaghoz kapott SaaS-előfizetés-aktiválás.                   |
| 400                  | `BadRequest`         | A szükséges fejlécek hiányoznak, vagy a JSON törzse hibásan formázott. |
| 403                  | `Forbidden`          | A hívó nem jogosult a művelet végrehajtására.                   |
| 404                  | `NotFound`           | Az előfizetés nem található a megadott azonosítóval                                  |
| 409                  | `Conflict`           | Egy másik művelet van folyamatban az előfizetés.                     |
| 429                  | `RequestThrottleId`  | A szolgáltatás a kérelmek feldolgozásával van elfoglalva, majd próbálkozzon újra.                  |
| 503                  | `ServiceUnavailable` | A szolgáltatás átmenetileg leállt, később próbálkozzon újra.                          |
|  |  |  |

A 202-es válasz, kövesse nyomon a kérelem művelet állapotát a "Művelet helye" fejléc. A hitelesítés megegyezik a többi Piactér API-k.

*Válaszfejlécek*

| **Fejléckulcs**     | **Szükséges** | **Leírás**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Igen          | Az ügyféltől kapott kérelemazonosító.                                                                   |
| x-ms-correlationid | Igen          | Korrelációs azonosító, ha az ügyfél átadta, ellenkező esetben ez az érték a kiszolgáló korrelációs azonosítója.                   |
| x-ms-activityid    | Igen          | A szolgáltatástól érkező kérelem nyomon követéséhez egyedi karakterlánc-érték. Ez az érték minden egyeztetéshez használatos. |
| Újrapróbálkozás utána        | Igen          | Az az időköz, amellyel az ügyfél ellenőrizheti az állapotot.                                                       |
| Művelet-hely | Igen          | Hivatkozás egy erőforrásra a művelet állapotának leválasztásához.                                                        |
|  |  |  |

### <a name="change-plan-endpoint"></a>Tervvégpont módosítása

A módosítási végpont lehetővé teszi a felhasználó számára, hogy a jelenleg előfizetett csomagját új csomaggá alakítsa át.

**Javítás**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Paraméter neve**  | **Leírás**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | SaaS-előfizetés azonosítója.                              |
| api-verzió         | A kérelemhez használandó művelet verziója. |
|  |  |

*Fejlécek*

| **Fejléckulcs**          | **Szükséges** | **Leírás**                                                                                                                                                                                                                  |
|-------------------------|--------------|---------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid          | Nem           | Egyedi karakterlánc-érték az ügyféltől érkező kérelem nyomon követéséhez. Javasoljuk a GUID. Ha ez nem biztosított, a válaszfejlécek létrehoznak és megadják az egyiket.   |
| x-ms-correlationid      | Nem           | Az ügyfélen végzett művelet egyedi karakterlánc-értéke. Ez az érték az ügyfélművelet összes eseményének a kiszolgálóoldali eseményekkel való korrelációjára szolgál. Ha ez nem biztosított, a válaszfejlécek létrehoznak és megadják az egyiket. |
| If-Match /If-None-Match | Nem           | Erős validator ETag érték.                              |
| tartalomtípus            | Igen          | `application/json`                                        |
| engedélyezés           | Igen          | A JSON webes jogkivonat (JWT) tulajdonosi token.                    |
|  |  |  |

*Törzs*

```json
{
    "planId": ""
}
```

|  **Elem neve** |  **Adattípus**  | **Leírás**                              |
|  ---------------- | -------------   | --------------------------------------       |
|  planId között           |  (Kötelező) Karakterlánc         | A SaaS-szolgáltatás felhasználójának csomagazonosítója előfizet.          |
|  |  |  |

*Válaszkódok*

| **HTTP-állapotkód** | **Hibakód**     | **Leírás**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Adott csomaghoz kapott SaaS-előfizetés-aktiválás.                   |
| 400                  | `BadRequest`         | A szükséges fejlécek hiányoznak, vagy a JSON törzse hibásan formázott. |
| 403                  | `Forbidden`          | A hívó nem jogosult a művelet végrehajtására.                   |
| 404                  | `NotFound`           | Az előfizetés nem található a megadott azonosítóval                                  |
| 409                  | `Conflict`           | Egy másik művelet van folyamatban az előfizetés.                     |
| 429                  | `RequestThrottleId`  | A szolgáltatás a kérelmek feldolgozásával van elfoglalva, majd próbálkozzon újra.                  |
| 503                  | `ServiceUnavailable` | A szolgáltatás átmenetileg leállt, később próbálkozzon újra.                          |
|  |  |  |

*Válaszfejlécek*

| **Fejléckulcs**     | **Szükséges** | **Leírás**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Igen          | Az ügyféltől kapott kérelemazonosító.                                                                   |
| x-ms-correlationid | Igen          | Korrelációs azonosító, ha az ügyfél átadta, ellenkező esetben ez az érték a kiszolgáló korrelációs azonosítója.                   |
| x-ms-activityid    | Igen          | A szolgáltatástól érkező kérelem nyomon követéséhez egyedi karakterlánc-érték. Ez az érték minden egyeztetéshez használatos. |
| Újrapróbálkozás utána        | Igen          | Az az időköz, amellyel az ügyfél ellenőrizheti az állapotot.                                                       |
| Művelet-hely | Igen          | Hivatkozás egy erőforrásra a művelet állapotának leválasztásához.                                                        |
|  |  |  |

### <a name="delete-subscription"></a>Előfizetés törlése

Az előfizetési végpont törlési művelete lehetővé teszi, hogy a felhasználó egy adott azonosítóval rendelkező előfizetést töröljön.

*Kérés*

**Töröl**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Paraméter neve**  | **Leírás**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | SaaS-előfizetés azonosítója.                              |
| api-verzió         | A kérelemhez használandó művelet verziója. |
|  |  |

*Fejlécek*

| **Fejléckulcs**     | **Szükséges** | **Leírás**                                                                                                                                                                                                                  |
|--------------------|--------------| ----------------------------------------------------------|
| x-ms-requestid     | Nem           | Egyedi karakterlánc-érték az ügyféltől érkező kérelem nyomon követéséhez. Javasoljuk a GUID. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és megadja a válaszfejlécekben.                                                           |
| x-ms-correlationid | Nem           | Az ügyfélen végzett művelet egyedi karakterlánc-értéke. Ez az érték az ügyfélművelet összes eseményének a kiszolgálóoldali eseményekkel való korrelációjára szolgál. Ha ez nem biztosított, a válaszfejlécek létrehoznak és megadják az egyiket. |
| engedélyezés      | Igen          | A JSON webes jogkivonat (JWT) tulajdonosi token.                    |
|  |  |  |

*Válaszkódok*

| **HTTP-állapotkód** | **Hibakód**     | **Leírás**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Adott csomaghoz kapott SaaS-előfizetés-aktiválás.                   |
| 400                  | `BadRequest`         | A szükséges fejlécek hiányoznak, vagy a JSON törzse hibásan formázott. |
| 403                  | `Forbidden`          | A hívó nem jogosult a művelet végrehajtására.                   |
| 404                  | `NotFound`           | Az előfizetés nem található a megadott azonosítóval                                  |
| 429                  | `RequestThrottleId`  | A szolgáltatás foglalt a kérelmek feldolgozásával, próbálkozzon később.                  |
| 503                  | `ServiceUnavailable` | A szolgáltatás átmenetileg leállt. Próbálkozzon később.                          |
|  |  |  |

A 202-es válasz, kövesse nyomon a kérelem művelet állapotát a "Művelet helye" fejléc. A hitelesítés megegyezik a többi Piactér API-k.

*Válaszfejlécek*

| **Fejléckulcs**     | **Szükséges** | **Leírás**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Igen          | Az ügyféltől kapott kérelemazonosító.                                                                   |
| x-ms-correlationid | Igen          | Korrelációs azonosító, ha az ügyfél átadta, ellenkező esetben ez a kiszolgáló korrelációs azonosítója.                   |
| x-ms-activityid    | Igen          | A szolgáltatástól érkező kérelem nyomon követéséhez egyedi karakterlánc-érték. Ez minden egyeztetéshez használatos. |
| Újrapróbálkozás utána        | Igen          | Az az időköz, amellyel az ügyfél ellenőrizheti az állapotot.                                                       |
| Művelet-hely | Igen          | Hivatkozás egy erőforrásra a művelet állapotának leválasztásához.                                                        |
|   |  |  |

### <a name="get-operation-status"></a>Művelet állapotának beolvasása

Ez a végpont lehetővé teszi a felhasználó számára, hogy nyomon kövesse az aktivált aszinkron művelet állapotát (Előfizetés/Leiratkozás/Csomag módosítása).

*Kérés*

**Kap**

**https://marketplaceapi.microsoft.com/api/saas/operations/*{operationId}*?api-version=2017-04-15**

| **Paraméter neve**  | **Leírás**                                       |
|---------------------|-------------------------------------------------------|
| operationId         | Az aktivált művelet egyedi azonosítója.                |
| api-verzió         | A kérelemhez használandó művelet verziója. |
|  |  |

*Fejlécek*

| **Fejléckulcs**     | **Szükséges** | **Leírás**                                                                                                                                                                                                                  |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Nem           | Egyedi karakterlánc-érték az ügyféltől érkező kérelem nyomon követéséhez. Javasoljuk a GUID. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és megadja a válaszfejlécekben.   |
| x-ms-correlationid | Nem           | Az ügyfélen végzett művelet egyedi karakterlánc-értéke. Ez az érték az ügyfélművelet összes eseményének a kiszolgálóoldali eseményekkel való korrelációjára szolgál. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és megadja a válaszfejlécekben.  |
| engedélyezés      | Igen          | A JSON webes jogkivonat (JWT) tulajdonosi token.                    |
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
| status             | Felsorolás          | Műveleti állapot, az alábbiak `In Progress` `Succeeded`egyike: `Failed`, , vagy .          |
| resourceLocation   | Sztring        | Hivatkozás a létrehozott vagy módosított előfizetésre. Ez segít az ügyfélnek a frissített állapotutáni művelet leésében. Ez az érték `Unsubscribe` nincs beállítva a műveletekhez. |
| Létrehozott            | DateTime      | A művelet létrehozási ideje UTC-ben.                                                           |
| lastModified (utolsó módosítás: )       | DateTime      | Utolsó frissítés a művelet UTC-ben.                                                      |
|  |  |  |

*Válaszkódok*

| **HTTP-állapotkód** | **Hibakód**     | **Leírás**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | A lekérés sikeresen megoldódott, és a törzs tartalmazza a választ.    |
| 400                  | `BadRequest`         | Vagy hiányoznak a szükséges fejlécek, vagy érvénytelen API-verzió van megadva. |
| 403                  | `Forbidden`          | A hívó nem jogosult a művelet végrehajtására.                      |
| 404                  | `NotFound`           | Az előfizetés nem található a megadott azonosítóval.                                     |
| 429                  | `RequestThrottleId`  | A szolgáltatás a kérelmek feldolgozásával van elfoglalva, majd próbálkozzon újra.                     |
| 503                  | `ServiceUnavailable` | A szolgáltatás átmenetileg leállt, később próbálkozzon újra.                             |
|  |  |  |

*Válaszfejlécek*

| **Fejléckulcs**     | **Szükséges** | **Leírás**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Igen          | Az ügyféltől kapott kérelemazonosító.                                                                   |
| x-ms-correlationid | Igen          | Korrelációs azonosító, ha az ügyfél átadta, ellenkező esetben ez a kiszolgáló korrelációs azonosítója.                   |
| x-ms-activityid    | Igen          | A szolgáltatástól érkező kérelem nyomon követéséhez egyedi karakterlánc-érték. Ez minden egyeztetéshez használatos. |
| Újrapróbálkozás utána        | Igen          | Az az időköz, amellyel az ügyfél ellenőrizheti az állapotot.                                                       |
|  |  |  |

### <a name="get-subscription"></a>Előfizetés beszereznie

A Get művelet előfizetni végpont lehetővé teszi, hogy a felhasználó lekérni egy előfizetést egy adott erőforrás-azonosítóval.

*Kérés*

**Kap**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Paraméter neve**  | **Leírás**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | SaaS-előfizetés azonosítója.                              |
| api-verzió         | A kérelemhez használandó művelet verziója. |
|  |  |

*Fejlécek*

| **Fejléckulcs**     | **Szükséges** | **Leírás**                                                                                           |
|--------------------|--------------|-----------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Nem           | Egyedi karakterlánc-érték az ügyféltől érkező kérés nyomon követéséhez, lehetőleg GUID. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és megadja a válaszfejlécekben.                                                           |
| x-ms-correlationid | Nem           | Az ügyfélen végzett művelet egyedi karakterlánc-értéke. Ez az érték az ügyfélművelet összes eseményének a kiszolgálóoldali eseményekkel való korrelációjára szolgál. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és megadja a válaszfejlécekben. |
| engedélyezés      | Igen          | A JSON webes jogkivonat (JWT) tulajdonosi token.                                                                    |
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
| id                     | Sztring        | Az Azure-beli SaaS-előfizetési erőforrás azonosítója.    |
| offerId                | Sztring        | Ajánlatazonosító, amelyre a felhasználó előfizetett.         |
| planId között                 | Sztring        | A csomag azonosítója, amelyre a felhasználó előfizetett.          |
| saasSubscriptionName   | Sztring        | A SaaS-előfizetés neve.                |
| saasSubscriptionStatus | Felsorolás          | Műveleti állapot.  Az alábbiak valamelyikének telepítve kell lennie:  <br/> - `Subscribed`: Az előfizetés aktív.  <br/> - `Pending`: A felhasználó hozza létre az erőforrást, de azt az isv nem aktiválja.   <br/> - `Unsubscribed`: A felhasználó leiratkozott.   <br/> - `Suspended`: A felhasználó felfüggesztette az előfizetést.   <br/> - `Deactivated`: Az Azure-előfizetés fel van függesztve.  |
| Létrehozott                | DateTime      | Előfizetés-létrehozási időbélyeg értéke utc-ben. |
| lastModified (utolsó módosítás: )           | DateTime      | Előfizetés módosított időbélyeg értéke UTC-ben. |
|  |  |  |

*Válaszkódok*

| **HTTP-állapotkód** | **Hibakód**     | **Leírás**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | A lekérés sikeresen megoldódott, és a törzs tartalmazza a választ.    |
| 400                  | `BadRequest`         | Vagy hiányoznak a szükséges fejlécek, vagy érvénytelen API-verzió van megadva. |
| 403                  | `Forbidden`          | A hívó nem jogosult a művelet végrehajtására.                      |
| 404                  | `NotFound`           | Az előfizetés nem található a megadott azonosítóval                                     |
| 429                  | `RequestThrottleId`  | A szolgáltatás a kérelmek feldolgozásával van elfoglalva, majd próbálkozzon újra.                     |
| 503                  | `ServiceUnavailable` | A szolgáltatás átmenetileg leállt, később próbálkozzon újra.                             |
|  |  |  |

*Válaszfejlécek*

| **Fejléckulcs**     | **Szükséges** | **Leírás**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Igen          | Az ügyféltől kapott kérelemazonosító.                                                                   |
| x-ms-correlationid | Igen          | Korrelációs azonosító, ha az ügyfél átadta, ellenkező esetben ez a kiszolgáló korrelációs azonosítója.                   |
| x-ms-activityid    | Igen          | A szolgáltatástól érkező kérelem nyomon követéséhez egyedi karakterlánc-érték. Ez minden egyeztetéshez használatos. |
| Újrapróbálkozás utána        | Nem           | Az az időköz, amellyel az ügyfél ellenőrizheti az állapotot.                                                       |
| Etag               | Igen          | Hivatkozás egy erőforrásra a művelet állapotának leválasztásához.                                                        |
|  |  |  |

### <a name="get-subscriptions"></a>Előfizetések beszereznie

A Get művelet előfizetések végpont lehetővé teszi a felhasználó számára, hogy lekérje az összes előfizetést az isv-ből az összes ajánlathoz.

*Kérés*

**Kap**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2017-04-15**

| **Paraméter neve**  | **Leírás**                                       |
|---------------------|-------------------------------------------------------|
| api-verzió         | A kérelemhez használandó művelet verziója. |
|  |  |

*Fejlécek*

| **Fejléckulcs**     | **Szükséges** | **Leírás**                                           |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Nem           | Egyedi karakterlánc-érték az ügyféltől érkező kérelem nyomon követéséhez. Javasoljuk a GUID. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és megadja a válaszfejlécekben.             |
| x-ms-correlationid | Nem           | Az ügyfélen végzett művelet egyedi karakterlánc-értéke. Ez az érték az ügyfélművelet összes eseményének a kiszolgálóoldali eseményekkel való korrelációjára szolgál. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és megadja a válaszfejlécekben. |
| engedélyezés      | Igen          | A JSON webes jogkivonat (JWT) tulajdonosi token.                    |
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
| offerId                | Sztring        | Ajánlatazonosító, amelyre a felhasználó előfizetett         |
| planId között                 | Sztring        | A csomag azonosítója, amelyre a felhasználó előfizetett          |
| saasSubscriptionName   | Sztring        | A SaaS-előfizetés neve                |
| saasSubscriptionStatus | Felsorolás          | Műveleti állapot.  Az alábbiak valamelyikének telepítve kell lennie:  <br/> - `Subscribed`: Az előfizetés aktív.  <br/> - `Pending`: A felhasználó hozza létre az erőforrást, de azt az isv nem aktiválja.   <br/> - `Unsubscribed`: A felhasználó leiratkozott.   <br/> - `Suspended`: A felhasználó felfüggesztette az előfizetést.   <br/> - `Deactivated`: Az Azure-előfizetés fel van függesztve.  |
| Létrehozott                | DateTime      | Előfizetés-létrehozási időbélyeg értéke az UTC-ben |
| lastModified (utolsó módosítás: )           | DateTime      | Előfizetés módosított időbélyegértéke UTC-ben |
|  |  |  |

*Válaszkódok*

| **HTTP-állapotkód** | **Hibakód**     | **Leírás**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | A lekérés sikeresen megoldódott, és a törzs tartalmazza a választ.    |
| 400                  | `BadRequest`         | Vagy hiányoznak a szükséges fejlécek, vagy érvénytelen API-verzió van megadva. |
| 403                  | `Forbidden`          | A hívó nem jogosult a művelet végrehajtására.                      |
| 404                  | `NotFound`           | Az előfizetés nem található a megadott azonosítóval                                     |
| 429                  | `RequestThrottleId`  | A szolgáltatás foglalt a kérelmek feldolgozásával, próbálkozzon később.                     |
| 503                  | `ServiceUnavailable` | A szolgáltatás átmenetileg leállt. Próbálkozzon később.                             |
|  |  |  |

*Válaszfejlécek*

| **Fejléckulcs**     | **Szükséges** | **Leírás**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Igen          | Az ügyféltől kapott kérelemazonosító.                                                                   |
| x-ms-correlationid | Igen          | Korrelációs azonosító, ha az ügyfél átadta, ellenkező esetben ez a kiszolgáló korrelációs azonosítója.                   |
| x-ms-activityid    | Igen          | A szolgáltatástól érkező kérelem nyomon követéséhez egyedi karakterlánc-érték. Ez minden egyeztetéshez használatos. |
| Újrapróbálkozás utána        | Nem           | Az az időköz, amellyel az ügyfél ellenőrizheti az állapotot.                                                       |
|  |  |  |

### <a name="saas-webhook"></a>SaaS Webhook

A SaaS webhook a SaaS-szolgáltatás proaktív módosításai bejelentésére szolgál. A POST API-t várhatóan nem hitelesíti, és a Microsoft szolgáltatás fogja meghívni. A SaaS-szolgáltatás várhatóan meghívja az operations API-t, hogy ellenőrizze és engedélyezze a webhook-értesítésművelet elvégzése előtt. 

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
| activityId   | Sztring        | A szolgáltatástól érkező kérelem nyomon követéséhez egyedi karakterlánc-érték. Ez minden egyeztetéshez használatos.               |
| subscriptionId                     | Sztring        | Az Azure-beli SaaS-előfizetési erőforrás azonosítója.    |
| offerId                | Sztring        | Ajánlatazonosító, amelyre a felhasználó előfizetett. Csak a "Frissítés" művelettel van ellátva.        |
| publisherId                | Sztring        | A SaaS-ajánlat közzétevői azonosítója         |
| planId között                 | Sztring        | A csomag azonosítója, amelyre a felhasználó előfizetett. Csak a "Frissítés" művelettel van ellátva.          |
| action                 | Sztring        | Az értesítést kiváltó művelet. Lehetséges értékek - Aktiválás, Törlés, Felfüggesztés, Visszaállítás, Frissítés          |
| Időbélyeg                 | Sztring        | TImestamp érték UTC-ben, amikor ez az értesítés aktiválódott.          |
|  |  |  |


## <a name="next-steps"></a>További lépések

A fejlesztők a [Cloud Partner Portal REST API-jaival](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)programozott módon is lekérhetik és módosíthatják a számítási feladatokat, ajánlatokat és közzétevői profilokat.
