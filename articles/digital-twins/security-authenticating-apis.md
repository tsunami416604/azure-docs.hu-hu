---
title: Megismerheti az Azure digitális Twins API-hitelesítés |} A Microsoft Docs
description: Az Azure digitális Twins használatával csatlakozhat, és API-hitelesítés
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: lyrana
ms.openlocfilehash: f85ab05e785ea559962490b43e75b196d1602159
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51016216"
---
# <a name="connect-and-authenticate-to-apis"></a>Csatlakozás és hitelesítés API-k

Az Azure digitális Twins felhasználók hitelesítéséhez és alkalmazások védelmét az Azure Active Directory (Azure AD) használja. Az Azure AD hitelesítési modern architektúrák különböző támogatja. Ezek mindegyike az iparági szabványnak megfelelő protokoll az OAuth 2.0 vagy OpenID Connect alapul. Emellett a fejlesztők használatával az Azure AD egybérlős és az üzletági (LOB) alkalmazásokat hozhat létre. A fejlesztők is használhatja az Azure AD több-bérlős alkalmazások fejlesztéséhez.

Az Azure AD áttekintést, látogasson el a [fundamentals oldal](https://docs.microsoft.com/azure/active-directory/fundamentals/index) részletes útmutatókat, fogalmak és gyors útmutatók.

Az alkalmazás vagy szolgáltatás az Azure AD-vel történő integrálásához a fejlesztőnek először regisztrálnia kell az alkalmazást az Azure AD-ben. Részletes utasításokat és képernyőképek: [ebben a rövid útmutatóban](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

[Öt elsődleges alkalmazási](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types) az Azure AD által támogatott:

* Egyoldalas alkalmazás (SPA): egy felhasználónak szüksége van a bejelentkezni egy egyoldalas alkalmazás Azure AD által védett.
* Webes alkalmazás webböngészőben: egy felhasználónak jelentkezzen be az Azure AD által védett webalkalmazás van szüksége.
* Natív alkalmazás webes API-hoz: egy natív alkalmazást, amely egy telefonon, táblagépen vagy számítógépen futtat egy felhasználói erőforrásokat lekérni a webes API-k az Azure AD által védett hitelesítenie kell.
* Webes alkalmazás webes API-hoz: webalkalmazás kell-erőforrásokat az Azure AD által biztonságossá tett webes API.
* Webes API-hoz démon vagy a kiszolgáló alkalmazása: egy démon, alkalmazás vagy a nem a webes felhasználói felületének kiszolgálói alkalmazás kell-erőforrásokat az Azure AD által biztonságossá tett webes API.

A Windows Azure-hitelesítési tár az Active Directory-jogkivonatok beszerzésére számos lehetőséget kínál. A szalagtár és a kód minták a részletekért lásd: [Ez a cikk](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Digitális Twins hívása a középső rétegbeli webes API-k

Amikor a fejlesztők számára a digitális Twins megoldások tervezhet, azokat általában hozzon létre egy középső rétegű alkalmazás vagy API-t. Az alkalmazás- vagy API ekkor meghívja a digitális Twins API aktiválásához. A felhasználók először hitelesíthetők a középső rétegbeli alkalmazás, és majd egy a-meghatalmazásos token folyamat hívására alsóbb rétegbeli szolgál. Az a-meghatalmazásos folyamat szervezését kapcsolatos útmutatásért lásd: [ezt oldal](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Emellett megtekintheti Kódminták [ezt oldal](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/).


## <a name="test-with-the-postman-client"></a>Tesztelése a Postman-ügyféllel

Elsajátíthatja a használatát a digitális Twins API-kkal, mint például a Postman ügyfél használhatja egy API-környezet. Postman segítségével gyorsan létrehozhatja az összetett HTTP-kérelmekre. A következő lépésekkel a Postman felhasználói felületén belül digitális Twins meghívásához szükséges Azure AD-token beszerzése.


1. Lépjen a https://www.getpostman.com/ letölteni az alkalmazást.
1. Kövesse a [ebben a rövid útmutatóban](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) létrehozása az Azure AD-alkalmazást. Vagy használhat egy létező regisztráció. 
1. A **szükséges engedélyek**, adja meg az "Azure digitális Twins", és válassza ki **delegált engedélyek**. Válassza ki **engedélyek megadása**.
1. Nyissa meg az alkalmazásjegyzékben, és állítsa be **oauth2AllowImplicitFlow** igaz értékre.
1. A válasz URL-cím konfigurálása [ https://www.getpostman.com/oauth2/callback ](https://www.getpostman.com/oauth2/callback).
1. Válassza ki a **engedélyezési** lapon jelölje be **OAuth 2.0**, majd válassza ki **új hozzáférési Token letöltése**.

    |**Mező**  |**Érték** |
    |---------|---------|
    | Engedélyezési típus | Implicit |
    | Visszahívási URL | [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) |
    | Hitelesítési URL-cím | https://login.microsoftonline.com/<Your Azure AD Tenant e.g. Contoso>.onmicrosoft.com/oauth2/Authorize?Resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0 |
    | Ügyfél-azonosító | Az Alkalmazásazonosítót használni létrehozott vagy a 2. lépésben azt egy megváltozott célra az Azure AD-alkalmazás. |
    | Hatókör | Hagyja üresen. |
    | Állapot | Hagyja üresen. |
    | Ügyfél-hitelesítés | Alapszintű hitelesítési fejléc küldése. |

1. Válassza ki **jogkivonat kérelmezéséhez**.

    >[!NOTE]
    >Ha a hibaüzenetet kapja, "OAuth 2 nem lehet befejezni a", megpróbálkozhat a következőkkel:
    > * Postman, zárja be és nyissa meg újra, és próbálkozzon újra.
   
1. Görgessen lefelé, és válassza ki **használható jogkivonat**.

## <a name="next-steps"></a>További lépések

Az Azure digitális Twins biztonságával kapcsolatban, olvassa el [létrehozása és kezelése a szerepkör-hozzárendelések](./security-create-manage-role-assignments.md).
