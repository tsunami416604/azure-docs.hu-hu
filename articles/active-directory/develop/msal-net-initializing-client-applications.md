---
title: Inicializálja az ügyfélalkalmazások (Microsoft-hitelesítési tár .NET) |} Az Azure
description: További információ a nyilvános ügyfél és a bizalmas ügyfélalkalmazások használatával a Microsoft-hitelesítési tár .NET (MSAL.NET) inicializálása közben.
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
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f22ff41e380a16af2aa45df9a61eefbf293ff83
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544319"
---
# <a name="initialize-client-applications-using-msalnet"></a>Az ügyfélalkalmazások MSAL.NET használatával inicializálása
Ez a cikk ismerteti az ügyfél nyilvános és Microsoft-hitelesítési tár .NET (MSAL.NET) használatával bizalmas ügyfélalkalmazások inicializálása.  Az ügyfél alkalmazástípusok és alkalmazáskonfigurációs beállítások kapcsolatos további információkért olvassa el a [áttekintése](msal-client-applications.md).

Az MSAL.NET 3.x, az ajánlott módszer a példányt létrehozni egy alkalmazást az alkalmazás kapcsolat építői használatával van: `PublicClientApplicationBuilder` és `ConfidentialClientApplicationBuilder`. Az alkalmazás a kódot, vagy egy konfigurációs fájlból, vagy akár mindkét módszert úgy konfigurálhatja egy hatékony mechanizmust kínálnak.

## <a name="prerequisites"></a>Előfeltételek
Alkalmazás inicializálása, előtt először létre kell [regisztrálja](quickstart-register-app.md) úgy, hogy az alkalmazás integrálható a Microsoft identitásplatformjához.  A regisztrációt követően szükség lehet a következő információkat (amely az Azure Portalon található):

- Az ügyfél-Azonosítót (GUID képviselő karakterláncot)
- Az identitásszolgáltató szolgáltató URL-címe (a példány neve) és az alkalmazás bejelentkezési célközönség. E két paraméter együttesen hatóságként ismert.
- A bérlő Azonosítóját, ha egy üzletági alkalmazás kizárólag a szervezetben (egybérlős alkalmazás elnevezett is ismert).
- Az alkalmazás titkos kulcs (ügyfél titkos karakterlánc) vagy a tanúsítvány (típusú X509Certificate2) egy bizalmas ügyfél alkalmazás esetén.
- A web apps, és egyes esetekben a nyilvános ügyfélalkalmazások (különösen ha az alkalmazás egy közvetítő) fog is beállította a redirectUri, lépjen kapcsolatba az identitásszolgáltató vissza az alkalmazás a biztonsági jogkivonatokkal.

## <a name="ways-to-initialize-applications"></a>Alkalmazások inicializálása módjai
Nincsenek számos különböző módon az ügyfélalkalmazások elindítását.

### <a name="initializing-a-public-client-application-from-code"></a>Egy nyilvános ügyfélalkalmazás kódból inicializálása

Az alábbi kód példányosít egy nyilvános ügyfélalkalmazás aláírási a felhasználók a Microsoft Azure nyilvános felhő, a munkahelyi és iskolai fiókokhoz vagy személyes Microsoft-fiókjukkal.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>A kód egy bizalmas ügyfélalkalmazás inicializálása

Ugyanolyan módon, a következő kódot a bizalmas alkalmazások példányosítja (található webalkalmazás `https://myapp.azurewebsites.net`) kezelése a Microsoft Azure nyilvános felhőben, a munkahelyi és iskolai fiókokhoz vagy személyes Microsoft-fiókjukkal felhasználók származó jogkivonatokat. Az alkalmazás megosztása ügyfélkódot, amelynél az identitásszolgáltató:

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

Mivel előfordulhat, hogy tudja, éles környezetben, hanem egy ügyfélkulcs használatával, előfordulhat, hogy megosztani kívánt Azure AD-vel egy tanúsítványt. A kód a következő lesz:

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>Egy nyilvános ügyfélalkalmazás konfigurációs lehetőség közül inicializálása

Az alábbi kód példányosít egy nyilvános ügyfélalkalmazást egy konfigurációs objektumot, amelyek ki vannak töltve a programozott módon vagy egy konfigurációs fájl olvasásakor:

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>Konfigurációs lehetőség közül bizalmas ügyfélalkalmazás inicializálása

A minta ugyanolyan típusú bizalmas ügyfélalkalmazások vonatkozik. Azt is megteheti a többi paraméter használatával `.WithXXX` dostupnosti (itt egy tanúsítványt).

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>A jelentéskészítő dostupnosti

