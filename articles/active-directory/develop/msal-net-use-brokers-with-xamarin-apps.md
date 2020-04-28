---
title: Brókerek használata a Xamarin iOS & Android rendszerhez | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan állíthatja be a Xamarin iOS-alkalmazásokat, amelyek a .NET-hez készült Microsoft Authenticator és a Microsoft Authentication Library (MSAL.NET) használatát is használhatják. Azt is megtudhatja, hogyan telepítheti át a .NET-hez készült Azure AD Authentication Library (ADAL.NET) rendszerről a Microsoft Authentication Library for .NET (MSAL.NET) szolgáltatásra.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79262788"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Microsoft Authenticator vagy Intune Céges portál használata Xamarin-alkalmazásokban

Android és iOS rendszeren a közvetítők, például a Microsoft Authenticator és az Android-specifikus Microsoft Intune Céges portál lehetővé teszik a következőket:

- **Egyszeri bejelentkezés (SSO)**: a felhasználóknak nem kell bejelentkezniük minden alkalmazásba.
- **Eszköz azonosítása**: a közvetítő hozzáfér az eszköz tanúsítványához. Ez a tanúsítvány a munkahelyhez való csatlakozáskor jön létre az eszközön.
- **Alkalmazás-azonosítás ellenőrzése**: amikor egy alkalmazás meghívja a közvetítőt, átadja az átirányítási URL-címét. A közvetítő ellenőrzi az URL-címet.

Ezen funkciók egyikének engedélyezéséhez használja a `WithBroker()` paramétert a `PublicClientApplicationBuilder.CreateApplication` metódus meghívásakor. Alapértelmezés `.WithBroker()` szerint a paraméter értéke TRUE (igaz). 

