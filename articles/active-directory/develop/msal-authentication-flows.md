---
title: Ügyfélalkalmazások (Microsoft-hitelesítési tár) |} Az Azure
description: További információ a nyilvános és bizalmas ügyfélprogram alkalmazások a Microsoft hitelesítési tár (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
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
ms.openlocfilehash: 096aa5e5ce2f33467457cef22220f338ae49b708
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65139100"
---
# <a name="authentication-flows"></a>Hitelesítési folyamatok

Ez a cikk ismerteti a Microsoft hitelesítési tár (MSAL) által biztosított különböző hitelesítési folyamatok.  Ezek a folyamatok egyes alkalmazási esetekben különböző használható.

| Folyamat | Leírás | Használatban|  
| ---- | ----------- | ------- | 
| [Implicit engedélyezés](#implicit-grant) | Itt engedélyezheti az alkalmazásnak a jogkivonatok lekérésére, egy háttérrendszer kiszolgálói hitelesítő adatok exchange végrehajtása nélkül. Ez lehetővé teszi az alkalmazásnak, hogy jelentkezzen be a felhasználói munkamenet fenntartásához és tokenekhez más webes API-k mindezt az ügyfelet JavaScript-kódot.| [Egylapos alkalmazások (SPA)](scenario-spa-overview.md) |
| [engedélyezési kód](#authorization-code) | Használja az alkalmazást, amely egy eszközön a védett erőforrások, például a webes API-k eléréséhez. Ez lehetővé teszi, hogy jelentkezzen be, és az API eléréséhez a mobil- és asztali alkalmazásokhoz. | [Asztali alkalmazások](scenario-desktop-overview.md), [mobilalkalmazások](scenario-mobile-overview.md), [Web Apps](scenario-web-app-call-api-overview.md) | 
| [On-behalf-of](#on-behalf-of) | Egy alkalmazás elindítja az szolgáltatás/webes API-kat, amelynek be kell egy másik szolgáltatást vagy webes API meghívásához. A cél pedig propagálása a delegált felhasználó identitása és a kérelem láncot engedélyeket. | [Webes API-k](scenario-web-api-call-api-overview.md) |
| [Ügyfél-hitelesítő adatok](#client-credentials) | Lehetővé teszi webes tárolt erőforrások eléréséhez az alkalmazás identitását. Gyakran használt kiszolgálók közötti kapcsolatok definiálására, amelyeket futtatnia kell a háttérben, egy felhasználóval azonnali beavatkozás nélkül. | [démon alkalmazások](scenario-daemon-overview.md) |
| [Eszköz kód](#device-code) | Jelentkezzen be a bemeneti korlátozott eszközök, mint egy Okostelevízió, IoT-eszközből vagy nyomtató lehetővé teszi. | [Asztali és mobil alkalmazások](scenario-desktop-acquire-token.md#command-line-tool-without-web-browser) |
| [Integrált Windows-hitelesítés](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Lehetővé teszi, hogy a tartomány vagy az Azure AD-alkalmazásokat csatlakoztatott számítógépek csendes módban (a felhasználó felhasználói felület felhasználói beavatkozás) nélkül egy token beszerzéséhez.| [Asztali és mobil alkalmazások](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Felhasználónév/jelszó](scenario-desktop-acquire-token.md#username--password) | Lehetővé teszi olyan alkalmazások, hogy jelentkezzen be a felhasználó közvetlenül kezeli a jelszavát. Ez a folyamat nem ajánlott. | [Asztali és mobil alkalmazások](scenario-desktop-acquire-token.md#username--password) | 


## <a name="implicit-grant"></a>Implicit

Az MSAL támogatja a [OAuth 2 implicit adja meg a folyamat](v2-oauth2-implicit-grant-flow.md), amely lehetővé teszi az alkalmazásnak, hogy tokenekhez a Microsoft identity platform háttérkiszolgálóhoz végrehajtása nélkül hitelesítő adatok. Ez lehetővé teszi az alkalmazásnak, hogy jelentkezzen be a felhasználói munkamenet fenntartásához és tokenekhez más webes API-k mindezt az ügyfelet JavaScript-kódot.

![Implicit engedélyezési folyamat](media/msal-authentication-flows/implicit-grant.svg)

Számos modern webalkalmazások épülnek, mint a JavaScript- vagy például az Angularrel, oldalon a Vue.js és React.js SPA keretrendszer használatával írt ügyféloldali egyoldalas alkalmazások. Ezek az alkalmazások futtatásához egy webböngészőben, és különböző hitelesítési jellemzőit, mint a hagyományos kiszolgálóoldali webes alkalmazások. A Microsoft identity platform lehetővé teszi, hogy az egyoldalas alkalmazások a felhasználók és a Háttérszolgáltatásokhoz vagy webes API-t az implicit engedélyezési folyamat eléréséhez tokenekhez. Az implicit folyamatot lehetővé teszi, hogy az alkalmazás és a hitelesített felhasználó is elérhetik a védett API-k meghívásához szükséges jogkivonatok azonosító tokenekhez.

A hitelesítési folyamat nem tartalmazza az alkalmazás-forgatókönyvek használatával Electron és a React-natív, többplatformos JavaScript keretrendszereket használó, mivel igényelnek további képességek a natív platformokhoz való együttműködéshez szükséges.

## <a name="authorization-code"></a>Engedélyezési kód
MSAL támogatja a [OAuth 2 engedélyezési kód](v2-oauth2-auth-code-flow.md), amelyek használhatók az alkalmazást, amely egy eszközön a védett erőforrások, például a webes API-k eléréséhez. Ez lehetővé teszi, hogy jelentkezzen be, és az API eléréséhez a mobil- és asztali alkalmazásokhoz. 

Felhasználói bejelentkeznek a webes alkalmazások (webhelyek), amikor a webalkalmazás kap egy engedélyezési kód.  Az engedélyezési kódot a webes API-k hívásához egy token beszerzéséhez válthatók be. Az ASP.NET / ASP.NET core web apps szolgáltatásban, az egyetlen célja `AcquireTokenByAuthorizationCode` egy token hozzáadása az tokengyorsítótárral, úgy, hogy ezután használható az alkalmazás (általában a vezérlő), amely egyszerűen lekérheti az API-t a token által `AcquireTokenSilent`.

![Az engedélyezési kód folyamata](media/msal-authentication-flows/authorization-code.png)

### <a name="considerations"></a>Megfontolandó szempontok
- Az engedélyezési kód az használható csak egyszer egy jogkivonat beváltása. Ne próbáljon többször, ugyanazt a hitelesítési kódot (kifejezetten tilos a szabványos protokoll-meghatározása szerint) egy token beszerzéséhez. Ha a webhelyen történő beváltása a kód többször szándékosan, vagy mert nem vegye figyelembe, hogy egy keretrendszert is mindezt megteszi Ön helyett, hibaüzenetet kap: `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Ha egy ASP.NET/ASP.NET Core-alkalmazást, ez akkor fordulhat, don't tell ASP.NET/Core keretében, ha már beváltott az engedélyezési kód. Ehhez meg kell hívnia `context.HandleCodeRedemption()` módszere a `AuthorizationCodeReceived` eseménykezelő.

- Kerülje a hozzáférési jogkivonat megosztását az ASP.NET-tel, ami megakadályozhatja a növekményes hozzájárulási megfelelően történik.  További információkért lásd: [#693 kiadása](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693).

## <a name="on-behalf-of"></a>On-behalf-of

Az MSAL támogatja a [OAuth 2--meghatalmazásos hitelesítési folyamat](v2-oauth2-on-behalf-of-flow.md).  Ez a folyamat akkor használja, ha egy alkalmazás elindítja a szolgáltatás/webes API-kat, amelynek be kell egy másik szolgáltatást vagy webes API meghívásához. A cél pedig propagálása a delegált felhasználó identitása és a kérelem láncot engedélyeket. A középső rétegű szolgáltatás hitelesített kéréseket küld az alárendelt szolgáltatás biztonságos hozzáférési tokent a Microsoft identity platform, a felhasználó nevében kell.

![Meghatalmazásos folyamat](media/msal-authentication-flows/on-behalf-of.png)

1. Egy hozzáférési jogkivonatot szerez a webes API-hoz
2. Egy ügyfél (webes, mobil-, asztali,-egyoldalas alkalmazás) meghívja a védett webes API, a hozzáférési jogkivonat hozzáadása a HTTP-kérés hitelesítési fejléc tulajdonosi jogkivonattal. A webes API hitelesíti a felhasználót.
3. Amikor az ügyfél meghívja a webes API, a webes API kérelmek--meghatalmazásos egy másik jogkivonatot a felhasználónak.  
4. A védett webes API alsóbb rétegbeli webes API hívása a alapú meghatalmazásos a felhasználó ezt a tokent használja.  A webes API később is kérhetnek jogkivonatok más alsóbb rétegbeli API-k (de még mindig ugyanaz a felhasználó nevében).

## <a name="client-credentials"></a>Ügyfél-hitelesítő adatok

Az MSAL támogatja a [OAuth 2 ügyfél hitelesítő adatai a flow](v2-oauth2-client-creds-grant-flow.md). Ez a folyamat lehetővé teszi webes tárolt erőforrások eléréséhez az alkalmazás identitását. Támogatás az ilyen kiszolgálók közötti kapcsolatok definiálására, amelyeket futtatnia kell a háttérben, egy felhasználóval azonnali beavatkozás nélkül gyakran használják. Ilyen típusú alkalmazások gyakran nevezzük démonok vagy szolgáltatásfiókokat. 

Az ügyfél hitelesítő adatainak megadása a folyamat lehetővé teszi egy webszolgáltatás (bizalmas ügyfél) a saját hitelesítő adatait, használja a felhasználó megszemélyesítése helyett egy másik webszolgáltatás hívásakor. Ebben a forgatókönyvben az ügyfél nem általában egy középső rétegű webszolgáltatás, egy démon, illetve egy webhelyen. A magasabb szintű megbízhatóságra a Microsoft identity platform is lehetővé teszi, hogy a hívó szolgáltatás (és nem egy közös titkos kulcsot) egy tanúsítvány használandó hitelesítő adatokat.

> [!NOTE]
> A bizalmas client flow nem érhető mobilplatformokon (UWP Xamarin.iOS és Xamarin.Android), mivel ezek az csak a nyilvános ügyfélalkalmazások támogatják.  Nyilvános ügyfélalkalmazások nem tudom, hogyan igazolja az alkalmazás identitását segítségével az Identitásszolgáltatókhoz. Biztonságos kapcsolat érhető el a webalkalmazás vagy webes API-t háttérrendszereket tanúsítvány telepítésével.

MSAL.NET ügyfél-hitelesítő adatok három típusát támogatja:

- Titkos alkalmazáskulcsok <BR>![Bizalmas ügyfél felhasználónévvel és jelszóval](media/msal-authentication-flows/confidential-client-password.png)
- Tanúsítványok <BR>![A tanúsítvány bizalmas ügyfél](media/msal-authentication-flows/confidential-client-certificate.png)
- Optimalizált ügyfél helyességi feltételek<BR>![A helyességi feltételek bizalmas ügyfél](media/msal-authentication-flows/confidential-client-assertions.png)

Ezen ügyfél hitelesítő adatait kell lennie:

- Az Azure ad-vel regisztrálva.
- Az átadott, a kódban a bizalmas ügyfélalkalmazás felépítése.


## <a name="device-code"></a>Eszköz kód
Támogatja az MSAL az [OAuth 2 eszköz kódfolyamat](v2-oauth2-device-code.md), amely lehetővé teszi, hogy a felhasználók számára, hogy jelentkezzen be a bemeneti korlátozott eszközök, mint egy Okostelevízió, IoT-eszközből vagy nyomtató. Interaktív hitelesítés az Azure ad-vel egy webböngésző szükséges. Az eszköz kódfolyamat lehetővé teszi, hogy a felhasználó egy másik eszközt (például egy másik számítógépre vagy egy mobiltelefon) használatával jelentkezzen be, ahol az eszköz vagy az operációs rendszer nem adja meg egy webböngészőben.

Az alkalmazás az eszköz hitelesítésikód-folyamata segítségével szerzi be a tokenek kifejezetten arra tervezték, ezek az eszközök/os kétlépéses folyamat. Ilyen alkalmazás például olyan iOT-eszközök vagy a parancssori eszközök (CLI) rendszeren futó alkalmazások. 

![Eszközkód folyamata](media/msal-authentication-flows/device-code.png)

1. Felhasználói hitelesítés szükség, amikor az alkalmazás egy kódot biztosít, és megkérdezi a felhasználót, nyissa meg egy URL-címet (például egy internethez csatlakozó okostelefon) egy másik eszköz használatával (például http://microsoft.com/devicelogin), ahol a felhasználó felszólítást kap a kódot. Hogy megtörtént, a weblap irányítja a felhasználót egy normál hitelesítési módszer, beleértve a beleegyezést kérő és a multi-factor Authentication hitelesítés, amennyiben szükséges keresztül.

2. Sikeres hitelesítést követően a parancssori alkalmazást fog kapni a szükséges jogkivonatok, biztonsági csatornán keresztül történik, és fogja használni, a szükséges API-hívások végrehajtásához.

### <a name="constraints"></a>Korlátozások

- Eszköz kódfolyamat csak nyilvános ügyfélalkalmazások számára érhető el.
- Amikor hozhat létre, amely a nyilvános ügyfélalkalmazás kell átadni a szolgáltató:
  - bérlős (a képernyő `https://login.microsoftonline.com/{tenant}/` ahol `{tenant}` vagy a bérlői azonosító vagy egy tartományhoz a tenanthoz társított képviselő GUID.
  - vagy bármely munkahelyi és iskolai fiókok (`https://login.microsoftonline.com/organizations/`).
- Személyes Microsoft-fiókok jelenleg nem támogatottak az Azure AD v2.0-végpont által (nem használható a `/common` vagy `/consumers` bérlők).

## <a name="integrated-windows-authentication"></a>Integrált Windows-hitelesítés
Az MSAL integrált Windows-hitelesítés (IWA) támogatja az asztali, vagy egy tartományhoz csatlakoztatottnak vagy az Azure ad-ben futó alkalmazásokat csatlakoztatott Windows-számítógépen. IWA használja, ezek az alkalmazások lekérheti a jogkivonat csendes módban (a felhasználó felhasználói felület felhasználói beavatkozás) nélkül. 

![Integrált Windows-hitelesítés](media/msal-authentication-flows/integrated-windows-authentication.png)

### <a name="constraints"></a>Korlátozások

Támogatja a IWA **az összevont** csak a felhasználókra.  A felhasználók az Active Directoryban létrehozott, és az Azure Active Directory által támogatott. A felhasználók közvetlenül az Azure AD-ben nélkül AD biztonsági hozott létre (**felügyelt** felhasználók) a hitelesítési folyamat nem használható. Ez a korlátozás nem befolyásolja a [felhasználónév/jelszó folyamat](#usernamepassword).

IWA olyan .NET-keretrendszer, a .NET Core és az univerzális Windows Platform-platformokhoz írt alkalmazásokat.

IWA nem kerüli a többtényezős hitelesítés (többtényezős hitelesítést). Ha a többtényezős hitelesítés van konfigurálva, IWA meghiúsulhat, ha az MFA-hitelesítést szükség, mert a többtényezős hitelesítés felhasználói beavatkozást igényel. Ez azonos a bonyolult feladat. IWA nem interaktív, de kétfaktoros engedélyezési (2FA) a felhasználó interaktivitási igényel. Nem, szabályozzák, ha az identitásszolgáltató kér 2FA kell elvégezni, a bérlői rendszergazda does. A megfigyelések a 2FA akkor szükség, jelentkezzen be más országban, ha nem kapcsolódnak VPN-en keresztül a vállalati hálózathoz, és néha még akkor is, ha a VPN-en keresztül kapcsolódik. Nem várt determinisztikus szabálykészletet, Azure Active Directory AI segítségével folyamatosan ismerje meg, ha 2FA megadása kötelező. Meg kell, hogy egy felhasználói kérés tartalék (https://aka.ms/msal-net-interactive) IWA meghibásodásakor.

Amikor hozhat létre, amely a nyilvános ügyfélalkalmazás kell átadni a szolgáltató:
- bérlős (a képernyő `https://login.microsoftonline.com/{tenant}/` ahol `tenant` vagy a bérlői azonosító vagy egy tartományhoz a tenanthoz társított képviselő GUID.
- a munkahelyi és iskolai fiókok (`https://login.microsoftonline.com/organizations/`). Személyes Microsoft-fiókok nem támogatottak (nem használhat `/common` vagy `/consumers` bérlők).

Mivel IWA beavatkozás nélküli folyamatok:
- az alkalmazás a felhasználó korábban hozzájárult kell, hogy használják az alkalmazást. 
- vagy a bérlői rendszergazda korábban hozzájárult kell használni az alkalmazást a bérlő minden felhasználója számára.
- Ez azt jelenti, hogy:
    - vagy a fejlesztők megnyomta a **Grant** gombra a szolgáltatást, az Azure Portalon 
    - vagy egy Bérlői rendszergazda rendelkezik pressed a **engedélyezéshez/visszavonáshoz rendszergazdai jóváhagyás {bérlői tartományhoz}** gombra a **API-engedélyek** a az alkalmazás regisztrációs lapján (lásd: [engedélyek hozzáadása hozzáférés a webes API-k](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis))
    - vagy, hogy engedélyt adjanak az alkalmazásnak a felhasználók a megadott (lásd: [egyedi felhasználói jóváhagyás kérése](v2-permissions-and-consent.md#requesting-individual-user-consent))
    - vagy, így a bérlői rendszergazdai jóváhagyást az alkalmazás a megadott (lásd: [rendszergazdai jóváhagyás](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant))

.NET asztali, a .NET Core és az univerzális Windows-Platformos alkalmazások engedélyezve van a IWA folyamatot. .NET Core-on csak a felhasználónév véve túlterhelés érhető el, a .NET Core platform nem kérje meg az operációs rendszer a felhasználónevet.
  
A jóváhagyás további információkért lásd: [v2.0 engedélyek és jóváhagyás](v2-permissions-and-consent.md).

## <a name="usernamepassword"></a>Felhasználónév/jelszó 
Az MSAL támogatja a [OAuth 2 erőforrás tulajdonosának jelszavas hitelesítése](v2-oauth-ropc.md), amely lehetővé teszi, hogy az alkalmazás közvetlenül kezeli a jelszavukat a felhasználói bejelentkezés. Az asztali alkalmazás használhatja a felhasználónév/jelszó folyamat egy token beszerzéséhez beavatkozás nélkül. Felhasználói felület nem szükséges, az alkalmazás használata során.

![Felhasználónév/jelszó folyamat](media/msal-authentication-flows/username-password.png)

> [!WARNING]
> Ez a folyamat **nem ajánlott** mert nagyfokú megbízhatóság és a felhasználó kitettség igényel.  Ez a folyamat csak akkor ajánlott, ha más, az biztonságosabb, a folyamatok nem használható. A problémáról további információk: [Ez a cikk](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Előnyben részesített tokenbeolvasás csendes módban a Windows-tartományhoz csatlakoztatott gépeket folyamatábrája [integrált Windows-hitelesítés](#integrated-windows-authentication). Ellenkező esetben használhatja [eszköz kódfolyamat](#device-code)

Bár ez hasznos, ha bizonyos esetekben (DevOps-forgatókönyvekre), ha azt szeretné, felhasználónév/jelszó használata az interaktív forgatókönyvek, ahol meg kell a saját felhasználói felület, valóban gondolja dobozból áthelyezése. Felhasználónév/jelszó használatával, vannak megadva felfelé több minden:
- Alapszintű bérlők modern identitás: jelszó fogott lekérdezi, játssza vissza. Mert a fogalom, amely megszerezhetik megosztás titkos kód.
Ez az beállításának kompatibilis.
- felhasználók, akik kell tennie a többtényezős hitelesítés nem lesz lehetősége bejelentkezni (mivel nem lett a beavatkozás nélküli)
- Felhasználók nem tudják az egyszeri bejelentkezés

### <a name="constraints"></a>Korlátozások

Kivéve a [integrált Windows-hitelesítés megkötések](#integrated-windows-authentication), a következő korlátozások is érvényesek:

- A felhasználónév/jelszó flow jelenleg nem kompatibilis a feltételes hozzáférés és a multi-factor authentication szolgáltatás: Következtében ha az alkalmazás fut az Azure AD-bérlővel, ahol a bérlői rendszergazda multi-factor Authentication hitelesítést igényel, nem használhatja ezt a folyamatot. Számos szervezet megtenni.
- Működés csak a munkahelyi és iskolai fiókok (nem MSA)
- A folyamat .NET asztali és a .NET core, de nem univerzális Windows Platform érhető el.

### <a name="azure-ad-b2c-specifics"></a>Az Azure AD B2C-vel tulajdonságairól

További információk a MSAL.NET és az Azure AD B2C-vel, [ROPC használata az Azure AD B2C-vel (MSAL.NET)](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c).