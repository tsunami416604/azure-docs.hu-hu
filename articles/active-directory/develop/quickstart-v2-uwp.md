---
title: A Microsoft identity platform Windows UWP rövid |} Az Azure
description: Ismerje meg, hogyan egy univerzális Windows Platform (XAML) alkalmazás hozzáférési jogkivonatot kapjon és egy API-t a Microsoft identity platform végpont által védett.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c61da6a55b1f4502deee056b29fdbc22ef33514
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59490618"
---
# <a name="quickstart-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Gyors útmutató: A Microsoft Graph API meghívása Univerzális Windows-platform- (UWP-) alkalmazásból

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

A jelen rövid útmutatóban szereplő kódmintán azt mutatjuk be, hogyan tud egy Univerzális Windows-platform- (UWP-) alkalmazás felhasználókat bejelentkeztetni személyes, munkahelyi vagy iskolai fiókokkal, lekérni egy hozzáférési jogkivonatot, és meghívni a Microsoft Graph API-t.

![Ez a rövid útmutató által létrehozott mintaalkalmazás működését mutatja](media/quickstart-v2-uwp/uwp-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>A rövid útmutató mintaalkalmazásának regisztrálása és letöltése
> [!div renderon="docs" class="sxs-lookup"]
> A rövid útmutató mintaalkalmazását kétféleképpen indíthatja el:
> * [Express] [1. lehetőség: Regisztráció és az automatikus állítsa be alkalmazását, és töltse le a kódminta](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuális] [2. lehetőség: Regisztráljon, és manuális konfigurálása az alkalmazás és a kód a minta](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1: Regisztráció és az automatikus állítsa be alkalmazását, és töltse le a kódminta
>
> 1. Nyissa meg az új [az Azure portal - alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/UwpQuickstartPage/sourceType/docs) ablaktáblán.
> 1. Adja meg az alkalmazás nevét, és kattintson a **Regisztráció** elemre.
> 1. Kövesse az új alkalmazás egy kattintással való letöltésére és automatikus konfigurálására vonatkozó utasításokat.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2: Regisztráljon, és manuális konfigurálása az alkalmazás és a kód a minta
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
> Az alkalmazás regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való hozzáadásához kövesse az alábbi lépéseket:
> 1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
> 1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
> 1. Keresse meg a fejlesztők a Microsoft identity platform [alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapot.
> 1. Válassza ki **új regisztrációs**.
> 1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:
>      - A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `UWP-App-calling-MsGraph`).
>      - A **Támogatott fióktípusok** szakaszban jelölje be a **Tetszőleges szervezeti címtárban található fiókok és a Személyes Microsoft-fiókok (például Skype, Xbox, Outlook.com)** beállítást.
>      - Válassza a **Regisztráció** elemet az alkalmazás létrehozásához.
> 1. Az alkalmazás oldalainak listájában válassza a **Hitelesítés** elemet.
> 1. Az **Átirányítási URI-k** szakaszban keresse meg a **Javasolt átirányítási URI-k nyilvános ügyfelek számára (mobil, asztali)** szakaszt, és válassza az **urn: ietf:wg:oauth:2.0:oob** címet.
> 1. Kattintson a **Mentés** gombra.

> [!div renderon="portal" class="sxs-lookup alert alert-info"]
> #### <a name="step-1-configure-your-application"></a>1. lépés: Az alkalmazás konfigurálása
> Ahhoz, hogy a rövid útmutató kódmintája működjön, hozzá kell adnia egy átirányítási URI-t a következő formában: **urn:ietf:wg:oauth:2.0:oob**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítás végrehajtása nekem]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már be van állítva](media/quickstart-v2-uwp/green-check.png) az alkalmazás ezekkel az attribútumokkal van konfigurálva.

#### <a name="step-2-download-your-visual-studio-project"></a>2. lépés: A Visual Studio-projekt letöltése

 - [A Visual Studio 2017-projekt letöltése](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>3. lépés: A Visual Studio-projekt konfigurálása

1. Csomagolja ki a zip-fájlt egy helyi mappába a lemez gyökerének közelében (például: **C:\Azure-Samples**).
1. Nyissa meg a projektet a Visual Studióban. A rendszer kérheti egy UWP-SDK telepítése. Ebben az esetben elfogadja.
1. Szerkesztés **MainPage.Xaml.cs** , és cserélje le az értékeket, a `ClientId` mező:

    ```csharp
    private const string ClientId = "Enter_the_Application_Id_here";
    ```

> [!div renderon="docs"]
> Az elemek magyarázata:
> - `Enter_the_Application_Id_here` – a regisztrált alkalmazás alkalmazásazonosítója.
>
> > [!TIP]
> > Az értékek azonosításához *Alkalmazásazonosító*, lépjen a a **áttekintése** lap

#### <a name="step-4-run-your-application"></a>4. lépés: Az alkalmazás futtatása

Ha szeretné kipróbálni a rövid útmutató a Windows-gépen:

1. A Visual Studio eszköztárában válassza a megfelelő platform (valószínűleg **x64** vagy **x86**, nem ARM).
   > Figyelje meg, hogy az eszköznek a változik *eszköz* való *helyi számítógépre*
1. Válassza ki a hibakeresési |} **Indítás hibakeresés nélkül**

