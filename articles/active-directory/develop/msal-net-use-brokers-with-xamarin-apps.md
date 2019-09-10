---
title: A Xamarin iOS-és Android-alkalmazások Microsoft Authenticator vagy Microsoft Intune vállalati portál használata | Azure
description: Ismerje meg, hogyan telepítheti át a Xamarin iOS-alkalmazásait a .NET-hez készült Azure AD-hitelesítési kódtár (ADAL.NET) és a .NET-hez készült Microsoft Authentication Library (MSAL.NET) Microsoft Authenticator használatával.
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e16ad801b3b691d942c2ba0dc723ba72e24cf4c0
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70875639"
---
# <a name="use-microsoft-authenticator-or-microsoft-intune-company-portal-on-xamarin-applications"></a>Microsoft Authenticator vagy Microsoft Intune vállalati portál használata a Xamarin-alkalmazásokban

Android és iOS rendszeren a közvetítők, például a Microsoft Authenticator vagy a Microsoft Intune vállalati portál engedélyezése (csak Android esetén):

- Egyszeri bejelentkezés (SSO). A felhasználóknak nem kell bejelentkezniük minden alkalmazásba.
- Eszköz azonosítása. Az eszközhöz való hozzáféréshez, amely az eszközön lett létrehozva munkahelyhez csatlakoztatva.
- Alkalmazás-azonosító ellenőrzése. Amikor egy alkalmazás meghívja a közvetítőt, átadja az átirányítási URL-címet, és ellenőrzi a közvetítőt.

Ezen funkciók egyikének engedélyezéséhez az alkalmazás fejlesztőinek a `WithBroker()` `PublicClientApplicationBuilder.CreateApplication` metódus meghívásakor a paramétert kell használniuk. `.WithBroker()`Alapértelmezés szerint True (igaz) értékre van állítva. A fejlesztőknek az alábbi lépéseket is követniük kell az [iOS](#brokered-authentication-for-ios) -vagy [Android](#brokered-authentication-for-android) -alkalmazásokhoz.

## <a name="brokered-authentication-for-ios"></a>Felügyelt hitelesítés iOS rendszerhez

Az alábbi lépéseket követve engedélyezheti a Xamarin. iOS-alkalmazásnak, hogy beszéljen a [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) alkalmazással.

### <a name="step-1-enable-broker-support"></a>1\. lépés: A közvetítő támogatásának engedélyezése
A közvetítői támogatás PublicClientApplication alapon engedélyezett. Alapértelmezés szerint le van tiltva. Használja a `WithBroker()` paramétert (alapértelmezés szerint igaz értékre állítva), amikor a PublicClientApplication a PublicClientApplicationBuilder keresztül hozza létre.

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>2\. lépés: AppDelegate frissítése a visszahívás kezeléséhez
Ha a MSAL.net meghívja a közvetítőt, akkor a közvetítő az `OpenUrl` `AppDelegate` osztály metódusával hívja vissza az alkalmazást. Mivel a MSAL megvárja a közvetítő válaszát, az alkalmazásnak együtt kell működnie a MSAL.NET visszahívásához. Az együttműködés engedélyezéséhez frissítse a `AppDelegate.cs` fájlt az alábbi metódus felülbírálásához.

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

Ezt a metódust minden alkalommal meghívja a rendszer, amikor elindítja az alkalmazást, és feldolgozza a közvetítő válaszát, és elvégzi a MSAL.NET által kezdeményezett hitelesítési folyamatot.

### <a name="step-3-set-a-uiviewcontroller"></a>3\. lépés: UIViewController () beállítása
`AppDelegate.cs`Továbbra is be kell állítania egy objektum ablakát. A Xamarin iOS esetében általában nincs szükség az objektum ablakának beállítására, de a brókertől érkező válaszok küldéséhez és fogadásához szükség van egy objektumra. 

Ehhez két dolgot kell tennie. 
1) A `AppDelegate.cs`ben állítsa be `App.RootViewController` a-t `UIViewController()`egy új értékre. Ez a hozzárendelés ellenőrzi, hogy van-e UIViewController a közvetítő meghívásával. Ha nincs megfelelően beállítva, a következő hibaüzenet jelenhet meg:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
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

### <a name="step-4-register-a-url-scheme"></a>4\. lépés: URL-séma regisztrálása
A MSAL.NET URL-címeket használ a közvetítő meghívásához, majd visszaküldi a közvetítői választ az alkalmazásnak. A kör alakú utazás befejezéséhez regisztrálnia kell egy URL-sémát az alkalmazásához a `Info.plist` fájlban.

A `CFBundleURLSchemes` `msauth.` névnek`CFBundleURLName`előtagként is szerepelnie kell, amelyet a neve követ.

`$"msauth.(BundleId)"`

**Például:** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> Ez az alkalmazás egyedi azonosításához használt RedirectUri részévé válik a közvetítő válaszának fogadásakor.

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

### <a name="step-5-lsapplicationqueriesschemes"></a>5\. lépés: Összes
A MSAL `–canOpenURL:` használatával ellenőrizhető, hogy a közvetítő telepítve van-e az eszközön. Az iOS 9-es verziójában az Apple zárolta, hogy az alkalmazás milyen sémákat tud lekérdezni. 

**Hozzáadás** **`msauthv2`** a fájl`Info.plist` szakaszához. `LSApplicationQueriesSchemes`

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="step-6-register-your-redirecturi-in-the-application-portal"></a>6\. lépés: A RedirectUri regisztrálása az alkalmazás-portálon
A közvetítő használata további követelményt is felhasznál a redirectUri. A redirectUri a következő formátumúnak _**kell lennie**_ :
```CSharp
$"msauth.{BundleId}://auth"
```
**Példa:**
```CSharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
**Figyelje meg, hogy a RedirectUri megegyezik `CFBundleURLSchemes` a `Info.plist` fájlban szereplő névvel.**

### <a name="step-7-make-sure-the-redirect-uri-is-registered-with-your-app"></a>7\. lépés: Győződjön meg arról, hogy az átirányítási URI regisztrálva van az alkalmazásban

Ezt az átirányítási URI-t regisztrálni kell az alkalmazás regisztrációs portálján (https://portal.azure.com) érvényes átirányítási URI-ként az alkalmazáshoz. 

A portálon egy új alkalmazás-regisztrációs portál található, amely segítséget nyújt a felügyelt válasz URI-azonosítójának kiszámításához a csomag azonosítója alapján:

1. Az alkalmazás regisztrálása lapon válassza a **hitelesítés** lehetőséget, és válassza **az új felület kipróbálása**lehetőséget.
   ![Próbálja ki az új alkalmazás-regisztrációs élményt](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Válassza a **platform hozzáadása**lehetőséget.
   ![Platform hozzáadása](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Ha a platformok listája támogatott, válassza az **iOS**lehetőséget.
   ![Az iOS konfigurálása](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Adja meg a köteg AZONOSÍTÓját a kért módon, majd kattintson a **regisztráció**gombra.
   ![Adja meg a köteg AZONOSÍTÓját](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

5. Az átirányítási URI-t az Ön számára számítjuk ki.
   ![Átirányítási URI másolása](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Felügyelt hitelesítés Android rendszerhez

A közvetítő támogatása nem érhető el az Android rendszerhez

## <a name="next-steps"></a>További lépések

[Univerzális Windows-platform-specifikus megfontolások a MSAL.NET](msal-net-uwp-considerations.md)