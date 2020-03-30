---
title: Ügyfélalkalmazások inicializálása MSAL.NET | Azure
titleSuffix: Microsoft identity platform
description: Információ a nyilvános ügyfél- és bizalmas ügyfélalkalmazások inicializálásáról a Microsoft Authentication Library for .NET (MSAL.NET) segítségével.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/12/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 57ce6ab31421cd4016f7e204eeabce82f2f7e6a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083984"
---
# <a name="initialize-client-applications-using-msalnet"></a>Ügyfélalkalmazások inicializálása MSAL.NET
Ez a cikk a nyilvános ügyfél- és bizalmas ügyfélalkalmazások inicializálását ismerteti a Microsoft Authentication Library for .NET (MSAL.NET) használatával.  Ha többet szeretne megtudni az ügyfélalkalmazás típusairól és az alkalmazás konfigurációs beállításairól, olvassa el az [áttekintést.](msal-client-applications.md)

A MSAL.NET 3.x esetén az alkalmazások példányosításának ajánlott módja `PublicClientApplicationBuilder` az `ConfidentialClientApplicationBuilder`alkalmazásépítők használata: és . Kínálnak egy hatékony mechanizmust, hogy konfigurálja az alkalmazást akár a kódot, vagy egy konfigurációs fájl, vagy akár a keverés mindkét megközelítés.

## <a name="prerequisites"></a>Előfeltételek
Az alkalmazás inicializálása előtt először regisztrálnia kell [azt,](quickstart-register-app.md) hogy az alkalmazás integrálható legyen a Microsoft identitásplatformral.  A regisztráció után a következő információkra lehet szüksége (amelyek az Azure Portalon találhatók):

- Az ügyfélazonosító (guid azonosítót képviselő karakterlánc)
- Az identitásszolgáltató URL-címe (a példány neve) és az alkalmazás bejelentkezési közönsége. Ezt a két paramétert együttesen hatóságnak nevezzük.
- A bérlőazonosító, ha egy üzleti alkalmazást kizárólag a szervezet (más néven egy-bérlős alkalmazás) ír.
- Az alkalmazás titkos (ügyféltitkos karakterlánc) vagy tanúsítvány (X509Certificate2 típus), ha bizalmas ügyfélalkalmazásról van szó.
- A webalkalmazások, és néha a nyilvános ügyfélalkalmazások (különösen, ha az alkalmazás kell használni a bróker), akkor is be kell állítania a redirectUri, ahol az identitásszolgáltató kapcsolatba lép az alkalmazás a biztonsági jogkivonatok.

## <a name="ways-to-initialize-applications"></a>Az alkalmazások inicializálásának módjai
Az ügyfélalkalmazások példányossá tételének számos módja van.

### <a name="initializing-a-public-client-application-from-code"></a>Nyilvános ügyfélalkalmazás inicializálása kódból

A következő kód egy nyilvános ügyfélalkalmazást, a Microsoft Azure nyilvános felhőben bejelentkező felhasználókat, a munkahelyi és iskolai fiókjukkal vagy a személyes Microsoft-fiókjukkal hozza meg.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>Bizalmas ügyfélalkalmazás inicializálása kódból

Ugyanígy a következő kód egy bizalmas alkalmazást (egy webalkalmazás, `https://myapp.azurewebsites.net`amely a webhelyen található) a Microsoft Azure nyilvános felhőben lévő felhasználók jogkivonatait, a munkahelyi és iskolai fiókjukkal vagy a személyes Microsoft-fiókjukkal hozza meg. Az alkalmazás az identitásszolgáltatóval egy ügyféltitok megosztásával van azonosítva:

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

Mint azt talán tudja, éles környezetben, ahelyett, hogy egy ügyféltitkos kulcsot, érdemes megosztani az Azure AD egy tanúsítványt. A kód ezután a következő:

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>Nyilvános ügyfélalkalmazás inicializálása a konfigurációs beállításokból

A következő kód egy nyilvános ügyfélalkalmazást hoz végre egy konfigurációs objektumból, amely programozott módon kitölthető vagy egy konfigurációs fájlból olvasható:

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>Bizalmas ügyfélalkalmazás inicializálása a konfigurációs beállításokból

Ugyanez a fajta minta vonatkozik a bizalmas ügyfélalkalmazásokra is. Más paramétereket is hozzáadhat `.WithXXX` módosítók (itt egy tanúsítvány) használatával.

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>Építőmódosítók

