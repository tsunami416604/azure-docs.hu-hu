---
title: 'Oktatóanyag: a részletes olvasó elindítása a Node. js használatával'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban létre fog hozni egy Node. js-alkalmazást, amely elindítja a magával ragadó olvasót.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 139dd2ebdabbc91a6de3b0a1eb921b110d47c3f3
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842027"
---
# <a name="tutorial-launch-the-immersive-reader-nodejs"></a>Oktatóanyag: a lebilincselő olvasó elindítása (node. js)

Az [Áttekintés](./overview.md)során megtanulta, hogy a magával ragadó olvasó Hogyan valósítja meg a bevált technikákat a nyelvtanulás, a feltörekvő olvasók és a tanulók tanulási különbségekkel való megismerésének javításához. Ez az oktatóanyag azt ismerteti, hogyan hozhat létre egy olyan Node. js-webalkalmazást, amely elindítja az olvasót. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Node. js-alapú Webalkalmazás létrehozása az Express-szel
> * Hozzáférési jogkivonat beszerzése
> * A részletes olvasó a minta tartalmának elindítása
> * Adja meg a tartalom nyelvét
> * A lebilincselő olvasó felület nyelvének meghatározása
> * A lebilincselő olvasó elindítása matematikai tartalommal

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* A Azure Active Directory hitelesítéshez konfigurált, magával ragadó olvasó erőforrás. A beállításhoz kövesse az [alábbi utasításokat](./how-to-create-immersive-reader.md) . A környezeti tulajdonságok konfigurálásakor itt létrehozott értékek némelyikére szüksége lesz. Mentse a munkamenet kimenetét szövegfájlba későbbi használatra.
* [Node. js](https://nodejs.org/) és [fonal](https://yarnpkg.com)
* IDE, például a [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Node. js-alapú Webalkalmazás létrehozása az Express-szel

Hozzon létre egy Node. js-webalkalmazást az `express-generator` eszközzel.

```bash
npm install express-generator -g
express --view=pug myapp
cd myapp
```

Telepítse a fonal-függőségeket, és vegyen fel függőségeket `request` és `dotenv`, amelyeket később az oktatóanyagban fog használni.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Azure AD-hitelesítési jogkivonat beszerzése

Ezután írja be a háttér-API-t egy Azure AD-hitelesítési jogkivonat lekéréséhez.

Ehhez a részhez az Azure AD Auth konfigurációjának előfeltétele lépésének néhány értékét kell megadnia. Nézze vissza az adott munkamenetből mentett szövegfájlt.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Ha már rendelkezik ezekkel az értékekkel, hozzon létre egy _. env_nevű új fájlt, és illessze be a következő kódot, és adja meg az egyéni tulajdonságértékek értékét a fentiek közül. Ne tartalmazzon idézőjeleket vagy a "{" és "}" karaktereket.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Ügyeljen arra, hogy ne véglegesítse ezt a fájlt a verziókövetés során, mert olyan titkos kulcsokat tartalmaz, amelyeket nem kell nyilvánosságra hozni.

Ezután nyissa meg az _app. js_ fájlt, és adja hozzá a következőt a fájl elejéhez. Ezzel betölti a. env fájlban definiált tulajdonságokat környezeti változókként a csomópontba.

```javascript
require('dotenv').config();
```

Nyissa meg a _routes\index.js_ fájlt, és cserélje le a tartalmát a következő kódra.

Ez a kód egy olyan API-végpontot hoz létre, amely Azure AD-hitelesítési jogkivonatot szerzi be a szolgáltatás egyszerű jelszava használatával. Az altartományt is lekéri. Ezután visszaadja a jogkivonatot és altartományt tartalmazó objektumot.

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

A **getimmersivereaderlaunchparams** API-végpontot biztonságossá kell tennie valamilyen hitelesítési módszer (például [OAuth](https://oauth.net/2/)) mögött, hogy megakadályozza a jogosulatlan felhasználók számára a jogkivonatok beszerzését a felhasználatlan olvasó szolgáltatás és a számlázás során. Ez a munka meghaladja az oktatóanyag hatókörét.

## <a name="launch-the-immersive-reader-with-sample-content"></a>A részletes olvasó a minta tartalmának elindítása

1. Nyissa meg a _views\layout.PUG_, és adja hozzá a következő kódot a `head` címke alatt a `body` címke előtt. Ezek a `script` címkék betöltik a [magától elolvasó SDK](https://github.com/microsoft/immersive-reader-sdk) -t és a jQuery-t.

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js')
    script(src='https://code.jquery.com/jquery-3.3.1.min.js')
    ```

2. Nyissa meg a _views\index.PUG_, és cserélje le a tartalmát a következő kódra. Ez a kód feltölti a lapot néhány minta tartalommal, és egy olyan gombot is felvesz, amely elindítja a magával ragadó olvasót.

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

3. A webalkalmazás most már készen áll. Indítsa el az alkalmazást a futtatásával:

    ```bash
    npm start
    ```

4. Nyissa meg a böngészőt, és navigáljon _http://localhost:3000_ . A fenti tartalomnak a lapon kell megjelennie. Kattintson a **magára az olvasó** gombra, hogy elindítsa a magával ragadó olvasót a tartalommal.

## <a name="specify-the-language-of-your-content"></a>Adja meg a tartalom nyelvét

A lebilincselő olvasó számos különböző nyelvet támogat. A tartalom nyelvét az alábbi lépésekkel adhatja meg.

1. Nyissa meg a _views\index.PUG_ , és adja hozzá az alábbi kódot az előző lépésben hozzáadott `p(id=content)` címke alá. Ez a kód egy spanyol tartalmat hoz létre a laphoz.

    ```pug
    p(id='content-spanish') El estudio de las formas terrestres de la Tierra se llama geografía física. Los accidentes geográficos pueden ser montañas y valles. También pueden ser glaciares, lagos o ríos.
    ```

2. A JavaScript-kódban adja hozzá a következőt a `ImmersiveReader.launchAsync`meghívásához. Ez a kód továbbítja a spanyol tartalmat a magával ragadó olvasónak.

    ```pug
    content.chunks.push({
      content: document.getElementById('content-spanish').innerText + '\n\n',
      lang: 'es'
    });
    ```

3. Navigáljon _http://localhost:3000_ újra. Meg kell jelennie a spanyol szövegnek az oldalon, és amikor a **magával ragadó olvasóra**kattint, megjelenik a magától megjelenő olvasó is.

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>A lebilincselő olvasó felület nyelvének meghatározása

Alapértelmezés szerint a magával ragadó olvasó felület nyelve megegyezik a böngésző nyelvi beállításaival. Megadhatja a magával ragadó olvasó felületének nyelvét is a következő kóddal.

1. A _views\index.PUG_-ben cserélje le a `ImmersiveReader.launchAsync(token, subdomain, content)`re irányuló hívást az alábbi kóddal.

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, subdomain, content, options);
    ```

2. Navigáljon _http://localhost:3000_ . A lebilincselő olvasó indításakor a felület francia nyelven jelenik meg.

## <a name="launch-the-immersive-reader-with-math-content"></a>A lebilincselő olvasó elindítása matematikai tartalommal

A [MathML](https://developer.mozilla.org/en-US/docs/Web/MathML)használatával a lebilincselő olvasóban matematikai tartalmakat is hozzáadhat.

1. Módosítsa a _views\index.PUG_ úgy, hogy a következő kódot tartalmazza a `ImmersiveReader.launchAsync`hívása felett:

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

2. Navigáljon _http://localhost:3000_ . Amikor elindítja a lebilincselő olvasót, és görgessen az aljára, megjelenik a matematikai képlet.

## <a name="next-steps"></a>Következő lépések

* Ismerkedjen meg a [magára az olvasói SDK](https://github.com/microsoft/immersive-reader-sdk) -val és az [olvasói SDK-referenciával](./reference.md)
* Kód mintáinak megtekintése a [githubon](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)
