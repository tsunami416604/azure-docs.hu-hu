---
title: Xamarin iOS szempontok (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: A Xamarin iOS használatával kapcsolatos szempontok at a Microsoft Authentication Library for .NET (MSAL.NET) segítségével.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76e614b605cd07cd5dc454824dd204447f806907
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262710"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>A Xamarin iOS MSAL.NET használatának szempontjai
Ha a Microsoft Authentication Library for .NET (MSAL.NET) rendszert használja a Xamarin iOS rendszeren, a következőket kell tennie: 

- A `OpenUrl` függvény felülbírálása `AppDelegate`és megvalósítása a alkalmazásban.
- Kulcskarika-csoportok engedélyezése.
- Tokengyorsítótár-megosztás engedélyezése.
- Kulcskarika-hozzáférés engedélyezése.
- Ismerje meg az iOS 12-vel és a hitelesítéssel kapcsolatos ismert problémákat.

## <a name="implement-openurl"></a>OpenUrl implementálja

A származtatott `FormsApplicationDelegate` osztály és `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`a hívás `OpenUrl` metódusának felülbírálása . Például:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Végezze el a következő feladatokat is: 
* URL-séma definiálása.
* Engedély kérése az alkalmazáshoz egy másik alkalmazás hívásához.
* Az átirányítási URL-címhez egy adott űrlap vonatkozik.
* Regisztrálja az átirányítási URL-címet az [Azure Portalon.](https://portal.azure.com)

### <a name="enable-keychain-access"></a>Kulcskarika-hozzáférés engedélyezése

A kulcskarika-hozzáférés engedélyezéséhez győződjön meg arról, hogy az alkalmazás rendelkezik egy kulcskarika-hozzáférési csoporttal. Beállíthatja a kulcskarika-hozzáférési csoportot, amikor létrehozza `WithIosKeychainSecurityGroup()` az alkalmazást az API használatával.

A gyorsítótár és az egyszeri bejelentkezés (SSO) előnyeinek kihasználása érdekében állítsa a kulcskarika-hozzáférési csoportot az összes alkalmazásban azonos értékre.

A telepítés ez a példája az MSAL 4.x-et használja:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Is lehetővé kulcskarika `Entitlements.plist` hozzáférést a fájlban. Használja a következő hozzáférési csoportot vagy a saját hozzáférési csoportot.

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

Az API `WithIosKeychainSecurityGroup()` használatakor az MSAL automatikusan hozzáfűzi a biztonsági csoportot az`AppIdentifierPrefix`alkalmazás *csapatazonosítójának* ( végéhez). Az MSAL hozzáadja a biztonsági csoportot, mert amikor az alkalmazást Xcode-ban építi fel, az ugyanezt fogja tenni. Ezért a fájlban szereplő `Entitlements.plist` jogosultságokat a `$(AppIdentifierPrefix)` kulcskarika-hozzáférési csoport elé kell foglalni.

További információt az [iOS-jogosultságok dokumentációjában](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps)talál. 

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Tokengyorsítótár-megosztás engedélyezése iOS-alkalmazásokban

Az MSAL 2.x-től kezdve megadhat egy kulcskarika-hozzáférési csoportot a jogkivonat-gyorsítótár több alkalmazás közötti megőrzéséhez. Ez a beállítás lehetővé teszi, hogy a jogkivonat-gyorsítótárat több olyan alkalmazás között ossza meg, amelyek azonos kulcskarika-hozzáférési csoporttal rendelkeznek. Megoszthatja a token készpénzt [ADAL.NET](https://aka.ms/adal-net) alkalmazások, MSAL.NET Xamarin.iOS alkalmazások, és a natív iOS alkalmazások at fejlesztettek ki [ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) vagy [MSAL.objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc).

A jogkivonat-gyorsítótár megosztásával egyszeri bejelentkezést (SSO) engedélyez het az összes olyan alkalmazás között, amely ugyanazt a kulcskarika-hozzáférési csoportot használja.

A gyorsítótár-megosztás engedélyezéséhez `WithIosKeychainSecurityGroup()` a módszerrel állítsa a kulcskarika-hozzáférési csoportot ugyanarra az értékre minden olyan alkalmazásban, amely azonos gyorsítótárral rendelkezik. A cikk első példa a módszer használatát mutatja be.

A cikk korábbi részén megtanulta, hogy `$(AppIdentifierPrefix)` az `WithIosKeychainSecurityGroup()` MSAL az API használatakor hozzáadódik. Az MSAL azért adja hozzá `AppIdentifierPrefix` ezt az elemet, mert a csapatazonosító biztosítja, hogy csak az ugyanazon közzétevő által készített alkalmazások osztozhassanak a kulcskarika-hozzáférésen.

> [!NOTE]
> A `KeychainSecurityGroup` tulajdonság elavult.
> 
> Az MSAL 2.x-től kezdve `TeamId` a fejlesztőknek az `KeychainSecurityGroup` előtagot kellett tartalmazniuk, amikor az ingatlant használták. Az MSAL 2.7.x-től kezdődően `iOSKeychainSecurityGroup` azonban az MSAL `TeamId` futásidőben feloldja az előtagot. Ha ezt a tulajdonságot használja, `TeamId` ne adja meg az előtagot az értékben. Az előtag nem kötelező.
>
> Mivel `KeychainSecurityGroup` a tulajdonság elavult, `iOSKeychainSecurityGroup` használja a tulajdonságot.

### <a name="use-microsoft-authenticator"></a>A Microsoft Hitelesítő használata

Az alkalmazás a Microsoft Authenticator tanoncként használhatja a következőkengedélyezését:

- **SSO:** Ha engedélyezi az SSO-t, a felhasználóknak nem kell minden alkalmazásba bejelentkezniük.
- **Eszközazonosítás**: Az eszközazonosító használatával hitelesítheti magát az eszköztanúsítvány elérésével. Ez a tanúsítvány akkor jön létre az eszközön, amikor csatlakozik a munkahelyhez. Az alkalmazás készen áll, ha a bérlői rendszergazdák engedélyezik az eszközökhöz kapcsolódó feltételes hozzáférést.
- **Alkalmazás-azonosítás ellenőrzése:** Amikor egy alkalmazás meghívja a brókert, átadja az átirányítási URL-címét. A bróker ellenőrzi az átirányítási URL-t.

A brókerek engedélyezéséről a [Microsoft Authenticator vagy a Microsoft Intune Vállalati portál használata Xamarin iOS és Android alkalmazásokon ( Microsoft Authenticator vagy Microsoft Intune Company Portal ) (Xamarin iOS és Android alkalmazásokon) részben](msal-net-use-brokers-with-xamarin-apps.md)talál további információt.

## <a name="known-issues-with-ios-12-and-authentication"></a>Ismert problémák az iOS 12-vel és a hitelesítéssel kapcsolatban
A Microsoft kiadott egy [biztonsági tanácsadót](https://github.com/aspnet/AspNetCore/issues/4647) az iOS 12 és bizonyos típusú hitelesítések közötti inkompatibilitásról. Az inkompatibilitás megszakítja a közösségi, WSFed és OIDC bejelentkezések. A biztonsági tanácsadó segít a fejlesztőknek megérteni, hogyan távolíthatják el ASP.NET biztonsági korlátozásokat az alkalmazásaikból, hogy azok kompatibilisek legyenek az iOS 12 rendszerrel.  

Amikor MSAL.NET alkalmazásokat fejleszt xamarin iOS rendszeren, végtelen ciklusjelenhet meg, amikor megpróbál bejelentkezni az iOS 12 webhelyeire. Ez a viselkedés hasonló ehhez az [ADAL-problémához.](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329) 

Előfordulhat, hogy az iOS 12 Safari rendszerrel ASP.NET Core OIDC-hitelesítés szünetel. További információt a [WebKit-tel kapcsolatos probléma](https://bugs.webkit.org/show_bug.cgi?id=188165)tartalmaz.

## <a name="next-steps"></a>További lépések

A Xamarin iOS tulajdonságairól a következő minta README.md fájljának [iOS-specifikus szempontok](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations) at talál:

Sample | Platform | Leírás
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, Univerzális Windows platform (UWP) | Egy egyszerű Xamarin Forms alkalmazás, amely bemutatja, hogyan használhatja az MSAL-t a Microsoft személyes fiókok és az Azure AD hitelesítéséhez az Azure AD 2.0-végponton keresztül. Az alkalmazás azt is bemutatja, hogyan használhatja az eredményül kapott jogkivonatot a Microsoft Graph eléréséhez.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->