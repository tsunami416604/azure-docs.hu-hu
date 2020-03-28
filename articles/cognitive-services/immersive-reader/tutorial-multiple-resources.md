---
title: 'Oktatóanyag: Több magával ragadó olvasóerőforrás integrálása'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban egy Node.js alkalmazást hoz létre, amely elindítja a Magával ragadó olvasót több magával ragadó olvasóerőforrás használatával.
author: skamal
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: skamal
ms.openlocfilehash: 3912d55b13f3977818e8d898efa651ffeb1a798a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76046274"
---
# <a name="tutorial-integrate-multiple-immersive-reader-resources"></a>Oktatóanyag: Több magával ragadó olvasóerőforrás integrálása

Az [áttekintésben](./overview.md)megtanulta, hogy mi a magával ragadó olvasó, és hogyan valósítja meg a bevált technikákat, hogy javítsa az olvasás megértését a nyelvtanulók, a feltörekvő olvasók és a tanulási különbségekkel rendelkező diákok számára. A [Node.js rövid útmutatóban](./quickstart-nodejs.md)megtanulta, hogyan használhatja a Immersive Reader-t egyetlen erőforrással. Ez az oktatóanyag bemutatja, hogyan integrálható több Immersive Reader erőforrás ugyanabban az alkalmazásban. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Több magával ragadó reader-erőforrás létrehozása meglévő erőforráscsoportban
> * Indítsa el a Magával ragadó olvasót több erőforrás használatával

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

* Kövesse a [rövid útmutatót,](./quickstart-nodejs.md) és hozzon létre egy webes alkalmazást, amely elindítja a Magával ragadó olvasót a NodeJS-sel. Ebben a rövid útmutatóban egyetlen Immersive Reader erőforrást konfigurál. Fogunk építeni a tetején, hogy ez a bemutató.

## <a name="create-the-immersive-reader-resources"></a>A Magával ragadó olvasó erőforrásainak létrehozása

Kövesse [ezeket](./how-to-create-immersive-reader.md) az utasításokat az egyes Immersive Reader-erőforrások létrehozásához. A **Create-ImmersiveReaderResource** `ResourceName`parancsfájl `ResourceSubdomain`, `ResourceLocation` és paraméterként rendelkezik. Ezeknek minden létrehozott erőforrásesetében egyedinek kell lenniük. A fennmaradó paramétereknek meg kell egyeznie az első Immersive Reader erőforrás beállításakor használt paraméterekével. Így minden erőforrás ugyanahhoz az Azure-erőforráscsoporthoz és az Azure AD-alkalmazáshoz kapcsolható.

Az alábbi példa bemutatja, hogyan hozhat létre két erőforrást, az egyik a WestUS-ban, a másik az EastUS-ban. Figyelje meg `ResourceName`a `ResourceSubdomain`és `ResourceLocation`a egyedi értékét.

```azurepowershell-interactive
Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_wus `
  -ResourceSubdomain resource-subdomain-wus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation westus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>

Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_eus `
  -ResourceSubdomain resource-subdomain-eus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation eastus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>
```

## <a name="add-resources-to-environment-configuration"></a>Erőforrások hozzáadása a környezet konfigurációjához

A rövid útmutatóban létrehozott egy környezeti konfigurációs `ClientId` `ClientSecret`fájlt, `Subdomain` amely a `TenantId`, , , és paramétereket tartalmazza. Mivel az összes erőforrás ugyanazt az Azure AD-alkalmazást használja, ugyanazokat az értékeket használhatjuk a `TenantId` `ClientId`, és `ClientSecret`a hoz. Az egyetlen módosítás, amit el kell tenni, hogy felsorolja az egyes altartományok at minden erőforráshoz.

Az új __.env__ fájl most a következőhez hasonlónak kell lennie:

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN_WUS={YOUR_WESTUS_SUBDOMAIN}
SUBDOMAIN_EUS={YOUR_EASTUS_SUBDOMAIN}
```

Ügyeljen arra, hogy ne véglegesítse ezt a fájlt a forrásvezérlőbe, mivel olyan titkos kulcsokat tartalmaz, amelyeket nem szabad nyilvánosságra hozni.

Ezután módosítjuk a _routes\index.js_ fájlt, amelyet a több erőforrás unk támogatása érdekében hoztunk létre. Cserélje le a tartalmát a következő kódra.

A kód hoz létre egy API-végpontot, amely beszerzi az Azure AD hitelesítési jogkivonatot az egyszerű szolgáltatás jelszavával. Ez úttal lehetővé teszi a felhasználó számára, hogy adjon meg egy erőforrás helyét, és adja át a lekérdezési paraméterként. Ezután egy olyan objektumot ad vissza, amely tartalmazza a jogkivonatot és a megfelelő altartományt.

```javascript
var express = require('express');
var router = express.Router();
var request = require('request');

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

