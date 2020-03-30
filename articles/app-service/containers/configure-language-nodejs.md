---
title: Node.js alkalmazások konfigurálása
description: Ismerje meg, hogyan konfigurálhat egy előre elkészített Node.js tárolót az alkalmazáshoz. Ez a cikk a leggyakoribb konfigurációs feladatokat mutatja be.
ms.devlang: nodejs
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: fdc5129fc395f99cb4c244414ea952b2776dc4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252726"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>Linux Node.js alkalmazás konfigurálása az Azure App Service-hez

Node.js alkalmazásokat kell telepíteni az összes szükséges NPM-függőségek. Az App Service központi telepítési motorja (Kudu) automatikusan fut `npm install --production` az Ön számára, amikor telepíti a [Git-tárház,](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)vagy egy [Zip-csomag](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) build folyamatok bekapcsolva. Ha azonban [FTP/S-sel](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)telepíti a fájlokat, manuálisan kell feltöltenie a szükséges csomagokat.

Ez az útmutató kulcsfontosságú fogalmakat és utasításokat tartalmaz a Node.js fejlesztők számára, akik egy beépített Linux-tárolót használnak az App Service-ben. Ha még soha nem használta az Azure App Service-t, kövesse a [Node.js rövid útmutatót](quickstart-nodejs.md) és a [Node.js-t a MongoDB oktatóanyaggal.](tutorial-nodejs-mongodb-app.md)

## <a name="show-nodejs-version"></a>Node.js verzió megjelenítése

Az aktuális Node.js verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com)ben:

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Az összes támogatott Node.js verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com)ben:

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>Node.js verzió beállítása

