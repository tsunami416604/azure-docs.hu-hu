---
title: ADAL a MSAL migrációs útmutató (MSAL4j) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan telepítheti át az Azure Active Directory hitelesítési könyvtár (ADAL) Java alkalmazását a Microsoft authentication library (MSAL).
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Java
ms.workload: identity
ms.date: 11/04/2019
ms.author: sagonzal
ms.reviewer: nacanuma, twhitney
ms.custom: aaddev
ms.openlocfilehash: 2929b94a2cb624b96649292714fe93dea09a2085
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886500"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>ADAL a MSAL migrációs útmutató java

Ez a cikk kiemeli azokat a módosításokat, amelyeket az Azure Active Directory hitelesítési könyvtár (ADAL) a Microsoft authentication library (MSAL) használatához használó alkalmazás áttelepítéséhez szükséges módosításokat kell végrehajtania.

A Microsoft Authentication Library for Java (MSAL4J) és az Azure AD Authentication Library for Java (ADAL4J) az Azure AD-entitások hitelesítésére és az Azure AD-től származó jogkivonatkérések reklamációjával szolgál. Eddig a legtöbb fejlesztő az Azure AD for developers platformmal (1.0-s) együttműködve hitelesítette az Azure AD-identitásokat (munkahelyi és iskolai fiókokat) az Azure AD hitelesítési könyvtár (ADAL) használatával történő jogkivonatok kérésével.

Az MSAL a következő előnyöket kínálja:

- Mivel az újabb Microsoft-identitásplatform-végpontot használja, hitelesítheti a Microsoft-identitások szélesebb körét, például az Azure AD-identitásokat, a Microsoft-fiókokat, valamint a közösségi és helyi fiókokat az Azure AD Business to Consumer (B2C) segítségével.
- A felhasználók a legjobb egyszeri bejelentkezési élményt kapják.
- Az alkalmazás engedélyezheti a növekményes jóváhagyást, és a feltételes hozzáférés támogatása egyszerűbb.

Az MSAL java-hoz az auth könyvtár, amelyet a Microsoft identity platformmal szeretne használni. Az ADAL4J-n nem lesznek új funkciók. Minden további erőfeszítés az MSAL javítására összpontosít.

## <a name="differences"></a>Eltérések

Ha már dolgozik az Azure AD fejlesztőknek (v1.0) végpont (és ADAL4J), érdemes lehet olvasni [Mi a különbség a Microsoft identity platform (v2.0) végpont?](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)

## <a name="scopes-not-resources"></a>Hatókörök nem erőforrások

