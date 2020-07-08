---
title: Windows Node.js-alkalmazások konfigurálása
description: Megtudhatja, hogyan konfigurálhat egy Node.js alkalmazást a App Service natív Windows-példányaiban. Ez a cikk a leggyakoribb konfigurációs feladatokat ismerteti.
ms.devlang: nodejs
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 9f4ccdd04b8d57784f452dc28fa4507fb7ea94c2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84908106"
---
# <a name="configure-a-windows-nodejs-app-for-azure-app-service"></a>Windows Node.js-alkalmazás konfigurálása Azure App Servicehoz

Node.js alkalmazásokat az összes szükséges NPM-függőséggel telepíteni kell. A App Service üzembe helyezési motor automatikusan elindul, `npm install --production` Amikor git- [tárházat](deploy-local-git.md)telepít, vagy egy olyan [ZIP-csomagot](deploy-zip.md) , amelyen engedélyezve van az Build Automation. Ha az [FTP/S](deploy-ftp.md)használatával helyezi üzembe a fájlokat, akkor manuálisan kell feltöltenie a szükséges csomagokat. A Linux-alkalmazásokkal kapcsolatos információkért lásd: [Linux php-alkalmazás konfigurálása Azure app Servicehoz](containers/configure-language-nodejs.md).

Ez az útmutató a App Service üzembe helyezéséhez Node.js fejlesztőknek szóló főbb fogalmakat és útmutatásokat tartalmazza. Ha még soha nem használta a Azure App Servicet, először kövesse a [Node.js](app-service-web-get-started-nodejs.md) rövid útmutató és a [Node.js a MongoDB oktatóanyagot](app-service-web-tutorial-nodejs-mongodb-app.md) .

## <a name="show-nodejs-version"></a>Node.js verzió megjelenítése

Az aktuális Node.js verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION'].value"
```

Az összes támogatott Node.js-verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes | grep node
```

## <a name="set-nodejs-version"></a>Node.js verziójának beállítása

Ha az alkalmazást egy [támogatott Node.js verzióra](#show-nodejs-version)szeretné beállítani, futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com) `WEBSITE_NODE_DEFAULT_VERSION` egy támogatott verzióra való beállításához:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_NODE_DEFAULT_VERSION="10.15"
```

Ezzel a beállítással adható meg a használni kívánt Node.js-verzió, és az automatikus csomagok visszaállításakor App Service Build Automation során.

> [!NOTE]
> Állítsa be a Node.js verziót a projektben `package.json` . Az üzembe helyezési motor egy külön folyamatban fut, amely az összes támogatott Node.js verziót tartalmazza.

## <a name="access-environment-variables"></a>Hozzáférés a környezeti változókhoz

App Service az [Alkalmazásbeállítások](configure-common.md) az alkalmazás kódján kívül is megadhatók. Ezután a standard Node.js mintával érheti el őket. Ha például egy nevű alkalmazáshoz szeretne hozzáférni `NODE_ENV` , használja a következő kódot:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Morog/Bower/Nyelő futtatása

Alapértelmezés szerint a App Service Build Automation akkor fut, `npm install --production` Amikor felismeri a Node.js alkalmazást a git használatával (vagy a Build automationt használó zip-telepítéssel). Ha az alkalmazáshoz a népszerű Automation-eszközök, például a morog, a Bower vagy a Nyelő szükséges, egy [egyéni telepítési parancsfájlt](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) kell megadnia a futtatásához.

Ha engedélyezni szeretné, hogy a tárház futtassa ezeket az eszközöket, hozzá kell adnia azokat a függőségekhez *package.json.* Például:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Egy helyi terminál ablakban váltson át a tárház gyökerére, és futtassa a következő parancsokat:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

A tárház gyökerének most két további fájlja van: *. Deployment* és *Deploy.sh*.

Nyissa meg a *Deploy.sh* , és keresse meg az `Deployment` alábbihoz hasonló szakaszt:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Ez a szakasz a futtatásával végződik `npm install --production` . Adja hozzá a Code *(kód)* szakaszt, amelyen futtatnia kell a szükséges eszközt a `Deployment` szakasz végén:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Tekintse [meg a MEAN.js minta példáját](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), ahol az üzembe helyezési parancsfájl egy egyéni parancsot is futtat `npm install` .

### <a name="bower"></a>Bower

Ez a kódrészlet fut `bower install` .

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Ez a kódrészlet fut `gulp imagemin` .

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Ez a kódrészlet fut `grunt` .

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="detect-https-session"></a>HTTPS-munkamenet észlelése

App Service az [SSL-megszakítás](https://wikipedia.org/wiki/TLS_termination_proxy) a hálózati terheléselosztó esetében történik, így minden HTTPS-kérelem titkosítatlan http-kérésként éri el az alkalmazást. Ha az alkalmazás logikájának ellenőriznie kell, hogy a felhasználói kérések titkosítva vannak-e, vagy sem, vizsgálja meg a `X-Forwarded-Proto` fejlécet.

A népszerű webes keretrendszerek lehetővé teszik a `X-Forwarded-*` szabványos alkalmazási mintában lévő információk elérését. Az [Express](https://expressjs.com/)-ben [megbízhatósági proxykat](https://expressjs.com/guide/behind-proxies.html)használhat. Például:

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

Ha egy működő Node.js alkalmazás másképp viselkedik App Service vagy hibákat tartalmaz, próbálkozzon a következőkkel:

- [A log stream elérése](#access-diagnostic-logs).
- Az alkalmazás helyi tesztelése éles módban. A App Service éles módban futtatja Node.js-alkalmazásait, ezért gondoskodnia kell arról, hogy a projekt a várt módon működjön a helyi üzemi módban. Például:
    - A *package.jstól*függően különböző csomagok is telepíthetők üzemi módba (vagy `dependencies` `devDependencies` ).
    - Bizonyos webes keretrendszerek eltérő üzemi módban telepíthetnek statikus fájlokat.
    - Bizonyos webes keretrendszerek éles módban történő futtatáskor egyéni indítási parancsfájlokat is használhatnak.
- Az alkalmazást App Service fejlesztési módban futtathatja. [MEAN.js](https://meanjs.org/)például beállíthatja, hogy az alkalmazás a futtatókörnyezet fejlesztési módjára állítsa be [az `NODE_ENV` alkalmazás beállításait](configure-common.md).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Node.js alkalmazás a MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)

