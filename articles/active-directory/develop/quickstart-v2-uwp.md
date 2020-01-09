---
title: Microsoft Identity platform – Windows UWP gyors üzembe helyezési útmutató | Azure
description: Ismerje meg, hogyan szerezhet be egy Univerzális Windows-platform (XAML) alkalmazás hozzáférési jogkivonatot, és hogyan hívhat meg a Microsoft Identity platform-végpont által védett API-t.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:UWP
ms.collection: M365-identity-device-management
ms.openlocfilehash: c29aadfde6c0ab01b2e6ed2c9e4bcc59572bef57
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424015"
---
# <a name="quickstart-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Rövid útmutató: A Microsoft Graph API meghívása Univerzális Windows-platform- (UWP-) alkalmazásból

Ez a rövid útmutató egy olyan kódrészletet tartalmaz, amely bemutatja, hogyan jelentkezhet be egy Univerzális Windows-platform (UWP) alkalmazás személyes fiókkal, munkahelyi és iskolai fiókkal, hozzáférési jogkivonat beszerzésével és a Microsoft Graph API meghívásával.

![Bemutatja, hogyan működik a rövid útmutatóban létrehozott minta alkalmazás](media/quickstart-v2-uwp/uwp-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>A rövid útmutató mintaalkalmazásának regisztrálása és letöltése
> [!div renderon="docs" class="sxs-lookup"]
> A rövid útmutató mintaalkalmazását kétféleképpen indíthatja el:
> * [Expressz] [1. lehetőség: Az alkalmazás regisztrálása és automatikus konfigurálása, majd a kódminta letöltése](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Kézi] [2. lehetőség: Az alkalmazás és a kódminta regisztrálása és kézi konfigurálása](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>1\. lehetőség: Az alkalmazás regisztrálása és automatikus konfigurálása, majd a kódminta letöltése
>
> 1. Nyissa meg az új [Azure Portal-Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/UwpQuickstartPage/sourceType/docs) ablaktáblát.
> 1. Adja meg az alkalmazás nevét, és kattintson a **Regisztráció** elemre.
> 1. Kövesse az új alkalmazás egy kattintással való letöltésére és automatikus konfigurálására vonatkozó utasításokat.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>2\. lehetőség: Az alkalmazás és a kódminta regisztrálása és kézi konfigurálása
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>1\. lépés: Alkalmazás regisztrálása
> Az alkalmazás regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való hozzáadásához kövesse az alábbi lépéseket:
> 1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
> 1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
> 1. Navigáljon a Microsoft Identity platform for Developers [Alkalmazásregisztrációk](https://aka.ms/MobileAppReg) oldalára.
> 1. Válassza az **új regisztráció**lehetőséget.
> 1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:
>      - A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `UWP-App-calling-MsGraph`).
>      - A **Támogatott fióktípusok** szakaszban jelölje be a **Tetszőleges szervezeti címtárban található fiókok és a Személyes Microsoft-fiókok (például Skype, Xbox, Outlook.com)** beállítást.
>      - Válassza a **Regisztráció** elemet az alkalmazás létrehozásához.
> 1. Az alkalmazás oldalainak listájában válassza a **Hitelesítés** elemet.
> 1. Az **átirányítási URI** - **k | a nyilvános ügyfelek számára javasolt átirányítási URI-k (mobil, asztali)** szakaszban keresse meg a **https://login.microsoftonline.com/common/oauth2/nativeclient** .
> 1. Kattintson a **Mentés** gombra.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>1\. lépés: Az alkalmazás konfigurálása
> Ahhoz, hogy a rövid útmutatóhoz tartozó mintakód működjön, **https://login.microsoftonline.com/common/oauth2/nativeclient ként** hozzá kell adnia egy ÁTirányítási URI-t.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítás alkalmazása]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-uwp/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-your-visual-studio-project"></a>2\. lépés: A Visual Studio-projekt letöltése

 - [A Visual Studio-projekt letöltése](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>3\. lépés: A Visual Studio-projekt konfigurálása

1. Csomagolja ki a zip-fájlt egy helyi mappába a lemez gyökerének közelében (például: **C:\Azure-Samples**).
1. Nyissa meg a projektet a Visual Studióban. Előfordulhat, hogy a rendszer a UWP SDK telepítését kéri. Ebben az esetben fogadja el.
1. Szerkessze a **MainPage.XAML.cs** , és cserélje le a `ClientId` mező értékét:

    ```csharp
    private const string ClientId = "Enter_the_Application_Id_here";
    ```
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Ez a rövid útmutató támogatja a Enter_the_Supported_Account_Info_Here.    

> [!div renderon="docs"]
> Helyszín:
> - `Enter_the_Application_Id_here` – ez a regisztrált alkalmazás alkalmazásazonosítója.
>
> > [!TIP]
> > Az *alkalmazás-azonosító*értékének megkereséséhez nyissa meg a portál **Áttekintés** szakaszát.

#### <a name="step-4-run-your-application"></a>4\. lépés: az alkalmazás futtatása

Ha a Windowsos gépen szeretné kipróbálni a gyors útmutatót:

1. A Visual Studio eszköztárán válassza ki a megfelelő platformot (valószínűleg **x64** vagy **x86**, nem ARM).
   > Figyelje meg, hogy a cél eszköz az *eszközről* a *helyi gépre* változik
1. Hibakeresés kiválasztása | **Indítás hibakeresés nélkül**

## <a name="more-information"></a>További információ

Ez a szakasz a rövid útmutatóról nyújt további információkat.

### <a name="msalnet"></a>MSAL.NET

A MSAL ([Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) a felhasználók bejelentkezésére és biztonsági jogkivonatok igénylésére használt könyvtár. A biztonsági jogkivonatok a Microsoft Identity platform által védett API-k elérésére használhatók a fejlesztők számára. Az MSAL telepítéséhez futtassa a következő parancsot a Visual Studio *Package Manager konzolján*:

```powershell
Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="msal-initialization"></a>Az MSAL inicializálása

Az MSAL-re mutató hivatkozás hozzáadásához adja hozzá az alábbi kódot:

```csharp
using Microsoft.Identity.Client;
```

Ezt követően a MSAL inicializálása a következő kód használatával történik:

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                                                    .Build();
```

> |Helyszín: ||
> |---------|---------|
> | `ClientId` | Az Azure Portalon regisztrált alkalmazás **alkalmazásazonosítója (ügyfél-azonosítója)** . Ezt az értéket az alkalmazás **Áttekintés** oldalán találja az Azure Portalon. |

### <a name="requesting-tokens"></a>Jogkivonatok lekérése

A MSAL két módszerrel szerezheti be a jogkivonatokat egy UWP-alkalmazásban: `AcquireTokenInteractive` és `AcquireTokenSilent`.

#### <a name="get-a-user-token-interactively"></a>Felhasználói jogkivonat interaktív lekérése

Bizonyos helyzetekben a felhasználóknak fel kell kényszeríteni a Microsoft Identity platform-végponttal való interakciót egy előugró ablakban a hitelesítő adatok érvényesítéséhez vagy a jóváhagyáshoz. Néhány példa:

- Az első alkalommal bejelentkezett felhasználók bejelentkeznek az alkalmazásba
- Ha a felhasználóknak újból meg kell adniuk a hitelesítési adataikat, mert lejárt a jelszó
- Ha az alkalmazás hozzáférést kér egy erőforráshoz, a felhasználónak hozzá kell járulnia
- Ha kétfaktoros hitelesítésre van szükség

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(scopes)
                      .ExecuteAsync();
```

> |Helyszín:||
> |---------|---------|
> | `scopes` | Tartalmazza a kért hatóköröket (például `{ "user.read" }` a Microsoft Graph és `{ "api://<Application ID>/access_as_user" }` az egyéni webes API-k esetében). |

#### <a name="get-a-user-token-silently"></a>Felhasználói jogkivonat csendes beszerzése

A `AcquireTokenSilent` metódussal szerezhet be jogkivonatokat a védett erőforrásokhoz való hozzáféréshez a kezdeti `AcquireTokenInteractive` metódus után. Nem szeretné megkövetelni, hogy a felhasználó minden alkalommal érvényesítse a hitelesítő adataikat, amikor hozzá kell férniük egy erőforráshoz. A tokenek beszerzését és megújítását a legtöbb esetben felhasználói beavatkozás nélkül kell használni

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

> |Helyszín: ||
> |---------|---------|
> | `scopes` | Tartalmazza a kért hatóköröket (például `{ "user.read" }` a Microsoft Graph és `{ "api://<Application ID>/access_as_user" }` az egyéni webes API-k esetében). |
> | `firstAccount` | Megadja az első felhasználói fiókot a gyorsítótárban (a MSAL több felhasználót is támogat egyetlen alkalmazásban) |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Következő lépések

Próbálja ki az asztali Windowshoz készült oktatóanyagot, amelyben teljes körű, részletes útmutatót talál az alkalmazások és új szolgáltatások létrehozásához, valamint megtalálja ennek a rövid útmutatónak a teljes magyarázatát is.

> [!div class="nextstepaction"]
> [UWP: A Graph API meghívása – oktatóanyag](tutorial-v2-windows-uwp.md)

Segítsen nekünk a Microsoft Identity platform fejlesztésében. Mondja el, mit gondol egy rövid, kétkérdéses felmérés végrehajtásával.

> [!div class="nextstepaction"]
> [Microsoft Identity platform-felmérés](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
