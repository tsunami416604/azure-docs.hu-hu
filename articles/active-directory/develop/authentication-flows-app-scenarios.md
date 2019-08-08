---
title: Hitelesítési forgatókönyvek a Microsoft Identity platformhoz | Azure
description: Ismerje meg a hitelesítési folyamatokat és a Microsoft Identity platform alkalmazási forgatókönyveit. Ismerje meg az identitások hitelesítéséhez, a jogkivonatok beszerzéséhez és a védett API-k meghívásához használható különböző típusú alkalmazásokat.
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
ms.date: 07/25/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 946eb0343c2858e275e40b7a581fd48e1713accd
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68853516"
---
# <a name="authentication-flows-and-application-scenarios"></a>Hitelesítési folyamatok és alkalmazási helyzetek

A Microsoft Identity platform (v 2.0) végpontja támogatja a különböző modern alkalmazás-architektúrák hitelesítését, amelyek mindegyike az iparági szabványnak megfelelő protokollok, az [2,0-es vagy az OpenID Connect-OAuth](active-directory-v2-protocols.md)alapul.  A [hitelesítési kódtárak](reference-v2-libraries.md)használatával az alkalmazások hitelesítik az identitásokat, és jogkivonatokat kapnak a védett API-k eléréséhez. Ez a cikk a különböző hitelesítési folyamatokat és azok alkalmazási forgatókönyveit ismerteti, amelyekben használatban van.  Ez a cikk az [alkalmazások forgatókönyveit és a támogatott hitelesítési folyamatokat](#scenarios-and-supported-authentication-flows) , valamint az alkalmazás forgatókönyveit és a [támogatott platformokat és nyelveket](#scenarios-and-supported-platforms-and-languages)is tartalmazza.

## <a name="application-categories"></a>Alkalmazás-kategóriák

A jogkivonatok számos különböző típusú alkalmazásból szerezhetők be: Webalkalmazások, mobil-vagy asztali alkalmazások, webes API-k és az olyan eszközökön futó alkalmazások, amelyek nem rendelkeznek böngészővel (vagy iOT). Az alkalmazások a következő kategóriákba sorolhatók:

- [Védett erőforrások és ügyfélalkalmazások](#protected-resources-vs-client-applications). Bizonyos forgatókönyvek az erőforrások (Web Apps vagy webes API-k) védelmére, valamint a védett webes API-k meghívására szolgáló biztonsági jogkivonat beszerzésére vonatkoznak.
- [Felhasználók vagy felhasználók nélkül](#with-users-or-without-users). Bizonyos forgatókönyvek bejelentkezett felhasználót érintenek, míg a többi nem tartalmaz felhasználót (démoni forgatókönyvet).
- [Egyoldalas alkalmazások, nyilvános ügyfélalkalmazások és bizalmas ügyfélalkalmazások](#single-page-applications-public-client-applications-and-confidential-client-applications). Ezek az alkalmazások három nagy kategóriája. A könyvtárak és az objektumok kezeléséhez használt objektumok eltérőek lesznek.
- [Bejelentkezési közönség](v2-supported-account-types.md#certain-authentication-flows-dont-support-all-the-account-types). Néhány hitelesítési folyamat nem érhető el bizonyos bejelentkezési célközönségek számára. Egyes folyamatok csak munkahelyi vagy iskolai fiókokhoz érhetők el, és némelyikük munkahelyi vagy iskolai fiókokhoz, valamint személyes Microsoft-fiókokhoz is elérhető. Az engedélyezett célközönség a hitelesítési folyamattól függ.
- [Támogatott OAuth 2,0 folyamatok](#scenarios-and-supported-authentication-flows).  A hitelesítési folyamatok a jogkivonatokat kérő alkalmazási forgatókönyvek megvalósítására szolgálnak.  Az alkalmazási forgatókönyvek és a hitelesítési folyamatok között nincs egy az egyhez típusú hozzárendelés.
- [Támogatott platformok](#scenarios-and-supported-platforms-and-languages). Nem minden alkalmazás-forgatókönyv érhető el minden platformhoz.

### <a name="protected-resources-vs-client-applications"></a>Védett erőforrások és ügyfélalkalmazások

A hitelesítési forgatókönyvek két tevékenységet foglalnak magukban:

- **Biztonsági** jogkivonatok beszerzése egy védett webes API-hoz. A Microsoft azt javasolja, hogy [hitelesítési kódtárakat](reference-v2-libraries.md#microsoft-supported-client-libraries) használjon a jogkivonatok beszerzéséhez, különösen a Microsoft Authentication librarys család (MSAL) számára.
- **Webes API** -k védelme (vagy egy webalkalmazás). Egy erőforrás (webalkalmazás vagy webes API) védelmének egyik kihívása a biztonsági jogkivonat ellenőrzése. A Microsoft néhány platformon, a [köztes könyvtárakban](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries)is kínál.

### <a name="with-users-or-without-users"></a>Felhasználók vagy felhasználók nélkül

A legtöbb hitelesítési forgatókönyv egy (bejelentkezett) **felhasználó**nevében szerzi be a jogkivonatokat.

![felhasználói helyzetek](media/scenarios/scenarios-with-users.svg)

Vannak azonban olyan forgatókönyvek is (Daemon-alkalmazások), ahol az alkalmazások saját maguk nevében (felhasználó nélkül) szerzik be a jogkivonatokat.

![Daemon-alkalmazások](media/scenarios/daemon-app.svg)

### <a name="single-page-applications-public-client-applications-and-confidential-client-applications"></a>Egyoldalas alkalmazások, nyilvános ügyfélalkalmazások és bizalmas ügyfélalkalmazások

A biztonsági jogkivonatok számos különböző típusú alkalmazásból szerezhetők be. Az alkalmazások általában három kategóriába sorolhatók:

- **Egyoldalas alkalmazások** A (SPA) a webalkalmazások olyan formája, ahol a jogkivonatok a böngészőben futó alkalmazásból szerezhetők be (JavaScript vagy írógéppel írt). Számos modern alkalmazás rendelkezik egy egyoldalas alkalmazás előtérrel, amely elsősorban JavaScript nyelven íródott. Az alkalmazás gyakran egy szögletes, reagáló vagy Vue keretrendszer használatával íródott. A MSAL. js az egyetlen Microsoft-hitelesítési függvénytár, amely támogatja az egyoldalas alkalmazásokat.

- A **nyilvános ügyfélalkalmazások** mindig bejelentkeznek a felhasználókba. Ezek az alkalmazások a következők:
  - A bejelentkezett felhasználó nevében webes API-kat hívó asztali alkalmazások.
  - Mobile-alkalmazások.
  - Az alkalmazások harmadik kategóriája, amely olyan eszközökön fut, amelyeken nincs böngésző (böngésző nélkül futtatott alkalmazások) a iOT-on futó példányon.

  Ezeket a [PublicClientApplication](msal-client-applications.md)nevű MSAL osztály képviseli.

- **Bizalmas ügyfélalkalmazások**
  - Webes API-t hívó webalkalmazások
  - Webes API-kat hívó webes API-k
  - Daemon-alkalmazások (még akkor is, ha a konzolos szolgáltatásként, például a Linuxon futó démonként vagy Windows-szolgáltatásként implementálva)
 
  Az ilyen típusú alkalmazások a [ConfidentialClientApplication](msal-client-applications.md) használják

## <a name="application-scenarios"></a>Alkalmazáshasználati helyzetek

A Microsoft Identity platform végpont számos alkalmazás-architektúrához támogatja a hitelesítést: egylapos alkalmazások, webalkalmazások, webes API-k, mobil-és natív alkalmazások, valamint démonok és kiszolgálóoldali alkalmazások.  Az alkalmazások a különböző hitelesítési folyamatokat használják a felhasználói bejelentkezéshez és a jogkivonatok lekéréséhez a védett API-k meghívásához.

### <a name="single-page-application"></a>Egyoldalas alkalmazás

Számos modern webalkalmazás úgy van kialakítva, mint a JavaScript vagy egy SPA-keretrendszer, például a szögletes, a Vue. js és a reakciós. js segítségével írt ügyféloldali egyoldalas alkalmazások. Ezek az alkalmazások webböngészőben futnak, és különböző hitelesítési jellemzőkkel rendelkeznek, mint a hagyományos kiszolgálóoldali webes alkalmazások. A Microsoft Identity platform lehetővé teszi, hogy az egyoldalas alkalmazások bejelentkezzenek a felhasználókba, és jogkivonatokat kapjanak a háttér-szolgáltatások vagy webes API-k eléréséhez

![Egyoldalas alkalmazás](media/scenarios/spa-app.svg)

További információért olvassa el az [egylapos alkalmazások](scenario-spa-overview.md)című témakört.

### <a name="web-application-signing-in-a-user"></a>Webalkalmazás-aláírás – felhasználó

![Webalkalmazás-jelek a felhasználókban](media/scenarios/scenario-webapp-signs-in-users.svg)

Ha **egy** webalkalmazást szeretne védelemmel ellátni (a felhasználó bejelentkezésekor), a következőt fogja használni:

- A .NET világában a ASP.NET vagy ASP.NET Core a ASP.NET Open ID kapcsolat middleware-vel. A motorháztető alatt az erőforrások védelme magában foglalja a biztonsági jogkivonat érvényesítését, amelyet a [.net Library IdentityModel-bővítményei](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) hajtanak végre, nem MSAL a kódtárakat

- Ha a Node. js-ben fejleszt fejlesztést, a Passport. js fájlt fogja használni.

További információért olvassa el a következő webalkalmazást: [Sign-in Users](scenario-web-app-sign-user-overview.md).

### <a name="web-application-signing-in-a-user-and-calling-a-web-api-on-behalf-of-the-user"></a>Webalkalmazás-aláírás – felhasználó és webes API meghívása a felhasználó nevében

![Webalkalmazás-hívások webes API-k](media/scenarios/web-app.svg)

A webalkalmazásból a **webes API** meghívásához a felhasználó nevében használja a MSAL `ConfidentialClientApplication`. Az engedélyezési kód folyamatát fogja használni, a beszerzett tokent a jogkivonat-gyorsítótárban tárolja. Ezt követően a vezérlő a gyorsítótárból csendesen szerzi be a jogkivonatokat, amikor szükséges. Ha szükséges, a MSAL frissíti a tokent.

További információért olvassa el a [Web App calls web API](scenario-web-app-call-api-overview.md)-k című témakört.

### <a name="desktop-application-calling-a-web-api-on-behalf-of-the-signed-in-user"></a>A bejelentkezett felhasználó nevében webes API-t hívó asztali alkalmazás

Ha egy webes API-t szeretne meghívni egy olyan asztali alkalmazásból, amely a felhasználók számára jelentkezik be, használja a MSAL PublicClientApplication's Interactive token beszerzési módszereit. Ezek az interaktív módszerek lehetővé teszik a bejelentkezési felhasználói felületi élmény szabályozását. A kapcsolat engedélyezéséhez a MSAL kihasznál egy webböngészőt.

![Asztali](media/scenarios/desktop-app.svg)

A Windows-tartományhoz vagy HRE csatlakoztatott számítógépeken futó Windows rendszerű alkalmazásokhoz egy másik lehetőség is rendelkezésre áll. Ezek az alkalmazások az [integrált Windows-hitelesítés](https://aka.ms/msal-net-iwa)használatával csendes úton vásárolhatnak jogkivonatot.

A böngésző nélküli eszközön futó alkalmazások továbbra is meghívhatják az API-t egy felhasználó nevében. A hitelesítéshez a felhasználónak be kell jelentkeznie egy másik, webböngészővel rendelkező eszközre. A forgatókönyv engedélyezéséhez az [eszköz kódjának folyamatát](https://aka.ms/msal-net-device-code-flow) kell használnia.

![Eszközkód folyamata](media/scenarios/device-code-flow-app.svg)

Végül, bár nem ajánlott, használhatja a [felhasználónevet és a jelszót](https://aka.ms/msal-net-up) a nyilvános ügyfélalkalmazások számára. Erre a folyamatra továbbra is szükség van bizonyos helyzetekben (például DevOps), de ügyeljen arra, hogy a használatával korlátozásokat fog alkalmazni az alkalmazására. Előfordulhat például, hogy a folyamatot használó alkalmazások nem tudnak bejelentkezni a többtényezős hitelesítést (feltételes hozzáférés) végrehajtó felhasználó számára. Az alkalmazás nem teszi lehetővé az egyszeri bejelentkezés előnyeit. A felhasználónévvel/jelszóval történő hitelesítés a modern hitelesítés alapelvei alapján történik, és csak az örökölt okok miatt biztosítható.

Asztali alkalmazásokban, ha azt szeretné, hogy a jogkivonat-gyorsítótár állandó legyen, [testre kell szabnia a jogkivonat-gyorsítótár szerializálását](https://aka.ms/msal-net-token-cache-serialization). A [kettős jogkivonat-gyorsítótár szerializálásának](https://aka.ms/msal-net-dual-cache-serialization)megvalósításával engedélyezheti a visszafelé és a továbbítással kompatibilis jogkivonat-gyorsítótárak használatát a hitelesítési kódtárak korábbi generációi (ADAL.net 3. x és 4. x) használatával is.

További információkért olvassa el a [webes API-kat meghívó asztali alkalmazás](scenario-desktop-overview.md)olvasása című témakört.

### <a name="mobile-application-calling-a-web-api-on-behalf-of-the-user-whos-signed-in-interactively"></a>A mobil alkalmazás a webes API-t hívja interaktív módon bejelentkezett felhasználó nevében

Az asztali alkalmazásokhoz hasonlóan a mobileszközök a MSAL PublicClientApplication's Interactive token beszerzési módszereit használják a webes API-k meghívására szolgáló token beszerzéséhez.

![mobil](media/scenarios/mobile-app.svg)

Az iOS és a MSAL Android MSAL alapértelmezés szerint a rendszer webböngészőjét használja. Azt is megteheti, hogy a beágyazott webes nézetet is használhatja. A mobil platformtól függően vannak sajátosságok: (UWP, iOS, Android).

Bizonyos forgatókönyvek, amelyek az eszköz azonosítójával kapcsolatos feltételes hozzáférést, vagy egy beléptetett eszközt igényelnek, a [közvetítőt](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS) egy eszközre kell telepíteni. A brókerek példái a Microsoft vállalati portál (Androidon), Microsoft Authenticator (Android és iOS). A MSAL mostantól képes a brókerekkel való interakcióra.

> [!NOTE]
> A mobil alkalmazás (a MSAL. iOS és a MSAL használatával. Az Android-vagy MSAL.NET/Xamarin-alapú alkalmazások esetében az alkalmazás-védelmi szabályzatok alkalmazhatók (például megakadályozhatja, hogy a felhasználó egy bizonyos védett szöveget másoljon). Ezt az [Intune kezeli](https://docs.microsoft.com/intune/app-sdk) , és az Intune felügyelt alkalmazásként ismeri fel. Az [INTUNE SDK](https://docs.microsoft.com/intune/app-sdk-get-started) különálló a MSAL-könyvtáraktól, és saját HRE beszél.

További információkért olvassa el a [webes API-kat meghívó Mobile App](scenario-mobile-overview.md)című témakört.

### <a name="protected-web-api"></a>Védett webes API

A Microsoft Identity platform végpontján keresztül biztonságossá teheti a webszolgáltatásokat, például az alkalmazás REST-alapú webes API-ját. Egy védett webes API-t egy hozzáférési jogkivonattal kell meghívni az adatai biztonságossá tételéhez és a bejövő kérések hitelesítéséhez. Egy webes API hívója hozzáfűz egy hozzáférési jogkivonatot egy HTTP-kérelem engedélyezési fejlécében. Ha védelemmel szeretné ellátni a ASP.NET vagy ASP.NET Core webes API-t, érvényesíteni kell a hozzáférési jogkivonatot. Ehhez a ASP.NET JWT middleware-t fogja használni. A motorháztető alatt az érvényesítést a [.net Library IdentityModel bővítményei](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) végzik, nem MSAL.net

További információért olvassa el a [védett webes API](scenario-protected-web-api-overview.md)-t.

### <a name="web-api-calling-another-downstream-web-api-on-behalf-of-the-user-for-whom-it-was-called"></a>Webes API egy másik alsóbb rétegbeli webes API-t hív meg azon felhasználó nevében, akivel meghívták

Ha továbbá szeretné, hogy a ASP.NET vagy ASP.NET Core a védett webes API-t egy másik webes API meghívására a felhasználó nevében, az alkalmazásnak meg kell adnia egy jogkivonatot az alárendelt webes API-hoz a ConfidentialClientApplication metódusával, amely egy jogkivonatot szerez be a következő nevében: [ felhasználó](https://aka.ms/msal-net-on-behalf-of). Ez a szolgáltatás a szolgáltatások közötti hívásoknak is nevezett.
A többi webes API-t meghívó webes API-k egyéni gyorsítótár-szerializálást is meg kell adniuk

  ![Webes API](media/scenarios/web-api.svg)

További információért olvassa el a webes API- [kat meghívó webes API](scenario-web-api-call-api-overview.md)-t.

### <a name="desktopservice-or-web-daemon-application-calling-web-api-without-a-user-in-its-own-name"></a>Asztali/szolgáltatás vagy webes démon alkalmazás, amely felhasználó nélkül hívja meg a webes API-t (a saját nevében)

A hosszan futó folyamatokkal rendelkező vagy felhasználói interakció nélkül működő alkalmazásokhoz is szükség van a biztonságos webes API-k elérésére. Ezek az alkalmazások hitelesítik és lekérhetik a jogkivonatokat az alkalmazás identitásával, és nem a felhasználó delegált identitását. Az identitást az ügyfél titkos vagy tanúsítványa alapján bizonyítják.
A MSAL ConfidentialClientApplication's- [ügyfél hitelesítő adatainak](https://aka.ms/msal-net-client-credentials) beszerzési módszereit használva megírhatja az alkalmazások (Daemon-alkalmazás) tokenjét az alkalmazáshoz. Ezek azt feltételezik, hogy az alkalmazás korábban már regisztrált egy titkos kulcsot (az alkalmazás jelszava vagy a tanúsítvány vagy az ügyfél kijelentését) az Azure AD-vel, amelyet aztán megosztanak ezzel a hívással.

![Démonalkalmazások](media/scenarios/daemon-app.svg)

További információkért olvassa el a [webes API-kat meghívó Daemon-alkalmazás](scenario-daemon-overview.md)című témakört.

## <a name="scenarios-and-supported-authentication-flows"></a>Forgatókönyvek és támogatott hitelesítési folyamatok

A jogkivonatok beszerzését érintő forgatókönyvek a [Microsoft Identity platform protokolljaiban](active-directory-v2-protocols.md) ismertetett részletek a OAuth 2,0 hitelesítési folyamatokra is leképezhetők.

|Forgatókönyv | Részletes forgatókönyv – útmutató | OAuth 2,0 flow/támogatás | Célközönség |
|--|--|--|--|
| [![Egyoldalas alkalmazás](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | [Egyoldalas alkalmazás](scenario-spa-overview.md) | [Implicit](v2-oauth2-implicit-grant-flow.md) | Munkahelyi vagy iskolai fiókok és személyes fiókok, B2C
| [![Webes alkalmazás, amely bejelentkezik a felhasználók számára](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | [Felhasználókba bejelentkező webes alkalmazás](scenario-web-app-sign-user-overview.md) | [Engedélyezési kód](v2-oauth2-auth-code-flow.md) | Munkahelyi vagy iskolai fiókok és személyes fiókok, B2C |
| [![Webes API-kat meghívó webalkalmazás](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | [Webes API-kat meghívó webalkalmazás](scenario-web-app-call-api-overview.md) | [Engedélyezési kód](v2-oauth2-auth-code-flow.md) | Munkahelyi vagy iskolai fiókok és személyes fiókok, B2C |
| [![Webes API-kat meghívó asztali alkalmazás](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) | [Webes API-kat hívó asztali alkalmazás](scenario-desktop-overview.md)| Interaktív ([engedélyezési kód](v2-oauth2-auth-code-flow.md) PKCE-mel) | Munkahelyi vagy iskolai fiókok és személyes fiókok, B2C |
| | | Integrált Windows | Munkahelyi vagy iskolai fiókok |
| | | [Erőforrás-tulajdonos jelszava](v2-oauth-ropc.md)  | Munkahelyi vagy iskolai fiókok, B2C |
| ![Eszközkód folyamata](media/scenarios/device-code-flow-app.svg)| [Webes API-kat hívó asztali alkalmazás](scenario-desktop-overview.md) | [Eszköz kódja](v2-oauth2-device-code.md)  | Munkahelyi vagy iskolai fiókok * |
| [![Webes API-kat meghívó mobil alkalmazás](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | [Webes API-kat meghívó mobil alkalmazás](scenario-mobile-overview.md) | Interaktív ([engedélyezési kód](v2-oauth2-auth-code-flow.md) PKCE-mel)  |   Munkahelyi vagy iskolai fiókok és személyes fiókok, B2C
| | | Erőforrás-tulajdonos jelszava  | Munkahelyi vagy iskolai fiókok, B2C |
| [![Daemon-alkalmazás](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | [Daemon-alkalmazás](scenario-daemon-overview.md) | [Ügyfél-hitelesítő adatok](v2-oauth2-client-creds-grant-flow.md)  |   Az alkalmazás csak a HRE-szervezetekre vonatkozó engedélyeket (nincs felhasználó)
| [![Webes API-kat meghívó webes API](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | [Webes API-kat meghívó webes API](scenario-web-api-call-api-overview.md)| [A következő nevében](v2-oauth2-on-behalf-of-flow.md) | Munkahelyi vagy iskolai fiókok és személyes fiókok |

## <a name="scenarios-and-supported-platforms-and-languages"></a>Forgatókönyvek és támogatott platformok és nyelvek

Nem minden alkalmazás-típus érhető el minden platformon. Az alkalmazások létrehozásához különböző nyelveket is használhat. A Microsoft hitelesítési könyvtárai számos platformot támogatnak (JavaScript, .NET-keretrendszer, .net Core, Windows 10/UWP, Xamarin. iOS, Xamarin. Android, natív iOS, natív Android, Java, Python)

|Forgatókönyv  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [Egyoldalas alkalmazás](scenario-spa-overview.md) <br/>[![Egyoldalas alkalmazás](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/logo_js.png) MSAL.js
| [Felhasználókba bejelentkező webes alkalmazás](scenario-web-app-sign-user-overview.md) <br/>[![Webes alkalmazás, amely bejelentkezik a felhasználók számára](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET](media/sample-v2-code/logo_NET.png)</br> ASP.NET ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)ASP.NET Core
| [Webes API-kat meghívó webalkalmazás](scenario-web-app-call-api-overview.md) <br/> [![Webes API-kat meghívó webalkalmazás](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET](media/sample-v2-code/logo_NET.png) </br> ASP.NET + MSAL.NET </br> ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) Lombik + MSAL Python| ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) Lombik + MSAL Python| ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) Lombik + MSAL Python
| [Webes API-kat hívó asztali alkalmazás](scenario-desktop-overview.md) <br/> A webes API![-k eszköz kódját [meghívó asztali alkalmazás ![](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md)](media/scenarios/device-code-flow-app.svg) | ![MSAL.NET](media/sample-v2-code/logo_NET.png)  MSAL.NET ![.NET Core](media/sample-v2-code/logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) MSAL Python| ![.NET Core](media/sample-v2-code/logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) MSAL Python| ![.NET Core](media/sample-v2-code/logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) MSAL Python
| [Webes API-kat meghívó mobil alkalmazás](scenario-mobile-overview.md) <br/> [![Webes API-kat meghívó mobil alkalmazás](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/logo_xamarin.png) MSAL.NET | | | ![iOS/Objective C vagy SWIFT](media/sample-v2-code/logo_iOS.png) MSAL. iOS | ![Android](media/sample-v2-code/logo_Android.png) MSAL. Android
| [Daemon-alkalmazás](scenario-daemon-overview.md) <br/> [![Daemon-alkalmazás](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET](media/sample-v2-code/logo_NET.png) MSAL.NET ![.NET Core](media/sample-v2-code/logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) MSAL Python| ![.NET Core](media/sample-v2-code/logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) MSAL Python| ![.NET Core](media/sample-v2-code/logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) MSAL Python
| [Webes API-kat meghívó webes API](scenario-web-api-call-api-overview.md) <br/> [![Webes API-kat meghívó webes API](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![.NET](media/sample-v2-code/logo_NET.png) <br/> ASP.NET + MSAL.NET ![.NET Core](media/sample-v2-code/logo_NETcore.png) <br/> ASP.NET Core + MSAL.NET| ![.NET Core](media/sample-v2-code/logo_NETcore.png) <br/> ASP.NET Core + MSAL.NET| ![.NET Core](media/sample-v2-code/logo_NETcore.png)<br/> ASP.NET Core + MSAL.NET

Lásd még: [Microsoft által támogatott könyvtárak operációs rendszer/nyelv szerint](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language)

## <a name="next-steps"></a>További lépések
További információ a [hitelesítés alapjairól](authentication-scenarios.md) és a [hozzáférési](access-tokens.md)jogkivonatokról.
