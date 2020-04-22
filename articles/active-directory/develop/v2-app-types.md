---
title: Alkalmazástípusok a Microsoft identity platformhoz | Azure
description: A Microsoft identity platform (2.0-s verzió) végpontja által támogatott alkalmazások és forgatókönyvek típusai.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: def92071496716f90b24158a50e4a5233e93c994
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677989"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Alkalmazástípusok a Microsoft identity platformhoz

A Microsoft identity platform (2.0-s verzió) végpontja számos modern alkalmazásarchitektúra hitelesítését támogatja, amelyek mindegyike az [OAuth 2.0 vagy az OpenID Connect](active-directory-v2-protocols.md)szabványnak megfelelő protokollokon alapul. Ez a cikk a Microsoft identity platform használatával létrehozható alkalmazástípusokat ismerteti, függetlenül a kívánt nyelvtől vagy platformtól. Az információ célja, hogy segítsen megérteni a magas szintű forgatókönyveket, mielőtt [elkezdené dolgozni a kódot.](v2-overview.md#getting-started)

## <a name="the-basics"></a>Az alapok

Minden olyan alkalmazást regisztrálnia kell, amely a Microsoft identity platform végpontját használja az új [alkalmazásregisztrációs portálon.](https://go.microsoft.com/fwlink/?linkid=2083908) Az alkalmazásregisztrációs folyamat összegyűjti és hozzárendeli ezeket az értékeket az alkalmazáshoz:

* **Alkalmazásazonosító( ügyfélazonosító,** amely egyedileg azonosítja az alkalmazást)
* Átirányítási **URI,** amelynek segítségével közvetlen válaszokat kaphat az alkalmazásnak
* Néhány egyéb forgatókönyv-specifikus érték, például a támogatott fióktípusok

A részletekért megtudhatja, hogyan [regisztrálhat egy alkalmazást.](quickstart-register-app.md)

Az alkalmazás regisztrálása után az alkalmazás kommunikál a Microsoft identity platform kérelmek küldésével a végpontra. Nyílt forráskódú keretrendszereket és kódtárakat biztosítunk, amelyek kezelik a kérések részleteit. A hitelesítési logikát saját kezűleg is megvalósíthatja a végpontok kéréseinek létrehozásával:

```HTTP
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Egyoldalas alkalmazások (JavaScript)

Számos modern alkalmazás egyoldalas alkalmazás előtér-végződésű, amely elsősorban JavaScript nyelven íródott. Gyakran előfordul, hogy egy keretrendszer használatával íródik, mint az Angular, a React vagy a Vue. A Microsoft identity platform végpontja támogatja ezeket az alkalmazásokat az [OAuth 2.0 implicit folyamat](v2-oauth2-implicit-grant-flow.md)használatával.

Ebben a folyamatban az alkalmazás közvetlenül a Microsoft identity platform authorizepointtól kap jogkivonatokat, kiszolgálók közötti cserék nélkül. Minden hitelesítési logika és munkamenet-kezelés teljes egészében a JavaScript-ügyfélben történik, további oldalátirányítások nélkül.

![Az implicit hitelesítési folyamat megjelenítése](./media/v2-app-types/convergence-scenarios-implicit.svg)

Ha működés közben szeretné látni ezt a forgatókönyvet, próbálja ki az egyoldalas alkalmazáskódminták egyikét a [Microsoft identity platform első lépések](v2-overview.md#getting-started) szakaszában.

## <a name="web-apps"></a>Webalkalmazások

A felhasználó által böngészőn keresztül elérhető webalkalmazások (.NET, PHP, Java, Ruby, Python, Node) esetén az [OpenID Connect](active-directory-v2-protocols.md) et használhatja a felhasználói bejelentkezéshez. Az OpenID Connect ben a webalkalmazás azonosító jogkivonatot kap. Az azonosító jogkivonat egy biztonsági jogkivonat, amely ellenőrzi a felhasználó identitását, és jogcímek formájában szolgáltat információt a felhasználóról:

```JSON
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

A Microsoft identity platform végpontjában használt különböző típusú jogkivonatok további részletei a [hozzáférési jogkivonat-hivatkozásban](access-tokens.md) és [id_token hivatkozásban találhatók.](id-tokens.md)

A webkiszolgáló-alkalmazásokban a bejelentkezési hitelesítési folyamat a következő magas szintű lépéseket teszi:

![A webalkalmazás hitelesítési folyamatának megjelenítése](./media/v2-app-types/convergence-scenarios-webapp.svg)

A felhasználó identitását úgy biztosíthatja, hogy az azonosítójogkivonatot a Microsoft identity platform végpontjától kapott nyilvános aláíró kulccsal érvényesíti. Be van állítva egy munkamenet-cookie, amely a felhasználó azonosítására használható a következő oldalkérésekben.

Ha működés közben szeretné látni ezt a forgatókönyvet, próbálja meg a webalkalmazás bejelentkezési kódmintáit a [Microsoft identity platform első lépések](v2-overview.md#getting-started) szakaszában.

Az egyszerű bejelentkezés mellett előfordulhat, hogy egy webkiszolgáló-alkalmazásnak hozzá kell férnie egy másik webszolgáltatáshoz, például egy REST API-hoz. Ebben az esetben a webkiszolgáló alkalmazás az [OAuth 2.0 engedélyezési kódfolyamatával](active-directory-v2-protocols.md)kombinált OpenID Connect és OAuth 2.0 folyamatot indít. A forgatókönyvvel kapcsolatos további információkért olvassa el a [webalkalmazások és webes API-k első lépéseit.](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)

## <a name="web-apis"></a>Webes API-k

A Microsoft identity platform végpont használatával biztonságos webes szolgáltatások, például az alkalmazás RESTful webes API-t. A webes API-k számos platformon és nyelven valósíthatók meg. Http-eseményindítók használatával is implementálhatók az Azure Functionsben. Az azonosítójogtok és a munkamenet-cookie-k helyett a webes API egy OAuth 2.0 hozzáférési jogkivonatot használ az adatok védelmére és a bejövő kérelmek hitelesítésére. A webes API hívója hozzáfűz egy hozzáférési jogkivonatot egy HTTP-kérelem engedélyezési fejlécében, például:

```HTTP
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

A webes API a hozzáférési jogkivonatot használja az API-hívó identitásának ellenőrzéséhez, és a hívóról a hozzáférési jogkivonatban kódolt jogcímekből származó információk kinyeréséhez. A Microsoft identity platform végpontjában használt különböző típusú jogkivonatok további részletei a [hozzáférési jogkivonat-hivatkozásban](access-tokens.md) és [id_token](id-tokens.md) hivatkozásban találhatók.

A webes API-k lehetővé teszik a felhasználók számára, hogy bizonyos funkciókat vagy adatokat engedélyezzenek vagy letiltsanak az engedélyek, más néven [hatókörök felfedésével.](v2-permissions-and-consent.md) Ahhoz, hogy egy hívó alkalmazás engedélyt szerezzen egy hatókörhöz, a felhasználónak hozzá kell járulnia a hatókörhöz a folyamat során. A Microsoft identity platform végpont engedélyt kér a felhasználótól, majd rögzíti az engedélyeket az összes hozzáférési jogkivonatot, amelyet a webes API kap. A webes API ellenőrzi a hozzáférési jogkivonatokat kap minden hívás, és engedélyezési ellenőrzéseket végez.

A webes API-k hozzáférési jogkivonatokat fogadhatnak minden típusú alkalmazásból, beleértve a webkiszolgálói alkalmazásokat, az asztali és mobilalkalmazásokat, az egyoldalas alkalmazásokat, a kiszolgálóoldali démonokat és még más webes API-kat is. A webes API magas szintű folyamata a következőképpen néz ki:

![A webes API hitelesítési folyamatának megjelenítése](./media/v2-app-types/convergence-scenarios-webapi.svg)

Ha meg szeretné tudni, hogyan biztonságossá egy webes API-t Az OAuth2 hozzáférési jogkivonatok használatával, tekintse meg a webes API-kód mintákat a [Microsoft identity platform első lépések](v2-overview.md#getting-started) szakaszban.

Sok esetben a webes API-k is kell, hogy a kimenő kérelmek et más alsóbb rétegbeli webes API-k által védett Microsoft identity platform. Ehhez a webes API-k kihasználhatják a nevében folyamat előnyeit, amely lehetővé teszi a webes **API-t** egy bejövő hozzáférési jogkivonat cseréjére egy másik hozzáférési jogkivonat hoz a kimenő kérelmekben használható. További információ: [Microsoft identity platform és OAuth 2.0 On-Behalf-Of flow](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>Mobil- és natív alkalmazások

Az eszközre telepített alkalmazásoknak, például a mobil- és asztali alkalmazásoknak gyakran hozzá kell férniük az adatokat tároló és a felhasználó nevében funkciókat ellátó háttérszolgáltatásokhoz vagy webes API-khoz. Ezek az alkalmazások az [OAuth 2.0 engedélyezési kódfolyamat](v2-oauth2-auth-code-flow.md)használatával adhatnak hozzá bejelentkezést és engedélyezést a háttérszolgáltatásokhoz.

Ebben a folyamatban az alkalmazás kap egy engedélyezési kódot a Microsoft identity platform végpont, amikor a felhasználó bejelentkezik. Az engedélyezési kód az alkalmazás azon engedélyét jelöli, hogy a bejelentkezett felhasználó nevében visszahívjon háttérszolgáltatásokat. Az alkalmazás kicserélheti az engedélyezési kódot a háttérben egy OAuth 2.0 hozzáférési jogkivonatra és egy frissítési jogkivonatra. Az alkalmazás a hozzáférési jogkivonat segítségével hitelesítheti a webes API-k at a HTTP-kérelmekben, és a frissítési jogkivonat segítségével új hozzáférési jogkivonatokat kaphat le, amikor a régebbi hozzáférési jogkivonatok lejárnak.

![A natív alkalmazás hitelesítési folyamatának megjelenítése](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>Démonok és kiszolgálóoldali alkalmazások

Azokaz alkalmazások, amelyek hosszú ideig futó folyamatokkal rendelkeznek, vagy amelyek a felhasználóval való interakció nélkül működnek, szintén szükségük van a biztonságos erőforrások, például a webes API-k elérésének módjára. Ezek az alkalmazások hitelesíthetik magukat, és jogkivonatokat kaphatnak az alkalmazás identitásának használatával, nem pedig a felhasználó delegált identitásával, az OAuth 2.0 ügyfélhitelesítő adatok folyamatával. Az alkalmazás identitását ügyféltitok vagy tanúsítvány segítségével bizonyíthatja. További információ: [.NET Core démonkonzol alkalmazás a Microsoft identity platform használatával.](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)

Ebben a folyamatban az alkalmazás közvetlenül kommunikál a `/token` végponteléréséhez:

![A démonalkalmazás hitelesítési folyamatának megjelenítése](./media/v2-app-types/convergence-scenarios-daemon.svg)

Démonalkalmazás létrehozásához tekintse meg az [ügyfél hitelesítő adatait igazoló dokumentációt,](v2-oauth2-client-creds-grant-flow.md)vagy próbálkozzon egy [.NET mintaalkalmazással.](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)
