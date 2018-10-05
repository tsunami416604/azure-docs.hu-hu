---
title: Azure - API-k segítségével SaaS értékesítési |} A Microsoft Docs
description: Azt ismerteti, hogyan hozhat létre egy SaaS-ajánlat piactérről API-k.
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
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: 0368d9822df193fbf00d8a2069108e23100a58cd
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810593"
---
<a name="saas-sell-through-azure---apis"></a>SaaS-értékesítési Azure - API-k segítségével
==============================

Ez a cikk bemutatja, hogyan hozhat létre egy SaaS-ajánlatok API-kkal. Ha rendelkezik értékesítési kiválasztott Azure-on keresztül, amely lehetővé teszi, hogy az SaaS-ajánlat előfizetéseinek az API-k szükségesek. Ha szeretne létrehozni, amelyen nincs engedélyezve a kereskedelmi rendszeres SaaS listaelem, tekintse meg a [SaaS alkalmazás műszaki közzétételi Guide]./cloud-partner-portal-saas-offers-tech-publishing-guide.md).

Ez a cikk két részből áll:

-   Szolgáltatások közötti hitelesítés egy SaaS-szolgáltatás és az Azure Marketplace-en között
-   API-metódusai és végpontok

A következő API-kat a SaaS-szolgáltatás integrációját az Azure-ral áll rendelkezésre:

-   Feloldás
-   Feliratkozás
-   Konvertálás
-   Leiratkozás

Az alábbi ábrán látható, az előfizetés folyamatot egy új ügyfél, és ezen API-k használatakor:

![SaaS API-folyamat ajánlat](media/saas-offer-publish-with-subscription-apis/saas-offer-publish-api-flow.png)

<a name="service-to-service-authentication-between-saas-service-and-azure-marketplace"></a>SaaS-szolgáltatás és az Azure piactér szolgáltatás hitelesítési szolgáltatás
----------------------------------------------------------------------------

Azure nincsenek megkötések a hitelesítést, amely a SaaS-szolgáltatás elérhetővé teszi a végfelhasználók számára. Azonban esetén, az Azure Marketplace-en API-kkal való kommunikációhoz SaaS-szolgáltatás, a hitelesítés történik, az Azure Active Directory (Azure AD-) alkalmazás környezetében.

Az alábbi szakasz létrehozása az Azure AD-alkalmazást.

### <a name="register-an-azure-ad-application"></a>Az Azure AD alkalmazás regisztrálása

Az Azure AD képességeit felhasználó alkalmazásokat először regisztrálni kell egy Azure AD-bérlőben. A regisztrálási folyamat magában foglalja a jogosultságot ad az alkalmazások, például az URL-címet, hogy hol található, a válaszok elküldésére, a felhasználó hitelesítése után az URL-cím az Azure AD részleteit az URI-t, amely azonosítja az alkalmazást, és így tovább.

Az Azure Portallal egy új alkalmazás regisztrálásához hajtsa végre az alábbi lépéseket:

