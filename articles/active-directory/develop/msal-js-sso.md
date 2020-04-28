---
title: Egyszeri bejelentkezés (MSAL. js) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg az egyszeri bejelentkezéses élmények létrehozását a JavaScripthez készült Microsoft Authentication Library (MSAL. js) használatával.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79262853"
---
# <a name="single-sign-on-with-msaljs"></a>Egyszeri bejelentkezés az MSAL.js-sel

Az egyszeri bejelentkezés (SSO) lehetővé teszi, hogy a felhasználók a bejelentkezéshez egyszer megadják a hitelesítő adataikat, és olyan munkamenetet hozzanak létre, amely többször is felhasználható a több alkalmazás között anélkül, hogy újra kellene hitelesíteni a hitelesítést. Ez zökkenőmentes felhasználói élményt nyújt a felhasználónak, és csökkenti a hitelesítő adatok ismételt megadását.

Az Azure AD egyszeri bejelentkezéses képességeket biztosít az alkalmazásokhoz, ha beállítja a munkamenet-cookie-t, amikor a felhasználó első alkalommal hitelesíti magát. A MSAL. js függvénytár lehetővé teszi, hogy az alkalmazások többféleképpen is kihasználják ezt.

## <a name="sso-between-browser-tabs"></a>Egyszeri bejelentkezés a böngésző lapjai között

Ha az alkalmazás több lapon van megnyitva, és először bejelentkezik egy lapra, a felhasználó a további lapokon is bejelentkezett, és nem kell megadnia. A MSAL. js gyorsítótárba helyezi a felhasználó azonosító tokenjét a `localStorage` böngészőben, és aláírja a felhasználót az alkalmazásba a többi megnyitott lapon.

Alapértelmezés szerint a MSAL. js `sessionStorage` nem engedélyezi, hogy a munkamenet ne legyen megosztva a lapok között. A lapok közötti egyszeri bejelentkezéshez ügyeljen arra, hogy a `cacheLocation` MSAL. js fájlban a `localStorage` lent látható módon állítsa be a következőt:.

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

## <a name="sso-between-apps"></a>Egyszeri bejelentkezés az alkalmazások között

Amikor egy felhasználó hitelesíti magát, egy munkamenet-cookie van beállítva az Azure AD-tartományban a böngészőben. A MSAL. js ezen munkamenet-cookie-ra támaszkodik, hogy a felhasználók a különböző alkalmazások közötti egyszeri bejelentkezést nyújtsanak. A MSAL. js emellett gyorsítótárazza a felhasználó azonosító jogkivonatait és hozzáférési jogkivonatait a böngésző tárterületén. Ennek eredményeképpen az egyszeri bejelentkezés viselkedése különböző esetekben változhat:  

### <a name="applications-on-the-same-domain"></a>Azonos tartományban lévő alkalmazások

Ha az alkalmazások ugyanazon a tartományon futnak, a felhasználó egyszer tud bejelentkezni egy alkalmazásba, majd kérés nélkül hitelesíti a többi alkalmazást. A MSAL. js kihasználja a felhasználó számára a gyorsítótárban gyorsítótárazott jogkivonatokat az egyszeri bejelentkezés biztosításához.

### <a name="applications-on-different-domain"></a>Különböző tartományba tartozó alkalmazások

Ha az alkalmazások különböző tartományokban futnak, az A tartományon gyorsítótárazott tokenek nem érhetők el a B tartomány MSAL. js fájljában.

Ez azt jelenti, hogy amikor a (z) tartományba bejelentkezett felhasználók megnyitnak egy alkalmazást a B tartományban, a rendszer átirányítja vagy megkéri az Azure AD-oldalát. Mivel az Azure AD továbbra is a felhasználói munkamenet cookie-val rendelkezik, be fog jelentkezni a felhasználóba, és nem kell újra megadnia a hitelesítő adatokat. Ha a felhasználó több felhasználói fiókkal rendelkezik az Azure AD-vel való munkamenetben, a rendszer felszólítja a felhasználót, hogy válassza ki a megfelelő fiókot a bejelentkezéshez.

### <a name="automatically-select-account-on-azure-ad"></a>Fiók automatikus kiválasztása az Azure AD-ben

Bizonyos esetekben az alkalmazás hozzáfér a felhasználó hitelesítési környezetéhez, és szeretné elkerülni az Azure AD-fiók kijelölését, ha több fiók van bejelentkezve.  Ezt többféleképpen is elvégezheti:

**Munkamenet-azonosító (SID) használata**

