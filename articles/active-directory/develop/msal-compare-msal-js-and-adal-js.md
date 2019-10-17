---
title: A MSAL. js és a ADAL. js közötti különbségek | Azure
description: Ismerje meg a Microsoft Authentication Library for JavaScript (MSAL. js) és az Azure AD Authentication Library for JavaScript (ADAL. js) közötti különbségeket, valamint azt, hogy melyiket válassza ki.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7149b9d871c43af083774ffb799255e0d1144113
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72429947"
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

* A ADAL. js-ben a jogkivonatok beszerzésének módszerei a `AuthenticationContext`-ban beállított egyetlen szolgáltatóhoz vannak társítva. A MSAL. js fájlban a jogkivonatok beszerzésére irányuló kérések eltérő jogosultságokat vehetnek fel, mint a `UserAgentApplication` értéknél. Ez lehetővé teszi a MSAL. js számára a tokenek beszerzését és gyorsítótárazását ugyanazon alkalmazás több bérlője és felhasználói fiókja számára.

* A tokenek csendes beszerzésének és megújításának módszere a felhasználók értesítése nélkül, a ADAL. js fájlban `acquireToken`. A MSAL. js fájlban ezt a metódust `acquireTokenSilent` névvel kell elnevezni, hogy ennél a funkciónál jobban legyen leíró jellegű.

### <a name="authority-value-common"></a>Szolgáltatói érték @no__t – 0

A 1.0-s verzióban a `https://login.microsoftonline.com/common` szolgáltató használatával a felhasználók bármilyen Azure AD-fiókkal bejelentkezhetnek (bármely szervezet esetében).

A 2.0-s verzióban a `https://login.microsoftonline.com/common` szolgáltató használatával a felhasználók bármely Azure AD-szervezeti fiókkal vagy egy személyes Microsoft-fiókkal (MSA) bejelentkezhetnek. Ha a bejelentkezést csak az Azure AD-fiókokra szeretné korlátozni (ugyanúgy, mint a ADAL. js-hez), akkor `https://login.microsoftonline.com/organizations` értéket kell használnia. Részletekért lásd a `authority` konfigurációs beállítást az [inicializálás a MSAL. js használatával](msal-js-initializing-client-applications.md)című témakörben.

### <a name="scopes-for-acquiring-tokens"></a>Jogkivonatok beszerzésének hatókörei
* A tokenek beszerzésére irányuló hitelesítési kérelmekben szereplő erőforrás-paraméter helyett a hatókör

    a v 2.0 protokoll hatóköröket használ a kérelmekben lévő erőforrások helyett. Más szóval, amikor az alkalmazásnak olyan jogkivonatokat kell igényelnie, amelyekhez engedélyek szükségesek egy adott erőforráshoz (például MS Graph), a függvénytár-metódusoknak átadott értékek különbsége a következő:

    1\.0-s verzió: erőforrás = HTTPS @ no__t-0//Graph. microsoft. com

    v 2.0: hatókör = HTTPS @ no__t-0//gráf. microsoft. com/user. Read

    Az API URI-ja alapján bármilyen erőforrás-API hatókörét lekérheti a következő formátumban: appidURI/scope, például: https: \//mytenant. onmicrosoft. com/myapi/API. Read

    Csak az MS Graph API esetében a hatókör értékének `user.read` a https: \//Graph. microsoft. com/user. Read, és használható szinonimaként.

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

    Ha a MSAL. js használatával futtatja a V 1.0 API-kat, az API-hoz regisztrált összes statikus hatókört megkérheti, ha a `.default` értéket az API alkalmazás-azonosító URI-jának hatókörként fűzi hozzá. Példa:

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>Következő lépések
További információkért tekintse meg a [1.0-s és a 2.0-s verzió összehasonlítását](active-directory-v2-compare.md)ismertető témakört.
