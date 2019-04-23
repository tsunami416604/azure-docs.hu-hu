---
title: A Microsoft identity platform alkalmazástípusok |} Az Azure
description: Milyen alkalmazásokat és a Microsoft identity platform (2.0-s verzió) végpont által támogatott forgatókönyveket.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2019
ms.author: celested
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 562b45f30bf057feba38a716e040ab2efc15da90
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785963"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Alkalmazástípusok a Microsoft identity platform

A Microsoft identity platform (2.0-s verzió) végpont számos különböző modern alkalmazásarchitektúrához, mindegyiket alapján az iparági szabványnak számító protokollokat támogatja a hitelesítést [OAuth 2.0 vagy OpenID Connect](active-directory-v2-protocols.md). Ez a cikk ismerteti az, hogy típusú alkalmazásokat hozhat létre a Microsoft identity platform, az előnyben részesített nyelvtől és platformtól függetlenül. Célja, hogy az információk segítségével megismerheti a magas szintű forgatókönyveket, mielőtt [a kóddal munka megkezdéséhez](v2-overview.md#getting-started).

> [!NOTE]
> A Microsoft identity platform végpont nem támogatja az összes Azure Active Directory (Azure AD-) forgatókönyveket és funkciókat. Annak megállapításához, hogy a Microsoft identity platform végpontot kell használnia, olvassa el [a Microsoft identity platform korlátozásai](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Az alapok

Regisztrálnia kell az egyes alkalmazásokhoz, amely a Microsoft identity platform végpontot használja az új [App regisztrációk portál](https://go.microsoft.com/fwlink/?linkid=2083908). Az alkalmazásregisztrációs művelet során gyűjt, és ezeket az értékeket az alkalmazáshoz rendeli hozzá:

* Egy **Alkalmazásazonosítót (ügyfél)** , amely egyedileg azonosítja az alkalmazást
* A **átirányítási URI-t** , amellyel küldött közvetlen válaszokhoz az alkalmazáshoz
* Más forgatókönyv-specifikus értékeket, például a támogatott néhány fióktípusok

Részletekért megtudhatja, hogyan [alkalmazás regisztrálása](quickstart-register-app.md).

Az alkalmazás regisztrálása után az alkalmazás kommunikál a Microsoft identity platform a végponthoz való kérések küldésével. Nyílt forráskódú keretrendszerek és könyvtárak, kezelni a kérések részleteinek biztosítunk. Hitelesítés a logikát alkalmazzák saját kezűleg kérelmek fenti végpontokkal való létrehozásával lehetősége is van:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Egylapos alkalmazások (JavaScript)

Számos modern alkalmazás egylapos alkalmazás előtérrendszerét elsősorban javascriptben írt rendelkezik. Gyakran előfordul az oktatóprogram egy keretrendszert, például az AngularJS, az Ember.js vagy Durandal.js használatával. A Microsoft identity platform végpont használatával támogatja ezeket az alkalmazásokat a [OAuth 2.0 implicit folyamat](v2-oauth2-implicit-grant-flow.md).

Ebből a folyamatból, az alkalmazás fogad jogkivonatok közvetlenül a Microsoft identity platform authorize végponton, minden olyan kiszolgálók közötti adatcsere nélkül. Hitelesítési logikát, és a munkamenet idő szükséges kezelése teljes mértékben a JavaScript-kliens nélkül extra oldal átirányítja az helyezze el.

![Implicit hitelesítési folyamat](./media/v2-app-types/convergence-scenarios-implicit.svg)

Ebben a forgatókönyvben működés közben látni, próbálja ki az egyoldalas alkalmazás Kódminták egyikét a [első lépések a Microsoft identity platform](v2-overview.md#getting-started) szakaszban.

## <a name="web-apps"></a>Webalkalmazások

A web apps (.NET, PHP, Java, Ruby, Python, csomópont), amelyhez a felhasználó böngészőn keresztül hozzáfér, használhatja a [OpenID Connect](active-directory-v2-protocols.md) a felhasználói bejelentkezés. OpenID Connect a webalkalmazás-azonosító jogkivonat kapja. Egy azonosító jogkivonat egy biztonsági jogkivonatot, amely ellenőrzi a felhasználó identitását, és a felhasználói jogcímek formájában információkat biztosít:

```
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

A Microsoft identity platform végponthoz használt tokenek különböző típusú további részleteket a [hozzáférési jogkivonat](access-tokens.md) referencia és [id_token referencia](id-tokens.md)

A webalkalmazásokban server a bejelentkezési hitelesítési folyamat veszi magas szintű lépéseket:

![Webes alkalmazás hitelesítési folyamata](./media/v2-app-types/convergence-scenarios-webapp.svg)

Az azonosító jogkivonat érvényesítése és a Microsoft identity platform végpont-tól kapott nyilvános aláírókulcs győződjön meg, hogy a felhasználó identitását. Egy munkamenetcookie-t be van állítva, a felhasználó lapkérések azonosítására használható.

Ebben a forgatókönyvben működés közben látni, próbálja meg a webes alkalmazás bejelentkezést kódmintái a egyikét a [első lépések a Microsoft identity platform](v2-overview.md#getting-started) szakaszban.

A bejelentkezés, mellett a webkiszolgáló-alkalmazásban egy másik webszolgáltatás, például REST API-t hozzá kell. Ebben az esetben a webkiszolgáló-alkalmazásban folytat egy kombinált OpenID Connectet és az OAuth 2.0 folyamat használatával a [OAuth 2.0 hitelesítési kódfolyamat](active-directory-v2-protocols.md). Ebben a forgatókönyvben kapcsolatos további információkért olvassa el [Ismerkedés a web apps és a webes API-k](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>Webes API-k

Használhatja a Microsoft identity platform végpont webes szolgáltatásokat, például az alkalmazás RESTful webes API biztonságossá tételéhez. Azonosító-jogkivonatokat és a munkamenet-cookie-k helyett egy webes API-t az OAuth 2.0 hozzáférési jogkivonatban használ, az adatok védelméhez és a bejövő kérések hitelesítéséhez. Egy webes API hívója hozzáfűz egy hozzáférési jogkivonatot az engedélyezési fejléc HTTP-kérés, ehhez hasonló:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

A webes API-t használ a hozzáférési jogkivonatot, ellenőrzi az API hívójának identitását, és információkhoz juthat a hívóról kibontani a hozzáférési jogkivonatban kódolt jogcímek. A Microsoft identity platform végponthoz használt tokenek különböző típusú további részleteket a [hozzáférési jogkivonat](access-tokens.md) referencia és [id_token referencia](id-tokens.md)

Egy webes API-t adhat a felhasználóknak engedélyezve van a hibajelentések vagy tilthatják le az adott funkció vagy adatok engedélyek, más néven motorunk [hatókörök](v2-permissions-and-consent.md). Egy hívó alkalmazás hatókörre engedély beszerzéséhez, a felhasználónak jóvá kell hagynia a hatókör folyamat során. A Microsoft identity platform végpont engedélyt arra kéri a felhasználót, és majd rögzíti az engedélyek összes hozzáférési jogkivonatok, amely megkapja a webes API-hoz. A webes API azt ellenőrzi, hogy az egyes hívások kap, és ellenőrzi az engedélyezési jogkivonatot.

Egy webes API hozzáférési jogkivonatok is fogad az összes típusú alkalmazásokat, beleértve a kiszolgáló webalkalmazások, asztali és mobilalkalmazások, egyoldalas alkalmazások, kiszolgálóoldali démonok és akár egyéb webes API-k. A magas szintű folyamat egy webes API-hoz a következőhöz hasonló:

![Webes API-hitelesítési folyamat](./media/v2-app-types/convergence-scenarios-webapi.svg)

Egy webes API biztonságossá OAuth2 hozzáférési jogkivonatok használatával kapcsolatban tekintse meg a webes API-Kódminták a [első lépések a Microsoft identity platform](v2-overview.md#getting-started) szakaszban.

Sok esetben a webes API-kat is szükséges, a kimenő kérelmek más alsóbb rétegbeli webes API-k a Microsoft identity platform által biztosított. Érdekében a webes API-k kihasználhatja a **--meghatalmazásos** folyamat, amely lehetővé teszi a webes API-t az exchange-hozzáférési jogkivonat bejövő kimenő kérelmek használhatók új hozzáférési jogkivonat. További információ: [a Microsoft identity platform és az OAuth 2.0-alapú meghatalmazásos folyamat](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>Mobil- és natív alkalmazások

Eszköz telepített alkalmazások, például az asztali és mobil alkalmazások gyakran igényelnek hozzáférést Háttérszolgáltatásokhoz vagy webes API-k, amelyek adatokat tárolhatnak, és a egy felhasználó nevében műveletek végrehajtására. Ezeket az alkalmazásokat adhat hozzá bejelentkezési és hitelesítési háttérszolgáltatások használatával a [OAuth 2.0 hitelesítési kódfolyamat](v2-oauth2-auth-code-flow.md).

Ezt a folyamatot az alkalmazás megkapja az engedélyezési kódot a Microsoft identity platform végpontról a felhasználó bejelentkezésekor. Az engedélyezési kód háttér-szolgáltatásokat a bejelentkezett felhasználó nevében engedélyt az alkalmazásnak jelöli. Az alkalmazás a háttérben, az OAuth 2.0 hozzáférési jogkivonatban és a egy frissítési jogkivonat a hozzáférési kód tudjon cserélni. Az alkalmazás a hozzáférési jogkivonat segítségével végezhet hitelesítést a webes API-k HTTP-kérelmekre, és a frissítési jogkivonat használatával új hozzáférési jogkivonatok kaphat, ha a korábbi hozzáférési jogkivonatok lejárnak.

![Natív alkalmazás hitelesítési folyamata](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>Démonok és a kiszolgálóoldali alkalmazások

Alkalmazások, amelyek hosszú ideig futó folyamatokat, vagy egy felhasználói interakció nélkül kíván hozzáférni a védett erőforrások, például a webes API-k is kell. Ezek az alkalmazások hitelesítheti és a jogkivonatok lekérésére, az alkalmazás identitás használatával, nem pedig a felhasználó delegált identitása, az OAuth 2.0 ügyfél-hitelesítési folyamata az. Titkos Ügyfélkód vagy tanúsítvány használata az alkalmazás identitását bizonyíthatja. További információ: [tanúsítványok démon alkalmazások a Microsoft identitásplatformja való hitelesítése](https://azure.microsoft.com/resources/samples/active-directory-dotnet-daemon-certificate-credential/).

Ebből a folyamatból, az alkalmazás közvetlenül kommunikál a `/token` hozzáférést szerezni a végpont:

![Démon alkalmazások hitelesítési folyamat](./media/v2-app-types/convergence-scenarios-daemon.svg)

Démon alkalmazások létrehozásának, tekintse meg a [ügyfél hitelesítő adatai dokumentáció](v2-oauth2-client-creds-grant-flow.md), vagy próbálkozzon egy [.NET mintaalkalmazás](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
