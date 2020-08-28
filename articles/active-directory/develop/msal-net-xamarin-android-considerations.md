---
title: Xamarin Android-kódok konfigurálása és hibaelhárítása (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Tudnivalók a Xamarin Android és a Microsoft Authentication Library for .NET (MSAL.NET) használatának szempontjairól.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 256bee26ec6aef464d9ee838ffca4d8c1ecbbb4e
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89047786"
---
# <a name="configuration-requirements-and-troubleshooting-tips-for-xamarin-android-with-msalnet"></a>Konfigurációs követelmények és hibaelhárítási tippek a Xamarin Androidhoz a MSAL.NET

A Xamarin Android és a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatával számos konfigurációs módosítást kell végrehajtania a kódban. A következő szakaszok ismertetik a szükséges módosításokat, majd egy [hibaelhárítási](#troubleshooting) szakaszt, amely segít elkerülni a leggyakoribb problémák némelyikét.

## <a name="set-the-parent-activity"></a>A szülő tevékenység beállítása

Az Android Xamarin állítsa be a szülő tevékenységet, hogy a jogkivonat az interakció után visszaadja a következőt:

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

A MSAL.NET 4,2-es és újabb verzióiban ezt a funkciót a [PublicClientApplication][PublicClientApplication]szintjén is megadhatja. Ehhez visszahívás használata:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Ha a [CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin)-t használja, a [`PublicClientApplication`][PublicClientApplication] Builder-kódnak ehhez a kódrészlethez hasonlóan kell kinéznie:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>Győződjön meg arról, hogy a vezérlő visszaadja a MSAL

Ha a hitelesítési folyamat interaktív része véget ér, a vezérlőt a MSAL való visszalépéssel visszaállíthatja [`Activity`][Activity] .[`OnActivityResult()`][OnActivityResult] metódus.

A felülbírálásban hívja meg a MSAL. NET `AuthenticationContinuationHelper` .`SetAuthenticationContinuationEventArgs()` a hitelesítési folyamat interaktív részének végén a vezérlés visszaadására szolgáló metódus MSAL.

```csharp
protected override void OnActivityResult(int requestCode,
                                         Result resultCode,
                                         Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    // Return control to MSAL
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                            resultCode,
                                                                            data);
}
```

## <a name="update-the-android-manifest"></a>Az Android-jegyzékfájl frissítése

A *AndroidManifest.xml* fájlnak a következő értékeket kell tartalmaznia:

```XML
  <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
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

Helyettesítse be a Azure Portalban regisztrált csomag nevét az `android:host=` értékhez. Helyettesítse be a Azure Portalban regisztrált kulcs kivonatát az `android:path=` értékhez. Az aláírási kivonat *nem* lehet URL-kódolású. Győződjön meg arról, hogy a kezdő perjel ( `/` ) megjelenik az aláírás kivonatának elején.

Azt is megteheti, [hogy a tevékenységet programkódban hozza létre](/xamarin/android/platform/android-manifest#the-basics) ahelyett, hogy manuálisan szerkeszti a *AndroidManifest.xml*. A tevékenység kódban való létrehozásához először hozzon létre egy osztályt, amely tartalmazza az `Activity` attribútumot és az `IntentFilter` attribútumot.

Az alábbi példa egy olyan osztályra mutat, amely az XML-fájl értékeit jelöli:

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

### <a name="xamarinforms-43x-manifest"></a>Xamarin. Forms 4.3. x jegyzékfájl

A Xamarin. Forms 4.3. x olyan kódot állít elő, amely az attribútumotAndroidManifest.xmlértékre állítja be `package` `com.companyname.{appName}` . * * Ha a `DataScheme` as-t használja `msal{client_id}` , érdemes lehet módosítani az értéket, hogy az megfeleljen a névtér értékének `MainActivity.cs` .

## <a name="use-the-embedded-web-view-optional"></a>A beágyazott webes nézet használata (nem kötelező)

Alapértelmezés szerint a MSAL.NET a rendszer webböngészőjét használja. Ez a böngésző lehetővé teszi az egyszeri bejelentkezést (SSO) a webalkalmazások és más alkalmazások használatával. Bizonyos ritkán előforduló esetekben előfordulhat, hogy a rendszer egy beágyazott webes nézetet szeretne használni.

Ez a kódrészlet egy beágyazott webes nézet beállítását mutatja be:

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

További információkért lásd: [webböngészők használata a MSAL.net](msal-net-web-browsers.md) és a [Xamarin Android rendszerbeli böngészőkkel kapcsolatos megfontolások](msal-net-system-browser-android-considerations.md).

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="general-tips"></a>Általános tippek

- Frissítse a meglévő MSAL.NET NuGet-csomagot a [MSAL.net legújabb verziójára](https://www.nuget.org/packages/Microsoft.Identity.Client/).
- Ellenőrizze, hogy a Xamarin. Forms a legújabb verzióban van-e.
- Ellenőrizze, hogy a Xamarin. Android. support. v4 a legújabb verzió-e.
- Győződjön meg arról, hogy az összes Xamarin. Android. support csomag a legújabb verziót célozza meg.
- Törölje vagy építse újra az alkalmazást.
- A Visual Studióban próbálja meg beállítani a párhuzamos projektek maximális számát **1**értékre. Ehhez válassza a **Beállítások**  >  **projektek és megoldások**  >  **Létrehozás és Futtatás**  >  **maximális száma párhuzamos projektek**buildek lehetőséget.
- Ha a parancssorból épít, és a parancs a parancsot használja `/m` , próbálja meg eltávolítani az elemet a parancsból.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Hiba: a AuthenticationContinuationHelper név nem létezik az aktuális környezetben.

Ha egy hiba azt jelzi, hogy `AuthenticationContinuationHelper` az aktuális környezetben nem létezik, előfordulhat, hogy a Visual Studio hibásan frissítette az *Android. csproj \* * fájlt. Előfordul, hogy a fájl elérési útja nem megfelelően van megadva a `<HintPath>` `netstandard13` helyett `monoandroid90` .

Ez a példa a fájl helyes elérési útját tartalmazza:

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Következő lépések

További információ: a [Microsoft Identity platformot használó Xamarin Mobile-alkalmazás](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations)mintája. A következő táblázat összefoglalja a fontos információkat a README fájlban.

| Sample | Platform | Description |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin. iOS, Android, UWP | Egy egyszerű Xamarin. Forms-alkalmazás, amely bemutatja, hogyan hitelesítheti a Microsoft személyes fiókjait és az Azure AD-t az Azure AD 2,0-végponton keresztül a MSAL használatával. Az alkalmazás azt is bemutatja, hogyan lehet elérni Microsoft Graph és megjeleníti az eredményül kapott jogkivonatot. <br>![Hitelesítési folyamat ábrája](media/msal-net-xamarin-android-considerations/topology.png) |

<!-- REF LINKS -->
[PublicClientApplication]: /dotnet/api/microsoft.identity.client.publicclientapplication
[OnActivityResult]: /dotnet/api/android.app.activity.onactivityresult
[Activity]: /dotnet/api/android.app.activity
