---
title: Jogkivonat-gyorsítótár szerializálási művelete a .NET-hez készült Microsoft Authentication Library-ben | Azure
description: A jogkivonat-gyorsítótár szerializálásának és ügyfél-szerializálásának megismerése a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatával.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5334c17b4f918e128ac69569e8ab6deeebac2182
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103944"
---
# <a name="token-cache-serialization-in-msalnet"></a>Jogkivonat-gyorsítótár szerializálása a MSAL.NET-ben
A [jogkivonat](msal-acquire-cache-tokens.md)beszerzése után a Microsoft Authentication Library (MSAL) gyorsítótárazza azt.  Az alkalmazás kódjának meg kell próbálnia kapni a tokent a gyorsítótárból, mielőtt másik módszerrel beszerezze a jogkivonatot.  Ez a cikk a jogkivonat-gyorsítótár alapértelmezett és egyéni szerializálását ismerteti a MSAL.NET-ben.

Ez a cikk a 3. x MSAL.NET. Ha érdekli a 2. x MSAL.NET, tekintse meg [a jogkivonat-gyorsítótár szerializálását a MSAL.NET 2. x verzióban](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Token-cache-serialization-2x).

## <a name="default-serialization-for-mobile-platforms"></a>A mobil platformok alapértelmezett szerializálása

A MSAL.NET-ben alapértelmezés szerint a memóriában tárolt jogkivonat-gyorsítótár van megadva. Alapértelmezés szerint a szerializálást olyan platformokhoz biztosítjuk, ahol a platform részeként a biztonságos tárterület elérhető a felhasználók számára. Ez a Univerzális Windows-platform (UWP), a Xamarin. iOS és a Xamarin. Android esetében.

