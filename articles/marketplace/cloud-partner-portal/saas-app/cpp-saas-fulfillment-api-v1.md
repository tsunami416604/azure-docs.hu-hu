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
ms.date: 02/27/2019
ms.author: pbutlerm
ms.openlocfilehash: 299c06fc043391fc9b765c95ec6d29da3d440719
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57318872"
---
# <a name="saas-fulfillment-apis-version-1"></a>SaaS teljesítése API-k 1-es verzió

Ez a cikk bemutatja, hogyan hozhat létre egy SaaS-ajánlatok API-kkal. Ha rendelkezik értékesítési kiválasztott Azure-on keresztül, amely lehetővé teszi, hogy az SaaS-ajánlat előfizetéseinek az API-k szükségesek.  

> [!WARNING]
> A kezdeti SaaS teljesítése API-verzió elavult; Ehelyett használjon [SaaS teljesítése API V2](./cpp-saas-fulfillment-api-v2.md).


Ez a cikk két részből áll:

-   Szolgáltatások közötti hitelesítés egy SaaS-szolgáltatás és az Azure Marketplace-en között
-   API-metódusai és végpontok

A következő API-kat a SaaS-szolgáltatás integrációját az Azure-ral áll rendelkezésre:

-   Feloldás
-   Feliratkozás
-   Konvertálás
-   Leiratkozás

Az alábbi ábrán látható, az előfizetés folyamatot egy új ügyfél, és ezen API-k használatakor:

![SaaS API-folyamat ajánlat](./media/saas-offer-publish-api-flow-v1.png)


## <a name="service-to-service-authentication-between-saas-service-and-azure-marketplace"></a>SaaS-szolgáltatás és az Azure piactér szolgáltatás hitelesítési szolgáltatás

Azure nincsenek megkötések a hitelesítést, amely a SaaS-szolgáltatás elérhetővé teszi a végfelhasználók számára. Azonban esetén, az Azure Marketplace-en API-kkal való kommunikációhoz SaaS-szolgáltatás, a hitelesítés történik, az Azure Active Directory (Azure AD-) alkalmazás környezetében.

Az alábbi szakasz létrehozása az Azure AD-alkalmazást.


### <a name="register-an-azure-ad-application"></a>Az Azure AD alkalmazás regisztrálása

Az Azure AD képességeit felhasználó alkalmazásokat először regisztrálni kell egy Azure AD-bérlőben. A regisztrálási folyamat magában foglalja a jogosultságot ad az alkalmazások, például az URL-címet, hogy hol található, a válaszok elküldésére, a felhasználó hitelesítése után az URL-cím az Azure AD részleteit az URI-t, amely azonosítja az alkalmazást, és így tovább.

Az Azure Portallal egy új alkalmazás regisztrálásához hajtsa végre az alábbi lépéseket:

