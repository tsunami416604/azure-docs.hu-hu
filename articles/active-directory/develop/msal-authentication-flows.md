---
title: MSAL-hitelesítési folyamatok | Azure
titleSuffix: Microsoft identity platform
description: Ismerkedjen meg a Microsoft Authentication Library (MSAL) által használt hitelesítési folyamatokkal és támogatásokkal.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/30/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: ace636152f6a0c9bf3896860eb17cc291bef2887
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2020
ms.locfileid: "77085127"
---
# <a name="authentication-flows"></a>Hitelesítési folyamatok

Ez a cikk a Microsoft Authentication Library (MSAL) által biztosított különböző hitelesítési folyamatokat ismerteti.  Ezek a folyamatok különféle alkalmazási forgatókönyvekben használhatók.

| Folyamat | Leírás | Használatban|  
| ---- | ----------- | ------- | 
| [Interaktív](#interactive) | Lekérdezi a jogkivonatot egy interaktív folyamaton keresztül, amely egy böngészőben vagy előugró ablakban kéri a felhasználótól a hitelesítő adatokat. | [Asztali alkalmazások](scenario-desktop-overview.md), [Mobile apps](scenario-mobile-overview.md) |
| [Implicit támogatás](#implicit-grant) | Lehetővé teszi, hogy az alkalmazás jogkivonatokat kapjon a háttér-kiszolgáló hitelesítő adatainak cseréje nélkül. Ez lehetővé teszi az alkalmazás számára, hogy bejelentkezzen a felhasználóba, karbantartsa a munkamenetet, és más webes API-k számára is lekérje a jogkivonatokat az ügyfél JavaScript-kódjában.| [Egyoldalas alkalmazások (SPA)](scenario-spa-overview.md) |
| [Engedélyezési kód](#authorization-code) | Az eszközre telepített alkalmazásokban használatos a védett erőforrásokhoz, például a webes API-khoz való hozzáféréshez. Ez lehetővé teszi a bejelentkezést és API-hozzáférést a mobil-és asztali alkalmazásokhoz. | [Asztali alkalmazások](scenario-desktop-overview.md), [Mobile apps](scenario-mobile-overview.md), [Web Apps](scenario-web-app-call-api-overview.md) | 
| [Nevében](#on-behalf-of) | Egy alkalmazás meghívja a szolgáltatást vagy a webes API-t, amely viszont egy másik szolgáltatást vagy webes API-t kell meghívnia. Az a cél, hogy a delegált felhasználói identitást és engedélyeket a kérési láncon keresztül propagálja. | [Webes API-k](scenario-web-api-call-api-overview.md) |
| [Ügyfél-hitelesítő adatok](#client-credentials) | Lehetővé teszi, hogy egy alkalmazás identitásának használatával hozzáférjen a webkiszolgálón lévő erőforrásokhoz. Általában olyan kiszolgálók közötti interakciók esetében használatos, amelyeket a háttérben kell futtatni, anélkül, hogy a felhasználóval való azonnali interakcióra lenne szükség. | [Daemon-alkalmazások](scenario-daemon-overview.md) |
| [Eszköz kódja](#device-code) | Lehetővé teszi a felhasználók számára, hogy bejelentkezzenek a bemeneti korlátozás alá tartozó eszközökre, például egy intelligens TV-re, egy IoT-eszközre vagy egy nyomtatóra. | [Asztali/mobil alkalmazások](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [Integrált Windows-hitelesítés](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Lehetővé teszi, hogy a tartományon vagy Azure Active Directoryon (Azure AD-ben) lévő alkalmazások a tokenek csendes beszerzéséhez (felhasználói FELÜLETi interakció nélkül) beszerezzék a jogkivonatot.| [Asztali/mobil alkalmazások](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Felhasználónév/jelszó](scenario-desktop-acquire-token.md#username-and-password) | Lehetővé teszi, hogy az alkalmazás közvetlenül a jelszavuk kezelését követően jelentkezzen be a felhasználóba. Ez a folyamat nem ajánlott. | [Asztali/mobil alkalmazások](scenario-desktop-acquire-token.md#username-and-password) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>Az egyes folyamatok tokeneket és kódokat bocsátanak ki
 
Az ügyfél felépítésének módjától függően a Microsoft Identity platform által támogatott hitelesítési folyamatok közül egy (vagy több) is használható.  Ezek a folyamatok különféle tokeneket (id_tokens, frissítési tokeneket, hozzáférési tokeneket) és engedélyezési kódokat hozhatnak létre, és különböző jogkivonatokat igényelnek a működésük érdekében. Ez a diagram áttekintést nyújt:
 
|Folyamat | Igényel | id_token | hozzáférési jogkivonat | jogkivonat frissítése | engedélyezési kód | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Engedélyezési kód folyamatábrája](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[Implicit folyamat](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hibrid OIDC folyamat](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Jogkivonat-beváltások frissítése](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | jogkivonat frissítése | x | x | x| |
|[Meghatalmazásos folyamat](v2-oauth2-on-behalf-of-flow.md) | hozzáférési jogkivonat| x| x| x| |
|[Eszköz kódjának folyamata](v2-oauth2-device-code.md) | | x| x| x| |
|[Ügyfél-hitelesítő adatok](v2-oauth2-client-creds-grant-flow.md) | | | x (csak alkalmazás)| | |
 
Az implicit módban kiállított tokenek hossza korlátozott, mert az URL-címen át kell adni a böngészőnek (ahol a `response_mode` `query` vagy `fragment`).  Egyes böngészőkben korlátozva van a böngészőablakban elhelyezhető URL-cím mérete, és a művelet nem hajtható végre, ha túl hosszú.  Így ezek a jogkivonatok nem rendelkeznek `groups` vagy `wids` jogcímekkel.

## <a name="interactive"></a>Interaktív

A MSAL támogatja a bejelentkezéshez szükséges hitelesítő adatok megadását és a tokenek beszerzését a hitelesítő adatok használatával.

![Interaktív folyamat diagramja](media/msal-authentication-flows/interactive.png)

További információ a MSAL.NET és a jogkivonatok interaktív beszerzéséről adott platformokon:
- [Xamarin Android rendszerhez](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [Univerzális Windows-platform](msal-net-uwp-considerations.md)

A MSAL. js-ben található interaktív hívásokkal kapcsolatos további információkért lásd: [MSAL. js interaktív kérések esetén a prompt viselkedése](msal-js-prompt-behavior.md).

## <a name="implicit-grant"></a>Implicit támogatás

A MSAL támogatja a [OAuth 2 implicit engedélyezési folyamatot](v2-oauth2-implicit-grant-flow.md), amely lehetővé teszi, hogy az alkalmazás jogkivonatokat szerezzen be a Microsoft Identity platformról a háttér-kiszolgáló hitelesítő adatainak cseréje nélkül. Ez lehetővé teszi az alkalmazás számára, hogy bejelentkezzen a felhasználóba, karbantartsa a munkamenetet, és más webes API-k számára is lekérje a jogkivonatokat az ügyfél JavaScript-kódjában.

![Implicit engedélyezési folyamat ábrája](media/msal-authentication-flows/implicit-grant.svg)

Számos modern webalkalmazást ügyféloldali, egyoldalas alkalmazásként, JavaScript vagy SPA-keretrendszer, például a szögletes, a Vue. js és a reakciós. js használatával írt be. Ezek az alkalmazások webböngészőben futnak, és különböző hitelesítési jellemzőkkel rendelkeznek, mint a hagyományos kiszolgálóoldali webes alkalmazások. A Microsoft Identity platform lehetővé teszi, hogy az egyoldalas alkalmazások bejelentkezzenek a felhasználókba, és jogkivonatokat kérjenek a háttér-szolgáltatások vagy webes API-k elérésére az implicit engedélyezési folyamat használatával. Az implicit folyamat lehetővé teszi, hogy az alkalmazás azonosító jogkivonatokat kapjon a hitelesített felhasználó képviseletére, valamint a védett API-k meghívásához szükséges jogkivonatok elérésére is.

Ez a hitelesítési folyamat nem tartalmaz olyan alkalmazási helyzeteket, amelyek platformfüggetlen JavaScript-keretrendszereket használnak, például az Electron-et és a reakciós Natívt, mivel további képességeket igényelnek a natív platformokkal való interakcióhoz.

## <a name="authorization-code"></a>Engedélyezési kód

A MSAL támogatja a [OAuth 2 engedélyezési kód engedélyezését](v2-oauth2-auth-code-flow.md). Ez a támogatás olyan alkalmazásokban használható, amelyek a védett erőforrásokhoz, például a webes API-khoz való hozzáféréshez vannak telepítve az eszközön. Ez lehetővé teszi a bejelentkezést és API-hozzáférést a mobil-és asztali alkalmazásokhoz. 

Amikor a felhasználók bejelentkeznek a webalkalmazásba (webhelyekre), a webalkalmazás megkapja az engedélyezési kódot.  Az engedélyezési kód a webes API-k hívására szolgáló token beszerzéséhez lett beváltva. A ASP.NET és ASP.NET Core webalkalmazásokban a `AcquireTokenByAuthorizationCode` egyetlen célja, hogy adjon hozzá egy tokent a jogkivonat-gyorsítótárhoz. Ezt követően az alkalmazás használhatja a jogkivonatot (általában a vezérlőkben, amely csak az API-k tokenjét kapja meg `AcquireTokenSilent`használatával).

![Az engedélyezési kód folyamatának ábrája](media/msal-authentication-flows/authorization-code.png)

Az előző ábrán az alkalmazás:

1. Egy hozzáférési jogkivonat számára beváltott engedélyezési kódot kér.
2. A hozzáférési token használatával hívja meg a webes API-t.

### <a name="considerations"></a>Megfontolások

- A jogkivonat beváltásához csak egyszer használhatja az engedélyezési kódot. Ne próbálja meg többször bekérni a jogkivonatot ugyanazzal az engedélyezési kóddal (a protokoll szabványos specifikációja explicit módon tiltja). Ha a kódot többször is beváltja, vagy ha nem tudja, hogy egy keretrendszer is elvégzi az Ön számára, a következő hibaüzenet jelenik meg: `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Ha ASP.NET vagy ASP.NET Core alkalmazást ír, akkor ez akkor fordulhat elő, ha nem adja meg azt a keretrendszert, amelyet már beváltotta az engedélyezési kódot. Ehhez meg kell hívnia a `AuthorizationCodeReceived` eseménykezelő `context.HandleCodeRedemption()` metódusát.

- Ne ossza meg a hozzáférési tokent a ASP.NET-mel, ami megakadályozhatja a növekményes hozzáférés megfelelő működését. További információ: [#693 kiadása](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693).

## <a name="on-behalf-of"></a>Nevében

A MSAL támogatja a [2. OAuth a hitelesítési folyamathoz](v2-oauth2-on-behalf-of-flow.md).  Ezt a folyamatot akkor kell használni, ha egy alkalmazás egy szolgáltatást vagy webes API-t hív meg, ami viszont egy másik szolgáltatást vagy webes API-t igényel. Az a cél, hogy a delegált felhasználói identitást és engedélyeket a kérési láncon keresztül propagálja. Ahhoz, hogy a középső szintű szolgáltatás hitelesített kéréseket továbbítson az alárendelt szolgáltatásnak, a felhasználó nevében védenie kell egy hozzáférési jogkivonatot a Microsoft Identity platformon.

![A folyamaton belüli adatforgalom diagramja](media/msal-authentication-flows/on-behalf-of.png)

Az előző ábrán:

1. Az alkalmazás egy hozzáférési jogkivonatot vásárol a webes API-hoz.
2. Egy ügyfél (web, Desktop, mobil vagy egylapos alkalmazás) egy védett webes API-t hív meg, amely a hozzáférési tokent tulajdonosi jogkivonatként adja hozzá a HTTP-kérelem hitelesítési fejlécéhez. A webes API hitelesíti a felhasználót.
3. Amikor az ügyfél meghívja a webes API-t, a webes API egy másik jogkivonatot kér a felhasználó nevében.  
4. A védett webes API ezt a tokent használja az alárendelt webes API-k hívására a felhasználó nevében.  A webes API később más alsóbb rétegbeli API-k számára is igényelhet jogkivonatokat (de ugyanazon felhasználó nevében továbbra is).

## <a name="client-credentials"></a>Ügyfél-hitelesítő adatok

A MSAL támogatja a [OAuth 2 ügyfél-hitelesítő adatok folyamatát](v2-oauth2-client-creds-grant-flow.md). Ez a folyamat lehetővé teszi a web-alapú erőforrások elérését egy alkalmazás identitásának használatával. Ezt a típust általában olyan kiszolgálók közötti interakciók esetén használják, amelyeknek a háttérben kell futniuk, anélkül, hogy a felhasználóval való azonnali interakcióra lenne szükség. Ezeket az alkalmazásokat gyakran démonoknak vagy szolgáltatásfiókoknek nevezzük. 

Az ügyfél hitelesítő adatainak megadása lehetővé teszi a webszolgáltatások (bizalmas ügyfél) számára a saját hitelesítő adatainak használatát a felhasználó megszemélyesítése helyett a hitelesítéshez egy másik webszolgáltatás hívásakor. Ebben az esetben az ügyfél általában egy közepes szintű webszolgáltatás, egy démon-szolgáltatás vagy egy webhely. A Microsoft Identity platform lehetővé teszi, hogy a hívó szolgáltatás hitelesítő adatként használjon tanúsítványokat (közös titok helyett).

> [!NOTE]
> A bizalmas ügyfél folyamata nem érhető el a mobil platformokon (UWP, Xamarin. iOS és Xamarin. Android), mert ezek csak a nyilvános ügyfélalkalmazások támogatását támogatják. A nyilvános ügyfélalkalmazások nem tudják, hogyan igazolják az alkalmazás identitását az identitás-szolgáltatónak. Egy biztonságos kapcsolat a webalkalmazáson vagy a webes API-ban is elérhető, ha egy tanúsítványt telepít.

A MSAL.NET két típusú ügyfél-hitelesítő adatot támogat. Az ügyfél hitelesítő adatait regisztrálni kell az Azure AD-ben. A hitelesítő adatokat a rendszer a bizalmas ügyfélalkalmazás konstruktorai számára adja át a kódban.

### <a name="application-secrets"></a>Alkalmazás-titkok

![Bizalmas ügyfél jelszavas diagramja](media/msal-authentication-flows/confidential-client-password.png)

Az előző ábrán az alkalmazás:

1. Jogkivonat beszerzése az alkalmazás titkos kódjának vagy a jelszó hitelesítő adatainak használatával.
2. A jogkivonat használatával teszi elérhetővé az erőforrás kéréseit.

### <a name="certificates"></a>Tanúsítványok

![A bizalmas ügyfél tanúsítványának ábrája](media/msal-authentication-flows/confidential-client-certificate.png)

Az előző ábrán az alkalmazás:

1. Jogkivonat beszerzése a tanúsítvány hitelesítő adatainak használatával.
2. A jogkivonat használatával teszi elérhetővé az erőforrás kéréseit.

Ezeknek az ügyfél-hitelesítő adatoknak a következőkre van szükségük:
- Regisztrálva van az Azure AD-ben.
- A kódot a bizalmas ügyfélalkalmazás felépítése során adták át.

## <a name="device-code"></a>Eszköz kódja

A MSAL támogatja a [2. OAuth](v2-oauth2-device-code.md), amely lehetővé teszi a felhasználók számára, hogy bejelentkezzenek a bevitt eszközökre, például egy intelligens TV-re, IoT-eszközre vagy nyomtatóra. Az Azure AD-vel való interaktív hitelesítéshez webböngésző szükséges. Az eszköz kódjának folyamata lehetővé teszi, hogy a felhasználó egy másik eszközt (például egy másik számítógépet vagy egy mobiltelefont) használjon az interaktív bejelentkezéshez, ahol az eszköz vagy az operációs rendszer nem biztosít webböngészőt.

Az eszköz kódjának használatával az alkalmazás egy kétlépéses folyamaton keresztül szerzi be a jogkivonatokat, kifejezetten ezekhez az eszközökhöz vagy operációs rendszerekhez. Ilyen alkalmazások például a IoT-eszközökön vagy parancssori eszközökön (CLI) futók. 

![Az eszköz kódjának folyamatábrája](media/msal-authentication-flows/device-code.png)

Az előző ábrán:

1. Ha felhasználói hitelesítésre van szükség, az alkalmazás egy kódot biztosít, és arra kéri a felhasználót, hogy használjon egy másik eszközt (például egy internetkapcsolattal rendelkező okostelefont) az URL-címre való ugráshoz (például https://microsoft.com/devicelogin). A rendszer ezután megkéri a felhasználót a kód megadására, és a normál hitelesítési felülettel folytatja, beleértve a hozzájárulási kéréseket és a többtényezős hitelesítést, ha szükséges.

2. A sikeres hitelesítés után a parancssori alkalmazás egy visszaadott csatornán keresztül fogadja a szükséges jogkivonatokat, és ezeket használja a szükséges webes API-hívások végrehajtásához.

### <a name="constraints"></a>Korlátozások

- Az eszköz kódjának folyamata csak a nyilvános ügyfélalkalmazások esetében érhető el.
- A nyilvános ügyfélalkalmazás összeállításakor átadott hatóságnak a következők egyikének kell lennie:
  - Bérlő (az űrlap `https://login.microsoftonline.com/{tenant}/`, ahol a `{tenant}` vagy a bérlő AZONOSÍTÓját vagy a bérlőhöz társított tartományt jelképező GUID).
  - Bármilyen munkahelyi és iskolai fiókhoz (`https://login.microsoftonline.com/organizations/`).
- Az Azure AD v 2.0-végpontja még nem támogatja a Microsoft személyes fiókjait (nem használhatja a `/common` vagy `/consumers` bérlőt).

## <a name="integrated-windows-authentication"></a>Integrált Windows-hitelesítés

A MSAL támogatja az integrált Windows-hitelesítést (IWA) olyan asztali vagy mobil alkalmazások esetében, amelyek tartományhoz csatlakoztatott vagy Azure AD-hez csatlakoztatott Windows-számítógépen futnak. Az IWA használatával ezek az alkalmazások csendesen tudják beszerezni a jogkivonatot (a felhasználó felhasználói FELÜLETének beavatkozása nélkül). 

![Integrált Windows-hitelesítés diagramja](media/msal-authentication-flows/integrated-windows-authentication.png)

Az előző ábrán az alkalmazás:

1. Jogkivonat beszerzése integrált Windows-hitelesítés használatával.
2. A jogkivonat használatával teszi elérhetővé az erőforrás kéréseit.

### <a name="constraints"></a>Korlátozások

A IWA csak az összevont felhasználókat támogatja, ami azt jelenti, hogy az Azure AD-ben létrehozott és Active Directory által támogatott felhasználók. A közvetlenül az Azure AD-ben létrehozott felhasználók Active Directory biztonsági mentés nélkül (felügyelt felhasználók) nem használhatják ezt a hitelesítési folyamatot. Ez a korlátozás nem befolyásolja a [Felhasználónév/jelszó folyamatát](#usernamepassword).

A IWA a .NET-keretrendszer, a .NET Core és a Univerzális Windows-platform platformokhoz írt alkalmazások esetében használható.

A IWA nem kerüli el a többtényezős hitelesítést. Ha a többtényezős hitelesítés konfigurálva van, a IWA sikertelen lehet, ha egy multi-Factor Authentication-kihívásra van szükség. A többtényezős hitelesítéshez felhasználói beavatkozás szükséges.

Nem szabályozhatja, hogy az identitás-szolgáltató Mikor kér kétfaktoros hitelesítést. A bérlői rendszergazda nem. A kétfaktoros hitelesítés általában akkor szükséges, ha egy másik országból jelentkezik be, ha nem VPN-kapcsolaton keresztül kapcsolódik a vállalati hálózathoz, és néha még akkor is, ha VPN-en keresztül csatlakozik. Az Azure AD a mesterséges intelligencia használatával folyamatosan megtanulja, hogy kétfaktoros hitelesítésre van-e szükség. Ha a IWA sikertelen, akkor vissza kell térnie egy [interaktív felhasználói üzenetre] (#interactive).

A nyilvános ügyfélalkalmazás összeállításakor átadott hatóságnak a következők egyikének kell lennie:
- Bérlő (az űrlap `https://login.microsoftonline.com/{tenant}/`, ahol a `tenant` vagy a bérlő AZONOSÍTÓját vagy a bérlőhöz társított tartományt jelképező GUID).
- Bármilyen munkahelyi és iskolai fiókhoz (`https://login.microsoftonline.com/organizations/`). A Microsoft személyes fiókjai nem támogatottak (`/common` vagy `/consumers` bérlők nem használhatók).

Mivel a IWA egy csendes folyamat, a következők egyikének igaznak kell lennie:
- Az alkalmazás felhasználójának előzőleg el kell juttatnia az alkalmazás használatát. 
- A bérlői rendszergazdának előzőleg el kell juttatnia a bérlő összes felhasználója számára az alkalmazás használatát.

Ez azt jelenti, hogy az alábbiak egyike igaz:
- A fejlesztőknek Ön **is kiválasztotta a Azure Portal** saját magának.
- A bérlői rendszergazda kiválasztotta az alkalmazás regisztrációjának **API-engedélyek** lapján a **{bérlői tartomány} rendszergazdai jóváhagyásának engedélyezése/visszavonása** lehetőséget (lásd: a [webes API-k eléréséhez szükséges engedélyek hozzáadása](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)).
- Lehetővé teszi, hogy a felhasználók beleegyeznek az alkalmazásba (lásd: [egyéni felhasználói belefoglalási kérelem kérése](v2-permissions-and-consent.md#requesting-individual-user-consent)).
- Lehetővé teszi, hogy a bérlői rendszergazda beleegyezett az alkalmazásba (lásd: [rendszergazdai engedély](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)).

A IWA folyamat engedélyezve van a .NET Desktop, a .NET Core és a Windows Universal platform alkalmazásaihoz. A .NET Core-ban meg kell adnia a felhasználónevet a IWA számára, mivel a .NET Core nem tud felhasználóneveket beolvasni az operációs rendszerből.
  
További információ a beleegyező adatokról: [v 2.0 engedélyek és beleegyezik](v2-permissions-and-consent.md).

## <a name="usernamepassword"></a>Felhasználónév/jelszó

A MSAL támogatja a [OAuth 2 erőforrás-tulajdonos jelszavas hitelesítő adatait](v2-oauth-ropc.md), amely lehetővé teszi, hogy az alkalmazás közvetlenül a jelszavuk kezelésével jelentkezzen be a felhasználóba. Az asztali alkalmazásban a username/Password folyamat használatával csendesen lehet lekérni a jogkivonatot. Az alkalmazás használatakor nincs szükség felhasználói felületre.

![A Felhasználónév/jelszó folyamat ábrája](media/msal-authentication-flows/username-password.png)

Az előző ábrán az alkalmazás:

1. A rendszer a Felhasználónév és a jelszó megadásával szerzi be a jogkivonatot az identitás-szolgáltatónak.
2. Meghívja a webes API-t a token használatával.

> [!WARNING]
> Ez a folyamat nem ajánlott. Magas fokú megbízhatóságot és felhasználói expozíciót igényel.  Ezt a folyamatot csak akkor használja, ha más, biztonságosabb, a folyamatok nem használhatók. További információ: [Mi a megoldás a jelszavak egyre növekvő problémájára?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

A jogkivonatok Windows-tartományhoz csatlakoztatott gépeken való csendes beszerzésének előnyben részesített folyamata [integrált Windows-hitelesítés](#integrated-windows-authentication). Ellenkező esetben az [eszköz kódjának folyamatát](#device-code)is használhatja.

Bár ez bizonyos esetekben hasznos (DevOps forgatókönyvek), ha olyan interaktív helyzetekben szeretné használni a felhasználónevet és a jelszót, ahol a saját felhasználói felületét adja meg, próbálja meg elkerülni. Felhasználónév/jelszó használatával:
- A többtényezős hitelesítést igénylő felhasználók nem fognak tudni bejelentkezni (mivel nincs interakció).
- A felhasználók nem tudnak egyszeri bejelentkezést végezni.

### <a name="constraints"></a>Korlátozások

Az [integrált Windows-hitelesítési korlátozásokon](#integrated-windows-authentication)kívül a következő korlátozások is érvényesek:

- A Felhasználónév/jelszó folyamat nem kompatibilis a feltételes hozzáféréssel és a többtényezős hitelesítéssel. Ennek következményeként, ha az alkalmazás egy Azure AD-bérlőn fut, ahol a bérlői rendszergazda a többtényezős hitelesítést igényli, nem használhatja ezt a folyamatot. Számos szervezet ezt teszi.
- Csak munkahelyi és iskolai fiókok (nem Microsoft-fiókok) esetében működik.
- A folyamat elérhető a .NET Desktopban és a .NET Core-on, de nem Univerzális Windows-platformon.

### <a name="azure-ad-b2c-specifics"></a>Azure AD B2C sajátosságok

További információ a MSAL.NET és a Azure AD B2C használatáról: a [ROPC használata Azure ad B2C (MSAL.net)](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c).
