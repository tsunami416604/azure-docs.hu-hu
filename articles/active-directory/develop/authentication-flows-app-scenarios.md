---
title: A Microsoft identitásplatform-hitelesítése alkalmazásforgatókönyvek & folyik | Azure
description: Ismerje meg a Microsoft identitásplatform alkalmazásforgatókönyveit, beleértve az identitások hitelesítését, a jogkivonatok beolvasását és a védett API-k hívását.
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
ms.openlocfilehash: e73da5e7b440a216841fffd65ca2e1b95de7a609
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480929"
---
# <a name="authentication-flows-and-application-scenarios"></a>Hitelesítési folyamatok és alkalmazási forgatókönyvek

A Microsoft identity platform (v2.0) végpont támogatja a hitelesítést a különböző modern alkalmazásarchitektúrák. Az összes architektúra az [OAuth 2.0 és az OpenID Connect](active-directory-v2-protocols.md)szabványprotokollokon alapul.  A [Microsoft identity platform hitelesítési kódtárai](reference-v2-libraries.md)használatával az alkalmazások hitelesítik az identitásokat, és jogkivonatokat szereznek be a védett API-k eléréséhez.

Ez a cikk a hitelesítési folyamatokat és az alkalmazásforgatókönyveket ismerteti:

- [Alkalmazási forgatókönyvek és támogatott hitelesítési folyamatok](#scenarios-and-supported-authentication-flows).
- [Alkalmazási forgatókönyvek és támogatott platformok és nyelvek](#scenarios-and-supported-platforms-and-languages).

## <a name="application-categories"></a>Alkalmazáskategóriák

A tokenek többféle alkalmazásból szerezhetők be, többek között a következőkből:

- Webalkalmazások
- Mobilalkalmazások
- Asztali alkalmazások
- Webes API-k

Jogkivonatokat is beszerezhető az olyan eszközökön futó alkalmazások, amelyek nem rendelkeznek böngészővel, vagy az IoT-n futnak.

Az alkalmazások az alábbi lista szerint kategorizálhatók:

- [Védett erőforrások és ügyfélalkalmazások:](#protected-resources-vs-client-applications)Egyes forgatókönyvek az erőforrások, például a webalkalmazások vagy a webes API-k védelméről szólnak. Más forgatókönyvek egy biztonsági jogkivonat beolvasása egy védett webes API-hívásához.
- [Felhasználók kal vagy anélkül:](#with-users-or-without-users)Egyes esetekben egy bejelentkezett felhasználó, de mások, például a démon forgatókönyvek, nem jár a felhasználó.
- [Egyoldalas, nyilvános ügyfél- és bizalmas ügyfélalkalmazások:](#single-page-public-client-and-confidential-client-applications)Ezek a típusok az alkalmazások három nagy kategóriáját képezik. Mindegyik különböző könyvtárakkal és objektumokkal van használatos.
- [Bejelentkezési közönség:](v2-supported-account-types.md#certain-authentication-flows-dont-support-all-the-account-types)Az elérhető hitelesítési folyamatok a bejelentkezési közönségtől függően eltérőek lehetnek. Egyes folyamatok csak munkahelyi vagy iskolai fiókokesetén érhetők el. És néhány áll rendelkezésre mind a munkahelyi vagy iskolai fiókok és a személyes Microsoft-fiókok. Az engedélyezett közönség a hitelesítési folyamatoktól függ.
- [Támogatott OAuth 2.0-s folyamatok:](#scenarios-and-supported-authentication-flows)Hitelesítési folyamatok a jogkivonatokat kérő alkalmazásforgatókönyvek megvalósításához használatosak. Nincs egy-az-egyhez hozzárendelés az alkalmazásforgatókönyvek és a hitelesítési folyamatok között.
- [Támogatott platformok:](#scenarios-and-supported-platforms-and-languages)Nem minden alkalmazásforgatókönyv érhető el minden platformon.

### <a name="protected-resources-vs-client-applications"></a>Védett erőforrások és ügyfélalkalmazások

A hitelesítési forgatókönyvek két tevékenységet foglalnak magukban:

- **Biztonsági jogkivonatok beszerzése védett webes API-hoz**: Javasoljuk, hogy [a Microsoft által támogatott ügyfélkódtárakat](reference-v2-libraries.md#microsoft-supported-client-libraries) használja a jogkivonatok, különösen a Microsoft Authentication Library (MSAL) család beszerzéséhez.
- **Webes API vagy webalkalmazás védelme:** A webes API vagy webalkalmazás-erőforrás védelmének egyik kihívása a biztonsági jogkivonat érvényesítése. Egyes platformokon a Microsoft [köztes szoftverkönyvtárakat](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries)kínál.

### <a name="with-users-or-without-users"></a>Felhasználókkal vagy felhasználók nélkül

A legtöbb hitelesítési forgatókönyv ek beszerezni jogkivonatok nevében bejelentkezett felhasználók.

![A felhasználókkal rendelkező esetek](media/scenarios/scenarios-with-users.svg)

Azonban vannak olyan démon-alkalmazás forgatókönyvek is, amelyekben az alkalmazások saját nevükben szerzik be a jogkivonatokat felhasználó nélkül.

![Daemon-alkalmazásokkal kapcsolatos forgatókönyvek](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>Egyoldalas, nyilvános ügyfél- és bizalmas ügyfélalkalmazások

A biztonsági jogkivonatok többféle alkalmazásból is beszerezhetők. Ezeket az alkalmazásokat általában három kategóriába sorolják:

- **Egyoldalas alkalmazások:** Más néven SPA-k, ezek az alkalmazások olyan webalkalmazások, amelyekben a tokenek a böngészőben futó JavaScript- vagy TypeScript-alkalmazásokból szerezhetők be. Számos modern alkalmazás egyoldalas alkalmazás előtér-előtérrel rendelkezik, amely elsősorban JavaScript nyelven íródott. Az alkalmazás gyakran használ keretrendszert, például Angular, React vagy Vue. Az MSAL.js az egyetlen olyan Microsoft-hitelesítési tár, amely támogatja az egyoldalas alkalmazásokat.

- **Nyilvános ügyfélalkalmazások**: Ezek az alkalmazások mindig bejelentkeznek a felhasználókhoz:
  - Webes API-kat hívó asztali alkalmazások a bejelentkezett felhasználó nevében
  - Mobilalkalmazások
  - Böngészővel nem rendelkező eszközökön futó alkalmazások, például az iOT-n futó alkalmazások

  Ezeket az alkalmazásokat az MSAL [PublicClientApplication](/dotnet/api/microsoft.identity.client.publicclientapplication) osztály képviseli. További információ: [Public client and confidential client applications](msal-client-applications.md).

- **Bizalmas ügyfélalkalmazások:**
  - Webes API-t hívó webalkalmazások
  - Webes API-k hívása webes API-k
  - Démonalkalmazások, még akkor is, ha konzolszolgáltatásként, például Linux démonként vagy Windows-szolgáltatásként valósítják meg

  Az ilyen típusú alkalmazások a [ConfidentialClientApplication](/dotnet/api/microsoft.identity.client.confidentialclientapplication) osztályt használják. További információ: [Public client and confidential client applications](msal-client-applications.md).

## <a name="application-scenarios"></a>Alkalmazáshasználati helyzetek

A Microsoft identity platform végpontja támogatja a különböző alkalmazásarchitektúrák hitelesítését:

- Egyoldalas alkalmazások
- Webalkalmazások
- Webes API-k
- Mobilalkalmazások
- Natív alkalmazások
- Démonalkalmazások
- Kiszolgálóoldali alkalmazások

Az alkalmazások a különböző hitelesítési folyamatok segítségével jelentkezzen be a felhasználók és a jogkivonatok hívása védett API-k.

### <a name="a-single-page-application"></a>Egyoldalas alkalmazás

Számos modern webalkalmazás ügyféloldali egyoldalas alkalmazásként készült. Ezek az alkalmazások JavaScript vagy egyoldalas alkalmazáskeretrendszert használnak, például Az Angular, a Vue.js és a React.js. Ezek az alkalmazások webböngészőben futnak.

A hitelesítési jellemzőik eltérnek a hagyományos kiszolgálóoldali webalkalmazásoktól. A Microsoft identity platform használatával az egyoldalas alkalmazások bejelentkezhetnek a felhasználókba, és jogkivonatokat kaphatnak a háttérszolgáltatások vagy webes API-k eléréséhez.

![Egyoldalas alkalmazás](media/scenarios/spa-app.svg)

További információ: [Egyoldalas alkalmazások](scenario-spa-overview.md).

### <a name="a-web-app-that-is-signing-in-a-user"></a>Felhasználót bejelentkező webalkalmazás

![Felhasználóban bejelentkező webalkalmazás](media/scenarios/scenario-webapp-signs-in-users.svg)

Felhasználót bejelentkező webalkalmazás védelme:

- Ha a .NET-ben fejlődik, ASP.NET vagy ASP.NET Core-t használja az Open ID Connect köztes ASP.NET. Az erőforrások védelme magában foglalja a biztonsági jogkivonat érvényesítését, amelyet a [.NET-kódtár IdentityModel bővítményei végeznek,](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) és nem az MSAL-kódtárak.

- Ha a Node.js-ben fejlődik ki, a Passport.js-t használja.

További információt a [felhasználókba bejelentkező webalkalmazás című témakörben talál.](scenario-web-app-sign-user-overview.md)

### <a name="a-web-app-that-signs-in-a-user-and-calling-a-web-api-on-behalf-of-the-user"></a>Olyan webalkalmazás, amely aláírja a felhasználót, és a felhasználó nevében webes API-t hív meg

![Webes API-kat hívó webalkalmazás](media/scenarios/web-app.svg)

Webes API hívásához egy webalkalmazás ból egy felhasználó nevében, használja az MSAL **ConfidentialClientApplication** osztályt. Az engedélyezési kód folyamat, és tárolja a beszerzett jogkivonatokat a jogkivonat-gyorsítótárban. Szükség esetén az MSAL frissíti a jogkivonatokat, és a vezérlő csendben beszerzi a jogkivonatokat a gyorsítótárból.

További információt a [Webapp webes API-k hívása című](scenario-web-app-call-api-overview.md)témakörben talál.

### <a name="a-desktop-app-calling-a-web-api-on-behalf-of-a-signed-in-user"></a>Webes API-t hívó asztali alkalmazás egy bejelentkezett felhasználó nevében

Ahhoz, hogy egy asztali alkalmazás hívja meg a webes API-t, amely aláírja a felhasználók, használja az interaktív jogkivonat-beszerzési módszerek az MSAL **PublicClientApplication** osztály. Ezekkel az interaktív módszerekkel szabályozhatja a bejelentkezési felhasználói felület élményét. Az MSAL webböngészőt használ ehhez az interakcióhoz.

![Webes API-t hívó asztali alkalmazás](media/scenarios/desktop-app.svg)

Van egy másik lehetőség a Windows által üzemeltetett alkalmazások a Windows-tartományhoz vagy az Azure Active Directory (Azure AD) által csatlakozott számítógépeken. Ezek az alkalmazások az integrált [Windows-hitelesítés](https://aka.ms/msal-net-iwa)segítségével csendben szerezhetnek tokeneket.

A böngésző nélküli eszközön futó alkalmazások továbbra is meghívhatnak egy API-t a felhasználó nevében. A hitelesítéshez a felhasználónak be kell jelentkeznie egy másik, webböngészővel rendelkező eszközön. Ebben a forgatókönyvben az [Eszközkód-folyamatot kell használnia.](https://aka.ms/msal-net-device-code-flow)

![Eszközkód-folyamat](media/scenarios/device-code-flow-app.svg)

Bár nem javasoljuk, hogy használja, a [felhasználónév/jelszó folyamat](https://aka.ms/msal-net-up) elérhető a nyilvános ügyfélalkalmazásokban. Ez a folyamat továbbra is szükség van bizonyos forgatókönyvekben, például a DevOps.

De ennek az áramlásnak a használata korlátozza az alkalmazásokat. Például az alkalmazások nem tudnak bejelentkezni egy olyan felhasználóba, akinek többtényezős hitelesítést vagy feltételes hozzáférést kell használnia. Az alkalmazások is nem részesülnek az egyszeri bejelentkezés.

A felhasználónév/jelszó folyamattal való hitelesítés ellentétes a modern hitelesítés alapelveivel, és csak örökölt okokból biztosított.

Ha azt szeretné, hogy a tokengyorsítótár megmaradjana, szabja testre a [tokengyorsítótár szerializálását.](https://aka.ms/msal-net-token-cache-serialization) A [kettős tokengyorsítótár-szerializálás](https://aka.ms/msal-net-dual-cache-serialization)megvalósításával visszamenőlegesen kompatibilis és előrekompatibilis token-gyorsítótárakat használhat. Ezek a jogkivonatok támogatják a hitelesítési kódtárak előző generációit. Adott tárak közé tartozik az Azure AD authentication library for .NET (ADAL.NET) 3-as és 4-es verzió.

További információt a [webes API-kat meghívjaó Asztali alkalmazás](scenario-desktop-overview.md)című témakörben talál.

### <a name="a-mobile-app-calling-a-web-api-on-behalf-of-an-interactive-user"></a>Webes API-t hívó mobilalkalmazás interaktív felhasználó nevében

Az asztali alkalmazásokhoz hasonlóan egy mobilalkalmazás meghívja az MSAL **PublicClientApplication** osztály interaktív jogkivonat-beszerzési módszereit, hogy egy webes API-híváshoz jogkivonatot szerezzen be.

![Webes API-t hívó mobilalkalmazás](media/scenarios/mobile-app.svg)

Az MSAL iOS és az MSAL Android alapértelmezés szerint a rendszer böngészőjét használja. Azonban utasíthatja őket a beágyazott webnézet használatára. A mobilplatformtól függő sajátosságok: Univerzális Windows platform (UWP), iOS vagy Android.

Egyes forgatókönyvek, például azok, amelyek egy eszközazonosítóhoz vagy eszközregisztrációhoz kapcsolódó feltételes hozzáférést foglalnak magukban, megkövetelik a közvetítő telepítését az eszközre. A brókerek például a Microsoft Company Portal Android és Microsoft Authenticator Android és iOS rendszeren. MSAL most kölcsönhatásba brókerek. További információ: [A brókerek kihasználása Android és iOS rendszeren.](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS)

További információt a [webes API-kat meghívjaó mobilalkalmazás című témakörben talál.](scenario-mobile-overview.md)

> [!NOTE]
> Az MSAL.iOS, MSAL rendszert használó mobilalkalmazás. Android, vagy MSAL.NET xamarin lehet alkalmazásvédelmi szabályzatok vonatkoznak rá. A házirendek például megakadályozhatják, hogy a felhasználó védett szöveget másoljon. A mobilalkalmazást az Intune kezeli, és az Intune felügyelt alkalmazásként ismeri fel. További információt a [Microsoft Intune App SDK – áttekintés című témakörben talál.](https://docs.microsoft.com/intune/app-sdk)
>
> Az [Intune App SDK](https://docs.microsoft.com/intune/app-sdk-get-started) külön az MSAL-kódtáraktól, és önállóan kommunikál az Azure AD-vel.

### <a name="a-protected-web-api"></a>Védett webes API

A Microsoft identity platform végpont használatával biztonságos webes szolgáltatások, például az alkalmazás RESTful webes API-t. A védett webes API-t egy hozzáférési jogkivonat használatával hívják meg. A jogkivonat biztonságossá teszi az API adatait, és hitelesíti a bejövő kérelmeket. A webes API hívója hozzáfűz egy hozzáférési jogkivonatot egy HTTP-kérelem engedélyezési fejlécében.

Ha meg szeretné védeni a ASP.NET vagy ASP.NET Core Web API-t, ellenőriznie kell a hozzáférési jogkivonatot. Ehhez az ellenőrzéshez a ASP.NET JWT köztes szoftvert használja. Az ellenőrzést a [.NET-kódtár IdentityModel bővítményei](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) végzik, nem pedig MSAL.NET.

További információ: [Protected web API](scenario-protected-web-api-overview.md).

### <a name="a-web-api-calling-another-web-api-on-behalf-of-a-user"></a>Webes API, amely egy másik webes API-t hív meg egy felhasználó nevében

Ahhoz, hogy ASP.NET vagy ASP.NET Core védett webes API-t egy másik webes API-hívásához egy felhasználó nevében, az alkalmazásnak be kell szereznie egy jogkivonatot az alsóbb rétegbeli webes API-hoz. Token megszerzéséhez az alkalmazás meghívja a **ConfidentialClientApplication** osztály [AcquireTokenOnBehalfOf](https://aka.ms/msal-net-on-behalf-of) metódusát. Az ilyen hívások neve *szolgáltatás-szolgáltatás* hívások is. A webes API-k, amelyek más webes API-kat hívnak, egyéni gyorsítótár-szerializálást kell biztosítaniuk.

  ![Egy webes API hívása egy másik webes API-t](media/scenarios/web-api.svg)

További információt a [webes API-kat meghívjaó webes API című](scenario-web-api-call-api-overview.md)témakörben talál.

### <a name="a-daemon-app-calling-a-web-api-in-the-daemons-name"></a>Egy démonalkalmazás, amely webes API-t hív meg a démon nevében

Azoknak az alkalmazásoknak, amelyek hosszú ideig futó folyamatokkal rendelkeznek, vagy amelyek felhasználói beavatkozás nélkül működnek, szintén szükségük van a biztonságos webes API-k elérésére. Egy ilyen alkalmazás hitelesítheti magát, és az alkalmazás identitásának használatával jogkivonatokat kaphat. Az alkalmazás ügyféltitok vagy tanúsítvány használatával bizonyítja személyazonosságát.

Az MSAL **ConfidentialClientApplication** osztály [ügyfélhitelesítő adatok beszerzésének](https://aka.ms/msal-net-client-credentials) módszereivel olyan démonalkalmazásokat írhat, amelyek tokenbe szereznek a hívó alkalmazáshoz. Ezek a módszerek megkövetelik, hogy a hívó alkalmazás regisztrált egy titkos Azure AD.These methods require that the calling app has registered a secret with Azure AD. Az alkalmazás ezután megosztja a titkot az úgynevezett démonnal. Ilyen titkos kulcsok közé tartozik például az alkalmazás jelszavak, tanúsítvány-állítás vagy ügyfél-állítás.

![Más alkalmazások és API-k által hívott démonalkalmazás](media/scenarios/daemon-app.svg)

További információ: [Daemon alkalmazás, amely webes API-kat hív meg.](scenario-daemon-overview.md)

## <a name="scenarios-and-supported-authentication-flows"></a>Forgatókönyvek és támogatott hitelesítési folyamatok

A jogkivonatok beolvasásával járó forgatókönyvek is leképezhetők az OAuth 2.0 hitelesítési folyamatokra. További információt az [OAuth 2.0 és az OpenID Connect protokoll a Microsoft identity platformon című témakörben talál.](active-directory-v2-protocols.md)

<table>
 <thead>
  <tr><th>Forgatókönyv</th> <th>Részletes forgatókönyv-átjárás</th> <th>OAuth 2.0 áramlás és támogatás</th> <th>Célközönség</th></tr>
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
   <td><a href="scenario-web-app-sign-user-overview.md">A felhasználókban bejelentkező webalkalmazás</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Engedélyezési kód</a></td>
   <td>Munkahelyi vagy iskolai fiókok, személyes fiókok és Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web App that signs in users" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">Webes API-kat meghívjaó webalkalmazás</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Engedélyezési kód</a></td>
   <td>Munkahelyi vagy iskolai fiókok, személyes fiókok és Azure AD B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">Webes API-kat meghívjaó asztali alkalmazás</a></td>
   <td>Interaktív az <a href="v2-oauth2-auth-code-flow.md">engedélyezési kód</a> és a PKCE használatával</td>
   <td>Munkahelyi vagy iskolai fiókok, személyes fiókok és Azure AD B2C</td>
 </tr>

  <tr>
   <td>Integrált Windows hitelesítés</td>
   <td>Munkahelyi vagy iskolai fiókok</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Erőforrás-tulajdonosi jelszó</a></td>
   <td>Munkahelyi vagy iskolai fiókok és Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">Eszköz kódja</a></td>
   <td>Munkahelyi vagy iskolai fiókok</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">Webes API-kat meghívjaó mobilalkalmazás</a></td>
   <td>Interaktív az <a href="v2-oauth2-auth-code-flow.md">engedélyezési kód</a> és a PKCE használatával</td>
   <td>Munkahelyi vagy iskolai fiókok, személyes fiókok és Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Erőforrás-tulajdonosi jelszó</a></td>
   <td>Munkahelyi vagy iskolai fiókok és Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-daemon-overview.md"><img alt="Daemon app that calls web APIs" src="media/scenarios/daemon-app.svg"></a></td>
   <td><a href="scenario-daemon-overview.md">Webes API-kat meghívjaó démonalkalmazás</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">Ügyfél-hitelesítő adatok</a></td>
   <td>Csak alkalmazásengedélyek felhasználó nélkül, és csak az Azure AD-szervezetekben használatosak</td>
 </tr>

  <tr>
   <td><a href="scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href="scenario-web-api-call-api-overview.md">Webes API-kat meghívó webes API</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">nevében</a></td>
   <td>Munkahelyi vagy iskolai fiókok és személyes fiókok</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>Forgatókönyvek és támogatott platformok és nyelvek

A Microsoft hitelesítést árak több platformot is támogatnak:

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

Különböző nyelveket is használhat az alkalmazások létrehozásához.

> [!NOTE]
> Egyes alkalmazástípusok nem érhetők el minden platformon.

A következő táblázat Windows oszlopában minden alkalommal, amikor a .NET Core-t említik, a . Az utóbbi kimarad, hogy elkerüljék a túlzsúfoltság az asztalra.

|Forgatókönyv  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [Egyoldalas alkalmazás](scenario-spa-overview.md) <br/>[![Egyoldalas alkalmazás](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [A felhasználókban bejelentkező webalkalmazás](scenario-web-app-sign-user-overview.md) <br/>[![A bejelentkező felhasználókat bejelentkező webalkalmazás](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET-mag](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET-mag | ![ASP.NET-mag](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET-mag | ![ASP.NET-mag](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET-mag
| [Webes API-kat meghívjaó webalkalmazás](scenario-web-app-call-api-overview.md) <br/> <br/>[![Webes API-kat meghívjaó webalkalmazás](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET-mag](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Lombik + MSAL Python| ![ASP.NET-mag](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Lombik + MSAL Python| ![ASP.NET-mag](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Lombik + MSAL Python
| [Webes API-kat hívó asztali alkalmazás](scenario-desktop-overview.md) <br/> <br/>[Webes API-kat meghívjaó asztali alkalmazás eszközkód-folyamat ![](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) ![](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python <br/> ![iOS / C célkitűzés vagy gyors](media/sample-v2-code/small_logo_iOS.png) MSAL.objc |
| [Webes API-kat hívó mobilalkalmazás](scenario-mobile-overview.md) <br/> [![Webes API-kat hívó mobilalkalmazás](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS / C célkitűzés vagy gyors](media/sample-v2-code/small_logo_iOS.png) MSAL.objc | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL. Android
| [Démonalkalmazások](scenario-daemon-overview.md) <br/> [![Démonalkalmazások](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python
| [Webes API-kat hívó webes API](scenario-web-api-call-api-overview.md) <br/><br/> [![Webes API, amely webes API-kat hív meg](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET-mag](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python

További információt a [Microsoft által támogatott operációs rendszer/nyelv könyvtárak című témakörben talál.](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language)

## <a name="next-steps"></a>További lépések

* További információ a [hitelesítési alapokról](authentication-scenarios.md) és a [Microsoft identitásplatform-hozzáférési jogkivonatokról.](access-tokens.md)
* További információ [az IoT-alkalmazásokhoz való hozzáférés biztosításáról.](/azure/architecture/example-scenario/iot-aad/iot-aad)
