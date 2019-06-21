---
title: 'Oktatóanyag: Indítsa el a ragadó Reader (Node.js)'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban egy Node.js-alkalmazás, amely elindítja a ragadó olvasó fog létrehozni.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: a6300d0233f222f26dc018136038c9fe96ecca84
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296736"
---
# <a name="tutorial-launch-the-immersive-reader-nodejs"></a>Oktatóanyag: Indítsa el a ragadó Reader (Node.js)

Az a [áttekintése](./overview.md), mi az a ragadó olvasó megismerkedett a, és hogyan valósítja meg olvasó szövegértést javítása nyelvi tanulók, az újonnan felbukkanó olvasók mind a tanulói tanulási különbségek a bevált módszereket. Ez az oktatóanyag bemutatja, hogyan hozhat létre egy Node.js-webalkalmazás, amely elindítja a ragadó olvasó. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Express Node.js-webalkalmazás létrehozása
> * Hozzáférési jogkivonat beszerzése
> * Indítsa el a ragadó olvasó minta tartalommal
> * Válassza ki a nyelvet a tartalom
> * Válassza ki a nyelvet a ragadó olvasó felület
> * Indítsa el a ragadó olvasó matematikai tartalommal

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Egy előfizetési kulcsot ragadó olvasó. Itt igényelhet a következő [ezek az utasítások](https://docs.microsoft.com/en-us/azure/cognitive-services/cognitive-services-apis-create-account).
* [NODE.js](https://nodejs.org/) és [Yarn](https://yarnpkg.com)
* Például az integrált fejlesztői Környezetig [Visual Studio Code-ot](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Express Node.js-webalkalmazás létrehozása

A Node.js webalkalmazás létrehozása a `express-generator` eszközt.

```bash
npm install express-generator -g
express --view=pug myapp
cd myapp
```

Yarn-függőségek telepítéséhez, és adja hozzá a függőségek `request` és `dotenv`, az oktatóanyag későbbi részében lesz használható.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="acquire-an-access-token"></a>Hozzáférési jogkivonat beszerzése

Következő lépésként írja meg a háttérrendszeri API használatával az előfizetési kulcs hozzáférési jogkivonatot beolvasni. Szükség van az előfizetési kulcs és a végpont a következő lépéssel. Ezt az információt, annak https://azure.microsoft.com/try/cognitive-services/my-apis/.

Miután az előfizetési kulcs és a végpont, hozzon létre egy új fájlt _.env_, és illessze be a következő kódot, és cserélje le `{YOUR_SUBSCRIPTION_KEY}` és `{YOUR_ENDPOINT}` az előfizetési kulcs és a végpontot, illetve.

```text
SUBSCRIPTION_KEY={YOUR_SUBSCRIPTION_KEY}
ENDPOINT={YOUR_ENDPOINT}
```

Mindenképpen nem véglegesíteni ezt a fájlt a forrásvezérlőben, mert tartalmaz, amely nem készített nyilvános titkok.

Ezután nyissa meg a _app.js_ és a fájl elejéhez adja hozzá a következő. Ez betölti az előfizetési kulcs és a végpont környezeti változókként csomópont.

```javascript
require('dotenv').config();
```

Nyissa meg a _routes\index.js_ tetején található a fájl importálása fájl- és a következő:

```javascript
var request = require('request');
```

Ezután adja hozzá a következő kódot közvetlenül a sor alá. Ez a kód létrehozza egy API-végpont használatával az előfizetési kulcs hozzáférési jogkivonatot szerez be, és ezután az adott jogkivonatot ad vissza.

```javascript
router.get('/token', function(req, res, next) {
  request.post({
    headers: {
        'Ocp-Apim-Subscription-Key': process.env.SUBSCRIPTION_KEY,
        'content-type': 'application/x-www-form-urlencoded'
    },
    url: process.env.ENDPOINT + '/issueToken'
  },
  function(err, resp, token) {
    return res.send(token);
  });
});
```

Az API-végpont védelméhez mögött valamilyen hitelesítés (például [OAuth](https://oauth.net/2/)); hogy van-e munkahelyi ebben az oktatóanyagban hatókörén kívül esik.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Indítsa el a ragadó olvasó minta tartalommal

1. Nyissa meg _views\layout.pug_, és adja hozzá a következő kódot alatt a `head` címke, mielőtt a `body` címke. Ezek `script` betölteni a címkéket a [ragadó olvasó SDK](https://github.com/Microsoft/immersive-reader-sdk) és jQuery.

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js')
    script(src='https://code.jquery.com/jquery-3.3.1.min.js')
    ```

2. Nyissa meg _views\index.pug_, és cserélje le annak tartalmát az alábbira. Ez a kód tölti fel az oldal néhány minta tartalommal, és hozzáad egy gombot, amely elindítja a ragadó olvasó.

    ```pug
    extends layout

    block content
      h2(id='title') Geography
      p(id='content') The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers.
      div(class='immersive-reader-button' data-button-style='iconAndText' data-locale='en-US' onclick='launchImmersiveReader()')
      script.
        function launchImmersiveReader() {
          // First, get a token using our /token endpoint
          $.ajax('/token', { success: token => {
            // Second, grab the content from the page
            const content = {
              title: document.getElementById('title').innerText,
              chunks: [ {
                content: document.getElementById('content').innerText + '\n\n',
                lang: 'en'
              } ]
            };

            // Third, launch the Immersive Reader
            ImmersiveReader.launchAsync(token, null, content);
          }});
        }
    ```

3. A web app most már készen áll. Indítsa el az alkalmazás futtatásával:

    ```bash
    npm start
    ```

4. Nyissa meg a böngészőt, és navigáljon a _http://localhost:3000_ . A fenti tartalomra a lapon kell megjelennie. Kattintson a **ragadó olvasó** gombra kattintva indítsa el a rendezvényein megismerkedhet a tartalom-olvasóhoz.

## <a name="specify-the-language-of-your-content"></a>Válassza ki a nyelvet a tartalom

Az ragadó olvasó rendelkezik a számos különböző nyelveihez támogatást. A nyelvi tartalom is megadhat az alábbi lépéseket követve.

1. Nyissa meg _views\index.pug_ , és adja hozzá a következő kódot a `p(id=content)` , amely az előző lépésben felvett kód. Ez a kód bizonyos tartalmakat spanyol tartalmat ad hozzá az oldalon.

    ```pug
    p(id='content-spanish') El estudio de las formas terrestres de la Tierra se llama geografía física. Los accidentes geográficos pueden ser montañas y valles. También pueden ser glaciares, lagos o ríos.
    ```

2. A JavaScript-kódba, adja hozzá a következő, a hívást a fenti `ImmersiveReader.launchAsync`. Ez a kód a spanyol tartalom ragadó olvasóba továbbítja.

    ```pug
    content.chunks.push({
      content: document.getElementById('content-spanish').innerText + '\n\n',
      lang: 'es'
    });
    ```

3. Navigáljon a _http://localhost:3000_ újra. A spanyol szöveg azt kell látnia, az oldalon, és kattintva **ragadó olvasó**, ragadó olvasó is megjelennek.

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>Válassza ki a nyelvet a ragadó olvasó felület

Alapértelmezés szerint a ragadó olvasó felület nyelve megegyezik a webböngésző nyelvi beállításai. A következő kóddal is megadhatja a ragadó olvasó felület nyelvét.

1. A _views\index.pug_, cserélje le a hívást `ImmersiveReader.launchAsync(token, content)` az alábbi kódra.

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, null, content, options);
    ```

2. Navigáljon a _http://localhost:3000_ . Ha elindítja a ragadó olvasó, a felület francia nyelven jelenik meg.

## <a name="launch-the-immersive-reader-with-math-content"></a>Indítsa el a ragadó olvasó matematikai tartalommal

Használatával a rendezvényein megismerkedhet a Reader matematikai tartalmat is felvehet [MathML](https://developer.mozilla.org/en-US/docs/Web/MathML).

1. Módosítsa _views\index.pug_ fent a hívást a következő kódot tartalmazza `ImmersiveReader.launchAsync`:

    ```javascript
    const mathML = '<math xmlns="http://www.w3.org/1998/Math/MathML" display="block"> \
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

2. Navigáljon a _http://localhost:3000_ . Ha elindítja a ragadó olvasó, és görgessen le, látni fogja a matematika a képlet.

## <a name="next-steps"></a>További lépések

* Fedezze fel a [ragadó olvasó SDK](https://github.com/Microsoft/immersive-reader-sdk) és a [ragadó olvasó SDK-referencia](./reference.md)
* Kódminták a nézeten [GitHub](https://github.com/microsoft/immersive-reader-sdk/samples/advanced-csharp)