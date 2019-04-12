---
title: A Microsoft identity platform Windows asztali rövid |} A Microsoft Docs
description: Ismerje meg, hogyan Windows asztali .NET (XAML) alkalmazás hozzáférési jogkivonatot kapjon és egy API-t a Microsoft identity platform végpont által védett
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
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95e5124d466c4294d83bbfa0b7ca15ff6f98e9ec
ms.sourcegitcommit: f24b62e352e0512dfa2897362021b42e0cb9549d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59505381"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-windows-desktop-app"></a>Gyors útmutató: Egy token beszerzéséhez és a Microsoft Graph API hívása Windows asztali alkalmazások

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Ebből a rövid útmutatóból megtudhatja, hogyan írhat egy Windows asztali .NET- (WPF-) alkalmazást, amely be tud jelentkezni személyes, munkahelyi és iskolai fiókokba, le tud kérni egy hozzáférési jogkivonatot, majd meg tudja hívni a Microsoft Graph API-t.

![Ez a rövid útmutató által létrehozott mintaalkalmazás működését mutatja](media/quickstart-v2-windows-desktop/windesktop-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>A rövid útmutató mintaalkalmazásának regisztrálása és letöltése
> A rövid útmutató mintaalkalmazását kétféleképpen indíthatja el:
> * [Express] [1. lehetőség: Regisztráció és az automatikus állítsa be alkalmazását, és töltse le a kódminta](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuális] [2. lehetőség: Regisztráljon, és manuális konfigurálása az alkalmazás és a kód a minta](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1: Regisztráció és az automatikus állítsa be alkalmazását, és töltse le a kódminta
>
> 1. Nyissa meg az új [az Azure portal - alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs).
> 1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
> 1. Kövesse az új alkalmazás egy kattintással való letöltésére és automatikus konfigurálására vonatkozó utasításokat.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2: Regisztráljon, és manuális konfigurálása az alkalmazás és a kód a minta
>
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
> Az alkalmazás regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való kézi hozzáadásához kövesse az alábbi lépéseket:
>
> 1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
> 1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
> 1. Keresse meg a fejlesztők a Microsoft identity platform [alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapot.
> 1. Válassza ki **új regisztrációs**.
>      - A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `Win-App-calling-MsGraph`).
>      - A **Támogatott fióktípusok** szakaszban jelölje be a **Tetszőleges szervezeti címtárban található fiókok és a Személyes Microsoft-fiókok (például Skype, Xbox, Outlook.com)** beállítást.
>      - Válassza a **Regisztráció** elemet az alkalmazás létrehozásához.
> 1. Az alkalmazás oldalainak listájában válassza a **Hitelesítés** elemet.
> 1. Az **Átirányítási URI-k** szakaszban keresse meg a **Javasolt átirányítási URI-k nyilvános ügyfelek számára (mobil, asztali)** szakaszt, és válassza az **urn: ietf:wg:oauth:2.0:oob** címet.
> 1. Kattintson a **Mentés** gombra.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>1. lépés: Az alkalmazás konfigurálása az Azure Portalon
> Ahhoz, hogy a rövid útmutató kódmintája működjön, hozzá kell adnia egy válasz URL-t a következő formában: **urn:ietf:wg:oauth:2.0:oob**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítás végrehajtása nekem]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már be van állítva](media/quickstart-v2-windows-desktop/green-check.png) az alkalmazás ezekkel az attribútumokkal van konfigurálva.

#### <a name="step-2-download-your-visual-studio-project"></a>2. lépés: A Visual Studio-projekt letöltése

