---
title: ADAL a MSAL áttelepítési útmutató Javához | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan telepítheti át Azure Active Directory Authentication Library (ADAL) Java-alkalmazását a Microsoft Authentication Library-be (MSAL).
services: active-directory
author: sangonzal
manager: henrikm
editor: twhitney
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: Java
ms.workload: identity
ms.date: 11/4/2019
ms.author: sagonzal
ms.reviewer: navyari.canumalla
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 158cbe4f2598722abe3b2ed3a0dd179a21dc1e1c
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74145270"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>ADAL a MSAL áttelepítési útmutató Javához

Ez a cikk azokat a módosításokat mutatja be, amelyeket a Azure Active Directory hitelesítési függvénytárat (ADAL) használó alkalmazások áttelepíteni kell a Microsoft Authentication Library (MSAL) használatára.

A Microsoft Authentication Library for Java (MSAL4J) és a Javához készült Azure AD Authentication Library (ADAL4J) is az Azure AD-entitások hitelesítésére és az Azure AD-jogkivonatok igénylésére szolgál. Eddig a legtöbb fejlesztő dolgozott együtt az Azure ad for Developers platformmal (v 1.0) az Azure AD-identitások (munkahelyi és iskolai fiókok) hitelesítéséhez az Azure AD Authentication Library (ADAL) használatával.

A MSAL a következő előnyöket kínálja:

- Mivel az újabb Microsoft Identity platform-végpontot használja, a Microsoft-identitások szélesebb körét hitelesítheti, például az Azure ad-identitásokat, a Microsoft-fiókokat, valamint a közösségi és helyi fiókokat az Azure AD Business to Consumer (B2C) szolgáltatáson keresztül.
- a felhasználók a legjobb egyszeri bejelentkezési élményt kapják meg.
- Az alkalmazás lehetővé teszi a növekményes hozzáférést, és megkönnyíti a feltételes hozzáférés támogatását.

A MSAL for Java (MSAL4J) a hitelesítési függvénytár, amelyet a Microsoft Identity platformmal való használatra ajánlott használni. A ADAL4J-on nem lesznek új funkciók implementálva. Minden, a jövőre irányuló erőfeszítés a MSAL javítására koncentrál.

## <a name="differences"></a>Különbségek

Ha az Azure AD for Developers (1.0) végpont (és a ADAL4J) használatával dolgozik, érdemes elolvasnia, hogy [Mi a különbség a Microsoft Identity platform (v 2.0) végpontján?](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison).

## <a name="scopes-not-resources"></a>Hatókörök nem erőforrásai

