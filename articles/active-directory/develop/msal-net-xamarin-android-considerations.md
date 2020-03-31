---
title: Xamarin Android szempontok (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: A cikk ismerteti a Xamarin Android és a Microsoft Authentication Library for .NET (MSAL.NET) használatával kapcsolatos szempontokat.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81b55253d757f641979c6f72001803d7d38d9af3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132501"
---
# <a name="considerations-for-using-xamarin-android-with-msalnet"></a>A Xamarin Android MSAL.NET használatának szempontjai
Ez a cikk azt ismerteti, hogy mit kell figyelembe venni, amikor a Xamarin Android ot használja a Microsoft Authentication Library for .NET (MSAL.NET) rendszerrel.

## <a name="set-the-parent-activity"></a>A fölérendelt tevékenység beállítása

A Xamarin Android, állítsa be a szülő tevékenységúgy, hogy a jogkivonat a kapcsolati tevékenység után visszatér. Íme egy példa egy kódra:

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

Az MSAL 4.2-es és újabb verzióiban ezt `PublicClientApplication`a funkciót a . Ehhez használjon visszahívást:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Ha [a CurrentActivityPlugin-t](https://github.com/jamesmontemagno/CurrentActivityPlugin)használja, akkor az `PublicClientApplication` építőkód a következő példához hasonlóan néz ki.

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>Annak biztosítása, hogy a vezérlés visszatérjön az MSAL-hoz 
Amikor a hitelesítési folyamat interaktív része véget ér, győződjön meg arról, hogy a vezérlő visszakerül az MSAL-ra. Androidon felülbírálja `OnActivityResult` a `Activity`metódust. Ezután `SetAuthenticationContinuationEventArgs` hívja meg `AuthenticationContinuationHelper` az MSAL osztály metódusát. 

Például:

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

Ez a sor biztosítja, hogy a vezérlő visszatér az MSAL-hoz a hitelesítési folyamat interaktív részének végén.

## <a name="update-the-android-manifest"></a>Az Android-jegyzékfájl frissítése
Az *AndroidManifest.xml* fájlnak a következő értékeket kell tartalmaznia:

<!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
```
  <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
     <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
     </intent-filter>
 </activity>
```

Helyettesítse az Azure Portalon regisztrált `android:host=` csomagnevet az értékkel. Helyettesítse az Azure Portalon regisztrált kulcskivonatot az `android:path=` értékkel. Az aláírás kivonatát *nem* szabad URL-kódolással kódolni. Győződjön meg arról, hogy az aláíráskivonat elején egy előrevezető perjel (`/`) jelenik meg.

Másik lehetőségként [hozza létre a tevékenységet kódban](https://docs.microsoft.com/xamarin/android/platform/android-manifest#the-basics) az *AndroidManifest.xml*manuális szerkesztése helyett. A tevékenység kódban való létrehozásához először `Activity` hozzon `IntentFilter` létre egy osztályt, amely tartalmazza az attribútumot és az attribútumot. 

Íme egy példa egy osztályra, amely az XML-fájl értékeit képviseli:

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

### <a name="xamarinforms-43x-manifest"></a>Xamarin.Forms 4.3.X manifeszt

A Xamarin.Forms 4.3.x olyan `package` kódot hoz `com.companyname.{appName}` létre, amely az attribútumot az *AndroidManifest.xml fájlra állítja.* Ha a `DataScheme` `msal{client_id}`programot használja, akkor érdemes lehet módosítani az `MainActivity.cs` értéket, hogy megfeleljen a névtér értékének.

## <a name="use-the-embedded-web-view-optional"></a>A beágyazott webnézet használata (nem kötelező)

Alapértelmezés szerint MSAL.NET a rendszer webböngészőjét használja. Ez a böngésző lehetővé teszi, hogy egyszeri bejelentkezés (SSO) segítségével webes alkalmazások és más alkalmazások. Néhány ritka esetben előfordulhat, hogy beágyazott webes nézetet szeretne használni a rendszer számára. 

Ez a példa kódbemutatja, hogyan állíthat be beágyazott webnézetet:

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

További információ: [Webböngészők használata MSAL.NET](msal-net-web-browsers.md) és [Xamarin Android rendszerböngészővel kapcsolatos szempontok.](msal-net-system-browser-android-considerations.md)


## <a name="troubleshoot"></a>Hibaelhárítás
Létrehozhat egy új Xamarin.Forms alkalmazást, és hozzáadhat egy hivatkozást a MSAL.NET NuGet csomaghoz.
Előfordulhat azonban, hogy egy meglévő Xamarin.Forms alkalmazást frissít, hogy MSAL.NET 1.1.2 vagy újabb verziót.

A buildelési problémák elhárítása:

- Frissítse a meglévő MSAL.NET NuGet csomagot MSAL.NET 1.1.2-es vagy újabb verziójú előzetesverziójára.
- Ellenőrizze, hogy a Xamarin.Forms automatikusan a 2.5.0.122203 verzióra frissült-e. Szükség esetén frissítse a Xamarin.Forms verziót erre a verzióra.
- Ellenőrizze, hogy a Xamarin.Android.Support.v4 automatikusan a 25.4.0.2-es verzióra frissült-e. Szükség esetén frissítsen a 25.4.0.2-es verzióra.
- Győződjön meg arról, hogy az összes Xamarin.Android.Support csomag a 25.4.0.2-es célverziót célozza.
- Tisztítsa meg vagy építse újra az alkalmazást.
- A Visual Studióban próbálja meg a párhuzamos projektbuildek maximális számát 1-re alapozni. Ehhez válassza a **Beállítások** > **projektek és megoldások** > **létrehozása és futtatása** > **a párhuzamos projektek maximális száma .**
- Ha a parancssorból épít, és `/m`a parancs a parancsot használja, próbálja meg eltávolítani ezt az elemet a parancsból.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Hiba: A AuthenticationContinuationHelper név nem létezik az aktuális környezetben

Ha egy hiba `AuthenticationContinuationHelper` azt jelzi, hogy nem létezik az aktuális környezetben, előfordulhat, hogy a Visual Studio helytelenül frissítette az Android.csproj* fájlt. Néha a * \<HintPath>* fájl elérési útja helytelenül tartalmaz *netstandard13* helyett *monoandroid90*.

Ez a példa a megfelelő fájlelérési utat tartalmazza:

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>További lépések

További információt a [Microsoft identity platformot használó Xamarin mobilalkalmazás mintájában talál.](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations) Az alábbi táblázat összefoglalja a README fájlban található releváns információkat.

| Sample | Platform | Leírás |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin.iOS, Android, UWP | Egy egyszerű Xamarin.Forms alkalmazás, amely bemutatja, hogyan lehet az MSAL használatával hitelesíteni a Microsoft személyes fiókjait és az Azure AD-t az Azure AD 2.0-s végponton keresztül. Az alkalmazás azt is bemutatja, hogyan érheti el a Microsoft Graph, és megmutatja az eredményül kapott jogkivonatot. <br>![Topológia](media/msal-net-xamarin-android-considerations/topology.png) |
