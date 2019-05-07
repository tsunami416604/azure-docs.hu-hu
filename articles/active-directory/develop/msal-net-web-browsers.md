---
title: .NET-hez készült Microsoft-hitelesítési tár webböngészők |} Az Azure
description: Figyelembe kell venni bizonyos ismerje meg a Xamarin Android használata a Microsoft-hitelesítési tár .NET (MSAL.NET) esetén.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
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
ms.openlocfilehash: 350cb3fec4d325d6cf5848733c0bae18d5efacca
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076840"
---
# <a name="using-web-browsers-in-msalnet"></a>Webböngészők MSAL.NET használatával
Webböngészők az interaktív hitelesítéshez szükségesek. Alapértelmezés szerint az MSAL.NET támogatja a [rendszer webböngésző](#system-web-browser-on-xamarinios-and-xamarinandroid) a Xamarin.iOS és [Xamarin.Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/system-browser). De [is engedélyezheti a beágyazott webböngésző](#enable-embedded-webviews) a követelményektől (UX, egyszeri bejelentkezés (SSO), biztonsági szükségességét) függően [Xamarin.iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) és [Xamarin.Android](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) az alkalmazások. És is [kiválasztása dinamikusan](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) melyik webböngésző használata Chrome-ban vagy a támogató Android egyéni lapok Chrome böngészőben jelenléte alapján.

## <a name="web-browsers-in-msalnet"></a>Az MSAL.NET webböngészők

Fontos megérteni, hogy egy token beszerzése a interaktív módon, amikor a párbeszédpanel a tartalom nem áll rendelkezésre a könyvtárban, hanem az STS-re (biztonsági jogkivonatokkal kapcsolatos szolgáltatástól). A hitelesítési végpontot küld vissza néhány HTML és JavaScript, amely szabályozza a kölcsönhatások, amely webböngészőt vagy webalkalmazás vezérlőelemben jelenik meg. Lehetővé teszi az STS-re a HTML-interakció kezelésére számos előnnyel jár:

- A jelszó (ha az egyik írta be), és az alkalmazás nem a hitelesítési tár a soha nem tárolja.
- Lehetővé teszi az átirányítások az egyéb identitás-szolgáltatóktól (például bejelentkezési-be a munkahelyi vagy iskolai fiókhoz vagy egy személyes fiók MSAL, vagy az Azure AD B2C-közösségi fiókkal).
- Lehetővé teszi, hogy az STS-feltételes hozzáférés, például szabályozhatja a felhasználó nincs több többtényezős hitelesítés (MFA) hitelesítési fázisában (a Windows Hello PIN-kód megadása vagy a telefon vagy a telefonjukra hitelesítési alkalmazás hívott) megadásával. Azokban az esetekben, ahol a szükséges többtényezős hitelesítést nincs beállítva, még a felhasználó is állítsa be az azonos párbeszédpanelen szerinti.  A felhasználó beírja a mobiltelefon száma, és a egy hitelesítési alkalmazás telepítéséhez és a egy QR címke, a felhasználó fiókját hozzáadja a vizsgálat a rendszer végigvezeti. Ez a kiszolgáló kapcsolati driven nagyszerű felhasználói élményt!
- Lehetővé teszi a felhasználónak módosítania kell a jelszavát, ezen a párbeszédpanelen, amikor a jelszó lejárt (kezeléséről további mezőket a régi jelszót és az új jelszót).
- Lehetővé teszi, hogy az Azure AD-Bérlői rendszergazda által vezérelt védjegyezés a bérlő vagy az application (képek) / alkalmazás tulajdonosa.
- Lehetővé teszi a felhasználóknak, hogy engedélyt adjanak az lehetővé teszi az alkalmazás-erőforrások eléréséhez, vagy csak a hitelesítést követően a neve hatóköröket.

## <a name="system-web-browser-on-xamarinios-and-xamarinandroid"></a>Rendszer webböngészőt Xamarin.iOS és Xamarin.Android

Alapértelmezés szerint MSAL.NET a rendszer webböngésző Xamarin.iOS és Xamarin.Android használatát támogatja. Az STS-szal üzemeltetésére, ADAL.NET csak használja a **beágyazott** webböngésző. Az összes használt platformon, amely a felhasználói felület (azaz nem a .NET Core) adja meg egy párbeszédpanel a beágyazás egy webes böngésző vezérlő kódtár által biztosított. MSAL.NET is használ egy beágyazott webes nézet a .NET asztali és WAB az UWP-platform. A modul alapértelmezés szerint azonban a **rendszer webböngésző** Xamarin iOS-és Xamarin Android-alkalmazások. Az iOS-még akkor is választja, az operációs rendszer verziójától függően használja a webes nézet (iOS12, ios11-et, és korábban).

Állapotmegosztás az egyszeri bejelentkezés más alkalmazásokkal és webes alkalmazásokat anélkül, hogy egy közvetítő jelentős előnye a rendszer böngészővel rendelkezik (vállalati portál / Authenticator). A rendszer használt böngészőtől, a Xamarin iOS- és Xamarin Android-platformok esetében az MSAL.NET alapértelmezés szerint mivel ezek a rendszerek, a rendszer webböngésző a teljes képernyőt elfoglalja és jobb felhasználói élményt. A rendszer webes nézetét nem megkülönböztethető egy párbeszédpanel. Az iOS-, a felhasználónak kell beleegyezés a böngésző vissza az alkalmazást, amely zavaró lehet meghívni.

### <a name="uwp-does-not-use-the-system-webview"></a>Az UWP nem használja a System WebView-t

Az asztali alkalmazások esetében azonban indítása System WebView-t vezet egy subpar felhasználói élmény, a felhasználó megkeresheti a böngészőben, előfordulhat, hogy már rendelkezik egyéb megnyitott lapokat. És hitelesítés történt, amikor a felhasználók egy oldal, zárja be ezt az ablakot abból. Ha a felhasználó nem odafigyelni, azok zárja be a teljes folyamat (beleértve a más lapokon, amelyek a hitelesítés nem kapcsolódnak). A rendszer böngésző asztali kihasználva is kellene helyi portok megnyitása és figyel, amely speciális engedélyeket lehet szükség az alkalmazás. Fejlesztőként, felhasználó vagy rendszergazda, akkor lehet védhetőek, ezzel a követelménnyel kapcsolatban.

## <a name="enable-embedded-webviews"></a>Beágyazott webnézeteket engedélyezése 
Xamarin.iOS és Xamarin.Android-alkalmazások a beágyazott webnézeteket is engedélyezheti. MSAL.NET 2.0.0-preview kezdve MSAL.NET használatát is támogatja a **beágyazott** webview lehetőséget. ADAL.NET, a beágyazott WebView-t csak a lehetőség támogatott.

Xamarin célzó MSAL.NET használatával a fejlesztők a beágyazott webnézeteket vagy rendszer böngészők használatával választhatja. Ez az Ön által választott, a felhasználói élmény és a biztonsági szempontok cél függően.

Jelenleg MSAL.NET még nem támogatja az Android és IOS rendszerű közvetítők. Ezért ha meg kell adnia az egyszeri bejelentkezés (SSO), a rendszer böngésző továbbra is lehet jobb megoldás. Az MSAL.NET várakozó fájlok számát a beágyazott böngészővel közvetítők támogató van.

### <a name="differences-between-embedded-webview-and-system-browser"></a>Beágyazott WebView-t és a rendszer böngésző közötti különbségek 
Nincsenek visual közötti különbségeket beágyazott WebView-t és a rendszer böngésző az MSAL.NET.

**Interaktív jelentkezzen be az MSAL.NET használatával, a beágyazott Webview:**

![Beágyazott](media/msal-net-web-browsers/embedded-webview.png)

**Interaktív jelentkezzen be az MSAL.NET használatával a rendszer böngészőben:**

![Rendszer-böngésző](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Fejlesztői beállítások

A fejlesztők MSAL.NET használatával az STS interaktív párbeszédpanel megjelenítése több lehetősége van:

- **Rendszer böngészőben.** A rendszer böngésző az erőforrástárban alapértelmezés szerint van beállítva. Ha Android használja, olvassa el a [rendszer böngészők](msal-net-system-browser-android-considerations.md) konkrét információkat arról, hogy mely hitelesítési böngészők támogatottak. Amikor a rendszer böngészővel Android, javasoljuk, hogy az eszköz rendelkezik, amely támogatja az egyéni lapok Chrome böngészőben.  Ellenkező esetben hitelesítés sikertelen lehet. 
- **Beágyazott WebView-t.** Csak a beágyazott webview MSAL.NET használatához túlterhelésekkel vannak a `UIParent()` konstruktor Android és IOS rendszereken érhető el.

    iOS:

    ```csharp
    public UIParent(bool useEmbeddedWebview)
    ```

    Android:

    ```csharp
    public UIParent(Activity activity, bool useEmbeddedWebview)
    ```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Beágyazott webböngészőt vagy Xamarin.iOS böngészővel rendszer közötti választáshoz

Az iOS-alkalmazásban a `AppDelegate.cs` rendszer böngészőben vagy a beágyazott WebView-t is használhatja.

```csharp
// Use only embedded webview
App.UIParent = new UIParent(true);

// Use only system browser
App.UIParent = new UIParent();
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Beágyazott webböngészőt vagy Xamarin.Android rendszer böngészővel közötti választáshoz

Az Android-alkalmazásba a `MainActivity.cs` eldöntheti, hogy a webview-beállítások megvalósításához.

```csharp
// Use only embedded webview
App.UIParent = new UIParent(Xamarin.Forms.Forms.Context as Activity, true);

// or
// Use only system browser
App.UIParent = new UIParent(Xamarin.Forms.Forms.Context as Activity);
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Egyéni lapok a Xamarin.Android jelenlétének

Ha meg szeretné használni a rendszer webböngészőben való egyszeri bejelentkezés engedélyezése a böngészőben futó alkalmazásokkal, de a felhasználói élmény az Android-eszközök nem rendelkeznek egy böngészőben az egyéni lap támogatásával kapcsolatos miatt aggódó, lehetősége van meghívásával dönthet arról, hogy a `IsSystemWebViewAvailable()` metódus az < c 2 > `UIParent` . A metódus visszatérése `true` , ha a PackageManager egyéni lapok észleli és `false` ha azok nem észlelhető az eszközön.

Ezt a módszert, és a követelmények által visszaadott érték alapján, akkor is döntéseket:

- Egyéni hibaüzenet a felhasználó számára adhat vissza. Példa: "Telepítse a Chrome-ban való hitelesítés folytatásához" – vagy –
- Térhet vissza a beágyazott webview lehetőséget, és indítsa el a felhasználói felület, mint egy beágyazott WebView-t.

Az alábbi kódot a beágyazott webview beállítás látható:

```csharp
bool useSystemBrowser = UIParent.IsSystemWebviewAvailable();
if (useSystemBrowser)
{
    // A browser with custom tabs is present on device, use system browser
    App.UIParent = new UIParent(Xamarin.Forms.Forms.Context as Activity);
}
else
{
    // A browser with custom tabs is not present on device, use embedded webview
    App.UIParent = new UIParent(Xamarin.Forms.Forms.Context as Activity, true);
}

// Alternative:
App.UIParent = new UIParent(Xamarin.Forms.Forms.Context as Activity, !useSystemBrowser);

```

## <a name="net-core-does-not-support-interactive-authentication"></a>.NET core nem támogatja az interaktív hitelesítéshez

A .NET Core tokenek megszerzésével interaktívan nem érhető el. Sőt a .NET Core nem biztosít felhasználói felület még. Adja meg interaktív bejelentkezési egy .NET Core-alkalmazást szeretne, ha, lehetővé teszi az alkalmazás jelenik meg a felhasználónak egy kódot, és jelentkezzen be interaktívan Ugrás URL-címe (lásd: [eszköz kód Flow](msal-authentication-flows.md#device-code)).