---
title: Jogkivonat-gyorsítótár szerializálása a Microsoft-hitelesítési tár .NET-keretrendszerhez készült |} Az Azure
description: Ismerje meg a szerializálási és az ügyfél szerializálása a Microsoft-hitelesítési tár .NET (MSAL.NET) használatával tokengyorsítótárral.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f1e9a48b114d328e0405a2f03764df4ce29b166
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65407062"
---
# <a name="token-cache-serialization-in-msalnet"></a>Az MSAL.NET tokengyorsítótárral szerializáció
Után egy [jogkivonat igényelve](msal-acquire-cache-tokens.md), a Microsoft hitelesítési tár (MSAL) szerint vannak gyorsítótárazva.  Alkalmazáskód egy token beszerzése a gyorsítótárból egy token beszerzése a más módon előtt próbálja meg.  Ez a cikk ismerteti az alapértelmezett és egyéni szerializálás a token gyorsítótár az MSAL.NET.

Ez a cikk szól MSAL.NET 3.x. Ha érdekli az MSAL.NET 2.x verzióját, lásd: [jogkivonatot az MSAL.NET gyorsítótár szerializálási 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Token-cache-serialization-2x).

## <a name="default-serialization-for-mobile-platforms"></a>Alapértelmezett szerializálási mobil platformokhoz

Egy memórián belüli tokengyorsítótárral MSAL.NET, alapértelmezés szerint nyújtja. Szerializálási platformok, ahol biztonságos tárolás érhető el egy felhasználót a platform részeként biztosítunk alapértelmezés szerint. Ez a helyzet a Universal Windows Platform (UWP), a Xamarin.iOS és Xamarin.Android.

