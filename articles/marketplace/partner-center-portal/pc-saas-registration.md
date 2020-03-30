---
title: SaaS-kérelem regisztrálása | Azure Piactér
description: Bemutatja, hogyan regisztrálhat egy SaaS-alkalmazást az Azure Portalhasználatával.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 9c20fe34e108de95a34aabea56390e8a6f0d858f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275713"
---
# <a name="register-a-saas-application"></a>SaaS-alkalmazás regisztrálása

Ebből a cikkből megtudhatja, hogyan regisztrálhat saas-alkalmazásokat a Microsoft [Azure portal használatával.](https://portal.azure.com/)  Sikeres regisztráció után egy Azure Active Directory (Azure AD) biztonsági jogkivonatot kap, amely segítségével hozzáférhet a SaaS-teljesítésAPI-k hoz.  Az Azure AD-ről a Mi a hitelesítés című témakörben talál további [információt.](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)


## <a name="service-to-service-authentication-flow"></a>Szolgáltatás-szolgáltatás hitelesítési folyamat

Az alábbi ábra egy új ügyfél előfizetési folyamatát mutatja be, és ha ezeket az API-kat használja:

![A SaaS API-folyamatot kínál](./media/saas-offer-publish-api-flow-v1.png)

Az Azure nem szab meg semmilyen korlátozást a hitelesítés, amely a SaaS-szolgáltatás elérhetővé teszi a végfelhasználók számára. A SaaS-teljesítési API-kkal való hitelesítés azonban egy Azure AD biztonsági jogkivonattal történik, amelyet általában az Azure Portalon keresztül a SaaS-alkalmazás regisztrálásával szerez nek be. 


## <a name="register-an-azure-ad-secured-app"></a>Azure AD-vel védett alkalmazás regisztrálása

Az Azure AD képességeit felhasználó alkalmazásokat először regisztrálni kell egy Azure AD-bérlőben. Ez a regisztrációs folyamat magában foglalja az Azure AD adatait az alkalmazás, például az URL-címet, ahol található, az URL-t a válaszok küldéséhez a felhasználó hitelesítése után, az URI, amely azonosítja az alkalmazást, és így tovább.  Ha új alkalmazást szeretne regisztrálni az Azure Portalhasználatával, hajtsa végre a következő lépéseket:

1.  Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2.  Ha a fiók hozzáférést biztosít egynél több, kattintson a fiók a jobb felső sarokban, és állítsa be a portálmunkamenet a kívánt Azure AD-bérlő.
3.  A bal oldali navigációs ablakban kattintson az **Azure Active Directory** szolgáltatásra, kattintson az **Alkalmazásregisztrációk**elemre, majd az **Új alkalmazásregisztráció parancsra.**

    ![SaaS AD alkalmazás regisztrációk](./media/saas-offer-app-registration-v1.png)

4.  A Létrehozás lapon adja\'meg az alkalmazások regisztrációs adatait:
    -   **Név**: Adjon meg értelmes alkalmazásnevet
    -   **Alkalmazás típusa**: 
        - Válassza a **Natív** lehetőséget a helyileg vagy eszközre telepített [ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) esetében. Ez a beállítás használatos a nyilvános OAuth [natív ügyfelekhez](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client).
        - Válassza ki a **webalkalmazás / API** a biztonságos kiszolgálóra telepített [ügyfélalkalmazásokhoz](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) és [erőforrás-/API-alkalmazásokhoz.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) Ez a beállítás az OAuth bizalmas [webes ügyfelek](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) és a nyilvános [felhasználói ügynök-alapú ügyfelek.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client)
        Egyazon alkalmazás az ügyfelet és az erőforrást/API-t is elérhetővé teheti.
    -   **Bejelentkezési URL:** Webapp/API-alkalmazások esetén adja meg az alkalmazás alap URL-címét. Lehet például **http://localhost:31544** a helyi számítógépen futó webalkalmazás URL-címe. A felhasználók ezt az URL-címet használják a webügyfél-alkalmazásba való bejelentkezéshez.
    -   **URI átirányítása:** Natív alkalmazások esetén adja meg az Azure AD által a jogkivonat-válaszok visszaadására használt URI-t. Adjon meg például **http://MyFirstAADApp**egy, az alkalmazásra jellemző értéket.

        ![SaaS AD alkalmazás regisztrációk](./media/saas-offer-app-registration-v1-2.png)

        A webalkalmazásokra vagy a natív alkalmazásokra vonatkozó konkrét példákat az Azure [AD Developers Guide](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide) *útmutató első* lépések szakaszában elérhető rövid útmutatós beállításokból talál.

5.  Ha végzett, kattintson a **Létrehozás** gombra. Az Azure AD egy egyedi *alkalmazásazonosítót* rendel\'az alkalmazáshoz, és az alkalmazás\'fő regisztrációs oldalára kerül. Attól függően, hogy az alkalmazás webes vagy natív, eltérő lehetőségek állnak rendelkezésre az alkalmazás további funkcióinak hozzáadásához.

>[!Note]
>Alapértelmezés szerint az újonnan regisztrált alkalmazás úgy van beállítva, hogy csak az azonos bérlőből származó felhasználók jelentkezzenek be az alkalmazásba.


## <a name="using-the-azure-ad-security-token"></a>Az Azure AD biztonsági jogkivonat használata

Miután regisztrálta az alkalmazást, programozott módon kérhet egy Azure AD biztonsági jogkivonatot.  A közzétevőnek használnia kell ezt a jogkivonatot, és meg kell adnia a probléma megoldását.  A különböző teljesítési API-k használatakor a token lekérdezési paraméter az URL-címben van, amikor a felhasználó átirányítja az Azure-ból saas-webhelyre.  Ez a jogkivonat csak egy óráig érvényes.  Ezenkívül a tetves i.

Ezekről a jogkivonatokról további információt az [Azure Active Directory-hozzáférési jogkivonatok című témakörben talál.](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)


### <a name="get-a-token-based-on-the-azure-ad-app"></a>Token beszereznie az Azure AD alkalmazás alapján

HTTP-módszer

`POST`

*Kérés URL-címe*

**https://login.microsoftonline.com/*{tenantId}*/oauth2/token**

*URI paraméter*

|  **Paraméter neve**  | **Szükséges**  | **Leírás**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| tenantId             | True (Igaz)          | A regisztrált AAD-alkalmazás bérlői azonosítója   |
|  |  |  |


*Kérelem fejléce*

|  **Fejléc neve**  | **Szükséges** |  **Leírás**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Content-Type     | True (Igaz)         | A kéréshez társított tartalomtípus. Az alapértelmezett érték `application/x-www-form-urlencoded`.  |
|  |  |  |


*Kérelem törzse*

| **Tulajdonság neve**   | **Szükséges** |  **Leírás**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True (Igaz)         | Támogatás típusa. Az alapértelmezett érték `client_credentials`.                    |
|  Client_id          | True (Igaz)         |  Az Azure AD alkalmazáshoz társított ügyfél-/alkalmazásazonosító.                  |
|  client_secret      | True (Igaz)         |  Az Azure AD alkalmazáshoz társított jelszó.                               |
|  Erőforrás           | True (Igaz)         |  Célerőforrás, amelyhez a jogkivonatot kérik. Az alapértelmezett érték `62d94f6c-d599-489b-a797-3e10e42fbe22`. |
|  |  |  |


*Válasz*

|  **Név**  | **Típus**       |  **Leírás**    |
| ---------- | -------------  | ------------------- |
| 200 OK    | TokenVálasz  | A kérelem sikerült   |
|  |  |  |

*TokenVálasz*

Mintaválasz-jogkivonat:

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


## <a name="next-steps"></a>További lépések

Az Azure AD-vel védett alkalmazás most már használhatja a [SaaS fulfillment API 2-es verziója.](./pc-saas-fulfillment-api-v2.md)
