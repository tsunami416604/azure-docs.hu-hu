---
title: Helyi (Visual Studio IDE) – keresztül a távoli figyelési megoldásban üzembe Azure |} A Microsoft Docs
description: Ez az útmutató bemutatja, hogyan lehet a távoli figyelési megoldásgyorsító üzembe helyezése a helyi gépen a Visual Studio használatával a teszteléshez és fejlesztéshez.
author: avneet723
manager: hegate
ms.author: avneet723
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: fdb9b6c09e81ce29d2587fb0faca1aa80d28ea38
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58309979"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio"></a>A távoli figyelési megoldásgyorsító helyileg – Visual Studio telepítése

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Ez a cikk bemutatja, hogyan való teszteléshez és fejlesztéshez a helyi gépre a távoli figyelési megoldásgyorsító üzembe helyezése. Megismerheti, hogyan futtathatja a mikroszolgáltatások a Visual Studióban. Egy helyi mikroszolgáltatások üzembe helyezés az alábbi cloud-szolgáltatásokat használja: Az IoT Hub, Cosmos DB, az Azure Stream Analytics és az Azure Time Series Insights-szolgáltatások a felhőben.

Ha szeretné a távoli figyelési megoldásgyorsító Futtatás a Dockerben a helyi gépén, [a Docker üzembe helyezése a távoli figyelési megoldásgyorsító helyileg -](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure-szolgáltatások használják a távoli figyelési megoldásgyorsító üzembe helyezéséhez aktív Azure-előfizetés szükséges.

Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Gép beállítása

A helyi telepítés befejezéséhez, szüksége van a helyi fejlesztői gépen telepítve van a következő eszközöket:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/)
* [Nginx](https://nginx.org/en/download.html)
* [NODE.js v8](https://nodejs.org/) – Ez a szoftver előfeltétele a számítógépek parancssori felület, amely a parancsfájlok használata Azure-erőforrások létrehozásához. Ne használja a Node.js v10.

> [!NOTE]
> A Visual Studio érhető el a Windows és Mac rendszerre.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>A mikroszolgáltatások futtatása

Ebben a szakaszban a távoli figyelési mikroszolgáltatások fogja futtatni. A webes felhasználói felület futtatása natív módon, az Eszközszimuláció service a Docker és a mikroszolgáltatások a Visual Studióban.

### <a name="run-the-device-simulation-service"></a>Eszköz szimulálása szolgáltatás futtatásához

Nyisson meg egy új parancsablakot győződjön meg arról, hogy a által beállított környezeti változókat a hozzáférést a **start.cmd** parancsfájlt az előző szakaszban.

Futtassa a következő parancsot a Docker-tárolót, az eszköz szimulálása szolgáltatás elindításához. A szolgáltatás a távoli figyelési megoldásban az eszközök szimulálja.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>A helyi számítógépen az összes többi mikroszolgáltatások üzembe helyezéséhez

A következő lépések bemutatják, hogyan futtathatja a távoli figyelési mikroszolgáltatás-alapú Visual Studio 2017-ben:

1. Indítsa el a Visual Studio 2017-ben
1. Nyissa meg a **távoli-monitoring.sln** megoldás a **szolgáltatások** mappa-adattár helyi példányában.
1. A **Megoldáskezelőben**, kattintson a jobb gombbal a megoldásra, majd a kattintson **tulajdonságok**.
1. Válassza ki **gyakori tulajdonságai > Kezdőprojekt**.
1. Válassza ki **több kezdőprojekt** és **művelet** való **Start** a következő projektek:
    * WebService (asa-manager\WebService)
    * Webszolgáltatás (auth\WebService)
    * WebService (config\WebService)
    * Webszolgáltatás (eszköz-telemetry\WebService)
    * WebService (iothub-manager\WebService)
    * WebService (storage-adapter\WebService)
1. Kattintson a **OK** menteni a beállításokat.
1. Kattintson a **Debug > Start Debugging** létrehozását és futtatását a webes szolgáltatások a helyi gépen.

Minden webszolgáltatáshoz nyitja meg egy parancssort, és a webes böngészőablakban. Parancsot a parancssorba láthatja a futó szolgáltatás, és a böngésző ablakához lehetővé teszi, hogy monitorozhatja az állapotát. Ne zárja be a parancs felszólítja vagy weblapokhoz, ez a művelet leállítja a webszolgáltatást.

### <a name="start-the-stream-analytics-job"></a>A Stream Analytics-feladat indítása

Kövesse az alábbi lépéseket a Stream Analytics-feladat indítása:

1. Lépjen az [Azure Portalra](https://portal.azure.com).
1. Keresse meg a **erőforráscsoport** létrehozott a megoldáshoz. Az erőforráscsoport neve nem a választott név a megoldáshoz, ha futtatta a **start.cmd** parancsfájl **.
1. Kattintson a **Stream Analytics-feladat** az erőforrások listájában.
1. A Stream Analytics-feladat a **áttekintése** lap, kattintson a **Start** gombra. Kattintson a **Start** a feladat elindításához.

### <a name="run-the-web-ui"></a>A webes felhasználói felület futtatása

Ebben a lépésben indítsa el a webes felhasználói felületen. Nyisson meg egy új parancsablakot győződjön meg arról, hogy a által beállított környezeti változókat a hozzáférést a **start.cmd** parancsfájlt. Keresse meg a **mire** mappát a helyi tárház másolja, és futtassa a következő parancsokat:

```cmd
npm install
npm start
```

A kezdő befejeződése után a böngésző megjeleníti-e az oldal **http:\//localhost:3000 / irányítópult**. Ezen az oldalon a hibák várhatóan. Hibák nélkül az alkalmazás megtekintéséhez hajtsa végre a következő lépéssel.

### <a name="configure-and-run-nginx"></a>Konfigurálása és futtatása az nginx-et

Állítsa be a fordított proxykiszolgáló mutató hivatkozást a webalkalmazás és a helyi gépen futó mikroszolgáltatásokat:

* Másolás a **nginx.conf** fájlt a **webui\scripts\localhost** a tárház helyi példányának mappájában a **nginx\conf** telepítési könyvtár.
* Futtatás **nginx**.

További információ a futó **nginx**, lásd: [nginx-et a Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Csatlakozás az irányítópulton

A távoli figyelési megoldás irányítópultján eléréséhez lépjen a http:\//localhost:9000 a böngészőben.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Kerülje a szükségtelen költségek, a tesztelés befejezése után távolítsa el a cloud services az Azure-előfizetésből. Távolítsa el a szolgáltatásokat, lépjen a [az Azure portal](https://ms.portal.azure.com) és törölje az erőforrást, amely a **start.cmd** létrehozott parancsfájlt.

A távoli figyelési jön létre, amikor a forráskódját a Githubról klónozott tárház helyi példányának is törölheti.

## <a name="next-steps"></a>További lépések

Most, hogy a távoli figyelési megoldás üzembe helyezte, a következő lépés, hogy [Fedezze fel a megoldás irányítópultján képességeit](quickstart-remote-monitoring-deploy.md).