1.  Jelentkezzen be az [Azure Portal](https://portal.azure.com/).
2.  Ha a fiókja több bérlőhöz is biztosít hozzáférést, kattintson a fiókra az oldal jobb felső sarkában, és állítsa a portál munkamenetét a kívánt Azure AD-bérlőre.
3.  A bal oldali navigációs ablaktáblán kattintson a **Azure Active Directory** szolgáltatást, kattintson a **alkalmazásregisztrációk**, és kattintson a **új alkalmazásregisztráció**.

    ![SaaS-AD Alkalmazásregisztrációk](media/saas-offer-publish-with-subscription-apis/saas-offer-app-registration.png)

4.  A Létrehozás lapon adja meg az alkalmazás\'s regisztrációs adatokat:
    -   **Név**: Adjon meg egy kifejező alkalmazásnevet
    -   **Az alkalmazástípus**: válassza ki **webalkalmazás / API** a [ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) és [erőforrás és az API-alkalmazások](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) biztonságos kiszolgálón telepített. Ezzel a beállítással az OAuth bizalmas [webes ügyfél](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) és nyilvános [felhasználói ügynök-alapú ügyfelek](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client).
        Egyazon alkalmazás az ügyfelet és az erőforrást/API-t is elérhetővé teheti.
    -   **Bejelentkezési URL-**: A webes alkalmazás és az API-alkalmazások, adja meg az alkalmazás alap URL-CÍMÉT. Ha például **http://localhost:31544** lehet, hogy a helyi gépen futó webalkalmazás URL-CÍMÉT. Felhasználók a webes ügyfélalkalmazás bejelentkezni majd használna az URL-címet.
    -   **Átirányítási URI**: A natív alkalmazások esetén adja meg az Azure AD a jogkivonatválaszok visszaadására használt URI-ja. Adja meg például egy adott értéket az alkalmazás **http://MyFirstAADApp**.

        Adott példák a webalkalmazásokra vagy natív alkalmazásokat, tekintse meg a gyors üzembe helyezési interaktív bevezetés szakaszában rendelkezésre álló beállításokat a [az Azure AD fejlesztői útmutató](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide#get-started).

5.  Ha végzett, kattintson a **Létrehozás** gombra. Az Azure AD egy egyedi Alkalmazásazonosító rendeli az alkalmazást, és\'újra megnyílik az alkalmazás\'s fő való regisztrációhoz. Attól függően, hogy az alkalmazás webes vagy natív, eltérő lehetőségek állnak rendelkezésre az alkalmazás további funkcióinak hozzáadásához.

    **Megjegyzés:** alapértelmezés szerint az újonnan regisztrált alkalmazás van konfigurálva, hogy csak a felhasználók az ugyanazon bérlőről, jelentkezzen be az alkalmazást.

<a name="api-methods-and-endpoints"></a>API-metódusai és végpontok
-------------------------

A következő szakaszok ismertetik az API-metódusai és a egy SaaS-ajánlat előfizetéseinek engedélyezése végpontok.

### <a name="get-a-token-based-on-the-azure-ad-app"></a>Az Azure AD-alkalmazás alapján egy token beszerzése

HTTP-metódus

`GET`

*Kérés URL-címe*

**https://login.microsoftonline.com/*{Bérlőazonosító}*  /oauth2/jogkivonat**

*URI-paraméter*

|  **Paraméter neve**  | **Szükséges**  | **Leírás**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| tenantId             | True (Igaz)          | A regisztrált AAD-alkalmazás Bérlőazonosítója   |
|  |  |  |


*Kérelem fejléce*

|  **Fejléc neve**  | **Szükséges** |  **Leírás**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Content-Type     | True (Igaz)         | A kéréshez társított tartalom típusa. Az alapértelmezett érték `application/x-www-form-urlencoded`.  |
|  |  |  |


*Kérelem törzse*

| **Tulajdonság neve**   | **Szükséges** |  **Leírás**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Megadástípus (grant_type)         | True (Igaz)         | Engedélyezési típus. Az alapértelmezett érték `client_credentials`.                    |
|  Client_id          | True (Igaz)         |  Az Azure AD-alkalmazáshoz társított ügyfél/alkalmazás azonosítója.                  |
|  client_secret      | True (Igaz)         |  Az Azure AD-alkalmazáshoz társított jelszót.                               |
|  Erőforrás           | True (Igaz)         |  A célerőforrás, amelyre a jogkivonat kérik. Az alapértelmezett érték `b3cca048-ed2e-406c-aff2-40cf19fe7bf5`. |
|  |  |  |


*Válasz*

|  **Name (Név)**  | **Típus**       |  **Leírás**    |
| ---------- | -------------  | ------------------- |
| 200 OK /    | TokenResponse  | A kérelem sikeres volt   |
|  |  |  |

*TokenResponse*

Minta válasz token:

``` json
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "b3cca048-ed2e-406c-aff2-40cf19fe7bf5",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }               
```

### <a name="marketplace-api-endpoint-and-api-version"></a>Piactér API végpontot és API-verzió

Az Azure Marketplace-en API-végpont `https://marketplaceapi.microsoft.com`.

Az API-verzió `api-version=2017-04-15`.


### <a name="resolve-subscription"></a>Oldja meg az előfizetés

A POST művelet oldja meg a végpont lehetővé teszi a felhasználóknak oldja fel a jogkivonatot egy állandó erőforrás-azonosítója.

*Kérés*

**POST**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=2017-04-15**

|  **Paraméter neve** |     **Leírás**                                      |
|  ------------------ |     ---------------------------------------------------- |
|  API-verzió        |  A művelet ehhez a kérelemhez használt verziója.   |
|  |  |


*Fejlécek*

| **Fejléc kulcs**     | **Szükséges** | **Leírás**                                                                                                                                                                                                                  |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-RequestId:     | Nem           | A kérelem követési az ügyfélről, lehetőleg a GUID egyedi karakterlánc-érték. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.  |
| x-ms-korrelációs azonosító | Nem           | A műveletet az ügyfél egyedi karakterlánc-érték. Ez utal. a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott. |
| a Content-type       | Igen          | `application/json`                                        |
| Engedélyezési      | Igen          | A JSON webes jogkivonat (JWT) tulajdonosi jogkivonatot.                    |
|  |  |  |
  

*Választörzs*

 ``` json       
    { 
        “id”: “”, 
        “offerId”:””, 
         “planId”:””, 
    }     
```

| **Paraméter neve** | **Adattípus** | **Leírás**                       |
|--------------------|---------------|---------------------------------------|
| id                 | Sztring        | Az SaaS-előfizetés azonosítója.          |
| Ajánlat azonosítója            | Sztring        | Ajánlat azonosítója, amelyre a felhasználó előfizetett. |
| planId             | Sztring        | Tervezze meg, hogy a felhasználó előfizetett azonosítója.  |
|  |  |  |


*Válaszkódok*

| **HTTP-állapotkód** | **Hibakód:**     | **Leírás**                                                                         |
|----------------------|--------------------| --------------------------------------------------------------------------------------- |
| 200                  | `OK`                 | A jogkivonat sikeresen feloldva.                                                            |
| 400                  | `BadRequest`         | Vagy kötelező fejléc hiányzik, vagy a megadott api-verziója érvénytelen. Nem sikerült feloldani a jogkivonatot, mert helytelen formátumúak vagy lejártak vagy a jogkivonatot. |
| 403                  | `Forbidden`          | A hívónak nincs engedélye e művelet elvégzéséhez.                                 |
| 429                  | `RequestThrottleId`  | Szolgáltatás egy foglalt feldolgozási kérelmek, próbálkozzon újra később.                                |
| 503                  | `ServiceUnavailable` | Szolgáltatás van lefelé átmenetileg, próbálkozzon újra később.                                        |
|  |  |  |


*Válaszfejlécek*

| **Fejléc kulcs**     | **Szükséges** | **Leírás**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-RequestId:     | Igen          | Kérés azonosítója, az ügyféltől kapott.                                                                   |
| x-ms-korrelációs azonosító | Igen          | Korrelációs Azonosítót, ha az ügyfél által átadott ellenkező esetben ezt az értéket, a korrelációs azonosítóját.                   |
| x-ms-tevékenységazonosító    | Igen          | A szolgáltatás a kérelem követési egyedi karakterlánc-érték. Bármely egyeztetések szolgál. |
| Retry-After        | Nem           | Ez az érték csak egy 429 válaszra van beállítva.                                                                   |
|  |  |  |


### <a name="subscribe"></a>Feliratkozás

Az előfizetési végpont lehetővé teszi, hogy a felhasználók számára egy egy SaaS-szolgáltatás egy adott csomag előfizetésének indítása és engedélyezése a számlázás a kereskedelmi rendszerben.

**PUT**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}* ? api-version = 2017-04-15**

| **Paraméter neve**  | **Leírás**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID az SaaS-előfizetés.                              |
| API-verzió         | A művelet ehhez a kérelemhez használt verziója. |
|  |  |

*Fejlécek*

|  **Fejléc kulcs**        | **Szükséges** |  **Leírás**                                                  |
| ------------------     | ------------ | --------------------------------------------------------------------------------------- |
| x-ms-RequestId:         |   Nem         | A kérelem követési az ügyfélről, lehetőleg a GUID egyedi karakterlánc-érték. Ha nincs megadva, az egyik létrehozott és a válaszfejlécek megadott. |
| x-ms-korrelációs azonosító     |   Nem         | A műveletet az ügyfél egyedi karakterlánc-érték. Ez az érték használatával történik a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye van. Ha nincs megadva, az egyik létrehozott és a válaszfejlécek megadott. |
| If-Match/If-None-Match |   Nem         |   Erős érvényesítő ETag-érték.                                                          |
| a Content-type           |   Igen        |    `application/json`                                                                   |
|  Engedélyezési         |   Igen        |    A JSON webes jogkivonat (JWT) tulajdonosi jogkivonatot.                                               |
|  |  |  |

*Törzs*

``` json
  { 
      “planId”:””, 
   }      
```

| **Elem neve** | **Adattípus** | **Leírás**                      |
|------------------|---------------|--------------------------------------|
| planId           | Sztring        | Tervezze meg, hogy a felhasználó előfizetett azonosítója. |
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

| **Fejléc kulcs**     | **Szükséges** | **Leírás**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-RequestId:     | Igen          | Kérés azonosítója, az ügyféltől kapott.                                                                   |
| x-ms-korrelációs azonosító | Igen          | Korrelációs Azonosítót, ha az ügyfél által átadott ellenkező esetben ezt az értéket, a korrelációs azonosítóját.                   |
| x-ms-tevékenységazonosító    | Igen          | A szolgáltatás a kérelem követési egyedi karakterlánc-érték. Ez az érték minden egyeztetések szolgál. |
| Retry-After        | Igen          | Melyik ügyfél időköz ellenőrizheti.                                                       |
| A művelet-helye | Igen          | Hivatkozás az erőforrás műveleti állapotának beolvasása.                                                        |
|  |  |  |

### <a name="change-plan-endpoint"></a>Módosítás terv végpont

A módosítás végpont lehetővé teszi, hogy a felhasználó az aktuálisan előfizetett csomag átalakítása egy új tervet.

**JAVÍTÁS**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}* ? api-version = 2017-04-15**

