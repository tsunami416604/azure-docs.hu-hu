---
title: Átirányítási URI-k használata Az MSAL (iOS/macOS) használatával | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg a Microsoft Authentication Library for ObjectiveC (MSAL iOS és macOS) és az Azure AD Authentication Library for ObjectiveC (ADAL) közötti különbségeket. ObjC), és hogyan vándorolnak közöttük.
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: jak
ms.custom: aaddev
ms.openlocfilehash: c36c6b1b1b08de6d2db9a7f7f9ebd3b162c02383
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240841"
---
# <a name="using-redirect-uris-with-the-microsoft-authentication-library-for-ios-and-macos"></a>Átirányítási URI-k használata az iOS és macOS Rendszerhez szükséges Microsoft hitelesítési könyvtárral

Amikor egy felhasználó hitelesíti magát, az Azure Active Directory (Azure AD) elküldi a jogkivonatot az alkalmazásnak az Azure AD-alkalmazásban regisztrált átirányítási URI használatával.

A Microsoft authentication library (MSAL) megköveteli, hogy az átirányítási URI-t regisztrálni kell az Azure AD alkalmazás egy adott formátumban. Az MSAL alapértelmezett átirányítási URI-t használ, ha nem ad meg egyet. A formátum `msauth.[Your_Bundle_Id]://auth`.

Az alapértelmezett átirányítási URI-formátum a legtöbb alkalmazáshoz és forgatókönyvhöz működik, beleértve a felügyelt hitelesítést és a rendszerwebnézetet. Amikor csak lehetséges, használja az alapértelmezett formátumot.

Előfordulhat azonban, hogy módosítania kell az átirányítási URI-t a speciális forgatókönyvekhez, az alábbiakban leírtak szerint.

## <a name="scenarios-that-require-a-different-redirect-uri"></a>Eltérő átirányítási URI-t igénylő esetek

### <a name="cross-app-single-sign-on-sso"></a>Alkalmazásközi egyszeri bejelentkezés (Egyszeri bejelentkezés)

Ahhoz, hogy a Microsoft Identity platform ossza meg a jogkivonatokat az alkalmazások között, minden alkalmazásnak ugyanazzal az ügyfélazonosítóval vagy alkalmazásazonosítóval kell rendelkeznie. Ez az egyedi azonosító, amelyet akkor ad meg, amikor regisztrálta az alkalmazást a portálon (nem pedig az alkalmazásköteg-azonosítót, amelyet alkalmazásonként regisztrál az Apple-nél).

Az átirányítási URI-knak minden iOS-alkalmazásesetében eltérőnek kell lenniük. Ez lehetővé teszi, hogy a Microsoft identity service egyedileg azonosítsa az alkalmazásazonosítót megosztó különböző alkalmazásokat. Minden alkalmazás rendelkezhet több átirányítási URI-k regisztrálva az Azure Portalon. A csomag minden alkalmazás a csomag ban lesz egy másik átirányítási URI.Each app in your suite will have a different redirect URI. Példa:

A következő alkalmazásregisztráció az Azure Portalon:

    Client ID: ABCDE-12345 (this is a single client ID)
    RedirectUris: msauth.com.contoso.app1://auth, msauth.com.contoso.app2://auth, msauth.com.contoso.app3://auth

Az App1 `msauth.com.contoso.app1://auth` az átirányítási alkalmazásthasználja2 az `msauth.com.contoso.app2://auth` App3-at`msauth.com.contoso.app1://auth`

### <a name="migrating-from-adal-to-msal"></a>Áttelepítés ADAL-ról MSAL-ra

Az Azure AD hitelesítési könyvtárat (ADAL) az MSAL-ba való áttelepítésekor előfordulhat, hogy már rendelkezik az alkalmazáshoz konfigurált átirányítási URI-val. Továbbra is használhatja ugyanazt az átirányítási URI-t, amíg az ADAL-alkalmazás a felügyelt forgatókönyvek támogatására lett konfigurálva, és az átirányítási URI megfelel az MSAL átirányítási URI-formátum követelményeinek.

## <a name="msal-redirect-uri-format-requirements"></a>Az MSAL átirányítása URI formátumra vonatkozó követelmények

* Az MSAL átirányítási URI-nak a`<scheme>://host`

    Hol `<scheme>` található az alkalmazást azonosító egyedi karakterlánc. Elsősorban az alkalmazás csomagazonosítóján alapul, hogy garantálja az egyediséget. Ha például az alkalmazás bundle-azonosítója, az `com.contoso.myapp`átirányítási URI a `msauth.com.contoso.myapp://auth`következő formában jelenik meg: .

    Ha ADAL-ról migrál, az átirányítási URI valószínűleg a következő formátummal fog rendelkezni: `<scheme>://[Your_Bundle_Id]`, hol `scheme` található egy egyedi karakterlánc. Ez a formátum az MSAL használatakor is működni fog.

* `<scheme>`regisztrálnia kell az alkalmazás Info.plist `CFBundleURLTypes > CFBundleURLSchemes`alatt .  Ebben a példában az Info.plist forráskódként lett megnyitva:

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>msauth.[BUNDLE_ID]</string>
            </array>
        </dict>
    </array>
    ```
    

Az MSAL ellenőrzi, hogy az átirányítási URI megfelelően regisztrál-e, és hibát ad vissza, ha nem.
    
* Ha univerzális hivatkozásokat szeretne átirányítási URI-ként használni, akkor a `<scheme>` rendszert a alkalmazásban kell és `https` nem is kell `CFBundleURLSchemes`deklarálni. Ehelyett konfigurálja az alkalmazást és a tartományt az Apple `handleMSALResponse:sourceApplication:` utasításai `MSALPublicClientApplication` szerint a Universal Links [for Developers](https://developer.apple.com/ios/universal-links/) oldalon, és hívja meg azt a módszert, amikor az alkalmazás univerzális hivatkozáson keresztül nyílik meg.

## <a name="use-a-custom-redirect-uri"></a>Egyéni átirányítási URI használata

Egyéni átirányítási URI használatához adja `redirectUri` át `MSALPublicClientApplicationConfig` a paramétert, és adja át az objektumot `MSALPublicClientApplication` az objektum inicializálásakor. Ha az átirányítási URI érvénytelen, az `nil` inicializáló visszatér, és beállítja a `redirectURIError`további információkat.  Példa:

C célkitűzés:

```objc
MSALPublicClientApplicationConfig *config =
        [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                        redirectUri:@"your-redirect-uri"
                                                        authority:authority];
NSError *redirectURIError;
MSALPublicClientApplication *application =
        [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&redirectURIError];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: authority)
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```



## <a name="handle-the-url-opened-event"></a>A megnyitott URL-esemény kezelése

Az alkalmazásnak meg kell hívnia az MSAL-t, ha url-sémákon vagy univerzális hivatkozásokon keresztül kap választ. Hívja `handleMSALResponse:sourceApplication:` meg `MSALPublicClientApplication` az alkalmazás megnyitásakori metódust. Íme egy példa az egyéni sémákra:

C célkitűzés:

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift:

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```



## <a name="next-steps"></a>További lépések

További információ a [hitelesítési folyamatokról és az alkalmazásforgatókönyvekről](authentication-flows-app-scenarios.md)
