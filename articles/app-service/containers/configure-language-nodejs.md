---
title: Node.js-alkalmazások – az Azure App Service konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogyan működik az Azure App Service Node.js-alkalmazások konfigurálása
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: 43dc76e6d1e1ec2a6167f1d3e3cc7b8780f843db
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551322"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>Az Azure App Service Linux Node.js-alkalmazás konfigurálása

NODE.js-alkalmazásokat kell telepíteni a szükséges NPM függőségeit. Az App Service üzembe helyezési motorban (Kudu) automatikusan futtatja `npm install --production` , ha telepít egy [Git-tárház](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), vagy egy [Zip-csomagját](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) az összeállítási folyamatairól be van kapcsolva. Ha a fájlok használatával telepít [FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), azonban töltse fel a szükséges csomagokat manuálisan kell.

Ez az útmutató a főbb fogalmakat és az App Service-ben egy beépített Linux-tárolót használó Node.js-fejlesztőknek utasításokat tartalmaz. Ha korábban nem használta az Azure App Service, kövesse a [Node.js rövid](quickstart-nodejs.md) és [Node.js és MongoDB-oktatóanyag](tutorial-nodejs-mongodb-app.md) első.

## <a name="show-nodejs-version"></a>Node.js-verzió megjelenítése

Az aktuális Node.js verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Összes támogatott Node.js verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>Node.js-verzió beállítása

