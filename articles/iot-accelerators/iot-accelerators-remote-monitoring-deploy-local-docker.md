---
title: Távoli figyelési megoldás helyi telepítése - Docker - Azure | Microsoft dokumentumok
description: Ez az útmutató bemutatja, hogyan telepítheti a távoli figyelési megoldásgyorsítót a helyi számítógépre a Docker használatával tesztelésre és fejlesztésre.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 32b47d9d6d45ff471961f55f8159dbe85eae2dce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888837"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---docker"></a>A távfigyelési megoldás gyorsítójának helyi telepítése - Docker

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Ez a cikk bemutatja, hogyan telepítheti a távoli figyelési megoldás gyorsítót a helyi számítógépre tesztelésre és fejlesztésre. Megtudhatja, hogyan telepítheti a mikroszolgáltatásokat a helyi Docker-tárolókban. A helyi mikroszolgáltatások központi telepítése a következő felhőszolgáltatásokat használja: IoT Hub, Cosmos DB, Azure Streaming Analytics és Azure Time Series Insights-szolgáltatások a felhőben.

Ha a távoli figyelési megoldásgyorsítót ide-ban szeretné futtatni a helyi számítógépen, [olvassa el a Távoli figyelési megoldásgyorsító helyi telepítése – Visual Studio](iot-accelerators-remote-monitoring-deploy-local.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

A távfigyelési megoldásgyorsító által használt Azure-szolgáltatások üzembe helyezéséhez aktív Azure-előfizetésre van szüksége.

Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Gép beállítása

A helyi telepítés végrehajtásához a következő eszközökre van szükség a helyi fejlesztői gépen:

* [Git](https://git-scm.com/)
* [Docker között](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/) – ha azt tervezi, hogy módosítja a mikroszolgáltatások.
* [Node.js v8](https://nodejs.org/) – ez a szoftver előfeltétele a PCS CLI, hogy a parancsfájlok azure-erőforrások létrehozásához használt. Ne használja a Node.js v10-et.

> [!NOTE]
> Ezek az eszközök számos platformon elérhetők, beleértve a Windows, Linux és iOS rendszereket.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices-in-docker"></a>A mikroszolgáltatások futtatása a Dockerben

Nyisson meg egy új parancssort, hogy biztosan hozzáférhessen a **start.cmd** parancsfájl által beállított környezeti változókhoz. Windows rendszerben a következő parancs futtatásával ellenőrizheti, hogy a környezeti változók be vannak-e állítva:

```cmd
set PCS
```

A parancs a **start.cmd** parancsfájl által beállított összes környezeti változót jeleníti meg.

Győződjön meg arról, hogy a Docker fut a helyi számítógépen.
> [!NOTE]
> A Dockernek [Linux-tárolókat](https://docs.docker.com/docker-for-windows/) kell futtatnia, ha Windows rendszeren fut.

A helyi Docker-tárolókban futó mikroszolgáltatásoknak hozzá kell férnie az Azure felhőszolgáltatásaihoz. A Docker-környezet internetkapcsolatát a következő paranccsal tesztelheti egy internetcím pingeléséhez egy tárolóból:

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

A megoldásgyorsító futtatásához keresse meg a **szolgáltatásparancsfájlok\\helyi mappáját\\** a parancssori környezetben, és futtassa a következő parancsot:

```cmd/sh
docker-compose up
```

> [!NOTE] 
> Futás előtt győződjön meg arról, `docker-compose up`hogy megoszt egy helyi [meghajtót](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/issues/115) a Dockerrel.

A parancs első futtatásakor a Docker letölti a mikroszolgáltatás-rendszerképeket a Docker hubról a tárolók helyi létrehozásához. A következő futtatások, Docker azonnal futtatja a tárolókat.

> [!TIP]
> A Microsoft gyakran tesz közzé új Docker-lemezképeket új funkciókkal. A következő parancskészlet segítségével a helyi Docker-tárolók és a megfelelő rendszerképek karbantartásához a legújabbak lekérése előtt:

```cmd/sh
docker list
docker rm <list_of_containers>
docker rmi <list_of_images>
```

A tárolónaplóinak megtekintéséhez külön rendszerhéj at használhat. Először keresse meg a `docker ps` tárolóazonosítót a parancs használatával. Ezután `docker logs {container-id} --tail 1000` a megadott tároló utolsó 1000 bejegyzésének megtekintéséhez használható.

### <a name="start-the-stream-analytics-job"></a>A Stream Analytics feladat indítása

A Stream Analytics-feladat elindításához kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)
1. Nyissa meg a megoldáshoz létrehozott **erőforráscsoportot.** Az erőforráscsoport neve az a név, amelyet a **start.cmd** parancsfájl futtatásakor választott a megoldáshoz.
1. Kattintson a **Stream Analytics feladatra** az erőforrások listájában.
1. A Stream Analytics feladat **áttekintése** lapon kattintson a **Start** gombra. Ezután kattintson a **Start** gombra a feladat elindításához.

### <a name="connect-to-the-dashboard"></a>Csatlakozás az irányítópulthoz

A Távfigyelési megoldás irányítópultjának eléréséhez keresse meg a `http://localhost:8080` böngészőben. Most már használhatja a webes felhasználói felületet és a helyi mikroszolgáltatásokat.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A szükségtelen díjak elkerülése érdekében, amikor befejezte a tesztelést, távolítsa el a felhőszolgáltatásokat az Azure-előfizetésből. A szolgáltatások eltávolításához keresse meg az [Azure Portalon,](https://ms.portal.azure.com) és törölje a **start.cmd** parancsfájl által létrehozott erőforráscsoportot.

A `docker-compose down --rmi all` parancs segítségével távolítsa el a Docker-lemezképeket, és szabadítson fel helyet a helyi számítógépen. Törölheti is a távoli figyelési tárház helyi példányát, amelyet akkor hozott létre, amikor klónozta a forráskódot a GitHubról.

## <a name="next-steps"></a>További lépések

Most, hogy telepítette a távoli figyelési megoldást, a következő lépés [a megoldásirányítópult képességeinek feltárása.](quickstart-remote-monitoring-deploy.md)
