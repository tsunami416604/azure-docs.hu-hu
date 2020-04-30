---
title: 'Oktatóanyag: több, magával ragadó olvasó erőforrásainak integrálása'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban egy Node. js-alkalmazást fog létrehozni, amely a sokrétű olvasót több, az olvasó erőforrásainak használatával indítja el.
author: skamal
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: skamal
ms.openlocfilehash: f68112095bc8a8fd9bcc1bd67ff77827d6d00fd7
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195621"
---
# <a name="tutorial-integrate-multiple-immersive-reader-resources"></a>Oktatóanyag: több, magával ragadó olvasó erőforrásainak integrálása

Az [Áttekintés](./overview.md)során megtanulta, hogy a magával ragadó olvasó Hogyan valósítja meg a bevált technikákat a nyelvtanulás, a feltörekvő olvasók és a tanulók tanulási különbségekkel való megismerésének javításához. A [Node. js](./quickstart-nodejs.md)rövid útmutatójában megtanulta, hogyan használhatja a lebilincselő olvasót egyetlen erőforrással. Ez az oktatóanyag azt ismerteti, hogyan integrálható egyszerre több, az olvasóhoz tartozó erőforrás ugyanabban az alkalmazásban. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Több alámerülés-olvasó erőforrás létrehozása egy meglévő erőforráscsoport alatt
> * A sokrétű olvasó elindítása több erőforrás használatával

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

* A rövid [útmutató segítségével](./quickstart-nodejs.md) hozzon létre egy webalkalmazást, amely elindítja a NodeJS-mel ellátott olvasót. Ebben a rövid útmutatóban egy egyszerű olvasó erőforrást konfigurál. Ezen az oktatóanyagon felül fogunk építeni.

## <a name="create-the-immersive-reader-resources"></a>A lebilincselő olvasó erőforrásainak létrehozása

Az [alábbi útmutatást](./how-to-create-immersive-reader.md) követve hozzon létre minden magától elolvasó erőforrást. A **create-ImmersiveReaderResource** parancsfájl `ResourceName` `ResourceSubdomain`a, a és `ResourceLocation` a paramétert adja meg. Ezeknek egyedieknek kell lenniük minden létrehozott erőforráshoz. A többi paraméternek azonosnak kell lennie, mint amit az első, az olvasói erőforrás beállításakor használt. Így minden erőforrás ugyanahhoz az Azure-erőforráscsoporthoz és Azure AD-alkalmazáshoz is csatolható.

Az alábbi példa bemutatja, hogyan hozhat létre két erőforrást, egyet a WestUS és egy másikat a EastUS-ben. Figyelje meg `ResourceName`a, `ResourceSubdomain`a és `ResourceLocation`a egyedi értékeit.

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

## <a name="add-resources-to-environment-configuration"></a>Erőforrások hozzáadása a környezeti konfigurációhoz

A rövid útmutatóban létrehozott egy környezeti konfigurációs fájlt, amely tartalmazza a `TenantId`, `ClientId` `ClientSecret`, és `Subdomain` paramétereket. Mivel az összes erőforrás ugyanazt az Azure ad-alkalmazást használja, a `TenantId`, `ClientId`a és `ClientSecret`a esetében is ugyanazokat az értékeket használhatja. Az egyetlen szükséges módosítás az egyes altartományok listázása az egyes erőforrásokhoz.

Az új __. env__ fájlnak ekkor a következőhöz hasonlóan kell kinéznie:

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN_WUS={YOUR_WESTUS_SUBDOMAIN}
SUBDOMAIN_EUS={YOUR_EASTUS_SUBDOMAIN}
```

Ügyeljen arra, hogy ne véglegesítse ezt a fájlt a verziókövetés során, mert olyan titkos kulcsokat tartalmaz, amelyeket nem kell nyilvánosságra hozni.

A következő lépés a több erőforrás támogatásához létrehozott _routes\index.js_ -fájl módosítása. Cserélje le a tartalmát a következő kódra.

Ahogy korábban is, ez a kód olyan API-végpontot hoz létre, amely Azure AD-hitelesítési tokent vásárol az egyszerű szolgáltatásnév jelszavával. Ezúttal lehetővé teszi, hogy a felhasználó megadjon egy erőforrás-helyet, és lekérdezési paraméterként adja át azt. Ezután egy olyan objektumot ad vissza, amely tartalmazza a tokent és a megfelelő altartományt.

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

A **getimmersivereaderlaunchparams** API-végpontot biztonságossá kell tennie valamilyen hitelesítési módszer (például [OAuth](https://oauth.net/2/)) mögött, hogy megakadályozza a jogosulatlan felhasználók számára a jogkivonatok beszerzését a felhasználatlan olvasó szolgáltatás és a számlázás során. Ez a munka meghaladja az oktatóanyag hatókörét.

## <a name="launch-the-immersive-reader-with-sample-content"></a>A részletes olvasó a minta tartalmának elindítása

1. Nyissa meg a _views\index.PUG_, és cserélje le a tartalmát a következő kódra. Ez a kód feltölti a lapot néhány minta tartalommal, és két, a magával ragadó olvasót elindító gombot hoz létre. Az egyik a EastUS-erőforrás magával ragadó olvasójának elindításához, egy másik pedig a WestUS-erőforráshoz.

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

3. A webalkalmazás most már készen áll. Indítsa el az alkalmazást a futtatásával:

    ```bash
    npm start
    ```

4. Nyissa meg a böngészőt `http://localhost:3000`, és navigáljon a gombra. A fenti tartalomnak a lapon kell megjelennie. Kattintson a EastUS- **Megölelő olvasó** gombra vagy a **WestUS magával ragadó olvasó** gombra, hogy a megfelelő erőforrásokkal elindítsa a magával ragadó olvasót.

## <a name="next-steps"></a>További lépések

* Ismerkedjen meg a [magára az olvasói SDK](https://github.com/microsoft/immersive-reader-sdk) -val és az [olvasói SDK-referenciával](./reference.md)
* Kód mintáinak megtekintése a [githubon](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)