## <a name="more-information"></a>További információ

Ez a szakasz a rövid útmutatóról nyújt további információkat.

### <a name="msalnet"></a>MSAL.NET

Az MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) segítségével a felhasználók, és biztonsági jogkivonatokat kérhetnek a könyvtár. A biztonsági jogkivonatokat a fejlesztők a Microsoft Identity platform által védett API-k elérésére szolgálnak. Az MSAL telepítéséhez futtassa a következő parancsot a Visual Studio *Package Manager konzolján*:

```powershell
Install-Package Microsoft.Identity.Client -Pre
```

### <a name="msal-initialization"></a>Az MSAL inicializálása

Az MSAL-re mutató hivatkozás hozzáadásához adja hozzá az alábbi kódot:

```csharp
using Microsoft.Identity.Client;
```

Ezután inicializálja az MSAL-t az alábbi kóddal:

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApp = new PublicClientApplicationBuilder.Create(ClientId)
                                                    .Build();
```

> |Az elemek magyarázata: ||
> |---------|---------|
> | `ClientId` | Az Azure Portalon regisztrált alkalmazás **alkalmazásazonosítója (ügyfél-azonosítója)**. Ezt az értéket az alkalmazás **Áttekintés** oldalán találja az Azure Portalon. |

### <a name="requesting-tokens"></a>Jogkivonatok lekérése

Az MSAL segítségével, interaktívan-jogkivonatok beszerzésének két módszer van: `AcquireTokenInteractive` és `AcquireTokenSilent`.

#### <a name="get-a-user-token-interactively"></a>Felhasználói jogkivonat interaktív lekérése

Bizonyos helyzetekben szükséges a Microsoft identity platform végpont egy felugró ablakban vagy a saját fiók hitelesítő adatainak érvényesítéséhez, vagy beleegyezés keresztül a kényszerítési felhasználók használják. Néhány példa:

- Az első alkalommal jelentkeznek be az alkalmazás
- Ha a felhasználóknak újból meg kell adniuk a hitelesítési adataikat, mert lejárt a jelszó
- Ha az alkalmazás, amely a felhasználónak van szüksége, hogy engedélyt adjanak az erőforrásokhoz való hozzáférést
- Ha kétfaktoros hitelesítésre van szükség

```csharp
authResult = await App.PublicClientApp.AcquireToken(scopes, this)
                       .ExecuteAsync();
```

> |Az elemek magyarázata:||
> |---------|---------|
> | `scopes` | Tartalmazza a kért hatóköröket (például `{ "user.read" }` a Microsoft Graph és `{ "api://<Application ID>/access_as_user" }` az egyéni webes API-k esetében). |
> | `this`| A WPF ablakot, amely a bejelentkezési párbeszédpanel center történik jelöli

#### <a name="get-a-user-token-silently"></a>Felhasználói jogkivonat csendes beszerzése

Nem ajánlott minden egyes alkalommal megkövetelni a felhasználóktól a hitelesítő adatok érvényesítését, amikor hozzá kell férniük egy erőforráshoz. Általában szerencsésebb, ha a jogkivonatok beszerzéséhez és megújításához nincs szükség felhasználói beavatkozásra. Kezdetben használja az `AcquireTokenAsync` metódust, majd a védett erőforrásokhoz való hozzáféréshez szükséges jogkivonatok beszerzéséhez az `AcquireTokenSilent` metódust használhatja:

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

> |Az elemek magyarázata: ||
> |---------|---------|
> | `scopes` | Tartalmazza a kért hatóköröket (például `{ "user.read" }` a Microsoft Graph és `{ "api://<Application ID>/access_as_user" }` az egyéni webes API-k esetében). |
> | `firstAccount` | Első felhasználói fiókot határozza meg a gyorsítótárban (MSAL támogatja a több felhasználó ugyanazon alkalmazásban) |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>További lépések

Próbálja ki az asztali Windowshoz készült oktatóanyagot, amelyben teljes körű, részletes útmutatót talál az alkalmazások és új szolgáltatások létrehozásához, valamint megtalálja ennek a rövid útmutatónak a teljes magyarázatát is.

> [!div class="nextstepaction"]
> [UWP - hívás Graph API-oktatóanyag](tutorial-v2-windows-uwp.md)