router.get('/GetTokenAndSubdomain', function(req, res) {
    try {
        request.post({
            headers: {
                'content-type': 'application/x-www-form-urlencoded'
            },
            url: `https://login.windows.net/${process.env.TENANT_ID}/oauth2/token`,
            form: {
                grant_type: 'client_credentials',
                client_id: process.env.CLIENT_ID,
                client_secret: process.env.CLIENT_SECRET,
                resource: 'https://cognitiveservices.azure.com/'
            }
        },
        function(err, resp, tokenResult) {
            if (err) {
                console.log(err);
                return res.status(500).send('CogSvcs IssueToken error');
            }

            var tokenResultParsed = JSON.parse(tokenResult);

            if (tokenResultParsed.error) {
                console.log(tokenResult);
                return res.send({error :  "Unable to acquire Azure AD token. Check the debugger for more information."})
            }

            var token = tokenResultParsed.access_token;

            var subdomain = "";
            var region = req.query && req.query.region;
            switch (region) {
                case "eus":
                    subdomain = process.env.SUBDOMAIN_EUS
                    break;
                case "wus":
                default:
                    subdomain = process.env.SUBDOMAIN_WUS
            }

            return res.send({token, subdomain});
        });
    } catch (err) {
        console.log(err);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

A **getimmersivereaderlaunchparams** API-végpontot valamilyen hitelesítési forma (például [OAuth)](https://oauth.net/2/)mögött kell biztosítani annak érdekében, hogy illetéktelen felhasználók ne szerezzenek be jogkivonatokat a Immersive Reader szolgáltatás és a számlázás ellen; hogy a munka túlmutat a bemutató.

## <a name="launch-the-immersive-reader-with-sample-content"></a>A Magával ragadó olvasó elindítása mintatartalommal

1. Nyissa meg _a views\index.pug_, és cserélje le annak tartalmát a következő kódra. Ez a kód feltölti az oldalt néhány mintatartalommal, és két gombot ad hozzá, amelyek elindítják a Magával ragadó olvasót. Az egyik a Immersive Reader az EastUS erőforráshoz, a másik pedig a WestUS erőforrás.

    ```pug
    doctype html
    html
        head
            title Immersive Reader Quickstart Node.js

            link(rel='stylesheet', href='https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css')

            // A polyfill for Promise is needed for IE11 support.
            script(src='https://cdn.jsdelivr.net/npm/promise-polyfill@8/dist/polyfill.min.js')

            script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js')
            script(src='https://code.jquery.com/jquery-3.3.1.min.js')

            style(type="text/css").
                .immersive-reader-button {
                background-color: white;
                margin-top: 5px;
                border: 1px solid black;
                float: right;
                }
        body
            div(class="container")
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("wus")') WestUS Immersive Reader
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("eus")') EastUS Immersive Reader

                h1(id="ir-title") About Immersive Reader
                div(id="ir-content" lang="en-us")
                p Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences. The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader

                    ul
                        li Shows content in a minimal reading view
                        li Displays pictures of commonly used words
                        li Highlights nouns, verbs, adjectives, and adverbs
                        li Reads your content out loud to you
                        li Translates your content into another language
                        li Breaks down words into syllables

                h3 The Immersive Reader is available in many languages.

                p(lang="es-es") El Lector inmersivo está disponible en varios idiomas.
                p(lang="zh-cn") 沉浸式阅读器支持许多语言
                p(lang="de-de") Der plastische Reader ist in vielen Sprachen verfügbar.
                p(lang="ar-eg" dir="rtl" style="text-align:right") يتوفر \"القارئ الشامل\" في العديد من اللغات.

    script(type="text/javascript").
    function getTokenAndSubdomainAsync(region) {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "/GetTokenAndSubdomain",
                    type: "GET",
                    data: {
                        region: region
                    },
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

        function handleLaunchImmersiveReader(region) {
            getTokenAndSubdomainAsync(region)
                .then(function (response) {
                    const token = response["token"];
                    const subdomain = response["subdomain"];
                    // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#chunk
                    const data = {
                        title: $("#ir-title").text(),
                        chunks: [{
                            content: $("#ir-content").html(),
                            mimeType: "text/html"
                        }]
                    };
                    // Learn more about options https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#options
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
    ```

3. A webalkalmazás unk készen áll. Indítsa el az alkalmazást a következő futtatásával:

    ```bash
    npm start
    ```

4. Nyissa meg a [http://localhost:3000](http://localhost:3000)böngészőt, és keresse meg a t. A fenti tartalomnak látnia kell az oldalon. Kattintson az **EastUS Immersive Reader** gombra vagy a **WestUS Immersive Reader** gombra, hogy elindítsa a Magával ragadó olvasót a megfelelő erőforrások használatával.

## <a name="next-steps"></a>További lépések

* Fedezze fel a [magával ragadó Reader SDK-t](https://github.com/microsoft/immersive-reader-sdk) és a [magával ragadó Reader SDK-referenciát](./reference.md)
* Kódminták megtekintése a [GitHubon](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)