---
title: Egyszeri bejelentkezés (Microsoft-hitelesítési tár JavaScript-) |} Az Azure
description: Ismerje meg az egyszeri bejelentkezés-megoldásokat a Microsoft-hitelesítési tár (MSAL.js) JavaScript-létrehozásához.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a0fb0731b7ac46210294e3766b33bd6a239dcc2
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075850"
---
# <a name="single-sign-on-with-msaljs"></a>Egyszeri bejelentkezés az MSAL.js

Egyszeri bejelentkezés (SSO) lehetővé teszi a felhasználók számára adja meg a hitelesítő adataikat egyszer jelentkezik be, és hozzon létre egy munkamenetet, amely anélkül, hogy újbóli felhasználhatók több alkalmazásban. Ez és zökkenőmentes élményt nyújt a felhasználónak a hitelesítő adatokat az ismétlődő utasításokat.

Az Azure AD biztosítja, hogy az alkalmazások egyszeri bejelentkezési képességek beállítása egy munkamenetcookie-t, amikor a felhasználó első alkalommal végzi a hitelesítést. Az MSAL.js kódtár lehetővé teszi az alkalmazások Ez több módon is használhatja.

## <a name="sso-between-browser-tabs"></a>Egyszeri bejelentkezés böngészőlapokon között

Amikor az alkalmazás meg nyitva a több lapra, és először jelentkezik be a felhasználó egy lapon, a felhasználó is jelentkezett be a többi lapon nélkül. MSAL.js gyorsítótárazza a felhasználó a böngészőben a azonosító jogkivonat `localStorage` és fog bejelentkezni a felhasználó az alkalmazás a többi megnyitott lap.

Alapértelmezés szerint az MSAL.js használja `sessionStorage` nem engedi a munkamenet lapok között lehetnek megosztva. Beolvasni az egyszeri bejelentkezési lapok között, ügyeljen arra, hogy állítsa be a `cacheLocation` az MSAL.js való `localStorage` alább látható módon.