Az ADAL4J jogkivonatokat szerez be az erőforrásokhoz, míg az MSAL java-hoz a hatókörök tokenjeit szerzi be. Számos Java-osztályhoz tartozó MSAL-osztály hoz szükség hatókör-paraméterre. Ez a paraméter azoknak a karakterláncoknak a listája, amelyek deklarálják a kért engedélyeket és erőforrásokat. Tekintse meg [a Microsoft Graph hatóköreit](https://docs.microsoft.com/graph/permissions-reference) a példaható körök megtekintéséhez.

## <a name="core-classes"></a>Alaposztályok

Az ADAL4J-ben az osztály a `AuthenticationContext` biztonsági jogkivonat-szolgáltatással (STS) vagy az engedélyezési kiszolgálóval létesített kapcsolatot jelöli egy hatóságon keresztül. Az MSAL for Java azonban az ügyfélalkalmazások köré épül. Két külön osztályt `PublicClientApplication` biztosít: és `ConfidentialClientApplication` az ügyfélalkalmazásokat.  Ez utóbbi `ConfidentialClientApplication`, egy olyan alkalmazást jelöl, amely egy titkos, például egy démonalkalmazás alkalmazásazonosítójának biztonságos megőrzésére szolgál.

Az alábbi táblázat bemutatja, hogyan térképezhető le az ADAL4J függvények az új MSAL java függvényekhez:

| ADAL4J módszer| MSAL4J módszer|
|------|-------|
|acquireToken(Karakterlánc-erőforrás, Ügyfélcredential hitelesítő adatok, AuthenticationCallback visszahívás) | acquireToken(ClientCredentialParameters)|
|acquireToken(Karakterlánc-erőforrás, ClientAssertion feltételállítás, AuthenticationCallback visszahívás)|acquireToken(ClientCredentialParameters)|
|acquireToken(Karakterlánc-erőforrás, AsymmetricKeyCredential hitelesítő adatok, AuthenticationCallback visszahívás)|acquireToken(ClientCredentialParameters)|
|acquireToken(Karakterlánc-erőforrás, Karakterlánc-ügyfélazonosító, Karakterlánc-felhasználónév, Karakterlánc-jelszó, AuthenticationCallback visszahívás)| acquireToken(FelhasználónévPasswordParameters)|
|acquireToken(Karakterlánc-erőforrás, Karakterlánc-ügyfélazonosító, Karakterlánc-felhasználónév, Karakterlánc jelszó=null, AuthenticationCallback visszahívás)|acquireToken(IntegratedWindowsAuthenticationParameters)|
|acquireToken(Karakterlánc-erőforrás, UserAssertion userAssertion, Ügyfélcredential hitelesítő adatok, AuthenticationCallback visszahívás)| acquireToken(OnBehalfOfParameters)|
|acquireTokenByAuthorizationCode() | acquireToken(AuthorizationCodeParameters) |
| acquireDeviceCode() és acquireTokenByDeviceCode()| acquireToken(DeviceCodeParameters)|
|acquireTokenByRefreshToken()| acquireTokenSilently(SilentParameters)|

## <a name="iaccount-instead-of-iuser"></a>IAccount az IUser helyett

ADAL4J manipulált a felhasználók számára. Bár a felhasználó egyetlen emberi vagy szoftverügynököt képvisel, egy vagy több fiókkal rendelkezhet a Microsoft identitásrendszerben. Például egy felhasználó több Azure AD, Azure AD B2C vagy Microsoft személyes fiókkal rendelkezhet.

MSAL java határozza meg a koncepció `IAccount` a számla a felületen keresztül. Ez egy törés változás ADAL4J, de ez egy jó, mert rögzíti azt a tényt, hogy ugyanaz a felhasználó több fiókkal rendelkezik, és talán még a különböző Azure AD-könyvtárak. Az MSAL for Java jobb információkat nyújt a vendégesetekben, mivel az otthoni fiók adatai meg vannak adva.

## <a name="cache-persistence"></a>Gyorsítótár-megőrzés

Az ADAL4J nem támogatta a tokengyorsítótárat.
Az MSAL for Java [egy jogkivonat-gyorsítótárat](msal-acquire-cache-tokens.md) ad hozzá a tokenélettartamok kezelésének egyszerűsítéséhez azáltal, hogy lehetőség szerint automatikusan frissíti a lejárt jogkivonatokat, és megakadályozza a szükségtelen kérések megadását a felhasználó számára, hogy szükség esetén adja meg a hitelesítő adatokat.

## <a name="common-authority"></a>Közös Hatóság

Az 1.0-s vagyoni, ha a jogosultsággal, a `https://login.microsoftonline.com/common` felhasználók bejelentkezhetnek bármely Azure Active Directory (AAD) fiók (bármely szervezet számára).

Ha a `https://login.microsoftonline.com/common` 2.0-s verzióban használja a jogosultságot, a felhasználók bármely AAD-szervezetnél, vagy akár egy Microsoft-személyes fiókkal (MSA) is bejelentkezhetnek. Az MSAL java-hoz, ha a bejelentkezést bármely AAD-fiókra `https://login.microsoftonline.com/organizations` szeretné korlátozni, akkor a jogosultságot kell használnia (amely ugyanaz a viselkedés, mint az ADAL4J esetében). Hatóság megadásához állítsa `authority` be a paramétert a [PublicClientApplication.Builder](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html) metódusban az `PublicClientApplication` osztály létrehozásakor.

## <a name="v10-and-v20-tokens"></a>1.0-s és 2.0-s jogkivonatok

A v1.0 végpont (aDal által használt) csak az 1.0-s jogkivonatokat bocsátja ki.

A v2.0 végpont (az MSAL által használt) képes kibocsátani a v1.0 és a v2.0 tokenek. A webes API alkalmazásjegyzék-tulajdonsága lehetővé teszi a fejlesztők számára, hogy kiválasszák a token elfogadott verzióját. Lásd `accessTokenAcceptedVersion` az [alkalmazás jegyzékfájl](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) hivatkozási dokumentációjában.

Az 1.0-s és a 2.0-s v2.0-s jogkivonatokról az [Azure Active Directory-hozzáférési jogkivonatok](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)című témakörben talál további információt.

## <a name="adal-to-msal-migration"></a>ADAL a MSAL migráció

Az ADAL4J-ben a frissítési jogkivonatok ki voltak téve – ami lehetővé tette a fejlesztők számára, hogy gyorsítótárazza őket. Ezután olyan `AcquireTokenByRefreshToken()` megoldások at engedélyeznek, mint például a hosszú ideig futó szolgáltatások megvalósítása, amelyek frissítik az irányítópultokat a felhasználó nevében, ha a felhasználó már nem csatlakozik.

Az MSAL java-hoz biztonsági okokból nem teszi elérhetővé a frissítési jogkivonatokat. Ehelyett az MSAL kezeli a frissítő tokeneket.

Az MSAL for Java api-val rendelkezik, amely lehetővé teszi az ADAL4j-vel beszerzett frissítési tokenek áttelepítését a ClientApplication: [acquireToken(RefreshTokenParameters)](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-)alkalmazásba. Ezzel a módszerrel biztosíthatja a korábban használt frissítési jogkivonatot a kívánt hatókörökkel (erőforrásokkal) együtt. A frissítési jogkivonatot a rendszer kicseréli egy újra, és gyorsítótárba helyezi az alkalmazás számára.

A következő kódrészlet néhány áttelepítési kódot mutat be egy bizalmas ügyfélalkalmazásban:

```java
String rt = GetCachedRefreshTokenForSIgnedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

A `IAuthenticationResult` visszaad egy hozzáférési jogkivonatot és egy azonosító tokent, míg az új frissítési jogkivonat a gyorsítótárban van tárolva. Az alkalmazás most már tartalmaz egy IAccount:

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

A gyorsítótárban lévő tokenek használatához hívja meg a következőket:

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build(); 
IAuthenticationResult result = app.acquireToken(parameters);
```