[A Visual Studio 2017-projekt letöltése](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>3. lépés: A Visual Studio-projekt konfigurálása

1. Csomagolja ki a zip-fájlt egy helyi mappába a lemez gyökerének közelében (például: **C:\Azure-Samples**).
1. Nyissa meg a projektet a Visual Studióban.
1. Módosítsa az **App.Xaml.cs** fájlt, és cserélje a `ClientId` és `Tenant` mezők értékét a következő kódra:

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    private static string Tenant = "Enter_the_Tenant_Info_Here";
    ```

> [!div renderon="docs"]
> Az elemek magyarázata:
> - `Enter_the_Application_Id_here` -a a **Alkalmazásazonosítót (ügyfél)** a regisztrált alkalmazás.
> - `Enter_the_Tenant_Info_Here` -értéke a következők egyikét:
>   - Ha az alkalmazás **az adott szervezeti címtárban lévő fiókokat** támogatja, ezt az értéket a **Bérlőazonosítóra** vagy a **Bérlő nevére** cserélje le (például contoso.microsoft.com)
>   - Ha az alkalmazás támogatja a **bármely szervezeti directory fiókok**, cserélje le ezt az értéket `organizations`
>   - Ha az alkalmazás támogatja a **fiókok minden olyan szervezeti directory és személyes Microsoft-fiókok**, cserélje le ezt az értéket `common`
>
> > [!TIP]
> > Az **alkalmazás (ügyfél) azonosítója**, a **címtár (bérlő) azonosítója** és a **támogatott fióktípusok** értékét az alkalmazás **Áttekintés** oldalán találja az Azure Portalon.

## <a name="more-information"></a>További információ

### <a name="msalnet"></a>MSAL.NET

Az MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) segítségével a felhasználók, és egy API-t a Microsoft identity platform által védett eléréséhez használt jogkivonatokat kérhetnek a könyvtár. Az MSAL telepítéséhez futtassa a következő parancsot a Visual Studio **Package Manager konzolján**:

```powershell
Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="msal-initialization"></a>Az MSAL inicializálása

Az MSAL-re mutató hivatkozás hozzáadásához adja hozzá az alábbi kódot:

```csharp
using Microsoft.Identity.Client;
```

Ezután inicializálja az MSAL-t az alábbi kóddal:

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .Build();
```

> |Az elemek magyarázata: ||
> |---------|---------|
> | `ClientId` | Az Azure Portalon regisztrált alkalmazás **alkalmazásazonosítója (ügyfél-azonosítója)**. Ezt az értéket az alkalmazás **Áttekintés** oldalán találja az Azure Portalon. |

### <a name="requesting-tokens"></a>Jogkivonatok lekérése

Az MSAL a következő két metódust használja a jogkivonatok beszerzéséhez: `AcquireToken` és `AcquireTokenSilent`.

#### <a name="get-a-user-token-interactively"></a>Felhasználói jogkivonat interaktív lekérése

Bizonyos helyzetekben szükséges a Microsoft identity platform végpont egy felugró ablakban vagy a saját fiók hitelesítő adatainak érvényesítéséhez, vagy beleegyezés keresztül a kényszerítési felhasználók használják. Néhány példa:

- Az első alkalommal, amikor felhasználók bejelentkeznek az alkalmazásba
- Ha a felhasználóknak újból meg kell adniuk a hitelesítési adataikat, mert lejárt a jelszó
- Amikor az alkalmazás olyan erőforráshoz kér hozzáférést, amelyhez szükséges a felhasználó hozzájárulása
- Ha kétfaktoros hitelesítésre van szükség

```csharp
authResult = await App.PublicClientApp.AcquireToken(_scopes)
                                      .ExecuteAsync();
```

> |Az elemek magyarázata:||
> |---------|---------|
> | `_scopes` | Tartalmazza a kért hatóköröket (például `{ "user.read" }` a Microsoft Graph és `{ "api://<Application ID>/access_as_user" }` az egyéni webes API-k esetében). |

#### <a name="get-a-user-token-silently"></a>Felhasználói jogkivonat csendes beszerzése

Nem ajánlott minden egyes alkalommal megkövetelni a felhasználóktól a hitelesítő adatok érvényesítését, amikor hozzá kell férniük egy erőforráshoz. Általában szerencsésebb, ha a jogkivonatok beszerzéséhez és megújításához nincs szükség felhasználói beavatkozásra. Kezdetben használja az `AcquireTokenAsync` metódust, majd a védett erőforrásokhoz való hozzáféréshez szükséges jogkivonatok beszerzéséhez az `AcquireTokenSilentAsync` metódust használhatja:

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

> |Az elemek magyarázata: ||
> |---------|---------|
> | `scopes` | Tartalmazza a kért hatóköröket (például `{ "user.read" }` a Microsoft Graph és `{ "api://<Application ID>/access_as_user" }` az egyéni webes API-k esetében). |
> | `firstAccount` | A gyorsítótár első felhasználóját határozza meg (az MSAL több felhasználót támogat egy alkalmazásban). |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>További lépések

Próbálja ki az asztali Windowshoz készült oktatóanyagot, amelyben teljes körű, részletes útmutatót talál az alkalmazások és új szolgáltatások létrehozásához, valamint megtalálja ennek a rövid útmutatónak a teljes magyarázatát is.

> [!div class="nextstepaction"]
> [Hívja a Graph API-oktatóanyag](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-windesktop)

