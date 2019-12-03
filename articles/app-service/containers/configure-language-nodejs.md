---
title: Node. js-alkalmazások konfigurálása
description: Megtudhatja, hogyan konfigurálhat egy előre elkészített Node. js-tárolót az alkalmazáshoz. Ez a cikk a leggyakoribb konfigurációs feladatokat ismerteti.
ms.devlang: nodejs
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 6cf60472307a378d2fd4258a9777152344a11ded
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74670271"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>Linux Node. js-alkalmazás konfigurálása Azure App Servicehoz

A Node. js-alkalmazásokat az összes szükséges NPM-függőséggel telepíteni kell. A App Service Deployment Engine (kudu) automatikusan futtatja az `npm install --production`, amikor git- [tárházat](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)telepít, vagy egy olyan [ZIP-csomagot](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) , amelyen a Build-folyamatok be lettek kapcsolva. Ha az [FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)használatával helyezi üzembe a fájlokat, akkor manuálisan kell feltöltenie a szükséges csomagokat.

Ez az útmutató ismerteti a Node. js-fejlesztők számára a App Service beépített Linux-tárolóját használó főbb fogalmakat és útmutatásokat. Ha még soha nem használta a Azure App Servicet, kövesse a [Node. js](quickstart-nodejs.md) rövid útmutatót és a [Node. js-t a MongoDB-oktatóanyag](tutorial-nodejs-mongodb-app.md) első lépésével.

## <a name="show-nodejs-version"></a>Node. js-verzió megjelenítése

Az aktuális Node. js-verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Az összes támogatott Node. js-verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>Node. js-verzió beállítása