Ha az alkalmazást [egy támogatott Node.js verzióra](#show-nodejs-version)szeretné beállítani, futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com)ben:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Ez a beállítás a Node.js használni andó verzióját adja meg, mind futásidőben, mind a Kudu automatikus csomag-visszaállítása során.

> [!NOTE]
> A Node.js verziót a projektben `package.json`kell beállítania. A központi telepítési motor egy külön tárolóban fut, amely tartalmazza az összes támogatott Node.js verziók.

## <a name="customize-build-automation"></a>Build-automatizálás testreszabása

Ha git- vagy zip-csomagok használatával telepíti az alkalmazást, és be van kapcsolva az alkalmazás, az App Service a következő sorrendben építi fel az automatizálási lépéseket:

1. Ha a program ezt `PRE_BUILD_SCRIPT_PATH`a.
1. Futtatás `npm install` nélkül zászlók, amely `preinstall` magában `postinstall` foglalja az npm és szkriptek és telepíti `devDependencies`.
1. Futtassa, `npm run build` ha a *package.json*ban meg van adva egy buildparancsfájl.
1. Futtassa, `npm run build:azure` ha a *package.json*ban meg van adva egy build:azure parancsfájl.
1. Ha a program ezt `POST_BUILD_SCRIPT_PATH`a.

> [!NOTE]
> Az [npm-dokumentumokban leírtak](https://docs.npmjs.com/misc/scripts) `prebuild` szerint `postbuild` a megnevezett és azt követő `build`parancsfájlok, illetve ha meg vannak adva. `preinstall`és `postinstall` fuss előtt `install`és utána, illetve.

`PRE_BUILD_COMMAND`és `POST_BUILD_COMMAND` olyan környezeti változók, amelyek alapértelmezés szerint üresek. Az előbuild parancsok futtatásához definiálja a programot. `PRE_BUILD_COMMAND` A létrehozás utáni parancsok `POST_BUILD_COMMAND`futtatásához definiálja a programot.

A következő példa a két változót egy parancssorozatra adja meg, vesszővel elválasztva.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

A buildautomatizálás testreszabásához további környezeti változókat az [Oryx konfigurációja](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)című témakörben tava látható.

Az App Service linuxos node.js-alkalmazások windowsos működéséről és felépítéséről az [Oryx dokumentációjában olvashat bővebben: Hogyan észlelhető és épül fel a Rendszer a Node.js alkalmazások.](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md)

## <a name="configure-nodejs-server"></a>Node.js kiszolgáló konfigurálása

A Node.js tárolók [a PM2,](https://pm2.keymetrics.io/)a termelési folyamat vezetője. Beállíthatja, hogy az alkalmazás a PM2-vel, az NPM-mel vagy egy egyéni paranccsal kezdődjön.

- [Egyéni parancs futtatása](#run-custom-command)
- [Npm indítás futtatása](#run-npm-start)
- [Futtatás a PM2-vel](#run-with-pm2)

### <a name="run-custom-command"></a>Egyéni parancs futtatása

Az App Service egyéni paranccsal indíthatja el az alkalmazást, például egy végrehajtható fájl, például *a run.sh.* Például a `npm run start:prod`futtatásához futtassa a következő parancsot a [Cloud Shellben:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Npm indítás futtatása

Az alkalmazás elindításához `npm start`csak győződjön meg arról, hogy egy `start` parancsfájl van a *package.json* fájlban. Példa:

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

Ha egyéni *package.json t* szeretne használni a projektben, futtassa a következő parancsot a [Cloud Shellben:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Futtatás a PM2-vel

A tároló automatikusan elindítja az alkalmazást a PM2-vel, ha a projektben megtalálható az egyik gyakori Node.js fájl:

- *kukk/www*
- *server.js*
- *alkalmazás.js*
- *index.js*
- *hostingstart.js*
- A következő [PM2 fájlok](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file)egyike: *process.json* és *ecosystem.config.js*

Egyéni kezdőfájlt a következő bővítményekkel is konfigurálhat:

- *A .js* fájl
- [PM2-fájl](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) *.json,* *.config.js*, *.yaml*vagy *.yml* kiterjesztéssel

Egyéni kezdőfájl hozzáadásához futtassa a következő parancsot a [Felhőrendszerhéjban:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Távoli hibakeresés

> [!NOTE]
> A távoli hibakeresés jelenleg előzetes verzióban van.

A Node.js alkalmazást távolról is elsajátíthatja a [Visual Studio-kódban,](https://code.visualstudio.com/) ha úgy konfigurálja, hogy [a PM2 használatával fusson,](#run-with-pm2)kivéve, ha a no.config.js, *.yml vagy *.yaml*használatával futtatja.

A legtöbb esetben nincs szükség további konfigurációra az alkalmazáshoz. Ha az alkalmazás *process.json* fájllal (alapértelmezett vagy egyéni) `script` fut, a JSON-gyökérben tulajdonsággal kell rendelkeznie. Példa:

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

A Visual Studio-kód távoli hibakereséshez való beállításához telepítse az [App Service-bővítményt.](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) Kövesse a bővítménylapon található utasításokat, és jelentkezzen be az Azure-ba a Visual Studio-kódban.

Az Azure Explorerben keresse meg a hibakeresést kívánt alkalmazást, kattintson rá a jobb gombbal, és válassza **a Távoli hibakeresés indítása parancsot.** Kattintson az **Igen** gombra az alkalmazáshoz való engedélyezéséhez. Az App Service elindítja a bújtatási proxyt, és csatolja a hibakeresőt. Ezután kéréseket intézhet az alkalmazáshoz, és a hibakereső szüneteltetése a töréspontoknál.

Miután befejezte a hibakeresést, állítsa le a hibakeresőt a **Kapcsolat bontása**gombra kattintva. Amikor a rendszer kéri, a távoli hibakeresés letiltásához kattintson az **Igen** gombra. Ha később le szeretné tiltani, kattintson ismét a jobb gombbal az alkalmazásra az Azure Explorerben, és válassza a **Távoli hibakeresés letiltása parancsot.**

## <a name="access-environment-variables"></a>Hozzáférés a környezeti változókhoz

Az App Service-ben az [alkalmazáskódokon kívül is megadhat alkalmazásbeállításokat.](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) Ezután elérheti őket a szabványos Node.js minta használatával. Például egy alkalmazás nevű `NODE_ENV`beállítás eléréséhez használja a következő kódot:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Fuss Grunt/Bower/Gulp

Alapértelmezés szerint a `npm install --production` Kudu akkor fut, amikor felismeri a Node.js alkalmazást. Ha az alkalmazás hoz létre a népszerű automatizálási eszközök, például a Grunt, a Bower vagy a Gulp, meg kell adnia egy [egyéni központi telepítési parancsfájl](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) futtatásához.

Ahhoz, hogy a tárház futtassa ezeket az eszközöket, hozzá kell adnia őket a *package.json függőségeihez.* Példa:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Egy helyi terminálablakból módosítsa a könyvtárat a tárház gyökérkönyvtárára, és futtassa a következő parancsokat:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

A tárház gyökérmost már két további fájlokat: *.deployment* és *deploy.sh*.

Nyissa *meg deploy.sh,* és keresse meg a `Deployment` szakaszt, amely így néz ki:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Ez a szakasz `npm install --production`a futással végződik. Adja hozzá a szükséges eszköz futtatásához szükséges kódszakaszt a `Deployment` szakasz *végén:*

- [Bower](#bower)
- [Nyelő](#gulp)
- [Grunt](#grunt)

Tekintse meg [a MEAN.js minta példáját,](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)ahol `npm install` a központi telepítési parancsfájl is futtat egy egyéni parancsot.

### <a name="bower"></a>Bower

Ez a kódrészlet fut `bower install`.

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Nyelő

Ez a kódrészlet fut `gulp imagemin`.

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Ez a kódrészlet fut `grunt`.

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="detect-https-session"></a>HTTPS-munkamenet észlelése

Az App Service-ben [az SSL-végződtetés](https://wikipedia.org/wiki/TLS_termination_proxy) a hálózati terheléselosztóknál történik, így minden HTTPS-kérelem titkosítatlan HTTP-kérelemként éri el az alkalmazást. Ha az alkalmazás logikájának ellenőriznie kell, hogy `X-Forwarded-Proto` a felhasználói kérelmek titkosítva vannak-e vagy sem, ellenőrizze a fejlécet.

A népszerű webes keretrendszerek `X-Forwarded-*` lehetővé teszik a szabványos alkalmazásmintában szereplő információk elérését. Az [Expressz programban](https://expressjs.com/) [bizalmi proxykat](https://expressjs.com/guide/behind-proxies.html)használhat. Példa:

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>SSH-munkamenet megnyitása a böngészőben

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

Ha egy működő Node.js alkalmazás eltérően viselkedik az App Service szolgáltatásban, vagy hibákat tapasztal, próbálkozzon a következőkkel:

- [A naplófolyam elérése](#access-diagnostic-logs).
- Tesztelje az alkalmazást helyileg éles módban. Az App Service éles környezetben futtatja a Node.js alkalmazásokat, ezért meg kell győződnie arról, hogy a projekt az éles környezetben helyileg a várt módon működik. Példa:
    - A *package.json*csomagtól függően különböző csomagok telepíthetők`dependencies` éles `devDependencies`üzemmódhoz ( vs. ).
    - Bizonyos webes keretrendszerek éles módban eltérő módon telepíthetik a statikus fájlokat.
    - Bizonyos webes keretrendszerek éles módban való futtatáskor egyéni indítási parancsfájlokat is használhatnak.
- Futtassa az alkalmazást az App Service-ben fejlesztési módban. A [MEAN.js](https://meanjs.org/)alkalmazásban például az [ `NODE_ENV` alkalmazás beállításával](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)beállíthatja az alkalmazást futóidőben fejlesztési módba.

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Node.js alkalmazás a MongoDB-vel](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Az App Service Linux – gyakori kérdések](app-service-linux-faq.md)
