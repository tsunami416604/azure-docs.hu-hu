---
title: 'Rövid útmutató: Node. js-alapú Webalkalmazás létrehozása'
description: Percek alatt üzembe helyezheti az első Node. js-"Helló világ!" alkalmazás Azure App Service. A Visual Studio Code használatával telepíthető, amely a App Service üzembe helyezésének számos módszerének egyike.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 03/04/2020
ms.custom: mvc, devcenter, seodec18
ms.openlocfilehash: df1e0809c0122eae593543cd193a6477b03d0938
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2020
ms.locfileid: "82690791"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Node.js-webalkalmazás létrehozása az Azure-ban 

Ismerkedjen meg Azure App Service egy Node. js/Express alkalmazás helyi létrehozásával a Visual Studio Code használatával, majd telepítse az alkalmazást a felhőbe. Mivel ingyenes App Service szintet használ, a rövid útmutató elvégzéséhez nem jár költséggel.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- [Node. js és NPM](https://nodejs.org). A parancs futtatásával `node --version` ellenőrizze, hogy telepítve van-e a Node. js.
- [Visual Studio Code](https://code.visualstudio.com/).
- A Visual Studio Code [Azure app Service bővítménye](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) .

## <a name="clone-and-run-a-local-nodejs-application"></a>Helyi Node. js-alkalmazás klónozása és futtatása

1. A helyi számítógépen nyisson meg egy terminált, és klónozással nyissa meg a minta tárházat:

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. Navigáljon az új alkalmazás mappájába:

    ```bash
    cd nodejs-docs-hello-world
    ```

1. Az alkalmazás elindítása helyi teszteléshez:

    ```bash
    npm start
    ```
    
1. Nyissa meg a böngészőt, és navigáljon a gombra `http://localhost:1337` . A böngészőben a következő üzenetnek kell megjelennie: ""Helló világ!"alkalmazás!".

1. A kiszolgáló leállításához nyomja le a **CTRL C billentyűt** + **C** a terminálon.

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-app-to-azure"></a>Az alkalmazás üzembe helyezése az Azure-ban

Ebben a szakaszban a Node. js-alkalmazást a VS Code és a Azure App Service bővítmény használatával helyezi üzembe az Azure-ban.

1. Győződjön meg arról, hogy a terminálon a *NodeJS-docs-Hello-World* mappában van, majd indítsa el a Visual Studio Code-ot a következő paranccsal:

    ```bash
    code .
    ```

1. A VS Code tevékenység sávján válassza ki az Azure-emblémát az **Azure app Service** Explorer megjelenítéséhez. Válassza a bejelentkezés az Azure-ba **...** lehetőséget, és kövesse az utasításokat. (Lásd: [Az Azure-bejelentkezés hibaelhárítása](#troubleshooting-azure-sign-in) , ha hibákat futtat.) A bejelentkezést követően a tallózónak meg kell jelennie az Azure-előfizetés nevének.

    ![Bejelentkezés az Azure-ba](containers/media/quickstart-nodejs/sign-in.png)

1. Az **Azure app Service** EXPLORERBEN a vs Code-ban kattintson a kék felfelé mutató nyíl ikonra az alkalmazás üzembe helyezéséhez az Azure-ban. (Ugyanezt a parancsot a **parancssorból** is meghívhatja (**CTRL** + **SHIFT** + **P**) a "üzembe helyezés a webalkalmazásba" parancs beírásával, és a **Azure app Service: üzembe helyezés a webalkalmazásban**) lehetőségre.

    ![Üzembe helyezés a webalkalmazásban](containers/media/quickstart-nodejs/deploy.png)
        
1. Válassza ki a *NodeJS-docs-Hello-World* mappát.

1. Válasszon egy létrehozási lehetőséget azon operációs rendszer alapján, amelyre telepíteni kívánja a következőt:

    - Linux: válassza az **új Webalkalmazás létrehozása** lehetőséget.
    - Windows: válassza az **új Webalkalmazás létrehozása... lehetőséget. Speciális**

1. Írjon be egy globálisan egyedi nevet a webalkalmazásnak, és nyomja le az **ENTER**billentyűt. A névnek egyedinek kell lennie az összes Azure-ban, és csak alfanumerikus karaktereket ("A-Z", "a-z" és "0-9") és kötőjeleket ("-") kell használnia.

1. A Linux megcélzása esetén válasszon egy Node. js-verziót, ha a rendszer kéri. Az **LTS** -verzió használata javasolt.

1. Ha a Windowst célozza meg, kövesse a további utasításokat:
    1. Válassza az **Új erőforráscsoport létrehozása**lehetőséget, majd adja meg az erőforráscsoport nevét, például: `AppServiceQS-rg` .
    1. Válassza a **Windows** lehetőséget az operációs rendszer számára.
    1. Válassza az **új App Service terv létrehozása**lehetőséget, majd adja meg a csomag nevét (például `AppServiceQS-plan` ), majd válassza az **ingyenes F1** lehetőséget a díjszabási szinten.
    1. Válassza **a Kihagyás lehetőséget,** amikor a rendszer rákérdez a Application Insightsra.
    1. Válasszon egy Önhöz közeli régiót vagy az elérni kívánt erőforrások közelében.

1. Az összes kérésre való válaszadás után a VS Code megjeleníti az alkalmazáshoz létrehozott Azure-erőforrásokat az értesítési felugró ablakban.

    Linux rendszeren való üzembe helyezéskor válassza az **Igen** lehetőséget, ha a rendszer felszólítja, hogy frissítse a konfigurációt `npm install` a cél Linux-kiszolgálón való futtatásra.

    ![Rákérdezés a konfiguráció frissítésére a cél Linux-kiszolgálón](containers/media/quickstart-nodejs/server-build.png)

1. Ha a rendszer kéri, válassza az **Igen** lehetőséget, és **mindig telepítse a "NodeJS-docs-Hello-World" munkaterületet az (alkalmazás neve)** elemre. Ha az **Igen** lehetőséget választja, a vs Code automatikusan ugyanazt a app Service webalkalmazást célozza meg a későbbi központi telepítésekkel.

1. Ha Linux rendszerre telepíti a rendszert, válassza a **Tallózás webhely** lehetőséget a kérdésben, hogy a telepítés befejezése után megtekintse a frissen telepített webalkalmazást. A böngészőben meg kell jelennie a következőnek: ""Helló világ!"alkalmazás!"

1. Ha Windows rendszeren telepíti a rendszert, először be kell állítania a Node. js verziószámát a webalkalmazáshoz:

    1. A VS Code-ban bontsa ki a csomópontot az új App Service-hez, kattintson a jobb gombbal az **Alkalmazásbeállítások**elemre, majd válassza az **új beállítás hozzáadása lehetőséget...**:

        ![Alkalmazás-beállítási parancs hozzáadása](containers/media/quickstart-nodejs/add-setting.png)

    1. Adja meg `WEBSITE_NODE_DEFAULT_VERSION` a beállítás kulcsát.
    1. Adja meg `10.15.2` a beállítás értékét.
    1. Kattintson a jobb gombbal az App Service-csomópontra, és válassza az **Újraindítás** lehetőséget.

        ![App Service-parancs újraindítása](containers/media/quickstart-nodejs/restart.png)

    1. Kattintson a jobb gombbal az App Service-csomópontra, és válassza a **Tallózás webhely**lehetőséget.

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="troubleshooting-azure-sign-in"></a>Azure-bejelentkezés hibaelhárítása

Ha az Azure-ba való bejelentkezéskor a **"nem található az előfizetés neve [előfizetés-azonosító]"** hibaüzenet jelenik meg, annak oka az lehet, hogy a proxy mögött van, és nem érhető el az Azure API. Konfigurálja a `HTTP_PROXY` és a `HTTPS_PROXY` környezeti változókat a saját proxy-adataival a használatával `export` .

```bash
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Ha a környezeti változók beállítása nem javítsa ki a problémát, vegye fel velünk a kapcsolatot, ha a fenti **probléma** gombra kattintott.

### <a name="update-the-app"></a>Az alkalmazás frissítése

Az alkalmazás módosításait úgy helyezheti üzembe, hogy a VS Code-ban szerkeszti a módosításokat, majd menti a fájlokat, majd ugyanazzal a folyamattal használja, mint a meglévő alkalmazás kiválasztását, és nem hoz létre újat.

## <a name="viewing-logs"></a>Naplók megtekintése

Az `console.log` alkalmazásból közvetlenül a vs Code kimenet ablakban megtekintheti a napló kimenetét (meghívások).

1. Az **Azure app Service** Explorerben kattintson a jobb gombbal az alkalmazás csomópontjára, és válassza a **streaming naplók indítása**lehetőséget.

    ![Adatfolyam-naplók indítása](containers/media/quickstart-nodejs/view-logs.png)

1. Ha a rendszer kéri, válassza a naplózás engedélyezését, és indítsa újra az alkalmazást. Az alkalmazás újraindítása után a VS Code kimenet ablak megnyílik a log streamtel létesített kapcsolatban. 

    ![Naplózás engedélyezése és újraindítás](containers/media/quickstart-nodejs/enable-restart.png)

1. Néhány másodperc elteltével a kimeneti ablak egy üzenetet jelenít meg, amely jelzi, hogy csatlakozik a log-streaming szolgáltatáshoz. A böngészőben az oldal frissítésével több kimeneti tevékenység is létrehozható.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).    
    </pre>

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
