---
title: Böngészők testreszabása & WebViews (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan szabhatja testre az MSAL iOS/macOS böngészőt a felhasználók bejelentkezéséhez.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 759f61860c62bcb668db6844df28c52fa28eac80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085909"
---
# <a name="how-to-customize-browsers-and-webviews-for-iosmacos"></a>Útmutató: Böngészők és webnézetek testreszabása iOS/macOS rendszeren

Az interaktív hitelesítéshez webböngésző szükséges. IOS rendszeren a Microsoft Authentication Library (MSAL) alapértelmezés szerint a rendszer webböngészőjét használja (amely az alkalmazás tetején jelenhet meg) interaktív hitelesítéssel a felhasználók bejelentkezéséhez. A rendszerböngésző használata azzal az előnnyel jár, hogy megosztja az Egyszeri bejelentkezés (SSO) állapotot más alkalmazásokkal és webalkalmazásokkal.

A felhasználói élményt úgy módosíthatja, hogy a konfigurációt a webes tartalom megjelenítésének egyéb beállításaira szabja, például:

Csak iOS esetén:

- [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [SFAuthentication session](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

iOS és macOS esetén:

- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

Az MSAL csak a `WKWebView`macOS rendszeren támogatja a .

## <a name="system-browsers"></a>Rendszerböngészők

IOS , `ASWebAuthenticationSession` `SFAuthenticationSession`, `SFSafariViewController` és rendszerböngészőnek minősül. Általánosságban elmondható, hogy a rendszerböngészők cookie-kat és egyéb webhelyadatokat osztanak meg a Safari böngészőalkalmazással.

Alapértelmezés szerint az MSAL dinamikusan észleli az iOS-verziót, és kiválasztja az adott verzióban elérhető ajánlott rendszerböngészőt. Az iOS 12+ `ASWebAuthenticationSession`rendszeren ez lesz . 

| Verzió | Webböngésző |
|:-------------:|:-------------:|
| iOS 12+ | ASWebAuthenticationSession |
| iOS 11 | SFAuthentication session |
| iOS 10 | SFSafariViewController |

A fejlesztők másik rendszerböngészőt is választhatnak az MSAL-alkalmazásokhoz:

- `SFAuthenticationSession`az iOS 11 `ASWebAuthenticationSession`verziója.
- `SFSafariViewController`általánosabb célja, és egy felületet biztosít a böngészéshez az interneten, és használható bejelentkezési célokra is. Az iOS 9 és 10 rendszerben a cookie-k és a webhely egyéb adatai meg vannak osztva a Safarival , de nem az iOS 11-es és újabb verzióiban.

## <a name="in-app-browser"></a>Alkalmazáson belüli böngésző

[A WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) egy alkalmazáson belüli böngésző, amely webes tartalmat jelenít meg. Nem osztja meg a cookie-kat vagy a webhely adatait más **WKWebView** példányokkal vagy a Safari böngészővel. A WKWebView egy platformfüggetlen böngésző, amely iOS és macOS rendszeren is elérhető.

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>A cookie-k megosztása és az egyszeri bejelentkezés (SSO) vonatkozásai

Az Ön által használt böngésző hatással van az SSO-élményre a cookie-k megosztása miatt. Az alábbi táblázatok az SSO-élményeket foglalják össze böngészőnként.

| Technológia    | Böngésző típusa  | iOS elérhetősége | a macOS elérhetősége | Cookie-k és egyéb adatok megosztása  | MSAL elérhetősége | SSO |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | Rendszer | iOS12 és fel | macOS 10.15 és fel | Igen | csak iOS esetén | w/ Safari-példányok
| [SFAuthentication session](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | Rendszer | iOS11 és fel | N/A | Igen | csak iOS esetén |  w/ Safari-példányok
| [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | Rendszer | iOS11 és fel | N/A | Nem | csak iOS esetén | Nem**
| **SFSafariViewController** | Rendszer | iOS10 | N/A | Igen | csak iOS esetén |  w/ Safari-példányok
| **WKWebView**  | Alkalmazáson belüli | iOS8 és fel | macOS 10.10 és fel | Nem | iOS és macOS | Nem**

** Ahhoz, hogy az Egyszeri szolgáltatás működjön, a jogkivonatokat meg kell osztani az alkalmazások között. Ehhez jogkivonat-gyorsítótárra vagy brókeralkalmazásra, például az iOS-hez való Microsoft Authenticator alkalmazásra van szükség.

## <a name="change-the-default-browser-for-the-request"></a>A kérelem alapértelmezett böngészőjének módosítása

A felhasználói élmény követelményeitől függően alkalmazáson belüli böngészőt vagy egy adott rendszerböngészőt a következő tulajdonság módosításával `MSALWebviewParameters`használhat:

```objc
@property (nonatomic) MSALWebviewType webviewType;
```

## <a name="change-per-interactive-request"></a>Módosítás interaktív kérésenként

Minden kérelem beállítható úgy, hogy felülbírálja `MSALInteractiveTokenParameters.webviewParameters.webviewType` az alapértelmezett böngészőt a tulajdonság módosításával, mielőtt átadná azt az `acquireTokenWithParameters:completionBlock:` API-nak.

Emellett az MSAL támogatja az `WKWebView` egyéni átadást a `MSALInteractiveTokenParameters.webviewParameters.customWebView` tulajdonság beállításával.

Példa:

Objective-C
```objc
UIViewController *myParentController = ...;
WKWebView *myCustomWebView = ...;
MSALWebviewParameters *webViewParameters = [[MSALWebviewParameters alloc] initWithParentViewController:myParentController];
webViewParameters.webviewType = MSALWebviewTypeWKWebView;
webViewParameters.customWebview = myCustomWebView;
MSALInteractiveTokenParameters *interactiveParameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"myscope"] webviewParameters:webViewParameters];
    
[app acquireTokenWithParameters:interactiveParameters completionBlock:completionBlock];
```
Swift
```swift
let myParentController: UIViewController = ...
let myCustomWebView: WKWebView = ...
let webViewParameters = MSALWebviewParameters(parentViewController: myParentController)
webViewParameters.webviewType = MSALWebviewType.wkWebView
webViewParameters.customWebview = myCustomWebView
let interactiveParameters = MSALInteractiveTokenParameters(scopes: ["myscope"], webviewParameters: webViewParameters)

app.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
```

Ha egyéni webmegtekintést használ, az értesítések jelzik a megjelenített webes tartalom állapotát, például:

```objc
/*! Fired at the start of a resource load in the webview. The URL of the load, if available, will be in the @"url" key in the userInfo dictionary */
extern NSString *MSALWebAuthDidStartLoadNotification;

/*! Fired when a resource finishes loading in the webview. */
extern NSString *MSALWebAuthDidFinishLoadNotification;

/*! Fired when web authentication fails due to reasons originating from the network. Look at the @"error" key in the userInfo dictionary for more details.*/
extern NSString *MSALWebAuthDidFailNotification;

/*! Fired when authentication finishes */
extern NSString *MSALWebAuthDidCompleteNotification;

/*! Fired before ADAL invokes the broker app */
extern NSString *MSALWebAuthWillSwitchToBrokerApp;
```

### <a name="options"></a>Beállítások

Az MSAL által támogatott webböngésző-típusok deklarálva vannak az [MSALWebviewType enum](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALDefinitions.h#L47)

```objc
typedef NS_ENUM(NSInteger, MSALWebviewType)
{
#if TARGET_OS_IPHONE
    // For iOS 11 and up, uses AuthenticationSession (ASWebAuthenticationSession
    // or SFAuthenticationSession).
    // For older versions, with AuthenticationSession not being available, uses
    // SafariViewController.
    MSALWebviewTypeDefault,
    
    // Use SFAuthenticationSession/ASWebAuthenticationSession
    MSALWebviewTypeAuthenticationSession,
    
    // Use SFSafariViewController for all versions.
    MSALWebviewTypeSafariViewController,
    
#endif
    // Use WKWebView
    MSALWebviewTypeWKWebView,
};
```

## <a name="next-steps"></a>További lépések

További információ a [hitelesítési folyamatokról és az alkalmazásforgatókönyvekről](authentication-flows-app-scenarios.md)
