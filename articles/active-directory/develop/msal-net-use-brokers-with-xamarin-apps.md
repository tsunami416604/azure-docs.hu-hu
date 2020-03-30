---
title: Használja brókerek Xamarin iOS & Android | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogy miként lehet beállítani a Microsoft Authenticator és a Microsoft Authentication Library for .NET (MSAL.NET) microsofti hitelesítési könyvtárát használó Xamarin iOS-alkalmazásokat. Azt is megtudhatja, hogyan lehet áttelepíteni az Azure AD authentication library for .NET (ADAL.NET) rendszerből a Microsoft Authentication Library for .NET (MSAL.NET) könyvtárába.
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1a57173311278c5e3e0304aeb12d4d6999379eb5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262788"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>A Microsoft Authenticator vagy az Intune vállalati portáljának használata Xamarin-alkalmazásokban

Android és iOS rendszeren az olyan brókerek, mint a Microsoft Authenticator és az Android-specifikus Microsoft Intune vállalati portál:

- **Egyszeri bejelentkezés (SSO)**: A felhasználóknak nem kell minden alkalmazásba bejelentkezniük.
- **Eszközazonosító**: A közvetítő hozzáfér az eszköztanúsítványhoz. Ez a tanúsítvány akkor jön létre az eszközön, amikor csatlakozik a munkahelyhez.
- **Alkalmazás-azonosítás ellenőrzése:** Amikor egy alkalmazás meghívja a brókert, átadja az átirányítási URL-címét. A bróker ellenőrzi az URL-t.

A szolgáltatások egyikének engedélyezéséhez `WithBroker()` használja a `PublicClientApplicationBuilder.CreateApplication` paramétert a metódus meghívásakor. A `.WithBroker()` paraméter alapértelmezés szerint igaz értékre van állítva. 

