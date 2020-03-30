---
title: Tokengyorsítótár szerializálása (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Információ a tokengyorsítótár szerializálásáról és ügyfélszeresítéséről a Microsoft Authentication Library for .NET (MSAL.NET) használatával.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/16/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1bd348ad27d892d0421b13c16ce81bc4f5dfb021
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262801"
---
# <a name="token-cache-serialization-in-msalnet"></a>Jogkivonat-gyorsítótár szerializálása MSAL.NET
A [token beolvasása](msal-acquire-cache-tokens.md)után a Microsoft Authentication Library (MSAL) gyorsítótárba helyezi.  Az alkalmazáskódnak meg kell próbálnia egy jogkivonatot beszerezni a gyorsítótárból, mielőtt egy jogkivonatot más módszerrel beszerezne.  Ez a cikk a MSAL.NET tokengyorsítótár alapértelmezett és egyéni szerializálását ismerteti.

Ez a cikk a MSAL.NET 3.x. Ha érdekli a MSAL.NET 2.x, [lásd: Token cache serialization in MSAL.NET 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Token-cache-serialization-2x).

## <a name="default-serialization-for-mobile-platforms"></a>Alapértelmezett szerializálás mobil platformokon

A MSAL.NET alapértelmezés szerint egy memórián belüli jogkivonat-gyorsítótár at biztosít. A szerializálás alapértelmezés szerint olyan platformokon érhető el, ahol a platform részeként biztonságos tárterület áll a felhasználó rendelkezésére. Ez a helyzet az Univerzális Windows platform (UWP), a Xamarin.iOS és a Xamarin.Android esetében.

> [!Note]
> Amikor egy Xamarin.Android-projektet MSAL.NET 1.x-ről MSAL.NET 3.x-re telepít át, érdemes hozzáadni `android:allowBackup="false"` a projekthez, hogy a régi gyorsítótárazott jogkivonatok ne térjenek vissza, amikor a Visual Studio központi telepítései a helyi tároló visszaállítását indítják el. Lásd: [#659](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/659#issuecomment-436181938).

## <a name="custom-serialization-for-windows-desktop-apps-and-web-appsweb-apis"></a>Egyéni szerializálás Windows asztali alkalmazásokhoz és webalkalmazásokhoz/webes API-khoz

Ne feledje, hogy az egyéni szerializálás nem érhető el mobil platformokon (UWP, Xamarin.iOS és Xamarin.Android). Az MSAL már definiál egy biztonságos és előleges szerializálási mechanizmust ezekhez a platformokhoz. A .NET asztali és a .NET Core alkalmazások azonban változatos architektúrával rendelkeznek, és az MSAL nem tud általános célú szerializálási mechanizmust megvalósítani. Előfordulhat például, hogy a webhelyek a tokeneket redis-gyorsítótárban tárolják, vagy az asztali alkalmazások titkosított fájlban tárolják a tokeneket. Tehát a szerializálás nem biztosított out-of-the-box. Ha állandó tokengyorsítótár-alkalmazást szeretne a .NET asztali vagy a .NET Core fájlban, testre kell szabnia a szerializálást.

A tokengyorsítótár-szerializálásban a következő osztályok és összeköttetések használatosak:

- `ITokenCache`, amely meghatározza a tokengyorsítótár-szerializálási kérelmekre való előfizetés eseményeit, valamint a gyorsítótár különböző formátumokban (ADAL v3.0, MSAL 2.x és MSAL 3.x = ADAL v5.0) szerializálásának vagy szerializálásának módszereit.
- `TokenCacheCallback`az eseményeknek átadott visszahívás, így kezelni tudja a szerializálást. A nemi típusú argumentumokkal `TokenCacheNotificationArgs`fogják hívni őket.
- `TokenCacheNotificationArgs`csak az `ClientId` alkalmazás, és egy hivatkozást a felhasználó, amelyhez a jogkivonat elérhető.

  ![Osztálydiagram](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> MSAL.NET jogkivonat-gyorsítótárakat hoz `IToken` létre, és az alkalmazás `UserTokenCache` `AppTokenCache` és a tulajdonságok hívásakor gyorsítótárat biztosít. Ön nem kellene végrehajtani a felület magad. Egyéni tokengyorsítótár-szerializálás megvalósításakor a feladata a következő:
> - Reagáljon `BeforeAccess` `AfterAccess` és "események" (vagy azok Async ízek). A `BeforeAccess` delegált felelős a gyorsítótár deszerializálásáért, míg az `AfterAccess` egyik felelős a gyorsítótár szerializálásáért.
> - Ezek az események egy része tárolja vagy töltse be a blobokat, amelyek az esemény argumentumon keresztül bármilyen tárolóba kerülnek.

A stratégiák eltérőek attól függően, hogy egy nyilvános [ügyfélalkalmazás](msal-client-applications.md) (asztal) vagy egy [bizalmas ügyfélalkalmazás](msal-client-applications.md)(webapp / web API, démon alkalmazás) tokengyorsítótár-szerializálást ír.

### <a name="token-cache-for-a-public-client"></a>Jogkivonat-gyorsítótár nyilvános ügyfél számára 

A MSAL.NET 2.x-es e-MSAL.NET számos lehetősége van egy nyilvános ügyfél tokengyorsítótárának szerializálására. A gyorsítótárat csak a MSAL.NET formátumban szerializálhatja (az egyesített formátumú gyorsítótár gyakori az MSAL-on és a platformokon).  Az ADAL V3 [örökölt](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) tokengyorsítótár-szerializálását is támogathatja.

A tokengyorsítótár szerializálásának testreszabása az egyszeri bejelentkezési állapot megosztásához ADAL.NET 3.x, ADAL.NET 5.x és MSAL.NET között a következő minta egy része ismerteti: [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

> [!Note]
> Az MSAL.NET 1.1.4-preview token cache formátum már nem támogatott MSAL 2.x. Ha az alkalmazások kihasználva MSAL.NET 1.x, a felhasználók nak kell újra bejelentkezni. Az ADAL 4.x (és a 3.x) alkalmazásból történő áttelepítés is támogatott.

#### <a name="simple-token-cache-serialization-msal-only"></a>Egyszerű tokengyorsítótár-szerializálás (csak MSAL esetén)

Az alábbiakban egy példa egy naiv végrehajtása egyéni szerializálása egy token cache asztali alkalmazások. Itt a felhasználói jogkivonat-gyorsítótár egy fájl ugyanabban a mappában, mint az alkalmazás.

Az alkalmazás létrehozása után a metódus hívásával `TokenCacheHelper.EnableSerialization()` és az `UserTokenCache`alkalmazás átadásával engedélyezheti a szerializálást.

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

A `TokenCacheHelper` segítő osztály definíciója:

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

A [microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) nyílt forráskódú kódtárból a nyilvános ügyfélalkalmazások (Windows, Mac és Linux rendszeren futó asztali alkalmazások) termékminőségi tokengyorsítótár-fájlalapú szerializálójának előnézete érhető el. Az alkalmazásokba a következő nuget csomagból foglalhatja bele: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

#### <a name="dual-token-cache-serialization-msal-unified-cache-and-adal-v3"></a>Kettős tokengyorsítótár-szerializálás (MSAL egyesített gyorsítótár és ADAL v3)

Ha a tokengyorsítótár szerializálását az egyesített gyorsítótár-formátummal (a ADAL.NET 4.x, MSAL.NET 2.x és más, azonos generációs vagy régebbi MSAL-okkal szeretné megvalósítani ugyanazon a platformon), vessen egy pillantást a következő kódra:

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

Ezúttal a segítő osztály meghatározása:

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

### <a name="token-cache-for-a-web-app-confidential-client-application"></a>Egy webalkalmazás tokengyorsítótára (bizalmas ügyfélalkalmazás)

A webalkalmazásokban vagy webes API-kban a gyorsítótár kihasználhatja a munkamenetet, a Redis-gyorsítótárat vagy az adatbázist.

A webes alkalmazásokban vagy webes API-kban fiókonként egy jogkivonat-gyorsítótárat tarthat meg.  A webalkalmazások esetében a jogkivonat-gyorsítótárat a fiókazonosítónak kell megkell keyd-nek lennie.  Webes API-k esetén a fiókot az API-k hívásához használt jogkivonat kivonatának kell megadnia. MSAL.NET egyéni tokengyorsítótár-szerializálást biztosít a .NET Framework és a . Események aktiválódnak, amikor a gyorsítótár elérésekor, alkalmazások választhatnak, hogy szeriaializálja vagy deszerializálja a gyorsítótárat. A felhasználókat kezelő bizalmas ügyfélalkalmazásokon (a felhasználókat bejelentkező és webes API-kat hívó webes API-k, valamint az alsóbb rétegbeli webes API-kat hívó webes API-k hívása) számos felhasználó és a felhasználók feldolgozása párhuzamosan történik. Biztonsági és teljesítménybeli okokból javasoljuk, hogy felhasználónként egy gyorsítótárat szerializáljanak. A szerializálási események a feldolgozott felhasználó identitása alapján számítják ki a gyorsítótárkulcsot, és szerializálják/deszerimizizálják az adott felhasználó tokengyorsítótárát.

Példák a token-gyorsítótárak webalkalmazásokhoz és webes API-khoz való használatára a [ASP.NET Core webalkalmazás oktatóanyagában](https://ms-identity-aspnetcore-webapp-tutorial) a [2-2 token gyorsítótárban.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) A megvalósítások tekintse meg a [mappát TokenCacheProviders](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/Microsoft.Identity.Web/TokenCacheProviders) a [microsoft-hitelesítés-extensions-for-dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) könyvtár (a [Microsoft.Identity.Client.Extensions.Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) mappában. 

## <a name="next-steps"></a>További lépések
A következő minták a tokengyorsítótár szerializálását szemléltetik.

| Sample | Platform | Leírás|
| ------ | -------- | ----------- |
|[active-directory-dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | Asztal (WPF) | A Microsoft Graph API-t hívó Windows Desktop .NET (WPF) alkalmazás. ![Topológia](media/msal-net-token-cache-serialization/topology.png)|
|[active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) | Asztal (konzol) | Visual Studio-megoldások készlete, amely az Azure AD 1.0-s alkalmazások (ADAL.NET használatával) azure AD v2.0-s alkalmazásokba való migrálását szemlélteti, más néven konvergens alkalmazások (MSAL.NET használatával), különösen [a tokengyorsítótár-áttelepítés](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md)|
