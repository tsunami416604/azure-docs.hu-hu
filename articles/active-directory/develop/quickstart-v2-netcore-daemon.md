---
title: Az Azure AD v2 .NET Core-démon |} A Microsoft Docs
description: Ismerje meg, hogyan egy .NET Core-folyamat hozzáférési jogkivonatot kapjon és egy API-t egy Azure Active Directory v2.0-végpont az alkalmazás saját identitással által védett
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/28/2018
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 787a817b32838d11cb8974737037543f73285b0d
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2018
ms.locfileid: "53653425"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-console-app-using-apps-identity"></a>Gyors útmutató: Egy token beszerzéséhez és a Microsoft Graph API hívása egy konzolalkalmazás használatával az alkalmazás-identitást

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Ebből a gyorsútmutatóból megtudhatja, hogyan írhat olyan .NET Core-alkalmazást, amely ezután hívja meg a Microsoft Graph API megjelenítéséhez és az alkalmazás saját identitással hozzáférési jogkivonatot kapjon a [felhasználók listája](https://docs.microsoft.com/graph/api/user-list) a címtárban. Ebben a forgatókönyvben hasznos olyan esetekben, ahol távfelügyelt, a felügyelet nélküli feladat vagy egy windows-szolgáltatás futtatásához szükséges egy alkalmazás identitással helyett a felhasználó identitását.

![A rövid útmutató által létrehozott mintaalkalmazás működése](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.png)

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutatóhoz [.NET Core 2.1](https://www.microsoft.com/net/download/dotnet-core/2.1).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>A rövid útmutató mintaalkalmazásának regisztrálása és letöltése
>
> A rövid útmutató mintaalkalmazását kétféleképpen indíthatja el:
> * [Express] [1. lehetőség: Regisztráció és az automatikus állítsa be alkalmazását, és töltse le a kódminta](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuális] [2. lehetőség: Regisztráljon, és manuális konfigurálása az alkalmazás és a kód a minta](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>1. lehetőség: Regisztráció és az automatikus állítsa be alkalmazását, és töltse le a kódminta
>
> 1. Nyissa meg az [Azure Portal – Alkalmazásregisztráció (előzetes verzió)](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs) szakaszt.
> 1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
> 1. Kövesse az új alkalmazás egy kattintással való letöltésére és automatikus konfigurálására vonatkozó utasításokat.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>2. lehetőség: Regisztráljon, és manuális konfigurálása az alkalmazás és a kód a minta
>
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
> Az alkalmazás regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való kézi hozzáadásához kövesse az alábbi lépéseket:
>
> 1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
> 1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
> 1. A bal oldali navigációs panelen válassza az **Azure Active Directory** szolgáltatást, majd válassza az **Alkalmazásregisztrációk (előzetes verzió)** > **Új regisztráció** lehetőséget.
> 1. Az a **neve** területén adjon meg egy kifejező alkalmazásnevet fog megjelenni a felhasználók számára az alkalmazás, például `Daemon-console`, majd **regisztrálása** hozhat létre az alkalmazást.
> 1. Miután regisztrált, válassza ki a **tanúsítványok és titkos kulcsok** menü.
> 1. A **ügyfél titkos kódok**válassza **+ új titkos ügyfélkulcsot**. Adjon meg egy nevet, és kattintson **Hozzáadás**. A titkos kulcsot, a biztonságos helyre másolja. Szüksége lesz rá használata a kódban.
> 1. Most válassza ki a **API-engedélyek** menüjében válassza **+ adjon hozzá egy engedélyt** gombra, válassza **Microsoft Graph**.
> 1. Válassza ki **Alkalmazásengedélyek**.
> 1. A **felhasználói** csomópontban jelölje ki **User.Read.All**, majd **engedélyek hozzáadása**

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Töltse le és állítsa be a rövid útmutató alkalmazását
> 
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>1. lépés: Az alkalmazás konfigurálása az Azure Portalon
> A kódminta működjön ez a rövid útmutató, hozzon létre egy ügyfélkulcsot, és adja hozzá a Graph API-k szüksége **User.Read.All** alkalmazásnak.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Ezt a módosítást a számomra]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-windows-desktop/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-your-visual-studio-project"></a>2. lépés: A Visual Studio-projekt letöltése

[A Visual Studio-projekt letöltése](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>3. lépés: A Visual Studio-projekt konfigurálása

1. Csomagolja ki a zip-fájlt egy helyi mappába a lemez gyökerének közelében (például: **C:\Azure-Samples**).
1. Nyissa meg a megoldást a Visual Studio - **démon-console.sln** (nem kötelező).
1. Szerkesztés **appsettings.json** , és cserélje le a mezők értékeit `ClientId`, `Tenant` és `ClientSecret` a következőkkel:

    ```json
    "Tenant": "Enter_the_Tenant_Id_Here",
    "ClientId": "Enter_the_Application_Id_Here",
    "ClientSecret": "Enter_the_Client_Secret_Here"
    ```
    > > [!div renderon="portal" id="certandsecretspage" class="sxs-lookup"]
    > > [Hozzon létre egy új titkos ügyfélkulcsot]()
    
    > [!div renderon="docs"]
    >> Az elemek magyarázata:
    >> * `Enter_the_Application_Id_Here` – ez a regisztrált alkalmazáshoz tartozó **Alkalmazás (ügyfél) azonosítója** érték.
    >> * `Enter_the_Tenant_Id_Here` – Ezt az értéket cserélje le a **Bérlőazonosító** vagy **bérlő neve** (például contoso.microsoft.com)
    >> * `Enter_the_Client_Secret_Here` – Ezt az értéket cserélje le az 1. lépésben létrehozott, az ügyfél titkos kulcsát.

    > [!div renderon="docs"]
    > > [!TIP]
    > > Az értékek azonosításához **Alkalmazásazonosítót (ügyfél)**, **(bérlő) címtár-azonosító**, lépjen az alkalmazás a **áttekintése** oldal az Azure Portalon. Hozzon létre egy új kulcsot, lépjen a **tanúsítványok és titkos kulcsok** lapot.
    
#### <a name="step-4-admin-consent"></a>4. lépés: Rendszergazdai jóváhagyás

Bármely *csak az alkalmazásra vonatkozó engedély* rendszergazdai jóváhagyást igénylő – ami azt jelenti, hogy egy beleegyezés az alkalmazásnak a címtár globális rendszergazdájának kell. Válassza ki a beállítások alatt a szerepkör függően:

##### <a name="global-tenant-administrator"></a>Globális Bérlői rendszergazda

> [!div renderon="docs"]
> Ha a globális bérlői rendszergazdája, nyissa meg a **API-engedélyek** az Azure Portal-alkalmazás regisztrációja (előzetes verzió) oldalra, majd válassza ki **adja meg a {bérlő neve} rendszergazdai jóváhagyás** (ahol a {bérlő neve} a könyvtár nevét).

> [!div renderon="portal" class="sxs-lookup"]
> Ha egy globális rendszergazdai, nyissa meg a **API-engedélyek** lapon jelölje be **Enter_the_Tenant_Name_Here a rendszergazdai jóváhagyás megadása**
> > [!div id="apipermissionspage"]
> > [Nyissa meg az API-engedélyek lap]()

##### <a name="standard-user"></a>Általános jogú felhasználó

Ha Ön a bérlő normál felhasználó, majd meg kell kérje meg a globális rendszergazda rendszergazdai hozzájárulás az alkalmazáshoz. Ehhez adja meg a következő URL-címet a rendszergazdának:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Az elemek magyarázata:
>> * `Enter_the_Tenant_Id_Here` – Ezt az értéket cserélje le a **Bérlőazonosító** vagy **bérlő neve** (például contoso.microsoft.com)
>> * `Enter_the_Application_Id_Here` – ez a regisztrált alkalmazáshoz tartozó **Alkalmazás (ügyfél) azonosítója** érték.

> [!NOTE]
> A hiba jelenhet *"AADSTS50011: A válaszcím nem az alkalmazás regisztrálva van a(z)* után hozzájárulás az alkalmazáshoz, a fenti URL-cím használatával. Ez történik, mert ez az alkalmazás és az URL-cím nem kell átirányítási URI - hagyja figyelmen kívül a hibát.

#### <a name="step-5-run-the-application"></a>5. lépés: Az alkalmazás futtatása

A Visual Studio használatakor nyomja le az ENTER **F5** az alkalmazás futtatásához, ellenkező esetben az alkalmazás futtatásához parancssort vagy a konzolon keresztül:

```console
cd {ProjectFolder}\daemon-console
dotnet run
```

> Az elemek magyarázata:
> * *{ProjectFolder}*  van a mappát, amelyikbe kibontotta a zip-fájlt. Példa **C:\Azure-Samples\active-directory-dotnetcore-daemon-v2**

Megjelenik azon felhasználók listáját az Azure AD-címtárban eredményként.

## <a name="more-information"></a>További információ

### <a name="msalnet"></a>MSAL.NET

Az MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) az a kódtár, amely felhasználók beléptetéséhez és egy Microsoft Azure Active Directory (Azure AD) által védett API-hoz való hozzáféréshez használt jogkivonatok kéréséhez használható. Leírt, ez a rövid útmutató kérelem jogkivonatok delegált engedélyeket a saját Alkalmazásidentitás helyett. A hitelesítési folyamatot ebben az esetben az úgynevezett  *[ügyfél hitelesítő adatai az oauth-folyamat](v2-oauth2-client-creds-grant-flow.md)*. További információ az ügyfél hitelesítési folyamata az MSAL.NET használatával, tekintse meg [Ez a cikk](https://aka.ms/msal-net-client-credentials).

 A Visual Studióban a következő parancs futtatásával telepítheti az MSAL.NET **Package Manager Console**:

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
ClientCredential clientCredentials = new ClientCredential(secret: config.ClientSecret);

var app = new ConfidentialClientApplication(
    clientId: config.ClientId, 
    authority: config.Authority, 
    redirectUri: "https://daemon", 
    clientCredential: clientCredentials, 
    userTokenCache: null, 
    appTokenCache: new TokenCache()
);
```

> | Az elemek magyarázata: ||
> |---------|---------|
> | `secret` | Az ügyfél titkos kulcsát az alkalmazás az Azure Portalon jön létre. |
> | `clientId` | Az Azure Portalon regisztrált alkalmazás **alkalmazásazonosítója (ügyfél-azonosítója)**. Ezt az értéket az alkalmazás **Áttekintés** oldalán találja az Azure Portalon. |
> | `Authority`    | (Nem kötelező) Az STS végpont a felhasználó hitelesítéséhez. Általában https://login.microsoftonline.com/{tenant} nyilvános felhő, {tenant} helyére a bérlő vagy a bérlő azonosítóját.|
> | `redirectUri`  | URL-címe, ahol felhasználóknak legyenek elküldve a hitelesítés után. Ebben az esetben mivel ez egy konzol/nem interaktív alkalmazást, ez a paraméter nem használatos |
> | `clientCredentials`  | A hitelesítő adatok ügyfélobjektumát, amely tartalmazza a titkos kulcsot vagy a tanúsítvány |
> | `userTokenCache`  | A felhasználói jogkivonat a gyorsítótárban példánya. Ebben az esetben mivel ez az alkalmazás az alkalmazás, és nem a felhasználó környezetében fut, ez az érték null értékű|
> | `appTokenCache`  | Az alkalmazás jogkivonat a gyorsítótárban példánya|

További információkért tekintse meg a [referenciadokumentációjára `ConfidentialClientApplication`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplication.-ctor?view=azure-dotnet#Microsoft_Identity_Client_ConfidentialClientApplication__ctor_System_String_System_String_System_String_Microsoft_Identity_Client_ClientCredential_Microsoft_Identity_Client_TokenCache_Microsoft_Identity_Client_TokenCache)

### <a name="requesting-tokens"></a>Jogkivonatok lekérése

Egy alkalmazás azonosítójának használatával tokent kér, használja a `AcquireTokenForClientAsync` módszer:

```csharp
result = await app.AcquireTokenForClientAsync(scopes);
```

> |Az elemek magyarázata:| |
> |---------|---------|
> | `scopes` | A kért hatóköröket tartalmazza. Bizalmas ügyfelek ezt kell használnia a formátuma a következőhöz hasonló `{Application ID URI}/.default` jelzi, hogy a kért lépnek az alkalmazás objektum statikusan megadott hatókörök beállítása az Azure Portalon (Microsoft Graph, `{Application ID URI}` mutat `https://graph.microsoft.com`). Egyéni webes API-k `{Application ID URI}` definiálva a **közzé API-k** az Azure Portal-alkalmazás regisztrációja (előzetes verzió) szakaszban. |

További információkért tekintse meg a [referenciadokumentációjára `AcquireTokenForClientAsync`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclientasync?view=azure-dotnet#Microsoft_Identity_Client_ConfidentialClientApplication_AcquireTokenForClientAsync_System_Collections_Generic_IEnumerable_System_String__)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>További lépések

További információ a engedélyek és jóváhagyás további információ:

> [!div class="nextstepaction"]
> [Engedélyek és jóváhagyás](v2-permissions-and-consent.md)

Többet tudni a hitelesítési folyamat ebben a forgatókönyvben, tekintse meg az Oauth 2.0 ügyfél-hitelesítési folyamata:

> [!div class="nextstepaction"]
> [Ügyfél-hitelesítési Oauth folyamata](v2-oauth2-client-creds-grant-flow.md)

> [!div class="nextstepaction"]
> [Ügyfél-hitelesítő adatok az MSAL.NET folyamatok](https://aka.ms/msal-net-client-credentials)