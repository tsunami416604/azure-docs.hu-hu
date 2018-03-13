---
title: "Azure - helyileg a távoli felügyeleti megoldás üzembe helyezéséhez |} Microsoft Docs"
description: "Ez az oktatóanyag bemutatja, hogyan tesztelése és fejlesztési a helyi számítógép a távoli figyelési előkonfigurált megoldás üzembe helyezéséhez."
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 03/07/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 77f40e2f10cbdb9930a22a4248e19bb3356af7af
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2018
---
# <a name="deploy-the-remote-monitoring-preconfigured-solution-locally"></a>A megoldás üzembe helyezéséhez távoli figyelési előre konfigurált helyileg

Ez a cikk bemutatja, hogyan tesztelése és fejlesztési a helyi számítógép a távoli figyelési előkonfigurált megoldás üzembe helyezéséhez. Ez a megközelítés a mikroszolgáltatások telepíti egy helyi Docker-tároló, és a felhőben az IoT-központ, a Cosmos-adatbázis és az Azure storage szolgáltatások használja. Az előkonfigurált megoldásokat (számítógépek) parancssori felület telepítése az Azure felhőszolgáltatások használhatja.

## <a name="prerequisites"></a>Előfeltételek

A távoli felügyeleti előkonfigurált megoldás által használt Azure-szolgáltatásokhoz központi telepítéséhez aktív Azure-előfizetés szükséges.

Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](http://azure.microsoft.com/pricing/free-trial/).

A helyi központi telepítés befejezéséhez szüksége van a helyi fejlesztői gépen telepítve van a következő eszközöket:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Docker compose](https://docs.docker.com/compose/install/)
* [NODE.js](https://nodejs.org/) -ezt a szoftvert a számítógépek CLI előfeltétele.
* SZÁMÍTÓGÉPEK PARANCSSORI FELÜLET

> [!NOTE]
> Ezek az eszközök számos platformon, beleértve a Windows, Linux és iOS érhetők el.

### <a name="install-the-pcs-cli"></a>A számítógépek parancssori felület telepítése

A parancssori felület telepítése, a következő parancsot a parancssori környezetben:

```cmd/sh
npm install iot-solutions -g
```

A parancssori Felülettel kapcsolatos további információkért lásd: [a CLI használata](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="deploy-the-azure-services"></a>Az Azure-szolgáltatások telepítése

Bár ez a cikk bemutatja, hogyan futtassa helyileg a mikroszolgáltatások létrehozására, a felhőben futó három Azure-szolgáltatások függenek. Az Azure-szolgáltatások manuálisan az Azure portálon keresztül telepítheti, vagy a számítógépek CLI használata. Ez a cikk bemutatja, hogyan használható a `pcs` eszköz.

### <a name="sign-in-to-the-cli"></a>Jelentkezzen be a parancssori felület

Az előkonfigurált megoldás telepítése előtt be kell jelentkeznie az Azure-előfizetéshez a parancssori felület használatával az alábbiak szerint:

```cmd/sh
pcs login
```

Kövesse a képernyőn megjelenő utasításokat a bejelentkezési folyamat befejezéséhez.

### <a name="run-a-local-deployment"></a>Egy helyi központi telepítés

Az alábbi parancs segítségével indítsa el a helyi telepítés:

```cmd/pcs
pcs -s local
```

A parancsprogram kéri a következő információkat:

* A megoldás neve.
* A használandó előfizetés.
* Az Azure-adatközpontban használandó helyét.

A parancsfájl létrehozza az IoT-központ példány, egy Cosmos DB példány és egy Azure storage-fiókot az Azure-előfizetéshez az erőforráscsoportban. Az erőforráscsoport neve nem a választott kulcsfájloknak megoldást nevét a `pcs` eszköz.

A parancsfájl futtatásához több percet vesz igénybe. Ha elkészült, megjelenik egy üzenet `Copy the following environment variables to /scripts/local/.env file:`. Másolja az üzenet a következő környezeti változó definíciókat, egy későbbi lépésben használja.

## <a name="download-the-source-code"></a>A forráskód letöltése

A távoli felügyeleti kód tárházban magában foglalja a Docker konfigurációs fájlokat kell letölteni, konfigurálásához és futtatásához a Docker-lemezképeket, amelyek tartalmazzák a mikroszolgáltatások létrehozására. Klónozza a tárházat, keresse meg a megfelelő mappát a helyi számítógépen, és futtassa a következő parancsok egyikét:

A Java implementációja a mikroszolgáltatások telepítéséhez futtassa:

```cmd/sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-java
```

A mikroszolgáltatások implementációja a .net telepítéséhez futtassa:

```cmd\sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet
```

Ezek a parancsok a mikroszolgáltatások forráskódja letöltése. A forráskód akkor hasznos, ha később szeretne az előkonfigurált megoldás módosítása és tesztelése a módosításokat a helyi, nincs szüksége a forráskódra futtassa a mikroszolgáltatások Docker, bár.

## <a name="run-the-microservices-in-docker"></a>Futtassa a mikroszolgáltatások Docker

Futtassa a mikroszolgáltatások Docker, először módosítsa a **parancsfájlok\\helyi\\.env** fájlt a helyi másolat készítése a tárházban. Cserélje le a fájl teljes tartalmát a kulcsfájloknak feljegyezte környezeti változó definíciókat a `pcs` korábban parancsot. Ezek a környezeti változók a mikroszolgáltatások létrehozására az a Docker-tároló által létrehozott Azure-szolgáltatásokhoz való csatlakozás engedélyezése a `pcs` eszköz.

Az előkonfigurált megoldás futtatásához nyissa meg a **scripts\local** mappa a parancssori környezetben, és futtassa az alábbi parancsot:

```cmd\sh
docker-compose up
```

Ez a parancs első futtatásakor Docker letölti a mikroszolgáltatási képek Docker központ a tárolók helyileg létrehozásához. Utólagosan, Docker a tárolók a rendszer azonnal futtatja.

Egy külön rendszerhéj-tárolójából. a naplók megtekintéséhez használhatja. Először a tárolót azonosító használatával található a `docker ps -a` parancsot. Ezután `docker logs {container-id} --tail 1000` megtekintése a legutóbbi 1000 naplóbejegyzéseket a megadott tároló.

A távoli felügyeleti megoldás irányítópultja eléréséhez lépjen [8080](http://localhost:8080) a böngészőben.

## <a name="tidy-up"></a>Rendezett

Elkerülése érdekében szükségtelen, ha befejezte a tesztelést, távolítsa el a felhőszolgáltatások az Azure-előfizetéshez. Távolítsa el a szolgáltatásokat a legegyszerűbb módja az Azure-portál használatával az erőforráscsoportot, hozta létre a `pcs` eszköz.

Használja a `docker-compose down --rmi all` parancs futtatásával távolítsa el a Docker képek és szabadítson fel lemezterületet a helyi számítógépen. A helyi példány, a távoli felügyeleti tárház jön létre, amikor a Githubból a forráskód klónozott is törölheti.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az előkonfigurált megoldás konfigurálása
> * Az előkonfigurált megoldás üzembe helyezéséhez
> * Jelentkezzen be az előkonfigurált megoldás

A távoli felügyeleti megoldás telepített, akkor a következő lépéssel fogja [megismerkedhet a megoldás irányítópultja képességeit](./iot-suite-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->