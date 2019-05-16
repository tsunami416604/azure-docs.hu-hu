---
title: MSAL.js és ADAL.js közötti különbségek |} Az Azure
description: További információ a JavaScript (MSAL.js) és az Azure AD Authentication Library for JavaScript (ADAL.js) és a kiválasztása, hogy melyiket kívánja használni a Microsoft-hitelesítési tár közötti különbségeket.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72da1130efc288c03229d990b75bc96c941265d7
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544291"
---
# <a name="differences-between-msal-js-and-adal-js"></a>Az MSAL JS és ADAL JS közötti különbségek

A Microsoft Authentication Library for JavaScript rendszerhez (MSAL.js) és az Azure AD Authentication Library for JavaScript (ADAL.js) is szolgálnak jogkivonatokat kérhet az Azure AD és az Azure AD-entitások hitelesítéséhez. Eddig nagyon a legtöbb fejlesztő már használta az Azure ad-ben (munkahelyi és iskolai fiókok esetében) az Azure AD-identitások hitelesítésére adal-t használó tokenek igénylésével (1.0-s verzió) fejlesztők számára. Most MSAL.js használatával, hitelesítheti a Microsoft identitások (Azure AD-identitások és a Microsoft-fiókok és közösségi és a helyi fiókok Azure AD B2C segítségével) a Microsoft identity platform (2.0-s verzió) révén szélesebb készletét használja.

Ez a cikk ismerteti, hogyan lehet a Microsoft-hitelesítési tár (MSAL.js) JavaScript- és az Azure AD Authentication Library for JavaScript (ADAL.js) közül választhat, és összehasonlítja a két kódtárakat.

## <a name="choosing-between-adaljs-and-msaljs"></a>ADAL.js és az MSAL.js közötti választáshoz

A legtöbb esetben a Microsoft identity platform és az MSAL.js használni kívánt azaz a Microsoft hitelesítési kódtárainak legújabb generációja. MSAL.js használatával, akkor szerzi be a jogkivonatokat a felhasználók számára a bejelentkezés az alkalmazásba az Azure ad-ben (munkahelyi és iskolai fiókok esetében), a Microsoft (személyes) fiókok (MSA), vagy az Azure AD B2C-t.

Már ismeri az 1.0-s verziójú végpont (és a ADAL.js), előfordulhat, hogy szeretné-e olvasni [Mi a v2.0-végpont?](active-directory-v2-compare.md).

Azonban továbbra is szeretné használni a ADAL.js, ha az alkalmazásnak a felhasználók a korábbi [Active Directory összevonási szolgáltatások (ADFS)](/windows-server/identity/active-directory-federation-services).

## <a name="key-differences-in-authentication-with-msaljs"></a>Hitelesítés az MSAL.js fontos eltérés

### <a name="core-api"></a>Core API

* ADAL.js használ [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) , az alkalmazás által létesített kapcsolatban identitásszolgáltató keresztül-szolgáltató URL-CÍMÉT vagy az engedélyezési kiszolgáló példányának reprezentációja. Ezzel szemben, MSAL.js API a felhasználói ügynök ügyfélalkalmazás köré (nyilvános, amelyben az Ügyfélkód hajtja végre a felhasználó például egy webes böngésző ügynök egy ügyfélalkalmazás egy űrlap). Biztosítja a `UserAgentApplication` osztály egy példányát az alkalmazás hitelesítési környezetet az engedélyezési kiszolgálón jelölésére. További részletekért lásd: [inicializálása MSAL.js használatával](msal-js-initializing-client-applications.md).

* A ADAL.js, a jogkivonatok beszerzéséhez módszerek hozzárendelve egyetlen hatóság beállítása az a `AuthenticationContext`. MSAL.js, a beolvasási jogkivonat-kérelmeket időt is igénybe vehet, mi van beállítva, másik szolgáltató értékeket a `UserAgentApplication`. Ez lehetővé teszi az MSAL.js beszerezni, és a jogkivonatok külön-külön gyorsítótár több bérlők és felhasználói fiókok ugyanabban az alkalmazásban.