A munkamenet-azonosító egy [opcionális jogcím](active-directory-optional-claims.md) , amely konfigurálható az azonosító jogkivonatokban. Ez a jogcím lehetővé teszi az alkalmazás számára, hogy azonosítsa a felhasználó Azure AD-munkamenetét a felhasználó fiókjának nevétől vagy felhasználónevétől függetlenül. A kérés paramétereinek átadhatja a biztonsági azonosítót a `acquireTokenSilent` híváshoz. Ez lehetővé teszi, hogy az Azure AD megkerüljék a fiók kijelölését. A SID a munkamenet-cookie-hoz van kötve, és nem fog több böngészőbeli kontextust felvenni.

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
> A SID csak a MSAL. js `acquireTokenSilent` hívásával végzett csendes hitelesítési kérések esetén használható.
A választható jogcímek konfigurálásának lépései [itt](active-directory-optional-claims.md)találhatók az alkalmazás jegyzékfájljában.

**A login hint használata**

Ha nincs konfigurálva SID-jogcím, vagy meg kell kerülnie a fiók kiválasztására vonatkozó kérést az interaktív hitelesítési hívásokban, `login_hint` ezt megteheti a kérelem paramétereinek és opcionálisan `domain_hint` `extraQueryParameters` a MSAL. js interaktív metódusokban (`loginPopup`, `loginRedirect` `acquireTokenPopup` és `acquireTokenRedirect`). Például:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

Login_hint és domain_hint értékeit a felhasználó azonosító jogkivonatában visszaadott jogcímek beolvasásával érheti el.

* a **loginHint** a `preferred_username` jogcímnek kell lennie az azonosító jogkivonatban.

* **domain_hint** csak akkor szükséges, ha az/gyakori hibák-szolgáltatót használja. A tartomány-emlékeztetőt a bérlői azonosító (TID) határozza meg.  Ha az `tid` azonosító jogkivonatban `9188040d-6c67-4c5b-b112-36a304b66dad` szereplő jogcím a fogyasztó. Egyéb esetben ez a szervezet.

Az [itt](v2-oauth2-implicit-grant-flow.md) olvasható további információ a bejelentkezési és a tartományi emlékeztető értékeiről.

> [!Note]
> A SID és a login_hint nem adható át egyszerre. Ez hibaüzenetet eredményez.

## <a name="sso-without-msaljs-login"></a>SSO MSAL. js-bejelentkezés nélkül

A MSAL. js megtervezése megköveteli, hogy a rendszer bejelentkezési metódust hozzon létre a felhasználói környezet létrehozásához az API-k jogkivonatok beszerzése előtt. Mivel a bejelentkezési módszerek interaktívak, a felhasználó egy kérést lát.

Bizonyos esetekben előfordulhat, hogy az alkalmazások egy másik alkalmazásban kezdeményezett hitelesítésen keresztül férnek hozzá a hitelesített felhasználó környezetéhez vagy azonosító jogkivonatához, és az egyszeri bejelentkezést szeretnék használni a tokenek beszerzéséhez anélkül, hogy a MSAL. js-en keresztül bejelentkeznek.

Példa erre: A felhasználó egy szülő webalkalmazásba van bejelentkezve, amely bővítményként vagy beépülő modulként futó másik JavaScript-alkalmazást üzemeltet.

Ebben az esetben az egyszeri bejelentkezés felhasználói felülete a következőképpen érhető el:

Adja át `sid` az IF elérhető ( `login_hint` vagy opcionális `domain_hint`) paraméterként a MSAL. js `acquireTokenSilent` -híváshoz a következő módon:

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

## <a name="sso-in-adaljs-to-msaljs-update"></a>SSO a ADAL. js-ben a MSAL. js frissítéséhez

A MSAL. js az Azure AD hitelesítési forgatókönyvek esetében a ADAL. js szolgáltatással biztosítja a funkció paritását. Ahhoz, hogy a ADAL. js fájlról MSAL. js-re váltson át, és ne kelljen újra bejelentkeznie a felhasználóktól, a függvénytár beolvassa a felhasználó munkamenetét jelképező ID tokent a ADAL. js gyorsítótárban, és zökkenőmentesen aláírja a felhasználót a MSAL. js fájlban.  

Az egyszeri bejelentkezés (SSO) működésének a ADAL. js fájlból történő frissítésekor való kihasználásához gondoskodnia kell arról, hogy a kódtárak gyorsítótárazási jogkivonatokat használjanak `localStorage` . Állítsa a `cacheLocation` `localStorage` (z) beállítást a MSAL. js és az ADAL. js konfigurációra az inicializáláskor a következőképpen:


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

Ha ez a beállítás be van állítva, a MSAL. js képes lesz olvasni a hitelesített felhasználó gyorsítótárazott állapotát a ADAL. js fájlban, és ezzel az SSO-t a MSAL. js fájlban is megadhatja.

## <a name="next-steps"></a>További lépések

További információ az [egyszeri bejelentkezési munkamenet és a jogkivonat élettartamának](active-directory-configurable-token-lifetimes.md) értékeiről az Azure ad-ben.
