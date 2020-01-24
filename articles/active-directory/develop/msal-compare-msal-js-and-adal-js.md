---
title: A MSAL. js és a ADAL. js közötti különbségek | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg a Microsoft Authentication Library for JavaScript (MSAL. js) és az Azure AD Authentication Library for JavaScript (ADAL. js) közötti különbségeket, valamint azt, hogy melyiket válassza ki.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/10/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 7238a78279528b4522d09178d00bf916f14bad88
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696418"
---
# <a name="differences-between-msal-js-and-adal-js"></a>A MSAL JS és a ADAL JS közötti különbségek

A javascripthez készült Microsoft Authentication Library (MSAL. js) és az Azure AD Authentication Library for JavaScript (ADAL. js) is használható az Azure AD-entitások hitelesítésére és az Azure AD-jogkivonatok igénylésére. Eddig a legtöbb fejlesztő dolgozott együtt az Azure ad for Developers (v 1.0) használatával az Azure AD-identitások (munkahelyi és iskolai fiókok) hitelesítéséhez a ADAL-t használó jogkivonatok igénylésével. Mostantól a MSAL. js használatával a Microsoft Identity platform (v 2.0) segítségével a Microsoft-identitások (Azure AD-identitások és Microsoft-fiókok, valamint közösségi és helyi Azure AD B2C fiókok) szélesebb körét is hitelesítheti.

Ez a cikk a javascripthez készült Microsoft Authentication Library (MSAL. js) és az Azure AD Authentication Library for JavaScript (ADAL. js) közötti választást ismerteti, és összehasonlítja a két könyvtárat.

## <a name="choosing-between-adaljs-and-msaljs"></a>Kiválasztás a ADAL. js és a MSAL. js között

A legtöbb esetben a Microsoft Identity platformot és a MSAL. js fájlt szeretné használni, amely a Microsoft hitelesítési kódtárainak legújabb generációja. A MSAL. js használatával az Azure AD-vel (munkahelyi és iskolai fiókokkal), a Microsoft (személyes) fiókokkal (MSA) vagy a Azure AD B2Cekkel bejelentkezett felhasználók számára tokeneket kell beszerezni az alkalmazásba.

Ha már ismeri a v 1.0-végpontot (és a ADAL. js fájlt), érdemes elolvasnia, hogy [Mi a különbség a v 2.0-végpontról?](active-directory-v2-compare.md).

Azonban továbbra is a ADAL. js fájlt kell használnia, ha az alkalmazásnak a [Active Directory összevonási szolgáltatások (AD FS) (ADFS)](/windows-server/identity/active-directory-federation-services)korábbi verzióival kell bejelentkeznie a felhasználókba.

## <a name="key-differences-in-authentication-with-msaljs"></a>A MSAL. js-sel való hitelesítés főbb eltérései

### <a name="core-api"></a>Alapszintű API

* A ADAL. js a [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) -t használja az alkalmazás egy példányának az engedélyezési kiszolgálóhoz vagy az identitás-szolgáltatóhoz való ábrázolásához egy szolgáltatói URL-címen keresztül. Éppen ellenkezőleg, a MSAL. js API-t a felhasználói ügynök ügyfélalkalmazás (a nyilvános ügyfélalkalmazás olyan formája) alapján tervezték meg, amelyben az ügyfél kódját egy felhasználói ügynök, például egy webböngésző hajtja végre. Biztosítja a `UserAgentApplication` osztályt, amely az alkalmazás hitelesítési környezetének egy példányát jelöli az engedélyezési kiszolgálóval. További részletek: inicializálás a [MSAL. js használatával](msal-js-initializing-client-applications.md).

* A ADAL. js-ben a jogkivonatok beszerzésének módszerei a `AuthenticationContext`ban lévő egyetlen szolgáltatóhoz vannak társítva. A MSAL. js fájlban a jogkivonatok beszerzésére irányuló kérések eltérő jogosultságokat vehetnek fel, mint a `UserAgentApplication`ban beállított értékek. Ez lehetővé teszi a MSAL. js számára a tokenek beszerzését és gyorsítótárazását ugyanazon alkalmazás több bérlője és felhasználói fiókja számára.

