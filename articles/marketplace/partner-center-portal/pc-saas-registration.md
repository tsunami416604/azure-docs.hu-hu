---
title: SaaS-alkalmazás regisztrálása | Azure piactér
description: Bemutatja, hogyan regisztrálhat egy SaaS-alkalmazást a Azure Portal használatával.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 9c20fe34e108de95a34aabea56390e8a6f0d858f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80275713"
---
# <a name="register-a-saas-application"></a>SaaS-alkalmazás regisztrálása

Ez a cikk azt ismerteti, hogyan regisztrálhat SaaS-alkalmazásokat a Microsoft [Azure Portal](https://portal.azure.com/)használatával.  Sikeres regisztráció után Azure Active Directory (Azure AD) biztonsági jogkivonatot fog kapni, amelyet a SaaS-teljesítési API-k eléréséhez használhat.  További információ az Azure AD-ről: [Mi az a hitelesítés?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)


## <a name="service-to-service-authentication-flow"></a>A szolgáltatások közötti hitelesítési folyamat

Az alábbi ábrán egy új ügyfél előfizetési folyamata látható, valamint az API-k használatakor:

![SaaS-ajánlat API-folyamata](./media/saas-offer-publish-api-flow-v1.png)

Az Azure nem kényszeríti az olyan hitelesítési korlátozásokat, amelyeket az SaaS szolgáltatás a végfelhasználók számára tesz elérhetővé. A SaaS-teljesítési API-kkal való hitelesítés azonban egy Azure AD biztonsági jogkivonattal történik, amelyet általában az SaaS-alkalmazásnak az Azure Portal használatával történő regisztrálásával szereztek be. 


## <a name="register-an-azure-ad-secured-app"></a>Azure AD-védelemmel ellátott alkalmazás regisztrálása

Az Azure AD képességeit felhasználó alkalmazásokat először regisztrálni kell egy Azure AD-bérlőben. Ez a regisztrációs folyamat magában foglalja az alkalmazással kapcsolatos Azure AD-adatokat, például a hely URL-címét, a válaszok küldését a felhasználó hitelesítése után, az alkalmazást azonosító URI-t és így tovább.  Ha az Azure Portal használatával szeretne regisztrálni egy új alkalmazást, hajtsa végre a következő lépéseket:

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2.  Ha a fiókja többhöz biztosít hozzáférést, kattintson a fiókra a jobb felső sarokban, és állítsa be a portál-munkamenetet a kívánt Azure AD-bérlőre.
3.  A bal oldali navigációs ablaktáblán kattintson a **Azure Active Directory** szolgáltatásra, majd a **Alkalmazásregisztrációk**elemre, majd az **új alkalmazás regisztrálása**elemre.

    ![SaaS AD-alkalmazások regisztrációi](./media/saas-offer-app-registration-v1.png)

4.  A létrehozás lapon adja meg az alkalmazás\'regisztrációs adatait:
    -   **Név**: adjon meg egy értelmes alkalmazásnév-nevet
    -   **Alkalmazás típusa**: 
        - Válassza a **Natív** lehetőséget a helyileg vagy eszközre telepített [ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) esetében. Ez a beállítás használatos a nyilvános OAuth [natív ügyfelekhez](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client).
        - Válassza a **webalkalmazás/API** lehetőséget a biztonságos kiszolgálóra telepített [ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) és [erőforrás-/API-alkalmazások](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) számára. Ezzel a beállítással OAuth a bizalmas [webes ügyfelek](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) és a nyilvános [felhasználói ügynökön alapuló ügyfelek](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client).
        Egyazon alkalmazás az ügyfelet és az erőforrást/API-t is elérhetővé teheti.
    -   **Bejelentkezési URL-cím**: webalkalmazás/API-alkalmazások esetén adja meg az alkalmazás alap URL-címét. Például **http://localhost:31544** lehet a helyi gépen futó webalkalmazás URL-címe. A felhasználók ezután ezt az URL-címet használják a webes ügyfélprogramba való bejelentkezéshez.
    -   **Átirányítási URI**: natív alkalmazásokhoz adja meg az Azure ad által a jogkivonat-válaszok visszaadásához használt URI-t. Adja meg az alkalmazáshoz tartozó értéket, például **http://MyFirstAADApp**:.

        ![SaaS AD-alkalmazások regisztrációi](./media/saas-offer-app-registration-v1-2.png)

        A webalkalmazásokhoz és a natív alkalmazásokhoz kapcsolódó példákért tekintse meg az [Azure ad fejlesztői útmutató](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)első *lépések* szakaszában elérhető gyors útmutatókat.

5.  Ha végzett, kattintson a **Létrehozás** gombra. Az Azure AD egy egyedi *alkalmazás-azonosítót* rendel az alkalmazáshoz, és\'újra az alkalmazás\'fő regisztrációs lapjára kerül. Attól függően, hogy az alkalmazás webes vagy natív, eltérő lehetőségek állnak rendelkezésre az alkalmazás további funkcióinak hozzáadásához.

>[!Note]
>Alapértelmezés szerint az újonnan regisztrált alkalmazás úgy van konfigurálva, hogy csak az azonos bérlőhöz tartozó felhasználók jelentkezzenek be az alkalmazásba.


## <a name="using-the-azure-ad-security-token"></a>Az Azure AD biztonsági jogkivonat használata

Az alkalmazás regisztrálását követően programozott módon kérhet Azure AD biztonsági jogkivonatot.  A közzétevőnek a tokent kell használnia, és el kell végeznie a feloldását.  A különböző teljesítési API-k használatakor a jogkivonat-lekérdezési paraméter az URL-címben szerepel, ha a felhasználót az Azure-ból származó SaaS-webhelyre irányítja át.  Ez a jogkivonat csak egy órára érvényes.  Emellett érdemes lehet URL-címet dekódolni a jogkivonat értékének a böngészőből való használata előtt.

További információ ezekről a jogkivonatokról: [Azure Active Directory hozzáférési tokenek](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).


### <a name="get-a-token-based-on-the-azure-ad-app"></a>Token beszerzése az Azure AD-alkalmazás alapján

HTTP-metódus

`POST`

*Kérés URL-címe*

**https://login.microsoftonline.com/*{tenantId}*/oauth2/token**

*URI-paraméter*

|  **Paraméter neve**  | **Szükséges**  | **Leírás**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| tenantId             | True (Igaz)          | A regisztrált HRE-alkalmazás bérlői azonosítója   |
|  |  |  |


*Kérelem fejléce*

|  **Fejléc neve**  | **Szükséges** |  **Leírás**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Content-Type     | True (Igaz)         | A kérelemhez társított tartalomtípus. Az alapértelmezett érték `application/x-www-form-urlencoded`.  |
|  |  |  |


*Kérelem törzse*

| **Tulajdonság neve**   | **Szükséges** |  **Leírás**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True (Igaz)         | Adja meg a típust. Az alapértelmezett érték `client_credentials`.                    |
|  Client_id          | True (Igaz)         |  Az Azure AD-alkalmazáshoz társított ügyfél/alkalmazás-azonosító.                  |
|  client_secret      | True (Igaz)         |  Az Azure AD-alkalmazáshoz társított jelszó.                               |
|  Erőforrás           | True (Igaz)         |  A célként megadott erőforrás, amelyre a tokent kérték. Az alapértelmezett érték `62d94f6c-d599-489b-a797-3e10e42fbe22`. |
|  |  |  |


*Válasz*

|  **Név**  | **Típus**       |  **Leírás**    |
| ---------- | -------------  | ------------------- |
| 200 OK    | TokenResponse  | A kérelem sikeres volt   |
|  |  |  |

*TokenResponse*

Példa a válasz tokenre:

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

Az Azure AD-védelemmel ellátott alkalmazás mostantól használhatja a [SaaS-teljesítési API 2-es verzióját](./pc-saas-fulfillment-api-v2.md).
