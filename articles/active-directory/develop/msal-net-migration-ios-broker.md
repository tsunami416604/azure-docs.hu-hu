---
title: Xamarin-alkalmazások migrálása közvetítők használatával MSAL.NET rendszerre
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogy miként telepítheti át a Microsoft Authenticatort használó Xamarin iOS-alkalmazásokat ADAL.NET MSAL.NET.
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
ms.openlocfilehash: de259daa7fd27cc4f138c294a7f347502ca482a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185831"
---
# <a name="migrate-ios-applications-that-use-microsoft-authenticator-from-adalnet-to-msalnet"></a>A Microsoft Authenticator t használó iOS-alkalmazások áttelepítése ADAL.NET MSAL.NET

Az Azure Active Directory hitelesítési könyvtára .NET (ADAL.NET) és az iOS-bróker. Most itt az ideje, hogy áttérjen a [Microsoft Authentication Library](msal-overview.md) for .NET (MSAL.NET), amely támogatja a bróker iOS-től release 4.3-tól. 

Hol kezdjem? Ez a cikk segít a Xamarin iOS-alkalmazás ADAL-ról MSAL-ra történő áttelepítésében.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy már rendelkezik egy Xamarin iOS alkalmazással, amely integrálva van az iOS-brókerrel. Ha nem, közvetlenül MSAL.NET, és kezdődik a bróker végrehajtása van. Az iOS-bróker új alkalmazással történő MSAL.NET való meghívásáról [a dokumentációban](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications)olvashat.

## <a name="background"></a>Háttér

### <a name="what-are-brokers"></a>Mik azok a brókerek?

A brókerek olyan alkalmazások, amelyeket a Microsoft androidos és iOS rendszeren biztosít. (Lásd: a [Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) alkalmazás iOS és Android rendszeren, valamint az Intune Vállalati portál alkalmazás Android on.) 

Ezek lehetővé teszik:

