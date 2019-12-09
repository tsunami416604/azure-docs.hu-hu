---
title: Felügyelt hitelesítés a Xamarin, iOS & Androidon | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan telepíthet át olyan Xamarin iOS-alkalmazásokat, amelyek a .NET-hez készült Azure AD-hitelesítési kódtár (ADAL.NET) használatával a .net-hez készült Microsoft Authentication Library (MSAL.NET) szolgáltatáshoz is használhatják a Microsoft Authenticator.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: a26f73354b99160275649855f7a2a616249ce05c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921846"
---
# <a name="use-microsoft-authenticator-or-microsoft-intune-company-portal-on-xamarin-applications"></a>Microsoft Authenticator-vagy Microsoft Intune-Céges portál használata Xamarin-alkalmazásokhoz

Android és iOS rendszeren, például Microsoft Authenticator vagy Microsoft Intune Céges portál engedélyezése (csak Android esetén):

- Egyszeri bejelentkezés (SSO). A felhasználóknak nem kell bejelentkezniük minden alkalmazásba.
- Eszköz azonosítása. A közvetítő hozzáfér az eszközhöz, amelyet a munkahelyhez való csatlakozáskor hozott létre az eszközön.
- Alkalmazás-azonosító ellenőrzése. Amikor egy alkalmazás meghívja a közvetítőt, átadja az átirányítási URL-címet, és ellenőrzi a közvetítőt.

