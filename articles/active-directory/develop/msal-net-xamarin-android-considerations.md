---
title: Xamarin Android-megfontolások (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Tudnivalók a Xamarin Android és a Microsoft Authentication Library for .NET (MSAL.NET) használatának szempontjairól.
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
ms.openlocfilehash: bb5950360734bc46923ef18424e3ad1ce275ad7a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82652672"
---
# <a name="considerations-for-using-xamarin-android-with-msalnet"></a>A Xamarin Android és a MSAL.NET használatának szempontjai
Ez a cikk azt ismerteti, hogy milyen szempontokat kell figyelembe vennie, amikor a Xamarin Androidot használja a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatával.

## <a name="set-the-parent-activity"></a>A szülő tevékenység beállítása

Az Android Xamarin állítsa be a szülő tevékenységet úgy, hogy a jogkivonat a beavatkozás után visszaadja. Példa a következő kódra:

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

A MSAL 4,2-es és újabb verzióiban ezt a funkciót a (z) szintjén is megadhatja `PublicClientApplication` . Ehhez visszahívás használata:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Ha a [CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin)-t használja, a `PublicClientApplication` Builder-kód a következő példához hasonlóan néz ki.

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>Győződjön meg arról, hogy a vezérlő visszaadja a MSAL 
Ha a hitelesítési folyamat interaktív része lejár, győződjön meg arról, hogy a vezérlő visszakerül a MSAL. Az Android rendszeren írja felül a `OnActivityResult` metódust `Activity` . Ezután hívja meg a `SetAuthenticationContinuationEventArgs` `AuthenticationContinuationHelper` MSAL osztály metódusát. 

Íme egy példa:

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

Ez a sor biztosítja, hogy a vezérlő visszaadja a MSAL a hitelesítési folyamat interaktív részének végén.

## <a name="update-the-android-manifest"></a>Az Android-jegyzékfájl frissítése
A *AndroidManifest.xml* fájlnak a következő értékeket kell tartalmaznia:

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

Helyettesítse be a Azure Portalban regisztrált csomag nevét az `android:host=` értékhez. Helyettesítse be a Azure Portalban regisztrált kulcs kivonatát az `android:path=` értékhez. Az aláírási kivonat *nem* lehet URL-kódolású. Győződjön meg arról, hogy a kezdő perjel ( `/` ) megjelenik az aláírás kivonatának elején.

Azt is megteheti, [hogy a tevékenységet programkódban hozza létre](https://docs.microsoft.com/xamarin/android/platform/android-manifest#the-basics) ahelyett, hogy manuálisan szerkeszti a *AndroidManifest.xml*. A tevékenység kódban való létrehozásához először hozzon létre egy osztályt, amely tartalmazza az `Activity` attribútumot és az `IntentFilter` attribútumot. 

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

### <a name="xamarinforms-43x-manifest"></a>Xamarin. Forms 4.3. X jegyzékfájl

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


## <a name="troubleshoot"></a>Hibaelhárítás
Létrehozhat egy új Xamarin. Forms-alkalmazást, és hozzáadhat egy hivatkozást a MSAL.NET NuGet-csomaghoz.
Előfordulhat azonban, hogy problémák merültek fel, ha meglévő Xamarin. Forms alkalmazást frissít a MSAL.NET Preview 1.1.2 vagy újabb verzióra.

A Build-problémák elhárítása:

- Frissítse a meglévő MSAL.NET NuGet-csomagot a MSAL.NET Preview 1.1.2 vagy újabb verzióra.
- Győződjön meg arról, hogy a Xamarin. Forms automatikusan frissült a 2.5.0.122203 verzióra. Ha szükséges, frissítse a Xamarin. Forms verziót erre a verzióra.
- Győződjön meg arról, hogy a Xamarin. Android. support. v4 automatikusan frissítve lett a 25.4.0.2 verzióra. Ha szükséges, frissítsen a verzió 25.4.0.2.
- Győződjön meg arról, hogy az összes Xamarin. Android. support csomagok cél verziója 25.4.0.2.
- Törölje vagy építse újra az alkalmazást.
- A Visual Studióban próbálja meg beállítani a párhuzamos projektek maximális számát 1 értékre. Ehhez válassza a **Beállítások**  >  **projektek és megoldások**  >  **Létrehozás és Futtatás**  >  **maximális száma párhuzamos projektek**buildek lehetőséget.
- Ha a parancssorból épít, és a parancs a parancsot használja `/m` , próbálja meg eltávolítani az elemet a parancsból.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Hiba: a AuthenticationContinuationHelper név nem létezik az aktuális környezetben.

Ha egy hiba azt jelzi, hogy `AuthenticationContinuationHelper` az aktuális környezetben nem létezik, előfordulhat, hogy a Visual Studio hibásan frissítette az Android. csproj * fájlt. Előfordulhat, hogy a *\<HintPath>* fájl elérési útja nem megfelelően tartalmaz *netstandard13* a *monoandroid90*helyett.

Ez a példa a fájl helyes elérési útját tartalmazza:

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>További lépések

További információ: a [Microsoft Identity platformot használó Xamarin Mobile-alkalmazás](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations)mintája. A következő táblázat összefoglalja a fontos információkat a README fájlban.

| Sample | Platform | Leírás |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin. iOS, Android, UWP | Egy egyszerű Xamarin. Forms-alkalmazás, amely bemutatja, hogyan hitelesítheti a Microsoft személyes fiókjait és az Azure AD-t az Azure AD 2,0-végponton keresztül a MSAL használatával. Az alkalmazás azt is bemutatja, hogyan lehet elérni Microsoft Graph és megjeleníti az eredményül kapott jogkivonatot. <br>![Topológia](media/msal-net-xamarin-android-considerations/topology.png) |
