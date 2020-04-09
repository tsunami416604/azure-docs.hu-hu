---
title: Az SSO konfigurálása macOS és iOS rendszeren
titleSuffix: Microsoft identity platform
description: További információ az egyszeri bejelentkezés (SSO) macOS és iOS rendszeren való konfigurálásáról.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 25389348476552298ddb947ccb59acb8b3d5bc57
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881248"
---
# <a name="how-to-configure-sso-on-macos-and-ios"></a>Útmutató: Az SSO konfigurálása macOS és iOS rendszeren

A macOS és iOS rendszerhez készült Microsoft Authentication Library (MSAL) támogatja a macOS/iOS alkalmazások és böngészők közötti egyszeri bejelentkezést (SSO). Ez a cikk a következő SSO-forgatókönyveket ismerteti:

- [Csendes sso több alkalmazás között](#silent-sso-between-apps)

Az ilyen típusú sso működik több alkalmazás között, amelyeket ugyanaz az Apple Developer forgalmaz. Csendes egyszeri szolgáltatót biztosít (azaz a felhasználó nem kéri a hitelesítő adatokat) a más alkalmazások által a kulcskarikából írt frissítési jogkivonatok olvasásával, és a hozzáférési jogkivonatok csendes cseréje.  

- [SSO hitelesítési brókeren keresztül](#sso-through-authentication-broker-on-ios)

> [!IMPORTANT]
> Ez a folyamat macOS rendszeren nem érhető el.

A Microsoft olyan alkalmazásokat, úgynevezett brókereket biztosít, amelyek lehetővé teszik az SSO-t a különböző szállítóktól származó alkalmazások között, feltéve, hogy a mobileszköz regisztrálva van az Azure Active Directoryban (AAD). Az ilyen típusú sso igényel közvetítő alkalmazást kell telepíteni a felhasználó eszközére.

- **SSO között MSAL és safari**

Az sso az [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc) osztályon keresztül érhető el. Más alkalmazások meglévő bejelentkezési állapotát és a Safari böngészőt használja. Ez nem korlátozódik az ugyanazon Apple Developer által terjesztett alkalmazásokra, de némi felhasználói beavatkozást igényel.

Ha az alkalmazás alapértelmezett webnézetét használja a felhasználók bejelentkezéséhez, automatikus egyszeri bejelentkezést kap az MSAL-alapú alkalmazások és a Safari között. Ha többet szeretne megtudni az MSAL által támogatott webes nézetekről, látogasson el [a Böngészők és webnézetek testreszabása című webhelyre.](customize-webviews.md)

> [!IMPORTANT]
> Ez a fajta sso jelenleg nem érhető el macOS rendszeren. A macOS rendszeren csak a WKWebView-t támogatja, amely nem rendelkezik SSO-támogatással a Safari val. 

- **Csendes sso az ADAL és az MSAL macOS/iOS alkalmazások között**

Az MSAL Objective-C támogatja az adal objektív-C-alapú alkalmazásokkal rendelkező áttelepítést és egyszeri sso-t. Az alkalmazásokat ugyanannak az Apple Developernek kell terjesztenie.

Lásd: [SSO között ADAL és MSAL alkalmazások macOS és iOS](sso-between-adal-msal-apps-macos-ios.md) utasításokat alkalmazások közötti Egyszeri szolgáltatások Között ADAL és MSAL-alapú alkalmazások.

## <a name="silent-sso-between-apps"></a>Csendes sso az alkalmazások között

Az MSAL támogatja az sso megosztást az iOS kulcskarika-hozzáférési csoportokon keresztül.

Az SSO-nak az alkalmazások közötti engedélyezéséhez a következő lépéseket kell megtennie, amelyeket az alábbiakban részletesebben ismertetünk:

1. Győződjön meg arról, hogy minden alkalmazás ugyanazt az ügyfélazonosítót vagy alkalmazásazonosítót használja.
1. Győződjön meg arról, hogy az összes alkalmazás ugyanazt az aláírási tanúsítványt az Apple- től, hogy megoszthassa a kulcsláncokat.
1. Kérje ugyanazt a kulcskarika jogosultságot az egyes alkalmazásokhoz.
1. Tájékoztassa az MSAL SDK-kat a megosztott kulcskarikáról, amelyet használni szeretne, ha az eltér az alapértelmezetttől.

### <a name="use-the-same-client-id-and-application-id"></a>Ugyanazt az ügyfélazonosítót és alkalmazásazonosítót használja

Ahhoz, hogy a Microsoft identity platform tudja, mely alkalmazások oszthatják meg a jogkivonatokat, ezeknek az alkalmazásoknak ugyanazt az ügyfélazonosítót vagy alkalmazásazonosítót kell megosztaniuk. Ez az az egyedi azonosító, amelyet akkor kapott, amikor regisztrálta az első alkalmazást a portálon.

A Microsoft identity platform azt mondja az azonos alkalmazásazonosítót használó alkalmazásoknak, hogy **átirányítási URI-k segítségével.** Minden alkalmazás rendelkezhet több átirányítási URI-k regisztrálva a bevezetési portálon. A csomag minden alkalmazás a csomag ban lesz egy másik átirányítási URI.Each app in your suite will have a different redirect URI. Például:

App1 átirányításURI:`msauth.com.contoso.mytestapp1://auth`  
App2 átirányításURI:`msauth.com.contoso.mytestapp2://auth`  
App3 átirányításURI:`msauth.com.contoso.mytestapp3://auth`  

> [!IMPORTANT]
> Az átirányítási uris formátumának kompatibilisnek kell lennie az MSAL-támogatottformátummal, amelyet az [MSAL átirányítási URI formátumkövetelményei dokumentálnak.](redirect-uris-ios.md#msal-redirect-uri-format-requirements)

### <a name="setup-keychain-sharing-between-applications"></a>Kulcskarika-megosztás beállítása az alkalmazások között

A kulcskarika-megosztás engedélyezéséhez tekintse meg az Apple [Képességek hozzáadása](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html) című cikkét. Az a fontos, hogy eldöntse, mit szeretne a kulcskarikának hívni, és adja hozzá ezt a képességet az összes alkalmazásához, amely részt vesz az SSO-ban.

Ha a jogosultságok megfelelően vannak beállítva, `entitlements.plist` megjelenik egy fájl a projektkönyvtárban, amely a következő példához hasonlót tartalmaz:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

#### <a name="add-a-new-keychain-group"></a>Új kulcskarika-csoport hozzáadása

Adjon hozzá egy új kulcskarika-csoportot a projekt **képességek hez.** A kulcskarika-csoportnak a következőknek kell lennie:
* `com.microsoft.adalcache`iOS rendszeren 
* `com.microsoft.identity.universalstorage`macOS rendszeren.

![kulcskarika példa](media/single-sign-on-macos-ios/keychain-example.png)

További információt a [kulcskarika-csoportok című témakörben talál.](howto-v2-keychain-objc.md)

## <a name="configure-the-application-object"></a>Az alkalmazásobjektum konfigurálása

Miután minden alkalmazásban engedélyezve van a kulcskarika-jogosultság, és készen áll `MSALPublicClientApplication` az Egyszeri bejelentkezés használatára, konfigurálja a kulcskarika-hozzáférési csoporttal a következő példában szereplő módon:

C célkitűzés:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<my-client-id>"];
configuration.cacheConfig.keychainSharingGroup = @"my.keychain.group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:configuration error:&error];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<my-client-id>")
config.cacheConfig.keychainSharingGroup = "my.keychain.group"

do {
   let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
} catch let error as NSError {
  // handle error here
}
```

> [!WARNING]
> Amikor megoszt egy kulcskarikát az alkalmazások között, bármely alkalmazás törölheti a felhasználókat, vagy akár az összes jogkivonatot az alkalmazáson keresztül.
> Ez különösen akkor, ha olyan alkalmazásokkal rendelkezik, amelyek a háttérmunka jogkivonataira támaszkodnak.
> A kulcskarika megosztása azt jelenti, hogy nagyon óvatosnak kell lennie, amikor az alkalmazás microsoftidentitás-SDK eltávolítási műveleteket használ.

Ennyi az egész! A Microsoft identity SDK mostantól megosztja a hitelesítő adatokat az összes alkalmazásban. A fióklista az alkalmazáspéldányok között is meg lesz osztva.

## <a name="sso-through-authentication-broker-on-ios"></a>SSO hitelesítési brókeren keresztül iOS rendszeren

Az MSAL támogatja a Microsoft Authenticator-nal való felügyelt hitelesítést. A Microsoft Authenticator sso-t biztosít az AAD regisztrált eszközökhöz, és segít az alkalmazásnak a feltételes hozzáférési házirendek követésében.

A következő lépések az SSO engedélyezésének módját egy hitelesítési bróker használatával engedélyezheti az alkalmazáshoz:

1. Regisztráljon egy brókerkompatibilis átirányítási URI-formátumot az alkalmazás info.plistájában. A bróker kompatibilis átirányítási `msauth.<app.bundle.id>://auth`URI formátum . Cserélje ki a '<app.bundle.id app.bundle.id>" helyett az alkalmazás csomagazonosítóját. Például:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

1. A következő sémák felvétele az alkalmazás `LSApplicationQueriesSchemes`info.p listájára az alábbi:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

1. A visszahívások `AppDelegate.m` kezeléséhez adja hozzá a következőket a fájlhoz:

    C célkitűzés:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift:
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```
    
**Ha Xcode 11-et használ,** akkor az MSAL visszahívását a `SceneDelegate` fájlba kell helyeznie.
Ha támogatja mind az UISceneDelegate, mind az UIApplicationDelegate programot a régebbi iOS rendszerrel való kompatibilitásérdekében, az MSAL-visszahívást mindkét fájlba be kell helyezni.

C célkitűzés:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Swift:

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

## <a name="next-steps"></a>További lépések

További információ a [hitelesítési folyamatokról és az alkalmazásforgatókönyvekről](authentication-flows-app-scenarios.md)