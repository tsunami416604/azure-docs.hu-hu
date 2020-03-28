---
title: 'Oktatóanyag: Indítsa el a magával ragadó olvasót a Node.js használatával'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban egy Node.js alkalmazást hoz létre, amely elindítja a Magával ragadó olvasót.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 139dd2ebdabbc91a6de3b0a1eb921b110d47c3f3
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842027"
---
# <a name="tutorial-launch-the-immersive-reader-nodejs"></a>Oktatóanyag: Indítsa el a magával ragadó olvasót (Node.js)

Az [áttekintésben](./overview.md)megtanulta, hogy mi a magával ragadó olvasó, és hogyan valósítja meg a bevált technikákat, hogy javítsa az olvasás megértését a nyelvtanulók, a feltörekvő olvasók és a tanulási különbségekkel rendelkező diákok számára. Ez az oktatóanyag bemutatja, hogyan hozhat létre egy Node.js webes alkalmazást, amely elindítja a Magával ragadó olvasót. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Node.js webalkalmazás létrehozása expressz elsajátítva
> * Hozzáférési jogkivonat beszerzése
> * A Magával ragadó olvasó elindítása mintatartalommal
> * A tartalom nyelvének megadása
> * Adja meg a Magával ragadó olvasó felület nyelvét
> * A magával ragadó olvasó elindítása matematikai tartalommal

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

