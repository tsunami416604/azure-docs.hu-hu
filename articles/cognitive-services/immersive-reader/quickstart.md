---
title: 'Rövid útmutató: Hozzon létre egy webes alkalmazást, amely elindítja a Magával ragadó olvasót C-vel #'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban teljesen új webalkalmazást hozhat létre, és hozzáadhatja a Magával ragadó Olvasó API-funkciót.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 8dd8459922caa9f765d59bc28fbf050b86834b46
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845241"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>Rövid útmutató: Hozzon létre egy webes alkalmazást, amely elindítja a Magával ragadó olvasót (C#)

A [Magával ragadó olvasó](https://www.onenote.com/learningtools) egy inkluzívan tervezett eszköz, amely bevált technikákat valósít meg az olvasás megértésének javítása érdekében.

Ebben a rövid útmutatóban teljesen új webes alkalmazást hozhat létre, és integrálhatja a Magával ragadó olvasót a Magával ragadó olvasó SDK használatával. A rövid útmutató teljes munkamintája [itt](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-csharp)érhető el.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads)
* Az Azure Active Directory-hitelesítéshez konfigurált magával ragadó reader-erőforrás. A beállításhoz kövesse [az alábbi utasításokat.](./how-to-create-immersive-reader.md) A mintaprojekt tulajdonságainak konfigurálásakor szüksége lesz néhány itt létrehozott értékre. Mentse a munkamenet kimenetét egy szöveges fájlba későbbi használatra.

## <a name="create-a-web-app-project"></a>Webalkalmazás-projekt létrehozása

Hozzon létre egy új projektet a Visual Studióban a beépített model-view-vezérlővel rendelkező ASP.NET Core webalkalmazás sablonnal és ASP.NET Core 2.1-es sel. Nevezze el a projektet "QuickstartSampleWebApp".Name the project "QuickstartSampleWebApp".

![Új projekt](./media/quickstart-csharp/1-createproject.png)

![Új projekt konfigurálása](./media/quickstart-csharp/2-configureproject.png)

![Új ASP.NET Core webalkalmazás](./media/quickstart-csharp/3-createmvc.png)

## <a name="set-up-authentication"></a>Hitelesítés beállítása

### <a name="configure-authentication-values"></a>Hitelesítési értékek konfigurálása

Kattintson a jobb gombbal a projektre a _Megoldáskezelőben,_ és válassza **a Felhasználói titkok kezelése parancsot**. Ekkor megnyílik a _secrets.json_nevű fájl. Ez a fájl nincs bevéve a forrásvezérlőbe. Tudjon meg többet [itt](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=windows). Cserélje le a _secrets.json_ tartalmát a következőkre, és adja meg a Immersive Reader erőforrás létrehozásakor megadott értékeket.

```json
{
  "TenantId": "YOUR_TENANT_ID",
  "ClientId": "YOUR_CLIENT_ID",
  "ClientSecret": "YOUR_CLIENT_SECRET",
  "Subdomain": "YOUR_SUBDOMAIN"
}
```

### <a name="add-the-microsoftidentitymodelclientsactivedirectory-nuget-package"></a>A Microsoft.IdentityModel.Clients.ActiveDirectory NuGet csomag hozzáadása

A következő kód a **Microsoft.IdentityModel.Clients.ActiveDirectory** NuGet csomag objektumait használja, ezért hozzá kell adnia egy hivatkozást a csomagra a projektben.