* A tokenek csendes beszerzésének és megújításának módszere a felhasználók értesítése nélkül, a ADAL. js fájlban `acquireToken` neve. A MSAL. js fájlban ezt a metódust `acquireTokenSilent` kell nevezni, hogy jobban fellegyen a funkció.

### <a name="authority-value-common"></a>A szolgáltató értéke `common`

A v 1.0-s verziójában a `https://login.microsoftonline.com/common`-szolgáltató használatával a felhasználók bármely, bármely szervezet számára elérhető Azure AD-fiókkal jelentkezhetnek be.

A 2.0-s verziójában a `https://login.microsoftonline.com/common`-szolgáltató használatával a felhasználók bármely Azure AD-szervezeti fiókkal vagy egy személyes Microsoft-fiókkal (MSA) bejelentkezhetnek. Ha a bejelentkezést csak az Azure AD-fiókokra szeretné korlátozni (ugyanúgy, mint a ADAL. js-hez), akkor a `https://login.microsoftonline.com/organizations`t kell használnia. Részletekért tekintse meg a [MSAL. js használatával történő inicializálás](msal-js-initializing-client-applications.md)`authority` konfigurációs beállítását.

### <a name="scopes-for-acquiring-tokens"></a>Jogkivonatok beszerzésének hatókörei
* A tokenek beszerzésére irányuló hitelesítési kérelmekben szereplő erőforrás-paraméter helyett a hatókör

    a v 2.0 protokoll hatóköröket használ a kérelmekben lévő erőforrások helyett. Más szóval, amikor az alkalmazásnak olyan jogkivonatokat kell igényelnie, amelyekhez engedélyek szükségesek egy adott erőforráshoz (például MS Graph), a függvénytár-metódusoknak átadott értékek különbsége a következő:

    1\.0-s verzió: erőforrás = HTTPS\://graph.microsoft.com

    v 2.0: hatókör = HTTPS\://graph.microsoft.com/User.Read

    Az API URI-ja használatával bármilyen erőforrás-API-ra kérhet hatóköröket az alábbi formátumban: appidURI/scope például: https:\//mytenant.onmicrosoft.com/myapi/api.read

    Csak az MS Graph API esetében a hatókör-érték `user.read` a https:\//graph.microsoft.com/User.Read, és használható szinonimaként.

    ```javascript
    var request = {
        scopes = ["User.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Dinamikus hatókörök a növekményes belehozatalhoz.

    Ha a 1.0-s verzióval hoz létre alkalmazásokat, regisztrálnia kell az alkalmazás által a bejelentkezéskor a felhasználó számára szükséges engedélyek (statikus hatókörök) teljes készletét. A 2.0-s verzióban a hatókör paraméterrel kérheti le az engedélyeket a kívánt időpontban. Ezeket dinamikus hatóköröknek nevezzük. Ez lehetővé teszi, hogy a felhasználó növekményes beleegyezett a hatókörökbe. Tehát ha az elején csak azt szeretné, hogy a felhasználó bejelentkezzen az alkalmazásba, és nincs szüksége semmilyen hozzáférésre, ezt megteheti. Ha később szükség van a felhasználó naptárának olvasására, a acquireToken metódusokban kérheti le a naptár hatókörét, és beolvashatja a felhasználó belefoglalását. Példa:

    ```javascript
    var request = {
        scopes = ["https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Calendar.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Hatókörök a V 1.0 API-khoz

    Ha a MSAL. js használatával futtatja a V 1.0 API-k jogkivonatait, az API-ban regisztrált összes statikus hatókört kérheti az API hatókörének `.default` hozzáfűzésével. Példa:

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>Következő lépések
További információkért tekintse meg a [1.0-s és a 2.0-s verzió összehasonlítását](active-directory-v2-compare.md)ismertető témakört.