```javascript
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>Alkalmazások közötti SSO

Egy felhasználó hitelesíti magát, amikor egy munkamenetcookie-t a böngészőben az Azure AD-tartomány van beállítva. MSAL.js támaszkodik a munkamenetcookie-t egyszeri Bejelentkezést biztosít a felhasználó más alkalmazások között. MSAL.js is gyorsítótáraz a azonosító-jogkivonatokat és hozzáférési jogkivonatokat a felhasználó a böngésző tárterület doména aplikace. Ennek eredményeképpen az egyszeri bejelentkezés működése változó, különböző esetek:  

### <a name="applications-on-the-same-domain"></a>Ugyanabban a tartományban lévő alkalmazások

Alkalmazások ugyanabban a tartományban található, amikor a felhasználó be tud jelentkezni az alkalmazás egyszer, és ezután hitelesítik az egyéb alkalmazásokra kérdés nélkül. MSAL.js kihasználja a jogkivonatokat, a felhasználó egyszeri Bejelentkezést biztosít a tartományon a gyorsítótárban.

### <a name="applications-on-different-domain"></a>Másik tartományban lévő alkalmazások

Amikor alkalmazásokat különböző tartományokban található, a gyorsítótárba helyezi a tartományt A jogkivonatok nem érhető el MSAL.js b tartományban

Ez azt jelenti, hogy felhasználók jelentkezett be a tartomány egy nyissa meg a B tartományban lévő alkalmazás, amikor azok lesz átirányítva vagy az Azure AD-oldalról a rendszer. Mivel Azure ad-ben továbbra is rendelkezik a felhasználói munkamenet cookie-t, a felhasználói bejelentkezéshez lesz, és adja meg újra a hitelesítő adatok nem rendelkeznek. Ha a felhasználó több felhasználói fiókot az Azure AD-munkamenetben, jelentkezzen be a megfelelő fiókot választja ki bekéri a felhasználó.

### <a name="automatically-select-account-on-azure-ad"></a>Automatikusan válassza ki a fiókot az Azure ad-ben

Bizonyos esetekben az alkalmazás hozzáfér a felhasználó hitelesítési környezetet, és szeretne elkerülése érdekében a az Azure AD-fiók kiválasztása használatával több fiók be van jelentkezve.  Ezt megteheti különböző módokon:

**A munkamenet-azonosító (SID) használata**

Munkamenet-azonosító egy [választható jogcím](active-directory-optional-claims.md) konfigurálható az azonosító-jogkivonatokat. Ez a jogcím lehetővé teszi, hogy az alkalmazás a felhasználó Azure AD munkamenet független a felhasználói fiók nevét vagy a felhasználónév az azonosításához. A kérelem paramétereit, és az SID adhasson a `acquireTokenSilent` hívja. Ez lehetővé teszi az Azure ad-ben a fiókválasztás kihagyásához. Biztonsági azonosító a munkamenetcookie-t van kötve, és nem lesz adatbázisközi böngésző környezeteket.

```javascript
var request = {
    scopes: ["user.read"],
    sid: sid
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

> [!Note]
> Biztonsági azonosító csak beavatkozás nélküli hitelesítési kérelmek által használható `acquireTokenSilent` MSAL.js-hívás.
Nem kötelező jogcímek konfigurálásáról az alkalmazásjegyzékben annak [Itt](active-directory-optional-claims.md).

**Bejelentkezési mutató használatával**

Ha nem rendelkezik SID jogcím konfigurálva, vagy a fiók kiválasztása használatával interaktív hitelesítés hívásokban megkerülése kell, megteheti azáltal, hogy egy `login_hint` a kérelem paramétereit, és opcionálisan egy `domain_hint` , `extraQueryParameters` az MSAL.js a interaktív metódusok (`loginPopup`, `loginRedirect`, `acquireTokenPopup` és `acquireTokenRedirect`). Példa:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

A jogcímek, az azonosító jogkivonat a felhasználó által visszaadott olvassa el az értékeket is igénybe login_hint és domain_hint.

* **loginHint** értékre kell állítani a `preferred_username` jogcím az azonosító jogkivonat.

* **domain_hint** átadni a/Common használata esetén csak szükséges szolgáltatót. A tartomány mutató bérlői ID(tid) határozza meg.  Ha a `tid` az azonosító jogkivonat jogcím `9188040d-6c67-4c5b-b112-36a304b66dad` fogyasztók. Ellenkező esetben célszerű a szervezetek számára.

Olvasási [Itt](v2-oauth2-implicit-grant-flow.md) további információ a bejelentkezési mutató és a tartomány mutató értéke.

> [!Note]
> Egyszerre nem adhatók át SID és login_hint. Hibaüzenetet eredményez.

## <a name="sso-without-msaljs-login"></a>Egyszeri bejelentkezés MSAL.js bejelentkezés nélkül

A kialakításból fakadóan MSAL.js megköveteli, hogy a bejelentkezési módszert hívja meg a felhasználói környezet létrehozása előtt API-jogkivonatok lekérésének lépéseiről. Mivel interaktív bejelentkezési módszert, a felhasználó kap egy parancssort.

Bizonyos esetekben, amely alkalmazások hozzáférhetnek a hitelesített felhasználói környezetben vagy azonosító jogkivonat-hitelesítésen keresztül egy másik alkalmazás által kezdeményezett, és szeretnék kihasználni az egyszeri bejelentkezés az első bejelentkezés nélkül MSAL.js használatával szerzi be a jogkivonatokat.

A következő példa erre: Egy felhasználó egy szülő-webalkalmazást, amely egy másik JavaScript-alkalmazást futtató egy beépülő modul vagy bővítmény be van jelentkezve.

Az egyszeri Bejelentkezéses felhasználói élmény ebben a forgatókönyvben a következőképpen érhető el:

Adja át a `sid` ha rendelkezésre áll (vagy `login_hint` és opcionálisan `domain_hint`), a kérés paraméterei, az MSAL.js `acquireTokenSilent` hívja az alábbiak szerint:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

## <a name="sso-in-adaljs-to-msaljs-update"></a>Egyszeri bejelentkezés az MSAL.js Update ADAL.js

MSAL.js számos lehetőséget kínál az Azure AD-hitelesítési forgatókönyvek az ADAL.js funkcióparitás. Győződjön meg arról, az áttelepítés ADAL.js az MSAL.js az egyszerű, így elkerülheti kéri a felhasználókat, hogy jelentkezzen be újra, a tár beolvassa a felhasználói munkamenetet képviselő ADAL.js gyorsítótárban azonosító jogkivonat, és zökkenőmentesen MSAL.js a felhasználó bejelentkezik.  

Kihasználhatja az egyszeri bejelentkezés (SSO) viselkedés ADAL.js frissítésekor, kell annak érdekében, hogy a kódtárak használata `localStorage` jogkivonatok gyorsítótárazáshoz. Állítsa be a `cacheLocation` való `localStorage` az MSAL.js és a ADAL.js konfigurációban inicializáláskor az alábbiak szerint:


```javascript
// In ADAL.js
window.config = {
   clientId: 'g075edef-0efa-453b-997b-de1337c29185',
   cacheLocation: 'localStorage'
};

var authContext = new AuthenticationContext(config);


// In latest MSAL.js version
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

Ha ez lett konfigurálva, MSAL.js olvashatja a hitelesített felhasználó ADAL.js a gyorsítótárazott állapotát, és adja meg az egyszeri bejelentkezés az MSAL.js felhasználhatja lesz.

## <a name="next-steps"></a>További lépések

Tudjon meg többet a [egyszeri bejelentkezés munkamenetet, és a jogkivonat élettartama](active-directory-configurable-token-lifetimes.md) értékeket az Azure ad-ben.
