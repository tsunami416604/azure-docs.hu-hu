---
title: Hitelesítési forgatókönyvek a Microsoft Identity platformhoz | Azure
description: Ismerje meg a hitelesítési folyamatokat és a Microsoft Identity platform alkalmazási forgatókönyveit. Ismerje meg az identitások hitelesítését, a jogkivonatok beszerzését és a védett API-k meghívását lehetővéó különböző típusú alkalmazásokat.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/27/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71557a2776bae36508beec8d5af9e00923393163
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72324757"
---
# <a name="authentication-flows-and-application-scenarios"></a>Hitelesítési folyamatok és alkalmazási helyzetek

A Microsoft Identity platform (v 2.0) végpontja támogatja a különböző modern alkalmazások architektúrájának hitelesítését. Az összes architektúra az iparági szabványnak megfelelő protokollok ( [OAuth 2,0 és OpenID Connect](active-directory-v2-protocols.md)) alapján történik.  A [hitelesítési kódtárak](reference-v2-libraries.md)használatával az alkalmazások hitelesítik az identitásokat, és jogkivonatokat kapnak a védett API-k eléréséhez.

Ez a cikk a különböző hitelesítési folyamatokat és azok alkalmazási forgatókönyveit ismerteti, amelyekben használatban van. A cikk a következőket is tartalmazza:
- [Alkalmazás-forgatókönyvek és támogatott hitelesítési folyamatok](#scenarios-and-supported-authentication-flows).
- [Alkalmazás-forgatókönyvek és támogatott platformok és nyelvek](#scenarios-and-supported-platforms-and-languages).

## <a name="application-categories"></a>Alkalmazás-kategóriák

A jogkivonatok számos különböző típusú alkalmazásból szerezhetők be, többek között a következők:

- Webalkalmazások
- Mobilalkalmazások
- Asztali alkalmazások
- Webes API-k

A szolgáltatás olyan eszközökön futó alkalmazásokból is beszerezhető, amelyek nem rendelkeznek böngészővel, vagy amelyek a IoT futnak.

Az alkalmazások az alábbi listában látható módon sorolhatók be:

- [Védett erőforrások és ügyfélalkalmazások](#protected-resources-vs-client-applications): egyes forgatókönyvek olyan erőforrások védelméről szólnak, mint például a Web Apps vagy a webes API-k. Más forgatókönyvek a védett webes API-k meghívására szolgáló biztonsági jogkivonat beszerzésére vonatkoznak.
- [Felhasználók vagy felhasználók nélkül](#with-users-or-without-users): bizonyos forgatókönyvek bejelentkezett felhasználóval rendelkeznek, de más, mint például a Daemon-forgatókönyvek nem tartalmaznak felhasználót.
- [Egyoldalas, nyilvános ügyfél és bizalmas ügyfélalkalmazások](#single-page-public-client-and-confidential-client-applications): ezek az alkalmazások három nagy kategóriája. Mindegyiket különböző kódtárak és objektumok használják.
- [Bejelentkezési célközönség](v2-supported-account-types.md#certain-authentication-flows-dont-support-all-the-account-types): a bejelentkezési célközönségtől függően a rendelkezésre álló hitelesítési folyamatok eltérőek lehetnek. Néhány folyamat csak munkahelyi vagy iskolai fiókokhoz érhető el. Mások pedig munkahelyi vagy iskolai fiókokhoz, valamint személyes Microsoft-fiókokhoz is elérhetők. Az engedélyezett célközönség a hitelesítési folyamattól függ.
- [Támogatott OAuth 2,0 folyamatok](#scenarios-and-supported-authentication-flows): a hitelesítési folyamatok a jogkivonatokat kérő alkalmazási forgatókönyvek megvalósítására szolgálnak. Az alkalmazási forgatókönyvek és a hitelesítési folyamatok között nincs egy az egyhez típusú hozzárendelés.
- [Támogatott platformok](#scenarios-and-supported-platforms-and-languages): nem minden alkalmazás-forgatókönyv érhető el minden platformhoz.

### <a name="protected-resources-vs-client-applications"></a>Védett erőforrások és ügyfélalkalmazások

A hitelesítési forgatókönyvek két tevékenységet foglalnak magukban:

- **Biztonsági jogkivonatok beszerzése egy védett webes API**-hoz: a Microsoft azt javasolja, hogy a [hitelesítési kódtárak](reference-v2-libraries.md#microsoft-supported-client-libraries) használatával szerezze be a jogkivonatokat, különösen a Microsoft Authentication Library (MSAL) termékcsaládot.
- **Webes API-k vagy webalkalmazások védelme**: a webes API-k vagy webalkalmazás-erőforrások védelmének egyik kihívása a biztonsági jogkivonat ellenőrzése. Egyes platformokon a Microsoft [köztes könyvtárakat](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries)is kínál.

### <a name="with-users-or-without-users"></a>Felhasználók vagy felhasználók nélkül

A legtöbb hitelesítési forgatókönyv a bejelentkezett felhasználók nevében jogkivonatokat vásárol.

![Felhasználói helyzetek](media/scenarios/scenarios-with-users.svg)

Vannak azonban olyan Daemon-app-forgatókönyvek is, amelyekben az alkalmazások a jogkivonatokat a felhasználók nevében szerzik be.

![Forgatókönyvek Daemon-alkalmazásokkal](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>Egyoldalas, nyilvános ügyfél és bizalmas ügyfélalkalmazások

A biztonsági jogkivonatok többféle típusú alkalmazásból is beszerezhetők. Ezek az alkalmazások általában három kategóriába sorolhatók:

- **Egyoldalas alkalmazások**: ezek olyan webalkalmazások, amelyek a böngészőben futó JavaScript vagy írógéppel alkalmazásból származó jogkivonatokat szereznek be. Számos modern alkalmazás egyoldalas kezelőfelülettel rendelkezik, amely elsősorban JavaScript nyelven íródott. Az alkalmazás gyakran olyan keretrendszert használ, mint a szögletes, a reakciós vagy a Vue. A MSAL. js az egyetlen olyan Microsoft-hitelesítési függvénytár, amely támogatja az egyoldalas alkalmazásokat.

- **Nyilvános ügyfélalkalmazások**: ezek az alkalmazások mindig bejelentkeznek a felhasználókba:
  - A bejelentkezett felhasználó nevében webes API-kat hívó asztali alkalmazások
  - Mobilalkalmazások
  - A böngészővel nem rendelkező eszközökön futó alkalmazások, például a iOT-on futó alkalmazások

  Ezeket az alkalmazásokat a MSAL [PublicClientApplication](msal-client-applications.md) osztály képviseli.

- **Bizalmas ügyfélalkalmazások**:
  - Webes API-t hívó webalkalmazások
  - Webes API-kat hívó webes API-k
  - Daemon-alkalmazások, még akkor is, ha a konzol szolgáltatásként, például Linux-démonként vagy Windows-szolgáltatásként van implementálva
 
  Az ilyen típusú alkalmazások a [ConfidentialClientApplication](msal-client-applications.md) osztályt használják.

## <a name="application-scenarios"></a>Alkalmazáshasználati helyzetek

A Microsoft Identity platform végpontja támogatja a különböző típusú alkalmazás-architektúrák hitelesítését:

- Egyoldalas alkalmazások
- Webalkalmazások
- Webes API-k
- Mobilalkalmazások
- Natív alkalmazások
- Démonalkalmazások
- Kiszolgálóoldali alkalmazások

Az alkalmazások a különböző hitelesítési folyamatokat használják a felhasználói bejelentkezéshez és a jogkivonatok lekéréséhez a védett API-k meghívásához.

### <a name="a-single-page-application"></a>Egyoldalas alkalmazás

Számos modern webalkalmazás úgy van kialakítva, mint a JavaScript vagy egy SPA-keretrendszer, például a szögletes, a Vue. js és a reakciós. js használatával írt ügyféloldali egyoldalas alkalmazások. Ezek az alkalmazások egy böngészőben futnak. A hitelesítési jellemzői eltérnek a hagyományos, kiszolgálóoldali webalkalmazások. A Microsoft Identity platform használatával az egyoldalas alkalmazások bejelentkezhetnek a felhasználókba, és jogkivonatokat kérhetnek a háttér-szolgáltatások vagy a webes API-k eléréséhez.

![Egyoldalas alkalmazás](media/scenarios/spa-app.svg)

További információ: [egyoldalas alkalmazások](scenario-spa-overview.md).

### <a name="a-web-app-that-is-signing-in-a-user"></a>Egy webalkalmazás, amely egy felhasználónál jelentkezik be

![Egy webalkalmazás, amely egy felhasználónál jelentkezik be](media/scenarios/scenario-webapp-signs-in-users.svg)

Felhasználó által bejelentkezett webalkalmazások elleni védelem:

- Ha a .NET-ben fejleszti a fejlesztést, a ASP.NET vagy a ASP.NET Coret használja a ASP.NET Open ID kapcsolat middleware használatával. Az erőforrások védelme magában foglalja a biztonsági jogkivonat érvényesítését, amelyet a .NET-függvénytár [IdentityModel bővítményei](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) végeznek, és nem MSAL a kódtárakat.

- Ha a Node. js-ben fejleszt fejlesztést, használja a Passport. js fájlt.

További információ: [a felhasználók által bejelentkező webalkalmazás](scenario-web-app-sign-user-overview.md).

### <a name="a-web-app-that-signs-in-a-user-and-calling-a-web-api-on-behalf-of-the-user"></a>Egy webalkalmazás, amely aláírja a felhasználót, és webes API-t hív meg a felhasználó nevében

![Webes API-kat hívó webalkalmazás](media/scenarios/web-app.svg)

Ha egy webes API-t egy felhasználó nevében szeretne meghívni egy webalkalmazásból, használja a MSAL **ConfidentialClientApplication** osztályt. Használja az engedélyezési kód folyamatát, és tárolja a beszerzett jogkivonatokat a jogkivonat-gyorsítótárban. Ha szükséges, a MSAL frissíti a jogkivonatokat, és a vezérlő csendesen beszerzi a tokeneket a gyorsítótárból.

További információkért tekintse meg [a webes API-kat hívó webalkalmazások](scenario-web-app-call-api-overview.md)című témakört.

### <a name="a-desktop-app-calling-a-web-api-on-behalf-of-a-signed-in-user"></a>A bejelentkezett felhasználó nevében webes API-t hívó asztali alkalmazás

Ahhoz, hogy egy asztali alkalmazás meghívjon egy webes API-t, amely a felhasználók számára jelentkezik, használja az MSAL **PublicClientApplication** osztály interaktív jogkivonat-beszerzési módszereit. Ezeknek az interaktív módszereknek a segítségével szabályozhatja a bejelentkezési felhasználói felületet. A MSAL webböngészőt használ ehhez az interakcióhoz.

![Webes API-t hívó asztali alkalmazás](media/scenarios/desktop-app.svg)

A Windows által üzemeltetett alkalmazások számára egy másik lehetőség van a Windows-tartományhoz vagy Azure Active Directory (Azure AD) csatlakoztatott számítógépeken. Ezek az alkalmazások a tokenek csendes beszerzését teszik lehetővé [integrált Windows-hitelesítés](https://aka.ms/msal-net-iwa)használatával.

A böngésző nélküli eszközön futó alkalmazások továbbra is hívhatnak API-t egy felhasználó nevében. A hitelesítéshez a felhasználónak egy webböngészővel rendelkező másik eszközön kell bejelentkeznie. Ehhez a forgatókönyvhöz az [eszköz kódjának folyamatát](https://aka.ms/msal-net-device-code-flow)kell használnia.

![Eszköz kódjának folyamata](media/scenarios/device-code-flow-app.svg)

Habár nem ajánlott használni, a [Felhasználónév/jelszó folyamat](https://aka.ms/msal-net-up) nyilvános ügyfélalkalmazások számára érhető el. Erre a folyamatra továbbra is szükség van bizonyos esetekben, például a DevOps.

Ez a folyamat azonban korlátozásokat alkalmaz az alkalmazásokra. A folyamatot használó alkalmazások például nem jelentkezhetnek be olyan felhasználókba, akiknek többtényezős hitelesítést vagy feltételes hozzáférést kell végrehajtaniuk. Az alkalmazások az egyszeri bejelentkezés előnyeit is kihasználhatják.

A Felhasználónév/jelszó folyamattal való hitelesítés a modern hitelesítés elvein alapul, és csak az örökölt okok miatt biztosítható.

Ha azt szeretné, hogy a jogkivonat-gyorsítótár állandó legyen, az asztali alkalmazásokban [testre kell szabnia a jogkivonat-gyorsítótár szerializálását](https://aka.ms/msal-net-token-cache-serialization). A [kettős jogkivonat-gyorsítótár szerializálásának](https://aka.ms/msal-net-dual-cache-serialization)megvalósításával visszamenőlegesen kompatibilis és továbbítható jogkivonat-gyorsítótárat használhat a hitelesítési könyvtárak előző generációi számára. Az adott kódtárak közé tartozik a .NET-hez készült Azure AD Authentication Library (ADAL.NET) 3-as és 4-es verziója.

További információ: a [webes API-kat meghívó asztali alkalmazás](scenario-desktop-overview.md).

### <a name="a-mobile-app-calling-a-web-api-on-behalf-of-an-interactive-user"></a>Egy interaktív felhasználó nevében webes API-t hívó mobil alkalmazás

Az asztali alkalmazásokhoz hasonlóan a Mobile-alkalmazások is meghívja a MSAL **PublicClientApplication** osztály interaktív jogkivonat-beszerzési módszereit, hogy jogkivonatot szerezzenek a webes API-k meghívásához.

![Webes API-t hívó mobil alkalmazás](media/scenarios/mobile-app.svg)

Az iOS és a MSAL Android MSAL alapértelmezés szerint a rendszerböngészőt használja. A beágyazott webes nézetet azonban közvetlenül is használhatja. Vannak olyan sajátosságok, amelyek a mobil platformtól függenek: Univerzális Windows-platform (UWP), iOS vagy Android.

Bizonyos forgatókönyvek, például az eszköz-AZONOSÍTÓhoz vagy az eszközök regisztrálásához kapcsolódó feltételes hozzáférésre vonatkozó feltételek, a szükséges, hogy a [bróker](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS) telepítve legyen az eszközön. Ilyenek például a Microsoft Céges portál az Androidon és a Microsoft Authenticator Androidon és iOS-en. A MSAL mostantól a brókerekkel is együttműködhet.

> [!NOTE]
> A MSAL. iOS, a MSAL-t használó mobil alkalmazás. Az androidos vagy a Xamarin-alapú MSAL.NET az alkalmazásra vonatkozó védelmi szabályzatok is alkalmazhatók. Előfordulhat például, hogy a házirendek megakadályozhatják a felhasználó számára a védett szöveg másolását. A Mobile alkalmazást az [Intune felügyeli](https://docs.microsoft.com/intune/app-sdk) , és az Intune felügyelt alkalmazásként ismeri fel. Az [Intune app SDK](https://docs.microsoft.com/intune/app-sdk-get-started) elkülöníti a MSAL-kódtárakat, és saját maga kezeli az Azure ad-t.

További információt a [webes API-kat meghívó Mobile App](scenario-mobile-overview.md)című témakörben talál.

### <a name="a-protected-web-api"></a>Védett webes API

A Microsoft Identity platform végpontján keresztül biztonságossá teheti a webszolgáltatásokat, például az alkalmazás REST-alapú webes API-ját. Egy védett webes API-t egy hozzáférési jogkivonattal kell meghívni az API-k adatai biztonságossá tételéhez és a bejövő kérések hitelesítéséhez. Egy webes API hívója hozzáfűz egy hozzáférési jogkivonatot egy HTTP-kérelem engedélyezési fejlécében.

Ha szeretné megóvni a ASP.NET vagy a ASP.NET Core webes API-t, érvényesíteni kell a hozzáférési jogkivonatot. Ehhez az ellenőrzéshez a ASP.NET JWT middleware-t használja. Az érvényesítést a .NET Library [IdentityModel bővítményei](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) , és nem a MSAL.net végzi el.

További információ: [Protected web API](scenario-protected-web-api-overview.md).

### <a name="a-web-api-calling-another-web-api-on-behalf-of-a-user"></a>Egy webes API, amely egy másik webes API-t hív meg egy felhasználó nevében

Ahhoz, hogy ASP.NET vagy ASP.NET Core védett webes API-t hívjon egy másik webes API-t egy felhasználó nevében, az alkalmazásnak tokent kell bekérnie az alárendelt webes API-hoz. Ezt a **ConfidentialClientApplication** osztály [AcquireTokenOnBehalfOf](https://aka.ms/msal-net-on-behalf-of) metódusának meghívásával végezheti el. Az ilyen hívások neve szolgáltatások közötti hívások is. A más webes API-kat meghívó webes API-knak egyéni gyorsítótár-szerializálást kell biztosítaniuk.

  ![Egy webes API, amely egy másik webes API-t hív meg](media/scenarios/web-api.svg)

További információt a webes [API-kat meghívó webes API](scenario-web-api-call-api-overview.md)-k című témakörben talál.

### <a name="a-daemon-app-calling-a-web-api-in-the-daemons-name"></a>Egy Daemon-alkalmazás, amely egy webes API-t hív meg a démon nevében

A hosszan futó folyamatokkal rendelkező vagy felhasználói interakció nélkül működő alkalmazásokhoz is szükség van a biztonságos webes API-k elérésére. Egy ilyen alkalmazás a felhasználó delegált identitása helyett az alkalmazás identitását használva hitelesítheti és lekérheti a jogkivonatokat. Az alkalmazás egy ügyfél-titkos vagy egy tanúsítvány használatával igazolja identitását.

A MSAL **ConfidentialClientApplication** osztály [ügyfél-hitelesítő adatainak](https://aka.ms/msal-net-client-credentials) beszerzési módszereivel olyan démoni alkalmazásokat írhat, amelyek jogkivonatot szereznek a hívó alkalmazás számára. Ezek a módszerek megkövetelik, hogy a hívó alkalmazás titkos kulcsot tartalmazzon az Azure AD-ben. Az alkalmazás ezután megosztja a titkot a nevezett démonsal. Ilyen titkok például az alkalmazás jelszavai, a tanúsítvány-érvényesítés vagy az ügyfél-érvényesítés.

![Más alkalmazások és API-k által hívott Daemon-alkalmazás](media/scenarios/daemon-app.svg)

További információ: a [webes API-kat meghívó alkalmazás](scenario-daemon-overview.md).

## <a name="scenarios-and-supported-authentication-flows"></a>Forgatókönyvek és támogatott hitelesítési folyamatok

A jogkivonatok beszerzését érintő forgatókönyvek a [Microsoft Identity platform protokolljaiban](active-directory-v2-protocols.md)részletezett OAuth 2,0 hitelesítési folyamatokra is leképezhetők.

<table>
 <thead>
  <tr><th>Alkalmazási helyzet</th> <th>Részletes forgatókönyv – útmutató</th> <th>OAuth 2,0 flow és Grant</th> <th>Közönség</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">Egyoldalas alkalmazás</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">Implicit</a></td>
   <td>Munkahelyi vagy iskolai fiókok, személyes fiókok és Microsoft Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web App that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">Felhasználókba bejelentkező webalkalmazás</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Engedélyezési kód</a></td>
   <td>Munkahelyi vagy iskolai fiókok, személyes fiókok és Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web App that signs in users" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">Webes API-kat meghívó webalkalmazás</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Engedélyezési kód</a></td>
   <td>Munkahelyi vagy iskolai fiókok, személyes fiókok és Azure AD B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">Webes API-kat meghívó asztali alkalmazás</a></td>
   <td>Interaktív az <a href="v2-oauth2-auth-code-flow.md">engedélyezési kód</a> és a PKCE használatával</td>
   <td>Munkahelyi vagy iskolai fiókok, személyes fiókok és Azure AD B2C</td>
 </tr>

  <tr>
   <td>Integrált Windows-hitelesítés</td>
   <td>Munkahelyi vagy iskolai fiókok</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Erőforrás-tulajdonos jelszava</a></td>
   <td>Munkahelyi vagy iskolai fiókok és Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">Eszköz kódja</a></td>
   <td>Munkahelyi vagy iskolai fiókok</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">Webes API-kat meghívó mobil alkalmazás</a></td>
   <td>Interaktív az <a href="v2-oauth2-auth-code-flow.md">engedélyezési kód</a> és a PKCE használatával</td>
   <td>Munkahelyi vagy iskolai fiókok, személyes fiókok és Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Erőforrás-tulajdonos jelszava</a></td>
   <td>Munkahelyi vagy iskolai fiókok és Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-daemon-overview.md"><img alt="Daemon app that calls web APIs" src="media/scenarios/daemon-app.svg"></a></td>
   <td><a href=scenario-daemon-overview.md">Webes API-kat meghívó Daemon-alkalmazás</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">Ügyfél-hitelesítő adatok</a></td>
   <td>Csak az Azure AD-szervezeteknél használatos és kizárólag az alkalmazáson belüli engedélyek</td>
 </tr>

  <tr>
   <td><a href=scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href=scenario-web-api-call-api-overview.md">Webes API-kat meghívó webes API</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">A következő nevében</a></td>
   <td>Munkahelyi vagy iskolai fiókok és személyes fiókok</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>Forgatókönyvek és támogatott platformok és nyelvek

A Microsoft hitelesítési könyvtárai több platformot támogatnak:

- JavaScript
- .NET-keretrendszer
- .NET Core
- Windows 10/UWP
- Xamarin.iOS
- Xamarin.Android
- Natív iOS
- macOS
- Natív Android
- Java
- Python

Az alkalmazások létrehozásához különböző nyelveket is használhat. Vegye figyelembe, hogy egyes alkalmazások típusai nem érhetők el minden platformon.

A következő táblázat Windows oszlopában a .NET-keretrendszer minden alkalommal megemlíti a .NET-keretrendszert is. Az utóbbi elhagyja a tábla zsúfoltságának elkerülését.

|Alkalmazási helyzet  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [Egyoldalas alkalmazás](scenario-spa-overview.md) <br/>[![Single-oldal alkalmazás](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Felhasználókba bejelentkező webes alkalmazás](scenario-web-app-sign-user-overview.md) <br/>[@no__t 1Web-alkalmazás, amely bejelentkezik a felhasználók számára](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core
| [Webes API-kat meghívó webalkalmazás](scenario-web-app-call-api-overview.md) <br/> <br/>[@no__t – webes API-kat meghívó 1Web-alkalmazás](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Lombik + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Lombik + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Lombik + MSAL Python
| [Webes API-kat hívó asztali alkalmazás](scenario-desktop-overview.md) <br/> <br/>[@no__t 1Desktop-alkalmazás, amely webes API-kat hív](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) meg @no__t – 2Device Code flow @ no__t-3 | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python <br/> MSAL. ObjC |
| [Webes API-kat meghívó mobil alkalmazás](scenario-mobile-overview.md) <br/> [@no__t – webes API-kat meghívó 1Mobile-alkalmazás](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS/Objective C vagy SWIFT](media/sample-v2-code/small_logo_iOS.png) MSAL. ObjC | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL. Android
| [Daemon-alkalmazás](scenario-daemon-overview.md) <br/> [@no__t – 1Daemon alkalmazás](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python
| [Webes API-kat meghívó webes API](scenario-web-api-call-api-overview.md) <br/><br/> [![Web API, amely webes API-kat hív meg](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python

Lásd még: [Microsoft által támogatott kódtárak operációs rendszer/nyelv alapján](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language).

## <a name="next-steps"></a>Következő lépések
További információ a [hitelesítés alapjairól](authentication-scenarios.md) és a [hozzáférési jogkivonatokról](access-tokens.md).
