---
title: Regisztráljon egy SaaS-alkalmazás – Azure Marketplace-en |} A Microsoft Docs
description: Azt ismerteti, hogyan regisztrálhat egy SaaS-alkalmazáshoz az Azure portal használatával.
services: Azure, Marketplace, Cloud Partner Portal, Azure portal
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
ms.date: 03/28/2019
ms.author: pbutlerm
ms.openlocfilehash: fbc542ea2ed76d99d551d668b00bad1fb3719a9f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59012935"
---
# <a name="register-a-saas-application"></a>Egy SaaS-alkalmazás regisztrálása

Ez a cikk azt ismerteti, hogyan regisztrálhat egy SaaS-alkalmazás használatával a Microsoft [az Azure portal](https://portal.azure.com/).  A sikeres regisztráció után kapni fog egy Azure Active Directory (Azure AD) biztonsági jogkivonatot a SaaS-teljesítési API-k elérésére használható.  Az Azure AD kapcsolatos további információkért lásd: [a hitelesítés?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)


## <a name="service-to-service-authentication-flow"></a>Szolgáltatások közötti hitelesítési folyamat

Az alábbi ábrán látható, az előfizetés folyamatot egy új ügyfél, és ezen API-k használatakor:

![SaaS API-folyamat ajánlat](./media/saas-offer-publish-api-flow-v1.png)

Azure nincsenek megkötések a hitelesítést, amely a SaaS-szolgáltatás elérhetővé teszi a végfelhasználók számára. Azonban a hitelesítés a SaaS-teljesítési API-kkal egy Azure AD biztonsági jogkivonatot, jellemzően előállított regisztrálja az SaaS-alkalmazás az Azure Portalon keresztül történik. 


## <a name="register-an-azure-ad-secured-app"></a>Az Azure AD által védett alkalmazás regisztrálása

Az Azure AD képességeit felhasználó alkalmazásokat először regisztrálni kell egy Azure AD-bérlőben. A regisztrálási folyamat magában foglalja a jogosultságot ad az alkalmazások, például az URL-címet, hogy hol található, a válaszok elküldésére, a felhasználó hitelesítése után az URL-cím az Azure AD részleteit az URI-t, amely azonosítja az alkalmazást, és így tovább.  Az Azure Portallal egy új alkalmazás regisztrálásához hajtsa végre az alábbi lépéseket:

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
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

        Adott példák a webalkalmazásokra vagy natív alkalmazásokat, tekintse meg a rövid útmutatóban elérhető beállítások interaktív a *Ismerkedés* szakaszában a [az Azure AD fejlesztői útmutató](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

5.  Ha végzett, kattintson a **Létrehozás** gombra. Az Azure AD rendeli hozzá egy egyedi *Alkalmazásazonosító* alkalmazását, és\'újra megnyílik az alkalmazás\'s fő való regisztrációhoz. Attól függően, hogy az alkalmazás webes vagy natív, eltérő lehetőségek állnak rendelkezésre az alkalmazás további funkcióinak hozzáadásához.

>[!Note]
>Alapértelmezés szerint az újonnan regisztrált alkalmazást, hogy csak a felhasználók az ugyanazon bérlőről, jelentkezzen be az alkalmazás van konfigurálva.


## <a name="using-the-azure-ad-security-token"></a>Az Azure AD biztonsági jogkivonat használatával

Ha már regisztrálta az alkalmazást, programozott módon is kérhető az Azure AD-biztonsági token.  A közzétevő használja ezt a jogkivonatot, és kérheti oldható meg kellene szerepelnie.  A különböző teljesítése API-k használata esetén a token lekérdezési paraméter esetén az URL-címben átirányítja a felhasználót, az SaaS-webhelyét az Azure-ból.  Ez a token csak a érvényes egy óra.  Ezenkívül URL-cím akkor használat előtt a böngészőből a token érték dekódolása.

Ezek a jogkivonatok kapcsolatos további információkért lásd: [Azure Active Directory hozzáférési jogkivonatok](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).


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


## <a name="next-steps"></a>További lépések

Az Azure AD által védett alkalmazás most már használhatja a [SaaS teljesítése API 2-es verzió](./cpp-saas-fulfillment-api-v2.md).