> [!Note]
> Ha egy Xamarin. Android projektet telepít át a MSAL.net 1. x verzióról a MSAL.net 3. x-re, érdemes `android:allowBackup="false"` lehet hozzáadni a projekthez, hogy elkerülje a régi gyorsítótárazott tokenek visszaszerzését, amikor a Visual Studio-telepítések elindítják a helyi tároló visszaállítását. Lásd: [probléma #659](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/659#issuecomment-436181938).

## <a name="custom-serialization-for-windows-desktop-apps-and-web-appsweb-apis"></a>Egyéni szerializálás Windowsos asztali alkalmazásokhoz és webalkalmazásokhoz/webes API-khoz

Ne feledje, hogy az egyéni szerializálás nem érhető el a mobil platformokon (UWP, Xamarin. iOS és Xamarin. Android). A MSAL már definiál egy biztonságos és teljesítménybeli szerializálási mechanizmust ezekhez a platformokhoz. A .NET Desktop és a .NET Core alkalmazások azonban eltérő architektúrákkal rendelkeznek, és a MSAL nem tudnak általános célú szerializálási mechanizmust megvalósítani. A webhelyek például dönthetnek úgy, hogy Redis-gyorsítótárban tárolják a jogkivonatokat, vagy az asztali alkalmazások egy titkosított fájlban tárolják a jogkivonatokat. Így a szerializálás nem áll rendelkezésére. Ha állandó jogkivonat-gyorsítótárazási alkalmazást szeretne telepíteni a .NET Desktopban vagy a .NET Core-ban, testre kell szabnia a szerializálást.

A következő osztályok és felületek használatosak a jogkivonat-gyorsítótár szerializálásakor:

- `ITokenCache`, amely a jogkivonat-gyorsítótár szerializálási kéréseire előfizetett eseményeket, valamint a gyorsítótár szerializálásának vagy deszerializálásának módszereit határozza meg különböző formátumokban (ADAL v 3.0, MSAL 2. x és MSAL 3. x = ADAL v 5.0).
- `TokenCacheCallback`az eseményeknek átadott visszahívás, hogy kezelni tudja a szerializálást. A rendszer a típusú `TokenCacheNotificationArgs`argumentumokkal hívja meg őket.
- `TokenCacheNotificationArgs`a csak az `ClientId` alkalmazás és annak a felhasználónak a hivatkozását adja meg, amelyhez a jogkivonat elérhető.

  ![Osztály diagramja](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> A MSAL.net jogkivonat-gyorsítótárat hoz létre az Ön számára, `IToken` és megadja a gyorsítótárat az `UserTokenCache` alkalmazás és `AppTokenCache` a tulajdonságok meghívásakor. Saját magának nem kell megvalósítani a felületet. Az egyéni jogkivonat-gyorsítótár szerializálásának megvalósításakor a következőt kell tennie:
> - Reagálás az `BeforeAccess` `AfterAccess` "események" (vagy az aszinkron ízek) értékre. A `BeforeAccess` delegált feladata a gyorsítótár deszerializálása, míg az `AfterAccess` egyik felelős a gyorsítótár szerializálásához.
> - Az események egy része tárolja vagy betölti a blobokat, amelyeket az Event argumentumon át kell adni a kívánt tárterülethez.

A stratégiák eltérőek attól függően, hogy egy [nyilvános ügyfélalkalmazás](msal-client-applications.md) (asztali) vagy egy [bizalmas ügyfélalkalmazás](msal-client-applications.md)(webalkalmazás/webes API, Daemon-alkalmazás) esetében a jogkivonat-gyorsítótár szerializálását írja-e a rendszer.

### <a name="token-cache-for-a-public-client"></a>Nyilvános ügyfél jogkivonat-gyorsítótára 

A MSAL.NET v2. x óta számos lehetőség van a nyilvános ügyfél jogkivonat-gyorsítótárának szerializálására. A gyorsítótárat csak a MSAL.NET-formátumra lehet szerializálni (az egyesített formátum gyorsítótára közös a MSAL és a platformok között).  Az örökölt jogkivonat- [](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) gyorsítótár szerializálását is támogathatja a ADAL V3 esetében.

A jogkivonat-gyorsítótár szerializálásának testreszabásával megoszthatja a ADAL.NET 3. x, ADAL.NET 5. x és MSAL.NET közötti egyszeri bejelentkezési állapotot a következő példa részeként: [Active-Directory-DotNet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

> [!Note]
> A MSAL.NET 1.1.4 – előzetes verziójú tokenek gyorsítótárazási formátuma már nem támogatott a MSAL 2. x verzióban. Ha a MSAL.NET 1. x-et használó alkalmazásokkal rendelkezik, a felhasználóknak újra be kell jelentkezniük. Másik lehetőségként a ADAL 4. x (és 3. x) közötti áttelepítés támogatott.

#### <a name="simple-token-cache-serialization-msal-only"></a>Egyszerű jogkivonat-gyorsítótár szerializálása (csak MSAL)

Az alábbi példa egy jogkivonat-gyorsítótár egyéni szerializálásának naiv implementációját mutatja be asztali alkalmazásokhoz. Itt a felhasználói jogkivonat gyorsítótára egy olyan fájl, amely ugyanabban a mappában található, mint az alkalmazás.

Az alkalmazás létrehozása után a `TokenCacheHelper.EnableSerialization()` metódus meghívásával és az alkalmazás `UserTokenCache`átadásával engedélyezheti a szerializálást.

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

A `TokenCacheHelper` segítő osztály a következőképpen van definiálva:

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

A termék minőségi jogkivonat-gyorsítótárazási fájlon alapuló szerializálásának előzetes verziója a nyilvános ügyfélalkalmazások számára (Windows, Mac és Linux rendszereken futó asztali alkalmazások esetén) a [Microsoft. Identity. Client. Extensions. Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) nyílt forráskódú könyvtárában érhető el. Az alkalmazásokban a következő nuget-csomagból veheti fel: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

#### <a name="dual-token-cache-serialization-msal-unified-cache-and-adal-v3"></a>Kettős jogkivonat-gyorsítótár szerializálási (MSAL Unified cache és ADAL v3)

Ha a jogkivonat-gyorsítótár szerializálását az egyesített gyorsítótár formátumával (Common ADAL.NET 4. x, MSAL.NET 2. x és más, ugyanazon generáció vagy régebbi MSALs) szeretné megvalósítani, tekintse meg a következő kódot:

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

Ezúttal a segítő osztály a következőként van meghatározva:

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Enables the serialization of the token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache tokenCache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

### <a name="token-cache-for-a-web-app-confidential-client-application"></a>Jogkivonat-gyorsítótár webalkalmazásokhoz (bizalmas ügyfélalkalmazás)

A Web Apps vagy webes API-k esetében a gyorsítótár kihasználhatja a munkamenetet, a Redis cache-t vagy egy adatbázist.

A Web Apps vagy a webes API-k esetében fiókon belül egy jogkivonat-gyorsítótárat kell megőrizni.  A webalkalmazások esetében a jogkivonat-gyorsítótárat a fiók AZONOSÍTÓjának kell megadnia.  Webes API-k esetében a fióknak az API meghívásához használt jogkivonat kivonatával kell megjelennie. A MSAL.NET egyéni jogkivonat-gyorsítótárazási szerializálást biztosít a .NET-keretrendszer és a .NET Core alplatformok számára. Ha a gyorsítótár elérhető, az alkalmazások eldönthetik, hogy szerializálják vagy deszerializálják a gyorsítótárat. A felhasználókat kezelő bizalmas ügyfélalkalmazások (webalkalmazások, amelyek bejelentkeznek a felhasználók számára, és meghívhatják a webes API-kat, valamint a webes API-kat hívó webes API-k), több felhasználó is lehet, és a felhasználók párhuzamosan lesznek feldolgozva. A biztonság és a teljesítmény érdekében javasoljuk, hogy felhasználónként egy gyorsítótárat szerializáljon. A szerializálási események kiszámítják a gyorsítótár-kulcsot a feldolgozott felhasználó identitása alapján, valamint az adott felhasználóhoz tartozó jogkivonat-gyorsítótár szerializálása/deszerializálása.

Példák a Web Apps és a webes API-k jogkivonat-gyorsítótárának használatára a [ASP.net Core webalkalmazás](https://ms-identity-aspnetcore-webapp-tutorial) -oktatóanyagban az 2-2-os [token gyorsítótárában](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache). A megvalósítások esetében tekintse meg a következő mappát a [Microsoft-Authentication-Extensions-for-DotNet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) Library ( [Microsoft. Identity. Client. Extensions. Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) mappában) [TokenCacheProviders](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web/TokenCacheProviders) . 

## <a name="next-steps"></a>További lépések
A következő minták a jogkivonat-gyorsítótár szerializálását szemléltetik.

| Minta | Platform | Leírás|
| ------ | -------- | ----------- |
|[active-directory-dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | Asztali (WPF) | A Microsoft Graph API-t hívó Windowsos asztali .NET (WPF) alkalmazás. ![Topológia](media/msal-net-token-cache-serialization/topology.png)|
|[active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) | Asztal (konzol) | A Visual Studio-megoldások készlete az Azure AD v 1.0-alkalmazások (ADAL.NET használatával) Azure AD v 2.0-alkalmazásokba való áttelepítését mutatja be (MSAL.NET használatával), különösen a [jogkivonat-gyorsítótár](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md) áttelepítését.|
