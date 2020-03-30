---
title: Egyszeri bejelentkezés (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan lehet egyszeri bejelentkezési élményt nyújt a Microsoft Authentication Library for JavaScript (MSAL.js) használatával.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8080d4cf4c3f0091f7837b3fccead5474c42db55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262853"
---
# <a name="single-sign-on-with-msaljs"></a>Egyszeri bejelentkezés az MSAL.js-sel

Egyszeri bejelentkezés (SSO) lehetővé teszi a felhasználók számára, hogy adja meg a hitelesítő adatait egyszer, hogy jelentkezzen be, és hozzon létre egy munkamenetet, amely újra felhasználható több alkalmazás anélkül, hogy újra hitelesíteni. Ez zökkenőmentes élményt nyújt a felhasználónak, és csökkenti a hitelesítő adatok ismételt kéréseit.

Az Azure AD egyszeri jelleggel biztosít az alkalmazások számára egy munkamenet-cookie beállításával, amikor a felhasználó első alkalommal hitelesíti magát. Az MSAL.js könyvtár lehetővé teszi az alkalmazások számára, hogy ezt több féleképpen használják ki.

## <a name="sso-between-browser-tabs"></a>SSO böngészőlapok között

Ha az alkalmazás több lapon is meg van nyitva, és először jelentkezik be a felhasználóba az egyik lapon, a felhasználó a többi lapon is be jelentkezik anélkül, hogy a rendszer rákérdezne. Az MSAL.js gyorsítótárazza a felhasználó azonosító `localStorage` tokenjét a böngészőben, és a többi megnyitott lapon bejelentkezik az alkalmazásba.

Alapértelmezés szerint az MSAL.js nem `sessionStorage` teszi lehetővé a munkamenet lapok közötti megosztását. Ahhoz, hogy SSO lapok között, `cacheLocation` győződjön meg róla, hogy állítsa be a MSAL.js az `localStorage` alábbiak szerint.

```javascript
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>SSO alkalmazások között

Amikor egy felhasználó hitelesíti magát, egy munkamenet-cookie van beállítva az Azure AD-tartományban a böngészőben. Az MSAL.js erre a munkamenet-cookie-ra támaszkodik, hogy a felhasználó számára sso-t biztosítson a különböző alkalmazások között. Az MSAL.js a felhasználó azonosító tokenjeit és hozzáférési tokenjeit is gyorsítótárazza a böngészőtárolóban alkalmazástartományonként. Ennek eredményeképpen az SSO viselkedése különböző esetekben változik:  

### <a name="applications-on-the-same-domain"></a>Alkalmazások ugyanazon a tartományon

Ha az alkalmazások ugyanazon a tartományon vannak tárolva, a felhasználó egyszer bejelentkezhet egy alkalmazásba, majd kérdés nélkül hitelesítheti magát a többi alkalmazáshoz. Az MSAL.js a tartomány felhasználója számára gyorsítótárazott tokeneket használja az Egyszeri sso biztosításához.

### <a name="applications-on-different-domain"></a>Alkalmazások különböző tartományban

Ha az alkalmazásokat különböző tartományokban üzemeltetik, az A tartományban gyorsítótárazott tokeneket az MSAL.js nem tudja elérni a B tartományban.

Ez azt jelenti, hogy amikor az A tartományban bejelentkezett felhasználók a B tartományban lévő alkalmazásra navigálnak, a rendszer átirányítja őket, vagy az Azure AD-lapot kéri. Mivel az Azure AD továbbra is rendelkezik a felhasználói munkamenet cookie-jával, bejelentkezik a felhasználóba, és nem kell újra megadnia a hitelesítő adatokat. Ha a felhasználó több felhasználói fiókkal rendelkezik az Azure AD-vel való munkamenetben, a rendszer felszólítja, hogy válassza ki a megfelelő fiókot, amelybe be szeretne jelentkezni.

### <a name="automatically-select-account-on-azure-ad"></a>Fiók automatikus kiválasztása az Azure AD-n

Bizonyos esetekben az alkalmazás hozzáfér a felhasználó hitelesítési környezetéhez, és el szeretné kerülni az Azure AD-fiók kiválasztása parancs, ha több fiók van bejelentkezve.  Ez lehet megtett néhány különböző módon:

**Munkamenet-azonosító (SID) használata**

A munkamenet-azonosító egy [választható jogcím,](active-directory-optional-claims.md) amely konfigurálható az azonosító jogkivonatokban. Ez a jogcím lehetővé teszi, hogy az alkalmazás a felhasználó fióknevétől vagy felhasználónevétől függetlenül azonosítsa a felhasználó Azure AD-munkamenetét. A kérelem paramétereiben a biztonsági azonosítót átadhatja a `acquireTokenSilent` hívásnak. Ez lehetővé teszi, hogy az Azure AD megkerülje a fiók kiválasztását. A SID a munkamenet-cookie-hoz van kötve, és nem lépi át a böngésző környezeteit.

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
> A SID csak az MSAL.js-ben történő hívás által `acquireTokenSilent` küldött néma hitelesítési kérelmekkel használható.
A választható jogcímek konfigurálásának lépéseit az alkalmazásjegyzékben [itt](active-directory-optional-claims.md)találja.

**Bejelentkezési tipp használata**

Ha nincs konfigurálva sid-jogcím, vagy meg kell kerülnie a fiókkiválasztási kérdést `login_hint` az interaktív hitelesítési hívásokban, ezt megteheti a kérelem paramétereinek és opcionálisan `domain_hint` a, `extraQueryParameters` mint az MSAL.js interaktív metódusok`loginPopup`( , `loginRedirect` `acquireTokenPopup` és `acquireTokenRedirect`. Példa:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

A login_hint és domain_hint értékeit a felhasználó azonosítójogkivonatában visszaadott jogcímek olvasásával kaphatja meg.

* **a loginHint-et** `preferred_username` az azonosító jogkivonatban lévő jogcímre kell beállítani.

* **domain_hint** csak a /common authority használata esetén kell átadni. A tartományi emlékeztetőt a bérlői azonosító(tid) határozza meg.  Ha `tid` a jogcím az `9188040d-6c67-4c5b-b112-36a304b66dad` azonosító jogkivonatban az a fogyasztók. Ellenkező esetben, hogy a szervezetek.

Olvassa el [itt](v2-oauth2-implicit-grant-flow.md) további információt az értékek et bejelentkezési tipp és domain tipp.

> [!Note]
> Nem adhatja át egyszerre a SID-et és a login_hint. Ez hibaválaszt eredményez.

## <a name="sso-without-msaljs-login"></a>SSO nélkül MSAL.js bejelentkezés

Az MSAL.js megköveteli, hogy egy bejelentkezési metódust kell hívni egy felhasználói környezet létrehozásához, mielőtt jogkivonatokat kapna az API-khoz. Mivel a bejelentkezési módszerek interaktívak, a felhasználó egy kérdést lát.

Vannak bizonyos esetek, amikor az alkalmazások hozzáférhetnek a hitelesített felhasználó környezetéhez vagy azonosító jogkivonatához egy másik alkalmazásban kezdeményezett hitelesítésen keresztül, és szeretné kitolni az Egyszeri bejelentkezést a jogkivonatok megszerzéséhez anélkül, hogy először bejelentkezne az MSAL.js-en keresztül.

Egy példa erre: A felhasználó be van jelentkezve egy szülő webalkalmazás, amely üzemelteti egy másik JavaScript-alkalmazás fut, mint egy add-on vagy plugin.

Az SSO-élmény ebben a forgatókönyvben a következőképpen érhető el:

Adja `sid` át a `login_hint` ha rendelkezésre áll (vagy opcionálisan `domain_hint`) `acquireTokenSilent` a kérés paramétereit az MSAL.js hívásnak az alábbiak szerint:

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

## <a name="sso-in-adaljs-to-msaljs-update"></a>SSO az ADAL.js-ben az MSAL.js frissítésre

Az MSAL.js szolgáltatásparitást hoz létre az ADAL.js-szel az Azure AD hitelesítési forgatókönyvekhez. Az ADAL.js fájlból az MSAL.js fájlba való áttérés egyszerűvé és a felhasználók ismételt bejelentkezésre való kérésének elkerülése érdekében a könyvtár beolvassa az ADAL.js gyorsítótárban lévő felhasználói munkamenetet képviselő azonosítótokent, és zökkenőmentesen bejelentkezik a felhasználóba az MSAL.js fájlban.  

Az ADAL.js fájlból történő frissítés során az egyszeri bejelentkezési (SSO) viselkedés előnyeinek kihasználásához gondoskodnia kell arról, hogy a kódtárak tokenek gyorsítótárazását használják. `localStorage` Állítsa `cacheLocation` be `localStorage` a következőt mind az MSAL.js, mind az ADAL.js konfigurációban az inicializáláskor:


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
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

Ha ez konfigurálva van, az MSAL.js képes lesz olvasni a hitelesített felhasználó gyorsítótárazott állapotát az ADAL.js fájlban, és ezt az SSO-t az MSAL.js fájlban biztosítva.

## <a name="next-steps"></a>További lépések

Tudjon meg többet az [egyszeri bejelentkezési munkamenet és a jogkivonat élettartama](active-directory-configurable-token-lifetimes.md) értékek az Azure AD-ben.