* A módszert szeretné beszerezni és a felhasználó értesítése nélkül csendes újítsa meg a jogkivonatok nevű `acquireToken` ADAL.js a. MSAL.js, ez a módszer neve `acquireTokenSilent` a funkció kifejezőbb is.

### <a name="authority-value-common"></a>Szolgáltató érték `common`

Az 1.0-s verziójú használja a `https://login.microsoftonline.com/common` szolgáltató lehetővé teszi a felhasználók számára, hogy jelentkezzen be minden olyan Azure AD-fiókot (bármely szervezet).

A 2.0-s verziójú használja a `https://login.microsoftonline.com/common` szolgáltatóként, lehetővé teszi a felhasználók számára történő bejelentkezést bármely szervezet Azure AD-fiókot vagy egy személyes Microsoft-fiókkal (MSA). Csak a bejelentkezési csak az Azure AD-fiókok (ugyanez a viselkedés, a ADAL.js), kell használnia `https://login.microsoftonline.com/organizations`. További információkért lásd: a `authority` konfigurációs lehetőség [inicializálása MSAL.js használatával](msal-js-initializing-client-applications.md).

### <a name="scopes-for-acquiring-tokens"></a>Hatókör-jogkivonatok beszerzésének
* Hatókör-resource paramétert a hitelesítési kérések jogkivonatok beszerzésére helyett

    v2.0 protokoll helyett erőforrás felé küldött kérésekben a hatóköröket. Más szóval az alkalmazásnak kell egy erőforrás, például az MS Graph engedélyekkel rendelkező jogkivonatokat kérhet, amikor a különbség a hitelesítésikönyvtár-metódusok átadott értékek a következőképpen történik:

    1.0-s verzió: erőforrás =https://graph.microsoft.com

    2.0-s verzió: hatókör = https://graph.microsoft.com/User.Read

    Kérheti, hogy valamelyik erőforrás URI-ját az API-t használja a következő formátumban API hatóköreinek: appidURI/hatókörben, például: https://mytenant.onmicrosoft.com/myapi/api.read

    Az MS Graph API, a hatókör értéke csak a `user.read` képez le https://graph.microsoft.com/User.Read és felcserélhetők.

    ```javascript
    var request = {
        scopes = ["User.Read"];
    };

    acquireTokenPopup(request);   
    ```

* A hatókörök dinamikus növekményes beleegyezést.

    1.0-s verziójú használó alkalmazások létrehozását, akkor a teljes körű engedélyeket (statikus hatóköröket), hogy engedélyt adjanak az bejelentkezés alkalmával a felhasználó az alkalmazás által igényelt regisztrációjához szükséges. A 2.0-s verziójú a hatókör-paramétert használhatja, ha az engedélyek kéréséhez a időpontban szeretné azokat. Ezek az úgynevezett dinamikus hatókörök. Ez lehetővé teszi a felhasználónak meg kell adnia a hatókörök növekményes hozzájárulást. Ezért ha elején szeretne a felhasználót, hogy jelentkezzen be az alkalmazást, és nincs szükség bármilyen hozzáférés, megteheti. Ha később kell tudnia kell olvasni a felhasználó a naptár, a naptár hatókör acquireToken módszerek kérelem és a felhasználó beleegyezését kérő. Példa:

    ```javascript
    var request = {
        scopes = ["https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Calendar.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Az 1.0-s verziójú API-k hatókörök

    Amikor MSAL.js használatával 1.0-s verziójú API-jogkivonatok lekérésének lépéseiről, kérheti a hozzáfűzésével az API-t regisztrált összes statikus hatókör `.default` , az Alkalmazásazonosító URI-t az API hatóköreként. Példa:

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>További lépések
További információkért tekintse meg [1.0-s és 2.0-s verzió összehasonlítása](active-directory-v2-compare.md).