| **Paraméter neve**  | **Leírás**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID az SaaS-előfizetés.                              |
| API-verzió         | A művelet ehhez a kérelemhez használt verziója. |
|  |  |

*Fejlécek*

| **Fejléc kulcs**          | **Szükséges** | **Leírás**                                                                                                                                                                                                                  |
|-------------------------|--------------|---------------------------------------------------------------------------------------------------------------------|
| x-ms-RequestId:          | Nem           | Az ügyfél a kérés követési egyedi karakterlánc-érték. Javasoljuk, egy GUID Azonosítót. Ha nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.   |
| x-ms-korrelációs azonosító      | Nem           | A műveletet az ügyfél egyedi karakterlánc-érték. Ez az érték használatával történik a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye van. Ha nincs megadva, az egyik létrehozott és a válaszfejlécek megadott. |
| If-Match /If-None-Match | Nem           | Erős érvényesítő ETag-érték.                              |
| a Content-type            | Igen          | `application/json`                                        |
| Engedélyezési           | Igen          | A JSON webes jogkivonat (JWT) tulajdonosi jogkivonatot.                    |
|  |  |  |


*Törzs*

``` json
                { 
                    “planId”:””, 
                } 
```


|  **Elem neve** |  **Adattípus**  | **Leírás**                              |
|  ---------------- | -------------   | --------------------------------------       |
|  planId           |  Sztring         | Tervezze meg, hogy a felhasználó előfizetett azonosítója.         |
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

