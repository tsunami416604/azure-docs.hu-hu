---
title: MSAL hitelesítési folyamatok | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg a Microsoft hitelesítési könyvtár (MSAL) által használt hitelesítési folyamatokat és támogatásokat.
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
ms.openlocfilehash: 25c219bedbbbec9fbc0c5617c7bd9fc482faf49a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050510"
---
# <a name="authentication-flows"></a>Hitelesítési folyamatok

Ez a cikk a Microsoft Authentication Library (MSAL) által biztosított különböző hitelesítési folyamatokat ismerteti.  Ezek a folyamatok számos különböző alkalmazási forgatókönyvben használhatók.

| Folyamat | Leírás | Használt|  
| ---- | ----------- | ------- | 
| [Interaktív](#interactive) | Lekéri a jogkivonatot egy interaktív folyamat, amely kéri a felhasználó hitelesítő adatokat egy böngészőben vagy előugró ablakban keresztül. | [Asztali alkalmazások](scenario-desktop-overview.md), [mobilalkalmazások](scenario-mobile-overview.md) |
| [Implicit támogatás](#implicit-grant) | Lehetővé teszi, hogy az alkalmazás jogkivonatokat kapjon a háttérkiszolgáló hitelesítő adatok cseréjének végrehajtása nélkül. Ez lehetővé teszi, hogy az alkalmazás jelentkezzen be a felhasználó, a munkamenet karbantartása, és tokenek más webes API-k, mind az ügyfél JavaScript-kód.| [Egyoldalas alkalmazások (SPA)](scenario-spa-overview.md) |
| [Engedélyezési kód](#authorization-code) | Az eszközre telepített alkalmazásokban használható a védett erőforrások, például a webes API-k eléréséhez. Ez lehetővé teszi a bejelentkezési és API-hozzáférést a mobil- és asztali alkalmazásokhoz. | [Asztali alkalmazások,](scenario-desktop-overview.md) [mobilalkalmazások](scenario-mobile-overview.md), [webalkalmazások](scenario-web-app-call-api-overview.md) | 
| [Nevében](#on-behalf-of) | Egy alkalmazás meghívja a szolgáltatás vagy webes API-t, amely viszont meg kell hívnia egy másik szolgáltatás vagy webes API-t. Az ötlet a delegált felhasználói identitás és engedélyek propagálása a kérelemláncon keresztül. | [Webes API-k](scenario-web-api-call-api-overview.md) |
| [Ügyfél-hitelesítő adatok](#client-credentials) | Lehetővé teszi a webes üzemeltetésű erőforrások elérését egy alkalmazás identitásának használatával. Gyakran használják a kiszolgálók közötti interakciókhoz, amelyeknek a háttérben kell futniuk, a felhasználóval való azonnali beavatkozás nélkül. | [Démonalkalmazások](scenario-daemon-overview.md) |
| [Eszköz kódja](#device-code) | Lehetővé teszi a felhasználók számára, hogy bejelentkezve a bemeneti korlátozott eszközök, például egy smart TV, IoT-eszköz vagy nyomtató. | [Asztali/mobilalkalmazások](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [Integrált Windows-hitelesítés](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Lehetővé teszi, hogy a tartományban vagy az Azure Active Directoryban (Azure AD) csatlakozott számítógépeken lévő alkalmazások csendesen szerezzenek be egy jogkivonatot (a felhasználó felhasználói felületi beavatkozása nélkül).| [Asztali/mobilalkalmazások](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Felhasználónév/jelszó](scenario-desktop-acquire-token.md#username-and-password) | Lehetővé teszi, hogy egy alkalmazás közvetlenül kezelje a jelszavát. Ez a folyamat nem ajánlott. | [Asztali/mobilalkalmazások](scenario-desktop-acquire-token.md#username-and-password) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>Hogyan bocsát ki az egyes folyamatok jogkivonatokat és kódokat?
 
Attól függően, hogy az ügyfél épül, használhatja a Microsoft identitásplatform által támogatott hitelesítési folyamatok egy (vagy több) használatát.  Ezek a folyamatok különböző jogkivonatokat (id_tokens, frissítési jogkivonatokat, hozzáférési jogkivonatokat) és engedélyezési kódokat hozhatnak létre, és különböző jogkivonatokat igényelnek, hogy működjenek. Ez a diagram áttekintést nyújt:
 
|Folyamat | Megköveteli | id_token | hozzáférési jogkivonat | token frissítése | engedélyezési kód | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Engedélyezési kód folyamata](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[Implicit folyamat](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hibrid OIDC-folyamat](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Token beváltásának frissítése](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | token frissítése | x | x | x| |
|[Meghatalmazásos folyamat](v2-oauth2-on-behalf-of-flow.md) | hozzáférési jogkivonat| x| x| x| |
|[Eszközkód-áramlás](v2-oauth2-device-code.md) | | x| x| x| |
|[Ügyfél-hitelesítő adatok](v2-oauth2-client-creds-grant-flow.md) | | | x (csak alkalmazásoknál)| | |
 
Az implicit módban kibocsátott tokenek hosszkorlátozással rendelkeznek, mivel az URL-en keresztül visszakerülnek a böngészőbe (hol `response_mode` van `query` vagy `fragment`).  Egyes böngészőkben korlátozható az URL mérete, amelyet a böngészősávba lehet helyezni, és ha túl hosszú.  Így ezek a jogkivonatok nem rendelkeznek, `groups` vagy `wids` jogcímek.

## <a name="interactive"></a>Interaktív

Az MSAL támogatja, hogy interaktívmódon kérje a felhasználóhitelesítő adatait a bejelentkezéshez, és szerezzen be egy jogkivonatot ezekkel a hitelesítő adatokkal.

![Interaktív folyamat diagramja](media/msal-authentication-flows/interactive.png)

A MSAL.NET használatával kapcsolatos további információ az adott platformokon lévő tokenek interaktív megszerzéséhez:
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [Univerzális Windows-platform](msal-net-uwp-considerations.md)

Az MSAL.js alkalmazásban található interaktív hívásokról az [MSAL.js interaktív kérések gyors viselkedése](msal-js-prompt-behavior.md)című témakörben talál további információt.

## <a name="implicit-grant"></a>Implicit támogatás

Az MSAL támogatja az [OAuth 2 implicit támogatási folyamatot,](v2-oauth2-implicit-grant-flow.md)amely lehetővé teszi, hogy az alkalmazás a háttérkiszolgálóhitelesítő adatok cseréje nélkül szerezzen be jogkivonatokat a Microsoft identitásplatformról. Ez lehetővé teszi, hogy az alkalmazás jelentkezzen be a felhasználó, a munkamenet karbantartása, és tokenek más webes API-k, mind az ügyfél JavaScript-kód.

![Implicit támogatási folyamat diagramja](media/msal-authentication-flows/implicit-grant.svg)

Számos modern webes alkalmazás ügyféloldali, egyoldalas alkalmazásként készült, javascript vagy SPA keretrendszer használatával, például Angular, Vue.js és React.js néven. Ezek az alkalmazások webböngészőben futnak, és eltérő hitelesítési jellemzőkkel rendelkeznek, mint a hagyományos kiszolgálóoldali webalkalmazások. A Microsoft identity platform lehetővé teszi, hogy az egyoldalas alkalmazások jelentkezzen be a felhasználók, és get jogkivonatok eléréséhez háttérszolgáltatások vagy webes API-k, az implicit támogatási folyamat használatával. Az implicit folyamat lehetővé teszi, hogy az alkalmazás azonosító jogkivonatokat kapjon a hitelesített felhasználó nak, és a védett API-k hívásához szükséges jogkivonatokat is.

Ez a hitelesítési folyamat nem tartalmazza az alkalmazásforgatókönyveket, amelyek platformfüggetlen JavaScript-keretrendszereket használnak, például electron és React-Native, mert további képességeket igényelnek a natív platformokkal való interakcióhoz.

## <a name="authorization-code"></a>Engedélyezési kód

Az MSAL támogatja az [OAuth 2 engedélyezési kód megadását.](v2-oauth2-auth-code-flow.md) Ez a támogatás olyan alkalmazásokban használható, amelyek egy eszközre vannak telepítve a védett erőforrásokhoz, például a webes API-khoz való hozzáféréshez. Ez lehetővé teszi a bejelentkezési és API-hozzáférést a mobil- és asztali alkalmazásokhoz. 

Amikor a felhasználók bejelentkeznek a webes alkalmazásokba (webhelyekre), a webalkalmazás megkapja az engedélyezési kódot.  Az engedélyezési kód beváltja a web API-k hívásához szükséges jogkivonat beszerzéséhez. A ASP.NET és ASP.NET Core webalkalmazásokban az egyetlen cél `AcquireTokenByAuthorizationCode` az, hogy egy jogkivonatot adjon hozzá a jogkivonat-gyorsítótárhoz. A jogkivonatot ezután az alkalmazás használhatja (általában a vezérlőkben, amelyek `AcquireTokenSilent`csak egy jogkivonatot kapnak egy API-hoz a használatával).

![Engedélyezési kódfolyamat diagramja](media/msal-authentication-flows/authorization-code.png)

Az előző ábrán az alkalmazás:

1. Egy hozzáférési jogkivonatra beváltott engedélyezési kódot kér.
2. A hozzáférési jogkivonat ot használja egy webes API hívásához.

### <a name="considerations"></a>Megfontolandó szempontok

- Az engedélyezési kódot csak egyszer használhatja a jogkivonat beváltásához. Ne próbálja meg többször is beszerezni a jogkivonatot ugyanazzal az engedélyezési kóddal (ezt a protokollszabvány-specifikáció kifejezetten tiltja). Ha szándékosan többször is beváltja a kódot, vagy mert nem tudja, hogy egy keretrendszer is megteszi, a következő hibaüzenet et kapja:`AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Ha ASP.NET vagy ASP.NET Core alkalmazás, ez akkor fordulhat elő, ha nem mondja el a keretrendszernek, hogy már beváltotta az engedélyezési kódot. Ehhez meg kell hívnia `context.HandleCodeRedemption()` az `AuthorizationCodeReceived` eseménykezelő metódusát.

- Ne ossza meg a hozzáférési jogkivonatot ASP.NET, ami megakadályozhatja a növekményes hozzájárulás megfelelő előfordulását. További információt a probléma #693 című témakörben [talál.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693)

## <a name="on-behalf-of"></a>Nevében

Az MSAL támogatja az [OAuth 2 hitelesítési folyamatot.](v2-oauth2-on-behalf-of-flow.md)  Ez a folyamat akkor használatos, amikor egy alkalmazás meghívja a szolgáltatás vagy webes API-t, amely viszont meg kell hívnia egy másik szolgáltatás vagy webes API-t. Az ötlet a delegált felhasználói identitás és engedélyek propagálása a kérelemláncon keresztül. Ahhoz, hogy a középső rétegbeli szolgáltatás hitelesített kérelmeket küldjön az alsóbb rétegbeli szolgáltatásnak, a felhasználó nevében biztosítania kell egy hozzáférési jogkivonatot a Microsoft identitásplatformról.

![Az áramlás nevében történő diagram](media/msal-authentication-flows/on-behalf-of.png)

A fenti diagram elemei:

1. Az alkalmazás beszerez egy hozzáférési jogkivonatot a webes API-hoz.
2. Egy ügyfél (webes, asztali, mobil vagy egyoldalas alkalmazás) meghívja a védett webes API-t, hozzáadva a hozzáférési jogkivonatot tulajdonosi jogkivonatként a HTTP-kérelem hitelesítési fejlécében. A webes API hitelesíti a felhasználót.
3. Amikor az ügyfél meghívja a webes API-t, a webes API egy másik jogkivonatot kér a felhasználó nevében.  
4. A védett webes API ezt a jogkivonatot használja egy alsóbb rétegbeli webes API-hívásához a felhasználó nevében.  A webes API később is kérhet jogkivonatokat más alsóbb rétegbeli API-k (de még mindig ugyanannak a felhasználónak a nevében).

## <a name="client-credentials"></a>Ügyfél-hitelesítő adatok

Az MSAL támogatja az [OAuth 2 ügyfél hitelesítő adatait.](v2-oauth2-client-creds-grant-flow.md) Ez a folyamat lehetővé teszi a webes üzemeltetésű erőforrások elérését egy alkalmazás identitásának használatával. Az ilyen típusú támogatást gyakran használják a kiszolgálók közötti interakciókhoz, amelyeknek a háttérben kell futniuk, a felhasználóval való azonnali interakció nélkül. Az ilyen típusú alkalmazásokat gyakran démonnak vagy szolgáltatásfióknak nevezik. 

Az ügyfél hitelesítő adatok at ad flow lehetővé teszi, hogy egy webszolgáltatás (egy bizalmas ügyfél) használja a saját hitelesítő adatait, ahelyett, hogy megszemélyesíteni egy felhasználó, hitelesítésre, amikor egy másik webszolgáltatás hívása. Ebben a forgatókönyvben az ügyfél általában egy középső rétegű webszolgáltatás, egy démonszolgáltatás vagy egy webhely. A magasabb szintű biztonság érdekében a Microsoft identity platform azt is lehetővé teszi, hogy a hívó szolgáltatás egy tanúsítványt használjon (a közös titok helyett) hitelesítő adatként.

> [!NOTE]
> A bizalmas ügyfélfolyamat nem érhető el a mobil platformokon (UWP, Xamarin.iOS és Xamarin.Android), mert ezek csak a nyilvános ügyfélalkalmazásokat támogatják. A nyilvános ügyfélalkalmazások nem tudják, hogyan bizonyítsa az alkalmazás identitását az identitásszolgáltatónak. Biztonságos kapcsolat érhető el a webapp vagy webes API-háttérrendszereken egy tanúsítvány telepítésével.

MSAL.NET kétféle ügyfélhitelesítő adatot támogat. Ezeket az ügyfélhitelesítő adatokat regisztrálni kell az Azure AD-vel. A hitelesítő adatok a kódban a bizalmas ügyfélalkalmazás konstruktorainak kerülnek átadásra.

### <a name="application-secrets"></a>Alkalmazásttitok

![A bizalmas ügyfél diagramja jelszóval](media/msal-authentication-flows/confidential-client-password.png)

Az előző ábrán az alkalmazás:

1. Jogkivonat beszerzése alkalmazástitkos vagy jelszó hitelesítő adatok használatával.
2. A jogkivonatot használja az erőforrás kéréseihez.

### <a name="certificates"></a>Tanúsítványok

![A bizalmas ügyfél diagramja a cert-tel](media/msal-authentication-flows/confidential-client-certificate.png)

Az előző ábrán az alkalmazás:

1. Jogkivonat beszerzése a tanúsítvány hitelesítő adataival.
2. A jogkivonatot használja az erőforrás kéréseihez.

Ezeknek az ügyfélhitelesítő adatoknak a következőknek kell lenniük:
- Regisztrálva van az Azure AD-nél.
- Átment az építőiparban a bizalmas ügyfél alkalmazás a kódot.

## <a name="device-code"></a>Eszköz kódja

Az MSAL támogatja az [OAuth 2 eszközkód-folyamatot,](v2-oauth2-device-code.md)amely lehetővé teszi a felhasználók számára, hogy bejelentkezzenek a bemeneti korlátozott eszközökre, például egy intelligens TV-re, IoT-eszközre vagy nyomtatóra. Az Azure AD-vel való interaktív hitelesítéshez webböngésző szükséges. Az eszközkód-áramlás lehetővé teszi, hogy a felhasználó egy másik eszközt (például egy másik számítógépet vagy mobiltelefont) használjon az interaktív bejelentkezéshez, ahol az eszköz vagy az operációs rendszer nem biztosít webböngészőt.

Az eszközkód-folyamat használatával az alkalmazás jogkivonatokat szerez be egy kétlépéses folyamaton keresztül, amelyet kifejezetten ezekhez az eszközökhöz vagy operációs rendszerekhez terveztek. Ilyen alkalmazások például az IoT-eszközökön vagy parancssori eszközökön (CLI) futó alkalmazások. 

![Az eszközkód-folyamat diagramja](media/msal-authentication-flows/device-code.png)

A fenti diagram elemei:

1. Amikor felhasználói hitelesítésre van szükség, az alkalmazás egy kódot ad meg, és megkéri a felhasználót, hogy `https://microsoft.com/devicelogin`használjon egy másik eszközt (például egy internethez csatlakoztatott okostelefont) egy URL-cím (például ) megugrásához. A felhasználó ezután kéri, hogy adja meg a kódot, és folytatja a szokásos hitelesítési élményt, beleértve a hozzájárulási utasításokat és a többtényezős hitelesítés, ha szükséges.

2. Sikeres hitelesítés után a parancssori alkalmazás fogadja a szükséges jogkivonatokat egy hátsó csatornán keresztül, és használja őket a szükséges webes API-hívások végrehajtásához.

### <a name="constraints"></a>Korlátozások

- Az eszközkód-folyamat csak nyilvános ügyfélalkalmazásokon érhető el.
- A nyilvános ügyfélalkalmazás összeállításakor átadott hatóságnak a következők egyikének kell lennie:
  - Bérlő (az az `https://login.microsoftonline.com/{tenant}/` `{tenant}` űrlap, ahol a bérlőazonosítót képviselő GUID vagy a bérlőhöz társított tartomány).
  - Minden munkahelyi és iskolai`https://login.microsoftonline.com/organizations/`számlák ( ).
- A Microsoft személyes fiókok még nem támogatja az Azure AD v2.0-végpont `/consumers` (nem használhatja a vagy a `/common` bérlők).

## <a name="integrated-windows-authentication"></a>Beépített Windows-hitelesítés

Az MSAL támogatja az integrált Windows-hitelesítést (IWA) olyan asztali vagy mobilalkalmazásokhoz, amelyek olyan tartományhoz csatlakoznak, vagy az Azure AD csatlakozott a Windows-számítógéphez. Az IWA használatával ezek az alkalmazások némán szerezhetnek be egy jogkivonatot (a felhasználó felhasználói felületi beavatkozása nélkül). 

![Integrált Windows-hitelesítés diagramja](media/msal-authentication-flows/integrated-windows-authentication.png)

Az előző ábrán az alkalmazás:

1. Token beszerzése integrált Windows-hitelesítéssel.
2. A jogkivonatot használja az erőforrás kéréseihez.

### <a name="constraints"></a>Korlátozások

Az IWA csak az összevont felhasználókat támogatja, ami azt jelenti, hogy az Active Directoryban létrehozott és az Azure AD által támogatott felhasználók. Közvetlenül az Azure AD-ben létrehozott felhasználók, Active Directory-biztonsági mentés nélkül (felügyelt felhasználók) nem használhatják ezt a hitelesítési folyamatot. Ez a korlátozás nincs hatással a [felhasználónév/jelszó folyamatára.](#usernamepassword)

Az IWA a .

Az IWA nem kerüli meg a többtényezős hitelesítést. Ha többtényezős hitelesítés van konfigurálva, az IWA sikertelen lehet, ha többtényezős hitelesítési kihívásra van szükség. A többtényezős hitelesítés felhasználói beavatkozást igényel.

Nem szabályozhatja, hogy az identitásszolgáltató mikor kéri a kétfaktoros hitelesítést. A bérlői rendszergazda igen. Általában kétfaktoros hitelesítésre van szükség, ha egy másik országból jelentkezik be, ha nem VPN-en keresztül csatlakozik egy vállalati hálózathoz, és néha akkor is, ha VPN-en keresztül csatlakozik. Az Azure AD a i segítségével folyamatosan megtudhatja, ha kétfaktoros hitelesítésre van szükség. Ha az IWA nem sikerül, vissza kell lépnie egy [interaktív felhasználói üzenetre] (#interactive).

A nyilvános ügyfélalkalmazás összeállításakor átadott hatóságnak a következők egyikének kell lennie:
- Bérlő (az az `https://login.microsoftonline.com/{tenant}/` `tenant` űrlap, ahol a bérlőazonosítót képviselő guid vagy a bérlőhöz társított tartomány található).
- Minden munkahelyi és iskolai`https://login.microsoftonline.com/organizations/`számlák ( ). A Microsoft személyes fiókjai nem támogatottak (nem használhatja vagy `/common` `/consumers` nem használhatja a bérlőket).

Mivel az IWA egy csendes folyamat, az alábbiak egyikének igaznak kell lennie:
- Az alkalmazás felhasználójának korábban hozzá kell járulnia az alkalmazás használatához. 
- A bérlő i. rendszergazda korábban már hozzájárult ahhoz, hogy a bérlő összes felhasználója használja az alkalmazást.

Ez azt jelenti, hogy az alábbiak egyike igaz:
- Ön, mint fejlesztő, saját maga választotta a **Grant** szolgáltatást az Azure Portalon.
- Egy bérlői rendszergazda az alkalmazás regisztrációjának **API-engedélyek** lapján **a(z) {tenant domain} rendszergazdai hozzájárulásának megadása/visszavonása** lehetőséget választotta (lásd: [Engedélyek hozzáadása webes API-khoz).](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)
- Ön módot adott arra, hogy a felhasználók hozzájáruljanak az alkalmazáshoz (lásd: [Egyéni felhasználó hozzájárulásának kérése).](v2-permissions-and-consent.md#requesting-individual-user-consent)
- Megadta a módját, hogy a bérlői rendszergazda hozzájáruljon az alkalmazáshoz (lásd: [rendszergazdai hozzájárulás).](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)

Az IWA-folyamat engedélyezve van a .NET asztali, a .NET Core és a Windows Universal Platform alkalmazásokhoz. A .NET Core rendszerben meg kell adnia a felhasználónevet az IWA-nak, mert a .NET Core nem tud felhasználóneveket beszerezni az operációs rendszerből.
  
A hozzájárulással kapcsolatos további információkért lásd a [2.0-s és a 2.0-s és a hozzájárulási engedélyt.](v2-permissions-and-consent.md)

## <a name="usernamepassword"></a>Felhasználónév/jelszó

Az MSAL támogatja az [OAuth 2 erőforrás-tulajdonos jelszó-hitelesítő adatok megadását,](v2-oauth-ropc.md)amely lehetővé teszi, hogy egy alkalmazás közvetlenül kezelje a jelszavát. Az asztali alkalmazásban a felhasználónév/jelszó folyamat segítségével némán szerezhet be egy jogkivonatot. Az alkalmazás használata kor nincs szükség felhasználói felületre.

![A felhasználónév/jelszó folyamat ábrája](media/msal-authentication-flows/username-password.png)

Az előző ábrán az alkalmazás:

1. A jogkivonat ot a felhasználónév és a jelszó elküldésével szerzi be az identitásszolgáltatónak.
2. Webes API-t hív meg a jogkivonat használatával.

> [!WARNING]
> Ez a folyamat nem ajánlott. Ez megköveteli a magas fokú bizalom és a felhasználói kitettség.  Ezt a folyamatot csak akkor használja, ha más, biztonságosabb, folyamatok nem használhatók. További információ: [Mi a megoldás a jelszavak növekvő problémájára?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

A token csendes beszerzésének előnyben részesített folyamata a Windows tartományhoz csatlakozó gépeken [az integrált Windows-hitelesítés](#integrated-windows-authentication). Ellenkező esetben [használhatja az Eszközkód-folyamatot](#device-code)is.

Bár ez bizonyos esetekben hasznos (DevOps-forgatókönyvek), ha felhasználónevet/jelszót szeretne használni interaktív forgatókönyvekben, ahol saját felhasználói felületet biztosít, próbálja meg elkerülni azt. Felhasználónév/jelszó használatával:
- Azok a felhasználók, akiknek többtényezős hitelesítést kell végezniük, nem tudnak bejelentkezni (mivel nincs interakció).
- A felhasználók nem tudnak egyszeri bejelentkezést végezni.

### <a name="constraints"></a>Korlátozások

Az integrált [Windows-hitelesítési megkötéseken](#integrated-windows-authentication)kívül a következő korlátozások is érvényesek:

- A felhasználónév/jelszó folyamat nem kompatibilis a feltételes hozzáférés és a többtényezős hitelesítés. Ennek következtében, ha az alkalmazás fut egy Azure AD-bérlő, ahol a bérlői rendszergazda többtényezős hitelesítést igényel, nem használhatja ezt a folyamatot. Sok szervezet csinálja ezt.
- Ez szerkezet egyetlen részére dolgozik és iskola számlák ( nem Mikroszkóp számlák).
- A folyamat elérhető a .NET asztalon és a .NET Core rendszeren, de az univerzális Windows platformon nem.

### <a name="azure-ad-b2c-specifics"></a>Az Azure AD B2C-specifikus ad

A MSAL.NET és az Azure AD B2C használatával kapcsolatos további tudnivalókért olvassa el [a ROPC használata az Azure AD B2C(MSAL.NET) című témakört.](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c)
