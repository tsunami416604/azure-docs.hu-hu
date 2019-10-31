---
title: Microsoft Authenticatort használó Xamarin iOS-alkalmazások migrálása a ADAL.NET-ből a MSAL.NET-be
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan telepíthet át Microsoft Authenticatort használó Xamarin iOS-alkalmazásokat a .NET-hez készült Azure AD Authentication Library (ADAL.NET) rendszerből a Microsoft Authentication Library for .NET (MSAL.NET) szolgáltatásba.
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
ms.openlocfilehash: 3c64f9f371424eddc7295b6ec40bda1ebdaaafd5
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175594"
---
# <a name="migrate-ios-applications-that-use-microsoft-authenticator-from-adalnet-to-msalnet"></a>Microsoft Authenticatort használó iOS-alkalmazások migrálása a ADAL.NET-ből a MSAL.NET-be

A .NET-hez készült Azure Active Directory Authentication Library (ADAL.NET) és az iOS-közvetítő használatát használta. Most itt az ideje, hogy migrálni lehessen a .NET-hez készült [Microsoft Authentication Library](msal-overview.md) (MSAL.net) szolgáltatásra, amely támogatja az iOS-es verzióról a 4,3-től kezdődően a közvetítőt. 

Hol érdemes elindítani? Ez a cikk segítséget nyújt a Xamarin iOS-alkalmazásnak a ADAL-ről a MSAL-re való áttelepíteni.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk azt feltételezi, hogy már rendelkezik egy IOS-közvetítővel integrált Xamarin iOS-alkalmazással. Ha nem, lépjen közvetlenül a MSAL.NET, és indítsa el a közvetítő megvalósítását. További információ a MSAL.NET iOS-közvetítő új alkalmazással való meghívásáról: Ez a [dokumentáció](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications).

## <a name="background"></a>Háttér

### <a name="what-are-brokers"></a>Mik azok a brókerek?

A brókerek a Microsoft által az Android és az iOS rendszerhez biztosított alkalmazások. (Lásd a [Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) alkalmazást iOS és Android rendszeren, valamint a Intune céges portál alkalmazást Androidon.) 

Ezek a következőket teszik lehetővé:

- Egyszeri bejelentkezés.
- A [feltételes hozzáférési szabályzatok](../conditional-access/overview.md)által igényelt eszköz azonosítása. További információ: [eszközkezelés](../conditional-access/conditions.md#device-platforms).
- Az alkalmazás azonosításának ellenőrzése, amely bizonyos vállalati helyzetekben is szükséges. További információ: az [Intune Mobile Application Management (MAM)](https://docs.microsoft.com/intune/mam-faq).

## <a name="migrate-from-adal-to-msal"></a>Migrálás a ADAL-ből a MSAL-be

### <a name="step-1-enable-the-broker"></a>1\. lépés: a közvetítő engedélyezése

<table>
<tr><td>Aktuális ADAL-kód:</td><td>MSAL-ügyfél:</td></tr>
<tr><td>
A ADAL.NET-ben a Broker-támogatás hitelesítésen alapuló kontextusban volt engedélyezve. Alapértelmezés szerint le van tiltva. Be kellett állítania a 

`useBroker` jelzőt True értékre a `PlatformParameters` konstruktorban a közvetítő meghívásához:

```CSharp
public PlatformParameters(
        UIViewController callerViewController, 
        bool useBroker)
```
Emellett a platform-specifikus kódban, ebben a példában az iOS-hez készült Page renderelő esetében állítsa be a `useBroker` 
igaz jelző:
```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```

Ezután adja meg a paramétereket a beszerzési jogkivonat hívásában:
```CSharp
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
A MSAL.NET-ben a közvetítői támogatás PublicClientApplication alapon engedélyezett. Alapértelmezés szerint le van tiltva. Az engedélyezéséhez használja a 

`WithBroker()` paraméter (alapértelmezés szerint igaz értékre állítva) a közvetítő meghívásához:

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
A jogkivonat beszerzése hívásban:
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>2\. lépés: UIViewController () beállítása
A ADAL.NET-ben a `PlatformParameters`részeként átadott egy UIViewController. (Lásd az 1. lépésben szereplő példát.) A MSAL.NET-ben a fejlesztők számára nagyobb rugalmasságot biztosítanak, de a normál iOS-használat esetében nem szükséges. Ha a közvetítőt szeretné használni, állítsa be az objektum ablakát a brókertől érkező válaszok küldéséhez és fogadásához. 
<table>
<tr><td>Aktuális ADAL-kód:</td><td>MSAL-ügyfél:</td></tr>
<tr><td>
A rendszer átadja a UIViewController 

`PlatformParameters` az iOS-specifikus platformon.

```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
A MSAL.NET-ben két dolgot kell beállítania az iOS-hez készült objektum ablakának beállításához:

1. A `AppDelegate.cs`ban állítsa be a `App.RootViewController`t egy új `UIViewController()`re. Ez a hozzárendelés biztosítja, hogy van egy UIViewController a közvetítő hívásával. Ha nincs megfelelően beállítva, a következő hibaüzenet jelenhet meg: `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. A AcquireTokenInteractive-hívásban használja a `.WithParentActivityOrWindow(App.RootViewController)`, és adja át a hivatkozást a használni kívánt objektum-ablakra.

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

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>3\. lépés: a visszahívás kezeléséhez a AppDelegate frissítése
Mind a ADAL, mind a MSAL meghívja a közvetítőt, a közvetítő pedig visszahívja az alkalmazást az `AppDelegate` osztály `OpenUrl` metódusával. További információkért tekintse meg [ezt a dokumentációt](msal-net-use-brokers-with-xamarin-apps.md#step-2-update-appdelegate-to-handle-the-callback).

A ADAL.NET és a MSAL.NET között nincs változás.

### <a name="step-4-register-a-url-scheme"></a>4\. lépés: URL-séma regisztrálása
A ADAL.NET és a MSAL.NET URL-címek használatával meghívja a közvetítőt, és visszaküldi a közvetítői választ az alkalmazásnak. Regisztrálja az URL-sémát az alkalmazás `Info.plist` fájljában a következőképpen:

<table>
<tr><td>Aktuális ADAL-kód:</td><td>MSAL-ügyfél:</td></tr>
<tr><td>
Az URL-séma egyedi az alkalmazás számára.
</td><td>
A(z) 

`CFBundleURLSchemes` névnek tartalmaznia kell 

`msauth.`

előtagként, majd a `CFBundleURLName`

Például:`$"msauth.(BundleId")`

```CSharp
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
> Ez az URL-séma az átirányítási URI részévé válik, amely az alkalmazás egyedi azonosítására szolgál, amikor a közvetítőtől kapott választ.

</table>

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>5\. lépés: az ügynök azonosítójának hozzáadása a összes szakaszhoz

A ADAL.NET és a MSAL.NET egyaránt a `-canOpenURL:` használatával ellenőrizze, hogy a közvetítő telepítve van-e az eszközön. Adja hozzá az iOS-közvetítő helyes azonosítóját az info. plist fájl összes szakaszához a következőképpen:

<table>
<tr><td>Aktuális ADAL-kód:</td><td>MSAL-ügyfél:</td></tr>
<tr><td>
Felhasználások 

`msauth`


```CSharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
Felhasználások 

`msauthv2`


```CSharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
</array>
```
</table>

### <a name="step-6-register-your-redirect-uri-in-the-portal"></a>6\. lépés: az átirányítási URI regisztrálása a portálon

A ADAL.NET és a MSAL.NET egyaránt hozzáadhat egy extra követelményt az átirányítási URI-hoz, ha az a közvetítőt célozza meg. Regisztrálja az átirányítási URI-t az alkalmazásával a portálon.
<table>
<tr><td>Aktuális ADAL-kód:</td><td>MSAL-ügyfél:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`

Példa: 

`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

Példa:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

További információ az átirányítási URI-portálon való regisztrálásáról: [a közvetítő kihasználása a Xamarin. iOS-alkalmazásokban](msal-net-use-brokers-with-xamarin-apps.md#step-7-make-sure-the-redirect-uri-is-registered-with-your-app).

## <a name="next-steps"></a>Következő lépések

Ismerje meg [, hogyan Xamarin az iOS-specifikus szempontokat a MSAL.net](msal-net-xamarin-ios-considerations.md). 