1.  Jelentkezzen be az [Azure Portal](https://portal.azure.com/).
2.  Ha a fiókja több bérlőhöz is biztosít hozzáférést, kattintson a fiókra az oldal jobb felső sarkában, és állítsa a portál munkamenetét a kívánt Azure AD-bérlőre.
3.  A bal oldali navigációs ablaktáblán kattintson a **Azure Active Directory** szolgáltatást, kattintson a **alkalmazásregisztrációk**, és kattintson a **új alkalmazásregisztráció**.

    ![SaaS-AD Alkalmazásregisztrációk](./media/saas-offer-app-registration-v1.png)

4.  A Létrehozás lapon adja meg az alkalmazás\'s regisztrációs adatokat:
    -   **Név**: Adjon meg egy kifejező alkalmazásnevet
    -   **Az alkalmazástípus**: 
        - Válassza a **Natív** lehetőséget a helyileg vagy eszközre telepített [ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) esetében. Ez a beállítás használatos a nyilvános OAuth [natív ügyfelekhez](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client).
        - Válassza ki **webalkalmazás / API** a [ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) és [erőforrás és az API-alkalmazások](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) biztonságos kiszolgálón telepített. Ezzel a beállítással az OAuth bizalmas [webes ügyfél](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) és nyilvános [felhasználói ügynök-alapú ügyfelek](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client).
        Egyazon alkalmazás az ügyfelet és az erőforrást/API-t is elérhetővé teheti.
    -   **Bejelentkezés URL-cím**: Webes alkalmazás és az API-alkalmazások adja meg az alkalmazás alap URL-CÍMÉT. Ha például **http://localhost:31544** lehet, hogy a helyi gépen futó webalkalmazás URL-CÍMÉT. Felhasználók a webes ügyfélalkalmazás bejelentkezni majd használna az URL-címet.
    -   **Átirányítási URI**: Natív alkalmazások esetén adja meg az Azure AD a jogkivonatválaszok visszaadására használt URI-ja. Adja meg például egy adott értéket az alkalmazás **http://MyFirstAADApp**.

        ![SaaS-AD Alkalmazásregisztrációk](./media/saas-offer-app-registration-v1-2.png)

        Adott példák a webalkalmazásokra vagy natív alkalmazásokat, tekintse meg a gyors üzembe helyezési interaktív bevezetés szakaszában rendelkezésre álló beállításokat a [az Azure AD fejlesztői útmutató](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

5.  Ha végzett, kattintson a **Létrehozás** gombra. Az Azure AD egy egyedi Alkalmazásazonosító rendeli az alkalmazást, és\'újra megnyílik az alkalmazás\'s fő való regisztrációhoz. Attól függően, hogy az alkalmazás webes vagy natív, eltérő lehetőségek állnak rendelkezésre az alkalmazás további funkcióinak hozzáadásához.

>[!Note]
>Alapértelmezés szerint az újonnan regisztrált alkalmazást, hogy csak a felhasználók az ugyanazon bérlőről, jelentkezzen be az alkalmazás van konfigurálva.

<a name="api-methods-and-endpoints"></a>API-metódusai és végpontok
-------------------------

A következő szakaszok ismertetik az API-metódusai és a egy SaaS-ajánlat előfizetéseinek engedélyezése végpontok.

### <a name="get-a-token-based-on-the-azure-ad-app"></a>Az Azure AD-alkalmazás alapján egy token beszerzése

HTTP-metódus

`GET`

*Kérés URL-címe*

**https://login.microsoftonline.com/*{Bérlőazonosító}*  /oauth2/jogkivonat**

*URI-paraméter*

|  **Paraméter neve**  | **Kötelező**  | **Leírás**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| tenantId             | True (Igaz)          | A regisztrált AAD-alkalmazás Bérlőazonosítója   |
|  |  |  |


*Kérelem fejléce*

|  **Fejléc neve**  | **Kötelező** |  **Leírás**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Content-Type     | True (Igaz)         | A kéréshez társított tartalom típusa. Az alapértelmezett érték `application/x-www-form-urlencoded`.  |
|  |  |  |


*Kérelem törzse*

| **Tulajdonság neve**   | **Kötelező** |  **Leírás**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True (Igaz)         | Engedélyezési típus. Az alapértelmezett érték `client_credentials`.                    |
|  Client_id          | True (Igaz)         |  Az Azure AD-alkalmazáshoz társított ügyfél/alkalmazás azonosítója.                  |
|  client_secret      | True (Igaz)         |  Az Azure AD-alkalmazáshoz társított jelszót.                               |
|  Erőforrás           | True (Igaz)         |  A célerőforrás, amelyre a jogkivonat kérik. Az alapértelmezett érték `62d94f6c-d599-489b-a797-3e10e42fbe22`. |
|  |  |  |


*Válasz*

|  **Name (Név)**  | **Típus**       |  **Leírás**    |
| ---------- | -------------  | ------------------- |
| 200 OK    | TokenResponse  | A kérelem sikeres volt   |
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
      "resource": "62d94f6c-d599-489b-a797-3e10e42fbe22",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }               
```

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
| x-ms-activityid    | Igen          | A szolgáltatás a kérelem követési egyedi karakterlánc-érték. Bármely egyeztetések szolgál. |
| Retry-After        | Nem           | Ez az érték csak egy 429 válaszra van beállítva.                                                                   |
|  |  |  |


### <a name="subscribe"></a>Feliratkozás

Az előfizetési végpont lehetővé teszi, hogy a felhasználók számára egy egy SaaS-szolgáltatás egy adott csomag előfizetésének indítása és engedélyezése a számlázás a kereskedelmi rendszerben.

**PUT**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Paraméter neve**  | **Leírás**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Saas-előfizetés, hogy a jogkivonat feloldása API-n keresztül megoldása után egyedi azonosítója.                              |
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
| offerId                | String        | Ajánlat azonosítója, amelyre a felhasználó előfizetett.         |
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
| offerId                | String        | Ajánlat azonosítója, amelyre a felhasználó előfizetett.         |
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
    "offerId": "sampleSaaSOffer", // Provided with "Update" action
    "publisherId": "contoso", 
    "planId": "silver",     // Provided with "Update" action
    "action": "Activate", // Activate/Delete/Suspend/Reinstate/Update
    "timeStamp": "2018-12-01T00:00:00"
  }
```

| **Paraméter neve**     | **Adattípus** | **Leírás**                               |
|------------------------|---------------|-----------------------------------------------|
| id  | String       | A művelet által aktivált egyedi azonosítója.                |
| Tevékenységazonosító   | String        | A szolgáltatás a kérelem követési egyedi karakterlánc-érték. Bármely egyeztetések szolgál.               |
| subscriptionId                     | String        | Előfizetés erőforrás azonosítója, SaaS az Azure-ban.    |
| offerId                | String        | Ajánlat azonosítója, amelyre a felhasználó előfizetett. Csak a "Frissítés" művelet megadott.        |
| publisherId                | String        | Az SaaS-ajánlat a Gyártóazonosítóval         |
| planId                 | String        | Tervezze meg, hogy a felhasználó előfizetett azonosítója. Csak a "Frissítés" művelet megadott.          |
| action                 | String        | A művelet, amely ezt az értesítést kezdeményezi. A lehetséges értékek - aktiválása, töröl, a felfüggesztés, visszaállítása, frissítés          |
| timeStamp                 | String        | Időbélyeg értékét UTC formátumban, amikor az értesítés lett elindítva.          |
|  |  |  |


## <a name="next-steps"></a>További lépések

Szoftveresen is lekérheti a fejlesztők és a számítási feladatok, az ajánlatok és a közzétevő adatkezelési profilok használatával a [Cloud Partner Portal REST API-k](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