A következő szakaszokban található utasításokat is használhatja az [iOS-alkalmazások](#brokered-authentication-for-ios) vagy [Android-alkalmazások](#brokered-authentication-for-android) felügyelt hitelesítésének beállításához.

## <a name="brokered-authentication-for-ios"></a>Felügyelt hitelesítés iOS-hez

Az alábbi lépésekkel engedélyezheti, hogy a Xamarin.iOS alkalmazás a [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) alkalmazással beszélgéljen.

### <a name="step-1-enable-broker-support"></a>1. lépés: Broker támogatás engedélyezése
Engedélyeznie kell a közvetítői `PublicClientApplication`támogatást az egyes példányaihoz. A támogatás alapértelmezés szerint le van tiltva. A rendszeren `PublicClientApplicationBuilder`keresztül `WithBroker()` történő létrehozáskor `PublicClientApplication` a következő példa szerint használja a paramétert. A `WithBroker()` paraméter alapértelmezés szerint igaz értékre van állítva.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>2. lépés: Kulcskarika-hozzáférés engedélyezése

A kulcskarika-hozzáférés engedélyezéséhez rendelkeznie kell egy kulcskarika-hozzáférési csoporttal az alkalmazáshoz. Az `WithIosKeychainSecurityGroup()` API segítségével beállíthatja a kulcskarika-hozzáférési csoportot az alkalmazás létrehozásakor:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

További információt a [Kulcskarika-hozzáférés engedélyezése](msal-net-xamarin-ios-considerations.md#enable-keychain-access)című témakörben talál.

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>3. lépés: Az AppDelegate frissítése a visszahívás kezeléséhez
Amikor a Microsoft Authentication Library for .NET (MSAL.NET) felhívja a `OpenUrl` közvetítő, `AppDelegate` a közvetítő felhívja vissza az alkalmazás az osztály módszerén keresztül. Mivel az MSAL várja a bróker válaszát, az alkalmazásnak együtt kell működnie, hogy visszahívja MSAL.NET. Az együttműködés engedélyezéséhez `AppDelegate.cs` frissítse a fájlt úgy, hogy felülírja a következő módszert.

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

Ez a metódus minden alkalommal meghívja az alkalmazást. Ez arra használható, mint egy lehetőséget, hogy feldolgozza a választ a bróker, és töltse ki a hitelesítési folyamatot, amely MSAL.NET kezdődött.

### <a name="step-4-set-uiviewcontroller"></a>4. lépés: UIViewController() beállítása
Még mindig `AppDelegate.cs` a fájlban van, be kell állítania egy objektumablakot. Általában a Xamarin iOS-hez nem kell beállítania az objektumablakot. De szüksége van egy objektum ablak küldeni és fogadni a válaszokat a bróker. 

Az objektumablak beállítása: 
1. A `AppDelegate.cs` fájlban `App.RootViewController` állítsa be `UIViewController()`az új . Ez a feladat biztosítja, hogy `UIViewController`a hívás a bróker tartalmazza . Ha ez a beállítás helytelenül van hozzárendelve, a következő hibaüzenet jelenhet meg:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. A `AcquireTokenInteractive` hívás, `.WithParentActivityOrWindow(App.RootViewController)` használja, majd adja át a hivatkozást, hogy az objektum ablakban fogja használni.

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

### <a name="step-5-register-a-url-scheme"></a>5. lépés: URL-séma regisztrálása
MSAL.NET URL-eket használ a bróker meghívásához, majd a közvetítő válaszának visszaadása az alkalmazásra. Az oda-vissza út befejezéséhez regisztráljon egy `Info.plist` URL-sémát az alkalmazáshoz a fájlban.

A `CFBundleURLSchemes` névnek `msauth.` előtagként kell szerepelnie. Kövesse az előtagot a segítségével. `CFBundleURLName` 

Az URL-sémában `BundleId` egyedileg azonosítja az alkalmazást: `$"msauth.(BundleId)"`. Tehát, `BundleId` `com.yourcompany.xforms`ha van , `msauth.com.yourcompany.xforms`akkor az URL-rendszer .

> [!NOTE]
> Ez az URL-séma az átirányítási URI részévé válik, amely egyedileg azonosítja az alkalmazást, amikor megkapja a választ a brókertől.

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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>6. lépés: A közvetítőazonosító hozzáadása az LSApplicationQueriesSchemes szakaszhoz

Az MSAL annak ellenőrzésére használja, `–canOpenURL:` hogy a bróker telepítve van-e a készüléken. Az iOS 9 rendszerben az Apple zárolta azalkalmazások által lekérdezhető sémákat. 

Adja `msauthv2` hozzá `LSApplicationQueriesSchemes` a `Info.plist` fájl szakaszához, ahogy az a következő példában is:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>7. lépés: Regisztrálja az átirányítási URI-t az alkalmazásportálon

A közvetítő használatakor az átirányítási URI-nak van egy extra követelménye. Az átirányítási _URI-nak_ a következő formátummal kell rendelkeznie:

```csharp
$"msauth.{BundleId}://auth"
```

Például:

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```

Figyelje meg, hogy `CFBundleURLSchemes` az átirányítási URI `Info.plist` megegyezik a fájlban szereplő névvel.

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>8. lépés: Győződjön meg arról, hogy az átirányítási URI regisztrálva van az alkalmazásban

Az átirányítási URI-t regisztrálni kell az [alkalmazás regisztrációs portálján](https://portal.azure.com) érvényes átirányítási URI-ként az alkalmazáshoz. 

Az alkalmazásregisztrációs portál új felületet biztosít a közvetített válasz URI-nak a csomagazonosítóból történő kiszámításához. 

Az átirányítási URI kiszámításához:

1. Az alkalmazásregisztrációs portálon válassza a **Hitelesítés** > **kipróbálása az új felületet**lehetőséget.

   ![Próbálja ki az új alkalmazásregisztrációs élményt](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Válassza **a Platform hozzáadása**lehetőséget.

   ![Platform hozzáadása](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. Ha a platformok listája támogatott, válassza az **iOS**lehetőséget.

   ![IOS konfigurálása](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Írja be a csomagazonosítóját a kérésnek megfelelően, majd válassza a **Konfigurálás**lehetőséget.

   ![Adja meg a csomag azonosító](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Ha végzett a lépésekkel, az átirányítási URI kiszámításra kerül.

![Átirányítás URI másolása](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Közvetített hitelesítés Androidra

### <a name="step-1-enable-broker-support"></a>1. lépés: Broker támogatás engedélyezése

A brókertámogatás nyilvános ügyfélalkalmazásonként engedélyezve van. Alapértelmezés szerint le van tiltva. A `WithBroker()` paraméter (alapértelmezés szerint igaz értékre `IPublicClientApplication` állítva) a létrehozásakor a `PublicClientApplicationBuilder`.

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) //(see step 4 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>2. lépés: Az AppDelegate frissítése a visszahívás kezeléséhez

Amikor MSAL.NET meghívja a brókert, a bróker viszont visszahívja az alkalmazást az OnActivityResult() metódussal. Mivel az MSAL megvárja a bróker válaszát, az alkalmazásnak az eredményt MSAL.NET kell irányítania.
Ez úgy érhető el, ha `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)` az eredményt az OnActivityResult() metódus felülbírálásával irányítja az alábbi módon.

```CSharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

Ez a módszer minden alkalommal meghívja a bróker alkalmazást, és lehetőséget ad a bróker válaszának feldolgozására és a MSAL.NET által elindított hitelesítési folyamat befejezésére.

### <a name="step-3-set-an-activity"></a>3. lépés: Tevékenység beállítása

A felügyelt hitelesítés működéséhez be kell állítania egy tevékenységet, hogy az MSAL elküldhesse és megkaphassa a választ a brókertől.

Ehhez meg kell adnia a tevékenységet (általában a MainActivity- t) a `WithParentActivityOrWindow(object parent)` szülőobjektumként. 

**Például:**

A Token beszerzése hívás:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-register-your-redirecturi-in-the-application-portal"></a>4. lépés: Regisztrálja redirectUri-ját az alkalmazásportálon

Az MSAL URL-címeket használ a bróker meghívásához, majd visszatér az alkalmazáshoz. Az oda-vissza út befejezéséhez regisztrálnia kell egy URL-sémát az alkalmazáshoz. Ezt az átirányítási URI-t regisztrálni kell az Azure AD alkalmazás regisztrációs portálján, az alkalmazás érvényes átirányítási URI-jaként.


Az alkalmazáshoz szükséges átirányítási URI az APK aláírásához használt tanúsítványtól függ.

```
Example: msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

Az URI utolsó része `hgbUYHVBYUTvuvT&Y6tr554365466=`az az aláírás, amelyhez az APK alá van írva, base64 kódolva.
Ha azonban a Visual Studio használatával az alkalmazás fejlesztési fázisában a kódot egy adott tanúsítvánnyal való aláírása nélkül használja, a Visual Studio hibakeresési célokra aláírja az apk-ot, így az APK egyedi aláírást kap a gép, amelyre épült. Így minden alkalommal, amikor egy másik gépen építi fel az alkalmazást, frissítenie kell az átirányítási URI-t az alkalmazás kódjában és az alkalmazás regisztrációját az Azure Portalon az MSAL-lal való hitelesítéshez. 

Hibakeresés közben előfordulhat, hogy egy MSAL kivétel (vagy naplóüzenet) jelzi, hogy a megadott átirányítási URI helytelen. **Ez a kivétel azt is biztosítja, hogy az átirányítási URI-t, amelyet** az aktuális gép hibakereséshez kell használnia. Ezzel az átirányítási URI-val egyelőre folytathatja a fejlesztést.

Miután készen áll a kód véglegesítésére, győződjön meg róla, hogy frissítse az átirányítási URI-t a kódban és az alkalmazás regisztrációján az Azure Portalon, hogy az APK-t aláíró tanúsítvány aláírását használja.

A gyakorlatban ez azt jelenti, hogy a csapat minden egyes tagjához be kell regisztrálnia egy átirányítási URI-t, valamint egy átirányítási URI-t az APK éles enaláírt verziójához.

Ezt az aláírást saját maga is kiszámíthatja, hasonlóan ahhoz, ahogyan az MSAL teszi: 

```CSharp
   private string GetRedirectUriForBroker()
   {
      string packageName = Application.Context.PackageName;
      string signatureDigest = this.GetCurrentSignatureForPackage(packageName);
      if (!string.IsNullOrEmpty(signatureDigest))
      {
            return string.Format(CultureInfo.InvariantCulture, "{0}://{1}/{2}", RedirectUriScheme,
               packageName.ToLowerInvariant(), signatureDigest);
      }

      return string.Empty;
   }

   private string GetCurrentSignatureForPackage(string packageName)
   {
            PackageInfo info = Application.Context.PackageManager.GetPackageInfo(packageName,
               PackageInfoFlags.Signatures);
            if (info != null && info.Signatures != null && info.Signatures.Count > 0)
            {
               // First available signature. Applications can be signed with multiple signatures.
               // The order of Signatures is not guaranteed.
               Signature signature = info.Signatures[0];
               MessageDigest md = MessageDigest.GetInstance("SHA");
               md.Update(signature.ToByteArray());
               return Convert.ToBase64String(md.Digest(), Base64FormattingOptions.None);
               // Server side needs to register all other tags. ADAL will
               // send one of them.
            }
   }
```

Lehetősége van arra is, hogy megszerezze a csomag aláírását a kulcseszköz használatával a következő parancsokkal:

Windows esetén:`keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android`

Mac esetén:`keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64`

## <a name="next-steps"></a>További lépések

Az [univerzális Windows-platform MSAL.NET használatával kapcsolatos szempontok](msal-net-uwp-considerations.md)ról.
