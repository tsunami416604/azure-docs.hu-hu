---
title: Webes API-kat hívó mobilalkalmazások konfigurálása | Azure
titleSuffix: Microsoft identity platform
description: További információ a webes API-kat meghívjaó mobilalkalmazás létrehozásáról (az alkalmazás kódkonfigurációja)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/23/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 06475b53acf6c9161888e29723feab9cdc4336d5
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882726"
---
# <a name="configure-a-mobile-app-that-calls-web-apis"></a>Webes API-kat meghívjaó mobilalkalmazás konfigurálása

Az alkalmazás létrehozása után megtudhatja, hogyan konfigurálhatja a kódot az alkalmazás regisztrációs paramétereivel. A mobil alkalmazások a létrehozási keretbe való beilleszkedéssel kapcsolatos bonyodalmakat jelentenek.

## <a name="find-msal-support-for-mobile-apps"></a>Mobilalkalmazások MSAL-támogatásának keresése

A következő Microsoft Authentication Library (MSAL) típusok támogatják a mobilalkalmazásokat.

MSAL | Leírás
------------ | ----------
![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Hordozható alkalmazások fejlesztésére szolgál. MSAL.NET a következő platformokat támogatja mobilalkalmazás létrehozásához: Univerzális Windows platform (UWP), Xamarin.iOS és Xamarin.Android.
![MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL.iOS | Natív iOS-alkalmazások fejlesztésére szolgál az Objective-C vagy a Swift használatával.
![MSAL. Android](media/sample-v2-code/logo_android.png) <br/> MSAL. Android | Natív Android-alkalmazások fejlesztésére szolgál java for Android nyelven.

## <a name="instantiate-the-application"></a>Az alkalmazás példányosítása

### <a name="android"></a>Android

A mobilalkalmazások `PublicClientApplication` az osztályt használják. Így kell példányosítani:

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

Az iOS-es mobilalkalmazásoknak kell `MSALPublicClientApplication` az osztályt példányosítaniuk. Az osztály példányosításához használja a következő kódot. 

```objc
NSError *msalError = nil;
     
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

[A további MSALPublicClientApplicationConfig tulajdonságok](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options) felülbírálhatják az alapértelmezett jogosultságot, megadhatnak egy átirányítási URI-t, vagy módosíthatják az MSAL token gyorsítótárazásának viselkedését. 

### <a name="xamarin-or-uwp"></a>Xamarin vagy UWP

Ez a szakasz bemutatja, hogyan hozlétre az alkalmazás a Xamarin.iOS, Xamarin.Android és UWP alkalmazások.

#### <a name="instantiate-the-application"></a>Az alkalmazás példányosítása

A Xamarin vagy UWP, a legegyszerűbb módja annak, hogy példányosítani az alkalmazás segítségével a következő kódot. Ebben a `ClientId` kódban a regisztrált alkalmazás GUID azonosítója található.

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

További `With<Parameter>` módszerek meg a felhasználói felület szülő, felülbírálja az alapértelmezett hatóság, adja meg az ügyfél nevét és verzióját telemetriai adatok, adja meg az átirányítási URI, és adja meg a HTTP-gyár használata. A HTTP-gyár használható például proxyk kezelésére, valamint telemetriai adatok és naplózás megadására. 

A következő szakaszok további információt nyújtanak az alkalmazás példányosításáról.

##### <a name="specify-the-parent-ui-window-or-activity"></a>A szülő felhasználói felület, ablak vagy tevékenység megadása

Android on, át kell adnia a szülő tevékenység, mielőtt az interaktív hitelesítés. Az iOS rendszeren, amikor brókert használ, `ViewController`át kell adnia. Ugyanígy az UWP-n is előfordulhat, hogy a szülőablakban szeretne bemenni. Adja át, amikor beszerzi a jogkivonatot. De amikor létrehozza az alkalmazást, megadhatja a visszahívást delegáltként is, amely a visszaad. `UIParent`

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

Android on javasoljuk, hogy [`CurrentActivityPlugin`](https://github.com/jamesmontemagno/CurrentActivityPlugin)használja a . Az `PublicClientApplication` eredményül kapott szerkesztőkód a következő példához hasonlóan néz ki:

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="find-more-app-building-parameters"></a>További alkalmazásépítési paraméterek keresése

A(z) metódusok listáját `PublicClientApplicationBuilder`a > [Metódusok listájában láthatja.](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)

A alkalmazásban elérhető összes beállítás leírását a `PublicClientApplicationOptions` [hivatkozási dokumentációban](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)találja.

## <a name="tasks-for-xamarin-ios"></a>Feladatok xamarin iOS

Ha a MSAL.NET xamarin iOS rendszeren használja, végezze el a következő feladatokat.

* [A `OpenUrl` funkció felülbírálása és megvalósítása`AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
* [Kulcskarika-csoportok engedélyezése](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
* [Tokengyorsítótár-megosztás engedélyezése](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
* [Kulcskarika-hozzáférés engedélyezése](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

További információ: [Xamarin iOS szempontok](msal-net-xamarin-ios-considerations.md).

## <a name="tasks-for-msal-for-ios-and-macos"></a>Feladatok az MSAL-hoz iOS és macOS rendszeren

Ezekre a feladatokra akkor van szükség, ha iOS és macOS rendszerhez használja az MSAL-t:

* [A `openURL` visszahívás megvalósítása](#brokered-authentication-for-msal-for-ios-and-macos)
* [Kulcskarika-hozzáférési csoportok engedélyezése](howto-v2-keychain-objc.md)
* [Böngészők és WebView objektumok testreszabása](customize-webviews.md)

## <a name="tasks-for-xamarinandroid"></a>Feladatok Xamarin.Android

Ha xamarin.Android ot használ, tegye a következőket:

- [A hitelesítési folyamat interaktív részének befejezése után az MSAL-ra való visszavezérlés biztosítása](msal-net-xamarin-android-considerations.md#ensure-that-control-returns-to-msal)
- [Az Android-jegyzékfájl frissítése](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [A beágyazott webnézet használata (nem kötelező)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [Szükség esetén hibaelhárítás](msal-net-xamarin-android-considerations.md#troubleshoot)

További információ: [Xamarin.Android szempontok](msal-net-xamarin-android-considerations.md).

Az Android böngészőkkel kapcsolatos szempontokat a [Xamarin.Android-specifikus, MSAL.NET kapcsolatos szempontok ban](msal-net-system-browser-android-considerations.md)olvassa el.

#### <a name="tasks-for-uwp"></a>Feladatok az UWP-hez

Az UWP-n használhatja a vállalati hálózatokat. A következő szakaszok ismertetik azokat a feladatokat, amelyeket a vállalati forgatókönyvben el kell végeznie.

További információt az [UWP-specifikus MSAL.NET kapcsolatos szempontok](msal-net-uwp-considerations.md)című témakörben talál.

## <a name="configure-the-application-to-use-the-broker"></a>Konfigurálja az alkalmazást, hogy használja a bróker

Android és iOS rendszeren a brókerek a következőket teszik lehetővé:

- **Egyszeri bejelentkezés (SSO)**: Az Egyszeri bejelentkezés az Azure Active Directory (Azure AD) szolgáltatásban regisztrált eszközökhöz használható. Az SSO használatakor a felhasználóknak nem kell minden alkalmazásba bejelentkezniük.
- **Eszközazonosítás:** Ez a beállítás engedélyezi az Azure AD-eszközökhöz kapcsolódó feltételes hozzáférési szabályzatokat. A hitelesítési folyamat azt az eszköztanúsítványt használja, amely akkor jött létre, amikor az eszköz csatlakozott a munkahelyhez.
- **Alkalmazás-azonosítás ellenőrzése:** Amikor egy alkalmazás meghívja a brókert, átadja az átirányítási URL-címét. Aztán a bróker ellenőrzi.

### <a name="enable-the-broker-on-xamarin"></a>Engedélyezze a bróker tabellán a Xamarin

Ahhoz, hogy a bróker a `WithBroker()` Xamarin, `PublicClientApplicationBuilder.CreateApplication` használja a paramétert, amikor meghívja a módszert. Alapértelmezés szerint `.WithBroker()` igaz értékre van állítva. 

A Xamarin.iOS protokollozott hitelesítésének engedélyezéséhez kövesse a cikk [Xamarin.iOS szakaszának](#enable-brokered-authentication-for-xamarin-ios) lépéseit.

### <a name="enable-the-broker-for-msal-for-android"></a>Engedélyezze a bróker MSAL For Android

A bróker Android-alapú engedélyezéséről a [Közvetített hitelesítés Androidon](brokered-auth.md)című témakörben talál további információt. 

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>A bróker engedélyezése az MSAL-hoz iOS és macOS rendszeren

A felügyelt hitelesítés alapértelmezés szerint engedélyezve van az Azure AD-forgatókönyvek hez az IOS és a macOS MSAL-ban. 

Az alábbi szakaszok útmutatást nyújtanak az alkalmazás konfigurálásához a XSAL Xamarin.iOS vagy az MSAL iOS és macOS rendszerhez felügyelt hitelesítési támogatásához. A két utasításcsoportban néhány lépés különbözik.

### <a name="enable-brokered-authentication-for-xamarin-ios"></a>Felügyelt hitelesítés engedélyezése a Xamarin iOS rendszerhez

Az ebben a szakaszban leírt lépéseket követve engedélyezheti, hogy a Xamarin.iOS alkalmazás a [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) alkalmazással beszélgéljen.

#### <a name="step-1-enable-broker-support"></a>1. lépés: Broker támogatás engedélyezése

A brókertámogatás alapértelmezés szerint le van tiltva. Engedélyezheti azt egy `PublicClientApplication` adott osztályhoz. A `WithBroker()` paramétert akkor `PublicClientApplication` használja, `PublicClientApplicationBuilder`ha az osztályt a segítségével hozza létre. A `WithBroker()` paraméter alapértelmezés szerint igaz értékre van állítva.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>2. lépés: Az AppDelegate frissítése a visszahívás kezeléséhez

Amikor MSAL.NET felhívja a bróker, a bróker, majd felhívja vissza az alkalmazás. A `AppDelegate.OpenUrl` metódus sal visszahív. Mivel az MSAL várja a választ a bróker, az alkalmazás együtt kell működnie, hogy hívja vissza MSAL.NET. Ezt a viselkedést úgy állíthatja be, hogy frissíti a `AppDelegate.cs` fájlt, hogy felülbírálja a metódust, ahogy azt a következő kód mutatja.

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

Ez a metódus minden alkalommal meghívja az alkalmazást. Ez egy lehetőség a bróker válaszának feldolgozására és a hitelesítési folyamat befejezésére, amelyet MSAL.NET elkezdett.

#### <a name="step-3-set-a-uiviewcontroller"></a>3. lépés: UIViewController() beállítása

A Xamarin iOS esetében általában nem kell objektumablakot beállítania. De ebben az esetben meg kell állítania, hogy küldjön és fogadjon válaszokat egy brókertől. Objektumablak beállításához a `AppDelegate.cs`alkalmazásban a `ViewController`.

Az objektumablak beállításához kövesse az alábbi lépéseket:

1. A `AppDelegate.cs`alkalmazásban `App.RootViewController` állítsa be `UIViewController()`az újat. Ez a beállítás biztosítja, hogy `UIViewController`a közvetítőhöz intézett hívás tartalmazza a . Ha nincs megfelelően beállítva, a következő hibaüzenet jelenhet meg:

    `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker."`

1. Híváskor `AcquireTokenInteractive` használja `.WithParentActivityOrWindow(App.RootViewController)`a használatát. Adja meg a használni kívánt objektumablakra mutató hivatkozást. Például:

    Az `App.cs` szkriptben:
    ```csharp
       public static object RootViewController { get; set; }
    ```
    Az `AppDelegate.cs` szkriptben:
    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```
    A `AcquireToken` hívás:
    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```
    
#### <a name="step-4-register-a-url-scheme"></a>4. lépés: URL-séma regisztrálása

MSAL.NET URL-címeket használ a bróker meghívásához, majd a közvetítő válaszának visszaadása az alkalmazásba. Az oda-vissza út befejezéséhez regisztrálja az `Info.plist` alkalmazás URL-sémát a fájlban. 

Az alkalmazás URL-séma regisztrálásához kövesse az alábbi lépéseket:

1. Előtag `CFBundleURLSchemes` `msauth`a segítségével. 
1. Add `CFBundleURLName` hozzá a végére. Kövesse ezt a mintát: 

   `$"msauth.(BundleId)"`

   Itt `BundleId` egyedileg azonosítja az eszközt. Ha például `BundleId` `yourcompany.xforms`a program , `msauth.com.yourcompany.xforms`az URL-séma a .
    
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
    
#### <a name="step-5-add-to-the-lsapplicationqueriesschemes-section"></a>5. lépés: Hozzáadás az LSApplicationQueriesSchemes szakaszhoz

MSAL `–canOpenURL:` használ, hogy ellenőrizze, ha a bróker telepítve van a készüléken. Az iOS 9 rendszerben az Apple zárolta azalkalmazások által lekérdezhető sémákat.

Adja `msauthv2` hozzá `LSApplicationQueriesSchemes` a `Info.plist` fájl szakaszához, ahogy az a következő példa kódban:

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>Felügyelt hitelesítés az MSAL-hoz iOS és macOS rendszeren

A felügyelt hitelesítés alapértelmezés szerint engedélyezve van az Azure AD-forgatókönyvekben.

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>1. lépés: Az AppDelegate frissítése a visszahívás kezeléséhez

Amikor az MSAL iOS és macOS kéri a bróker, a `openURL` bróker felhívja vissza az alkalmazás a módszer használatával. Mivel az MSAL várja a választ a bróker, a kérelmet kell együttműködni, hogy hívja vissza MSAL. Ezt a lehetőséget úgy `AppDelegate.m` állítsa be, hogy frissíti a fájlt a metódus felülbírálásához, ahogy azt az alábbi kódpéldák mutatják.

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
> Ha az `UISceneDelegate` iOS 13-as vagy újabb rendszerén fogadta `scene:openURLContexts:` `UISceneDelegate` el a rendszert, akkor helyezze az MSAL-visszahívást a helyére. Az `handleMSALResponse:sourceApplication:` MSAL-t minden URL-címhez csak egyszer szabad meghívni.
>
> További információt az [Apple dokumentációjában](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)talál.

#### <a name="step-2-register-a-url-scheme"></a>2. lépés: URL-séma regisztrálása

Az iOS és a macOS MSAL URL-címeket használ a bróker meghívásához, majd a közvetítő válaszának visszaadása az alkalmazásra. Az oda-vissza út befejezéséhez regisztráljon egy `Info.plist` URL-sémát az alkalmazáshoz a fájlban.

Séma regisztrálása az alkalmazáshoz: 

1. Az egyéni URL-séma előtaggal. `msauth` 

1. Adja hozzá a csomagazonosítót a séma végéhez. Kövesse ezt a mintát: 

   `$"msauth.(BundleId)"`

   Itt `BundleId` egyedileg azonosítja az eszközt. Ha például `BundleId` `yourcompany.xforms`a program , `msauth.com.yourcompany.xforms`az URL-séma a .
  
   > [!NOTE]
   > Ez az URL-séma az átirányítási URI részévé válik, amely egyedileg azonosítja az alkalmazást, amikor megkapja a közvetítő válaszát. Győződjön meg arról, hogy `msauth.(BundleId)://auth` az átirányítási URI formátumban regisztrálva van az alkalmazás hoz az [Azure Portalon.](https://portal.azure.com)
  
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

#### <a name="step-3-add-lsapplicationqueriesschemes"></a>3. lépés: LSApplicationQueriesSchemes hozzáadása

A `LSApplicationQueriesSchemes` Hozzáadáshoz engedélyezze a Microsoft Authenticator alkalmazás hívásainak engedélyezését, ha telepítve van.

> [!NOTE]
> A `msauthv3` séma akkor szükséges, ha az alkalmazást Xcode 11-es és újabb kóddal állítják össze. 

Íme egy példa a hozzáadásra: `LSApplicationQueriesSchemes`

```XML 
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Közvetített hitelesítés a Xamarin.Android számára

MSAL.NET nem támogatja az Android brókereket.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Token megszerzése](scenario-mobile-acquire-token.md)