* Az Azure Active Directory-hitelesítéshez konfigurált magával ragadó reader-erőforrás. A beállításhoz kövesse [az alábbi utasításokat.](./how-to-create-immersive-reader.md) A környezeti tulajdonságok konfigurálásakor szüksége lesz néhány itt létrehozott értékre. Mentse a munkamenet kimenetét egy szöveges fájlba későbbi használatra.
* [Node.js](https://nodejs.org/) és [fonal](https://yarnpkg.com)
* Egy IDE, mint például a [Visual Studio kód](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Node.js webalkalmazás létrehozása expressz elsajátítva

Hozzon létre egy Node.js webalkalmazást az `express-generator` eszközzel.

```bash
npm install express-generator -g
express --view=pug myapp
cd myapp
```

Telepítse a fonalfüggőségeket, és `request` `dotenv`adja hozzá a függőségeket és a , amelyeket az oktatóanyag későbbi részében fog használni.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Azure AD-hitelesítési jogkivonat beszerzése

Ezután írjon egy háttér-API-t egy Azure AD-hitelesítési jogkivonat lekéréséhez.

Szüksége van néhány értéket az Azure AD hitelesítési hitelesítési beállítás előfeltétele fenti lépés ebben a részben. Hivatkozzon vissza a munkamenetből mentett szövegfájlra.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Miután ezeket az értékeket, hozzon létre egy új fájlt nevű _.env_, és illessze be a következő kódot, amely az egyéni tulajdonság értékek felülről. Ne adjon meg idézőjeleket vagy a "{" és "}" karaktereket.

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

Nyissa meg a _routes\index.js_ fájlt, és cserélje le annak tartalmát a következő kódra.

Ez a kód létrehoz egy API-végpontot, amely beszerzi az Azure AD hitelesítési jogkivonatot az egyszerű szolgáltatás jelszavával. Az altartományt is lekéri. Ezután egy jogkivonatot és altartományt tartalmazó objektumot ad vissza.

```javascript
var request = require('request');
var express = require('express');
var router = express.Router();

router.get('/getimmersivereaderlaunchparams', function(req, res) {
    request.post ({
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
        function(err, resp, tokenResponse) {
                if (err) {
                    return res.status(500).send('CogSvcs IssueToken error');
                }

                const token = JSON.parse(tokenResponse).access_token;
                const subdomain = process.env.SUBDOMAIN;
                return res.send({token: token, subdomain: subdomain});
        }
  );
});

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

module.exports = router;

```

A **getimmersivereaderlaunchparams** API-végpontot valamilyen hitelesítési forma (például [OAuth)](https://oauth.net/2/)mögött kell biztosítani annak érdekében, hogy illetéktelen felhasználók ne szerezzenek be jogkivonatokat a Immersive Reader szolgáltatás és a számlázás ellen; hogy a munka túlmutat a bemutató.

## <a name="launch-the-immersive-reader-with-sample-content"></a>A Magával ragadó olvasó elindítása mintatartalommal

1. Nyissa meg _a nézeteket\layout.pug_, `head` és adja `body` hozzá a következő kódot a címke alá, a címke elé. Ezek `script` a címkék betöltik a [Magával ragadó Reader SDK](https://github.com/microsoft/immersive-reader-sdk) és jQuery.

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js')
    script(src='https://code.jquery.com/jquery-3.3.1.min.js')
    ```

2. Nyissa meg _a views\index.pug_, és cserélje le annak tartalmát a következő kódra. Ez a kód feltölti az oldalt néhány mintatartalommal, és hozzáad egy gombot, amely elindítja a Magával ragadó olvasót.

    ```pug
    extends layout

    block content
          h2(id='title') Geography
          p(id='content') The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers.
          div(class='immersive-reader-button' data-button-style='iconAndText' data-locale='en-US' onclick='launchImmersiveReader()')
          script.

            function getImmersiveReaderLaunchParamsAsync() {
                    return new Promise((resolve, reject) => {
                        $.ajax({
                                url: '/getimmersivereaderlaunchparams',
                                type: 'GET',
                                success: data => {
                                        resolve(data);
                                },
                                error: err => {
                                        console.log('Error in getting token and subdomain!', err);
                                        reject(err);
                                }
                        });
                    });
            }

            async function launchImmersiveReader() {
                    const content = {
                            title: document.getElementById('title').innerText,
                            chunks: [{
                                    content: document.getElementById('content').innerText + '\n\n',
                                    lang: 'en'
                            }]
                    };

                    const launchParams = await getImmersiveReaderLaunchParamsAsync();
                    const token = launchParams.token;
                    const subdomain = launchParams.subdomain;

                    ImmersiveReader.launchAsync(token, subdomain, content);
            }
    ```

3. A webalkalmazás unk készen áll. Indítsa el az alkalmazást a következő futtatásával:

    ```bash
    npm start
    ```

4. Nyissa meg a _http://localhost:3000_böngészőt, és keresse meg a t. A fenti tartalomnak látnia kell az oldalon. Kattintson a **Magával ragadó olvasó** gombra, hogy elindítsa a Magával ragadó olvasót a tartalommal.

## <a name="specify-the-language-of-your-content"></a>A tartalom nyelvének megadása

A Magával ragadó olvasó számos különböző nyelvet támogat. A tartalom nyelvét az alábbi lépések végrehajtásával adhatja meg.

1. Nyissa meg _a nézeteket\index.pug,_ és adja hozzá a következő kódot az `p(id=content)` előző lépésben hozzáadott címke alá. Ez a kód hozzáad néhány tartalmat spanyol tartalom az oldalon.

    ```pug
    p(id='content-spanish') El estudio de las formas terrestres de la Tierra se llama geografía física. Los accidentes geográficos pueden ser montañas y valles. También pueden ser glaciares, lagos o ríos.
    ```

2. A JavaScript-kódban adja hozzá a `ImmersiveReader.launchAsync`fenti következő t a híváshoz. Ez a kód továbbítja a spanyol tartalmat a Magával ragadó reader.

    ```pug
    content.chunks.push({
      content: document.getElementById('content-spanish').innerText + '\n\n',
      lang: 'es'
    });
    ```

3. Keresse _http://localhost:3000_ meg újra. Önnek kellene lát a Spanyol szöveg az oldalon, és ha ön kettyenés -ra **Alámerülés Olvasó**, ez akarat felmutat -ban Alámerülés Olvasó ugyancsak.

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>Adja meg a Magával ragadó olvasó felület nyelvét

Alapértelmezés szerint a Magával ragadó olvasó felület nyelve megegyezik a böngésző nyelvi beállításaival. A Immersive Reader felület nyelvét a következő kóddal is megadhatja.

1. A _views\index.pug mappában_ `ImmersiveReader.launchAsync(token, subdomain, content)` cserélje le a hívást az alábbi kódra.

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, subdomain, content, options);
    ```

2. Keresse _http://localhost:3000_meg a it. Amikor elindítja a Immersive Reader, a felület jelenik meg a francia.

## <a name="launch-the-immersive-reader-with-math-content"></a>A magával ragadó olvasó elindítása matematikai tartalommal

A [MathML](https://developer.mozilla.org/en-US/docs/Web/MathML)segítségével matematikai tartalmat is felvehet a Magával ragadó olvasóba.

1. A _nézetek\index.pug_ módosítása, hogy az `ImmersiveReader.launchAsync`a következő kódot tartalmazza a hívás felett:

    ```javascript
    const mathML = '<math xmlns="https://www.w3.org/1998/Math/MathML" display="block"> \
      <munderover> \
        <mo>∫</mo> \
        <mn>0</mn> \
        <mn>1</mn> \
      </munderover> \
      <mrow> \
        <msup> \
          <mi>x</mi> \
          <mn>2</mn> \
        </msup> \
        <mo>ⅆ</mo> \
        <mi>x</mi> \
      </mrow> \
    </math>';

    content.chunks.push({
      content: mathML,
      mimeType: 'application/mathml+xml'
    });
    ```

2. Keresse _http://localhost:3000_meg a it. Amikor elindítja a Magával ragadó olvasót, és az aljára görget, megjelenik a matematikai képlet.

## <a name="next-steps"></a>További lépések

* Fedezze fel a [magával ragadó Reader SDK-t](https://github.com/microsoft/immersive-reader-sdk) és a [magával ragadó Reader SDK-referenciát](./reference.md)
* Kódminták megtekintése a [GitHubon](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)