Ezen funkciók egyikének engedélyezéséhez az alkalmazás fejlesztőinek a `WithBroker()` paramétert kell használniuk a `PublicClientApplicationBuilder.CreateApplication` metódus meghívásakor. az `.WithBroker()` alapértelmezés szerint True (igaz) értékre van állítva. A fejlesztőknek az [iOS](#brokered-authentication-for-ios) -vagy [Android](#brokered-authentication-for-android) -alkalmazásokkal kapcsolatos lépéseket is követniük kell.

## <a name="brokered-authentication-for-ios"></a>Felügyelt hitelesítés iOS rendszerhez

A következő lépésekkel engedélyezheti a Xamarin. iOS-alkalmazásnak, hogy beszéljen a [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) alkalmazással.

### <a name="step-1-enable-broker-support"></a>1\. lépés: a közvetítő támogatásának engedélyezése
A közvetítői támogatás PublicClientApplication alapon engedélyezett. Alapértelmezés szerint le van tiltva. Ha a PublicClientApplication a PublicClientApplicationBuilder keresztül hozza létre, használja a `WithBroker()` paramétert (alapértelmezés szerint igaz értékre állítva).

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>2\. lépés: a visszahívást kezelő AppDelegate frissítése
Ha a .NET-hez készült Microsoft Authentication Library (MSAL.NET) meghívja a közvetítőt, a közvetítő a `AppDelegate` osztály `OpenUrl` metódusával hívja vissza az alkalmazást. Mivel a MSAL megvárja a közvetítőtől érkező választ, az alkalmazásnak együtt kell működnie a MSAL.NET visszahívásához. Az együttműködés engedélyezéséhez frissítse a `AppDelegate.cs` fájlt a következő módszer felülbírálásához.

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

Ezt a metódust az alkalmazás minden indításakor meg kell hívni. Lehetőség van arra, hogy feldolgozza a közvetítő válaszát, és elvégezze a MSAL.NET által kezdeményezett hitelesítési folyamatot.

### <a name="step-3-set-a-uiviewcontroller"></a>3\. lépés: UIViewController () beállítása
Továbbra is `AppDelegate.cs`, be kell állítania egy objektum ablakát. A Xamarin iOS esetében általában nincs szükség az objektum ablakának beállítására. A közvetítőtől érkező válaszok küldéséhez és fogadásához egy objektumra van szükség. 

Ehhez két dolgot kell tennie. 
1. A `AppDelegate.cs`ban állítsa be a `App.RootViewController` egy új `UIViewController()`. Ez a hozzárendelés ellenőrzi, hogy van-e UIViewController a közvetítő meghívásával. Ha nincs megfelelően beállítva, a következő hibaüzenet jelenhet meg: `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. A AcquireTokenInteractive-hívásban használja a `.WithParentActivityOrWindow(App.RootViewController)` és adja át a hivatkozást a használni kívánt objektum-ablakra.

**Például:**

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

### <a name="step-4-register-a-url-scheme"></a>4\. lépés: URL-séma regisztrálása
A MSAL.NET URL-címeket használ a közvetítő meghívásához, majd visszaküldi a közvetítői választ az alkalmazásnak. A kör alakú útvonal befejezéséhez regisztráljon egy URL-sémát az alkalmazáshoz a `Info.plist` fájlban.

A `CFBundleURLSchemes` névnek `msauth.` előtagként kell szerepelnie, amelyet a `CFBundleURLName`követ.

`$"msauth.(BundleId)"`

**Például:**

`msauth.com.yourcompany.xforms`

> [!NOTE]
> Ez az URL-séma az átirányítási URI részévé válik, amely az alkalmazás egyedi azonosítására szolgál, amikor a közvetítőtől kapott választ.

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

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>5\. lépés: az ügynök azonosítójának hozzáadása a összes szakaszhoz
A MSAL `–canOpenURL:` használatával ellenőrizze, hogy a közvetítő telepítve van-e az eszközön. Az iOS 9-es verziójában az Apple zárolta, hogy az alkalmazás milyen sémákat tud lekérdezni. 

`msauthv2` hozzáadása az `Info.plist` fájl `LSApplicationQueriesSchemes` szakaszához.

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="step-6-register-your-redirect-uri-in-the-application-portal"></a>6\. lépés: az átirányítási URI regisztrálása az alkalmazás-portálon
A közvetítő használatával további követelményt adhat az átirányítási URI-hoz. Az átirányítási URI formátumának a következő formátumúnak kell _lennie_ :
```CSharp
$"msauth.{BundleId}://auth"
```
**Például:**
```CSharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
Figyelje meg, hogy az átirányítási URI megegyezik a `Info.plist` fájlban foglalt `CFBundleURLSchemes` névvel.

### <a name="step-7-make-sure-the-redirect-uri-is-registered-with-your-app"></a>7\. lépés: Győződjön meg arról, hogy az átirányítási URI regisztrálva van az alkalmazásban

Ezt az átirányítási URI-t regisztrálni kell az alkalmazás regisztrációs portálján (https://portal.azure.com) érvényes átirányítási URI-ként az alkalmazáshoz. 

A portálon új felhasználói élményre vonatkozó regisztrációs portál található, amely segítséget nyújt a felügyelt válasz URI-AZONOSÍTÓjának kiszámításához.

1. Az alkalmazás regisztrálása lapon válassza a **hitelesítés** lehetőséget, és válassza **az új felület kipróbálása**lehetőséget.

   ![Próbálja ki az új alkalmazás-regisztrációs élményt](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Válassza **a platform hozzáadása**lehetőséget.

   ![Platform hozzáadása](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. Ha a platformok listája támogatott, válassza az **iOS**lehetőséget.

   ![Az iOS konfigurálása](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Adja meg a köteg AZONOSÍTÓját a kérésnek megfelelően, majd válassza a **Konfigurálás**lehetőséget.

   ![Adja meg a köteg AZONOSÍTÓját](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

1. Az átirányítási URI-t az Ön számára számítjuk ki.

   ![Átirányítási URI másolása](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Felügyelt hitelesítés Android rendszerhez

A MSAL.NET jelenleg csak a Xamarin. iOS platformot támogatja. Még nem támogatja a Xamarin. Android platformhoz tartozó közvetítőket.

A MSAL Android natív könyvtára már támogatja azt. Részletekért lásd: [a közvetítő hitelesítés az Androidban](brokered-auth.md)

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg [univerzális Windows-platform-specifikus megfontolásokkal a MSAL.net](msal-net-uwp-considerations.md).