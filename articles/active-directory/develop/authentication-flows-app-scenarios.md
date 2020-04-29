---
title: A Microsoft Identity platform hitelesítési folyamatai & alkalmazás forgatókönyvei | Azure
description: Ismerje meg a Microsoft Identity platform alkalmazási forgatókönyveit, beleértve az identitások hitelesítését, a jogkivonatok beszerzését és a védett API-k meghívását.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: bf47a352542be0ad4467ed420cc38f62da0d47dc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81534862"
---
# <a name="authentication-flows-and-application-scenarios"></a>Hitelesítési folyamatok és alkalmazási helyzetek

A Microsoft Identity platform (v 2.0) végpontja támogatja a különböző modern alkalmazások architektúrájának hitelesítését. Az összes architektúra az iparági szabványnak megfelelő protokollok ( [OAuth 2,0 és OpenID Connect](active-directory-v2-protocols.md)) alapján történik.  A [Microsoft Identity platform hitelesítési kódtárainak](reference-v2-libraries.md)használatával az alkalmazások hitelesítik az identitásokat, és a védett API-k eléréséhez jogkivonatokat szerezzenek be.

Ez a cikk a hitelesítési folyamatokat és a használt alkalmazási forgatókönyveket ismerteti:

- [Alkalmazás-forgatókönyvek és támogatott hitelesítési folyamatok](#scenarios-and-supported-authentication-flows).
- [Alkalmazás-forgatókönyvek és támogatott platformok és nyelvek](#scenarios-and-supported-platforms-and-languages).

## <a name="application-categories"></a>Alkalmazás-kategóriák

A jogkivonatok számos különböző típusú alkalmazásból szerezhetők be, többek között:

- Webalkalmazások
- Mobilalkalmazások
- Asztali alkalmazások
- Webes API-k

A tokenek beszerezhetők olyan eszközökön futó alkalmazásokból is, amelyek nem rendelkeznek böngészővel, vagy amelyek a IoT futnak.

Az alkalmazások az alábbi listában látható módon sorolhatók be:

- [Védett erőforrások és ügyfélalkalmazások](#protected-resources-vs-client-applications): egyes forgatókönyvek olyan erőforrások védelméről szólnak, mint például a Web Apps vagy a webes API-k. Más forgatókönyvek a védett webes API-k meghívására szolgáló biztonsági jogkivonat beszerzésére vonatkoznak.
- [Felhasználók vagy felhasználók nélkül](#with-users-or-without-users): bizonyos forgatókönyvek bejelentkezett felhasználóval rendelkeznek, másokat is, például a Daemon-forgatókönyveket, nem tartalmaznak felhasználót.
- [Egyoldalas, nyilvános ügyfél és bizalmas ügyfélalkalmazások](#single-page-public-client-and-confidential-client-applications): ezek a típusok három nagy méretű alkalmazásból állnak. Mindegyiket különböző kódtárak és objektumok használják.
- [Bejelentkezési célközönség](v2-supported-account-types.md#certain-authentication-flows-dont-support-all-the-account-types): a bejelentkezési célközönségtől függően a rendelkezésre álló hitelesítési folyamatok eltérőek lehetnek. Néhány folyamat csak munkahelyi vagy iskolai fiókokhoz érhető el. Mások pedig munkahelyi vagy iskolai fiókokhoz, valamint személyes Microsoft-fiókokhoz is elérhetők. Az engedélyezett célközönség a hitelesítési folyamattól függ.
- [Támogatott OAuth 2,0 folyamatok](#scenarios-and-supported-authentication-flows): a hitelesítési folyamatok a jogkivonatokat kérő alkalmazási forgatókönyvek megvalósítására szolgálnak. Az alkalmazási forgatókönyvek és a hitelesítési folyamatok között nincs egy az egyhez típusú hozzárendelés.
- [Támogatott platformok](#scenarios-and-supported-platforms-and-languages): nem minden alkalmazás-forgatókönyv érhető el minden platformhoz.

### <a name="protected-resources-vs-client-applications"></a>Védett erőforrások és ügyfélalkalmazások

A hitelesítési forgatókönyvek két tevékenységet foglalnak magukban:

- **Biztonsági jogkivonatok beszerzése egy védett webes API**-hoz: javasoljuk, hogy a [Microsoft által támogatott ügyféloldali kódtárak](reference-v2-libraries.md#microsoft-supported-client-libraries) használatával szerezze be a jogkivonatokat, különösen a Microsoft Authentication Library (MSAL) termékcsaládot.
- **Webes API-k vagy webalkalmazások védelme**: a webes API-k vagy webalkalmazás-erőforrások védelmének egyik kihívása a biztonsági jogkivonat ellenőrzése. Egyes platformokon a Microsoft [köztes könyvtárakat](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries)is kínál.

### <a name="with-users-or-without-users"></a>Felhasználók vagy felhasználók nélkül

A legtöbb hitelesítési forgatókönyv a bejelentkezett felhasználók nevében jogkivonatokat vásárol.

![Felhasználói helyzetek](media/scenarios/scenarios-with-users.svg)

Vannak azonban olyan Daemon-app-forgatókönyvek is, amelyekben az alkalmazások a jogkivonatokat a felhasználók nevében szerzik be.

![Forgatókönyvek Daemon-alkalmazásokkal](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>Egyoldalas, nyilvános ügyfél és bizalmas ügyfélalkalmazások

A biztonsági jogkivonatok többféle típusú alkalmazásból is beszerezhetők. Ezek az alkalmazások általában három kategóriába sorolhatók:

- **Egyoldalas alkalmazások**: más néven gyógyfürdők, ezek az alkalmazások olyan webalkalmazások, amelyekben jogkivonatok szerezhetők be a böngészőben futó JavaScript vagy írógéppel alkalmazásból. Számos modern alkalmazás egyoldalas kezelőfelülettel rendelkezik, amely elsősorban JavaScript nyelven íródott. Az alkalmazás gyakran olyan keretrendszert használ, mint a szögletes, a reakciós vagy a Vue. A MSAL. js az egyetlen olyan Microsoft-hitelesítési függvénytár, amely támogatja az egyoldalas alkalmazásokat.

- **Nyilvános ügyfélalkalmazások**: ezek az alkalmazások mindig bejelentkeznek a felhasználókba:
  - A bejelentkezett felhasználó nevében webes API-kat hívó asztali alkalmazások
  - Mobilalkalmazások
  - A böngészővel nem rendelkező eszközökön futó alkalmazások, például a iOT-on futó alkalmazások

  Ezeket az alkalmazásokat a MSAL [PublicClientApplication](/dotnet/api/microsoft.identity.client.publicclientapplication) osztály képviseli. További információ: [nyilvános ügyfél és bizalmas ügyfélalkalmazások](msal-client-applications.md).

- **Bizalmas ügyfélalkalmazások**:
  - Webes API-t hívó webalkalmazások
  - Webes API-kat hívó webes API-k
  - Daemon-alkalmazások, még akkor is, ha a konzol szolgáltatásként, például Linux-démonként vagy Windows-szolgáltatásként van implementálva

  Az ilyen típusú alkalmazások a [ConfidentialClientApplication](/dotnet/api/microsoft.identity.client.confidentialclientapplication) osztályt használják. További információ: [nyilvános ügyfél és bizalmas ügyfélalkalmazások](msal-client-applications.md).

## <a name="application-scenarios"></a>Alkalmazáshasználati helyzetek

A Microsoft Identity platform végpontja támogatja a különböző alkalmazás-architektúrák hitelesítését:

- Egyoldalas alkalmazások
- Webalkalmazások
- Webes API-k
- Mobilalkalmazások
- Natív alkalmazások
- Démonalkalmazások
- Kiszolgálóoldali alkalmazások

Az alkalmazások a különböző hitelesítési folyamatokat használják a felhasználói bejelentkezéshez és a jogkivonatok lekéréséhez a védett API-k meghívásához.

### <a name="a-single-page-application"></a>Egyoldalas alkalmazás

Számos modern webalkalmazás úgy van kialakítva, mint az ügyféloldali egyoldalas alkalmazások. Ezek a JavaScript vagy egy egyoldalas alkalmazás-keretrendszer, például a szögletes, a Vue. js és a reakciós. js használatával használhatók. Ezek az alkalmazások egy böngészőben futnak.

A hitelesítési jellemzői eltérnek a hagyományos kiszolgálóoldali webalkalmazások. A Microsoft Identity platform használatával az egyoldalas alkalmazások bejelentkezhetnek a felhasználókba, és jogkivonatokat kérhetnek a háttér-szolgáltatások vagy a webes API-k eléréséhez.

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

Ez a folyamat azonban korlátozza az alkalmazásait. Az alkalmazások például nem jelentkezhetnek be olyan felhasználók számára, akiknek többtényezős hitelesítést vagy feltételes hozzáférést kell használniuk. Az alkalmazások az egyszeri bejelentkezés előnyeit is kihasználhatják.

A Felhasználónév/jelszó folyamattal való hitelesítés a modern hitelesítés elvein alapul, és csak az örökölt okok miatt biztosítható.

Ha azt szeretné, hogy a jogkivonat gyorsítótára továbbra is fennmaradjon, akkor a [jogkivonat-gyorsítótár szerializálását](https://aka.ms/msal-net-token-cache-serialization)a Desktop apps alkalmazásban kell beállítani. A [kettős jogkivonat-gyorsítótár szerializálásának](https://aka.ms/msal-net-dual-cache-serialization)megvalósításával visszamenőlegesen kompatibilis és továbbítható jogkivonat-gyorsítótárak is használhatók. Ezek a jogkivonatok támogatják a hitelesítési kódtárak előző generációit. Az adott kódtárak közé tartozik a .NET-hez készült Azure AD Authentication Library (ADAL.NET) 3-as és 4-es verziója.

További információ: a [webes API-kat meghívó asztali alkalmazás](scenario-desktop-overview.md).

### <a name="a-mobile-app-calling-a-web-api-on-behalf-of-an-interactive-user"></a>Egy interaktív felhasználó nevében webes API-t hívó mobil alkalmazás

Az asztali alkalmazásokhoz hasonlóan a Mobile-alkalmazások is meghívja a MSAL **PublicClientApplication** osztály interaktív jogkivonat-beszerzési módszereit, hogy jogkivonatot szerezzenek a webes API-k meghívásához.

![Webes API-t hívó mobil alkalmazás](media/scenarios/mobile-app.svg)

Az iOS és a MSAL Android MSAL alapértelmezés szerint a rendszerböngészőt használja. A beágyazott webes nézetet azonban közvetlenül is használhatja. Vannak olyan sajátosságok, amelyek a mobil platformtól függenek: Univerzális Windows-platform (UWP), iOS vagy Android.

Bizonyos forgatókönyvek, például az eszköz-AZONOSÍTÓhoz vagy az eszközök regisztrálásához kapcsolódó feltételes hozzáférésre vonatkozó feltételek, a szükséges, hogy a bróker telepítve legyen az eszközön. Ilyenek például a Microsoft Céges portál az Androidon és a Microsoft Authenticator Androidon és iOS-en. A MSAL mostantól képes együttműködni a brókerekkel. További információ: [a brókerek kihasználása Androidon és iOS-](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS)en.

További információt a [webes API-kat meghívó Mobile App](scenario-mobile-overview.md)című témakörben talál.

> [!NOTE]
> A MSAL. iOS, a MSAL-t használó mobil alkalmazás. Az androidos vagy a Xamarin-alapú MSAL.NET az alkalmazásra vonatkozó védelmi szabályzatok is alkalmazhatók. Előfordulhat például, hogy a házirendek megakadályozhatják a felhasználó számára a védett szöveg másolását. A Mobile alkalmazást az Intune felügyeli, és az Intune felügyelt alkalmazásként ismeri fel. További információ: [Microsoft Intune app SDK – áttekintés](https://docs.microsoft.com/intune/app-sdk).
>
> Az [Intune app SDK](https://docs.microsoft.com/intune/app-sdk-get-started) elkülöníti a MSAL-kódtárakat, és saját maga kezeli az Azure ad-t.

### <a name="a-protected-web-api"></a>Védett webes API

A Microsoft Identity platform végpontján keresztül biztonságossá teheti a webszolgáltatásokat, például az alkalmazás REST-alapú webes API-ját. Egy védett webes API-t hozzáférési jogkivonat használatával kell meghívni. A jogkivonat gondoskodik az API adatairól és a bejövő kérések hitelesítéséről. Egy webes API hívója hozzáfűz egy hozzáférési jogkivonatot egy HTTP-kérelem engedélyezési fejlécében.

Ha szeretné megóvni a ASP.NET vagy a ASP.NET Core webes API-t, érvényesíteni kell a hozzáférési jogkivonatot. Ehhez az ellenőrzéshez a ASP.NET JWT middleware-t használja. Az érvényesítést a .NET Library [IdentityModel bővítményei](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) , és nem a MSAL.net végzi el.

További információ: [Protected web API](scenario-protected-web-api-overview.md).

### <a name="a-web-api-calling-another-web-api-on-behalf-of-a-user"></a>Egy webes API, amely egy másik webes API-t hív meg egy felhasználó nevében

Ahhoz, hogy ASP.NET vagy ASP.NET Core védett webes API-t hívjon egy másik webes API-t egy felhasználó nevében, az alkalmazásnak tokent kell bekérnie az alárendelt webes API-hoz. A jogkivonat beszerzéséhez az alkalmazás meghívja a **ConfidentialClientApplication** osztály [AcquireTokenOnBehalfOf](https://aka.ms/msal-net-on-behalf-of) metódusát. Az ilyen hívások neve szolgáltatások *közötti* hívások is. A más webes API-kat meghívó webes API-knak egyéni gyorsítótár-szerializálást kell biztosítaniuk.

  ![Egy webes API, amely egy másik webes API-t hív meg](media/scenarios/web-api.svg)

További információt a webes [API-kat meghívó webes API](scenario-web-api-call-api-overview.md)-k című témakörben talál.

### <a name="a-daemon-app-calling-a-web-api-in-the-daemons-name"></a>Egy Daemon-alkalmazás, amely egy webes API-t hív meg a démon nevében

A hosszan futó folyamatokkal rendelkező vagy felhasználói interakció nélkül működő alkalmazásokhoz is szükség van a biztonságos webes API-k elérésére. Egy ilyen alkalmazás az alkalmazás identitásával hitelesítheti és lekérheti a jogkivonatokat. Az alkalmazás egy ügyfél-titkos vagy egy tanúsítvány használatával igazolja identitását.

A MSAL **ConfidentialClientApplication** osztály [ügyfél-hitelesítő adatainak](https://aka.ms/msal-net-client-credentials) beszerzési módszereivel olyan démoni alkalmazásokat írhat, amelyek jogkivonatot szereznek a hívó alkalmazás számára. Ezek a módszerek megkövetelik, hogy a hívó alkalmazás titkos kulcsot tartalmazzon az Azure AD-ben. Az alkalmazás ezután megosztja a titkot a nevezett démonsal. Ilyen titkok például az alkalmazás jelszavai, a tanúsítvány-érvényesítés vagy az ügyfél-érvényesítés.

![Más alkalmazások és API-k által hívott Daemon-alkalmazás](media/scenarios/daemon-app.svg)

További információ: a [webes API-kat meghívó alkalmazás](scenario-daemon-overview.md).

## <a name="scenarios-and-supported-authentication-flows"></a>Forgatókönyvek és támogatott hitelesítési folyamatok

A jogkivonatok beszerzését érintő forgatókönyvek a OAuth 2,0 hitelesítési folyamatokra is leképezhetők. További információ: [OAuth 2,0 és OpenID Connect protokollok a Microsoft Identity platformon](active-directory-v2-protocols.md).

<table>
 <thead>
  <tr><th>Forgatókönyv</th> <th>Részletes forgatókönyv – útmutató</th> <th>OAuth 2,0 flow és Grant</th> <th>Célközönség</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">Egyoldalas alkalmazás</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">Implicit</a></td>
   <td>Munkahelyi vagy iskolai fiókok, személyes fiókok és Microsoft Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web app that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">Felhasználókba bejelentkező webalkalmazás</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Engedélyezési kód</a></td>
   <td>Munkahelyi vagy iskolai fiókok, személyes fiókok és Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web app that signs in users" src="media/scenarios/web-app.svg"></a></td>
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
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
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
   <td><a href="scenario-daemon-overview.md">Webes API-kat meghívó Daemon-alkalmazás</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">Ügyfél-hitelesítő adatok</a></td>
   <td>Csak az Azure AD-szervezeteknél használatos és kizárólag az alkalmazáson belüli engedélyek</td>
 </tr>

  <tr>
   <td><a href="scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href="scenario-web-api-call-api-overview.md">Webes API-kat meghívó webes API</a></td>
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

Az alkalmazások létrehozásához különböző nyelveket is használhat.

> [!NOTE]
> Egyes alkalmazások típusai nem érhetők el minden platformon.

A következő táblázat Windows oszlopában a .NET-keretrendszer minden alkalommal megemlíti a .NET-keretrendszert is. Az utóbbi elhagyja a tábla zsúfoltságának elkerülését.

|Forgatókönyv  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [Egyoldalas alkalmazás](scenario-spa-overview.md) <br/>[![Egyoldalas alkalmazás](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Felhasználót bejelentkeztető webalkalmazás](scenario-web-app-sign-user-overview.md) <br/>[![Webes alkalmazás, amely bejelentkezik a felhasználók számára](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET-mag](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET-mag | ![ASP.NET-mag](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET-mag | ![ASP.NET-mag](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET-mag
| [Webes API-kat hívó webalkalmazás](scenario-web-app-call-api-overview.md) <br/> <br/>[![Webes API-kat hívó webalkalmazás](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET-mag](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Lombik + MSAL Python| ![ASP.NET-mag](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Lombik + MSAL Python| ![ASP.NET-mag](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Lombik + MSAL Python
| [Webes API-kat hívó asztali alkalmazás](scenario-desktop-overview.md) <br/> <br/>A webes API ![-k eszköz kódját [meghívó asztali alkalmazás ![](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md)](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python <br/> ![iOS/Objective C vagy SWIFT](media/sample-v2-code/small_logo_iOS.png) MSAL. ObjC |
| [Webes API-kat hívó mobilalkalmazás](scenario-mobile-overview.md) <br/> [![Webes API-kat hívó mobilalkalmazás](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS/Objective C vagy SWIFT](media/sample-v2-code/small_logo_iOS.png) MSAL. ObjC | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL. Android
| [Démonalkalmazások](scenario-daemon-overview.md) <br/> [![Démonalkalmazások](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python
| [Webes API-kat hívó webes API](scenario-web-api-call-api-overview.md) <br/><br/> [![Webes API-kat hívó webes API](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET-mag](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python

További információ: Microsoft által [támogatott könyvtárak operációs rendszer/nyelv alapján](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language).

## <a name="next-steps"></a>További lépések

* További információ a [hitelesítés alapjairól](authentication-scenarios.md) és a [Microsoft Identity platform hozzáférési jogkivonatáról](access-tokens.md).
* További információ a [IoT-alkalmazásokhoz való hozzáférés biztonságossá tételéről](/azure/architecture/example-scenario/iot-aad/iot-aad).
