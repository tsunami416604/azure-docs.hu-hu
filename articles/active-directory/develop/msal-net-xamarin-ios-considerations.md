---
title: Xamarin iOS-megfontolások (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogy milyen szempontokat kell figyelembe venni a Xamarin iOS és a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használata esetén.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 599b8a3fdbad5747b0b303c71aeef084d04db6df
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76694920"
---
# <a name="xamarin-ios-specific-considerations-with-msalnet"></a>Xamarin iOS-specifikus megfontolások a MSAL.NET
A Xamarin iOS-ben számos szempontot figyelembe kell venni a MSAL.NET használatakor

- [Az iOS 12 és a hitelesítés ismert problémái](#known-issues-with-ios-12-and-authentication)
- [A `OpenUrl` függvény felülbírálása és implementálása a `AppDelegate`](#implement-openurl)
- [Kulcstartó-csoportok engedélyezése](#enable-keychain-access)
- [Jogkivonat-gyorsítótár megosztásának engedélyezése](#enable-token-cache-sharing-across-ios-applications)
- [Kulcstartó-hozzáférés engedélyezése](#enable-keychain-access)

## <a name="implement-openurl"></a>OpenUrl megvalósítása

Először felül kell bírálnia a `FormsApplicationDelegate` származtatott osztály `OpenUrl` metódusát, és meg kell hívnia a `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Emellett meg kell határoznia egy URL-sémát, meg kell adnia, hogy az alkalmazásnak meg kell hívnia egy másik alkalmazást, rendelkeznie kell egy adott űrlappal az átirányítási URL-címhez, és regisztrálnia kell az átirányítási URL-címet a [Azure Portal](https://portal.azure.com)

### <a name="enable-keychain-access"></a>Kulcstartó-hozzáférés engedélyezése

A kulcstartó-hozzáférés engedélyezéséhez az alkalmazásnak rendelkeznie kell egy kulcstartó-hozzáférési csoporttal.
A kulcstartó-hozzáférési csoportot a `WithIosKeychainSecurityGroup()` API-val állíthatja be az alkalmazás létrehozásakor az alábbi ábrán látható módon:

A gyorsítótár és az egyszeri bejelentkezés előnyeinek kihasználása érdekében a kulcstartó-hozzáférési csoportot ugyanahhoz az értékhez kell állítania az összes alkalmazásban.

Erre példa a MSAL v4. x használatával:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Ez a módosítás a kulcstartó hozzáférésének engedélyezése *mellett* a `Entitlements.plist` fájlban, vagy az alábbi hozzáférési csoport vagy a saját használatával érhető el:

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

Ha a `WithIosKeychainSecurityGroup()` API-t használja, a MSAL automatikusan hozzáfűzi a biztonsági csoportot az alkalmazás *csapat-azonosítójának* (AppIdentifierPrefix) végéhez, mert az alkalmazás a Xcode használatával történő létrehozásakor ugyanaz lesz. További információ: iOS- [jogosultságok dokumentációja](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps). Ezért szükséges, hogy a jogosultságok a `Entitlements.plist`a kulcstartó-hozzáférési csoport előtt is szerepeljenek `$(AppIdentifierPrefix)`.

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Jogkivonat-gyorsítótár megosztásának engedélyezése iOS-alkalmazások között

A 2. x MSAL megadhat egy kulcstartó-hozzáférési csoportot, amelyet a jogkivonat-gyorsítótár több alkalmazásban való megőrzésére használhat. Ezzel a beállítással megoszthatja a jogkivonat-gyorsítótárat több alkalmazás között ugyanazzal a kulcstartó-hozzáférési csoporttal, beleértve a [ADAL.net](https://aka.ms/adal-net), MSAL.net Xamarin. iOS-alkalmazásokkal és a [ADAL. ObjC](https://github.com/AzureAD/azure-activedirectory-library-for-objc) vagy [MSAL.](https://github.com/AzureAD/microsoft-authentication-library-for-objc)ObjC használatával fejlesztett natív iOS-alkalmazásokkal.

A jogkivonat-gyorsítótár megosztása lehetővé teszi az egyszeri bejelentkezést az összes olyan alkalmazás között, amelyek ugyanazt a kulcstartó-hozzáférési csoportot használják.

A gyorsítótár megosztásának engedélyezéséhez be kell állítania a "WithIosKeychainSecurityGroup ()" metódust úgy, hogy a kulcstartó-hozzáférési csoportot azonos értékre állítsa az összes olyan alkalmazásban, amely ugyanazt a gyorsítótárat használja, mint a fenti példában látható.

Korábban megemlítettük, hogy a MSAL hozzáadta a $ (AppIdentifierPrefix) értéket, amikor a `WithIosKeychainSecurityGroup()` API-t használja. Ennek az az oka, hogy a AppIdentifierPrefix vagy a "csapat azonosítója" annak biztosítására szolgál, hogy csak az azonos közzétevő által készített alkalmazások tudják megosztani a kulcstartók elérését.

> [!NOTE]
> **A `KeychainSecurityGroup` tulajdonság elavult.**
> 
> Korábban a MSAL 2. x verziójában a fejlesztők a `KeychainSecurityGroup` tulajdonság használatakor kénytelenek voltak belefoglalni a TeamId előtagot.
>
>  A MSAL 2.7. x verziójában az új `iOSKeychainSecurityGroup` tulajdonság használatakor a MSAL feloldja a TeamId előtagot a futtatókörnyezet során. Ha ezt a tulajdonságot használja, az érték nem tartalmazhatja a TeamId előtagot.
>  Használja az új `iOSKeychainSecurityGroup` tulajdonságot, amely nem igényli a TeamId megadását, mivel az előző `KeychainSecurityGroup` tulajdonság már elavult.

### <a name="use-microsoft-authenticator"></a>Microsoft Authenticator használata

Az alkalmazás a következő engedélyezéséhez használhatja a Microsoft Authenticatort (a közvetítőt):

- Egyszeri bejelentkezés (SSO). A felhasználóknak nem kell bejelentkezniük minden alkalmazásba.
- Eszköz azonosítása. Az eszközhöz való hozzáféréshez, amely az eszközön lett létrehozva munkahelyhez csatlakoztatva. Az alkalmazás készen áll, ha a bérlői rendszergazdák engedélyezik az eszközökhöz kapcsolódó feltételes hozzáférést.
- Alkalmazás-azonosító ellenőrzése. Amikor egy alkalmazás meghívja a közvetítőt, átadja az átirányítási URL-címet, és ellenőrzi a közvetítőt.

A közvetítő engedélyezésével kapcsolatos további információkért lásd: [Microsoft Authenticator vagy Microsoft Intune vállalati portál használata a Xamarin iOS-és Android-alkalmazásokban](msal-net-use-brokers-with-xamarin-apps.md).

### <a name="sample-illustrating-xamarin-ios-specific-properties"></a>Példa a Xamarin iOS-specifikus tulajdonságainak szemléltetésére

További részletek a következő minta readme.md-fájljának [iOS-specifikus szempontjaival](https://github.com/azure-samples/active-directory-xamarin-native-v2#ios-specific-considerations) kapcsolatos bekezdésében találhatók:

Minta | Platform | Leírás
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Egy egyszerű Xamarin űrlapos alkalmazás, amely bemutatja, hogyan használható a MSAL a MSA és az Azure AD hitelesítésére az Azure AD V 2.0-végponton keresztül, és az eredményül kapott jogkivonattal fér hozzá a Microsoft Graphhoz.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->

## <a name="known-issues-with-ios-12-and-authentication"></a>Az iOS 12 és a hitelesítés ismert problémái
A Microsoft [biztonsági tanácsadót](https://github.com/aspnet/AspNetCore/issues/4647) bocsátott ki a iOS12 és bizonyos hitelesítési típusok közötti inkompatibilitással kapcsolatos információk biztosításához. A kompatibilitási megszakítja a közösségi, a WSFed és a OIDC bejelentkezéseket. Ez a tanácsadó emellett útmutatást nyújt arról, hogy mit tehet a fejlesztők a ASP.NET által a iOS12 való kompatibilitás érdekében hozzáadott aktuális biztonsági korlátozások eltávolításához.  

A MSAL.NET-alkalmazások Xamarin iOS rendszeren való fejlesztésekor a rendszer végtelen hurkot tapasztalhat, amikor megpróbál bejelentkezni a webhelyekre az iOS 12 rendszerből (ehhez hasonlóan ehhez a [ADAL-hibához](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329)). 

Előfordulhat, hogy a jelen [WebKit-probléma](https://bugs.webkit.org/show_bug.cgi?id=188165)című részben leírtak szerint ASP.net Core OIDC-hitelesítést is megtalálhatja az iOS 12 Safariban.
