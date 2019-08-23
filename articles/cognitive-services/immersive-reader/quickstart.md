---
title: 'Gyors útmutató: Hozzon létre egy webalkalmazást, amely elindítja a magával ragadó olvasót a következővelC#'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban létrehozhat egy webalkalmazást a semmiből, és hozzáadhatja a magával ragadó olvasó API funkcióját.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: ed49f834cb7cd4f649d84aea9e549e212771eead
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899368"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>Gyors útmutató: Hozzon létre egy webalkalmazást, amely elindítja aC#lebilincselő olvasót ()

A teljes [olvasó](https://www.onenote.com/learningtools) egy olyan, integráltan kialakított eszköz, amely bevált technikákat valósít meg az olvasási szövegértés javítására.

Ebben a rövid útmutatóban egy webalkalmazást hoz létre a semmiből, és integrálja a magával ragadó olvasót a saját olvasó SDK használatával. Ebben a rövid útmutatóban egy teljes körű működő [](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-csharp)minta érhető el.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads)
* A Azure Active Directory-(Azure AD-) hitelesítéshez konfigurált, magával ragadó olvasó erőforrás. A beállításhoz kövesse az [alábbi utasításokat](./azure-active-directory-authentication.md) . A minta projekt tulajdonságainak konfigurálásakor itt létrehozott értékek némelyikére szüksége lesz. Mentse a munkamenet kimenetét szövegfájlba későbbi használatra.

## <a name="create-a-web-app-project"></a>Webalkalmazás-projekt létrehozása

Hozzon létre egy új projektet a Visual Studióban a ASP.NET Core webalkalmazás-sablon használatával a beépített Model-View-Controller paranccsal.

![Új projekt](./media/vswebapp.png)

![Új ASP.NET Core webalkalmazás](./media/vsmvc.png)

## <a name="acquire-an-azure-ad-authentication-token"></a>Azure AD-hitelesítési jogkivonat beszerzése

Ehhez a részhez az Azure AD-hitelesítés konfigurációjának előfeltétele lépésnél néhány értéket kell megadnia. Nézze vissza az adott munkamenetből mentett szövegfájlt.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Kattintson a jobb gombbal a projektre a _megoldáskezelő_ , majd válassza a **felhasználói titkok kezelése**lehetőséget. Ekkor megnyílik egy _Secrets. JSON_nevű fájl. Cserélje le a fájl tartalmát a következőre, és adja meg az egyéni tulajdonságértékek értékét a fentiek közül.

```json
{
  "TenantId": YOUR_TENANT_ID,
  "ClientId": YOUR_CLIENT_ID,
  "ClientSecret": YOUR_CLIENT_SECRET,
  "Subdomain": YOUR_SUBDOMAIN
}
```

Nyissa meg a _Controllers\HomeController.cs_, és cserélje le a fájlt a következő kódra.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.IdentityModel.Clients.ActiveDirectory;

namespace QuickstartSampleWebApp.Controllers
{
    public class HomeController : Controller
    {
        private readonly string TenantId;     // Azure subscription TenantId
        private readonly string ClientId;     // Azure AD ApplicationId
        private readonly string ClientSecret; // Azure AD Application Service Principal password
        private readonly string Subdomain;    // Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')

        public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
        {
            TenantId = configuration["TenantId"];
            ClientId = configuration["ClientId"];
            ClientSecret = configuration["ClientSecret"];
            Subdomain = configuration["Subdomain"];

            if (string.IsNullOrWhiteSpace(TenantId))
            {
                throw new ArgumentNullException("TenantId is null! Did you add that info to secrets.json?");
            }

            if (string.IsNullOrWhiteSpace(ClientId))
            {
                throw new ArgumentNullException("ClientId is null! Did you add that info to secrets.json?");
            }

            if (string.IsNullOrWhiteSpace(ClientSecret))
            {
                throw new ArgumentNullException("ClientSecret is null! Did you add that info to secrets.json?");
            }

            if (string.IsNullOrWhiteSpace(Subdomain))
            {
                throw new ArgumentNullException("Subdomain is null! Did you add that info to secrets.json?");
            }
        }

        public IActionResult Index()
        {
            return View();
        }

        [Route("subdomain")]
        public string GetSubdomain()
        {
            return Subdomain;
        }