| **Fejléc kulcs**     | **Szükséges** | **Leírás**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-RequestId:     | Igen          | Kérés azonosítója, az ügyféltől kapott.                                                                   |
| x-ms-korrelációs azonosító | Igen          | Korrelációs Azonosítót, ha az ügyfél által átadott ellenkező esetben ezt az értéket, a korrelációs azonosítóját.                   |
| x-ms-tevékenységazonosító    | Igen          | A szolgáltatás a kérelem követési egyedi karakterlánc-érték. Ez az érték minden egyeztetések szolgál. |
| Retry-After        | Igen          | Melyik ügyfél időköz ellenőrizheti.                                                       |
| A művelet-helye | Igen          | Hivatkozás az erőforrás műveleti állapotának beolvasása.                                                        |
|  |  |  |

### <a name="delete-subscription"></a>Előfizetés törlése

Az előfizetési végpont a törlési művelet lehetővé teszi, hogy a felhasználó töröl egy előfizetést a megadott azonosítót.

*Kérés*

**DELETE**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}* ? api-version = 2017-04-15**

| **Paraméter neve**  | **Leírás**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID az SaaS-előfizetés.                              |
| API-verzió         | A művelet ehhez a kérelemhez használt verziója. |
|  |  |

*Fejlécek*

| **Fejléc kulcs**     | **Szükséges** | **Leírás**                                                                                                                                                                                                                  |
|--------------------|--------------| ----------------------------------------------------------|
| x-ms-RequestId:     | Nem           | Az ügyfél a kérés követési egyedi karakterlánc-érték. Javasoljuk, egy GUID Azonosítót. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.                                                           |
| x-ms-korrelációs azonosító | Nem           | A műveletet az ügyfél egyedi karakterlánc-érték. Ez az érték használatával történik a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye van. Ha nincs megadva, az egyik létrehozott és a válaszfejlécek megadott. |
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

