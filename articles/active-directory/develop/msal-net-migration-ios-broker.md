---
title: Xamarin iOS-alkalmazások áttelepítése a ADAL.NET-ről a MSAL.NET-re való Microsoft Authenticator használatával | Azure
description: Ismerje meg, hogyan telepíthet át Xamarin iOS-alkalmazásokat a .NET-hez készült Azure AD Authentication Library (ADAL.NET) Microsoft Authenticator használatával a .NET-hez készült Microsoft Authentication Library (MSAL.NET) segítségével.
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
ms.openlocfilehash: 609aeeada78985466cb7c254cab32864e5d84b0e
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70875652"
---
# <a name="migrating-ios-applications-using-microsoft-authenticator-from-adalnet-to-msalnet"></a>IOS-alkalmazások migrálása Microsoft Authenticator használatával a ADAL.NET-ből a MSAL.NET-be

A ADAL.NET-t és az iOS-közvetítőt használta, és itt az ideje, hogy áttelepítsen a MSAL.NET [Microsoft Authentication Library](msal-overview.md)-re, amely támogatja az iOS-es verzióról a 4,3-től kezdődően a közvetítőt. 

Első lépések Ez a cikk segít áttelepíteni a Xamarin iOS-alkalmazást a ADAL-ről a MSAL-re.

## <a name="prerequisites"></a>Előfeltételek
Ez a dokumentum azt feltételezi, hogy már rendelkezik egy IOS-közvetítővel integrált Xamarin iOS-alkalmazással. Ha nem, érdemes közvetlenül a MSAL.NET áthelyezni, és ott megkezdeni a közvetítő megvalósítását. Tekintse meg [ezt a dokumentációt](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications) , amely ismerteti, hogyan kell megkeresni az iOS-közvetítőt a MSAL.net egy új alkalmazással.

## <a name="background"></a>Háttér

### <a name="what-are-brokers"></a>Mik azok a brókerek?

