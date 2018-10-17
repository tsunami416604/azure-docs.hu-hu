---
title: Tudnivalók az Azure digitális Twins API-hitelesítés |} A Microsoft Docs
description: Az Azure digitális Twins csatlakozás és hitelesítés API-k használatával
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: lyrana
ms.openlocfilehash: ef7838c41bb479da273123c2eb3def8e12802390
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49351302"
---
# <a name="connect-and-authenticate-to-apis"></a>Csatlakozás és hitelesítés API-k

Az Azure digitális Twins felhasználók hitelesítéséhez és alkalmazások védelmét az Azure Active Directory (Azure AD) használja. Az Azure AD hitelesítési támogatja a modern architektúrák számos, a szabványos protokollok mindegyiknek alapján, az OAuth 2.0 vagy OpenID Connect. Az Azure AD emellett lehetővé teszi a fejlesztők is egybérlős, – üzletági (LOB) alkalmazások, valamint szeretne több-bérlős alkalmazások fejlesztése a fejlesztők.

Áttekintheti az Azure ad-ben keresse fel a [fundamentals oldal](https://docs.microsoft.com/azure/active-directory/fundamentals/index) részletes útmutatókat, fogalmak és a rövid útmutatók.

Az alkalmazás vagy szolgáltatás az Azure AD-vel történő integrálásához a fejlesztőnek először regisztrálnia kell az alkalmazást az Azure AD-ben. A részletes utasításokat és képernyőképek a vonatkozó utasítások megtekintése [Itt](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

Ezek a [öt elsődleges alkalmazási](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types) Azure AD által támogatott:

* Egyoldalas alkalmazás (SPA): egy felhasználónak szüksége van a bejelentkezni egy egyoldalas alkalmazás Azure AD által védett.
* Webes alkalmazás webböngészőben: egy felhasználónak jelentkezzen be az Azure AD által védett webalkalmazás van szüksége.
* Natív alkalmazás webes API-hoz: egy natív alkalmazás egy telefonon, táblagépen vagy számítógépen futó kell hitelesíteni a felhasználót, hogy a webes API-k az Azure AD által védett erőforrások beolvasása.
* Webes alkalmazás webes API-hoz: webalkalmazás kell-erőforrásokat az Azure AD által biztonságossá tett webes API.
* Webes API-hoz démon, vagy egy kiszolgálói alkalmazás: A démon alkalmazások vagy kiszolgálói alkalmazás webes felhasználói felület nélkül kell-erőforrásokat az Azure AD által biztonságossá tett webes API.

A Windows Azure-hitelesítési tár az Active Directory-jogkivonatok beszerzésére számos lehetőséget kínál. Egy részletes elemzése a könyvtár, valamint a kód a minta irányítópult megtekintése [Itt](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

## <a name="calling-digital-twins-from-a-middle-tier-web-api"></a>A középső rétegbeli webes API-k hívása a digitális Twins

Digitális Twins megoldások fejlesztőknek gyakran mikroszolgáltatásokra válasszon egy középső rétegű alkalmazás vagy az API, amely ezután meghív aktiválásához a digitális Twins API létrehozása. A középső rétegbeli alkalmazás első lenne a felhasználók hitelesíthetők, és ezután egy a-meghatalmazásos token folyamat használni kívánt alsóbb rétegbeli hívásakor. Az a-meghatalmazásos folyamat szervezését kapcsolatos részletes útmutatásért látogasson el [ezt oldal](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Megtekintheti továbbá kódpéldák [Itt](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/).


## <a name="test-with-the-postman-client"></a>Tesztelése a Postman-ügyféllel

Annak érdekében, hogy az első lépésekhez a digitális Twins API-kkal olyan ügyfél, mint például a Postman-API-környezet is használhatja. Postman segítségével gyorsan létrehozhatja az összetett HTTP-kérelmekre. Az alábbi utasítások a Postman felhasználói felületén belül digitális Twins meghívásához szükséges Azure AD-token beszerzése összpontosít.


1. Navigáljon a https://www.getpostman.com/ letölteni az alkalmazást
1. Kövesse a lépéseket [Itt](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) Azure Active Directory-alkalmazás létrehozása (vagy ha szeretné újra felhasználhatja az egy létező regisztráció). 
1. A szükséges engedélyek hozzáadása a "Az Azure digitális Twins", és válassza ki a delegált engedélyek. Ne felejtse el véglegesítéséhez engedélyek megadása gombra.
1. A válasz URL-cím konfigurálása [ https://www.getpostman.com/oauth2/callback ](https://www.getpostman.com/oauth2/callback).
1. Válassza ki a **engedélyezési lapon**, kattintson a **OAuth 2.0**, és válassza ki **új hozzáférési Token letöltése**.

    |**Mező**  |**Érték** |
    |---------|---------|
    | Engedélyezési típus | Implicit |
    | Visszahívási URL | [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) |
    | Hitelesítési URL-cím | [https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0](https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0)
    | Ügyfél-azonosító | Az alkalmazásazonosítót használni létrehozott vagy az 1. lépésben azt egy megváltozott célra az Azure AD-alkalmazás |
    | Hatókör | Hagyja üresen |
    | Állapot | Hagyja üresen |
    | Ügyfél-hitelesítés | Alapszintű hitelesítési fejléc küldése |

1. Kattintson a **jogkivonat kérelmezéséhez**.

    >[!NOTE]
    >Ha hibaüzenetet kap, "OAuth 2 nem lehet befejezni a", megpróbálkozhat a következőkkel:
    > * Postman zárja be és nyissa meg újra, és próbálkozzon újra.
   
1. Görgessen lefelé, és kattintson a **használható jogkivonat**.

## <a name="next-steps"></a>További lépések

Az Azure digitális Twins biztonságával kapcsolatban, olvassa el [létrehozása és kezelése a szerepkör-hozzárendelések](./security-create-manage-role-assignments.md).