Az alkalmazás sikerei, számos kódtöredékekre `.With` módszereit lehet alkalmazni, dostupnosti (például `.WithCertificate` és `.WithRedirectUri`). 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>Nyilvános és a bizalmas ügyfélalkalmazások közös dostupnosti

A állíthat be egy nyilvános ügyfél vagy a bizalmas ügyfél alkalmazás builder módosítók a következők:

|Paraméter | Leírás|
|--------- | --------- |
|`.WithAuthority()` 7 felülbírálások | Az alkalmazás alapértelmezett szolgáltató beállítása az Azure AD-szolgáltatót a lehetőséget választja, az Azure Cloud, a közönség a bérlő (bérlői azonosító vagy a tartomány neve), vagy közvetlenül a hitelesítésszolgáltatói URI megadása.|
|`.WithAdfsAuthority(string)` | Az alkalmazás alapértelmezett-kezelő szolgáltatóként az ADFS-szolgáltató beállítása.|
|`.WithB2CAuthority(string)` | Beállítja az alkalmazás alapértelmezett-kezelő szolgáltatóként egy Azure AD B2C-szolgáltatót.|
|`.WithClientId(string)` | Felülbírálja az ügyfél-azonosítót.|
|`.WithComponent(string)` | Beállítja a tár nevét, az MSAL.NET használatával (a telemetriai adatok okok miatt). |
|`.WithDebugLoggingCallback()` | Neve a, ha az alkalmazás meghívja `Debug.Write` egyszerűen engedélyezése nyomkövetések hibakeresés. Lásd: [naplózás](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) további információt.|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | A rendszer küldi az összes hitelesítési kérés alkalmazás szintű további lekérdezési paraméterek megadása Ez a felülbírálható mindegyik token beszerzéséhez metódus szintjén (azonos `.WithExtraQueryParameters pattern`).|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | Lehetővé teszi a speciális forgatókönyvek, például egy HTTP-proxy vagy a kényszerített MSAL egy adott HttpClient használata (például az ASP.NET Core web apps és API-k) konfigurálása.|
|`.WithLogging()` | Nevű, ha az alkalmazás meghívja a nyomkövetések hibakeresést egy visszahívást. Lásd: [naplózás](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) további információt.|
|`.WithRedirectUri(string redirectUri)` | Felülbírálja az alapértelmezett átirányítási URI-t. Nyilvános ügyfélalkalmazások, esetén ez akkor lehet hasznos a forgatókönyveket a közvetítőn.|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | Beállítja a küldött telemetriai adatok elküldésére használatosak.|
|`.WithTenantId(string tenantId)` | Felülbírálja a bérlői azonosító vagy bérlői leírása.|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Xamarin.iOS-alkalmazásokhoz adott dostupnosti

A módosítók Xamarin.iOS a szerkesztő alkalmazás nyilvános ügyfél megadhatja azt a következők:

|Paraméter | Leírás|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Csak a Xamarin.iOS**: Beállítja a IOS-es főbb lánc biztonsági csoport (a gyorsítótár megőrzése).|

### <a name="modifiers-specific-to-confidential-client-applications"></a>Bizalmas ügyfélalkalmazások számára meghatározott dostupnosti

A állíthat be egy bizalmas ügyfél alkalmazás builder módosítók a következők:

|Paraméter | Leírás|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Beállítja a tanúsítványt, az alkalmazás az Azure AD-azonosító.|
|`.WithClientSecret(string clientSecret)` | Az alkalmazás Azure AD-vel azonosítja az ügyfél titkos (alkalmazásjelszót) beállítása.|

Ezek dostupnosti olyan kölcsönösen kizárják egymást. Ha is megad, az MSAL kivételt fogja kijelezni egy jelentéssel bíró kivétel.

### <a name="example-of-usage-of-modifiers"></a>Példa a használatra, dostupnosti

Tegyük fel, hogy az alkalmazás-e egy üzleti alkalmazás, amely csak a szervezet számára.  Majd írhat:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

Ha érdekes válik, hogy az országos felhők programozási most már rendelkezik egyszerűsített. Ha azt szeretné, az alkalmazás egy több-bérlős alkalmazás országos felhőben, is írható, például:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

Emellett van egy AD FS felülbírálást (AD FS 2019 jelenleg nem támogatott):
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Végül ha Ön egy Azure AD B2C-t fejlesztő, megadhatja a bérlő ehhez hasonló:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```