Az alábbi részekben ismertetett útmutatást követve az [iOS](#brokered-authentication-for-ios) -alkalmazások és az [Android](#brokered-authentication-for-android) -alkalmazások felügyelt hitelesítésének beállítására is használható.

## <a name="brokered-authentication-for-ios"></a>Felügyelt hitelesítés iOS rendszerhez

A következő lépésekkel engedélyezheti a Xamarin. iOS-alkalmazásnak, hogy beszéljen a [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) alkalmazással.

### <a name="step-1-enable-broker-support"></a>1. lépés: a közvetítő támogatásának engedélyezése
Az egyes példányai esetében engedélyeznie kell a közvetítő támogatását `PublicClientApplication`. A támogatás alapértelmezés szerint le van tiltva. Amikor létrehozza `PublicClientApplication` a `PublicClientApplicationBuilder`-t, a `WithBroker()` következő példában látható módon használja a paramétert. Alapértelmezés `WithBroker()` szerint a paraméter értéke TRUE (igaz).

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>2. lépés: kulcstartó-hozzáférés engedélyezése

A kulcstartó-hozzáférés engedélyezéséhez rendelkeznie kell egy kulcstartó-hozzáférési csoporttal az alkalmazáshoz. Az API-t `WithIosKeychainSecurityGroup()` az alkalmazás létrehozásakor a kulcstartó-hozzáférési csoport beállításához használhatja:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

További információ: a [kulcstartók hozzáférésének engedélyezése](msal-net-xamarin-ios-considerations.md#enable-keychain-access).

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>3. lépés: a visszahívás kezeléséhez a AppDelegate frissítése
Ha a .NET-hez készült Microsoft Authentication Library (MSAL.NET) meghívja a közvetítőt, a közvetítő az `OpenUrl` `AppDelegate` osztály metódusával hívja vissza az alkalmazást. Mivel a MSAL megvárja a közvetítő válaszát, az alkalmazásnak együtt kell működnie a MSAL.NET visszahívásához. Az együttműködés engedélyezéséhez frissítse a `AppDelegate.cs` fájlt a következő módszer felülbírálásához.

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

Ezt a metódust az alkalmazás minden indításakor meghívja a rendszer. Lehetőség van arra, hogy feldolgozza a közvetítő válaszát, és elvégezze a MSAL.NET által elindított hitelesítési folyamatot.

### <a name="step-4-set-uiviewcontroller"></a>4. lépés: a UIViewController beállítása ()
Továbbra is `AppDelegate.cs` a fájlban kell beállítania egy objektum ablakát. A Xamarin iOS esetében általában nincs szükség az objektum ablakának beállítására. Azonban szükség van egy objektum-ablakra, amely a közvetítőtől érkező válaszokat küld és fogad. 

Az objektum ablakának beállítása: 
1. A `AppDelegate.cs` fájlban állítsa egy új `App.RootViewController` `UIViewController()`értékre. Ez a hozzárendelés biztosítja, hogy a közvetítő hívása `UIViewController`tartalmazza a-t. Ha a beállítás helytelenül van hozzárendelve, a következő hibaüzenet jelenhet meg:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. A `AcquireTokenInteractive` hívásnál használja `.WithParentActivityOrWindow(App.RootViewController)` a (z), majd adja át a hivatkozást a használni kívánt objektum-ablakra.

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

### <a name="step-5-register-a-url-scheme"></a>5. lépés: URL-séma regisztrálása
A MSAL.NET URL-címeket használ a közvetítő meghívásához, majd visszaküldi az alkalmazásnak a közvetítő válaszát. A kör alakú útvonal befejezéséhez regisztráljon egy URL-sémát az alkalmazásához `Info.plist` a fájlban.

A `CFBundleURLSchemes` névnek előtagként `msauth.` kell szerepelnie. Kövesse az előtagot `CFBundleURLName`a következővel:. 

Az URL-sémában `BundleId` egyedi módon azonosítja az alkalmazást: `$"msauth.(BundleId)"`. Tehát ha `BundleId` igen `com.yourcompany.xforms`, akkor az URL-séma `msauth.com.yourcompany.xforms`.

> [!NOTE]
> Ez az URL-séma az átirányítási URI részévé válik, amely egyedileg azonosítja az alkalmazást, amikor megkapja a választ a közvetítőtől.

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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>6. lépés: az ügynök azonosítójának hozzáadása a összes szakaszhoz

A MSAL `–canOpenURL:` segítségével ellenőrizhető, hogy a közvetítő telepítve van-e az eszközön. Az iOS 9-es verzióban az Apple zárolta azokat a sémákat, amelyeket az alkalmazás lekérhet. 

Adja `msauthv2` hozzá a `LSApplicationQueriesSchemes` `Info.plist` fájlt a fájl szakaszához, ahogy az alábbi példában is látható:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>7. lépés: az átirányítási URI regisztrálása az alkalmazás-portálon

A közvetítő használatakor az átirányítási URI-nak külön követelménye van. Az átirányítási URI formátumának a következő formátumúnak kell _lennie_ :

```csharp
$"msauth.{BundleId}://auth"
```

Például:

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```

Figyelje meg, hogy az átirányítási `CFBundleURLSchemes` URI megegyezik a `Info.plist` fájlban szereplő névvel.

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>8. lépés: Győződjön meg arról, hogy az átirányítási URI regisztrálva van az alkalmazásban

Az átirányítási URI-t regisztrálni kell az alkalmazás [regisztrációs portálján](https://portal.azure.com) érvényes átirányítási URI-ként. 

Az alkalmazás-regisztrációs portál új felhasználói élményt nyújt a felügyelt válasz URI-AZONOSÍTÓjának kiszámításához. 

Az átirányítási URI kiszámítása:

1. Az alkalmazás-regisztrációs portálon válassza a **hitelesítés** > **kipróbálom az új felhasználói élményt**.

   ![Próbálja ki az új alkalmazás-regisztrációs élményt](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Válassza **a platform hozzáadása**lehetőséget.

   ![Platform hozzáadása](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. Ha a platformok listája támogatott, válassza az **iOS**lehetőséget.

   ![Az iOS konfigurálása](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Adja meg a köteg AZONOSÍTÓját a kérésnek megfelelően, majd válassza a **Konfigurálás**lehetőséget.

   ![Adja meg a köteg AZONOSÍTÓját](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Ha elkészült a lépésekkel, a rendszer az átirányítási URI-t számítja ki.

![Átirányítási URI másolása](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Felügyelt hitelesítés Android rendszerhez

### <a name="step-1-enable-broker-support"></a>1. lépés: a közvetítő támogatásának engedélyezése

A közvetítői támogatás PublicClientApplication alapon engedélyezett. Alapértelmezés szerint le van tiltva. Használja a `WithBroker()` paramétert (alapértelmezés szerint igaz értékre állítva), `IPublicClientApplication` amikor létrehozza `PublicClientApplicationBuilder`a-t.

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) //(see step 4 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>2. lépés: a visszahívást kezelő AppDelegate frissítése

Ha a MSAL.NET meghívja a közvetítőt, akkor a közvetítő a OnActivityResult () metódussal hívja vissza az alkalmazást. Mivel a MSAL megvárja a közvetítő válaszát, az alkalmazásnak az eredményt MSAL.NET kell irányítani.
Ezt úgy érheti el, ha az alábbi módon felülbírálja az eredményt a OnActivityResult () metódus felülbírálásával `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)` :

```CSharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

Ezt a metódust a rendszer minden alkalommal meghívja a közvetítő alkalmazás indításakor, és lehetőséget biztosít a közvetítő válaszának feldolgozására és a MSAL.NET által elindított hitelesítési folyamat befejezésére.

### <a name="step-3-set-an-activity"></a>3. lépés: tevékenység beállítása

A felügyelt hitelesítéshez meg kell adnia egy tevékenységet, hogy a MSAL képes legyen elküldeni és fogadni a brókertől kapott választ.

Ehhez meg kell adnia a tevékenységet (általában a MainActivity) a `WithParentActivityOrWindow(object parent)` szülő objektumként. 

**Például:**

A jogkivonat beszerzése hívásban:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-register-your-redirecturi-in-the-application-portal"></a>4. lépés: a RedirectUri regisztrálása az alkalmazás-portálon

A MSAL URL-címekkel hívja meg a közvetítőt, majd térjen vissza az alkalmazáshoz. Ennek a menetnek a befejezéséhez regisztrálnia kell egy URL-sémát az alkalmazáshoz. Ezt az átirányítási URI-t regisztrálni kell az Azure AD-alkalmazás regisztrációs portálján az alkalmazás érvényes átirányítási URI-JAként.


Az alkalmazáshoz szükséges átirányítási URI az APK aláírásához használt tanúsítványtól függ.

```
Example: msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

Az URI utolsó része, a `hgbUYHVBYUTvuvT&Y6tr554365466=`, az az aláírás, amelyet az apk aláír, Base64 kódolással.
Ha azonban az alkalmazás fejlesztési fázisában a Visual Studióval végzi a kód hibakeresését anélkül, hogy az apk-t egy adott tanúsítvánnyal aláírja, a Visual Studio a hibakeresési célból aláírja az apk-t, így az APK egyedi aláírást ad a számítógépnek, amelyre az épül. Így minden alkalommal, amikor az alkalmazást egy másik gépen hozza létre, frissítenie kell az átirányítási URI-t az alkalmazás kódjában, valamint az alkalmazás regisztrációját a Azure Portal a MSAL való hitelesítéshez. 

A hibakeresés során előfordulhat, hogy a MSAL-kivétel (vagy a naplófájl) helytelenül van megadva az átirányítási URI-nak. **Ez a kivétel azt az átirányítási URI-t is megadja, amelyet** a jelenleg hibakeresés alatt álló géppel kell használnia. Ezzel az átirányítási URI-val továbbra is folytathatja a fejlesztést.

Ha készen áll a kód véglegesítésére, frissítse az átirányítási URI-t a kódban és az alkalmazás regisztrációjában a Azure Portalban, hogy használja a tanúsítvány aláírását, amelyet a (z) APK-val fog aláírni.

A gyakorlatban ez azt jelenti, hogy regisztrálnia kell egy átirányítási URI-t a csoport minden tagjához, valamint egy átirányítási URI-t az APK éles aláírt verziójához.

Saját maga is kiszámíthatja az aláírást, hasonlóan a MSAL: 

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

Azt is megteheti, hogy az alábbi parancsokkal megszerezheti a csomag aláírását:

Windows esetén:`keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android`

Mac rendszer esetén:`keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64`

## <a name="next-steps"></a>További lépések

Tudnivalók a [univerzális Windows-platform és a MSAL.net használatának szempontjairól](msal-net-uwp-considerations.md).
