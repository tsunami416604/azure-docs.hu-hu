---
title: Webes API-kat meghívó Mobile apps konfigurálása | Azure
titleSuffix: Microsoft identity platform
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
ms.openlocfilehash: fc25f13d0b0b8a264dcd47a5fdebb0533e93fb55
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132492"
---
# <a name="configure-a-mobile-app-that-calls-web-apis"></a>Webes API-kat meghívó mobil alkalmazás konfigurálása

Az alkalmazás létrehozása után megtudhatja, hogyan konfigurálhatja a kódot az alkalmazás regisztrációs paramétereinek használatával. A mobil alkalmazások a létrehozási keretrendszerbe való beépítéshez kapcsolódó bonyolultságot jelentenek.

## <a name="find-msal-support-for-mobile-apps"></a>MSAL-támogatás keresése a Mobile apps szolgáltatáshoz

A következő Microsoft Authentication Library-(MSAL-) típusok támogatják a Mobile Apps szolgáltatást.

MSAL | Leírás
------------ | ----------
![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Hordozható alkalmazások fejlesztéséhez használatos. A MSAL.NET a következő platformokat támogatja a Mobile-alkalmazások létrehozásához: Univerzális Windows-platform (UWP), Xamarin. iOS és Xamarin. Android.
![MSAL. iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL. iOS | Natív iOS-alkalmazások fejlesztésére szolgál Objective-C vagy SWIFT használatával.
![MSAL. Android](media/sample-v2-code/logo_android.png) <br/> MSAL. Android | Natív Android-alkalmazások fejlesztéséhez használható a Java Android rendszerhez.

## <a name="instantiate-the-application"></a>Az alkalmazás példányának példánya

### <a name="android"></a>Android

A mobil alkalmazások a `PublicClientApplication` osztályt használják. A következő módon hozhatja létre:

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

Az iOS-hez készült Mobile-alkalmazásoknak a `MSALPublicClientApplication` osztályt kell létrehozniuk. Az osztály létrehozásához használja a következő kódot. 

```objc
NSError *msalError = nil;
     
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

[További MSALPublicClientApplicationConfig-tulajdonságok](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options) felülbírálják az alapértelmezett szolgáltatót, megadhatnak egy átirányítási URI-t, vagy megváltoztathatják a MSAL-token gyorsítótárazásának viselkedését. 

### <a name="xamarin-or-uwp"></a>Xamarin vagy UWP

Ez a szakasz azt ismerteti, hogyan hozható létre az alkalmazás a Xamarin. iOS, a Xamarin. Android és a UWP alkalmazások számára.

#### <a name="instantiate-the-application"></a>Az alkalmazás példányának példánya

A Xamarin vagy UWP az alkalmazás létrehozásának legegyszerűbb módja az alábbi kód használata. Ebben a kódban a `ClientId` a regisztrált alkalmazás GUID azonosítója.

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

További `With<Parameter>` módszerekkel állítsa be a felhasználói felület szülőjét, bírálja felül az alapértelmezett szolgáltatót, adja meg a telemetria ügyfél nevét és verzióját, adjon meg egy átirányítási URI-t, és adja meg a használni kívánt HTTP-gyárat. Lehetséges, hogy a HTTP-gyár használható például a proxyk kezelésére, valamint a telemetria és a naplózás megadására. 

A következő szakaszokban további információkat talál az alkalmazás létrehozásáról.

##### <a name="specify-the-parent-ui-window-or-activity"></a>Szülő felhasználói felület, ablak vagy tevékenység meghatározása

Androidon át kell adni a fölérendelt tevékenységet az interaktív hitelesítés előtt. Az iOS-ben a közvetítő használata esetén be kell `ViewController`. A UWP megegyező módon érdemes átadni a fölérendelt ablakot. Ezt a token beszerzése után adja át. Az alkalmazás létrehozásakor azonban a visszahívást is megadhatja meghatalmazottként, amely visszaadja a `UIParent`.

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

Androidon a [`CurrentActivityPlugin`](https://github.com/jamesmontemagno/CurrentActivityPlugin)használatát javasoljuk. Az eredményül kapott `PublicClientApplication` Builder-kód a következő példához hasonlóan néz ki:

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="find-more-app-building-parameters"></a>További alkalmazás-létrehozási paraméterek keresése

Az `PublicClientApplicationBuilder`on elérhető módszerek listáját a [módszerek listájában](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)tekintheti meg.

A `PublicClientApplicationOptions`ban elérhető összes beállítás leírását a [dokumentációban](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)találja.

## <a name="tasks-for-xamarin-ios"></a>Xamarin iOS-feladatok

Ha a MSAL.NET-t használja a Xamarin iOS-eszközön, hajtsa végre a következő feladatokat.

* [A `OpenUrl` függvény felülbírálása és implementálása `AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
* [Kulcstartó-csoportok engedélyezése](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
* [Jogkivonat-gyorsítótár megosztásának engedélyezése](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
* [Kulcstartó-hozzáférés engedélyezése](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

További információ: [Xamarin iOS-megfontolások](msal-net-xamarin-ios-considerations.md).

## <a name="tasks-for-msal-for-ios-and-macos"></a>Az iOS és a macOS rendszerhez készült MSAL feladatai

Ezek a feladatok akkor szükségesek, ha iOS-és macOS-MSAL használ:

* [A `openURL` visszahívás megvalósítása](#brokered-authentication-for-msal-for-ios-and-macos)
* [Kulcstartó-hozzáférési csoportok engedélyezése](howto-v2-keychain-objc.md)
* [Böngészők és webnézetek testreszabása](customize-webviews.md)

## <a name="tasks-for-xamarinandroid"></a>Xamarin. Android-feladatok

Ha a Xamarin. Android-t használja, tegye a következő feladatokat:

- [Győződjön meg arról, hogy a vezérlő visszatér a MSAL, miután a hitelesítési folyamat interaktív része véget ér](msal-net-xamarin-android-considerations.md#ensure-that-control-returns-to-msal)
- [Az Android-jegyzékfájl frissítése](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [A beágyazott webes nézet használata (nem kötelező)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [A szükséges hibák megoldása](msal-net-xamarin-android-considerations.md#troubleshoot)

További információ: [Xamarin. Android megfontolások](msal-net-xamarin-android-considerations.md).

Az androidos böngészőkkel kapcsolatos megfontolásokat lásd: [Xamarin. Android-specifikus megfontolások a MSAL.net](msal-net-system-browser-android-considerations.md).

#### <a name="tasks-for-uwp"></a>UWP kapcsolatos feladatok

A UWP-on vállalati hálózatokat használhat. A következő szakasz azokat a feladatokat ismerteti, amelyeket el kell végeznie a vállalati forgatókönyvben.

További információ: [UWP-specifikus megfontolások a MSAL.net](msal-net-uwp-considerations.md).

## <a name="configure-the-application-to-use-the-broker"></a>Az alkalmazás konfigurálása a közvetítő használatára

Az Android és az iOS rendszeren a brókerek a következőket teszik lehetővé:

- **Egyszeri bejelentkezés (SSO)** : használhat egyszeri bejelentkezést az Azure Active Directory (Azure ad) szolgáltatásban regisztrált eszközökhöz. Ha SSO-t használ, a felhasználóknak nem kell bejelentkezniük minden alkalmazásba.
- **Eszköz azonosítása**: Ez a beállítás engedélyezi az Azure ad-eszközökhöz kapcsolódó feltételes hozzáférési szabályzatokat. A hitelesítési folyamat az eszköznek a munkahelyhez való csatlakozásakor létrehozott tanúsítványát használja.
- **Alkalmazás-azonosítás ellenőrzése**: amikor egy alkalmazás meghívja a közvetítőt, átadja az átirányítási URL-címét. Ezután a közvetítő ellenőrzi.

### <a name="enable-the-broker-on-xamarin"></a>A közvetítő engedélyezése a Xamarin

Ha engedélyezni szeretné a közvetítőt a Xamarin, használja a `WithBroker()` paramétert a `PublicClientApplicationBuilder.CreateApplication` metódus meghívásakor. Alapértelmezés szerint a `.WithBroker()` értéke TRUE (igaz). 

A Xamarin. iOS felügyelt hitelesítésének engedélyezéséhez kövesse a jelen cikk [Xamarin. iOS című szakaszának](#enable-brokered-authentication-for-xamarin-ios) lépéseit.

### <a name="enable-the-broker-for-msal-for-android"></a>Az Android rendszerhez készült MSAL-ügynök engedélyezése

További információ a brókerek Androidon való engedélyezéséről: felügyelt [hitelesítés az Androidon](brokered-auth.md). 

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>Az iOS és a macOS rendszerhez készült MSAL-ügynök engedélyezése

A felügyelt hitelesítés alapértelmezés szerint engedélyezve van az Azure AD-forgatókönyvek esetében az iOS és a macOS rendszerhez készült MSAL. 

A következő szakasz útmutatást nyújt az alkalmazásnak a Xamarin. iOS vagy a MSAL for iOS és macOS rendszerhez készült MSAL való közvetítéses hitelesítéshez való konfigurálásához. A két utasításban a lépések némelyike eltérő.

### <a name="enable-brokered-authentication-for-xamarin-ios"></a>Felügyelt hitelesítés engedélyezése a Xamarin iOS-hez

A jelen szakaszban ismertetett lépéseket követve engedélyezheti a Xamarin. iOS-alkalmazásnak, hogy beszéljen a [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) alkalmazással.

#### <a name="step-1-enable-broker-support"></a>1\. lépés: a közvetítő támogatásának engedélyezése

A Broker-támogatás alapértelmezés szerint le van tiltva. Ezt az egyéni `PublicClientApplication` osztályhoz engedélyezheti. A `WithBroker()` paramétert akkor használja, ha a `PublicClientApplication` osztályt `PublicClientApplicationBuilder`használatával hozza létre. A `WithBroker()` paraméter alapértelmezés szerint True (igaz) értékre van állítva.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>2\. lépés: a visszahívást kezelő AppDelegate frissítése

Amikor a MSAL.NET meghívja a közvetítőt, a közvetítő ezután visszahívja az alkalmazást. A `AppDelegate.OpenUrl` metódus használatával hívja vissza. Mivel a MSAL megvárja a közvetítőtől érkező választ, az alkalmazásnak együtt kell működnie a MSAL.NET visszahívásához. Ezt a viselkedést úgy állíthatja be, hogy a `AppDelegate.cs` fájl frissítésével felülbírálja a metódust, ahogy az alábbi kód látható.

```csharp
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

Ezt a metódust az alkalmazás minden indításakor meg kell hívni. Lehetőség van a közvetítő válaszának feldolgozására és a MSAL.NET elindított hitelesítési folyamat befejezésére.

#### <a name="step-3-set-a-uiviewcontroller"></a>3\. lépés: UIViewController () beállítása

A Xamarin iOS esetében általában nem kell beállítania az objektum ablakát. Ebben az esetben azonban úgy kell beállítania, hogy a brókertől érkező válaszokat küldjön és fogadjon. Egy objektum ablakának beállításához a `AppDelegate.cs`ban állítson be egy `ViewController`.

Az objektum ablakának beállításához kövesse az alábbi lépéseket:

1. A `AppDelegate.cs`ban állítsa be a `App.RootViewController` egy új `UIViewController()`. Ez a beállítás biztosítja, hogy a közvetítőnek meghívása `UIViewController`tartalmaz. Ha nincs megfelelően beállítva, a következő hibaüzenet jelenhet meg:

    `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker."`

1. A `AcquireTokenInteractive` hívásban használja a `.WithParentActivityOrWindow(App.RootViewController)`. Adja át a hivatkozást a használni kívánt objektum-ablakra. Például:

    Az `App.cs` szkriptben:
    ```csharp
       public static object RootViewController { get; set; }
    ```
    Az `AppDelegate.cs` szkriptben:
    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```
    A `AcquireToken` hívásban:
    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```
    
#### <a name="step-4-register-a-url-scheme"></a>4\. lépés: URL-séma regisztrálása

A MSAL.NET URL-címeket használ a közvetítő meghívásához, majd visszaküldi a közvetítői választ az alkalmazásnak. A kör alakú útvonal befejezéséhez regisztrálja az alkalmazás URL-sémáját a `Info.plist` fájlban. 

Az alkalmazás URL-sémájának regisztrálásához kövesse az alábbi lépéseket:

1. Előtag `CFBundleURLSchemes` a `msauth`. 
1. `CFBundleURLName` hozzáadása a végéhez. Kövesse ezt a mintát: 

   `$"msauth.(BundleId)"`

   Itt `BundleId` egyedileg azonosítja az eszközt. Ha például a `BundleId` `yourcompany.xforms`, az URL-séma `msauth.com.yourcompany.xforms`.
    
   > [!NOTE]
   > Ez az URL-séma az átirányítási URI részévé válik, amely egyedileg azonosítja az alkalmazást, amikor megkapja a közvetítő válaszát.
    
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
    
#### <a name="step-5-add-to-the-lsapplicationqueriesschemes-section"></a>5\. lépés: Hozzáadás a összes szakaszhoz

A MSAL `–canOpenURL:` használatával ellenőrizze, hogy a közvetítő telepítve van-e az eszközön. Az iOS 9-es verzióban az Apple zárolta azokat a sémákat, amelyeket az alkalmazás lekérhet.

Adja hozzá a `msauthv2`t a `Info.plist` fájl `LSApplicationQueriesSchemes` szakaszához, ahogy az alábbi példában látható:

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>Felügyelt hitelesítés iOS és macOS rendszerű MSAL

A felügyelt hitelesítés alapértelmezés szerint engedélyezve van az Azure AD-forgatókönyvek esetében.

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>1\. lépés: a visszahívást kezelő AppDelegate frissítése

Ha az iOS-és macOS-MSAL meghívja a közvetítőt, a közvetítő a `openURL` metódus használatával visszahívja az alkalmazást. Mivel a MSAL megvárja a közvetítőtől érkező választ, az alkalmazásnak együtt kell működnie a MSAL visszahívásához. Állítsa be ezt a képességet úgy, hogy a `AppDelegate.m` fájl frissítésével felülbírálja a metódust, az alábbi példákban látható módon.

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

> [!NOTE]
> Ha az iOS 13 vagy újabb verzióban `UISceneDelegate`, akkor helyezze a MSAL visszahívást a `UISceneDelegate` `scene:openURLContexts:`ba. Az MSAL `handleMSALResponse:sourceApplication:` csak egyszer hívható meg minden URL-címhez.
>
> További információt az [Apple dokumentációjában](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)talál.

#### <a name="step-2-register-a-url-scheme"></a>2\. lépés: URL-séma regisztrálása

Az iOS-hez és macOS-hez készült MSAL URL-címeket használ a közvetítő meghívásához, majd visszaküldi az alkalmazásnak a közvetítő válaszát. A kör alakú útvonal befejezéséhez regisztráljon egy URL-sémát az alkalmazáshoz a `Info.plist` fájlban.

Séma regisztrálása az alkalmazáshoz: 

1. Előtagként adja meg az egyéni URL-sémát `msauth`. 

1. Adja hozzá a köteg azonosítóját a séma végéhez. Kövesse ezt a mintát: 

   `$"msauth.(BundleId)"`

   Itt `BundleId` egyedileg azonosítja az eszközt. Ha például a `BundleId` `yourcompany.xforms`, az URL-séma `msauth.com.yourcompany.xforms`.
  
   > [!NOTE]
   > Ez az URL-séma az átirányítási URI részévé válik, amely egyedileg azonosítja az alkalmazást, amikor megkapja a közvetítő válaszát. Győződjön meg arról, hogy az átirányítási URI formátuma `msauth.(BundleId)://auth` van regisztrálva az alkalmazáshoz a [Azure Portalban](https://portal.azure.com).
  
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

#### <a name="step-3-add-lsapplicationqueriesschemes"></a>3\. lépés: összes hozzáadása

Ha telepítve van, `LSApplicationQueriesSchemes` hozzáadásával engedélyezheti a Microsoft Authenticator alkalmazás hívásait.

> [!NOTE]
> A `msauthv3` sémára akkor van szükség, ha az alkalmazást a Xcode 11-ös vagy újabb verziójának használatával állítják össze. 

Íme egy példa a `LSApplicationQueriesSchemes`hozzáadására:

```XML 
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Felügyelt hitelesítés a Xamarin. Android rendszerhez

A MSAL.NET nem támogatja az Android rendszerhez készült közvetítőket.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Token beszerzése](scenario-mobile-acquire-token.md)