A ADAL4J az erőforrások tokeneit szerzi be, míg a MSAL4J a hatókörökhöz tartozó jogkivonatokat. Számos MSAL4J-osztályhoz hatókör-paraméter szükséges. Ez a paraméter a szükséges engedélyeket és erőforrásokat deklaráló karakterláncok listája. Tekintse [meg a Microsoft Graph hatókörét](https://docs.microsoft.com/graph/permissions-reference) a példa hatókörök megjelenítéséhez.

## <a name="core-classes"></a>Alapvető osztályok

A ADAL4J-ben a `AuthenticationContext` osztály a biztonsági jogkivonat szolgáltatással (STS) vagy az engedélyezési kiszolgálóval létesített kapcsolatát jelenti a szolgáltatón keresztül. A MSAL4J azonban ügyfélalkalmazások számára lett kialakítva. Két különálló osztályt biztosít: `PublicClientApplication` és `ConfidentialClientApplication` az ügyfélalkalmazások képviseletére.  Az utóbbi, `ConfidentialClientApplication`egy olyan alkalmazást képvisel, amely biztonságos karbantartást, például egy Daemon-alkalmazás alkalmazás-azonosítóját jelöli.

A következő táblázat bemutatja, hogyan képezi le a ADAL4J functions az új MSAL4J-függvényeket:

| ADAL4J metódus| MSAL4J metódus|
|------|-------|
|acquireToken (karakterlánc-erőforrás, ClientCredential hitelesítő adat, AuthenticationCallback visszahívás) | acquireToken(ClientCredentialParameters)|
|acquireToken (karakterlánc-erőforrás, ClientAssertion-érvényesítés, AuthenticationCallback visszahívás)|acquireToken(ClientCredentialParameters)|
|acquireToken (karakterlánc-erőforrás, AsymmetricKeyCredential hitelesítő adat, AuthenticationCallback visszahívás)|acquireToken(ClientCredentialParameters)|
|acquireToken (karakterlánc-erőforrás, karakterlánc clientId, karakterlánc felhasználóneve, karakterlánc jelszava, AuthenticationCallback visszahívás)| acquireToken(UsernamePasswordParameters)|
|acquireToken (karakterlánc-erőforrás, karakterlánc clientId, karakterlánc felhasználóneve, karakterlánc jelszava = null, AuthenticationCallback visszahívás)|acquireToken(IntegratedWindowsAuthenticationParameters)|
|acquireToken (karakterlánc-erőforrás, UserAssertion userAssertion, ClientCredential hitelesítő adat, AuthenticationCallback visszahívás)| acquireToken(OnBehalfOfParameters)|
|acquireTokenByAuthorizationCode() | acquireToken(AuthorizationCodeParameters) |
| acquireDeviceCode () és acquireTokenByDeviceCode ()| acquireToken(DeviceCodeParameters)|
|acquireTokenByRefreshToken()| acquireTokenSilently(SilentParameters)|

## <a name="iaccount-instead-of-iuser"></a>IAccount helyett IUser

ADAL4J manipulált felhasználók. Bár a felhasználók egyetlen emberi vagy szoftveres ügynököt jelölnek, a Microsoft Identity rendszer egy vagy több fiókja is lehet. Előfordulhat például, hogy egy felhasználó több Azure AD-, Azure AD B2C-vagy személyes Microsoft-fiókkal rendelkezik.

A MSAL4J a `IAccount` felületen keresztül határozza meg a fiók fogalmát. Ez egy ADAL4J-változás, de ez egy jó megoldás, mivel ez azt a tényt rögzíti, hogy ugyanaz a felhasználó több fiókkal is rendelkezhet, és talán akár különböző Azure AD-címtárakban is. A MSAL4J jobb információkat biztosít a vendég forgatókönyvekhez, mivel a rendszer megadja az otthoni fiók adatait.

## <a name="cache-persistence"></a>Gyorsítótár-megőrzés

A ADAL4J nem támogatta a jogkivonat-gyorsítótárat.
A MSAL4J hozzáadja a jogkivonat- [gyorsítótárat](msal-acquire-cache-tokens.md) a jogkivonatok élettartamának kezeléséhez a lejárt tokenek automatikus frissítésével, amikor lehetséges, és megakadályozza, hogy a felhasználó a hitelesítő adatok megadását a lehető legpontosabban adja meg.

## <a name="common-authority"></a>Általános szolgáltató

1\.0-s verzióban, ha a `https://login.microsoftonline.com/common`-szolgáltatót használja, a felhasználók bármilyen Azure Active Directory-(HRE-) fiókkal bejelentkezhetnek (bármely szervezet esetében).

Ha a `https://login.microsoftonline.com/common`-szolgáltatót használja a 2.0-s verzióban, a felhasználók bármely HRE-szervezettel, vagy akár a Microsoft személyes fiókjával (MSA) is bejelentkezhetnek. Ha a MSAL4J bármely HRE-fiókra korlátozni szeretné a bejelentkezést, akkor a `https://login.microsoftonline.com/organizations`-szolgáltatót kell használnia (ez ugyanaz, mint a ADAL4J esetében). A szolgáltató megadásához állítsa a `authority` paramétert a [PublicClientApplication. Builder](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html) metódusban a `PublicClientApplication` osztály létrehozásakor.

## <a name="v10-and-v20-tokens"></a>1\.0-s és v 2.0-tokenek

A ADAL által használt v 1.0 végpont csak v 1.0 jogkivonatokat bocsát ki.

A v 2.0-s végpont (a MSAL által használt) 1.0-s és v 2.0-tokeneket bocsát ki. A webes API alkalmazási jegyzékfájljának egyik tulajdonsága lehetővé teszi a fejlesztők számára, hogy a jogkivonat melyik verzióját fogadják el. Tekintse meg `accessTokenAcceptedVersion` az [alkalmazás jegyzékfájljának](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) dokumentációjában.

A 1.0-s és a 2.0-s verziókkal kapcsolatos további információkért lásd: [Azure Active Directory hozzáférési tokenek](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).

## <a name="adal-to-msal-migration"></a>ADAL a MSAL áttelepítéséhez

A ADAL4J a frissítési jogkivonatok elérhetők – ami lehetővé tette a fejlesztők számára a gyorsítótárazást. Ezután a `AcquireTokenByRefreshToken()` használatával engedélyezhetik a megoldásokat, például olyan hosszan futó szolgáltatásokat implementálnak, amelyek a felhasználó nevében frissítik az irányítópultokat, ha a felhasználó már nincs csatlakoztatva.

A MSAL4J biztonsági okokból nem teszi elérhetővé a frissítési jogkivonatokat. Ehelyett a MSAL kezeli a frissítő tokeneket.

A MSAL4J rendelkezik egy olyan API-val, amely lehetővé teszi a ADAL4j-ben szerzett frissítési tokenek áttelepíthetők a ClientApplication: [acquireToken (RefreshTokenParameters)](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-). Ezzel a módszerrel megadhatja a korábban használt frissítési jogkivonatot a kívánt hatókörökkel (erőforrásokkal) együtt. A frissítési tokent egy újat cseréli a rendszer, és az alkalmazás által használt gyorsítótárazza.

Az alábbi kódrészlet egy bizalmas ügyfélalkalmazás áttelepítési kódját mutatja be:

```java
String rt = GetCachedRefreshTokenForSIgnedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

A `IAuthenticationResult` egy hozzáférési jogkivonatot és egy azonosító jogkivonatot ad vissza, míg az új frissítési jogkivonat a gyorsítótárban tárolódik. Az alkalmazás ekkor is tartalmaz egy IAccount:

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

A gyorsítótárban lévő tokenek használatához hívja a következőt:

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build(); 
IAuthenticationResult result = app.acquireToken(parameters);
```
