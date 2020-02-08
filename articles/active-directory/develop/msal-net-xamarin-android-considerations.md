---
title: Xamarin Android-megfontolások (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg a Xamarin Android és a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatára vonatkozó szempontokat.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: fd6dd6781b808bc454a402a55aac9d07a6fc23b0
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2020
ms.locfileid: "77085819"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Xamarin Android-specifikus megfontolások a MSAL.NET
Ez a cikk a Xamarin Android és a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatára vonatkozó szempontokat ismerteti.

## <a name="set-the-parent-activity"></a>A szülő tevékenység beállítása

A Xamarin. Android esetében be kell állítania a fölérendelt tevékenységet, hogy a jogkivonat a beavatkozást követően visszakerüljön.

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```
Ezt a PublicClientApplication szintjén (MSAL 4.2 +) is beállíthatja egy visszahívás használatával.

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Javasoljuk, hogy a CurrentActivityPlugin használja [itt](https://github.com/jamesmontemagno/CurrentActivityPlugin).  Ezt követően a PublicClientApplication Builder-kód így fog kinézni:

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```


## <a name="ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends"></a>Annak biztosítása, hogy a vezérlés visszakerüljön a MSAL, amint a hitelesítési folyamat interaktív része véget ér
Androidon felül kell bírálnia a `Activity` `OnActivityResult` metódusát, és meg kell hívnia a AuthenticationContinuationHelper MSAL osztály SetAuthenticationContinuationEventArgs metódusát.

```csharp
protected override void OnActivityResult(int requestCode, 
                                         Result resultCode, Intent data)
{
 base.OnActivityResult(requestCode, resultCode, data);
 AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                         resultCode,
                                                                         data);
}

```
Ez a vonal biztosítja, hogy a vezérlő visszakerüljön a MSAL, amint a hitelesítési folyamat interaktív része befejeződött.

## <a name="update-the-android-manifest"></a>Az Android-jegyzékfájl frissítése
A `AndroidManifest.xml`nak a következő értékeket kell tartalmaznia:

<!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
  <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">< leképezés – szűrő > <action android:name="android.intent.action.VIEW" /> <category android:name="android.intent.category.DEFAULT" /> <category android:name="android.intent.category.BROWSABLE" /> <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" /> </Intent-Filter ></activity>
```
Substitute the package name you registered in the Azure portal for the `android:host=` value. Substitute the key hash you registered in the Azure portal for the `android:path=` value. The Signature Hash should **not** be URL encoded. Ensure that there is a leading `/` at the beginning of your Signature Hash.

Or, you can [create the activity in code](https://docs.microsoft.com/xamarin/android/platform/android-manifest#the-basics) and not manually edit `AndroidManifest.xml`. For that, you must create a class that has the `Activity` and `IntentFilter` attribute. A class that represents the same values of the above xml would be:

```csharp
  [Activity]
  [IntentFilter(new[] { Intent.ActionView },
        Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
        DataHost = "auth",
        DataScheme = "msal{client_id}")]
  public class MsalActivity : BrowserTabActivity
  {
  }
```

### <a name="xamarinforms-43x-manifest"></a>XamarinForms 4.3. X jegyzékfájl

A XamarinForms 4.3. x által generált kód a `package` attribútumot állítja be `com.companyname.{appName}`ra a `AndroidManifest.xml`. Előfordulhat, hogy az értéket a `MainActivity.cs` névterével megegyezőre szeretné módosítani, ha a `DataScheme`t `msal{client_id}`ként használja.

## <a name="use-the-embedded-web-view-optional"></a>A beágyazott webes nézet használata (nem kötelező)

Alapértelmezés szerint a MSAL.NET a rendszerböngészőt használja, amely lehetővé teszi az egyszeri bejelentkezést a webalkalmazásokkal és más alkalmazásokkal. Bizonyos ritkán előforduló esetekben érdemes megadnia, hogy a beágyazott webes nézetet kívánja használni. További információ: [a MSAL.net webböngészőt](msal-net-web-browsers.md) és [Android rendszerű böngészőt](msal-net-system-browser-android-considerations.md)használ.

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

## <a name="troubleshooting"></a>Hibakeresés
Ha létrehoz egy új Xamarin. Forms alkalmazást, és felvesz egy hivatkozást a MSAL.Net NuGet-csomagra, akkor ez csak működni fog.
Ha azonban egy meglévő Xamarin. Forms alkalmazást szeretne frissíteni a MSAL.NET Preview 1.1.2 vagy újabb verziójára, előfordulhat, hogy felmerülő problémák merülhetnek fel.

A problémák elhárításához a következőket kell tennie:
- A meglévő MSAL.NET NuGet-csomag frissítése a MSAL.NET Preview 1.1.2-es vagy újabb verziójára
- Ellenőrizze, hogy a Xamarin. Forms automatikusan frissítve lett-e a 2.5.0.122203 verzióra (ha nem, frissítsen erre a verzióra)
- Ellenőrizze, hogy a Xamarin. Android. support. v4 automatikusan frissítve lett-e a 25.4.0.2 verzióra (ha nem, frissítsen erre a verzióra)
- Az összes Xamarin. Android. support csomagnak a 25.4.0.2 verziónak kell megcéloznia
- Tisztítás/Újraépítés
- Próbálja meg a maximális párhuzamos projektet 1 értékre állítani a Visual Studióban (Options-> projektek és megoldások – > Build és Run – > a párhuzamos projektek maximális száma)
- Ha a parancssorból kíván felépíteni, próbálja meg eltávolítani az/m parancsot a parancsból, ha azt használja.


### <a name="error-the-name-authenticationcontinuationhelper-does-not-exist-in-the-current-context"></a>Hiba: a "AuthenticationContinuationHelper" név nem létezik az aktuális környezetben.

Ez valószínűleg azért van, mert a Visual Studio nem tudta megfelelően frissíteni az Android. csproj * fájlt. Előfordul, hogy a **\<HintPath >** filepath helytelenül tartalmaz netstandard13 a **monoandroid90**helyett.

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Következő lépések

További részleteket és mintákat a következő minta readme.md-fájljának [Android-specifikus megfontolások](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations) című részében talál:

| Sample | Platform | Leírás |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Egy egyszerű Xamarin űrlapos alkalmazás, amely bemutatja, hogyan használhatja az MSAL-t a MSA és az Azure AD hitelesítésére a AADD 2.0-s végponton keresztül, és az eredményül kapott jogkivonattal fér hozzá a Microsoft Graphhoz. <br>![Topológia](media/msal-net-xamarin-android-considerations/topology.png) |
