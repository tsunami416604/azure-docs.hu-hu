---
title: Xamarin Android szempontok (Microsoft-hitelesítési tár .NET) |} Az Azure
description: Figyelembe kell venni bizonyos ismerje meg a Xamarin Android használata a Microsoft-hitelesítési tár .NET (MSAL.NET) esetén.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 357c83cfd0ae3fed8b13419e72f50fcb90c04186
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550659"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Xamarin Android-specifikus MSAL.NET megfontolások
Ez a cikk ismerteti a figyelembe kell venni bizonyos Xamarin Android használata a Microsoft-hitelesítési tár .NET (MSAL.NET) esetén.

Ez a cikk szól MSAL.NET 3.x. Ha érdekli az MSAL.NET 2.x verzióját, lásd: [Xamarin Android tulajdonságairól az MSAL.NET 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics-2x).

## <a name="set-the-parent-activity"></a>A szülőtevékenység beállítása

Xamarin.Android, meg kell, hogy a jogkivonat beolvasása vissza, ha a kapcsolati történt, állítsa be a fölérendelt tevékenység.

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

## <a name="ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends"></a>Ellenőrzést biztosító visszatér arra MSAL egyszer interaktív részének a hitelesítési folyamat véget ér
Android rendszeren felül kell bírálnia az `OnActivityResult` módszer a `Activity` és AuthenticationContinuationHelper MSAL osztály SetAuthenticationContinuationEventArgs metódust.

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
Sort biztosítja, hogy a vezérlőelem visszatér arra MSAL után interaktív részének a hitelesítési folyamat véget ért.

## <a name="update-the-android-manifest"></a>Az Android-jegyzékfájlban frissítése
A `AndroidManifest.xml` tartalmaznia kell a következő értékeket:
```csharp
<activity android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="msal{client_id}" android:host="auth" />
         </intent-filter>
</activity>
```

## <a name="use-the-embedded-web-view-optional"></a>(Nem kötelező) a beágyazott webes nézet használata

Alapértelmezés szerint az MSAL.NET használja a rendszer webböngésző, amely lehetővé teszi, hogy az egyszeri bejelentkezés a webes alkalmazások és más alkalmazások. Egyes ritka esetekben érdemes lehet, hogy adja meg, hogy szeretné-e használni a beágyazott webes nézet. További információkért lásd: [MSAL.NET használ egy webes böngésző](msal-net-web-browsers.md) és [Android rendszer böngésző](msal-net-system-browser-android-considerations.md).

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

## <a name="troubleshooting"></a>Hibaelhárítás
Hozzon létre egy új Xamarin.Forms-alkalmazást, és vegyen fel egy hivatkozást az MSAL.Net NuGet-csomagot, ha ezt ugyanúgy működnek.
Ha frissíteni szeretne egy meglévő Xamarin.Forms-alkalmazás az MSAL.NET 1.1.2 előzetes verzió, vagy később build problémákat tapasztalhat.

Ezek a problémák elhárításához, a következőket kell elvégeznie:
- Frissítse a meglévő MSAL.NET NuGet-csomag MSAL.NET előzetes 1.1.2 vagy újabb
- Ellenőrizze, hogy Xamarin.Forms automatikusan frissülnek 2.5.0.122203 verzió (Ha nem, erre a verzióra frissítés)
- Ellenőrizze, hogy Xamarin.Android.Support.v4 automatikusan frissülnek 25.4.0.2 verzió (Ha nem, erre a verzióra frissítés)
- Összes Xamarin.Android.Support csomagot kell célzó 25.4.0.2 verzió
- Tiszta/újraépítése
- Próbálja meg a maximális párhuzamos projekt beállítása hoz létre a Visual Studióban 1 (beállításai -> projektek és megoldások -> Build és Futtatás -> párhuzamos projektek buildek maximális száma)
- Azt is megteheti Ha hoz létre a parancssorból, próbálja meg /m eltávolítását a parancs, ha azt használja.


### <a name="error-the-name-authenticationcontinuationhelper-does-not-exist-in-the-current-context"></a>Hiba: A név "AuthenticationContinuationHelper" nem létezik az aktuális környezetben

Ennek oka valószínűleg az, hogy a Visual Studio megfelelően nem frissítette a Android.csproj* fájlt. Egyes esetekben a  **\<HintPath >** filepath helytelenül tartalmaz helyett netstandard13 **monoandroid90**.

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>További lépések

További részletek és példák találhatók a [Android figyelembe kell venni bizonyos](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations) bekezdés readme.md fájl a következő mintát:

| Sample | Platform | Leírás |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Egy egyszerű Xamarin Forms alkalmazás amely hogyan használható az MSAL MSA és az Azure AD a AADD v2.0-végpont hitelesítéséhez, és hozzáférhet a Microsoft Graph, az eredményül kapott jogkivonatokkal. <br>![Topológia](media/msal-net-xamarin-android-considerations/topology.png) |