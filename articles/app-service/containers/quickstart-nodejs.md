---
title: Node.js-alapú webes alkalmazás létrehozása
description: Node. js üzembe helyezése az Azure App Services
author: KarlErickson
ms.author: karler
ms.date: 07/18/2019
ms.topic: quickstart
ms.service: app-service
ms.devlang: javascript
ms.openlocfilehash: ced2977509f16f8dab2abe5546e19b7e05fb2a3d
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68975809"
---
# <a name="create-a-nodejs-app-in-azure"></a>Node. js-alkalmazás létrehozása az Azure-ban

A Azure App Service egy jól méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt. Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe egy Node. js-alkalmazást a Azure App Service.

## <a name="prerequisites"></a>Előfeltételek

Ha még nem rendelkezik Azure-fiókkal, [Regisztráljon még ma](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) az Azure-kreditek $200-es ingyenes fiókjával, hogy kipróbálja a szolgáltatások bármilyen kombinációját.

A Node. js [és a NPM](https://nodejs.org/en/download), valamint a Node. js csomagkezelő segítségével telepítenie kell a [Visual Studio Code](https://code.visualstudio.com/) -ot.

Emellett telepítenie kell a [Azure app Service bővítményt](vscode:extension/ms-azuretools.vscode-azureappservice)is, amellyel Linux-web Appseket hozhat létre, kezelhet és helyezhet üzembe az Azure platform szolgáltatásként (Péter).

### <a name="sign-in"></a>Bejelentkezés

Miután telepítette a bővítményt, jelentkezzen be az Azure-fiókjába. Az **Azure app Service** Explorer megjelenítéséhez a tevékenység sávján kattintson az Azure-embléma elemre. Kattintson a bejelentkezés az Azure-ba **...** lehetőségre, és kövesse az utasításokat.

![Bejelentkezés az Azure-ba](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Hibaelhárítás

Ha a következő hibaüzenet jelenik meg: **"nem található az előfizetés a (z) [előfizetés-azonosítóval]" névvel**, lehetséges, hogy a proxy mögött van, és nem érhető el az Azure API. Konfigurálja `HTTP_PROXY` a `HTTPS_PROXY` és a környezeti változókat a saját proxy-adataival a használatával `export`.

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Ha a környezeti változók beállítása nem javítsa ki a problémát, vegye fel velünk a kapcsolatot az alábbi, a **probléma elhárítása** gombra kattintva.

### <a name="prerequisite-check"></a>Előfeltételek ellenőrzése

A folytatás előtt győződjön meg arról, hogy az összes előfeltétel telepítve és konfigurálva van.

A VS Code-ban az Azure **app Service** Explorerben tekintse meg az Azure-beli e-mail-címét az állapotjelző sávban és az előfizetésében.

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>A Node. js-alkalmazás létrehozása

Ezután hozzon létre egy Node. js-alkalmazást, amely üzembe helyezhető a felhőben. Ez a rövid útmutató egy alkalmazás-generátort használ az alkalmazás egy terminálból való gyors előkészítéséhez.

> [!TIP]
> Ha már végrehajtotta a [Node. js](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial)-oktatóanyagot, ugorjon [a webhely üzembe helyezése](#deploy-the-website)lehetőségre.

### <a name="install-the-express-generator"></a>Az Express Generator telepítése

Az [Express](https://www.expressjs.com) egy népszerű keretrendszer Node. js-alkalmazások létrehozásához és futtatásához. Az [Express Generator](https://expressjs.com/en/starter/generator.html) Tool használatával új expressz alkalmazást készíthet (létrehozhatja). Az Express Generator NPM-modulként van elküldve, és a NPM parancssori eszköz `npm`használatával telepíthető.

```bash
npm install -g express-generator
```

A `-g` kapcsoló az expressz generátort globálisan telepíti a gépre, így bárhonnan futtathatja.

### <a name="scaffold-a-new-application"></a>Új alkalmazás készítése

Ezután a következő futtatásával indítson el `myExpressApp` egy új expressz alkalmazást:

```bash
express myExpressApp --view pug --git
```

A `--view pug --git` paraméterek közlik a létrehozóval, hogy a [mopsz](https://pugjs.org/api/getting-started.html) -sablon motorját `jade`(korábbi nevén: `.gitignore` ) és egy fájl létrehozásához használja.

Az alkalmazás összes függőségének telepítéséhez nyissa meg az új mappát, és futtassa a `npm install`parancsot.

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>Az alkalmazás futtatása

Ezután győződjön meg arról, hogy az alkalmazás fut. A terminálból indítsa el az alkalmazást a `npm start` parancs használatával a kiszolgáló indításához.

```bash
npm start
```

Most nyissa meg a böngészőt, [http://localhost:3000](http://localhost:3000)és lépjen a címre, ahol a következőhöz hasonlónak kell megjelennie:

![Expressz alkalmazás futtatása](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-website"></a>A webhely üzembe helyezése

Ebben a szakaszban üzembe helyezi a Node. js-webhelyet a VS Code és a Azure App Service bővítmény használatával. Ez a rövid útmutató a legalapvetőbb üzembe helyezési modellt használja, amelyben az alkalmazás tömörített és üzembe helyezése egy Azure Web App on Linux.

### <a name="deploy-using-azure-app-service"></a>Üzembe helyezés Azure App Service használatával

Először nyissa meg az alkalmazás mappáját a VS Code-ban.

```bash
code .
```

Az **Azure app Service** Explorerben kattintson a kék felfelé mutató nyíl ikonra az alkalmazás üzembe helyezéséhez az Azure-ban.

![Üzembe helyezés a webalkalmazásban](./media/quickstart-nodejs/deploy.png)

> [!TIP]
> A parancssorból is üzembe helyezhető (CTRL + SHIFT + P) a "központi telepítés webalkalmazásba" beírásával és a **Azure app Service futtatásával: Üzembe helyezés a webalkalmazásban** parancs.

1. Válassza ki a jelenleg megnyitott `myExpressApp`könyvtárat.

2. Válassza az **új Webalkalmazás létrehozása**lehetőséget.

3. Írjon be egy globálisan egyedi nevet a webalkalmazásnak, és nyomja le az ENTER billentyűt. Az alkalmazás nevének érvényes karaktereinek neve: "a-z", "0-9" és "-".

4. Válassza ki a **Node. js-verziót**, az LTS használata javasolt.

    Az értesítési csatorna az alkalmazáshoz létrehozott Azure-erőforrásokat jeleníti meg.

Kattintson az **Igen** gombra, amikor a rendszer kéri, `npm install` hogy frissítse a konfigurációt a célkiszolgálón való futtatásra. Ezután üzembe helyezi az alkalmazást.

![Konfigurált üzemelő példány](./media/quickstart-nodejs/server-build.png)

Ha elindul az üzembe helyezés, a rendszer kéri, hogy frissítse a munkaterületet, hogy a későbbi központi telepítések automatikusan ugyanazt a App Service webalkalmazást célozzák meg. Válassza az **Igen** lehetőséget, hogy a módosítások a megfelelő alkalmazásra legyenek telepítve.

![Konfigurált üzemelő példány](./media/quickstart-nodejs/save-configuration.png)

> [!TIP]
> Győződjön meg arról, hogy az alkalmazás figyeli a PORT környezeti változó által biztosított portot: `process.env.PORT`.

### <a name="browse-the-website"></a>Böngészés a webhelyen

Miután az üzembe helyezés befejeződött, kattintson a **Tallózás** webhelyre a kérdésben a frissen telepített webhely megtekintéséhez.

### <a name="troubleshooting"></a>Hibaelhárítás

Ha a következő hibaüzenet jelenik meg: **"nincs engedélye a könyvtár vagy lap megtekintésére."** , akkor az alkalmazás valószínűleg nem indult el megfelelően. Lépjen a következő szakaszra, és tekintse meg a napló kimenetét, és javítsa ki a hibát. Ha nem tudja kijavítani, vegye fel velünk a kapcsolatot az alábbi, a **probléma** gombra kattintva. Örömmel segítünk!

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="updating-the-website"></a>A webhely frissítése

Az alkalmazás módosításait ugyanazzal a folyamattal telepítheti, és a meglévő alkalmazást is kiválaszthatja, és nem hozhat létre újat.

## <a name="viewing-logs"></a>Naplók megtekintése

Ebből a szakaszból megtudhatja, hogyan tekintheti meg (vagy "farok") a naplókat a futó webhelyről. A webhelyre irányuló `console.log` hívások a Visual Studio Code kimenet ablakában jelennek meg.

Keresse meg az alkalmazást az **Azure app Service** Explorerben, kattintson a jobb gombbal az alkalmazásra, majd válassza a **folyamatos átviteli naplók megtekintése**lehetőséget.

Ha a rendszer kéri, válassza a naplózás engedélyezését, és indítsa újra az alkalmazást. Az alkalmazás újraindítása után a VS Code kimenet ablak megnyílik a log streamtel létesített kapcsolatban.

![Folyamatos átviteli naplók megtekintése](./media/quickstart-nodejs/view-logs.png)

![Naplózás engedélyezése és újraindítás](./media/quickstart-nodejs/enable-restart.png)

Néhány másodperc elteltével megjelenik egy üzenet, amely jelzi, hogy csatlakozik a log-streaming szolgáltatáshoz.

```bash
Connecting to log-streaming service...
2017-12-21 17:33:51.428 INFO  - Container practical-mustache_2 for site practical-mustache initialized successfully.
2017-12-21 17:33:56.500 INFO  - Container logs
```

Frissítse a lapot néhányszor a böngészőben a napló kimenetének megtekintéséhez.

```bash
2017-12-21 17:35:17.774 INFO  - Container logs
2017-12-21T17:35:14.955412230Z GET / 304 141.798 ms - -
2017-12-21T17:35:15.248930479Z GET /stylesheets/style.css 304 3.180 ms - -
2017-12-21T17:35:15.378623115Z GET /favicon.ico 404 53.839 ms - 995
```

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>További lépések

Gratulálunk, sikeresen elvégezte ezt a rövid útmutatót!

Ezután tekintse meg a többi Azure-bővítményt.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker-eszközök](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI-eszközök](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Eszközök Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Az Azure Extension Packhez készült [Node Pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) telepítésével vagy az összes beszerzésével.
