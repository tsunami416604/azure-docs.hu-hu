---
title: Xamarin iOS-megfontolások (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg a Xamarin iOS és a Microsoft Authentication Library for .NET (MSAL.NET) használatának szempontjait.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 7125559dd39e1626634dae7c45b0744bfff57d8c
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82652655"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>A Xamarin iOS és a MSAL.NET használatának szempontjai
Ha a .NET-hez készült Microsoft Authentication Library (MSAL.NET) szolgáltatást használja a Xamarin iOS-ben, akkor a következőket kell tennie: 

- Felülbírálja és implementálja `OpenUrl` a `AppDelegate`függvényt a alkalmazásban.
- Kulcstartó-csoportok engedélyezése.
- Engedélyezze a jogkivonat-gyorsítótár megosztását.
- Kulcstartó-hozzáférés engedélyezése.
- Ismerje meg az iOS 12 és a hitelesítés ismert problémáit.

## <a name="implement-openurl"></a>OpenUrl megvalósítása

A `OpenUrl` `FormsApplicationDelegate` származtatott osztály és a hívás `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`metódusának felülbírálása. Például:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Hajtsa végre a következő feladatokat is: 
* Adjon meg egy URL-sémát.
* Engedély megkövetelése az alkalmazás számára egy másik alkalmazás meghívásához.
* Az átirányítási URL-címhez egy adott űrlap tartozik.
* Regisztrálja az átirányítási URL-címet a [Azure Portalban](https://portal.azure.com).

### <a name="enable-keychain-access"></a>Kulcstartó-hozzáférés engedélyezése

A kulcstartó-hozzáférés engedélyezéséhez győződjön meg arról, hogy az alkalmazás rendelkezik kulcstartó-hozzáférési csoporttal. A kulcstartó-hozzáférési csoportot beállíthatja úgy, hogy az `WithIosKeychainSecurityGroup()` API használatával hozza létre az alkalmazást.

A gyorsítótár és az egyszeri bejelentkezés (SSO) kihasználása érdekében állítsa a kulcstartó-hozzáférési csoportot az összes alkalmazás azonos értékére.

A telepítőhöz tartozó példa a MSAL 4. x verzióját használja:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Engedélyezze a kulcstartó-hozzáférést is a `Entitlements.plist` fájlban. Használja a következő hozzáférési csoportot vagy a saját hozzáférési csoportját.

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

Ha az `WithIosKeychainSecurityGroup()` API-t használja, a MSAL automatikusan hozzáfűzi a biztonsági csoportot az alkalmazás *Team ID* (`AppIdentifierPrefix`) végéhez. A MSAL hozzáadja a biztonsági csoportot, mert amikor az alkalmazást a Xcode-ben hozza létre, az ugyanaz lesz. Ezért a `Entitlements.plist` fájl jogosultságait a kulcstartó-hozzáférési csoport elé kell `$(AppIdentifierPrefix)` foglalni.

További információ: [iOS-jogosultságok dokumentációja](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps). 

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Jogkivonat-gyorsítótár megosztásának engedélyezése iOS-alkalmazások között

A MSAL 2. x verziótól kezdődően megadhat egy kulcstartó-hozzáférési csoportot, amely több alkalmazás között is megőrzi a jogkivonat-gyorsítótárat. Ez a beállítás lehetővé teszi, hogy megossza a jogkivonat-gyorsítótárat több olyan alkalmazás között, amelyek ugyanazzal a kulcstartó-hozzáférési csoporttal rendelkeznek. Megoszthatja a tokent a [ADAL.net](https://aka.ms/adal-net) alkalmazások, MSAL.net Xamarin. iOS-alkalmazások és a [ADAL. ObjC](https://github.com/AzureAD/azure-activedirectory-library-for-objc) vagy [MSAL.](https://github.com/AzureAD/microsoft-authentication-library-for-objc)ObjC által fejlesztett natív iOS-alkalmazások között.

A jogkivonat-gyorsítótár megosztásával engedélyezheti az egyszeri bejelentkezést (SSO) az összes olyan alkalmazás között, amelyek ugyanazt a kulcstartó-hozzáférési csoportot használják.

A gyorsítótár megosztásának engedélyezéséhez a `WithIosKeychainSecurityGroup()` metódus használatával állítsa be a kulcstartó-hozzáférési csoportot az összes olyan alkalmazásban, amely ugyanazt a gyorsítótárat használja. A cikkben szereplő első példa azt mutatja be, hogyan használható a metódus.

A cikk korábbi szakaszaiban megtudta, hogy a `$(AppIdentifierPrefix)` MSAL az `WithIosKeychainSecurityGroup()` API használatakor is felhasználja. A MSAL hozzáadja ezt az elemet, mert `AppIdentifierPrefix` a csoport azonosítója biztosítja, hogy csak az azonos közzétevő által készített alkalmazások tudják megosztani a kulcstartó-hozzáférést.

> [!NOTE]
> A `KeychainSecurityGroup` tulajdonság elavult.
> 
> A MSAL 2. x verziótól kezdődően a fejlesztők a `TeamId` `KeychainSecurityGroup` tulajdonság használatakor kénytelenek voltak belefoglalni az előtagot. A MSAL 2.7. x verziótól kezdődően azonban az új `iOSKeychainSecurityGroup` tulajdonság használatakor a MSAL az előtagot `TeamId` a futtatókörnyezet során oldja fel. Ha ezt a tulajdonságot használja, ne adja `TeamId` meg az előtagot az értékben. Az előtag megadása nem kötelező.
>
> Mivel a `KeychainSecurityGroup` tulajdonság elavult, használja a `iOSKeychainSecurityGroup` tulajdonságot.

### <a name="use-microsoft-authenticator"></a>Microsoft Authenticator használata

Az alkalmazás a Microsoft Authenticatort közvetítőként használhatja a következők engedélyezéséhez:

- **SSO**: Ha engedélyezi az SSO-t, a felhasználóknak nem kell bejelentkezniük minden alkalmazásba.
- **Eszköz azonosítása**: a hitelesítéshez használja az eszköz azonosítását az eszköz tanúsítványának eléréséhez. Ez a tanúsítvány a munkahelyhez való csatlakozáskor jön létre az eszközön. Az alkalmazás készen áll, ha a bérlői rendszergazdák engedélyezik az eszközökhöz kapcsolódó feltételes hozzáférést.
- **Alkalmazás-azonosítás ellenőrzése**: amikor egy alkalmazás meghívja a közvetítőt, átadja az átirányítási URL-címét. A közvetítő ellenőrzi az átirányítási URL-címet.

A közvetítők engedélyezésével kapcsolatos további információkért lásd: [Microsoft Authenticator vagy Microsoft Intune céges portál használata iOS-és Android-alkalmazások Xamarin](msal-net-use-brokers-with-xamarin-apps.md).

## <a name="known-issues-with-ios-12-and-authentication"></a>Az iOS 12 és a hitelesítés ismert problémái
A Microsoft [biztonsági tanácsadót](https://github.com/aspnet/AspNetCore/issues/4647) bocsátott ki az iOS 12 és bizonyos típusú hitelesítések közötti inkompatibilitással kapcsolatban. A kompatibilitási megszakítja a közösségi, WSFed és OIDC bejelentkezéseket. A biztonsági tanácsadó segítséget nyújt a fejlesztőknek arról, hogyan távolíthatja el az ASP.NET biztonsági korlátozásokat az alkalmazásaikban, hogy azok kompatibilisek legyenek az iOS 12 rendszerrel.  

Ha MSAL.NET-alkalmazásokat fejleszt ki a Xamarin iOS-ben, akkor a rendszer végtelen hurkot fog látni, amikor megpróbál bejelentkezni a webhelyekre az iOS 12 rendszerből. Ez a viselkedés hasonló ehhez a [ADAL-problémához](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329). 

Előfordulhat, hogy az iOS 12 Safari-ban ASP.NET Core OIDC-hitelesítés is megszakad. További információkért tekintse meg ezt a [WebKit-problémát](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="next-steps"></a>További lépések

További információ a Xamarin iOS-es tulajdonságairól: a következő minta README.md-fájljának [iOS-specifikus szempontjait](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations) ismertető bekezdése:

Sample | Platform | Leírás
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, Univerzális Windows-platform (UWP) | Egy egyszerű Xamarin űrlapos alkalmazás, amely bemutatja, hogyan hitelesítheti a Microsoft személyes fiókjait és az Azure AD-t az Azure AD 2,0-végponton keresztül a MSAL használatával. Az alkalmazás azt is bemutatja, hogyan használható az eredményül kapott jogkivonat a Microsoft Graph eléréséhez.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->