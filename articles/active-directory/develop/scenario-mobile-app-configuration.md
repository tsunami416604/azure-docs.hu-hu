---
title: Webes API-kat meghívó mobil alkalmazás (kód konfigurációja) – Microsoft Identity platform | Azure
description: Ismerje meg, hogyan hozhat létre webes API-kat meghívó mobil alkalmazást (az alkalmazás kódjának konfigurációja)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 168fbb275f70acd229dfd8f2e3f0d4c325db0f94
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71678017"
---
# <a name="mobile-app-that-calls-web-apis---code-configuration"></a>Webes API-kat meghívó mobil alkalmazás – kód konfigurálása

Az alkalmazás létrehozása után megtudhatja, hogyan konfigurálhatja a kódot az alkalmazás regisztrációs paramétereinek használatával. A mobil alkalmazások is rendelkeznek néhány összetett sajátossággal, amelyek az alkalmazások összeállításához használt keretrendszerbe illeszkednek.

## <a name="msal-libraries-supporting-mobile-apps"></a>Mobile apps-t támogató MSAL-kódtárak

A Mobile Apps szolgáltatást támogató Microsoft-kódtárak a következők:

  MSAL-könyvtár | Leírás
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Hordozható alkalmazások fejlesztéséhez. A MSAL.NET által támogatott platformok a UWP, a Xamarin. iOS és a Xamarin. Android platformra épülnek.
  ![MSAL. iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL. iOS | Natív iOS-alkalmazások fejlesztése Objective-C vagy SWIFT használatával
  ![MSAL. Android](media/sample-v2-code/logo_android.png) <br/> MSAL. Android | Natív Android-alkalmazások fejlesztése Java-ban Androidon

## <a name="instantiating-the-application"></a>Az alkalmazás példányának példánya

### <a name="android"></a>Android

A Mobile Applications `PublicClientApplication` az osztályt használja. A következő módon hozhatja létre:

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

Az iOS-hez készült Mobile-alkalmazásoknak `MSALPublicClientApplication` az osztályt kell létrehozniuk.

Objective-C:

```objc
NSError *msalError = nil;
     
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

[További MSALPublicClientApplicationConfig-tulajdonságok](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options) is felülbírálják az alapértelmezett szolgáltatót, megadhatnak egy átirányítási URI-t vagy megváltoztathatják a MSAL-token gyorsítótárazási viselkedését. 

### <a name="xamarin-or-uwp"></a>Xamarin vagy UWP

A következő bekezdés ismerteti, hogyan hozható létre az alkalmazás a Xamarin. iOS, a Xamarin. Android és a UWP alkalmazások számára.

#### <a name="instantiating-the-application"></a>Az alkalmazás példányának példánya

A Xamarin vagy UWP az alkalmazás létrehozásának legegyszerűbb módja a következő, ahol a `ClientId` a regisztrált alkalmazás GUID-azonosítója.

```CSharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

A felhasználói felület szülőjének beállítása, az alapértelmezett szolgáltató felülbírálása, az ügyfél nevének és verziószámának megadása (telemetria esetén) további*paraméterekkel* rendelkezik, adjon meg egy átirányítási URI-t, adja meg a használni kívánt http-gyárat (például a proxyk kezelésére, adja meg a következőt: telemetria és naplózás). Ez a következő bekezdések témája.

##### <a name="specifying-the-parent-uiwindowactivity"></a>A szülő felhasználói felület/ablak/tevékenység meghatározása

Androidon át kell adni a fölérendelt tevékenységet az interaktív hitelesítés előtt. Az iOS-ben a közvetítő használata esetén át kell adnia a ViewController. A UWP megegyező módon érdemes átadni a fölérendelt ablakot. Ez akkor lehetséges, ha a jogkivonat beszerzése után a visszahívást is megadhatja az alkalmazás létrehozásakor, amikor a delegált visszaküldi a UIParent.

```CSharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

Az Androidon az `CurrentActivityPlugin` [itt](https://github.com/jamesmontemagno/CurrentActivityPlugin)ajánlott használni.  Ezt követően `PublicClientApplication` a Builder-kód így fog kinézni:

```CSharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="more-app-building-parameters"></a>További alkalmazás-létrehozási paraméterek

- Az összes elérhető módosító listájának megtekintéséhez tekintse `PublicClientApplicationBuilder`meg a Reference dokumentáció [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- A `PublicClientApplicationOptions` [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)-ben elérhető összes beállítás leírását a dokumentációban találja

## <a name="xamarin-ios-specific-considerations"></a>Xamarin iOS-specifikus megfontolások

A Xamarin iOS esetében több szempontot is figyelembe kell venni a MSAL.NET használatakor:

1. [A `OpenUrl` függvény felülbírálása és implementálása a`AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
1. [Kulcstartó-csoportok engedélyezése](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
1. [Jogkivonat-gyorsítótár megosztásának engedélyezése](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
1. [Kulcstartó-hozzáférés engedélyezése](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

A részletek a [Xamarin iOS-megfontolásokban](msal-net-xamarin-ios-considerations.md) találhatók

## <a name="msal-for-ios-and-macos-specific-considerations"></a>MSAL az iOS-és macOS-specifikus megfontolásokhoz

Az iOS és a macOS rendszerhez készült MSAL használata esetén hasonló szempontokat kell figyelembe venni:

1. [A `openURL` visszahívás megvalósítása](#brokered-authentication-for-msal-for-ios-and-macos)
2. [Kulcstartó-hozzáférési csoportok engedélyezése](howto-v2-keychain-objc.md)
3. [Böngészők és webnézetek testreszabása](customize-webviews.md)

## <a name="xamarin-android-specific-considerations"></a>Xamarin Android-specifikus megfontolások

Az alábbi Xamarin Android-specifikusak:

- [Annak biztosítása, hogy a vezérlés visszakerüljön a MSAL, amint a hitelesítési folyamat interaktív része véget ér](msal-net-xamarin-android-considerations.md#ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends)
- [Az Android-jegyzékfájl frissítése](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [A beágyazott webes nézet használata (nem kötelező)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [Hibaelhárítás](msal-net-xamarin-android-considerations.md#troubleshooting)

A részletek a [Xamarin Android-megfontolásokban](msal-net-xamarin-android-considerations.md) érhetők el

Végül pedig van néhány sajátosság arról, hogy az androidos böngészőkről van szó. A [Xamarin az Android-specifikus szempontokat](msal-net-system-browser-android-considerations.md) ismertetik a MSAL.net

#### <a name="uwp-specific-considerations"></a>UWP-specifikus megfontolások

A UWP-on vállalati hálózatokat használhat. A MSAL-kódtár UWP-vel való használatával kapcsolatos további információkért lásd: [univerzális Windows-platform-specifikus megfontolások a MSAL.net](msal-net-uwp-considerations.md).

## <a name="configuring-the-application-to-use-the-broker"></a>Az alkalmazás konfigurálása a közvetítő használatára

### <a name="why-use-brokers-in-ios-and-android-applications"></a>Miért érdemes közvetítőket használni az iOS-és Android-alkalmazásokban?

Az Android és az iOS rendszeren a brókerek a következőket teszik lehetővé:

- Egyszeri bejelentkezés (SSO), ha az eszköz regisztrálva van a HRE-ben. A felhasználóknak nem kell bejelentkezniük minden alkalmazásba.
- Eszköz azonosítása. Lehetővé teszi az Azure AD-eszközökhöz kapcsolódó feltételes hozzáférési szabályzatok elérését az eszközön a munkahelyhez való csatlakozáskor létrehozott tanúsítvány elérésekor.
- Alkalmazás-azonosító ellenőrzése. Amikor egy alkalmazás meghívja a közvetítőt, átadja az átirányítási URL-címet, és ellenőrzi a közvetítőt.

### <a name="enable-the-broker-on-xamarin"></a>A közvetítő engedélyezése a Xamarin

Ezen funkciók egyikének engedélyezéséhez használja a `WithBroker()` paramétert a `PublicClientApplicationBuilder.CreateApplication` metódus meghívásakor. `.WithBroker()`Alapértelmezés szerint True (igaz) értékre van állítva. Kövesse az alábbi lépéseket a [Xamarin. iOS](#brokered-authentication-for-xamarinios)-hez.

### <a name="enable-the-broker-for-msal-for-android"></a>Az Android rendszerhez készült MSAL-ügynök engedélyezése

A bróker Androidon való engedélyezésével kapcsolatos információkért lásd: felügyelt [hitelesítés az Androidban](brokered-auth.md) . 

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>Az iOS és a macOS rendszerhez készült MSAL-ügynök engedélyezése

A felügyelt hitelesítés alapértelmezés szerint engedélyezve van az iOS és a macOS rendszerhez készült MSAL HRE forgatókönyvek esetében. Az [iOS és a MacOS](#brokered-authentication-for-msal-for-ios-and-macos)rendszerhez készült MSAL esetében kövesse az alábbi lépéseket az alkalmazás felügyelt hitelesítési támogatásának konfigurálásához. Vegye figyelembe, hogy egyes lépések eltérnek a [MSAL for Xamarin. iOS](#brokered-authentication-for-xamarinios) és [a MSAL for iOS és a MacOS](#brokered-authentication-for-msal-for-ios-and-macos)között.

### <a name="brokered-authentication-for-xamarinios"></a>Felügyelt hitelesítés a Xamarin. iOS-hez

Az alábbi lépéseket követve engedélyezheti a Xamarin. iOS-alkalmazásnak, hogy beszéljen a [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) alkalmazással.

#### <a name="step-1-enable-broker-support"></a>1\. lépés: A közvetítő támogatásának engedélyezése

A közvetítői támogatás alapja az engedélyezett`PublicClientApplication` . Alapértelmezés szerint le van tiltva. A (z) `WithBroker()` paramétert kell használnia (alapértelmezés szerint igaz értékre `PublicClientApplication` állítva) `PublicClientApplicationBuilder`a (z) használatával.

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>2\. lépés: AppDelegate frissítése a visszahívás kezeléséhez

Ha a MSAL.net meghívja a közvetítőt, akkor a közvetítő a `AppDelegate.OpenUrl` metódussal hívja vissza az alkalmazást. Mivel a MSAL megvárja a közvetítő válaszát, az alkalmazásnak együtt kell működnie a MSAL.NET visszahívásához. Ezt úgy teheti meg, `AppDelegate.cs` hogy a fájl frissítésével felülbírálja az alábbi metódust.

```CSharp
public override bool OpenUrl(UIApplication app, NSUrl url,
                             string sourceApplication,
                             NSObject annotation)
{
 if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
 {
  AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
  return true;
 }
 else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
 {
  return false;
 }
 return true;
}
```

Ezt a metódust az alkalmazás minden indításakor meghívja a rendszer, és lehetőséget biztosít a közvetítő válaszának feldolgozására és a MSAL.NET által kezdeményezett hitelesítési folyamat befejezésére.

#### <a name="step-3-set-a-uiviewcontroller"></a>3\. lépés: UIViewController () beállítása

A Xamarin iOS esetében általában nem kell beállítania egy objektum-ablakot, ebben az esetben azonban a brókertől érkező válaszok küldésére és fogadására van szükség. Még mindig `AppDelegate.cs`a-ben állítson be egy ViewController.

Az objektum ablakának beállításához tegye a következőket:

1) A `AppDelegate.cs`ben állítsa be `App.RootViewController` a-t `UIViewController()`egy új értékre. Ez gondoskodik arról `UIViewController` , hogy a közvetítő meghívása legyen. Ha nincs megfelelően beállítva, a következő hibaüzenet jelenhet meg:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) A AcquireTokenInteractive-hívásban használja a `.WithParentActivityOrWindow(App.RootViewController)` és a pass hivatkozást a használni kívánt Object (objektum) ablakra.

**Példa:**

Az `App.cs` szkriptben:
```CSharp
   public static object RootViewController { get; set; }
```
Az `AppDelegate.cs` szkriptben:
```CSharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
A jogkivonat beszerzése hívásban:
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

#### <a name="step-4-register-a-url-scheme"></a>4\. lépés: URL-séma regisztrálása

A MSAL.NET URL-címeket használ a közvetítő meghívásához, majd visszaküldi a közvetítői választ az alkalmazásnak. A kör alakú utazás befejezéséhez regisztrálnia kell egy URL-sémát az alkalmazásához a `Info.plist` fájlban.

Előtag a `CFBundleURLSchemes` - `msauth`val. Ezután adja `CFBundleURLName` hozzá a végéhez.

`$"msauth.(BundleId)"`

**Például:** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> Ez az URL-séma az alkalmazás egyedi azonosításához használt RedirectUri részévé válik a közvetítő válaszának fogadásakor.

```XML
 <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.yourcompany.xforms</string>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.com.yourcompany.xforms</string>
        </array>
      </dict>
    </array>
```

#### <a name="step-5-lsapplicationqueriesschemes"></a>5\. lépés: Összes

A MSAL `–canOpenURL:` használatával ellenőrizhető, hogy a közvetítő telepítve van-e az eszközön. Az iOS 9-es verziójában az Apple zárolta, hogy az alkalmazás milyen sémákat tud lekérdezni.

**Hozzáadás** **`msauthv2`** a fájl`Info.plist` szakaszához. `LSApplicationQueriesSchemes`

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>Felügyelt hitelesítés iOS és macOS rendszerű MSAL

A HRE-forgatókönyvek esetében a felügyelt hitelesítés alapértelmezés szerint engedélyezve van.

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>1\. lépés: AppDelegate frissítése a visszahívás kezeléséhez

Ha az iOS-és MacOS-MSAL meghívja a közvetítőt, akkor a közvetítő a `openURL` metódussal hívja vissza az alkalmazást. Mivel a MSAL megvárja a közvetítő válaszát, az alkalmazásnak együtt kell működnie a MSAL visszahívásához. Ezt úgy teheti meg, `AppDelegate.m` hogy a fájl frissítésével felülbírálja az alábbi metódust.

Objective-C:

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

Vegye figyelembe, hogy ha a UISceneDelegate-t iOS 13 +-on fogadta el, akkor a `scene:openURLContexts:` MSAL visszahívást a UISceneDelegate kell helyezni (lásd az [Apple dokumentációját](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)). Az `handleMSALResponse:sourceApplication:` MSAL csak egyszer hívható meg minden URL-címhez.

#### <a name="step-2-register-a-url-scheme"></a>2\. lépés: URL-séma regisztrálása

Az iOS és a macOS rendszerhez készült MSAL URL-címeket használ a közvetítő meghívásához, majd visszaküldi a közvetítői választ az alkalmazásnak. A kör alakú utazás befejezéséhez regisztrálnia kell egy URL-sémát az alkalmazásához a `Info.plist` fájlban.

Előtagként adja meg az egyéni `msauth`URL-sémáját. Ezután adja hozzá a **köteg-azonosítót** a végéhez.

`msauth.(BundleId)`

**Például:** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> Ez az URL-séma az alkalmazás egyedi azonosításához használt RedirectUri részévé válik a közvetítő válaszának fogadásakor. Győződjön meg arról, hogy az `msauth.(BundleId)://auth` [Azure Portalon](https://portal.azure.com)az alkalmazáshoz tartozó RedirectUri van regisztrálva.

```XML
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

#### <a name="step-3-lsapplicationqueriesschemes"></a>3\. lépés: Összes

**A `LSApplicationQueriesSchemes` hozzáadásával** engedélyezheti, hogy a rendszer meghívja a Microsoft Authenticator, ha telepítve van.
Vegye figyelembe, hogy a "msauthv3" sémára akkor van szükség, amikor az alkalmazást a Xcode 11-ös vagy újabb verziójával együtt fordítja 

```XML 
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Felügyelt hitelesítés a Xamarin. Android rendszerhez

A MSAL.NET még nem támogatja az Android rendszerhez készült közvetítőket.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Token beszerzése](scenario-mobile-acquire-token.md)
