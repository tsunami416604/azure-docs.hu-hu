---
title: 'Rövid útmutató: Node.js webalkalmazás létrehozása'
description: Az első Node.js Hello World alkalmazást percek alatt telepítheti az Azure App Service szolgáltatásba. A Visual Studio-kód használatával telepíti, amely az App Service szolgáltatásban való üzembe helyezés számos módja.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 03/04/2020
ms.custom: mvc, devcenter, seodec18
experimental: false
experiment_id: a231f2b4-2625-4d
ms.openlocfilehash: bcb0ae2457175e7511c3539187d72d48e80baa5b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80047135"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Node.js-webalkalmazás létrehozása az Azure-ban 

Az Azure App Service használatának megkezdéséhez hozzon létre egy Node.js/Express alkalmazást helyileg a Visual Studio-kód használatával, majd telepítse az alkalmazást a felhőbe. Mivel egy ingyenes App Service-szint, nem jár költségeket a rövid útmutató végrehajtásához.

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- [Node.js és npm](https://nodejs.org). Futtassa `node --version` a parancsot a Node.js telepítésének ellenőrzéséhez.
- [Visual Studio kód](https://code.visualstudio.com/).
- Az [Azure App Service-bővítmény](vscode:extension/ms-azuretools.vscode-azureappservice) a Visual Studio-kódhoz.

## <a name="clone-and-run-a-local-nodejs-application"></a>Helyi Node.js alkalmazás klónozása és futtatása

1. A helyi számítógépen nyisson meg egy terminált, és klónozza a mintatárolót:

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. Navigálás az új alkalmazásmappába:

    ```bash
    cd nodejs-docs-hello-world
    ```

1. Indítsa el az alkalmazást a helyi teszteléshez:

    ```bash
    npm start
    ```
    
1. Nyissa meg a [http://localhost:1337](http://localhost:1337)böngészőt, és keresse meg a t. A böngészőben meg kell jelennie a "Hello World!".

1. A kiszolgáló leállításához nyomja le a **Ctrl**+**C** billentyűt a terminálon.

> [!div class="nextstepaction"]
> [Belefutottam egy problémába.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-app-to-azure"></a>Az alkalmazás üzembe helyezése az Azure-ban

Ebben a szakaszban telepíti a Node.js alkalmazást az Azure-ba a VS Code és az Azure App Service-bővítmény használatával.

1. Győződjön meg arról, hogy a terminálon a *nodejs-docs-hello-world* mappában van, majd indítsa el a Visual Studio Code alkalmazást a következő paranccsal:

    ```bash
    code .
    ```

1. A VS Code tevékenységi sávon válassza ki az Azure emblémát az **AZURE APP SERVICE explorer** megjelenítéséhez. Válassza **a Bejelentkezés az Azure-ba lehetőséget,** és kövesse az utasításokat. (Lásd: [Az Azure-bejelentkezés ek elhárítása](#troubleshooting-azure-sign-in) az alábbi témakörben, ha hibákba ütközik.) Miután bejelentkezett, az explorernek meg kell jelennie az Azure-előfizetés nevében.

    ![Bejelentkezés az Azure-ba](containers/media/quickstart-nodejs/sign-in.png)

1. A VS Code **AZURE APP SERVICE** explorer ben válassza ki a kék felfelé mutató nyíl ikont az alkalmazás Azure-ba való üzembe helyezéséhez. (Ugyanazt a parancsot a **parancspalettából** is meghívhatja (**Ctrl**+**Shift**+**P**) a "deploy to web app" beírásával és az Azure App **Service: Deploy to Web App**parancs kiválasztásával.

    ![Üzembe helyezés a Web App ban](containers/media/quickstart-nodejs/deploy.png)
        
1. Válassza ki a *nodejs-docs-hello-world* mappát.

1. Válasszon egy létrehozási lehetőséget azon operációs rendszer alapján, amelyre telepíteni szeretné a következőket:

    - Linux: Válassza **az Új webalkalmazás létrehozása lehetőséget**
    - Windows: Válassza **az Új webalkalmazás létrehozása lehetőséget... Speciális**

1. Írja be a webalkalmazás globálisan egyedi nevét, és nyomja le az **Enter billentyűt.** A névnek egyedinek kell lennie az Azure-ban, és csak alfanumerikus karaktereket ('A-Z', "a-z" és "0-9") és kötőjeleket ('-') kell használnia.

1. Ha a Linux ot célozza, válassza ki a Node.js verziót, amikor a rendszer kéri. **LTS-verzió** használata ajánlott.

1. Ha a Windows rendszert célozza, kövesse a további utasításokat:
    1. Válassza **az Új erőforráscsoport létrehozása**lehetőséget, majd adja `AppServiceQS-rg`meg az erőforráscsoport nevét, például .
    1. Válassza a **Windows** elemet az operációs rendszerhez.
    1. Válassza **az Új App Service-csomag létrehozása**lehetőséget, `AppServiceQS-plan`majd adja meg a csomag nevét (például ), majd válassza az **F1 Free** lehetőséget a tarifacsomaghoz.
    1. Válassza **a Kihagyás most,** amikor az Application Insights kérdéséről kérdezve.
    1. Válasszon egy közeli régiót vagy az elérni kívánt erőforrásokat.

1. Miután válaszolt az összes kérdésre, a VS-kód megjeleníti az alkalmazáshoz az értesítési előugró ablakban létrehozott Azure-erőforrásokat.

    Linuxra való üzembe helyezéskor válassza az **Igen** lehetőséget, amikor a rendszer kéri a konfiguráció frissítését a cél Linux-kiszolgálón való futtatáshoz. `npm install`

    ![A cél Linux-kiszolgáló konfigurációjának frissítésére vonatkozó kérdés](containers/media/quickstart-nodejs/server-build.png)

1. Válassza az **Igen** lehetőséget, amikor a rendszer **a "nodejs-docs-hello-world" munkaterületet telepíti (alkalmazásnév)"** lehetőséget. **Az Igen** lehetőséget választva a VS-kód automatikusan megcélozhatja ugyanazt az App Service Web App-ot a későbbi központi telepítésekkel.

1. Ha linuxos üzembe helyezést hajt végre, válassza a **Webhely böngészése** lehetőséget a kérdésben a frissen telepített webalkalmazás megtekintéséhez a telepítés befejezése után. A böngészőnek meg kell jelenítenie a "Hello World!"

1. Windows rendszerre történő telepítés esetén először be kell állítania a Node.js verziószámát a webalkalmazáshoz:

    1. A VS-kód ban bontsa ki az új alkalmazásszolgáltatás csomópontját, kattintson a jobb gombbal az **Alkalmazásbeállítások**elemre, és válassza **az Új beállítás hozzáadása parancsot...**:

        ![Alkalmazásbeállítás hozzáadása parancs](containers/media/quickstart-nodejs/add-setting.png)

    1. Adja `WEBSITE_NODE_DEFAULT_VERSION` meg a beállítási kulcsot.
    1. Adja `10.15.2` meg a beállítási értéket.
    1. Kattintson a jobb gombbal az alkalmazásszolgáltatás csomópontjára, és válassza az **Újraindítás** parancsot

        ![Az alkalmazásszolgáltatás újraindítása parancs](containers/media/quickstart-nodejs/restart.png)

    1. Kattintson még egyszer a jobb gombbal az alkalmazásszolgáltatás csomópontjára, és válassza a **Webhely tallózása parancsot.**

> [!div class="nextstepaction"]
> [Belefutottam egy problémába.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="troubleshooting-azure-sign-in"></a>Az Azure-bejelentkezés hibáinak elhárítása

Ha az Azure-ba való bejelentkezéskor a **"Nem található előfizetés [subscription ID] nevű előfizetés t,** annak az lehet az oka, hogy egy proxy mögött van, és nem tudja elérni az Azure API-t. Konfigurálja `HTTPS_PROXY` és állítsa be `export` `HTTP_PROXY` a környezeti változókat a proxyadatokkal a terminálon a használatával.

```bash
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Ha a környezeti változók beállítása nem oldja meg a problémát, lépjen kapcsolatba velünk a fenti **Probléma** gomb kiválasztásával.

### <a name="update-the-app"></a>Az alkalmazás frissítése

Az alkalmazás módosításait úgy telepítheti, hogy módosításokat végez a VS-kódban, menti a fájlokat, majd ugyanazt a folyamatot használja, mint mielőtt csak a meglévő alkalmazást választaná, nem pedig újat hoz létre.

## <a name="viewing-logs"></a>Naplók megtekintése

A naplókimenet `console.log`(hívások) közvetlenül az alkalmazásból, a VS Code kimeneti ablakban tekinthető meg.

1. Az **AZURE APP SERVICE** explorerben kattintson a jobb gombbal az alkalmazáscsomópontra, és válassza a **Streamelési naplók indítása parancsot.**

    ![Streamelési naplók indítása](containers/media/quickstart-nodejs/view-logs.png)

1. Amikor a rendszer kéri, engedélyezze a naplózást, és indítsa újra az alkalmazást. Az alkalmazás újraindítása után megnyílik a VS-kód kimeneti ablaka a naplófolyamhoz való csatlakozással. 

    ![Naplózás és újraindítás engedélyezése](containers/media/quickstart-nodejs/enable-restart.png)

1. Néhány másodperc múlva a kimeneti ablakban megjelenik egy üzenet, amely jelzi, hogy csatlakozik a napló-streaming szolgáltatáshoz. Több kimeneti tevékenységet generálhat, ha frissíti az oldalt a böngészőben.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).    
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