Ha az alkalmazást egy [támogatott Node. js-verzióra](#show-nodejs-version)szeretné beállítani, futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Ezzel a beállítással adható meg a Node. js-verzió, amelyet a rendszer futásidőben és az automatikus csomag visszaállításakor a kudu-ben használ.

> [!NOTE]
> Állítsa be a Node. js-verziót a projekt `package.json`. Az üzembe helyezési motor egy külön tárolóban fut, amely tartalmazza az összes támogatott Node. js-verziót.

## <a name="configure-nodejs-server"></a>Node. js-kiszolgáló konfigurálása

A Node. js-tárolók a [PM2](https://pm2.keymetrics.io/), a Production Process Manager szolgáltatással jönnek. Beállíthatja, hogy az alkalmazás a PM2 vagy a NPM, vagy egy egyéni paranccsal induljon el.

- [Egyéni parancs futtatása](#run-custom-command)
- [NPM-indítás futtatása](#run-npm-start)
- [Futtatás a PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Egyéni parancs futtatása

App Service elindíthatja az alkalmazást egy egyéni parancs használatával, például egy végrehajtható fájlt (például *Run.sh*). `npm run start:prod`futtatásához például futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>NPM-indítás futtatása

Ha `npm start`használatával szeretné elindítani az alkalmazást, győződjön meg arról, hogy a *Package. JSON* fájlban a `start` parancsfájl szerepel. Példa:

```json
{
  ...
  "scripts": {
    "start": "gulp",
    ...
  },
  ...
}
```

Ha egyéni *Package. JSON* fájlt szeretne használni a projektben, futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Futtatás a PM2

A tároló automatikusan elindítja az alkalmazást a PM-ben, ha az egyik Common. js fájl található a projektben:

- *bin/www*
- *Server. js*
- *app. js*
- *index. js*
- *hostingstart. js*
- A következő PM2- [fájlok](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file)egyike: *Process. JSON* és *ökoszisztéma. config. js*

Egyéni indítási fájlt is beállíthat a következő kiterjesztésekkel:

- Egy *. js* -fájl
- Egy *. JSON*, *. config. js*, *. YAML*vagy *. YML* kiterjesztésű [PM2-fájl](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file)

Egyéni indítási fájl hozzáadásához futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Távoli hibakeresés

> [!NOTE]
> A távoli hibakeresés jelenleg előzetes verzióban érhető el.

A Node. js-alkalmazást távolról is lekérdezheti a [Visual Studio Code](https://code.visualstudio.com/) -ban, ha úgy konfigurálja, hogy a PM-sel [fusson](#run-with-pm2), kivéve ha *. config. js, *. YML vagy *. YAML*használatával futtatja.

A legtöbb esetben nincs szükség további konfigurációra az alkalmazáshoz. Ha az alkalmazás egy *Process. JSON* fájllal (alapértelmezett vagy egyéni) fut, akkor a JSON-gyökérben `script` tulajdonsággal kell rendelkeznie. Példa:

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Ha a Visual Studio Code-ot szeretné beállítani a távoli hibakereséshez, telepítse az [app Service-bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Kövesse a bővítmény lapon megjelenő utasításokat, és jelentkezzen be az Azure-ba a Visual Studio Code-ban.

Az Azure Explorerben keresse meg a hibakereséshez használni kívánt alkalmazást, kattintson rá a jobb gombbal, és válassza a **távoli hibakeresés indítása**lehetőséget. Az alkalmazás engedélyezéséhez kattintson az **Igen** gombra. App Service elindít egy bújtatási proxyt, és csatolja a hibakeresőt. Ezután megteheti a kérelmeket az alkalmazásnak, és megtekintheti a hibakereső szüneteltetését a megszakítási pontokon.

Miután végzett a hibakereséssel, állítsa le a hibakeresőt a **kapcsolat bontása**lehetőség kiválasztásával. Ha a rendszer kéri, kattintson az **Igen** gombra a távoli hibakeresés letiltásához. Ha később szeretné letiltani, kattintson a jobb gombbal az alkalmazásra az Azure Explorerben, és válassza a **távoli hibakeresés letiltása**lehetőséget.

## <a name="access-environment-variables"></a>Hozzáférési környezeti változók

App Service az [Alkalmazásbeállítások](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) az alkalmazás kódján kívül is megadhatók. Ezt követően a standard Node. js-minta használatával érheti el őket. Ha például egy `NODE_ENV`nevű alkalmazás-beállítást szeretne elérni, használja a következő kódot:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Morog/Bower/Nyelő futtatása

Alapértelmezés szerint a kudu `npm install --production` fut, amikor felismeri a Node. js-alkalmazást. Ha az alkalmazáshoz a népszerű Automation-eszközök, például a morog, a Bower vagy a Nyelő szükséges, egy [egyéni telepítési parancsfájlt](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) kell megadnia a futtatásához.

Ha engedélyezni szeretné, hogy a tárház futtassa ezeket az eszközöket, hozzá kell adnia őket a *Package. JSON* -ban található függőségekhez. Példa:

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

Nyissa meg a *Deploy.sh* , és keresse meg a `Deployment` szakaszt, amely a következőképpen néz ki:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Ez a szakasz a `npm install --production`futtatásával végződik. Adja hozzá a Code (kód) szakaszt a `Deployment` szakasz *végén* a szükséges eszköz futtatásához:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Tekintse [meg a Mean. js mintában szereplő példát](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), ahol az üzembe helyezési parancsfájl egy egyéni `npm install` parancsot is futtat.

### <a name="bower"></a>Bower

Ez a kódrészlet `bower install`futtat.

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Ez a kódrészlet `gulp imagemin`futtat.

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Ez a kódrészlet `grunt`futtat.

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="detect-https-session"></a>HTTPS-munkamenet észlelése

App Service az [SSL-megszakítás](https://wikipedia.org/wiki/TLS_termination_proxy) a hálózati terheléselosztó esetében történik, így minden HTTPS-kérelem titkosítatlan http-kérésként éri el az alkalmazást. Ha az alkalmazás logikájának ellenőriznie kell, hogy a felhasználói kérések titkosítva vannak-e, vagy sem, vizsgálja meg a `X-Forwarded-Proto` fejlécét.

A népszerű webes keretrendszerek lehetővé teszik a szabványos alkalmazási mintában lévő `X-Forwarded-*` információk elérését. Az [Express](https://expressjs.com/)-ben [megbízhatósági proxykat](https://expressjs.com/guide/behind-proxies.html)használhat. Példa:

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>SSH-munkamenet megnyitása böngészőben

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Hibakeresés

Ha egy működő Node. js-alkalmazás máshogy viselkedik App Service vagy hibákat tartalmaz, próbálkozzon a következőkkel:

- [A log stream elérése](#access-diagnostic-logs).
- Az alkalmazás helyi tesztelése éles módban. App Service a Node. js-alkalmazásokat éles módban futtatja, ezért a projektnek a várt módon kell működnie a helyi üzemi módban. Példa:
    - A *Package. JSON*fájltól függően különböző csomagok is telepíthetők üzemi módba (`dependencies` vagy `devDependencies`).
    - Bizonyos webes keretrendszerek eltérő üzemi módban telepíthetnek statikus fájlokat.
    - Bizonyos webes keretrendszerek éles módban történő futtatáskor egyéni indítási parancsfájlokat is használhatnak.
- Az alkalmazást App Service fejlesztési módban futtathatja. A [Mean. js](https://meanjs.org/)fájlban például beállíthatja, hogy az alkalmazás a Futtatás közben fejlesztési módba legyen állítva [a `NODE_ENV` alkalmazás beállításával](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Node. js-alkalmazás a MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [App Service Linux – gyakori kérdések](app-service-linux-faq.md)
