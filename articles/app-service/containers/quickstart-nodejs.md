---
title: 'Rövid útmutató: Linux Node.js alkalmazás létrehozása'
description: Az Azure App Service Linux-alkalmazásai üzembe helyezésével az első Node.js alkalmazást egy Linux-tárolóba telepíti az App Service-ben.
author: msangapu-msft
ms.author: msangapu
ms.date: 08/12/2019
ms.topic: quickstart
ms.devlang: javascript
ms.openlocfilehash: 08a246b07effb8d5f3e2473a4d7959882cf43235
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687937"
---
# <a name="create-a-nodejs-app-in-azure"></a>Node.js alkalmazás létrehozása az Azure-ban

Az Azure App Service egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás. Ez a rövid útmutató bemutatja, hogyan telepíthet egy Node.js alkalmazást az Azure App Service-ben.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-fiókkal, [még ma regisztráljon](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) egy 200 usd azure-kreditet kapó ingyenes fiókot, és próbálja ki a szolgáltatások bármilyen kombinációját.

A [Visual Studio-kódot](https://code.visualstudio.com/) a [Node.js és az npm](https://nodejs.org/en/download), a Node.js csomagkezelő vel együtt kell telepíteni.

Emellett telepítenie kell az [Azure App Service-bővítményt,](vscode:extension/ms-azuretools.vscode-azureappservice)amellyel linuxos webalkalmazásokat hozhat létre, kezelhet és telepíthet az Azure Platformszolgáltatáson szolgáltatásként (PaaS).

### <a name="sign-in"></a>Bejelentkezés

A bővítmény telepítése után jelentkezzen be az Azure-fiókjába. A tevékenységsávon válassza ki az Azure emblémát az **AZURE APP SERVICE-kezelő** megjelenítéséhez. Válassza **a Bejelentkezés az Azure-ba lehetőséget,** és kövesse az utasításokat.

![bejelentkezés az Azure-ba](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Hibaelhárítás

Ha a **"Nem található előfizetés neve [subscription ID] nevű hiba jelenik meg,** annak az lehet az oka, hogy egy proxy mögött van, és nem tudja elérni az Azure API-t. Konfigurálja `HTTPS_PROXY` és állítsa be `export` `HTTP_PROXY` a környezeti változókat a proxyadatokkal a terminálon a használatával.

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Ha a környezeti változók beállítása nem oldja meg a problémát, lépjen kapcsolatba velünk az alábbi **Probléma** gomb kiválasztásával.

### <a name="prerequisite-check"></a>Előfeltétel-ellenőrzés

A folytatás előtt győződjön meg arról, hogy az összes előfeltétel telepítve van és konfigurálva van.

A VS-kódban az Azure-beli e-mail-címét az állapotsorban és az előfizetést az **AZURE APP SERVICE-kezelőben** kell látnia.

> [!div class="nextstepaction"]
> [Belefutottam egy problémába.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>A Node.js alkalmazás létrehozása

Ezután hozzon létre egy Node.js alkalmazást, amely telepíthető a felhőbe. Ez a rövid útmutató egy alkalmazásgenerátort használ az alkalmazás terminálról való gyors állványzatának gyors állványzatához.

> [!TIP]
> Ha már befejezte a [Node.js oktatóanyagot,](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial)ugorjon előre az [Azure-ba való üzembe helyezés hez.](#deploy-to-azure)

### <a name="scaffold-a-new-application-with-the-express-generator"></a>Állványzat egy új alkalmazás az Express Generator

[Az Express](https://www.expressjs.com) a Node.js alkalmazások létrehozásának és futtatásának népszerű keretrendszere. Az [Express Generator](https://expressjs.com/en/starter/generator.html) eszközzel felállványzthat (létrehozhat) egy új Express alkalmazást. Az Express Generator npm modulként kerül kiszállításra, és közvetlenül (telepítés nélkül) `npx`futtatható az npm parancssori eszközzel.

```bash
npx express-generator myExpressApp --view pug --git
```

A `--view pug --git` paraméterek megmondják a generátornak, hogy használja a [mopsz sablonmotort](https://pugjs.org/api/getting-started.html) (korábbi nevén `jade`) és hozzon létre egy `.gitignore` fájlt.

Az alkalmazás összes függőségének telepítéséhez nyissa meg az `npm install`új mappát, és futtassa a parancsot.

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>Az alkalmazás futtatása

Ezután győződjön meg arról, hogy az alkalmazás fut. A terminálról indítsa el `npm start` az alkalmazást a paranccsal a kiszolgáló elindításához.

```bash
npm start
```

Most nyissa meg a [http://localhost:3000](http://localhost:3000)böngészőt, és keresse meg a , ahol meg kell látni valami ilyesmi:

![Expressz alkalmazás futtatása](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Belefutottam egy problémába.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-to-azure"></a>Üzembe helyezés az Azure-ban

Ebben a szakaszban telepíti a Node.js alkalmazást a VS Code és az Azure App Service-bővítmény használatával. Ez a rövid útmutató a legalapvetőbb üzembe helyezési modellt használja, ahol az alkalmazás tömörített és üzembe helyezett egy Azure Web App Linux on.

### <a name="deploy-using-azure-app-service"></a>Üzembe helyezés az Azure App Service használatával

Először nyissa meg az alkalmazásmappát a VS Code alkalmazásban.

```bash
code .
```

Az **AZURE APP SERVICE** explorerben válassza a kék felfelé mutató ikont az alkalmazás Azure-ba való üzembe helyezéséhez.

![Üzembe helyezés a Web App ban](./media/quickstart-nodejs/deploy.png)

> [!TIP]
> A **parancspalettáról** (CTRL + SHIFT + P) is üzembe helyezheti a "deploy to web app" parancsot, és futtathatja az **Azure App Service: Deploy to Web App** parancsot.

1. Válassza ki a jelenleg megnyitott könyvtárat. `myExpressApp`

1. Válassza **az Új webalkalmazás létrehozása**lehetőséget, amely alapértelmezés szerint linuxos alkalmazásszolgáltatásra telepíti.

1. Írja be a Web App globálisan egyedi nevét, és nyomja le az ENTER billentyűt. Az alkalmazásnevének érvényes karakterei: "a-z", "0-9" és "-".

1. Válassza ki a **Node.js verziót**, LTS ajánlott.

    Az értesítési csatorna az alkalmazáshoz létrehozott Azure-erőforrásokat jeleníti meg.

1. Válassza az **Igen** lehetőséget, amikor `npm install` a rendszer kéri a konfiguráció nak a célkiszolgálón való futtatására vonatkozó frissítését. Az alkalmazás ezután telepítve van.

    ![Konfigurált telepítés](./media/quickstart-nodejs/server-build.png)

1. A központi telepítés indításakor a rendszer kéri a munkaterület frissítését, hogy a későbbi telepítések automatikusan ugyanazt az App Service Web App-alkalmazást célozzák meg. Válassza az **Igen** lehetőséget, ha biztosítani szeretné, hogy a módosítások a megfelelő alkalmazásba legyenek telepítve.

    ![Konfigurált telepítés](./media/quickstart-nodejs/save-configuration.png)

> [!TIP]
> Győződjön meg arról, hogy az alkalmazás figyeli `process.env.PORT`a PORT környezeti változó által biztosított portot: .

### <a name="browse-the-app-in-azure"></a>Böngésszen az alkalmazásban az Azure-ban

Miután a központi telepítés befejeződött, válassza **a Webhely böngészése** a kérdésben a frissen telepített webalkalmazás megtekintéséhez.

### <a name="troubleshooting"></a>Hibaelhárítás

Ha a **"Nincs engedélye a könyvtár vagy lap megtekintéséhez"** hibaüzenet jelenik meg, akkor az alkalmazás valószínűleg nem indult el megfelelően. Menjen a következő szakaszra, és tekintse meg a napló kimenetét a hiba megkereséséhez és javításához. Ha nem tudja megjavítani, lépjen kapcsolatba velünk az alábbi **Probléma** gomb kiválasztásával. Örömmel segítünk!

> [!div class="nextstepaction"]
> [Belefutottam egy problémába.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="update-the-app"></a>Az alkalmazás frissítése

Az alkalmazás módosításait ugyanazzal a folyamattal telepítheti, és a meglévő alkalmazást választhatja ki ahelyett, hogy újat hozna létre.

## <a name="viewing-logs"></a>Naplók megtekintése

Ebben a szakaszban megtudhatja, hogyan tekintheti meg (vagy "farok") a naplókat a futó App Service-alkalmazásból. Az alkalmazásban `console.log` érkező hívások a Visual Studio-kód kimeneti ablakában jelennek meg.

Keresse meg az alkalmazást az **AZURE APP SERVICE** explorerben, kattintson a jobb gombbal az alkalmazásra, és válassza a **Streamelési naplók megtekintése parancsot.**

Amikor a rendszer kéri, engedélyezze a naplózást, és indítsa újra az alkalmazást. Az alkalmazás újraindítása után megnyílik a VS-kód kimeneti ablaka a naplófolyamhoz való csatlakozással.

![Streamelési naplók megtekintése](./media/quickstart-nodejs/view-logs.png)

![Naplózás és újraindítás engedélyezése](./media/quickstart-nodejs/enable-restart.png)

Néhány másodperc múlva megjelenik egy üzenet, amely jelzi, hogy csatlakozik a napló-streaming szolgáltatáshoz. Frissítse az oldalt néhányszor, hogy több tevékenységet lásson.

<pre>
2019-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
2019-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
2019-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
2019-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
</pre>

> [!div class="nextstepaction"]
> [Belefutottam egy problémába.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>További lépések

Gratulálunk, sikeresen befejezte ezt a rövid útmutatót!

Ezután tekintse meg a többi Azure-bővítményt.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker-eszközök](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI-eszközök](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Az Azure Resource Manager eszközei](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Vagy mindet beszerezheti a [Node Pack for Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) bővítménycsomag telepítésével.
