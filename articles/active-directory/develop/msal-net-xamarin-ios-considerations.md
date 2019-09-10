---
title: Xamarin iOS-megfontolások (Microsoft Authentication Library for .NET) | Azure
description: Ismerje meg, hogy milyen szempontokat kell figyelembe venni a Xamarin iOS és a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használata esetén.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 054033c0fc9f1138ef9ecf7eaceca626f6f53423
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70872848"
---
# <a name="xamarin-ios-specific-considerations-with-msalnet"></a>Xamarin iOS-specifikus megfontolások a MSAL.NET
A Xamarin iOS-ben számos szempontot figyelembe kell venni a MSAL.NET használatakor

- [Az iOS 12 és a hitelesítés ismert problémái](#known-issues-with-ios-12-and-authentication)
- [A `OpenUrl` függvény felülbírálása és implementálása a`AppDelegate`](#implement-openurl)
- [Kulcstartó-csoportok engedélyezése](#enable-keychain-access)
- [Jogkivonat-gyorsítótár megosztásának engedélyezése](#enable-token-cache-sharing-across-ios-applications)
- [Kulcstartó-hozzáférés engedélyezése](#enable-keychain-access)

## <a name="known-issues-with-ios-12-and-authentication"></a>Az iOS 12 és a hitelesítés ismert problémái
A Microsoft [biztonsági tanácsadót](https://github.com/aspnet/AspNetCore/issues/4647) bocsátott ki a iOS12 és bizonyos hitelesítési típusok közötti inkompatibilitással kapcsolatos információk biztosításához. A kompatibilitási megszakítja a közösségi, a WSFed és a OIDC bejelentkezéseket. Ez a tanácsadó emellett útmutatást nyújt arról, hogy mit tehet a fejlesztők a ASP.NET által a iOS12 való kompatibilitás érdekében hozzáadott aktuális biztonsági korlátozások eltávolításához.  

A MSAL.NET-alkalmazások Xamarin iOS rendszeren való fejlesztésekor a rendszer végtelen hurkot tapasztalhat, amikor megpróbál bejelentkezni a webhelyekre az iOS 12 rendszerből (ehhez hasonlóan ehhez a [ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329)-hibához). 

Előfordulhat, hogy a jelen [WebKit-probléma](https://bugs.webkit.org/show_bug.cgi?id=188165)című részben leírtak szerint ASP.net Core OIDC-hitelesítést is megtalálhatja az iOS 12 Safariban.

## <a name="implement-openurl"></a>OpenUrl megvalósítása

Először felül kell bírálnia `OpenUrl` a `FormsApplicationDelegate` származtatott osztály metódusát és a hívást `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`.

```CSharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Emellett meg kell határoznia egy URL-sémát, meg kell adnia, hogy az alkalmazásnak meg kell hívnia egy másik alkalmazást, rendelkeznie kell egy adott űrlappal az átirányítási URL-címhez, és regisztrálnia kell az átirányítási URL-címet a [Azure Portal](https://portal.azure.com)

### <a name="enable-keychain-access"></a>Kulcstartó-hozzáférés engedélyezése

A kulcstartó-hozzáférés engedélyezéséhez az alkalmazásnak rendelkeznie kell egy kulcstartó-hozzáférési csoporttal.
Az alkalmazás létrehozásakor az `WithIosKeychainSecurityGroup()` API használatával állíthatja be a kulcstartó-hozzáférési csoportot:

Az egyszeri bejelentkezés engedélyezéséhez a `PublicClientApplication.iOSKeychainSecurityGroup` tulajdonságot az összes alkalmazásban azonos értékre kell állítania.

Erre példa a MSAL v3. x használatával:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.msalrocks")
     .Build();
```

A jogosultságokat. plist frissíteni kell, hogy az a következő XML-kódrészlethez hasonlítson:

Ez a változás a kulcstartó hozzáférésének `Entitlements.plist` engedélyezése mellett a fájlban, vagy az alábbi hozzáférési csoport vagy a saját:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.msalrocks</string>
  </array>
</dict>
</plist>
```

Erre példa a MSAL v4. x használatával:

```csharp
PublicClientApplication.iOSKeychainSecurityGroup = "com.microsoft.msalrocks";
```

Ha az `WithIosKeychainSecurityGroup()` API-t használja, a MSAL automatikusan hozzáfűzi a biztonsági csoportot az alkalmazás "Team id" (AppIdentifierPrefix) végéhez, mert az alkalmazásnak a Xcode használatával történő létrehozásakor ugyanaz lesz. [További részletekért tekintse meg az iOS](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps)-jogosultságok dokumentációját. Ezért frissítenie kell a jogosultságokat, hogy a $ (AppIdentifierPrefix) belefoglalása előtt a jogosultságok. plist fájlhoz tartozó kulcstartó-hozzáférési csoport szerepeljen.

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Jogkivonat-gyorsítótár megosztásának engedélyezése iOS-alkalmazások között

A 2. x MSAL megadhat egy kulcstartó-hozzáférési csoportot, amelyet a jogkivonat-gyorsítótár több alkalmazásban való megőrzésére használhat. Ezzel a beállítással megoszthatja a jogkivonat-gyorsítótárat több alkalmazás között ugyanazzal a kulcstartó-hozzáférési csoporttal, beleértve azokat is, amelyek a [ADAL.net](https://aka.ms/adal-net), a MSAL.net Xamarin. iOS-alkalmazásokkal és a fejlesztett [natív iOS-alkalmazásokkal lettek kifejlesztve ADAL. ObjC](https://github.com/AzureAD/azure-activedirectory-library-for-objc) vagy [MSAL. ObjC](https://github.com/AzureAD/microsoft-authentication-library-for-objc)).

A jogkivonat-gyorsítótár megosztása lehetővé teszi az egyszeri bejelentkezést az összes olyan alkalmazás között, amelyek ugyanazt a kulcstartó-hozzáférési csoportot használják.

A gyorsítótár megosztásának engedélyezéséhez be kell állítania a "WithIosKeychainSecurityGroup ()" metódust úgy, hogy a kulcstartó-hozzáférési csoportot azonos értékre állítsa az összes olyan alkalmazásban, amely ugyanazt a gyorsítótárat használja, mint a fenti példában látható.

Korábban már említettük, hogy a MSAL hozzáadta a $ (AppIdentifierPrefix) értéket, `WithIosKeychainSecurityGroup()` amikor az API-t használja. Ennek az az oka, hogy a AppIdentifierPrefix vagy a "csapat azonosítója" annak biztosítására szolgál, hogy csak az azonos közzétevő által készített alkalmazások tudják megosztani a kulcstartók elérését.

> [!NOTE]
> **A `KeychainSecurityGroup` tulajdonság elavult.**
> 
> Korábban a MSAL 2. x verziójában a fejlesztők a `KeychainSecurityGroup` tulajdonság használatakor kénytelenek voltak belefoglalni a TeamId előtagot.
>
>  A MSAL 2.7. x verziójában az új `iOSKeychainSecurityGroup` tulajdonság használatakor a MSAL feloldja a TeamId előtagot a futtatókörnyezet során. Ha ezt a tulajdonságot használja, az érték nem tartalmazhatja a TeamId előtagot.
>  Használja az új `iOSKeychainSecurityGroup` tulajdonságot, amely nem igényli a TeamId megadását, mivel az előző `KeychainSecurityGroup` tulajdonság elavult.

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