Állítsa az alkalmazás egy [Node.js-verzió támogatott](#show-nodejs-version), futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Ezzel a beállítással a Node.js-verzió használatához futásidőben, és a Kudu automatizált csomag-visszaállítás során is.

> [!NOTE]
> A projekt kell beállítania a Node.js-verzió `package.json`. Egy külön tárolót, amely tartalmazza az összes támogatott Node.js-verzió fut az üzembe helyezési motorban.

## <a name="configure-nodejs-server"></a>Node.js-kiszolgáló konfigurálása

A Node.js-tárolók kapható [PM2](http://pm2.keymetrics.io/), egy üzem folyamatkezelő. Az alkalmazás elindításához a PM2, vagy az npm-mel, vagy egy egyéni paranccsal konfigurálhatja.

- [Egyéni parancs futtatása](#run-custom-command)
- [Futtatásához npm start](#run-npm-start)
- [Futtassa a PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Egyéni parancs futtatása

App Service-ben, mint például egy végrehajtható fájl, például elindíthatja az alkalmazás egy egyéni paranccsal *run.sh*. Futtassa például `npm run start:prod`, futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Futtatásához npm start

Az alkalmazás használatának megkezdéséhez `npm start`, ügyeljen rá, hogy egy `start` szkript szerepel a *package.json* fájlt. Példa:

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

Egyéni használandó *package.json* projektbe, futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Futtassa a PM2

Egy közös Node.js fájlt a projektben található a tároló automatikusan elindul az alkalmazás a PM2:

- *bin/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- Az alábbi [PM2 fájlok](http://pm2.keymetrics.io/docs/usage/application-declaration/#process-file): *process.json* és *ecosystem.config.js*

Egy egyéni indítási fájlt a következő kiterjesztésű is konfigurálhatja:

- A *.js* fájl
- A [PM2 fájl](http://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) kiterjesztésű *.json*, *. config.js*, *.yaml*, vagy *yml*

Adjon hozzá egy egyéni indítási fájlt, futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Távoli hibakeresés

> [!NOTE]
> Távoli hibakeresés jelenleg csak előzetes formában.

A távolról a Node.js-alkalmazás hibakeresése is [Visual Studio Code](https://code.visualstudio.com/) konfigurálja úgy, hogy ha [futtassa a PM2](#run-with-pm2), kivéve, ha futtatja a egy *. config.js, *.yml, vagy *.yaml*.

A legtöbb esetben nem szükséges az alkalmazás további konfiguráció nélkül. Ha az alkalmazás futását egy *process.json* fájlt (alapértelmezett vagy egyéni), rendelkeznie kell egy `script` tulajdonság JSON gyökere. Példa:

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Távoli hibakeresés beállításához a Visual Studio Code, telepítse a [App Service-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Kövesse az utasításokat a bővítmény oldalon, és jelentkezzen be az Azure-bA a Visual Studio Code-ban.

Keresse meg a hibakeresés, kattintson a jobb gombbal, és válassza ki a kívánt alkalmazást az Azure explorer **távoli hibakeresés indítása**. Kattintson a **Igen** engedélyezheti az alkalmazás számára. App Service-ben elindítja egy alagutat proxyt az Ön számára, és csatolja a hibakeresőt. Ezután kéréseket küld az alkalmazást, és tekintse meg a hibakeresőt break időpontokban felfüggesztése.

Miután befejeződött a hibakeresést, a hibakereső leállítása kiválasztásával **Disconnect**. Amikor a rendszer kéri, kattintson **Igen** távoli hibakeresés letiltása. Később letiltja, kattintson a jobb gombbal a Azure explorer újra az alkalmazást, és válassza **távoli hibakeresés letiltása**.

## <a name="access-environment-variables"></a>Hozzáférés a környezeti változókhoz

Az App Service-ben is [állítsa be az alkalmazásbeállításokat](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) kívül a kódját. Ezután elérheti azokat a standard szintű Node.js-minta használatával. Például egy alkalmazás-beállítás eléréséhez nevű `NODE_ENV`, a következő kóddal:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Futtassa a Grunt/Bower/Gulp

Alapértelmezés szerint a Kudu futtat `npm install --production` Ha észleli egy Node.js-alkalmazás telepítve van. Ha az adott alkalmazáshoz szükséges a népszerű automatizálási eszközök, például Grunt, Bower vagy Gulp, meg kell adnia egy [egyéni üzembehelyezési szkript](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) a futtatáshoz.

Ahhoz, hogy az eszközöket a tárházban, hozzáadhatja őket a függőségeket kell *package.json.* Példa:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

A helyi terminálablakból az adattár gyökérkönyvtárában abba a könyvtárba, és futtassa a következő parancsokat:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Az adattár gyökérkönyvtárában most már két további fájllal rendelkezik: *.deployment* és *deploy.sh*.

Nyissa meg *deploy.sh* , és keresse meg a `Deployment` szakaszban, mely kell kinéznie:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Ez a szakasz végződik futó `npm install --production`. Adja hozzá a kód szakaszban kell futtatnia a szükséges eszköz *végén* , a `Deployment` szakaszban:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Tekintse meg az [a MEAN.js-mintában példa](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), ahol a telepítési szkript is fut egy egyéni `npm install` parancsot.

### <a name="bower"></a>Bower

Ez a kódrészlet futtatása `bower install`.

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Ez a kódrészlet futtatása `gulp imagemin`.

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Ez a kódrészlet futtatása `grunt`.

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="detect-https-session"></a>Észleli a HTTPS-KAPCSOLATON keresztül

Az App Service-ben [SSL-lezárást](https://wikipedia.org/wiki/TLS_termination_proxy) történik, ha a hálózati terheléselosztók, így az összes HTTPS-kérelmek elérni az alkalmazás nem titkosított HTTP-kérések. Ha az alkalmazás logikai igényeinek megfelelően, ellenőrizze, hogy ha a felhasználói kérelmek titkosítottak-e vagy sem, vizsgálja meg a `X-Forwarded-Proto` fejléc.

Népszerű webes keretrendszerek, hozzáférést biztosítanak a `X-Forwarded-*` a szabványos mintában információkat. A [Express](https://expressjs.com/), használhat [proxyk megbízható](http://expressjs.com/guide/behind-proxies.html). Példa:

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Nyissa meg böngészőben SSH-munkamenet

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

Amikor egy működő Node.js-alkalmazás működését eltérően az App Service-ben, vagy hibát, megpróbálkozhat a következőkkel:

- [A naplózási adatfolyam eléréséhez](#access-diagnostic-logs).
- Az alkalmazás helyi tesztelése éles módban. App Service-ben a a Node.js-alkalmazások éles módban fut, ezért győződjön meg arról, hogy a projekt megfelelően működik-e helyi éles módban kell. Példa:
    - Attól függően, a *package.json*, különböző csomagokban megtalál mindent éles üzemmódhoz előfordulhat, hogy telepíteni (`dependencies` és `devDependencies`).
    - Bizonyos webes keretrendszerek statikus fájlok eltérően éles módban helyezheti üzembe.
    - Bizonyos webes keretrendszerek egyéni indítási parancsfájlok felhasználhatja az éles módban való futtatáskor.
- Az alkalmazás futtatása az App Service-ben a fejlesztői módban. Például a [MEAN.js](http://meanjs.org/), fejlesztői mód által futtatókörnyezetben is beállíthatja az alkalmazás [beállítás a `NODE_ENV` Alkalmazásbeállítás](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: NODE.js-alkalmazás MongoDB-vel](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Az App Service Linux – gyakori kérdések](app-service-linux-faq.md)