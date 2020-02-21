---
title: 'Rövid útmutató: Node. js-alapú Webalkalmazás létrehozása'
description: Percek alatt üzembe helyezheti az első Node. js-"Helló világ!" alkalmazás Azure App Service. A Visual Studio Code használatával telepíthető, amely a App Service üzembe helyezésének számos módszerének egyike.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 09/30/2019
ms.custom: seodec18
experimental: false
experiment_id: a231f2b4-2625-4d
ms.openlocfilehash: 3fcc4d9d33637cd61f5621cc95788e9de8ffaec6
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77500110"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Node.js-webalkalmazás létrehozása az Azure-ban 

A Azure App Service egy jól méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt. Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe egy Node. js-alkalmazást a Azure App Service.

## <a name="prerequisites"></a>Előfeltételek

Ha még nem rendelkezik Azure-fiókkal, [Regisztráljon még ma](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) az Azure-kreditek $200-es ingyenes fiókjával, hogy kipróbálja a szolgáltatások bármilyen kombinációját.

A Node. js [és a NPM](https://nodejs.org/en/download), valamint a Node. js csomagkezelő segítségével telepítenie kell a [Visual Studio Code](https://code.visualstudio.com/) -ot.

Emellett telepítenie kell a [Azure app Service bővítményt](vscode:extension/ms-azuretools.vscode-azureappservice)is, amellyel Linux-web Appseket hozhat létre, kezelhet és helyezhet üzembe az Azure platform szolgáltatásként (Péter).

### <a name="sign-in"></a>Bejelentkezés

Miután telepítette a bővítményt, jelentkezzen be az Azure-fiókjába. Az **Azure app Service** Explorer megjelenítéséhez a tevékenység sávján válassza ki az Azure-emblémát. Válassza a bejelentkezés az Azure-ba **...** lehetőséget, és kövesse az utasításokat.

![Bejelentkezés az Azure-ba](containers/media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Hibakeresés

Ha a következő hibaüzenet jelenik meg: **"nem található az előfizetés a (z) [előfizetés-azonosítóval]" névvel**, lehetséges, hogy a proxy mögött van, és nem érhető el az Azure API. A `export`használatával konfigurálhatja a `HTTP_PROXY` és `HTTPS_PROXY` környezeti változókat a terminálon a proxy adataival.

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
> Ha már végrehajtotta a [Node. js-oktatóanyagot](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial), ugorjon az Azure-ba való [üzembe helyezésre](#deploy-to-azure).

### <a name="scaffold-a-new-application-with-the-express-generator"></a>Új alkalmazás készítése az expressz generátorral

Az [Express](https://www.expressjs.com) egy népszerű keretrendszer Node. js-alkalmazások létrehozásához és futtatásához. Az [Express Generator](https://expressjs.com/en/starter/generator.html) Tool használatával új expressz alkalmazást készíthet (létrehozhatja). Az expressz generátor NPM-modulként van elküldve, és közvetlenül (telepítés nélkül) futtatható a NPM parancssori eszköz `npx`használatával.

```bash
npx express-generator myExpressApp --view pug --git
```

A `--view pug --git` paraméterek közlik a létrehozóval, hogy a [mopsz](https://pugjs.org/api/getting-started.html) -sablon motorját (korábbi nevén `jade`) használja, és egy `.gitignore` fájlt hozzon létre.

Az alkalmazás összes függőségének telepítéséhez nyissa meg az új mappát, és futtassa a `npm install`.

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>Az alkalmazás futtatása

Ezután győződjön meg arról, hogy az alkalmazás fut. A terminálból indítsa el az alkalmazást a `npm start` parancs használatával a kiszolgáló indításához.

```bash
npm start
```

Most nyissa meg a böngészőt, és navigáljon a [http://localhost:3000hoz ](http://localhost:3000), ahol a következőhöz hasonlónak kell megjelennie:

![Expressz alkalmazás futtatása](containers/media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-to-azure"></a>Üzembe helyezés az Azure-ban

Ebben a szakaszban a Node. js-alkalmazást a VS Code és a Azure App Service bővítmény használatával helyezi üzembe. Ez a rövid útmutató a legalapvetőbb üzembe helyezési modellt használja, amelyben az alkalmazás tömörített és üzembe helyezése egy Azure Web App on Linux.

### <a name="deploy-using-azure-app-service"></a>Üzembe helyezés Azure App Service használatával

Először nyissa meg az alkalmazás mappáját a VS Code-ban.

```bash
code .
```

Az **Azure app Service** Explorerben válassza a kék felfelé mutató nyíl ikont az alkalmazás üzembe helyezéséhez az Azure-ban.

![Üzembe helyezés a webalkalmazásban](containers/media/quickstart-nodejs/deploy.png)

> [!TIP]
> A **parancssorból** (CTRL + SHIFT + P) is üzembe helyezheti az "üzembe helyezés a webalkalmazásba" parancsot, és futtathatja a **Azure app Service: Deploy to Web App** paranccsal.

1. Válassza ki a jelenleg megnyitott könyvtárat, `myExpressApp`.

1. Válasszon egy létrehozási lehetőséget azon operációs rendszer alapján, amelyre telepíteni kívánja a következőt:

    - Linux: válassza az **új Webalkalmazás létrehozása**lehetőséget.
    - Windows: válassza az **új Webalkalmazás létrehozása** lehetőséget, és válassza a **speciális** lehetőséget.

1. Írjon be egy globálisan egyedi nevet a webalkalmazásnak, és nyomja le az ENTER billentyűt. Az alkalmazás nevének érvényes karaktereinek neve: "a-z", "0-9" és "-".

1. A Linux megcélzása esetén válasszon egy Node. js-verziót, ha a rendszer kéri. Az **LTS** -verzió használata javasolt.

1. Ha a Windowst a **speciális** beállítással célozza meg, kövesse a további utasításokat:
    1. Válassza az **Új erőforráscsoport létrehozása**lehetőséget, majd adja meg az erőforráscsoport nevét.
    1. Válassza a **Windows** lehetőséget az operációs rendszer számára.
    1. Válasszon ki egy meglévő App Service csomagot, vagy hozzon létre egy újat. Új csomag létrehozásakor kiválaszthat egy díjszabási szintet.
    1. Válassza **a Kihagyás lehetőséget,** amikor a rendszer rákérdez a Application Insightsra.
    1. Válasszon egy Önhöz közeli régiót vagy az elérni kívánt erőforrások közelében.

1. Az összes kérésre való válaszadás után az értesítési csatorna az alkalmazáshoz létrehozott Azure-erőforrásokat jeleníti meg.

1. Válassza az **Igen** lehetőséget, ha a rendszer kéri, hogy frissítse a konfigurációt `npm install` futtatásához a célkiszolgálón. Ezután üzembe helyezi az alkalmazást.

    ![Konfigurált üzemelő példány](containers/media/quickstart-nodejs/server-build.png)

1. Ha elindul az üzembe helyezés, a rendszer kéri, hogy frissítse a munkaterületet, hogy a későbbi központi telepítések automatikusan ugyanazt a App Service webalkalmazást célozzák meg. Válassza az **Igen** lehetőséget, hogy a módosítások a megfelelő alkalmazásra legyenek telepítve.

    ![Konfigurált üzemelő példány](containers/media/quickstart-nodejs/save-configuration.png)

> [!TIP]
> Győződjön meg arról, hogy az alkalmazás figyeli a PORT környezeti változó által biztosított portot: `process.env.PORT`.

### <a name="browse-the-app-in-azure"></a>Az alkalmazás tallózása az Azure-ban

Miután az üzembe helyezés befejeződött, a kérdésben válassza a **Tallózás webhely** lehetőséget a frissen telepített webalkalmazás megtekintéséhez.

### <a name="troubleshooting"></a>Hibakeresés

Ha a következő hibaüzenet jelenik meg: **"nincs engedélye a könyvtár vagy lap megtekintésére."** , akkor az alkalmazás valószínűleg nem indult el megfelelően. Lépjen a következő szakaszra, és tekintse meg a napló kimenetét, és javítsa ki a hibát. Ha nem tudja kijavítani a problémát, lépjen kapcsolatba velünk az alábbi, a **probléma** elhárítása gombra kattintva. Örömmel segítünk!

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="update-the-app"></a>Az alkalmazás frissítése

Az alkalmazás módosításait ugyanazzal a folyamattal telepítheti, és a meglévő alkalmazást is kiválaszthatja, és nem hozhat létre újat.

## <a name="viewing-logs"></a>Naplók megtekintése

Ebből a szakaszból megtudhatja, hogyan tekintheti meg (vagy "farok") a naplókat a futó App Service alkalmazásból. Az alkalmazásban `console.log` összes hívás megjelenik a Visual Studio Code kimenet ablakában.

Keresse meg az alkalmazást az **Azure app Service** Explorerben, kattintson a jobb gombbal az alkalmazásra, majd válassza a **folyamatos átviteli naplók megtekintése**lehetőséget.

Ha a rendszer kéri, válassza a naplózás engedélyezését, és indítsa újra az alkalmazást. Az alkalmazás újraindítása után a VS Code kimenet ablak megnyílik a log streamtel létesített kapcsolatban.

![Folyamatos átviteli naplók megtekintése](containers/media/quickstart-nodejs/view-logs.png)

![Naplózás engedélyezése és újraindítás](containers/media/quickstart-nodejs/enable-restart.png)

Néhány másodperc elteltével megjelenik egy üzenet, amely jelzi, hogy csatlakozik a log-streaming szolgáltatáshoz. Frissítse az oldalt néhányszor, hogy láthassa a tevékenységeket.

    ```bash
    2019-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
    2019-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
    2019-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
    2019-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
    ```

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Következő lépések

Gratulálunk, sikeresen elvégezte ezt a rövid útmutatót!

Ezután tekintse meg a többi Azure-bővítményt.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker-eszközök](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI-eszközök](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Eszközök Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Az Azure Extension Packhez készült [Node Pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) telepítésével vagy az összes beszerzésével.
