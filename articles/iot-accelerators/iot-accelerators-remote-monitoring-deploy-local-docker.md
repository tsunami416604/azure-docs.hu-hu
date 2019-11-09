---
title: Távoli figyelési megoldás üzembe helyezése helyileg – Docker – Azure | Microsoft Docs
description: Ez a útmutató bemutatja, hogyan helyezheti üzembe a távoli figyelési megoldás-gyorsító szolgáltatást a helyi gépre a Docker használatával a teszteléshez és fejlesztéshez.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 32b47d9d6d45ff471961f55f8159dbe85eae2dce
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888837"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---docker"></a>A távoli figyelési megoldás-gyorsító üzembe helyezése helyileg – Docker

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Ebből a cikkből megtudhatja, hogyan helyezheti üzembe a távoli figyelési megoldás Gyorssegédjét a helyi gépre tesztelés és fejlesztés céljából. Megtudhatja, hogyan helyezheti üzembe a szolgáltatásait a helyi Docker-tárolókban. A helyi fürtszolgáltatások üzembe helyezése a következő felhőalapú szolgáltatásokat használja: IoT Hub, Cosmos DB, Azure streaming Analytics és Azure Time Series Insights Services a felhőben.

Ha a távoli figyelési megoldáshoz tartozó gyorssegédet a helyi gépen található IDE-ben szeretné futtatni, tekintse meg [a távoli figyelési megoldás központi telepítése helyileg – Visual Studio](iot-accelerators-remote-monitoring-deploy-local.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

A távoli figyelési megoldás által használt Azure-szolgáltatások üzembe helyezéséhez aktív Azure-előfizetésre van szükség.

Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Gép beállítása

A helyi telepítés befejezéséhez a következő eszközökre van szükség a helyi fejlesztési gépen:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/) – ha azt tervezi, hogy módosításokat hajt végre a Service-szolgáltatásokon.
* [Node. js V8](https://nodejs.org/) – ez a szoftver előfeltétele annak a PC CLI-nek, amelyet a parancsfájlok az Azure-erőforrások létrehozásához használnak. Ne használja a Node. js v10-et.

> [!NOTE]
> Ezek az eszközök számos platformon elérhetők, például Windows, Linux és iOS rendszereken.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices-in-docker"></a>A webszolgáltatás futtatása a Docker-ben

Nyisson meg egy új parancssort, és győződjön meg arról, hogy hozzáfér a **Start. cmd** parancsfájl által beállított környezeti változókhoz. Windows rendszeren a következő parancs futtatásával ellenőrizheti, hogy a környezeti változók be vannak-e állítva:

```cmd
set PCS
```

A parancs megjeleníti a **Start. cmd** parancsfájl által beállított összes környezeti változót.

Győződjön meg arról, hogy a Docker fut a helyi gépen.
> [!NOTE]
> Ha Windows rendszeren fut, a Docker-nek [Linux-tárolókat](https://docs.docker.com/docker-for-windows/) kell futtatnia.

A helyi Docker-tárolókban futó szolgáltatásoknak hozzá kell férniük az Azure Cloud Serviceshez. A Docker-környezet internetkapcsolatának teszteléséhez a következő paranccsal pingelheti az internetes címeket egy tárolón belül:

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

A megoldás-gyorsító futtatásához navigáljon a **szolgáltatások\\parancsfájlok\\helyi** mappát a parancssori környezetben, és futtassa a következő parancsot:

```cmd/sh
docker-compose up
```

> [!NOTE] 
> A `docker-compose up`futtatása előtt győződjön meg arról, hogy [megosztja a Docker helyi meghajtóját](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/issues/115) .

A parancs első futtatásakor a Docker letölti a Service-lemezképeket a Docker hub-ból a tárolók helyi létrehozásához. A következő futtatásakor a Docker azonnal futtatja a tárolókat.

> [!TIP]
> A Microsoft gyakran tesz közzé új Docker-rendszerképeket új funkciókkal. A következő parancsokkal törölheti a helyi Docker-tárolókat és a hozzájuk tartozó képeket, mielőtt lekéri a legújabb fájlokat:

```cmd/sh
docker list
docker rm <list_of_containers>
docker rmi <list_of_images>
```

Egy külön rendszerhéj használatával megtekintheti a tárolóban lévő naplókat. Először keresse meg a tároló AZONOSÍTÓját a `docker ps` parancs használatával. Ezután a `docker logs {container-id} --tail 1000` használatával megtekintheti a megadott tároló utolsó 1000 bejegyzését.

### <a name="start-the-stream-analytics-job"></a>A Stream Analytics-feladatok elindítása

A Stream Analytics feladat elindításához kövesse az alábbi lépéseket:

1. Lépjen az [Azure Portalra](https://portal.azure.com).
1. Navigáljon a megoldáshoz létrehozott **erőforráscsoporthoz** . Az erőforráscsoport neve az a név, amelyet a megoldáshoz választott a **Start. cmd** parancsfájl futtatásakor.
1. Kattintson a **stream Analytics feladatokra** az erőforrások listájában.
1. A Stream Analytics feladatok **Áttekintés** lapján kattintson a **Start** gombra. Ezután kattintson az **Indítás** gombra a feladatok elindításához.

### <a name="connect-to-the-dashboard"></a>Kapcsolódás az irányítópulthoz

A távoli figyelési megoldás irányítópultjának eléréséhez navigáljon `http://localhost:8080` a böngészőben. Mostantól használhatja a webes felhasználói felületet és a helyi szolgáltatásait is.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A szükségtelen díjak elkerüléséhez, ha befejezte a tesztelést, távolítsa el a Cloud Servicest az Azure-előfizetésből. A szolgáltatások eltávolításához navigáljon a [Azure Portal](https://ms.portal.azure.com) , és törölje a **Start. cmd** parancsfájl által létrehozott erőforráscsoportot.

A `docker-compose down --rmi all` parancs használatával távolítsa el a Docker-rendszerképeket, és szabadítson fel lemezterületet a helyi gépen. Törölheti a távoli figyelési tárház helyi példányát is, amikor a forráskódot a GitHubról klónozotta.

## <a name="next-steps"></a>További lépések

Most, hogy üzembe helyezte a távoli figyelési megoldást, a következő lépés a [megoldás irányítópultjának képességeinek megismerése](quickstart-remote-monitoring-deploy.md).
