---
title: Távoli figyelési megoldás helyi telepítése - VS IDE - Azure | Microsoft dokumentumok
description: Ez az útmutató bemutatja, hogyan telepítheti a távoli figyelési megoldásgyorsítót a helyi számítógépre a Visual Studio használatával tesztelésre és fejlesztésre.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: a1eba1fceb959bd475d205176c2c53f6409fdc77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890888"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio"></a>A távfigyelési megoldás gyorsítójának helyi telepítése – Visual Studio

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Ez a cikk bemutatja, hogyan telepítheti a távoli figyelési megoldás gyorsítót a helyi számítógépre tesztelésre és fejlesztésre. Megtudhatja, hogyan futtathatja a mikroszolgáltatásokat a Visual Studióban. A helyi mikroszolgáltatások központi telepítése a következő felhőszolgáltatásokat használja: IoT Hub, Cosmos DB, Azure Streaming Analytics és Azure Time Series Insights-szolgáltatások a felhőben.

Ha a Docker ben szeretné futtatni a távoli figyelési megoldásgyorsítót a helyi számítógépen, [olvassa el a Távoli figyelési megoldásgyorsító helyi telepítése – Docker című témakört.](iot-accelerators-remote-monitoring-deploy-local-docker.md)

## <a name="prerequisites"></a>Előfeltételek

A távfigyelési megoldásgyorsító által használt Azure-szolgáltatások üzembe helyezéséhez aktív Azure-előfizetésre van szüksége.

Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Gép beállítása

A helyi telepítés végrehajtásához a következő eszközökre van szükség a helyi fejlesztői gépen:

* [Git](https://git-scm.com/)
* [Docker között](https://www.docker.com)
* [Vizuális stúdió](https://visualstudio.microsoft.com/)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/) – ez a szoftver előfeltétele a PCS CLI, hogy a parancsfájlok azure-erőforrások létrehozásához használt. Ne használja a Node.js v10-et.

> [!NOTE]
> A Visual Studio Windows és Mac rendszeren érhető el.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>A mikroszolgáltatások futtatása

Ebben a szakaszban futtatja a távoli figyelési mikroszolgáltatások. A webes felhasználói felületet natívmódon futtatja, a Docker eszközszimulációs szolgáltatását és a Visual Studio mikroszolgáltatásait.

### <a name="run-the-device-simulation-service"></a>Az eszközszimulációs szolgáltatás futtatása

Nyisson meg egy új parancssorablakot, és győződjön meg arról, hogy hozzáfér az előző szakaszban a **start.cmd** parancsfájl által beállított környezeti változókhoz.

Futtassa a következő parancsot az eszközszimulációs szolgáltatás Docker-tárolójának elindításához. A szolgáltatás szimulálja a távoli figyelési megoldás eszközeit.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Az összes többi mikroszolgáltatás telepítése helyi számítógépen

Az alábbi lépések bemutatják, hogyan futtathatja a Távoli figyelés mikroszolgáltatásokat a Visual Studióban:

1. Indítsa el a Visual Studiót.
1. Nyissa meg a **remote-monitoring.sln** megoldást a **szolgáltatásmappában** a tárház helyi példányában.
1. A **Megoldáskezelőben**kattintson a jobb gombbal a megoldásra, és kattintson a **Tulajdonságok parancsra.**
1. Válassza **a Közös tulajdonságok > indítási projekt lehetőséget.**
1. Válassza **a Több indítási projekt** lehetőséget, és állítsa a **Művelet** **indításra** lehetőséget a következő projektekhez:
    * WebSzolgáltatás (asa-manager\WebService)
    * Webszolgáltatás (hitelesítés\WebService)
    * Webszolgáltatás (config\WebService)
    * WebSzolgáltatás (eszköz-telemetria\WebService)
    * WebSzolgáltatás (iothub-manager\WebService)
    * WebService (tároló-adapter\WebService)
1. A beállítások mentéséhez kattintson az **OK** gombra.
1. Kattintson **a Hibakeresés > a Hibakeresés indítása parancsra** a webszolgáltatások helyi számítógépen történő létrehozásához és futtatásához.

Minden webszolgáltatás megnyit egy parancssort és egy webböngésző ablakot. A parancssorban megjelenik a futó szolgáltatás kimenete, és a böngészőablak lehetővé teszi az állapot figyelése. Ne zárja be a parancssortokat vagy weblapokat, ez a művelet leállítja a webszolgáltatást.

### <a name="start-the-stream-analytics-job"></a>A Stream Analytics feladat indítása

A Stream Analytics-feladat elindításához kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)
1. Nyissa meg a megoldáshoz létrehozott **erőforráscsoportot.** Az erőforráscsoport neve az a név, amelyet a **start.cmd** parancsfájl futtatásakor választott a megoldáshoz.
1. Kattintson a **Stream Analytics feladatra** az erőforrások listájában.
1. A Stream Analytics feladat **áttekintése** lapon kattintson a **Start** gombra. Ezután kattintson a **Start** gombra a feladat elindításához.

### <a name="run-the-web-ui"></a>A webes felhasználói felület futtatása

Ebben a lépésben indítsa el a webes felhasználói felületet. Nyisson meg egy új parancssorablakot, és győződjön meg arról, hogy hozzáfér a **start.cmd** parancsfájl által beállított környezeti változókhoz. Nyissa meg a tárház helyi példányában található **webui** mappát, és futtassa a következő parancsokat:

```cmd
npm install
npm start
```

Amikor a start befejeződött, a böngésző megjeleníti az oldalt **\/http: /localhost:3000/dashboard**. A hibák ezen az oldalon várható. Az alkalmazás hibamentes megtekintéséhez hajtsa végre a következő lépést.

### <a name="configure-and-run-nginx"></a>Az NGINX konfigurálása és futtatása

Fordított proxykiszolgáló beállítása a helyi számítógépen futó webalkalmazás és mikroszolgáltatások összekapcsolására:

* Másolja a **nginx.conf** fájlt a tárház helyi példányának **webui\scripts\localhost** mappájából a **nginx\conf** telepítési könyvtárba.
* Fuss **nginx**.

A **nginx**futtatásáról további információt [a Nginx for Windows](https://nginx.org/en/docs/windows.html)című témakörben talál.

### <a name="connect-to-the-dashboard"></a>Csatlakozás az irányítópulthoz

A távoli figyelési megoldás irányítópultjának eléréséhez keresse meg a http:\//localhost:9000 lapot a böngészőben.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A szükségtelen díjak elkerülése érdekében, amikor befejezte a tesztelést, távolítsa el a felhőszolgáltatásokat az Azure-előfizetésből. A szolgáltatások eltávolításához keresse meg az [Azure Portalon,](https://ms.portal.azure.com) és törölje a **start.cmd** parancsfájl által létrehozott erőforráscsoportot.

Törölheti is a távoli figyelési tárház helyi példányát, amelyet akkor hozott létre, amikor klónozta a forráskódot a GitHubról.

## <a name="next-steps"></a>További lépések

Most, hogy telepítette a távoli figyelési megoldást, a következő lépés [a megoldásirányítópult képességeinek feltárása.](quickstart-remote-monitoring-deploy.md)