*Válaszfejlécek*

| **Fejléc kulcs**     | **Szükséges** | **Leírás**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-RequestId:     | Igen          | Kérés azonosítója, az ügyféltől kapott.                                                                   |
| x-ms-korrelációs azonosító | Igen          | Korrelációs azonosító által átadott az ügyfelet, egyébként ez-e a korrelációs azonosítóját.                   |
| x-ms-tevékenységazonosító    | Igen          | A szolgáltatás a kérelem követési egyedi karakterlánc-érték. Bármely egyeztetések szolgál. |
| Retry-After        | Igen          | Melyik ügyfél időköz ellenőrizheti.                                                       |
| A művelet-helye | Igen          | Hivatkozás az erőforrás műveleti állapotának beolvasása.                                                        |
|   |  |  |

### <a name="get-operation-status"></a>Műveleti állapotának beolvasása

Ez a végpont lehetővé teszi a felhasználó egy aktivált aszinkron művelet (az előfizetés/Unsubscribe/csomag módosítása) állapotának nyomon követését.

*Kérés*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/operations/*{operationId}* ? api-version = 2017-04-15**

| **Paraméter neve**  | **Leírás**                                       |
|---------------------|-------------------------------------------------------|
| operationId         | A művelet által aktivált egyedi azonosítója.                |
| API-verzió         | A művelet ehhez a kérelemhez használt verziója. |
|  |  |


*Fejlécek*

| **Fejléc kulcs**     | **Szükséges** | **Leírás**                                                                                                                                                                                                                  |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------------------------|
| x-ms-RequestId:     | Nem           | Az ügyfél a kérés követési egyedi karakterlánc-érték. Javasoljuk, egy GUID Azonosítót. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.   |
| x-ms-korrelációs azonosító | Nem           | A műveletet az ügyfél egyedi karakterlánc-érték. Ez az érték használatával történik a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye van. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.  |
| Engedélyezési      | Igen          | A JSON webes jogkivonat (JWT) tulajdonosi jogkivonatot.                    |
|  |  |  | 
  

*Választörzs*

``` json
  { 
      “id”: “”, 
      “status”:””, 
       “resourceLocation”:””, 
      “created”:””, 
      “lastModified”:”” 
  } 
```

| **Paraméter neve** | **Adattípus** | **Leírás**                                                                                                                                               |
|--------------------|---------------|-------------------------------------------------------------------------------------------|
| id                 | Sztring        | A művelet azonosítója.                                                                      |
| status             | Enum          | A művelet állapotát, a következők egyikét: `In Progress`, `Succeeded`, vagy `Failed`.          |
| resourceLocation   | Sztring        | Az előfizetést, amelyhez a létrehozott vagy módosított mutató hivatkozás. Ez segít az ügyfél megszerezni a frissítve állapotot post műveletet. Ez az érték nincs beállítva `Unsubscribe` műveleteket. |
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

| **Fejléc kulcs**     | **Szükséges** | **Leírás**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-RequestId:     | Igen          | Kérés azonosítója, az ügyféltől kapott.                                                                   |
| x-ms-korrelációs azonosító | Igen          | Korrelációs azonosító által átadott az ügyfelet, egyébként ez-e a korrelációs azonosítóját.                   |
| x-ms-tevékenységazonosító    | Igen          | A szolgáltatás a kérelem követési egyedi karakterlánc-érték. Bármely egyeztetések szolgál. |
| Retry-After        | Igen          | Melyik ügyfél időköz ellenőrizheti.                                                       |
| A művelet-helye | Igen          | Hivatkozás az erőforrás műveleti állapotának beolvasása.                                                        |
|  |  |  |

### <a name="get-subscription"></a>Előfizetés beszerzése

A Get műveletet az előfizetési végpont lehetővé teszi, hogy egy felhasználó egy adott erőforrás-azonosítójú előfizetés lekéréséhez.

*Kérés*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}* ? api-version = 2017-04-15**

| **Paraméter neve**  | **Leírás**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID az SaaS-előfizetés.                              |
| API-verzió         | A művelet ehhez a kérelemhez használt verziója. |
|  |  |

*Fejlécek*

| **Fejléc kulcs**     | **Szükséges** | **Leírás**                                                                                           |
|--------------------|--------------|-----------------------------------------------------------------------------------------------------------|
| x-ms-RequestId:     | Nem           | A kérelem követési az ügyfélről, lehetőleg a GUID egyedi karakterlánc-érték. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.                                                           |
| x-ms-korrelációs azonosító | Nem           | A műveletet az ügyfél egyedi karakterlánc-érték. Ez az érték használatával történik a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye van. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott. |
| Engedélyezési      | Igen          | A JSON webes jogkivonat (JWT) tulajdonosi jogkivonatot.                                                                    |
|  |  |  |

*Választörzs*

``` json
  { 
      “id”: “”, 
      “saasSubscriptionName”:””, 
      “offerId”:””, 
       “planId”:””, 
      “saasSubscriptionStatus”:”” 
      “created”:”” 
      “lastModified”: “”, 
  }
```
| **Paraméter neve**     | **Adattípus** | **Leírás**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | Sztring        | Előfizetés erőforrás azonosítója, SaaS az Azure-ban.    |
| OfferId                | Sztring        | Ajánlat azonosítója, amelyre a felhasználó előfizetett.         |
| planId                 | Sztring        | Tervezze meg, hogy a felhasználó előfizetett azonosítója.          |
| saasSubscriptionName   | Sztring        | Az SaaS-előfizetés neve.                |
| saasSubscriptionStatus | Enum          | A művelet állapotát.  A következők egyikét:  <br/> - `Subscribed`: Előfizetés már aktív.  <br/> - `Pending`: A felhasználó létrehozása az erőforrás, de nem aktiválódik, a független szoftvergyártó.   <br/> - `Unsubscribed`: A felhasználó leiratkozott rendelkezik.   <br/> - `Suspended`: A felhasználó felfüggesztette az előfizetést.   <br/> - `Deactivated`: Az azure-előfizetés fel van függesztve.  |
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

| **Fejléc kulcs**     | **Szükséges** | **Leírás**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-RequestId:     | Igen          | Kérés azonosítója, az ügyféltől kapott.                                                                   |
| x-ms-korrelációs azonosító | Igen          | Korrelációs azonosító által átadott az ügyfelet, egyébként ez-e a korrelációs azonosítóját.                   |
| x-ms-tevékenységazonosító    | Igen          | A szolgáltatás a kérelem követési egyedi karakterlánc-érték. Bármely egyeztetések szolgál. |
| Retry-After        | Nem           | Melyik ügyfél időköz ellenőrizheti.                                                       |
| az eTag               | Igen          | Hivatkozás az erőforrás műveleti állapotának beolvasása.                                                        |
|  |  |  |


### <a name="get-subscriptions"></a>Előfizetések beolvasása

A Get műveletet előfizetések végponton lehetővé teszi, hogy egy felhasználó összes előfizetés összes ajánlatok lekérése az ISV-k.

*Kérés*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/subscriptions?api-version=2017-04-15**

| **Paraméter neve**  | **Leírás**                                       |
|---------------------|-------------------------------------------------------|
| API-verzió         | A művelet ehhez a kérelemhez használt verziója. |
|  |  |

*Fejlécek*

| **Fejléc kulcs**     | **Szükséges** | **Leírás**                                           |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-RequestId:     | Nem           | Az ügyfél a kérés követési egyedi karakterlánc-érték. Javasoljuk, egy GUID Azonosítót. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott.             |
| x-ms-korrelációs azonosító | Nem           | A műveletet az ügyfél egyedi karakterlánc-érték. Ez az érték használatával történik a kiszolgálói oldalon eseményekkel rendelkező ügyfél művelet összes eseménye van. Ha ez az érték nincs megadva, az egyik létrehozott és a válaszfejlécek megadott. |
| Engedélyezési      | Igen          | A JSON webes jogkivonat (JWT) tulajdonosi jogkivonatot.                    |
|  |  |  |


*Választörzs*

``` json
  { 
      “id”: “”, 
      “saasSubscriptionName”:””, 
      “offerId”:””, 
       “planId”:””, 
      “saasSubscriptionStatus”:”” 
      “created”:”” 
      “lastModified”: “”, 
  }
```

| **Paraméter neve**     | **Adattípus** | **Leírás**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | Sztring        | Előfizetés erőforrás azonosítója, SaaS az Azure-ban.    |
| OfferId                | Sztring        | Ajánlat azonosítója, amelyre a felhasználó előfizetett.         |
| planId                 | Sztring        | Tervezze meg, hogy a felhasználó előfizetett azonosítója.          |
| saasSubscriptionName   | Sztring        | Az SaaS-előfizetés neve.                |
| saasSubscriptionStatus | Enum          | A művelet állapotát.  A következők egyikét:  <br/> - `Subscribed`: Előfizetés már aktív.  <br/> - `Pending`: A felhasználó létrehozása az erőforrás, de nem aktiválódik, a független szoftvergyártó.   <br/> - `Unsubscribed`: A felhasználó leiratkozott rendelkezik.   <br/> - `Suspended`: A felhasználó felfüggesztette az előfizetést.   <br/> - `Deactivated`: Az azure-előfizetés fel van függesztve.  |
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

| **Fejléc kulcs**     | **Szükséges** | **Leírás**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-RequestId:     | Igen          | Kérés azonosítója, az ügyféltől kapott.                                                                   |
| x-ms-korrelációs azonosító | Igen          | Korrelációs azonosító által átadott az ügyfelet, egyébként ez-e a korrelációs azonosítóját.                   |
| x-ms-tevékenységazonosító    | Igen          | A szolgáltatás a kérelem követési egyedi karakterlánc-érték. Bármely egyeztetések szolgál. |
| Retry-After        | Nem           | Melyik ügyfél időköz ellenőrizheti.                                                       |
|  |  |  |
