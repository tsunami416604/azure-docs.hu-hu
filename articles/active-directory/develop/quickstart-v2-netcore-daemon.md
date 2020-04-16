---
title: Token beszerezni& hívja meg a Microsoft Graphot a konzolalkalmazás identitásával | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan szerezhet be jogkivonatot, és hogyan hívhat meg vele védett Microsoft Graph API-t egy .NET Core alkalmazásból
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 4332755c1ee47cd648f048768307846f02503e20
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407047"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-using-console-apps-identity"></a>Rövid útmutató: Jogkivonat beszerzése és a Microsoft Graph API hívása a konzolalkalmazás identitásával

Ebben a rövid útmutatóban megtudhatja, hogyan írhat egy .NET Core alkalmazást, amely az alkalmazás saját identitásával kaphat hozzáférési jogkivonatot, majd hívja meg a Microsoft Graph API-t a címtárban lévő [felhasználók listájának](https://docs.microsoft.com/graph/api/user-list) megjelenítéséhez. Ez a forgatókönyv olyan helyzetekben hasznos, ahol a fej nélküli, felügyelet nélküli feladat vagy a Windows-szolgáltatás kell futtatni egy alkalmazás identitás, a felhasználó identitása helyett. (Lásd: [Hogyan működik a minta](#how-the-sample-works) egy illusztrációhoz.)

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz [.NET Core 2.2](https://www.microsoft.com/net/download/dotnet-core/2.2)szükséges.

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>A rövid útmutató mintaalkalmazásának regisztrálása és letöltése

> [!div renderon="docs" class="sxs-lookup"]
>
> A rövid útmutató elindításához két lehetőség van: Expressz (alább 1. lehetőség) és Manuális (2. lehetőség)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>1. lehetőség: Az alkalmazás regisztrálása és automatikus konfigurálása, majd a kódminta letöltése
>
> 1. Nyissa meg az új [Azure Portal – Alkalmazásregisztrációk](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs) ablaktáblát.
> 1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
> 1. Kövesse az új alkalmazás egy kattintással való letöltésére és automatikus konfigurálására vonatkozó utasításokat.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>2. lehetőség: Az alkalmazás és a kódminta regisztrálása és kézi konfigurálása

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
> Az alkalmazás regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való kézi hozzáadásához kövesse az alábbi lépéseket:
>
> 1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
> 1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
> 1. Keresse meg a Microsoft identity platform ot a fejlesztőknek [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapon.
> 1. Válassza **az Új regisztráció lehetőséget.**
> 1. Amikor megjelenik a **Jelentkezés iszatának regisztrálása** lap, adja meg a jelentkezési lap regisztrációs adatait. 
> 1. A **Név szakaszban** adjon meg egy értelmes alkalmazásnevet, amely megjelenik az alkalmazás felhasználói számára, például `Daemon-console`a lehetőséget, majd válassza a **Regisztráció** lehetőséget az alkalmazás létrehozásához.
> 1. A regisztrációt követően válassza a **Tanúsítványok & titkos kulcsok menüt.**
> 1. Az **Ügyféltitkok csoportban**válassza a **+ Új ügyféltitok**lehetőséget. Adjon neki nevet, és válassza **a Hozzáadás**lehetőséget. Másolja a titkot egy biztonságos helyre. Szüksége lesz rá, hogy használja a kódot.
> 1. Most válassza az **API-engedélyek menüt,** válassza **a + Engedély hozzáadása** gombot, és válassza a Microsoft **Graph**lehetőséget .
> 1. Válassza **az Alkalmazásengedélyek lehetőséget.**
> 1. A **Felhasználói** csomópont csoportban válassza a **User.Read.All ( Felhasználó. olvasás)** lehetőséget, majd az Engedélyek **hozzáadása** lehetőséget.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>A rövid útmutató alkalmazás letöltése és konfigurálása
> 
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>1. lépés: Az alkalmazás konfigurálása az Azure Portalon
> A gyorsútmutató működéséhez a kódminta működéséhez létre kell hoznia egy ügyféltitkot, és hozzá kell adnia a Graph API **User.Read.All** alkalmazásengedélyét.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Hajtsa végre ezeket a módosításokat nekem]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-netcore-daemon/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-your-visual-studio-project"></a>2. lépés: A Visual Studio-projekt letöltése

> [!div renderon="docs"]
> [A Visual Studio-projekt letöltése](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Futtassa a projektet a Visual Studio 2019 használatával.
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [A kódminta letöltése](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
    
> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>3. lépés: A Visual Studio-projekt konfigurálása
> 
> 1. Csomagolja ki a zip-fájlt egy helyi mappába a lemez gyökerének közelében (például: **C:\Azure-Samples**).
> 1. Nyissa meg a megoldást a Visual Studio - **1-Call-MSGraph\daemon-console.sln** (nem kötelező).
> 1. Edit **appsettings.json** és cserélje ki `ClientId` `Tenant` az `ClientSecret` értékeket a mezők , és a következő:
> 
>    ```json
>    "Tenant": "Enter_the_Tenant_Id_Here",
>    "ClientId": "Enter_the_Application_Id_Here",
>    "ClientSecret": "Enter_the_Client_Secret_Here"
>    ```
>   Az elemek magyarázata:
>   - `Enter_the_Application_Id_Here` – ez a regisztrált alkalmazáshoz tartozó **Alkalmazás (ügyfél) azonosítója** érték.
>   - `Enter_the_Tenant_Id_Here`- cserélje le ezt az értéket a **bérlőazonosító** vagy **a bérlő nevére** (például contoso.microsoft.com)
>   - `Enter_the_Client_Secret_Here`- cserélje le ezt az értéket az 1.

> [!div renderon="docs"]
> > [!TIP]
> > **Az alkalmazás (ügyfél) azonosító**, **címtár (bérlői) azonosító**értékek et, keresse fel az alkalmazás **áttekintése** lapot az Azure Portalon. Új kulcs létrehozásához nyissa meg **a Tanúsítványok & titkos kulcsok** lapot.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>3. lépés: Rendszergazdai hozzájárulás

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>4. lépés: Rendszergazdai hozzájárulás

Ha ezen a ponton próbálja meg futtatni az alkalmazást, *http 403 – Tiltott* hibaüzenet jelenik meg: `Insufficient privileges to complete the operation`. Ennek az az oka, hogy minden *csak alkalmazásengedélyhez* rendszergazdai hozzájárulás szükséges, ami azt jelenti, hogy a címtár globális rendszergazdájának beleegyezését kell adnia az alkalmazáshoz. A szerepkörtől függően válasszon az alábbi lehetőségek közül:

##### <a name="global-tenant-administrator"></a>Globális bérlői rendszergazda

> [!div renderon="docs"]
> Ha Globális bérlői rendszergazda, lépjen az **API-engedélyek** lapra az Azure Portal alkalmazásregisztrációjában (előzetes verzió), és válassza a **Rendszergazdai hozzájárulás megadása {Tenant Name} (ahol** {Tenant Name} a címtár neve).

> [!div renderon="portal" class="sxs-lookup"]
> Ha Ön globális rendszergazda, nyissa meg **az API-engedélyek** lapot, és válassza **a Rendszergazdai hozzájárulás megadása Enter_the_Tenant_Name_Here**
> > [!div id="apipermissionspage"]
> > [Ugrás az API-engedélyek lapra]()

##### <a name="standard-user"></a>Általános jogú felhasználó

Ha Ön a bérlő általános jogú felhasználója, akkor meg kell kérnie egy globális rendszergazdát, hogy adja meg az alkalmazás rendszergazdai beleegyezését. Ehhez adja meg a következő URL-címet a rendszergazdának:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Az elemek magyarázata:
>> * `Enter_the_Tenant_Id_Here`- cserélje le ezt az értéket a **bérlőazonosító** vagy **a bérlő nevére** (például contoso.microsoft.com)
>> * `Enter_the_Application_Id_Here` – ez a regisztrált alkalmazáshoz tartozó **Alkalmazás (ügyfél) azonosítója** érték.

> [!NOTE]
> Az *"AADSTS50011: Nincs válaszcím regisztrálva az alkalmazáshoz"* hibaüzenet jelenhet meg, miután az előző URL-címet használva engedélyezte az alkalmazás hozzájárulását. Ez azért történik, mert ez az alkalmazás és az URL-cím nem rendelkezik átirányítási URI-val - hagyja figyelmen kívül a hibát.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>4. lépés: Az alkalmazás futtatása

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>5. lépés: Az alkalmazás futtatása

Ha a Visual Studio alkalmazást használja, nyomja le az **F5** billentyűt az alkalmazás futtatásához, ellenkező esetben futtassa az alkalmazást a parancssorból vagy a konzolon keresztül:

```console
cd {ProjectFolder}\daemon-console\1-Call-Graph
dotnet run
```

> Az elemek magyarázata:
> * *A(z) {ProjectFolder}* az a mappa, amelyben kibontotta a zip fájlt. Példa **C:\Azure-Samples\active-directory-dotnetcore-daemon-v2**

Ennek eredményeképpen meg kell jelennie az Azure AD-címtárban lévő felhasználók listájának.

> [!IMPORTANT]
> Ez a rövid útmutató alkalmazás egy ügyféltitkos kulcsot használ, hogy bizalmas ügyfélként azonosítsa magát. Mivel az ügyféltitkos kulcsot egyszerű szövegként adja hozzá a projektfájlokhoz, biztonsági okokból ajánlott az alkalmazás éles alkalmazásként való felhasználása előtt az ügyféltitok helyett tanúsítványt használni. A tanúsítványok használatával kapcsolatos további információkért tekintse meg [ezeket](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates) az utasításokat a GitHub-tárházban ehhez a mintához.

## <a name="more-information"></a>További információ

### <a name="how-the-sample-works"></a>A minta működése
![Megmutatja, hogyan működik az ez a rövid útmutató által létrehozott mintaalkalmazás](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

### <a name="msalnet"></a>MSAL.NET

Az MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) a Microsoft identity platform által védett API-k eléréséhez használt felhasználók bejelentkezésére és tokenek kérelmezésére használt tár. A mint ismertetjük, ez a gyorsindítás jogkivonatokat kér az alkalmazás saját identitásának használatával a delegált engedélyek helyett. Az ebben az esetben használt hitelesítési folyamat *[az ügyfél hitelesítő adatainak oauth](v2-oauth2-client-creds-grant-flow.md)* flow-ja. A MSAL.NET ügyfélhitelesítő adatok kal történő használatával kapcsolatos további tudnivalókért olvassa el [ezt a cikket.](https://aka.ms/msal-net-client-credentials)

 A MSAL.NET a Visual Studio **Csomagkezelő konzolján**a következő parancs futtatásával telepítheti:

```powershell
Install-Package Microsoft.Identity.Client
```

Ha nem használja a Visual Studio alkalmazást, a következő paranccsal hozzáadhatja az MSAL-t a projekthez:

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

> | Az elemek magyarázata: ||
> |---------|---------|
> | `config.ClientSecret` | Az alkalmazáshoz az Azure Portalon létrehozott ügyféltitok. |
> | `config.ClientId` | Az Azure Portalon regisztrált alkalmazás **alkalmazásazonosítója (ügyfél-azonosítója)**. Ezt az értéket az alkalmazás **Áttekintés** oldalán találja az Azure Portalon. |
> | `config.Authority`    | (Nem kötelező) A felhasználó hitelesítéséhez szükséges STS-végpont. Általában <https://login.microsoftonline.com/{tenant}> a nyilvános felhőben, ahol {tenant} a bérlő vagy a bérlőazonosító neve.|

További információkért kérjük, olvassa el [a `ConfidentialClientApplication` ](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.iconfidentialclientapplication?view=azure-dotnet)

### <a name="requesting-tokens"></a>Jogkivonatok lekérése

Ha az alkalmazás identitásával szeretne tokeneket igényelni, használja `AcquireTokenForClient` a módszert:

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

> |Az elemek magyarázata:| |
> |---------|---------|
> | `scopes` | A kért hatóköröket tartalmazza. A bizalmas ügyfelek esetében ez a `{Application ID URI}/.default` formátum ot kell használnia annak jelzésére, hogy a kért hatókörök statikusan definiálva `{Application ID URI}` vannak `https://graph.microsoft.com`az Azure Portalon beállított alkalmazásobjektumban (a Microsoft Graph esetében a rámutat). Az egyéni webes `{Application ID URI}` API-k, az Azure Portal alkalmazásregisztráció (előzetes verzió) **Elérhetővé egy API szakaszban** van definiálva. |

További információkért kérjük, olvassa el [a `AcquireTokenForClient` ](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient?view=azure-dotnet)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a démonalkalmazásokról, tekintse meg a forgatókönyv céloldalát

> [!div class="nextstepaction"]
> [Webes API-kat meghívjaó démonalkalmazás](scenario-daemon-overview.md)

A démonalkalmazás oktatóanyagát lásd:

> [!div class="nextstepaction"]
> [Daemon .NET Core konzol oktatóanyag](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)

További információ az engedélyekről és a hozzájárulásról:

> [!div class="nextstepaction"]
> [Engedélyek és hozzájárulás](v2-permissions-and-consent.md)

Ha többet szeretne tudni az ebben a forgatókönyvben szereplő hitelesítési folyamatról, olvassa el az Oauth 2.0 ügyfél hitelesítő adatainak folyamatát:

> [!div class="nextstepaction"]
> [Ügyfél hitelesítő adatai Oauth-folyamat](v2-oauth2-client-creds-grant-flow.md)
