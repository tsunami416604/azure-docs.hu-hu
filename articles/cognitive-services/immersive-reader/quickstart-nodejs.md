---
title: 'Rövid útmutató: Hozzon létre egy webes alkalmazást, amely elindítja a Magával ragadó olvasót a Node.js'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban teljesen új webalkalmazást hozhat létre, és hozzáadhatja a Magával ragadó Olvasó API-funkciót.
author: pasta
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 01/14/2020
ms.author: pasta
ms.openlocfilehash: 749e75fed409632c613713a49154e4cd8dc265b3
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75946326"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-nodejs"></a>Rövid útmutató: Hozzon létre egy webes alkalmazást, amely elindítja a Magával ragadó olvasót (Node.js)

A [Magával ragadó olvasó](https://www.onenote.com/learningtools) egy inkluzívan tervezett eszköz, amely bevált technikákat valósít meg az olvasás megértésének javítása érdekében.

Ebben a rövid útmutatóban teljesen új webes alkalmazást hozhat létre, és integrálhatja a Magával ragadó olvasót a Magával ragadó olvasó SDK használatával. A rövid útmutató teljes munkamintája [itt](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-nodejs)érhető el.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

* Az Azure Active Directory-hitelesítéshez konfigurált magával ragadó reader-erőforrás. A beállításhoz kövesse [az alábbi utasításokat.](./how-to-create-immersive-reader.md) A környezeti tulajdonságok konfigurálásakor szüksége lesz néhány itt létrehozott értékre. Mentse a munkamenet kimenetét egy szöveges fájlba későbbi használatra.
* [Node.js](https://nodejs.org/) és [fonal](https://yarnpkg.com)
* Egy IDE, mint például a [Visual Studio kód](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Node.js webalkalmazás létrehozása expressz elsajátítva

Hozzon létre egy Node.js webalkalmazást az `express-generator` eszközzel.

```bash
npm install express-generator -g
express --view=pug quickstart-nodejs
cd quickstart-nodejs
```

Telepítse a fonalfüggőségeket, és `request` `dotenv`adja hozzá a függőségeket és a , amelyeket a program később a rövid útmutatóban fog használni.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="set-up-authentication"></a>Hitelesítés beállítása

### <a name="configure-authentication-values"></a>Hitelesítési értékek konfigurálása

Hozzon létre egy új fájlt, amelynek neve _.env_ a projekt gyökerében. Illessze be a következő kódot, amely a Immersive Reader erőforrás létrehozásakor megadott értékeket adja meg.
Ne adjon meg idézőjeleket vagy a "{" és "}" karaktereket.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Ügyeljen arra, hogy ne véglegesítse ezt a fájlt a forrásvezérlőbe, mivel olyan titkos kulcsokat tartalmaz, amelyeket nem szabad nyilvánosságra hozni.

Ezután nyissa meg _az app.js fájlt,_ és adja hozzá a következőket a fájl tetejéhez. Ezzel betölti az .env fájlban környezeti változóként definiált tulajdonságokat a csomópontba.

```javascript
require('dotenv').config();
```

### <a name="update-the-router-to-acquire-the-token"></a>Frissítse az útválasztót a token beszerzéséhez
Nyissa meg a _routes\index.js_ fájlt, és cserélje le az automatikusan létrehozott kódot a következő kódra.

Ez a kód létrehoz egy API-végpontot, amely beszerzi az Azure AD hitelesítési jogkivonatot az egyszerű szolgáltatás jelszavával. Az altartományt is lekéri. Ezután egy jogkivonatot és altartományt tartalmazó objektumot ad vissza.

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
            var subdomain = process.env.SUBDOMAIN;
            return res.send({token, subdomain});
        });
    } catch (err) {
        console.log(err);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

A **GetTokenAndSubdomain** API-végpontot valamilyen hitelesítési forma (például [OAuth)](https://oauth.net/2/)mögött kell biztosítani annak érdekében, hogy illetéktelen felhasználók ne szerezzenek be jogkivonatokat a Magával ragadó olvasó szolgáltatás és a számlázás ellen; hogy a munka túlmutat a rövid útmutató hatókörén.

## <a name="add-sample-content"></a>Mintatartalom hozzáadása

Most hozzáadjuk a mintatartalmat ehhez a webalkalmazáshoz. Nyissa meg _a nézeteket\index.pug,_ és cserélje le az automatikusan létrehozott kódot erre a mintára:

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
        button(class="immersive-reader-button" data-button-style="iconAndText" data-locale="en")

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
  function getTokenAndSubdomainAsync() {
        return new Promise(function (resolve, reject) {
            $.ajax({
                url: "/GetTokenAndSubdomain",
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


Figyelje meg, hogy az egész szöveg nek van **egy lang** attribútuma, amely leírja a szöveg nyelveit. Ez az attribútum segít a Magával ragadó olvasó nak a megfelelő nyelvi és nyelvtani funkciók biztosításában.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

A webalkalmazás unk készen áll. Indítsa el az alkalmazást a következő futtatásával:

```bash
npm start
```

Nyissa meg a _http://localhost:3000_böngészőt, és keresse meg a t. A következőnek kell megjelennie:

![Mintaalkalmazás](./media/quickstart-nodejs/1-buildapp.png)

## <a name="launch-the-immersive-reader"></a>Indítsa el a magával ragadó olvasót

Ha a "Magával ragadó olvasó" gombra kattint, látni fogja a Magával ragadó olvasót, amelyet az oldalon lévő tartalommal indít.

![Modern olvasó](./media/quickstart-nodejs/2-viewimmersivereader.png)

## <a name="next-steps"></a>További lépések

* Fedezze fel a [magával ragadó Reader SDK-t](https://github.com/microsoft/immersive-reader-sdk) és a [magával ragadó Reader SDK-referenciát](./reference.md)