        [Route("token")]
        public async Task<string> GetToken()
        {
            return await GetTokenAsync();
        }

        /// <summary>
        /// Get an Azure AD authentication token
        /// </summary>
        private async Task<string> GetTokenAsync()
        {
            string authority = $"https://login.windows.net/{TenantId}";
            const string resource = "https://cognitiveservices.azure.com/";

            AuthenticationContext authContext = new AuthenticationContext(authority);
            ClientCredential clientCredential = new ClientCredential(ClientId, ClientSecret);

            AuthenticationResult authResult = await authContext.AcquireTokenAsync(resource, clientCredential);

            return authResult.AccessToken;
        }
    }
}
```

## <a name="add-the-microsoftidentitymodelclientsactivedirectory-nuget-package"></a>Adja hozzá a Microsoft. IdentityModel. clients. ActiveDirectory NuGet-csomagot

A fenti kód a **Microsoft. IdentityModel. clients. ActiveDirectory** NuGet-csomag objektumait használja, ezért hozzá kell adnia egy hivatkozást az adott csomaghoz a projektben.

Nyissa meg a NuGet csomagkezelő konzolt a **Tools-> NuGet csomagkezelő-> Package** Manager konzolon, és írja be a következőt:

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.1.0
```

## <a name="add-sample-content"></a>Minta tartalmának hozzáadása

Most hozzáadunk egy minta tartalmat a webalkalmazáshoz. Nyissa meg a _Views\Home\Index.cshtml_ , és cserélje le az automatikusan generált kódot a következő mintára:

```html
<h1 id='title'>Geography</h1>
<span id='content'>
    <p>The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers. Landforms are sometimes called physical features. It is important for students to know about the physical geography of Earth. The seasons, the atmosphere and all the natural processes of Earth affect where people are able to live. Geography is one of a combination of factors that people use to decide where they want to live.</p>
</span>

<div class='immersive-reader-button' data-button-style='iconAndText' onclick='launchImmersiveReader()'></div>

@section scripts {
    <script type='text/javascript' src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js'></script>
    <script type='text/javascript' src='https://code.jquery.com/jquery-3.3.1.min.js'></script>
    <script type='text/javascript'>
    function getImmersiveReaderTokenAsync() {
        return new Promise((resolve) => {
            $.ajax({
                url: '/token',
                type: 'GET',
                success: token => {
                    resolve(token);
                }
            });
        });
    }

    function getSubdomainAsync() {
        return new Promise((resolve) => {
            $.ajax({
                url: '/subdomain',
                type: 'GET',
                success: subdomain => {
                    resolve(subdomain);
                }
            });
        });
    }

    async function launchImmersiveReader() {
        const content = {
            title: document.getElementById('title').innerText,
            chunks: [ {
                content: document.getElementById('content').innerText,
                lang: 'en'
            } ]
        };

        const token = await getImmersiveReaderTokenAsync();
        var subdomain = await getSubdomainAsync();

        ImmersiveReader.launchAsync(token, subdomain, content, { uiZIndex: 1000000 });
    }
    </script>
}
```

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

A menüsávban válassza a **hibakeresés > a hibakeresés elindítása**lehetőséget, vagy nyomja le az **F5** billentyűt az alkalmazás elindításához.

A böngészőben a következőknek kell megjelennie:

![Minta alkalmazás](./media/quickstart-result.png)

Ha a "magára olvasó" gombra kattint, megjelenik a megjelenő, az oldalon található tartalommal ellátott olvasó.

![Modern olvasó](./media/quickstart-immersive-reader.png)

## <a name="next-steps"></a>További lépések

* Tekintse meg a [Node. js](./tutorial-nodejs.md) -oktatóanyagot, amelyből megtudhatja, hogy a Node. js használatával milyen egyéb műveleteket végezhet el az olvasói SDK-val
* Tekintse meg a [Python](./tutorial-python.md) -oktatóanyagot, amelyből megtudhatja, hogy mit tehet a részletes olvasó SDK-val a Python használatával
* Tekintse meg az [iOS](./tutorial-ios-picture-immersive-reader.md) -oktatóanyagot, amelyből megtudhatja, mit tehet a gyors
* Ismerkedjen meg a magára az [olvasói SDK](https://github.com/microsoft/immersive-reader-sdk) -val és az [olvasói SDK](./reference.md) -referenciával