A brókerek a Microsoft által biztosított alkalmazások, Androidon és iOS-en ([Microsoft Authenticator](https://www.microsoft.com/account/authenticator) iOS és Android rendszeren, Intune céges portál az Android rendszeren). 

Ezek a következőket teszik lehetővé:

- Egyszeri bejelentkezés,
- Az eszköz azonosítása, amelyet bizonyos [feltételes hozzáférési házirendek](../conditional-access/overview.md) igényelnek (lásd: [eszközkezelés](../conditional-access/conditions.md#device-platforms))
- Az alkalmazások azonosításának ellenőrzése bizonyos vállalati helyzetekben is szükséges (lásd: az [Intune Mobile Application Management vagy a MAM](https://docs.microsoft.com/intune/mam-faq))

## <a name="migrate-from-adal-to-msal"></a>Migrálás a ADAL-ből a MSAL-be

### <a name="step-1-enable-the-broker"></a>1\. lépés: A közvetítő engedélyezése

<table>
<tr><td>Aktuális ADAL-kód:</td><td>MSAL-ügyfél:</td></tr>
<tr><td>
A ADAL.NET-ben a Broker-támogatás hitelesítésen alapuló kontextusban volt engedélyezve, alapértelmezés szerint le van tiltva. A Broker hívásához a `useBroker` `PlatformParameters` konstruktorban igaz értéket kell beállítania a következőhöz:

```CSharp
public PlatformParameters(
        UIViewController callerViewController, 
        bool useBroker)
```
Emellett a platform-specifikus kódban, ebben a példában az iOS-hez készült oldal megjelenítő lapján állítsa be a következőt:`useBroker` 
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
A MSAL.NET-ben a Broker-támogatás nyilvános ügyfélalkalmazás alapján engedélyezett. Ez alapértelmezés szerint le van tiltva. Az engedélyezéséhez használja a következőt: 

`WithBroker()`a (z) paraméter (alapértelmezés szerint True értékre van állítva) a Broker meghívásához:

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
A ADAL.NET-ben a UIViewController a PlatformParameters részeként adták át (lásd az 1. lépésben szereplő példát). Azonban a MSAL.NET-ben a fejlesztőnek rugalmasabban kell lennie, és a szokásos iOS-használathoz nem szükséges. A közvetítő használatához azonban be kell állítania az objektum ablakát, hogy a brókertől érkező válaszokat küldjön és fogadjon. 
<table>
<tr><td>Aktuális ADAL-kód:</td><td>MSAL-ügyfél:</td></tr>
<tr><td>
A UIViewController az iOS-specifikus platform PlatformParamters továbbítja a rendszer.

```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
A MSAL.NET-ben két dolgot kell megadnia az iOS-hez készült objektum ablakának beállításához:

1) A `AppDelegate.cs`ben állítsa be `App.RootViewController` a-t `UIViewController()`egy új értékre. Ez a hozzárendelés biztosítja, hogy van egy UIViewController a közvetítő hívásával. Ha nincs megfelelően beállítva, a következő hibaüzenet jelenhet meg:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) A AcquireTokenInteractive-hívásban használja a`.WithParentActivityOrWindow(App.RootViewController)`
és adja át az objektum ablak you'will való hivatkozását.

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

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>3\. lépés: AppDelegate frissítése a visszahívás kezeléséhez
Mind a ADAL, mind a MSAL meg fogja hívni a közvetítőt, a közvetítő pedig az `OpenUrl` `AppDelegate` osztály metódusával hívja vissza az alkalmazást. További információk [itt](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS/_edit#step-two-update-appdelegate-to-handle-the-callback) érhetők el

: heavy_check_mark:**a ADAL.net és a MSAL.net között nincsenek változások**

### <a name="step-4-register-a-url-scheme"></a>4\. lépés: URL-séma regisztrálása
A ADAL.NET és a MSAL.NET URL-címek használatával meghívja a közvetítőt, és visszaküldi a közvetítői választ az alkalmazásnak. Az alkalmazáshoz tartozó fájlban regisztrálja az URL-sémát a `Info.plist` következő módon:

<table>
<tr><td>Aktuális ADAL-kód:</td><td>MSAL-ügyfél:</td></tr>
<tr><td>
Az URL-séma egyedi az alkalmazás számára.
</td><td>
A(z) 

`CFBundleURLSchemes`a névnek szerepelnie kell 

`msauth.`

előtagként, amelyet a`CFBundleURLName`

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
>  Ez az URL-séma az alkalmazás egyedi azonosításához használt RedirectUri részévé válik a közvetítő válaszának fogadásakor

</table>

### <a name="step-5-lsapplicationqueriesschemes"></a>5\. lépés: Összes

A ADAL.net és a MSAL.net `-canOpenURL:` egyaránt azt vizsgálja, hogy a közvetítő telepítve van-e az eszközön. Adja hozzá az iOS-közvetítő helyes azonosítóját az info. plist fájl összes szakaszához a következőképpen: 
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

### <a name="step-6-register-you-redirecturi-in-the-portal"></a>6\. lépés: RedirectUri regisztrálása a portálon

A ADAL.NET és a MSAL.NET egyaránt hozzáadunk egy extra követelményt a redirectUri a közvetítő célzásakor. Regisztrálja az átirányítási URI-t az alkalmazásával a portálon.
<table>
<tr><td>Aktuális ADAL-kód:</td><td>MSAL-ügyfél:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`például`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

példa:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

További információ a redirectUri a portálon való regisztrálásáról: a közvetítő kihasználása a [Xamarin. iOS-alkalmazásokban](msal-net-use-brokers-with-xamarin-apps.md#step-7-make-sure-the-redirect-uri-is-registered-with-your-app) további részletekért.

## <a name="next-steps"></a>További lépések

Ismerje meg [, hogyan Xamarin az iOS-specifikus szempontokat a MSAL.net](msal-net-xamarin-ios-considerations.md). 