Az alkalmazásépítőket használó kódrészletekben számos `.With` módszer alkalmazható módosítóként (például `.WithCertificate` és `.WithRedirectUri`. 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>A nyilvános és bizalmas ügyfélalkalmazásokban közös módosítók

A nyilvános ügyfélen vagy a bizalmas ügyfélalkalmazások szerkesztőjén beállítható módosítók a következők:

|Módosító | Leírás|
|--------- | --------- |
|`.WithAuthority()`7 felülbírálások | Az alkalmazás alapértelmezett jogosultságát egy Azure AD-szolgáltatóra állítja be, azzal a lehetőséggel, hogy az Azure Cloudot, a közönséget, a bérlőt (bérlőazonosító vagy tartománynév) választja, vagy közvetlenül a hatóság URI-ját adja meg.|
|`.WithAdfsAuthority(string)` | Az alkalmazás alapértelmezett jogosultságát ADFS-jogosultságként állítja be.|
|`.WithB2CAuthority(string)` | Az alkalmazás alapértelmezett jogosultsága azure AD B2C-jogosultság.|
|`.WithClientId(string)` | Felülbírálja az ügyfélazonosítót.|
|`.WithComponent(string)` | Beállítja a könyvtár nevét MSAL.NET használatával (telemetriai okokból). |
|`.WithDebugLoggingCallback()` | Ha hívják, az `Debug.Write` alkalmazás egyszerűen meghívja a hibakeresési nyomkövetéseket. További információt a [Naplózás](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) című témakörben talál.|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | Állítsa be az alkalmazás szintű extra lekérdezési paramétereket, amelyeket a rendszer az összes hitelesítési kérelemben küld. Ez minden tokenbeszerzési módszer szintjén túlszállítható `.WithExtraQueryParameters pattern`(ugyanazzal a).|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | Speciális forgatókönyvek, például http-proxy konfigurálása vagy az MSAL kényszerítése egy adott HttpClient használatára (például ASP.NET Core webalkalmazások/API-k).|
|`.WithLogging()` | Ha meghívja, az alkalmazás visszahívást hív meg hibakeresési nyomkövetésekkel. További információt a [Naplózás](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) című témakörben talál.|
|`.WithRedirectUri(string redirectUri)` | Felülbírálja az alapértelmezett átirányítási URI-t. Nyilvános ügyfélalkalmazások esetén ez hasznos lesz a brókert érintő forgatókönyvek esetén.|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | Beállítja a telemetriai adatok küldéséhez használt meghatalmazottat.|
|`.WithTenantId(string tenantId)` | Felülbírálja a bérlői azonosítót vagy a bérlő leírását.|

### <a name="modifiers-specific-to-xamarinios-applications"></a>A Xamarin.iOS alkalmazásokra jellemző módosítók

A Xamarin.iOS rendszeren a nyilvános ügyfélalkalmazások szerkesztőjének beállítása a következő módosítók:

|Módosító | Leírás|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Csak Xamarin.iOS**: Beállítja az iOS kulcslánc biztonsági csoportját (a gyorsítótár-megőrzéshez).|

### <a name="modifiers-specific-to-confidential-client-applications"></a>A bizalmas ügyfélalkalmazásokra jellemző módosítók

A bizalmas ügyfélalkalmazások szerkesztőjének módosíthatói a következők:

|Módosító | Leírás|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Beállítja az alkalmazást azonosító tanúsítványt az Azure AD-vel.|
|`.WithClientSecret(string clientSecret)` | Beállítja az alkalmazás titkos adatait (alkalmazásjelszó) az Azure AD-vel.|

Ezek a módosítók kölcsönösen kizárják egymást. Ha mindkettőt megadja, az MSAL jelentős kivételt fog mondani.

### <a name="example-of-usage-of-modifiers"></a>Példa módosítók használatára

Tegyük fel, hogy az alkalmazás egy üzletági alkalmazás, amely csak a szervezet számára.  Akkor lehet írni:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

Ami érdekessé válik, az az, hogy a nemzeti felhők programozása mostanra leegyszerűsödött. Ha azt szeretné, hogy az alkalmazás egy több-bérlős alkalmazás legyen egy nemzeti felhőben, például a következőket írhatja:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

Az ADFS-hez is van felülbírálás (az ADFS 2019 jelenleg nem támogatott):
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Végül, ha Egy Azure AD B2C fejlesztő, megadhatja a bérlő, mint ez:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```
