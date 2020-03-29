---
title: Különbségek az MSAL.js és az ADAL.js között | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg a Különbségeket a Microsoft Authentication Library for JavaScript (MSAL.js) és az Azure AD Authentication Library for JavaScript (ADAL.js) között, és hogyan választhatja ki, hogy melyiket használja.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696418"
---
# <a name="differences-between-msal-js-and-adal-js"></a>Az MSAL JS és az ADAL JS közötti különbségek

A Microsoft Authentication Library for JavaScript (MSAL.js) és az Azure AD Authentication Library for JavaScript (ADAL.js) az Azure AD-entitások hitelesítésére és az Azure AD-től tokenek kérésére használható. Eddig a legtöbb fejlesztő az Azure AD fejlesztőknek (1.0-s) az Azure AD-identitások (munkahelyi és iskolai fiókok) hitelesítésén keresztül az ADAL használatával jogkivonatok kérésével. Az MSAL.js használatával a Microsoft-identitások szélesebb körét (Azure AD-identitások és Microsoft-fiókok, valamint közösségi és helyi fiókok az Azure AD B2C-n keresztül) hitelesítheti a Microsoft-identitások platformon keresztül (2.0-s verzió).

Ez a cikk azt ismerteti, hogy miként választhat a Microsoft Authentication Library for JavaScript (MSAL.js) és az Azure AD Authentication Library for JavaScript (ADAL.js) között, és hogyan hasonlítja össze a két könyvtárat.

## <a name="choosing-between-adaljs-and-msaljs"></a>Az ADAL.js és az MSAL.js közötti választás

A legtöbb esetben a Microsoft identity platformot és az MSAL.js-t szeretné használni, amely a Microsoft hitelesítési kódtárai legújabb generációja. Az MSAL.js használatával jogkivonatokat szerez be az alkalmazásba Azure AD-vel (munkahelyi és iskolai fiókokkal), Microsoft (personal) fiókokkal (MSA) vagy Azure AD B2C-vel bejelentkező felhasználók számára.

Ha már ismeri a v1.0 végpontot (és az ADAL.js) érdemes elolvasni [a Mi a különbség a v2.0 végpontban?](active-directory-v2-compare.md).

Az ADAL.js fájl használatát azonban továbbra is használnia kell, ha az alkalmazásnak be kell jelentkeznie az [Active Directory összevonási szolgáltatások (ADFS)](/windows-server/identity/active-directory-federation-services)korábbi verzióival rendelkező felhasználókba.

## <a name="key-differences-in-authentication-with-msaljs"></a>Az MSAL.js-szel való hitelesítés legfontosabb különbségei

### <a name="core-api"></a>Alapvető API

* Az ADAL.js az [AuthenticationContext-t](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) használja az alkalmazás és az engedélyezési kiszolgáló vagy az identitásszolgáltató egy példányának egy példányának ábrázolásaként egy hatóság URL-címén keresztül. Éppen ellenkezőleg, MSAL.js API köré felhasználói ügynök ügyfélalkalmazás (egy fajta nyilvános ügyfél alkalmazás, amelyben az ügyfélkód végrehajtása a felhasználói ügynök, például egy webböngésző). Az osztály `UserAgentApplication` az alkalmazás hitelesítési környezetének egy példányát jelöli az engedélyezési kiszolgálóval. További információt az [Inicializálás a MSAL.js használatával .](msal-js-initializing-client-applications.md)

* Az ADAL.js-ben a tokenek beszerzésének módszerei a `AuthenticationContext`készletében beállított egyetlen hatósághoz kapcsolódnak. Az MSAL.js-ben a beszerzési jogkivonat-kérelmek a `UserAgentApplication`beállítástól eltérő jogosultsági értékeket vehetnek igénybe. Ez lehetővé teszi az MSAL.js számára, hogy a jogkivonatokat külön szerezze be és gyorsítótárazza különböző módon több bérlőés felhasználói fiók számára ugyanabban az alkalmazásban.

* A jogkivonatok csendes beszerzésének és megújításának módszere a felhasználók megkérdezése nélkül az ADAL.js névre `acquireToken` kerül. Az MSAL.js-ben ez `acquireTokenSilent` a módszer a funkció leíróbbneve.

### <a name="authority-value-common"></a>Hatóság értéke`common`

A 1.0-s `https://login.microsoftonline.com/common` vagyonhasználatával a hatóság lehetővé teszi a felhasználók számára, hogy jelentkezzen be bármely Azure AD-fiók (bármely szervezet számára).

A 2.0-s `https://login.microsoftonline.com/common` verzióban a hatóság használatával lehetővé teszi a felhasználók számára, hogy jelentkezzen be bármely Azure AD-szervezet fiókjában vagy a Microsoft személyes fiók (MSA). Ha csak az Azure AD-fiókokra szeretné korlátozni a bejelentkezést (ugyanaz `https://login.microsoftonline.com/organizations`a viselkedés, mint az ADAL.js esetén), a programot kell használnia. További információt az `authority` [Initialize using MSAL.js konfigurációs](msal-js-initializing-client-applications.md)beállításban talál.

### <a name="scopes-for-acquiring-tokens"></a>Gyűjtőkörök tokenek megszerzéséhez
* Hatókör az erőforrásparaméter helyett a jogkivonatok beszerzésére irányuló hitelesítési kérelmekben

    A 2.0-s protokoll a kérelmekben erőforrás helyett hatóköröket használ. Más szóval, ha az alkalmazásnak jogkivonatokat kell kérnie egy erőforráshoz, például az MS Graphhoz, a könyvtármetódusoknak átadott értékek közötti különbség a következő:

    1.0-s oldal:\:erőforrás = https //graph.microsoft.com

    2.0-s érték:\:hatókör = https //graph.microsoft.com/User.Read

    Az API URI-ját használó bármely erőforrás-API-hoz hatókört kérhet ebben a formátumban:\/appidURI/scope Például: https: /mytenant.onmicrosoft.com/myapi/api.read

    Csak az MS Graph API-hoz a hatókör értéke `user.read` https:\//graph.microsoft.com/User.Read, és szinonimaként használható.

    ```javascript
    var request = {
        scopes = ["User.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Dinamikus hatókörök a növekményes jóváhagyáshoz.

    Amikor az 1.0-s verziója használatával alkalmazásokat hoz létre, regisztrálnia kell az alkalmazás által igényelt engedélyek (statikus hatókörök) teljes készletét, amelyhez a felhasználónak a bejelentkezéskori hozzájárulásához szükséges. A 2.0-s vagyonban a hatókör paraméterrel kérheti az engedélyeket a kívánt időpontban. Ezeket dinamikus hatóköröknek nevezzük. Ez lehetővé teszi a felhasználó számára, hogy növekményes jóváhagyást adjon a hatókörökhöz. Tehát, ha az elején csak azt szeretné, hogy a felhasználó jelentkezzen be az alkalmazásba, és nem kell semmilyen hozzáférést, megteheti. Ha később szüksége van a felhasználó naptárának olvasására, akkor kérheti a naptár hatókörét a acquireToken metódusokban, és kérheti a felhasználó beleegyezését. Példa:

    ```javascript
    var request = {
        scopes = ["https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Calendar.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Az 1.0-s API-k hatókörei

    Ha az MSAL.js használatával lekéri a V1.0 API-k tokenjeit, az `.default` API-n regisztrált összes statikus hatókört kérheti az API-n az API-hatókörként való hozzáfűzésével. Példa:

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>További lépések
További információ: [1.0 és 2.0-s összehasonlítás.](active-directory-v2-compare.md)
