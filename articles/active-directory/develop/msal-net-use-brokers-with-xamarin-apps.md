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
ms.openlocfilehash: 25b8aa9b5e80720e9543dafce7970404a62b7d1f
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132640"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Microsoft Authenticator vagy Intune Céges portál használata Xamarin-alkalmazásokban

Android és iOS rendszeren a közvetítők, például a Microsoft Authenticator és az Android-specifikus Microsoft Intune Céges portál lehetővé teszik a következőket:

- **Egyszeri bejelentkezés (SSO)** : a felhasználóknak nem kell bejelentkezniük minden alkalmazásba.
- **Eszköz azonosítása**: a közvetítő hozzáfér az eszköz tanúsítványához. Ez a tanúsítvány a munkahelyhez való csatlakozáskor jön létre az eszközön.
- **Alkalmazás-azonosítás ellenőrzése**: amikor egy alkalmazás meghívja a közvetítőt, átadja az átirányítási URL-címét. A közvetítő ellenőrzi az URL-címet.

Ezen funkciók egyikének engedélyezéséhez használja a `WithBroker()` paramétert a `PublicClientApplicationBuilder.CreateApplication` metódus meghívásakor. A `.WithBroker()` paraméter alapértelmezés szerint True (igaz) értékre van állítva. 

Az alábbi részekben ismertetett útmutatást követve az [iOS](#brokered-authentication-for-ios) -alkalmazások és az [Android](#brokered-authentication-for-android) -alkalmazások felügyelt hitelesítésének beállítására is használható.

## <a name="brokered-authentication-for-ios"></a>Felügyelt hitelesítés iOS rendszerhez

A következő lépésekkel engedélyezheti a Xamarin. iOS-alkalmazásnak, hogy beszéljen a [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) alkalmazással.

### <a name="step-1-enable-broker-support"></a>1\. lépés: a közvetítő támogatásának engedélyezése
A `PublicClientApplication`egyes példányai esetében engedélyeznie kell a közvetítő támogatását. A támogatás alapértelmezés szerint le van tiltva. Amikor `PublicClientApplication`t hoz létre a `PublicClientApplicationBuilder`on keresztül, az alábbi példában látható módon használja az `WithBroker()` paramétert. A `WithBroker()` paraméter alapértelmezés szerint True (igaz) értékre van állítva.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>2\. lépés: kulcstartó-hozzáférés engedélyezése

A kulcstartó-hozzáférés engedélyezéséhez rendelkeznie kell egy kulcstartó-hozzáférési csoporttal az alkalmazáshoz. Az alkalmazás létrehozásakor használhatja a `WithIosKeychainSecurityGroup()` API-t a kulcstartó-hozzáférési csoport beállításához:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

További információ: a [kulcstartók hozzáférésének engedélyezése](msal-net-xamarin-ios-considerations.md#enable-keychain-access).

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>3\. lépés: a visszahívás kezeléséhez a AppDelegate frissítése
Ha a .NET-hez készült Microsoft Authentication Library (MSAL.NET) meghívja a közvetítőt, a közvetítő a `AppDelegate` osztály `OpenUrl` metódusával hívja vissza az alkalmazást. Mivel a MSAL megvárja a közvetítő válaszát, az alkalmazásnak együtt kell működnie a MSAL.NET visszahívásához. Az együttműködés engedélyezéséhez frissítse a `AppDelegate.cs` fájlt a következő módszer felülbírálásához.

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

### <a name="step-4-set-uiviewcontroller"></a>4\. lépés: a UIViewController beállítása ()
Továbbra is a `AppDelegate.cs` fájlban kell beállítania egy objektum ablakát. A Xamarin iOS esetében általában nincs szükség az objektum ablakának beállítására. Azonban szükség van egy objektum-ablakra, amely a közvetítőtől érkező válaszokat küld és fogad. 

Az objektum ablakának beállítása: 
1. A `AppDelegate.cs` fájlban állítsa be a `App.RootViewController`t egy új `UIViewController()`re. Ez a hozzárendelés biztosítja, hogy a közvetítőnek meghívása `UIViewController`tartalmaz. Ha a beállítás helytelenül van hozzárendelve, a következő hibaüzenet jelenhet meg:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. A `AcquireTokenInteractive` hívásban használja a `.WithParentActivityOrWindow(App.RootViewController)`, majd adja át a hivatkozást a használni kívánt objektum-ablakra.

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

### <a name="step-5-register-a-url-scheme"></a>5\. lépés: URL-séma regisztrálása
A MSAL.NET URL-címeket használ a közvetítő meghívásához, majd visszaküldi az alkalmazásnak a közvetítő válaszát. A kör alakú útvonal befejezéséhez regisztráljon egy URL-sémát az alkalmazáshoz a `Info.plist` fájlban.

A `CFBundleURLSchemes` névnek `msauth.` előtagként kell szerepelnie. Kövesse az előtagot `CFBundleURLName`. 

Az URL-sémában `BundleId` egyedileg azonosítja az alkalmazást: `$"msauth.(BundleId)"`. Tehát ha a `BundleId` `com.yourcompany.xforms`, az URL-séma `msauth.com.yourcompany.xforms`.

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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>6\. lépés: az ügynök azonosítójának hozzáadása a összes szakaszhoz
A MSAL a `–canOpenURL:` segítségével vizsgálja meg, hogy telepítve van-e a közvetítő az eszközön. Az iOS 9-es verzióban az Apple zárolta azokat a sémákat, amelyeket az alkalmazás lekérhet. 

Adja hozzá `msauthv2` az `Info.plist` fájl `LSApplicationQueriesSchemes` szakaszához, ahogy az alábbi példában is látható:

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>7\. lépés: az átirányítási URI regisztrálása az alkalmazás-portálon
A közvetítő használatakor az átirányítási URI-nak külön követelménye van. Az átirányítási URI formátumának a következő formátumúnak kell _lennie_ :
```csharp
$"msauth.{BundleId}://auth"
```

Például: 

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
Figyelje meg, hogy az átirányítási URI megegyezik a `Info.plist` fájlban szereplő `CFBundleURLSchemes` nevével.

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>8\. lépés: Győződjön meg arról, hogy az átirányítási URI regisztrálva van az alkalmazásban

Az átirányítási URI-t regisztrálni kell az alkalmazás [regisztrációs portálján](https://portal.azure.com) érvényes átirányítási URI-ként. 

Az alkalmazás-regisztrációs portál új felhasználói élményt nyújt a felügyelt válasz URI-AZONOSÍTÓjának kiszámításához. 

Az átirányítási URI kiszámítása:

1. Az alkalmazás-regisztrációs portálon válassza a **hitelesítés** > **próbálja ki az új felhasználói élményt**.

   ![Próbálja ki az új alkalmazás-regisztrációs élményt](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Válassza **a platform hozzáadása**lehetőséget.

   ![Platform hozzáadása](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. Ha a platformok listája támogatott, válassza az **iOS**lehetőséget.

   ![Az iOS konfigurálása](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Adja meg a köteg AZONOSÍTÓját a kérésnek megfelelően, majd válassza a **Konfigurálás**lehetőséget.

   ![Adja meg a köteg AZONOSÍTÓját](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

A lépések elvégzése után a rendszer az átirányítási URI-t számítja ki.

![Átirányítási URI másolása](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Felügyelt hitelesítés Android rendszerhez

A MSAL.NET csak a Xamarin. iOS platformot támogatja. Még nem támogatja a Xamarin. Android platformhoz tartozó közvetítőket.

A MSAL Android natív könyvtára már támogatja a felügyelt hitelesítést. További információ: felügyelt [hitelesítés az Androidban](brokered-auth.md).

## <a name="next-steps"></a>Következő lépések

Tudnivalók a [univerzális Windows-platform és a MSAL.net használatának szempontjairól](msal-net-uwp-considerations.md).
