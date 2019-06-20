---
title: Hitelesítési folyamatok (Microsoft-hitelesítési tár) |} Az Azure
description: Ismerje meg a hitelesítési folyamatok és a Microsoft hitelesítési tár (MSAL) által használt biztosít.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 821143d39f8a4c06501ee38ef598a9d06d267d72
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273114"
---
# <a name="authentication-flows"></a>Hitelesítési folyamatok

Ez a cikk ismerteti a Microsoft hitelesítési tár (MSAL) által biztosított különböző hitelesítési folyamatok.  Ezek a folyamatok egyes alkalmazási esetekben különböző használható.

| Folyamat | Leírás | Használatban|  
| ---- | ----------- | ------- | 
| [Interaktív](#interactive) | A token beolvasása egy interaktív folyamat, amely felszólítja a felhasználót a hitelesítő adatokat böngésző vagy a felugró ablakban. | [Asztali alkalmazások](scenario-desktop-overview.md), [mobilalkalmazások](scenario-mobile-overview.md) |
| [Implicit engedélyezés](#implicit-grant) | Itt engedélyezheti az alkalmazásnak egy háttér-hitelesítő adat exchange végrehajtása nélkül tokenekhez. Ez lehetővé teszi az alkalmazásnak, hogy jelentkezzen be a felhasználói munkamenet fenntartásához és tokenekhez más webes API-k, mindezt az ügyfelet JavaScript-kódot.| [Egylapos alkalmazások (SPA)](scenario-spa-overview.md) |
| [engedélyezési kód](#authorization-code) | Használja az alkalmazást, amely egy eszközön a védett erőforrások, például a webes API-k eléréséhez. Ez lehetővé teszi, hogy be- és API-hozzáférés hozzáadása a mobil- és asztali alkalmazásokhoz. | [Asztali alkalmazások](scenario-desktop-overview.md), [mobilalkalmazások](scenario-mobile-overview.md), [webes alkalmazások](scenario-web-app-call-api-overview.md) | 
| [On-behalf-of](#on-behalf-of) | Egy alkalmazás egy szolgáltatás vagy a webes API-t, amelyet viszont másik szolgáltatás hívásához használtakat vagy webes API-t hív meg. A cél pedig propagálása a delegált felhasználó identitása és a kérelem láncot engedélyeket. | [Webes API-k](scenario-web-api-call-api-overview.md) |
| [Ügyfél-hitelesítő adatok](#client-credentials) | Lehetővé teszi webes tárolt erőforrások eléréséhez az alkalmazás identitását. Gyakran használt kiszolgálók közötti kapcsolatok definiálására, amelyeket futtatnia kell a háttérben, egy felhasználóval azonnali beavatkozás nélkül. | [démon alkalmazások](scenario-daemon-overview.md) |
| [Eszköz kód](#device-code) | Jelentkezzen be a bemeneti korlátozott eszközök, mint egy Okostelevízió, IoT-eszközből vagy nyomtató lehetővé teszi. | [Asztali és mobil alkalmazások](scenario-desktop-acquire-token.md#command-line-tool-without-web-browser) |
| [Integrált Windows-hitelesítés](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Lehetővé teszi a tartományban vagy az Azure Active Directory (Azure AD-) alkalmazások csatlakoztatott számítógépek csendes módban (a felhasználó felhasználói felület felhasználói beavatkozás) nélkül egy token beszerzéséhez.| [Asztali és mobil alkalmazások](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Felhasználónév/jelszó](scenario-desktop-acquire-token.md#username--password) | Lehetővé teszi olyan alkalmazások, hogy jelentkezzen be a felhasználó közvetlenül kezeli a jelszavát. Ez a folyamat nem ajánlott. | [Asztali és mobil alkalmazások](scenario-desktop-acquire-token.md#username--password) | 

## <a name="interactive"></a>Interaktív
Az MSAL támogatja az interaktív módon kéri a felhasználót a hitelesítő adatait, jelentkezzen be, és ezeket a hitelesítő adatokat a jogkivonat beszerzése.

![Interaktív folyamat ábrája](media/msal-authentication-flows/interactive.png)

További információ az MSAL.NET használatával interaktív módon szerzi be a jogkivonatokat az adott platformon lásd:
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [Univerzális Windows-platform](msal-net-uwp-considerations.md)

Interaktív hívás MSAL.js további információkért lásd: [MSAL.js interaktív kérelmek viselkedés kérni](msal-js-prompt-behavior.md).

## <a name="implicit-grant"></a>Implicit engedélyezés

Az MSAL támogatja a [OAuth 2 implicit adja meg a folyamat](v2-oauth2-implicit-grant-flow.md), amely lehetővé teszi az alkalmazásnak, hogy tokenekhez a Microsoft identity platform a háttérkiszolgáló-végrehajtása nélkül hitelesítő adatok. Ez lehetővé teszi az alkalmazásnak, hogy jelentkezzen be a felhasználói munkamenet fenntartásához és tokenekhez más webes API-k, mindezt az ügyfelet JavaScript-kódot.

![Implicit engedélyezési folyamat ábrája](media/msal-authentication-flows/implicit-grant.svg)

Számos modern webes alkalmazások beépített JavaScript- vagy egy Angular oldalon a Vue.js és React.js például SPA-keretrendszer használatával írt ügyféloldali, egyetlen lapon alkalmazásokként. Ezek az alkalmazások futtatása böngészőben, és hagyományos kiszolgálóoldali webes alkalmazásokhoz, mint a különböző hitelesítési jellemzőkkel rendelkeznek. A Microsoft identity platform lehetővé teszi, hogy a felhasználók bejelentkeztetéséhez és tokenekhez Háttérszolgáltatásokhoz vagy webes API-k, az implicit engedélyezési folyamat elérni egyoldalas alkalmazások. Az implicit folyamatot lehetővé teszi, hogy az alkalmazás számára, és a hitelesített felhasználó képviselik, is elérhetik a védett API-k meghívásához szükséges jogkivonatok azonosító tokenekhez.

A hitelesítési folyamat nem tartalmazza a Electron és a React-natív, többplatformos JavaScript keretrendszereket használó alkalmazás-forgatókönyvek, mert ilyen esetben további képességek a natív platformokhoz való együttműködéshez szükséges.

## <a name="authorization-code"></a>engedélyezési kód
Az MSAL támogatja a [OAuth 2 engedélyezési kód](v2-oauth2-auth-code-flow.md). Ez a támogatás az alkalmazást, amely egy eszközön a védett erőforrások, például a webes API-k eléréséhez használható. Ez lehetővé teszi, hogy be- és API-hozzáférés hozzáadása a mobil- és asztali alkalmazásokhoz. 

Amikor bejelentkeznek a webes alkalmazásokhoz (webhelyek), a webes alkalmazás kap egy engedélyezési kód.  Az engedélyezési kódot a webes API-k hívásához egy token beszerzéséhez válthatók be. Az ASP.NET és az ASP.NET Core web apps, az egyetlen célja `AcquireTokenByAuthorizationCode` jogkivonat hozzáadása a jogkivonatok gyorsítótárát. A jogkivonat ezután felhasználhatók az alkalmazás (általában a vezérlő, amely csak egy jogkivonatot kapjon a API-k használatával `AcquireTokenSilent`).

![Az engedélyezési kód folyamata ábrája](media/msal-authentication-flows/authorization-code.png)

A fenti ábrán, az alkalmazás:

1. Az engedélyezési kódot, amely van válthatók be egy hozzáférési jogkivonatot kér.
2. A hozzáférési jogkivonat segítségével webes API-hívás.

### <a name="considerations"></a>Megfontolandó szempontok
- A hozzáférési kód csak egyszer segítségével-jogkivonat beváltása. Ne próbálja többször, ugyanazt a hitelesítési kódot (kifejezetten tilos a szabványos protokoll-meghatározása szerint) egy token beszerzéséhez. Ha a webhelyen történő beváltása a kód többször szándékosan, vagy mert nem vegye figyelembe, hogy egy keretrendszert is mindezt megteszi Ön helyett, a következő hiba fog kapni: `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Ha szeretne írni egy ASP.NET- vagy ASP.NET Core-alkalmazást, ez akkor fordulhat, don't tell a keretrendszer, amely korábban már beváltott az engedélyezési kód. Ehhez meg kell hívnia a `context.HandleCodeRedemption()` módszere a `AuthorizationCodeReceived` eseménykezelő.

- Kerülje a hozzáférési jogkivonat megosztását az ASP.NET-tel, ami megakadályozhatja a növekményes hozzájárulási megfelelően történik. További információkért lásd: [#693 kiadása](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693).

## <a name="on-behalf-of"></a>On-behalf-of

Az MSAL támogatja a [OAuth 2--meghatalmazásos hitelesítési folyamat](v2-oauth2-on-behalf-of-flow.md).  Ez a folyamat akkor használja, ha egy alkalmazás elindítja egy szolgáltatás vagy a webes API-t, amely viszont másik szolgáltatás hívásához használtakat, vagy webes API-t kell. A cél pedig propagálása a delegált felhasználó identitása és a kérelem láncot engedélyeket. A középső rétegű szolgáltatás hitelesített kéréseket küld az alárendelt szolgáltatás biztonságos hozzáférési tokent a Microsoft identity platform, a felhasználó nevében kell.

![A-meghatalmazásos folyamat ábrája](media/msal-authentication-flows/on-behalf-of.png)

A fenti ábrán:

1. Az alkalmazás megszerzi a webes API hozzáférési jogkivonat.
2. (Webes, asztali-, mobil, vagy egyoldalas alkalmazás) ügyfél meghívja a védett webes API-k, a hozzáférési jogkivonat hozzáadása a HTTP-kérés hitelesítési fejléc tulajdonosi jogkivonattal. A webes API hitelesíti a felhasználót.
3. Amikor az ügyfél meghívja a webes API, a webes API kérelmek--meghatalmazásos egy másik jogkivonatot a felhasználónak.  
4. A védett webes API a jogkivonat alapján alsóbb rétegbeli webes API-hívás a alapú meghatalmazásos a felhasználó.  A webes API-t is jogkivonatok kérelmezésére újabb más alsóbb rétegbeli API-k (de még mindig ugyanaz a felhasználó nevében).

## <a name="client-credentials"></a>Ügyfél-hitelesítő adatok

Az MSAL támogatja a [OAuth 2 ügyfél hitelesítő adatai a flow](v2-oauth2-client-creds-grant-flow.md). Ez a folyamat lehetővé teszi webes tárolt erőforrások eléréséhez az alkalmazás identitását. Támogatás az ilyen kiszolgálók közötti kapcsolatok definiálására, amelyeket futtatnia kell a háttérben, egy felhasználóval azonnali beavatkozás nélkül gyakran használják. Ilyen típusú alkalmazások gyakran nevezzük démonok vagy szolgáltatásfiókokat. 

Az ügyfél hitelesítő adatainak megadása a folyamat lehetővé teszi egy webszolgáltatás (bizalmas ügyfél) a saját hitelesítő adatait, használja a felhasználó megszemélyesítése helyett egy másik webszolgáltatás hívásakor. Ebben a forgatókönyvben az ügyfél nem általában egy középső rétegű webszolgáltatás, egy démonszolgáltatás vagy webhely. A magasabb szintű megbízhatóságra a Microsoft identity platform is lehetővé teszi, hogy a hívó szolgáltatás (és nem egy közös titkos kulcsot) egy tanúsítvány használandó hitelesítő adatokat.

> [!NOTE]
> A bizalmas ügyfél flow nem érhető el (UWP Xamarin.iOS és Xamarin.Android), a mobil platformokon, mert ezek csak nyilvános ügyfélalkalmazások támogatja. Nyilvános ügyfélalkalmazások nem tudom, hogyan igazolja az alkalmazás identitását segítségével az Identitásszolgáltatókhoz. Biztonságos kapcsolat webalkalmazás a érhető el, vagy webes API-k biztonsági tanúsítvány telepítésével vége.

MSAL.NET ügyfél-hitelesítő adatok két típusát támogatja. Ezen ügyfél-hitelesítő adatok az Azure ad-vel regisztrálni kell. A hitelesítő adatok továbbítódnak a a konstruktorok az bizalmas ügyfélalkalmazás a kódban.

### <a name="application-secrets"></a>Titkos alkalmazáskulcsok 

![Bizalmas ügyfél felhasználónévvel és jelszóval ábrája](media/msal-authentication-flows/confidential-client-password.png)

A fenti ábrán, az alkalmazás:

1. Tokenbeolvasás alkalmazás titkos kulcs vagy jelszó hitelesítő adatok használatával.
2. Használja a jogkivonatot, hogy az erőforrás kéréseket.

### <a name="certificates"></a>Tanúsítványok 

![A tanúsítvány bizalmas ügyfél ábrája](media/msal-authentication-flows/confidential-client-certificate.png)

A fenti ábrán, az alkalmazás:

1. Tokenbeolvasás tanúsítvány hitelesítő adatok használatával.
2. Használja a jogkivonatot, hogy az erőforrás kéréseket.

Ezen ügyfél hitelesítő adatait kell lennie:
- Az Azure ad-vel regisztrálva.
- Az átadott, a kódban a bizalmas ügyfélalkalmazás felépítése.


## <a name="device-code"></a>Eszköz kód
Támogatja az MSAL az [OAuth 2 eszköz kódfolyamat](v2-oauth2-device-code.md), amely lehetővé teszi, hogy a felhasználók számára, hogy jelentkezzen be a bemeneti korlátozott eszközök, mint egy Okostelevízió, IoT-eszközből vagy nyomtató. Interaktív hitelesítés az Azure ad-vel egy webböngésző szükséges. Az eszköz kódfolyamat lehetővé teszi, hogy a felhasználó egy másik eszközt (például egy másik számítógépre vagy egy mobiltelefon) használatával jelentkezzen be interaktívan, ahol az eszköz vagy az operációs rendszer nem adja meg egy webböngészőben.

Az alkalmazás az eszköz hitelesítésikód-folyamata segítségével kéri le a jogkivonatok segítségével egy kétlépéses folyamat ezen eszközök vagy az operációs rendszerek készült. Az ilyen alkalmazások közé tartoznak az IoT-eszközök vagy a parancssori eszközök (CLI). 

![Eszköz kódfolyamat ábrája](media/msal-authentication-flows/device-code.png)

A fenti ábrán:

1. Felhasználói hitelesítés szükség, amikor az alkalmazás egy kódot biztosít, és megkérdezi a felhasználót, nyissa meg egy URL-címet (például egy internethez csatlakozó okostelefon) egy másik eszköz használatával (például https://microsoft.com/devicelogin). A felhasználó kéri majd, hogy adja meg a kódot, és a egy normál hitelesítési módszer, beleértve a beleegyezést kérő és szükség esetén a multi-factor authentication végighalad.

2. Sikeres hitelesítést követően a parancssori alkalmazás kapja a szükséges jogkivonatok, biztonsági csatornán keresztül történik, és használja ezeket a szükséges API-hívások végrehajtásához.

### <a name="constraints"></a>Korlátozások

- Eszköz kódfolyamat csak nyilvános ügyfélalkalmazások számára érhető el.
- A szolgáltató átadott amikor hozhat létre, amely a nyilvános ügyfélalkalmazás kell lennie a következők egyikét:
  - Bérlős (a képernyő `https://login.microsoftonline.com/{tenant}/` ahol `{tenant}` vagy a bérlői azonosító vagy egy tartományhoz a tenanthoz társított képviselő GUID).
  - a munkahelyi és iskolai fiókok (`https://login.microsoftonline.com/organizations/`).
- Személyes Microsoft-fiókok még nem támogatja az Azure AD v2.0-végpont (nem használható a `/common` vagy `/consumers` bérlők).

## <a name="integrated-windows-authentication"></a>Integrált Windows-hitelesítés
Az MSAL integrált Windows-hitelesítés (IWA) támogatja az asztali, vagy egy tartományhoz csatlakoztatottnak vagy az Azure ad-ben futó alkalmazásokat csatlakoztatott Windows-számítógépen. IWA használja, ezek az alkalmazások lekérheti a jogkivonat csendes módban (a felhasználó felhasználói felület felhasználói beavatkozás) nélkül. 

![Integrált Windows-hitelesítés diagramja](media/msal-authentication-flows/integrated-windows-authentication.png)

A fenti ábrán, az alkalmazás:

1. Tokenbeolvasás integrált Windows-hitelesítés használatával.
2. Használja a jogkivonatot, hogy az erőforrás kéréseket.

### <a name="constraints"></a>Korlátozások

IWA támogatja az összevont felhasználók csak, azaz a felhasználók az Active Directory címtárban, és az Azure AD által támogatott. Active Directory biztonsági nélkül közvetlenül az Azure AD-ben létrehozott felhasználók (kezelt felhasználó) a hitelesítési folyamat nem használható. Ez a korlátozás nem befolyásolja a [felhasználónév/jelszó folyamat](#usernamepassword).

IWA olyan .NET-keretrendszer, a .NET Core és az univerzális Windows Platform-platformokhoz írt alkalmazásokat.

IWA nem a multi-factor authentication kihagyásához. Ha a multi-factor authentication szolgáltatás van konfigurálva, IWA meghiúsulhat, ha a multi-factor authentication kihívást megadása kötelező. A multi-factor authentication felhasználói beavatkozást igényel.

Nem szabályozhatja, amikor az identitásszolgáltató igényel a kéttényezős hitelesítést kell elvégezni. A bérlői rendszergazda hajtja végre. Általában kétfaktoros hitelesítés kötelező, ha bejelentkezik a más országban, amikor nincs csatlakozva VPN-en keresztül a vállalati hálózathoz és néha még ha csatlakozott VPN-en keresztül. Az Azure AD mesterséges Intelligencia használatával folyamatosan ismerje meg, ha a kétfaktoros hitelesítés szükség. IWA meghiúsul, ha meg kell térhet vissza a felhasználói kérés (https://aka.ms/msal-net-interactive).

A szolgáltató átadott amikor hozhat létre, amely a nyilvános ügyfélalkalmazás kell lennie a következők egyikét:
- Bérlős (a képernyő `https://login.microsoftonline.com/{tenant}/` ahol `tenant` vagy a bérlői azonosító vagy egy tartományhoz a tenanthoz társított képviselő GUID).
- a munkahelyi és iskolai fiókok (`https://login.microsoftonline.com/organizations/`). Személyes Microsoft-fiókok nem támogatottak (nem használhat `/common` vagy `/consumers` bérlők).

Mivel a IWA beavatkozás nélküli folyamatok, a következők egyikét kell teljesülniük:
- az alkalmazás a felhasználó korábban hozzájárult kell, hogy használják az alkalmazást. 
- A bérlői rendszergazda korábban hozzájárult kell, hogy az alkalmazás a bérlő összes felhasználója.

Ez azt jelenti, hogy az alábbiak egyike igaz:
- A fejlesztők kiválasztott **Grant** maga az Azure Portalon.
- Bérlői rendszergazda ki van választva **engedélyezéshez/visszavonáshoz rendszergazdai jóváhagyás {bérlői tartományhoz}** a a **API-engedélyek** a az alkalmazás regisztrációs lapján (lásd: [webes API-k elérésére vonatkozó engedélyek hozzáadása ](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)).
- A megadott felhasználók, hogy engedélyt adjanak az alkalmazásnak (lásd: [egyedi felhasználói jóváhagyás kérése](v2-permissions-and-consent.md#requesting-individual-user-consent)).
- Így a bérlői rendszergazdai jóváhagyást az alkalmazás a megadott (lásd: [rendszergazdai jóváhagyás](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)).

.NET asztali, a .NET Core és az univerzális Windows-Platformos alkalmazások engedélyezve van a IWA folyamatot. A .NET Core keretrendszert csak a felhasználónév véve túlterhelés érhető el. A .NET Core platform nem kérje meg az operációs rendszer a felhasználónevet.
  
A jóváhagyás további információkért lásd: [v2.0 engedélyek és jóváhagyás](v2-permissions-and-consent.md).

## <a name="usernamepassword"></a>Felhasználónév/jelszó 
Az MSAL támogatja a [OAuth 2 erőforrás tulajdonosának jelszavas hitelesítése](v2-oauth-ropc.md), amely lehetővé teszi, hogy az alkalmazás közvetlenül kezeli a jelszavukat a felhasználói bejelentkezés. Az asztali alkalmazás használhatja a felhasználónév/jelszó folyamat egy token beszerzéséhez beavatkozás nélkül. Felhasználói felület nem szükséges, az alkalmazás használata során.

![A felhasználónév/jelszó folyamat ábrája](media/msal-authentication-flows/username-password.png)

A fenti ábrán, az alkalmazás:

1. Tokenbeolvasás, ha az identitásszolgáltató küld a felhasználónevet és jelszót.
2. A jogkivonat használatával meghívja a webes API-t.

> [!WARNING]
> Ez a folyamat nem ajánlott. Nagyfokú megbízhatóság és a felhasználó fenyegetéseknek van szükség.  Ez a folyamat csak akkor ajánlott, ha más, az biztonságosabb, a folyamatok nem használható. További információkért lásd: [Mi a probléma megoldása érdekében a növekvő jelszavak?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Előnyben részesített tokenbeolvasás csendes módban a Windows-tartományhoz csatlakoztatott gépeket folyamatábrája [integrált Windows-hitelesítés](#integrated-windows-authentication). Ellenkező esetben használhatja [eszköz kódfolyamat](#device-code).

Bár ez hasznos, ha bizonyos esetekben (DevOps-forgatókönyvekre), ha azt szeretné, felhasználónév/jelszó használata az interaktív forgatókönyvek, ahol meg kell a saját felhasználói felület, próbálja meg ezt elkerülni. Felhasználónév/jelszó használatával:
- Felhasználók, akik kell tennie, hogy a multi-factor authentication nem lehet bejelentkezni (mivel nem lett a beavatkozás nélküli).
- Felhasználók nem tudják az egyszeri bejelentkezés.

### <a name="constraints"></a>Korlátozások

Kivéve a [integrált Windows-hitelesítés megkötések](#integrated-windows-authentication), a következő korlátozások is érvényesek:

- A felhasználónév/jelszó folyamat nem kompatibilis a feltételes hozzáférés és a multi-factor authentication szolgáltatás. Következtében ha az alkalmazás fut az Azure AD-bérlővel, ahol a bérlői rendszergazda multi-factor Authentication hitelesítést igényel, nem használhatja ezt a folyamatot. Számos szervezet megtenni.
- Csak a munkahelyi és iskolai fiókok (nem a Microsoft-fiókok) működik.
- A folyamat .NET asztali és a .NET Core, de nem univerzális Windows Platform érhető el.

### <a name="azure-ad-b2c-specifics"></a>Az Azure AD B2C-vel tulajdonságairól

A MSAL.NET és az Azure AD B2C-vel további információkért lásd: [ROPC használata az Azure AD B2C-vel (MSAL.NET)](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c).