> [!Note]
> Végez áttelepítést a Xamarin.Android-projekt MSAL.NET 1.x MSAL.NET 3.x, érdemes hozzáadni `android:allowBackup="false"` elkerülése érdekében a projekt régi gyorsítótárazza a jogkivonatok vissza, ha a Visual Studio üzemelő példányainak elindítani a visszaállítást a helyi tároló kerüljön. Lásd: [#659 kiadása](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/659#issuecomment-436181938).

## <a name="custom-serialization-for-windows-desktop-apps-and-web-appsweb-apis"></a>Egyéni szerializálás a Windows asztali alkalmazások és a web apps/webes API-k

Ne feledje, hogy egyéni sorba rendezésre nem érhető el a mobil platformokra (UWP Xamarin.iOS és Xamarin.Android). Az MSAL már egy biztonságos, nagy teljesítményű sorba rendezési mechanizmust ezekhez a platformokhoz határozza meg. .NET asztali és a .NET Core alkalmazásokat, azonban architektúrák változott, és az MSAL nem valósítható meg egy általános célú sorba rendezési mechanizmust. Webhelyek például válassza ki a Redis Cache-gyorsítótárhoz vagy asztali alkalmazások áruházbeli jogkivonatok titkosított jogkivonatok tárolására. Ezért a szerializálási-a-beépített nincs megadva. Ahhoz, hogy egy állandó token gyorsítótár-alkalmazás a .NET asztali vagy a .NET Core, a szerializálási testreszabásához szükséges.

A következő osztályok és kapcsolatok tokengyorsítótárral szerializálási használhatók:

- `ITokenCache`, események tokengyorsítótárral szerializálási kérelmek, valamint a módszerek szerializálni vagy deszerializálni a különféle formátumokat gyorsítótárát előfizetni határozza meg, amely (ADAL v3.0, MSAL 2.x és MSAL 3.x = ADAL 5.0-s verzió).
- `TokenCacheCallback` egy visszahívás átadott az eseményeket, hogy a szerializálási kezelheti. Ezek típusú argumentumok fogja meghívni `TokenCacheNotificationArgs`.
- `TokenCacheNotificationArgs` csak a nyújt a `ClientId` az alkalmazás és a egy hivatkozást a felhasználó számára, amely a token érhető el.

  ![Osztály diagramja](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> MSAL.NET token gyorsítótárak hoz létre, és biztosítja a `IToken` gyorsítótárazza, ha egy alkalmazás hívása `GetUserTokenCache` és `GetAppTokenCache` módszereket. Nem lehet saját maga a felület megvalósítása. A felelősség, egy egyéni tokengyorsítótárral szerializálási megvalósításának, hogy:
> - Reagálás `BeforeAccess` és `AfterAccess` "események". A `BeforeAccess` delegált feladata deszerializálni a gyorsítótárat, mivel a `AfterAccess` egyik feladata a gyorsítótár szerializálásához.
> - Ezek az események egy része tárolni vagy betölteni a blobok, amely bármilyen kívánt tárolóra az esemény argumentumot átadni.

A stratégiák különböznek attól függően, ha egy tokengyorsítótárral szerializálás a egy [ügyfélalkalmazás nyilvános](msal-client-applications.md) (asztalon), vagy egy [bizalmas ügyfélalkalmazás](msal-client-applications.md)) (webalkalmazás vagy webes API, démon alkalmazás ).

### <a name="token-cache-for-a-public-client"></a>Nyilvános ügyfél tokengyorsítótárral 

MSAL.NET 2.x óta egy nyilvános ügyfél tokengyorsítótárral szerializálásához több lehetősége van. A gyorsítótár a csak az MSAL.NET formátumát (az egyesített formátum gyorsítótár közös MSAL és a platformok között) is szerializálásához.  Is támogat a [örökölt](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) ADAL V3 gyorsítótár szerializálása token.

A jogkivonatok gyorsítótárát szerializálási közötti ADAL.NET egyszeri bejelentkezési állapot megosztása testreszabása 3.x, ADAL.NET 5.x és MSAL.NET kifejtett részben a következő minta: [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

> [!Note]
> Az MSAL.NET 1.1.4-preview tokengyorsítótárral formátuma már nem támogatott az MSAL 2.x. Ha alkalmazásokat MSAL.NET 1.x, a felhasználóknak kell újra-regisztrálási be. Azt is megteheti az ADAL 4.x-es (és 3.x) történő áttelepítés támogatott.

#### <a name="simple-token-cache-serialization-msal-only"></a>Egyszerű tokengyorsítótárral szerializálási (csak az MSAL)

Alább, amelyek az asztali alkalmazások jogkivonat a gyorsítótárban egyedi szerializálás naiv végrehajtásának. A felhasználói jogkivonatok gyorsítótárát Íme egy fájlt az alkalmazás ugyanabban a mappában.

Után az alkalmazás létrehozásához, a szerializálási meghívásával engedélyezi a `TokenCacheHelper.EnableSerialization()` metódust, és átadja a alkalmazás `UserTokenCache`.

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

A `TokenCacheHelper` segítőosztály típusúként van definiálva:

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

Előzetes verziója a termék minőségének tokengyorsítótárral fájlalapú serializátor nyilvános ügyfélalkalmazások (a Windows, Mac és Linux rendszereken futó asztali alkalmazások) esetében érhető el a [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) nyílt forráskódú kódtár. Az alkalmazások a következő nuget-csomagot a tartalmazhatnak: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

#### <a name="dual-token-cache-serialization-msal-unified-cache-and-adal-v3"></a>Kettős tokengyorsítótárral szerializálási (MSAL egyesített gyorsítótár és ADAL v3)

Ha szeretné megvalósítani tokengyorsítótárral szerializálási mindkét egységes gyorsítótár formátumban (ADAL.NET közös 4.x, MSAL.NET 2.x és más MSALs generációs azonos vagy régebbi, ugyanarra a platformra), tekintse meg a következő kódot:

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

Ezúttal a segítőosztály meghatározottak szerint:

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
  /// Get the user token cache
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
  public static void EnableSerialization(ITokenCache cache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   usertokenCache = cache;
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   usertokenCache.SetBeforeAccess(BeforeAccessNotification);
   usertokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Token cache
  /// </summary>
  static ITokenCache usertokenCache;

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

### <a name="token-cache-for-a-web-app-confidential-client-application"></a>Jogkivonatok gyorsítótárát egy web App (bizalmas ügyfél-alkalmazás)

A webalkalmazások és webes API-k a gyorsítótár hasznosíthatja a munkamenet, Redis Cache-gyorsítótárhoz vagy egy adatbázist.

Egy fontos megjegyezni, hogy webalkalmazások és webes API-kat, nem kell egy tokengyorsítótárral felhasználónként (fiók). Az egyes fiókok számára tokengyorsítótárral szerializálni kell.

Példák a jogkivonat-gyorsítótárakat használ a webalkalmazásokhoz és webes API-kat érhetők el a [ASP.NET Core-alapú webappokról szóló oktatóanyagunkat](https://ms-identity-aspnetcore-webapp-tutorial) fázisában [2-2 Tokengyorsítótárral](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache). Megvalósításokhoz rendelkeznie a következő mappát egy pillantást [TokenCacheProviders](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web/TokenCacheProviders) a a [microsoft-hitelesítési-bővítmények-az-dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) könyvtár (az a [ Microsoft.Identity.Client.Extensions.Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) mappát. 

## <a name="next-steps"></a>További lépések
A következő példák bemutatják, tokengyorsítótárral szerializálási.

| Sample | Platform | Leírás|
| ------ | -------- | ----------- |
|[active-directory-dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | (WPF) asztali | A Microsoft Graph API hívása Windows asztali .NET-(WPF-) alkalmazás. ![Topológia](media/msal-net-token-cache-serialization/topology.png)|
|[active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) | Asztal (konzol) | A Visual Studio megoldáscsomag ábrázoló (használatával ADAL.NET) Azure AD-1.0-s verziójú alkalmazások áttelepítése az Azure AD v2.0-alkalmazások, más néven összevont alkalmazások (az MSAL.NET használatával), különösen [Token gyorsítótár áttelepítése](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md)|