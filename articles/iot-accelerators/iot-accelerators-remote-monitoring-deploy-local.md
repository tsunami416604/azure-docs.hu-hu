---
title: Távoli figyelési megoldás üzembe helyezése helyileg – VS IDE – Azure | Microsoft Docs
description: Ez a útmutató bemutatja, hogyan helyezheti üzembe a távoli figyelési megoldás-gyorsító szolgáltatást a helyi gépen a Visual Studio használatával a teszteléshez és a fejlesztéshez.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: a1eba1fceb959bd475d205176c2c53f6409fdc77
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890888"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio"></a>A távoli figyelési megoldás helyi telepítése – Visual Studio

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Ebből a cikkből megtudhatja, hogyan helyezheti üzembe a távoli figyelési megoldás Gyorssegédjét a helyi gépre tesztelés és fejlesztés céljából. Megtudhatja, hogyan futtathatja a szolgáltatásait a Visual Studióban. A helyi fürtszolgáltatások üzembe helyezése a következő felhőalapú szolgáltatásokat használja: IoT Hub, Cosmos DB, Azure streaming Analytics és Azure Time Series Insights Services a felhőben.

Ha a távoli figyelési megoldás gyorssegédét a Docker-ben szeretné futtatni a helyi gépen, tekintse meg [a távoli figyelési megoldás központi telepítése helyileg – Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

A távoli figyelési megoldás által használt Azure-szolgáltatások üzembe helyezéséhez aktív Azure-előfizetésre van szükség.

Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Gép beállítása

A helyi telepítés befejezéséhez a következő eszközökre van szükség a helyi fejlesztési gépen:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/)
* [Nginx](https://nginx.org/en/download.html)
* [Node. js V8](https://nodejs.org/) – ez a szoftver előfeltétele annak a PC CLI-nek, amelyet a parancsfájlok az Azure-erőforrások létrehozásához használnak. Ne használja a Node. js v10-et.

> [!NOTE]
> A Visual Studio a Windows és a Mac rendszerhez érhető el.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>A szervizcsomagok futtatása

Ebben a szakaszban a távoli monitorozási szolgáltatásait futtatja. A webes KEZELŐFELÜLETet natív módon, a Docker eszköz-szimulációs szolgáltatásával és a Visual Studióban található Service-szolgáltatásokkal futtathatja.

### <a name="run-the-device-simulation-service"></a>Az eszköz szimulációs szolgáltatásának futtatása

Nyisson meg egy új parancssorablakot, és ellenőrizze, hogy rendelkezik-e hozzáféréssel az előző szakaszban található **Start. cmd** parancsfájl által beállított környezeti változókhoz.

A következő parancs futtatásával indítsa el a Docker-tárolót a Device szimulációs szolgáltatáshoz. A szolgáltatás szimulálja az eszközöket a távoli figyelési megoldáshoz.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Az összes többi webszolgáltatás üzembe helyezése a helyi gépen

A következő lépések bemutatják, hogyan futtathatja a távoli monitorozási szolgáltatásokat a Visual Studióban:

1. Indítsa el a Visual studiót.
1. Nyissa meg a **Remote-Monitoring. SLN** megoldást a tárház helyi példányában található **szolgáltatások** mappában.
1. A **megoldáskezelő**kattintson a jobb gombbal a megoldásra, majd kattintson a **Tulajdonságok**elemre.
1. Válassza az **Általános tulajdonságok > indítási projekt**lehetőséget.
1. Válasszon **több indítási projektet** , és állítsa be a **műveletet** a következő projektek **elindításához** :
    * Webszolgáltatás (asa-manager\WebService)
    * Webszolgáltatás (auth\WebService)
    * Webszolgáltatás (config\WebService)
    * Webszolgáltatás (device-telemetry\WebService)
    * Webszolgáltatás (iothub-manager\WebService)
    * Webszolgáltatás (storage-adapter\WebService)
1. A beállítások mentéséhez kattintson **az OK** gombra.
1. Kattintson a **hibakeresés > a hibakeresés indítása** lehetőségre a webszolgáltatások helyi számítógépen való létrehozásához és futtatásához.

Minden webszolgáltatás megnyílik egy parancssor és egy webböngésző ablak. A parancssorban megtekintheti a futó szolgáltatás kimenetét, a böngészőablakban pedig nyomon követheti az állapotot. Ne zárjuk be a parancssort vagy a weblapokat, ez a művelet leállítja a webszolgáltatást.

### <a name="start-the-stream-analytics-job"></a>A Stream Analytics-feladatok elindítása

A Stream Analytics feladat elindításához kövesse az alábbi lépéseket:

1. Lépjen az [Azure Portalra](https://portal.azure.com).
1. Navigáljon a megoldáshoz létrehozott **erőforráscsoporthoz** . Az erőforráscsoport neve az a név, amelyet a megoldáshoz választott a **Start. cmd** parancsfájl futtatásakor.
1. Kattintson a **stream Analytics feladatokra** az erőforrások listájában.
1. A Stream Analytics feladatok **Áttekintés** lapján kattintson a **Start** gombra. Ezután kattintson az **Indítás** gombra a feladatok elindításához.

### <a name="run-the-web-ui"></a>A webes felhasználói felület futtatása

Ebben a lépésben elindítja a webes felhasználói felületet. Nyisson meg egy új parancssorablakot, és ellenőrizze, hogy rendelkezik-e hozzáféréssel a **Start. cmd** parancsfájl által beállított környezeti változókhoz. A tárház helyi példányában navigáljon a **WebUI** mappához, és futtassa a következő parancsokat:

```cmd
npm install
npm start
```

A kezdés befejezésekor a böngésző megjeleníti a **http:\//localhost: 3000/Dashboard**lapot. A rendszer a lapon szereplő hibákat várta. Ha hiba nélkül szeretné megtekinteni az alkalmazást, hajtsa végre a következő lépést.

### <a name="configure-and-run-nginx"></a>Az NGINX konfigurálása és futtatása

Hozzon létre egy fordított proxykiszolgálót a helyi gépen futó webalkalmazás és-szolgáltatások összekapcsolásához:

* Másolja az **Nginx. conf** fájlt a **webui\scripts\localhost** mappából a tárház helyi példányában a **nginx\conf** telepítési könyvtárába.
* Az **Nginx**futtatása.

További információ az **Nginx**futtatásáról: [Nginx for Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Kapcsolódás az irányítópulthoz

A távoli figyelési megoldás irányítópultjának eléréséhez nyissa meg a következőt: http:\//localhost: 9000 a böngészőben.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A szükségtelen díjak elkerüléséhez, ha befejezte a tesztelést, távolítsa el a Cloud Servicest az Azure-előfizetésből. A szolgáltatások eltávolításához navigáljon a [Azure Portal](https://ms.portal.azure.com) , és törölje a **Start. cmd** parancsfájl által létrehozott erőforráscsoportot.

Törölheti a távoli figyelési tárház helyi példányát is, amikor a forráskódot a GitHubról klónozotta.

## <a name="next-steps"></a>További lépések

Most, hogy üzembe helyezte a távoli figyelési megoldást, a következő lépés a [megoldás irányítópultjának képességeinek megismerése](quickstart-remote-monitoring-deploy.md).
