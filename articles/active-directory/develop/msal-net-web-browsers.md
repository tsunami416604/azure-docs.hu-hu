---
title: Webböngészők használata (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, milyen szempontok at a Xamarin Android használatával kapcsolatos, a Microsoft Authentication Library for .NET (MSAL.NET) használatával kapcsolatban.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: ed1f47ae99f6346a932d0fe94be7586dc25a672f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262736"
---
# <a name="using-web-browsers-msalnet"></a>Webböngészők használata (MSAL.NET)

Az interaktív hitelesítéshez webböngészők szükségesek. Alapértelmezés szerint a MSAL.NET támogatja a [rendszer böngészőjét](#system-web-browser-on-xamarinios-xamarinandroid) xamarin.iOS és Xamarin.Android rendszeren. De [engedélyezheti a beágyazott webböngészőt a](#enable-embedded-webviews-on-ios-and-android) [Xamarin.iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) és [xamarin.Android](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) alkalmazások követelményeitől függően (UX, egyszeri bejelentkezés (SSO), biztonság) is. És akkor is [választhat dinamikusan](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) melyik böngészőt használni jelenléte alapján a Chrome vagy a böngésző támogatja a Chrome egyéni lapok Android. MSAL.NET csak a .NET Core asztali alkalmazások rendszerböngészőjét támogatja.

## <a name="web-browsers-in-msalnet"></a>Webböngészők a MSAL.NET

### <a name="interaction-happens-in-a-web-browser"></a>Az interakció webböngészőben történik

Fontos megérteni, hogy a jogkivonat interaktív beolvasásakor a párbeszédpanel tartalmát nem a könyvtár, hanem az STS (Security Token Service) biztosítja. A hitelesítési végpont visszaküld néhány HTML-t és JavaScriptet, amelyek a kapcsolati tevékenységet vezérelik, ami webböngészőben vagy webvezérlőben jelenik meg. Annak lehetővé tétele, hogy az STS kezelni tudja a HTML-interakciót, számos előnnyel jár:

- A jelszót (ha beírták) az alkalmazás, sem a hitelesítési könyvtár soha nem tárolja.
- Lehetővé teszi, hogy más identitásszolgáltatók (például a bejelentkezés egy munkahelyi iskolai fiókkal vagy egy személyes fiók MSAL, vagy egy közösségi fiók az Azure AD B2C).
- Lehetővé teszi, hogy az STS vezérelje a feltételes hozzáférést, például azáltal, hogy a felhasználó többtényezős hitelesítést (MFA) végez a hitelesítési fázisban (Windows Hello pin beírása, vagy a telefonján vagy a telefonján lévő hitelesítési alkalmazáson keresztül). Azokban az esetekben, amikor a szükséges többtényezős hitelesítés még nincs beállítva, a felhasználó beállíthatja azt éppen időben ugyanabban a párbeszédablakban.  A felhasználó megadja a mobiltelefonszámát, és egy hitelesítési alkalmazás telepítésére és egy QR-címke beírására irányítja a fiók hozzáadásához. Ez a szerver vezérelt interakció egy nagy élmény!
- Lehetővé teszi, hogy a felhasználó módosítsa a jelszavát ugyanebben a párbeszédpanelen, amikor a jelszó lejárt (további mezőket biztosítva a régi és az új jelszóhoz).
- Engedélyezi a bérlő márkajelzését, vagy az Azure AD-bérlői rendszergazda / alkalmazás tulajdonosa által vezérelt alkalmazást (lemezképeket).
- Lehetővé teszi a felhasználók számára, hogy hozzájáruljon az alkalmazás erőforrások / hatókörök a nevükben közvetlenül a hitelesítés után.

### <a name="embedded-vs-system-web-ui"></a>Beágyazott vs Rendszer Web UI

MSAL.NET egy többkeretes könyvtár, és keretrendszer-specifikus kóddal rendelkezik, amely egy böngészőt üzemeltet a felhasználói felület vezérlőjében (például a .Net Classic-on WinForms- et, xamarin-on natív mobilvezérlőket használ stb.). Ezt a `embedded` vezérlőt webes felhasználói felületnek nevezzük. Alternatív megoldásként, MSAL.NET is képes kick off a rendszer operációs rendszer böngésző.

Általában ajánlott a platform alapértelmezett használata, és ez általában a rendszerböngésző. A rendszer böngésző jobban emlékszik a felhasználók, akik bejelentkezett előtt. Ha módosítania kell ezt a viselkedést, használja a`WithUseEmbeddedWebView(bool)`

### <a name="at-a-glance"></a>Első pillantásra

| Keretrendszer        | Beágyazott | Rendszer | Alapértelmezett |
| ------------- |-------------| -----| ----- |
| .NET Klasszikus     | Igen | Igen^ | Beágyazott |
| .NET Core     | Nem | Igen^ | Rendszer |
| .NET Standard | Nem | Igen^ | Rendszer |
| UWP | Igen | Nem | Beágyazott |
| Xamarin.Android | Igen | Igen  | Rendszer |
| Xamarin.iOS | Igen | Igen  | Rendszer |
| Xamarin.Mac| Igen | Nem | Beágyazott |

^ "http://localhost" " átirányításURI-t igényel

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Rendszer böngésző xamarin.iOS, Xamarin.Android

Alapértelmezés szerint a MSAL.NET támogatja a rendszer webböngészőjét a Xamarin.iOS, a Xamarin.Android és a .NET Core rendszeren. A felhasználói felületet biztosító összes platform (azaz a .NET Core) esetében a webböngésző-vezérlőt beágyazó tár egy párbeszédpanelt biztosít. MSAL.NET a .NET Desktop és a WAB beépített webnézetét is használja az UWP platformhoz. Azonban alapértelmezés szerint kihasználja a Xamarin iOS és Xamarin Android alkalmazások **rendszerböngészőjét.** IOS rendszeren még az operációs rendszer verziójától (iOS12, iOS11 és korábbi) függően is kiválasztja a webes nézet használatát.

A rendszer böngésző jegeli a jelentős előnye, hogy megosztja az SSO állam más alkalmazások és webes alkalmazások nélkül kell egy bróker (Cég portál / Hitelesítő). A rendszer böngészőhasználták, alapértelmezés szerint, MSAL.NET a Xamarin iOS és Xamarin Android platformok, mert ezeken a platformokon, a rendszer böngésző elfoglalja az egész képernyőt, és a felhasználói élmény jobb. A rendszer webes nézete nem különböztethető meg a párbeszédektől. Az iOS-en azonban előfordulhat, hogy a felhasználónak beleegyezését kell adnia ahhoz, hogy a böngésző visszahívja az alkalmazást, ami bosszantó lehet.

## <a name="system-browser-experience-on-net-core"></a>Rendszerböngésző-élmény a .NET Core rendszeren

A .NET Core rendszerben MSAL.NET külön folyamatként indítja el a rendszerböngészőt. MSAL.NET nem rendelkezik a böngésző felett, de amint a felhasználó befejezi a hitelesítést, a weboldal átirányításra kerül oly módon, hogy MSAL.NET elfogja az Uri-t.

A .NET Classic számára írt alkalmazásokat is beállíthatja a böngésző használatára, ha megadja a

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET nem tudja észlelni, ha a felhasználó elnavigál, vagy egyszerűen bezárja a böngészőt. Az ezzel a technikával készült alkalmazásokat `CancellationToken`arra ösztönzik, hogy időhosszabbítást határozzanak meg (via). Azt javasoljuk, hogy legalább néhány perces időtúllépéseket, hogy figyelembe vegye az eseteket, amikor a felhasználó kéri, hogy változtassa meg a jelszót, vagy többtényezős hitelesítést.

### <a name="how-to-use-the-default-os-browser"></a>Az alapértelmezett operációsrendszer-böngésző használata

MSAL.NET kell hallgatni, `http://localhost:port` és elfogja a kódot, hogy AAD küld, ha a felhasználó befejezte a hitelesítés (lásd az engedélyezési [kódot](v2-oauth2-auth-code-flow.md) a részletekért)

A rendszerböngésző engedélyezése:

1. Az alkalmazás regisztrációja során konfiguráljon `http://localhost` átirányítási uri-ként (a B2C jelenleg nem támogatja)
2. A PublicClientApplication összeállításakor adja meg ezt az átirányítási uri-t:

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> Ha konfigurálja `http://localhost`a , belsőleg MSAL.NET véletlenszerűen nyitott portot talál, és használja azt.

### <a name="linux-and-mac"></a>Linux és MAC

Linux on, MSAL.NET megnyitja az alapértelmezett operációs rendszer böngésző segítségével xdg-open eszköz. A hibaelhárításhoz futtassa az eszközt egy terminálról, például`xdg-open "https://www.bing.com"`  
Mac en a böngésző ta-`open <url>`

### <a name="customizing-the-experience"></a>Az élmény testreszabása

> [!NOTE]
> A testreszabás MSAL.NET 4.1.0-s vagy újabb verzióban érhető el.

MSAL.NET képes válaszolni egy HTTP-üzenettel, amikor egy token érkezik, vagy hiba esetén. Megjeleníthet egy HTML-üzenetet, vagy átirányíthatja az Ön által választott URL-re:

```csharp
var options = new SystemWebViewOptions() 
{
    HtmlMessageError = "<p> An error occured: {0}. Details {1}</p>",
    BrowserRedirectSuccess = new Uri("https://www.microsoft.com");
}

await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
         .WithSystemWebViewOptions(options)
         .ExecuteAsync();
```

### <a name="opening-a-specific-browser-experimental"></a>Adott böngésző megnyitása (Kísérleti)

Testreszabhatja a böngésző MSAL.NET megnyitása módját. Például ahelyett, hogy bármilyen böngészőt használna az alapértelmezett, kényszeríthetegy adott böngésző megnyitását:

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>Az UWP nem használja a System Webview nézetet

Asztali alkalmazások esetén azonban a Rendszer webnézet elindítása subpar felhasználói élményt nyújt, mivel a felhasználó látja a böngészőt, ahol előfordulhat, hogy más lapok is meg vannak nyitva. És amikor a hitelesítés megtörtént, a felhasználók kap egy oldalt, amely arra kéri őket, hogy zárják be ezt az ablakot. Ha a felhasználó nem figyel, bezárhatja a teljes folyamatot (beleértve a hitelesítéshez nem kapcsolódó egyéb lapokat is). Kihasználva a rendszer böngésző asztali is szükség van a helyi portok megnyitása és figyelése őket, ami szükség lehet speciális engedélyeket az alkalmazás. Ön, mint fejlesztő, felhasználó vagy rendszergazda, lehet, hogy vonakodik ettől a követelménytől.

## <a name="enable-embedded-webviews-on-ios-and-android"></a>Beágyazott webnézetek engedélyezése iOS és Android rendszeren

A Xamarin.iOS és a Xamarin.Android alkalmazásokban is engedélyezheti a beágyazott webnézeteket. A MSAL.NET 2.0.0-előzetes verziótól kezdve MSAL.NET is támogatja a **beágyazott** webmegtekintési lehetőség használatát. A ADAL.NET esetében a beágyazott webmegtekintés az egyetlen támogatott beállítás.

A Xamarint célzó MSAL.NET fejlesztőként dönthet úgy, hogy beágyazott webmegtekintéseket vagy rendszerböngészőket használ. Ez a felhasználó által választott, a megcélozni kívánt felhasználói élménytől és biztonsági aggályoktól függően.

Jelenleg MSAL.NET még nem támogatja az Android és az iOS brókereket. Ezért ha egyszeri bejelentkezést (SSO) kell megadnia, a rendszerböngésző továbbra is jobb megoldás lehet. Támogató brókerek a beágyazott böngésző van a MSAL.NET hátralék.

### <a name="differences-between-embedded-webview-and-system-browser"></a>A beágyazott webnézet és a rendszerböngésző közötti különbségek
Van néhány vizuális különbség a beágyazott webview és a rendszerböngésző között MSAL.NET.

**Interaktív bejelentkezés MSAL.NET a Beágyazott webmegtekintés használatával:**

![beágyazva](media/msal-net-web-browsers/embedded-webview.png)

**Interaktív bejelentkezés a MSAL.NET a rendszerböngészővel:**

![Rendszerböngésző](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Fejlesztői beállítások

A MSAL.NET használó fejlesztőként számos lehetősége van az STS interaktív párbeszédpanelének megjelenítésére:

- **Rendszerböngésző.** A rendszerböngésző alapértelmezés szerint be van állítva a tárban. Ha Androidot használ, olvassa el a [rendszerböngészőket,](msal-net-system-browser-android-considerations.md) ahol konkrét információkat talál arról, hogy mely böngészők támogatottak a hitelesítéshez. Ha androidos rendszerböngészőt használ, javasoljuk, hogy az eszköz olyan böngészővel rendelkezik, amely támogatja a Chrome egyéni lapjait.  Ellenkező esetben a hitelesítés sikertelen lehet.
- **Beágyazott webview.** Ha csak beágyazott webmegtekintést `AcquireTokenInteractively` szeretne használni MSAL.NET, a paraméterek készítője tartalmaz egy metódust. `WithUseEmbeddedWebView()`

    iOS

    ```csharp
    AuthenticationResult authResult;
    authResult = app.AcquireTokenInteractively(scopes)
                    .WithUseEmbeddedWebView(useEmbeddedWebview)
                    .ExecuteAsync();
    ```

    Android::

    ```csharp
    authResult = app.AcquireTokenInteractively(scopes)
                .WithParentActivityOrWindow(activity)
                .WithUseEmbeddedWebView(useEmbeddedWebview)
                .ExecuteAsync();
    ```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Választás a beágyazott webböngésző vagy a rendszerböngésző között a Xamarin.iOS fájlban

Az iOS-alkalmazásban `AppDelegate.cs` inicializálhatja a `ParentWindow` rendszert. `null` Nem használják az iOS-ben

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>A beágyazott webböngésző vagy a rendszerböngésző közötti választás a Xamarin.Android webhelyen

Az Android-alkalmazásban `MainActivity.cs` beállíthatja a szülőtevékenységet, így a hitelesítési eredmény visszatérhet hozzá:

```csharp
 App.ParentWindow = this;
```

Aztán `MainPage.xaml.cs`a:

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Az egyéni lapok jelenlétének észlelése a Xamarin.Android-on

Ha azt szeretné, hogy a rendszer böngésző, hogy sso az alkalmazások futnak a böngészőben, de aggódik a felhasználói élményt az Android `IsSystemWebViewAvailable()` eszközök `IPublicClientApplication`nem rendelkeznek a böngésző egyéni lap támogatás, akkor a lehetőséget, hogy úgy dönt, hívja a metódust . Ez a `true` módszer akkor ad vissza, `false` ha a PackageManager egyéni lapokat észlel, és ha azok nem észlelhetők az eszközön.

Az ezzel a módszerrel visszaadott érték és a követelmények alapján a következő döntéseket hozhatja:

- Egyéni hibaüzenetet küldhet vissza a felhasználónak. Például: "Telepítse a Chrome-ot a hitelesítés folytatásához" -OR-
- Visszatérhet a beágyazott webmegtekintési lehetőséghez, és beágyazott webnézetként elindíthatja a felhasználói felületet.

Az alábbi kód a beágyazott webmegtekintési lehetőséget mutatja:

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>A .NET Core nem támogatja a beágyazott böngészővel való interaktív hitelesítést

A .NET Core esetében a tokenek interaktív beszerzése csak a rendszer webböngészőjében érhető el, beágyazott webes nézetekkel nem. Sőt, a .NET Core még nem biztosít felhasználói felületet.
Ha szeretné testre szabni a böngészési élményt a rendszer böngésző, akkor végre az [IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui) felület, és még a saját böngésző.
