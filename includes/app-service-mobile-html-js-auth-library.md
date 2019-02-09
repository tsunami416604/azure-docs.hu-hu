---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 5fe9fe8ced675f68161f0df9f2665b47f9d47ac5
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55905003"
---
### <a name="server-auth"></a>kézikönyv: Hitelesítés szolgáltatóval (Server Flow)
Ha azt szeretné, hogy a Mobile Apps kezelje az alkalmazása hitelesítési folyamatát, regisztrálnia kell az alkalmazását az identitásszolgáltatójánál. Ezután az Azure App Service-ben be kell állítania a szolgáltatótól kapott alkalmazásazonosítót és titkos kulcsot.
További információt a [hitelesítés alkalmazásokhoz történő hozzáadását](../articles/app-service-mobile/app-service-mobile-cordova-get-started-users.md) ismertető oktatóanyagban találhat.

Ha már regisztrálta az identitásszolgáltatót, hívja meg a `.login()` metódust a szolgáltató nevével. Ha például való bejelentkezés Facebook használja a következő kódot:

```javascript
client.login("facebook").done(function (results) {
     alert("You are now signed in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

A szolgáltatóhoz tartozó érvényes értékek a következők: „aad”, „facebook”, „google”, „microsoftaccount” és „twitter”.

> [!NOTE]
> A hitelesítés Google-fiókkal jelenleg nem használható a Server Flow-n keresztül.  A Google-lel való hitelesítéshez [Client Flow metódust](#client-auth) kell használnia.

Ebben az esetben az Azure App Service felügyeli az OAuth 2.0-s hitelesítési folyamatot.  Ez a kiválasztott szolgáltató bejelentkezési oldalát jeleníti meg, és létrehoz egy App Service hitelesítési tokent a sikeres bejelentkezést követően az identitásszolgáltatónál történő. Amikor a login függvény lezárult, egy olyan JSON-objektumot ad vissza, amely a felhasználói azonosítót és az App Service-hitelesítési tokent a megfelelő userID és auhenticationToken mezőbe helyezi. Ez a token gyorsítótárazható, és újra felhasználható, amíg le nem jár.

### <a name="client-auth"></a>kézikönyv: Hitelesítés szolgáltatóval (Client Flow)

Az alkalmazás függetlenül is kapcsolatba léphet az identitásszolgáltatóval, majd átadhatja a visszakapott tokent az App Service-nek hitelesítésre. Ez a Client Flow lehetővé teszi, hogy egyszeri bejelentkezésen alapuló működést tegyen elérhetővé a felhasználóknak, vagy további felhasználói adatokat kérjen le az identitásszolgáltatótól.

#### <a name="social-authentication-basic-example"></a>Közösségi portálos hitelesítés – alapszintű példa

Ez a példa a Facebook ügyfél-SDK-t használja a hitelesítéshez:

```javascript
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now signed in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});

```
Ez a példa feltételezi, hogy a megfelelő szolgáltatói SDK által adott tokent a rendszer a „token” változóban tárolja.

### <a name="auth-getinfo"></a>kézikönyv: A hitelesített felhasználó adatainak beszerzése

A hitelesítési adatok bármely AJAX-kódtárral lekérhetők az `/.auth/me` végpontról egy HTTP-híváson keresztül.  Ügyeljen arra, hogy a hitelesítési tokenhez az `X-ZUMO-AUTH` fejlécet állítsa be.  A hitelesítési token a következő helyen van tárolva: `client.currentUser.mobileServiceAuthenticationToken`.  Példa a fetch API használatára:

```javascript
var url = client.applicationUrl + '/.auth/me';
var headers = new Headers();
headers.append('X-ZUMO-AUTH', client.currentUser.mobileServiceAuthenticationToken);
fetch(url, { headers: headers })
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

A fetch elérhető [npm-csomagként](https://www.npmjs.com/package/whatwg-fetch), vagy letölthető a [CDNJS-ről](https://cdnjs.com/libraries/fetch). Az adatok lekéréséhez használhatja a jQuery-t vagy egy másik AJAX API-t is.  Az adatokat a rendszer JSON-objektumként fogadja.
