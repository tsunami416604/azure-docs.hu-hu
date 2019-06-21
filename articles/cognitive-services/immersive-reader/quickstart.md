---
title: 'Gyors útmutató: Hozzon létre egy webalkalmazást, amely elindítja a ragadó Reader (C#)'
titlesuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban-webalkalmazás létrehozása az alapoktól, és adja hozzá a ragadó olvasók funkciói.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 77d95383c801038c256ccb2bf386ddf06048cf78
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67311807"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>Gyors útmutató: Hozzon létre egy webalkalmazást, amely elindítja a ragadó Reader (C#)

A [ragadó olvasó](https://www.onenote.com/learningtools) szélsőértékeket is beleértve megtervezett eszköz, amely megvalósítja a bevált módszereket olvasási szövegértést javítása érdekében.

Ebben a rövid útmutatóban-webalkalmazás létrehozása az alapoktól, és integrálhatja a ragadó olvasó a ragadó olvasó SDK-val. Egy teljes érhető el ebben a rövid, működő minta [Itt](https://github.com/Microsoft/immersive-reader-sdk/samples/quickstart-csharp).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads)
* Egy előfizetési kulcsot ragadó olvasó. Itt igényelhet a következő [ezek az utasítások](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).

## <a name="create-a-web-app-project"></a>Hozzon létre egy webalkalmazás-projektben

Hozzon létre egy új projektet a Visual Studióban, a Model-View-Controller beépített az ASP.NET Core-webalkalmazás-sablon használatával.

![Új projekt](./media/vswebapp.png)

![New ASP.NET Core Web Application](./media/vsmvc.png)

## <a name="acquire-an-access-token"></a>Hozzáférési jogkivonat beszerzése

Szükség van az előfizetési kulcs és a végpont a következő lépéssel. Az előfizetési kulcs az ragadó olvasó erőforrást az Azure Portal kulcsok lapján található. A végpont az Áttekintés oldal találhatja meg.

Kattintson a jobb gombbal a projektre a a _Megoldáskezelőben_ válassza **felhasználói titkok kezelése**. Ekkor megnyílik egy nevű fájlt _secrets.json_. Cserélje le a fájl tartalmát a következő, az előfizetési kulcs és a végpont ellátására, szükség esetén.

```json
{
  "SubscriptionKey": YOUR_SUBSCRIPTION_KEY,
  "Endpoint": YOUR_ENDPOINT
}
```

Nyissa meg _Controllers\HomeController.cs_, és cserélje le a `HomeController` osztályban az alábbi kódra.

```csharp
public class HomeController : Controller
{
    private readonly string SubscriptionKey;
    private readonly string Endpoint;

    public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
    {
        SubscriptionKey = configuration["SubscriptionKey"];
        Endpoint = configuration["Endpoint"];

        if (string.IsNullOrEmpty(Endpoint) || string.IsNullOrEmpty(SubscriptionKey))
        {
            throw new ArgumentNullException("Endpoint or subscriptionKey is null!");
        }
    }

    public IActionResult Index()
    {
        return View();
    }

    [Route("token")]
    public async Task<string> Token()
    {
        return await GetTokenAsync();
    }

    /// <summary>
    /// Exchange your Azure subscription key for an access token
    /// </summary>
    private async Task<string> GetTokenAsync()
    {
        using (var client = new System.Net.Http.HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", SubscriptionKey);
            using (var response = await client.PostAsync(Endpoint, null))
            {
                return await response.Content.ReadAsStringAsync();
            }
        }
    }
}
```

## <a name="add-sample-content"></a>Minta tartalom hozzáadása

Most néhány mintatartalmakat próbálhat kell hozzáadni a webalkalmazáshoz. Nyissa meg _Views\Home\Index.cshtml_ és az automatikusan létrehozott kód cserélje le ezt a mintát:

```html
<h1 id='title'>Geography</h1>
<span id='content'>
    <p>The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers. Landforms are sometimes called physical features. It is important for students to know about the physical geography of Earth. The seasons, the atmosphere and all the natural processes of Earth affect where people are able to live. Geography is one of a combination of factors that people use to decide where they want to live.</p>
</span>

<div class='immersive-reader-button' data-button-style='iconAndText' onclick='launchImmersiveReader()'></div>

@section scripts {
<script type='text/javascript' src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.1.js'></script>
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

    async function launchImmersiveReader() {
        const content = {
            title: document.getElementById('title').innerText,
            chunks: [ {
                content: document.getElementById('content').innerText,
                lang: 'en'
            } ]
        };

        const token = await getImmersiveReaderTokenAsync();
        ImmersiveReader.launchAsync(token, content, { uiZIndex: 1000000 });
    }
</script>
}
```

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

A menüsávban válassza **Debug > Start Debugging**, vagy nyomja le az **F5** az alkalmazás elindításához.

A böngészőben kell megjelennie:

![Mintaalkalmazás](./media/quickstart-result.png)

Ha a "Ragadó olvasó" gombra kattint, láthatja a ragadó olvasó elindítja a tartalom az oldalon.

![Modern olvasó](./media/quickstart-immersive-reader.png)

## <a name="next-steps"></a>További lépések

* Nézet a [oktatóanyag](./tutorial.md) megtekintheti, milyen egyéb teheti a ragadó olvasó SDK-val
* Fedezze fel a [ragadó olvasó SDK](https://github.com/Microsoft/immersive-reader-sdk) és a [ragadó olvasó SDK-referencia](./reference.md)