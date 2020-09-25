---
title: 'Gyors útmutató: token beszerzése & Call Microsoft Graph a konzol alkalmazásban | Azure'
titleSuffix: Microsoft identity platform
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a .NET Core-minta alkalmazás az ügyfél hitelesítő adatait a token beszerzéséhez és a Microsoft Graph meghívásához.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 6f4f4c2de3b1030c4d14cb74e562954a3d3d1144
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91257824"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-using-console-apps-identity"></a>Gyors útmutató: token beszerzése és Microsoft Graph API meghívása a konzol alkalmazás identitásával

Ebből a rövid útmutatóból megtudhatja, hogyan írhat olyan .NET Core-alkalmazást, amely hozzáférési tokent tud beolvasni az alkalmazás saját identitásával, majd meghívja a Microsoft Graph API-t a címtárban lévő [felhasználók listájának](/graph/api/user-list) megjelenítéséhez. Ez a forgatókönyv olyan helyzetekben hasznos, amikor a fej, felügyelet nélküli feladatot vagy egy Windows-szolgáltatást alkalmazás-identitással kell futtatni a felhasználó identitása helyett. (Lásd: [Hogyan működik a minta](#how-the-sample-works) egy ábrán.)

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a [.net Core 2,2](https://www.microsoft.com/net/download/dotnet-core/2.2)szükséges.

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>A rövid útmutató mintaalkalmazásának regisztrálása és letöltése

> [!div renderon="docs" class="sxs-lookup"]
>
> A gyors üzembe helyezési alkalmazás elindításához két lehetőség közül választhat: Express (1. lehetőség) és manuális (2. lehetőség)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>1. lehetőség: Az alkalmazás regisztrálása és automatikus konfigurálása, majd a kódminta letöltése
>
> 1. Nyissa meg az új [Azure Portal-Alkalmazásregisztrációk](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs) ablaktáblát.
> 1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
> 1. Kövesse az új alkalmazás egy kattintással való letöltésére és automatikus konfigurálására vonatkozó utasításokat.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>2. lehetőség: Az alkalmazás és a kódminta regisztrálása és kézi konfigurálása

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
> Az alkalmazás regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való kézi hozzáadásához kövesse az alábbi lépéseket:
>
> 1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
> 1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
> 1. Navigáljon a Microsoft Identity platform for Developers [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) oldalára.
> 1. Válassza az **új regisztráció**lehetőséget.
> 1. Amikor megjelenik az **alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait.
> 1. A **név** szakaszban adjon meg egy értelmezhető nevet, amely megjelenik az alkalmazás felhasználói számára, például a `Daemon-console` **regisztrálás** elemre kattintva hozza létre az alkalmazást.
> 1. A regisztrálás után válassza a **tanúsítványok & titkok** menüt.
> 1. Az **ügyfél**titkos kulcsa területen válassza az **+ új ügyfél titka**lehetőséget. Adja meg a nevet, és válassza a **Hozzáadás**lehetőséget. Másolja a titkos kulcsot egy biztonságos helyre. Szüksége lesz rá a kódban való használatra.
> 1. Most válassza ki az **API-engedélyek** menüt, válassza az **+ engedély hozzáadása** gombot, és válassza a **Microsoft Graph**lehetőséget.
> 1. Válassza ki az **alkalmazás engedélyeit**.
> 1. A **felhasználó** csomópont alatt válassza a **felhasználó. olvasás. mind**, majd az **engedélyek hozzáadása** elemet.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>A Gyorsindítás alkalmazás letöltése és konfigurálása
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>1. lépés: Az alkalmazás konfigurálása az Azure Portalon
> Ahhoz, hogy a rövid útmutatóhoz tartozó mintakód működjön, létre kell hoznia egy ügyfél-titkos kulcsot, és hozzá kell adnia Graph API **felhasználóját. Read. All** Application engedély.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítások elvégzése]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-netcore-daemon/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-your-visual-studio-project"></a>2. lépés: A Visual Studio-projekt letöltése

> [!div renderon="docs"]
> [A Visual Studio-projekt letöltése](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Futtassa a projektet a Visual Studio 2019 használatával.
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [A mintakód letöltése](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>3. lépés: A Visual Studio-projekt konfigurálása
>
> 1. Csomagolja ki a zip-fájlt egy helyi mappába a lemez gyökerének közelében (például: **C:\Azure-Samples**).
> 1. Nyissa meg a megoldást a Visual Studio- **1-Call-MSGraph\daemon-Console.SLN** (nem kötelező).
> 1. Szerkessze **appsettings.js** , és cserélje le a mezők értékeit `ClientId` , `Tenant` és `ClientSecret` a következővel:
>
>    ```json
>    "Tenant": "Enter_the_Tenant_Id_Here",
>    "ClientId": "Enter_the_Application_Id_Here",
>    "ClientSecret": "Enter_the_Client_Secret_Here"
>    ```
>   Ebben a példában:
>   - `Enter_the_Application_Id_Here` – ez a regisztrált alkalmazáshoz tartozó **Alkalmazás (ügyfél) azonosítója** érték.
>   - `Enter_the_Tenant_Id_Here` – cserélje le ezt az értéket a **bérlői azonosító** vagy a **bérlő nevére** (például contoso.microsoft.com).
>   - `Enter_the_Client_Secret_Here` – cserélje le ezt az értéket az 1. lépésben létrehozott ügyfél-titkos kulcsra.

> [!div renderon="docs"]
> > [!TIP]
> > Az **alkalmazás (ügyfél) azonosítójának**, a **bérlői azonosító**értékének megkereséséhez lépjen az alkalmazás **Áttekintés** lapjára a Azure Portal. Új kulcs létrehozásához nyissa meg a **tanúsítványok & titkok** lapot.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>3. lépés: rendszergazdai engedély

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>4. lépés: rendszergazdai engedély

Ha ezen a ponton próbálja meg futtatni az alkalmazást, *HTTP 403-Tiltott* hibaüzenetet kap: `Insufficient privileges to complete the operation` . Ez azért történik, mert az *csak alkalmazásokra vonatkozó engedélyek* rendszergazdai hozzájárulást igényelnek, ami azt jelenti, hogy a címtár globális rendszergazdájának hozzájárulást kell adnia az alkalmazáshoz. Válassza ki az alábbi lehetőségek egyikét a szerepkörtől függően:

##### <a name="global-tenant-administrator"></a>Globális bérlői rendszergazda

> [!div renderon="docs"]
> Ha Ön globális bérlői rendszergazda, nyissa meg az **API-engedélyek** lapot az Azure Portal alkalmazás-regisztrációjában (előzetes verzió), és válassza a **rendszergazdai jóváhagyás megadása a következőhöz: {bérlő neve}** (a (z) {bérlő neve} a címtár neve).

> [!div renderon="portal" class="sxs-lookup"]
> Ha Ön globális rendszergazda, lépjen az API- **engedélyek** oldalra, és válassza **a rendszergazdai jóváhagyás megadása Enter_the_Tenant_Name_Here**
> > [!div id="apipermissionspage"]
> > [Ugrás az API-engedélyek oldalra]()

##### <a name="standard-user"></a>Általános jogú felhasználó

Ha Ön a bérlő általános jogú felhasználója, kérje meg a globális rendszergazdát, hogy adja meg az alkalmazáshoz tartozó rendszergazdai jóváhagyást. Ehhez adja meg a következő URL-címet a rendszergazdának:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Ebben a példában:
>> * `Enter_the_Tenant_Id_Here` – cserélje le ezt az értéket a **bérlői azonosító** vagy a **bérlő nevére** (például contoso.microsoft.com).
>> * `Enter_the_Application_Id_Here` – ez a regisztrált alkalmazáshoz tartozó **Alkalmazás (ügyfél) azonosítója** érték.

> [!NOTE]
> Ha az előző URL-cím használatával engedélyezte a jóváhagyást az *alkalmazáshoz, a "AADSTS50011: No Válaszcím* " hibaüzenet jelenik meg. Ez azért fordulhat elő, mert ez az alkalmazás és az URL-cím nem rendelkezik átirányítási URI-val. Kérjük, hagyja figyelmen kívül a hibát.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>4. lépés: az alkalmazás futtatása

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>5. lépés: az alkalmazás futtatása

Ha a Visual studiót használja, nyomja le az **F5** billentyűt az alkalmazás futtatásához, ellenkező esetben futtassa az alkalmazást a parancssorból vagy a konzolon keresztül:

```console
cd {ProjectFolder}\daemon-console\1-Call-Graph
dotnet run
```

> Ebben a példában:
> * *{ProjectFolder}* az a mappa, ahová kibontotta a zip-fájlt. Példa **C:\Azure-Samples\active-Directory-dotnetcore-Daemon-v2**

Ennek eredményeképpen meg kell jelennie az Azure AD-címtárban található felhasználók listájának.

> [!IMPORTANT]
> Ez a rövid útmutató alkalmazás egy ügyfél titkos kulcsát használja, amely bizalmas ügyfélként azonosítja magát. Mivel az ügyfél titkos kulcsát egyszerű szövegként adja hozzá a Project-fájlokhoz, biztonsági okokból javasolt a tanúsítvány használata az ügyfél titkos kulcsa helyett, mielőtt az alkalmazást éles alkalmazásként venné fontolóra. A tanúsítványok használatáról a minta GitHub-tárházában talál [további](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates) információt.

## <a name="more-information"></a>További információ

### <a name="how-the-sample-works"></a>A minta működése
![Bemutatja, hogyan működik a rövid útmutatóban létrehozott minta alkalmazás](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

### <a name="msalnet"></a>MSAL.NET

A MSAL ([Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) az a könyvtár, amellyel a felhasználók bejelentkezhetnek, és a Microsoft Identity platform által védett API eléréséhez használt jogkivonatokat kérhetnek. A leírtak szerint ez a rövid útmutató a jogkivonatokat az alkalmazás saját identitásának használatával kéri le a delegált engedélyek helyett. Az ebben az esetben használt hitelesítési folyamat az *[ügyfél hitelesítő adatai OAuth folyamat](v2-oauth2-client-creds-grant-flow.md)*. A MSAL.NET és az ügyfél-hitelesítő adatok folyamatával kapcsolatos további információkért tekintse meg [ezt a cikket](https://aka.ms/msal-net-client-credentials).

 A MSAL.NET a következő parancs futtatásával telepítheti a Visual Studio **Package Manager konzolján**:

```powershell twhitney
```console
dotnet add package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>Az MSAL inicializálása

Az MSAL-re mutató hivatkozás hozzáadásához adja hozzá az alábbi kódot:

```csharp
using Microsoft.Identity.Client;
```

Ezután inicializálja az MSAL-t az alábbi kóddal:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientSecret(config.ClientSecret)
                                          .WithAuthority(new Uri(config.Authority))
                                          .Build();
```

> | Ebben a példában: | Leírás |
> |---------|---------|
> | `config.ClientSecret` | Az Azure Portalon az alkalmazáshoz létrehozott ügyfél-titkos kulcs. |
> | `config.ClientId` | Az Azure Portalon regisztrált alkalmazás **alkalmazásazonosítója (ügyfél-azonosítója)**. Ezt az értéket az alkalmazás **Áttekintés** oldalán találja az Azure Portalon. |
> | `config.Authority`    | Választható A hitelesítéshez a felhasználó STS-végpontja. A nyilvános felhő esetében általában a (z) `https://login.microsoftonline.com/{tenant}` {bérlő} a bérlő vagy a bérlői azonosító neve.|

További információkért tekintse [meg `ConfidentialClientApplication` ](/dotnet/api/microsoft.identity.client.iconfidentialclientapplication) a következő dokumentációt:

### <a name="requesting-tokens"></a>Jogkivonatok lekérése

Ha a tokent az alkalmazás identitásával szeretné kérelmezni, használja a következő `AcquireTokenForClient` metódust:

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

> |Ebben a példában:| Leírás |
> |---------|---------|
> | `scopes` | A kért hatóköröket tartalmazza. A bizalmas ügyfelek esetében a hasonló formátumot kell használnia, hogy `{Application ID URI}/.default` jelezze, hogy a kért hatókörök az Azure Portalon beállított app Object (Microsoft Graph, `{Application ID URI}` pont –) számára statikusan meghatározottak `https://graph.microsoft.com` . Az egyéni webes API- `{Application ID URI}` k esetében az Azure Portal alkalmazás-regisztrációjában (előzetes verzió), az **API közzététele** részben van meghatározva. |

További információkért tekintse [meg `AcquireTokenForClient` ](/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient) a következő dokumentációt:

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Következő lépések

A Daemon-alkalmazásokkal kapcsolatos további tudnivalókért tekintse meg a forgatókönyv áttekintését:

> [!div class="nextstepaction"]
> [Webes API-kat meghívó Daemon-alkalmazás](scenario-daemon-overview.md)