- Egyszeri bejelentkezés.
- Eszközazonosítás, amelyet néhány [feltételes hozzáférési házirend](../conditional-access/overview.md)igényel. További információ: [Device management](../conditional-access/concept-conditional-access-conditions.md#device-platforms).
- Alkalmazás-azonosítás ellenőrzése, amely bizonyos vállalati forgatókönyvekben is szükséges. További információ: [Intune mobile application management (MAM)](https://docs.microsoft.com/intune/mam-faq).

## <a name="migrate-from-adal-to-msal"></a>Áttelepítés ADAL-ról MSAL-ra

### <a name="step-1-enable-the-broker"></a>1. lépés: Engedélyezze a brókert

<table>
<tr><td>Aktuális ADAL-kód:</td><td>MSAL-partner:</td></tr>
<tr><td>
A ADAL.NET a közvetítői támogatás hitelesítésenkénti környezetben engedélyezve volt. Alapértelmezés szerint le van tiltva. Be kellett állítania egy 

`useBroker`zászló igaz a `PlatformParameters` konstruktor, hogy hívja a bróker:

```csharp
public PlatformParameters(
        UIViewController callerViewController, 
        bool useBroker)
```
A platformspecifikus kódban ebben a példában az iOS lapmegjelenítőben állítsa be a`useBroker` 
zászló igaz:
```csharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```

Ezután adja meg a paramétereket a beszerzési tokenhívásban:
```csharp
 AuthenticationResult result =
                    await
                        AuthContext.AcquireTokenAsync(
                              Resource, 
                              ClientId, 
                              new Uri(RedirectURI), 
                              platformParameters)
                              .ConfigureAwait(false);
```

</td><td>
A MSAL.NET a közvetítői támogatás nyilvános ügyfélalkalmazásonként engedélyezve van. Alapértelmezés szerint le van tiltva. Ennek engedélyezéséhez használja a 

`WithBroker()`paraméter (alapértelmezés szerint igaz) annak érdekében, hogy hívja a bróker:

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
A beszerzéstoken-hívásban:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>2. lépés: UIViewController() beállítása
A ADAL.NET a `PlatformParameters`. (Lásd a példát az 1. lépésben.) A MSAL.NET, hogy a fejlesztők nagyobb rugalmasságot, egy objektum ablakot használnak, de ez nem szükséges a rendszeres iOS-használat. Ahhoz, hogy használja a bróker, állítsa be az objektum ablakot annak érdekében, hogy küldjön és fogadjon válaszokat a bróker. 
<table>
<tr><td>Aktuális ADAL-kód:</td><td>MSAL-partner:</td></tr>
<tr><td>
Egy UIViewController adatik át 

`PlatformParameters`az iOS-specifikus platformon.

```csharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
Az MSAL.NET két dolgot tehet az iOS objektumablakának beállításához:

1. A `AppDelegate.cs`alkalmazásban `App.RootViewController` új `UIViewController()`ra van beállítva. Ez a hozzárendelés biztosítja, hogy van egy UIViewController a hívást a bróker. Ha nincs megfelelően beállítva, a következő hibaüzenet jelenhet meg:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. A AcquireTokenInteractive hívás, `.WithParentActivityOrWindow(App.RootViewController)`használja a , és adja át a hivatkozást az objektum ablakban használni fogja.

**Például:**

Az `App.cs` szkriptben:
```csharp
   public static object RootViewController { get; set; }
```
Az `AppDelegate.cs` szkriptben:
```csharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
A beszerzéstoken-hívásban:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>3. lépés: Az AppDelegate frissítése a visszahívás kezeléséhez
Mind ADAL és MSAL hívja a bróker, és a bróker `OpenUrl` viszont `AppDelegate` felhívja vissza az alkalmazás módszerén keresztül az osztály. További információt [ebben a dokumentációban](msal-net-use-brokers-with-xamarin-apps.md#step-3-update-appdelegate-to-handle-the-callback)talál.

Itt nincs változás ADAL.NET és MSAL.NET között.

### <a name="step-4-register-a-url-scheme"></a>4. lépés: URL-séma regisztrálása
ADAL.NET és MSAL.NET URL-címeket használ a közvetítő meghívásához és a közvetítő válaszának visszaadásához az alkalmazásnak. Regisztrálja az URL-sémát az alkalmazás fájljában az `Info.plist` alábbiak szerint:

<table>
<tr><td>Aktuális ADAL-kód:</td><td>MSAL-partner:</td></tr>
<tr><td>
Az URL-séma egyedi az alkalmazás.
</td><td>
A(z) 

`CFBundleURLSchemes`a névnek tartalmaznia kell 

`msauth.`

előtagként, majd a`CFBundleURLName`

Például:`$"msauth.(BundleId")`

```csharp
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

> [!NOTE]
> Ez az URL-séma az átirányítási URI részévé válik, amely az alkalmazás egyedi azonosítására szolgál, amikor megkapja a választ a brókertől.

</table>

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>5. lépés: A közvetítőazonosító hozzáadása az LSApplicationQueriesSchemes szakaszhoz

ADAL.NET és MSAL.NET `-canOpenURL:` egyaránt használja, hogy ellenőrizze, ha a bróker telepítve van a készüléken. Adja hozzá az iOS-bróker megfelelő azonosítóját az info.plist fájl LSApplicationQueriesSchemes szakaszába az alábbiak szerint:

<table>
<tr><td>Aktuális ADAL-kód:</td><td>MSAL-partner:</td></tr>
<tr><td>
Használat 

`msauth`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
Használat 

`msauthv2`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```
</table>

### <a name="step-6-register-your-redirect-uri-in-the-portal"></a>6. lépés: Az átirányítási URI regisztrálása a portálon

ADAL.NET és MSAL.NET egy további követelményt ad hozzá az átirányítási URI-hoz, amikor a közvetítőt célozza meg. Regisztrálja az átirányítási URI-t az alkalmazással a portálon.
<table>
<tr><td>Aktuális ADAL-kód:</td><td>MSAL-partner:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`

Példa: 

`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

Példa:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

Az átirányítási URI portálon történő regisztrálásáról a [Broker kihasználása a Xamarin.iOS-alkalmazásokban](msal-net-use-brokers-with-xamarin-apps.md#step-8-make-sure-the-redirect-uri-is-registered-with-your-app)című témakörben talál további információt.

## <a name="next-steps"></a>További lépések

További információ a [Xamarin iOS-specifikus szempontokról a MSAL.NET .](msal-net-xamarin-ios-considerations.md) 