Nyissa meg a NuGet Package Manager konzolt a **Tools -> NuGet Package Manager -> Csomagkezelő konzolról,** és futtassa a következő parancsot:

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.2.0
```

### <a name="update-the-controller-to-acquire-the-token"></a>A vezérlő frissítése a token beszerzéséhez 

Nyissa _meg a Controllers\HomeController.cs_fájlt, és adja hozzá a következő kódot a fájl tetején _lévő utasítások_ használata után.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Most konfiguráljuk a vezérlőt, hogy az Azure AD-értékeket a _secrets.json.now._ A _HomeController_ osztály tetején, ```public class HomeController : Controller {```miután a , adja hozzá a következő kódot.

```csharp
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

[HttpGet]
public async Task<JsonResult> GetTokenAndSubdomain()
{
    try
    {
        string tokenResult = await GetTokenAsync();

        return new JsonResult(new { token = tokenResult, subdomain = Subdomain });
    }
    catch (Exception e)
    {
        string message = "Unable to acquire Azure AD token. Check the debugger for more information.";
        Debug.WriteLine(message, e);
        return new JsonResult(new { error = message });
    }
}
```

## <a name="add-sample-content"></a>Mintatartalom hozzáadása
Először nyissa meg a _Nézetek\Megosztott\Layout.cshtml fájlt._ A sor ```</head>```előtt adja hozzá a következő kódot:

```html
@RenderSection("Styles", required: false)
```

Most hozzáadjuk a mintatartalmat ehhez a webalkalmazáshoz. Nyissa meg _a Nézetek\Kezdőlap\Index.cshtml fájlt,_ és cserélje le az összes automatikusan létrehozott kódot erre a mintára:

```html
@{
    ViewData["Title"] = "Immersive Reader C# Quickstart";
}

@section Styles {
    <style type="text/css">
        .immersive-reader-button {
            background-color: white;
            margin-top: 5px;
            border: 1px solid black;
            float: right;
        }
    </style>
}

<div class="container">
    <button class="immersive-reader-button" data-button-style="iconAndText" data-locale="en"></button>

    <h1 id="ir-title">About Immersive Reader</h1>
    <div id="ir-content" lang="en-us">
        <p>
            Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences.
            The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader
            <ul>
                <li>
                    Shows content in a minimal reading view
                </li>
                <li>
                    Displays pictures of commonly used words
                </li>
                <li>
                    Highlights nouns, verbs, adjectives, and adverbs
                </li>
                <li>
                    Reads your content out loud to you
                </li>
                <li>
                    Translates your content into another language
                </li>
                <li>
                    Breaks down words into syllables
                </li>
            </ul>
        </p>
        <h3>
            The Immersive Reader is available in many languages.
        </h3>
        <p lang="es-es">
            El Lector inmersivo está disponible en varios idiomas.
        </p>
        <p lang="zh-cn">
            沉浸式阅读器支持许多语言
        </p>
        <p lang="de-de">
            Der plastische Reader ist in vielen Sprachen verfügbar.
        </p>
        <p lang="ar-eg" dir="rtl" style="text-align:right">
            يتوفر \"القارئ الشامل\" في العديد من اللغات.
        </p>
    </div>
</div>
```

Figyelje meg, hogy az egész szöveg nek van **egy lang** attribútuma, amely leírja a szöveg nyelveit. Ez az attribútum segít a Magával ragadó olvasó nak a megfelelő nyelvi és nyelvtani funkciók biztosításában.

## <a name="add-javascript-to-handle-launching-the-immersive-reader"></a>Add JavaScript kezelni indít a magával ragadó olvasó

A Magával ragadó olvasó könyvtár olyan funkciókat kínál, mint a Magával ragadó olvasó elindítása és a Magával ragadó olvasó gombok renderelése. Tudjon meg többet [itt](https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference).

A _Nézetek\Kezdőlap\Index.cshtml_oldal alján adja meg a következő kódot:

```html
@section Scripts
{
    <script src="https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js"></script>
    <script>
        function getTokenAndSubdomainAsync() {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "@Url.Action("GetTokenAndSubdomain", "Home")",
                    type: "GET",
                    success: function (data) {
                        if (data.error) {
                            reject(data.error);
                        } else {
                            resolve(data);
                        }
                    },
                    error: function (err) {
                        reject(err);
                    }
                });
            });
        }
    
        $(".immersive-reader-button").click(function () {
            handleLaunchImmersiveReader();
        });
    
        function handleLaunchImmersiveReader() {
            getTokenAndSubdomainAsync()
                .then(function (response) {
                    const token = response["token"];
                    const subdomain = response["subdomain"];
    
                    // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/en-us/azure/cognitive-services/immersive-reader/reference#chunk
                    const data = {
                        title: $("#ir-title").text(),
                        chunks: [{
                            content: $("#ir-content").html(),
                            mimeType: "text/html"
                        }]
                    };
    
                    // Learn more about options https://docs.microsoft.com/en-us/azure/cognitive-services/immersive-reader/reference#options
                    const options = {
                        "onExit": exitCallback,
                        "uiZIndex": 2000
                    };
    
                    ImmersiveReader.launchAsync(token, subdomain, data, options)
                        .catch(function (error) {
                            alert("Error in launching the Immersive Reader. Check the console.");
                            console.log(error);
                        });
                })
                .catch(function (error) {
                    alert("Error in getting the Immersive Reader token and subdomain. Check the console.");
                    console.log(error);
                });
        }
    
        function exitCallback() {
            console.log("This is the callback function. It is executed when the Immersive Reader closes.");
        }
    </script>
}
```

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

A menüsorban válassza a **Debug > Start Debugging (Hibakeresés) lehetőséget,** vagy nyomja le az **F5** billentyűt az alkalmazás elindításához.

A böngészőben a következőket kell látnia:

![Mintaalkalmazás](./media/quickstart-csharp/4-buildapp.png)

## <a name="launch-the-immersive-reader"></a>Indítsa el a magával ragadó olvasót

Ha a "Magával ragadó olvasó" gombra kattint, látni fogja a Magával ragadó olvasót, amelyet az oldalon lévő tartalommal indít.

![Modern olvasó](./media/quickstart-csharp/5-viewimmersivereader.png)

## <a name="next-steps"></a>További lépések

* Tekintse meg a [Node.js rövid útmutatót,](./quickstart-nodejs.md) hogy mi mást tehet a Magával ragadó Reader SDK segítségével Node.js
* Tekintse meg a [Python oktatóanyag,](./tutorial-python.md) hogy mi mást tehet ünk a magával ragadó Reader SDK python használatával
* Tekintse meg az [iOS bemutató,](./tutorial-ios-picture-immersive-reader.md) hogy mi mást tehetünk a magával ragadó Reader SDK swift
* Fedezze fel a [magával ragadó Reader SDK-t](https://github.com/microsoft/immersive-reader-sdk) és a [magával ragadó Reader SDK-referenciát](./reference.md)