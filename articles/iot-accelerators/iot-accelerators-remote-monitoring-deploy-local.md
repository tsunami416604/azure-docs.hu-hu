---
title: Azure - helyileg a távoli felügyeleti megoldás üzembe helyezéséhez |} Microsoft Docs
description: Az oktatóanyag bemutatja, tesztelése és fejlesztési a helyi számítógép a távoli felügyeleti megoldásgyorsító központi telepítése.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/07/2018
ms.topic: conceptual
ms.openlocfilehash: 3f723d716a652e64527310a499d6b06a6cf6bc6f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627231"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally"></a>A távoli megfigyelési megoldásgyorsító helyileg telepíteni

Ez a cikk bemutatja a teszteléshez és a fejlesztői a helyi számítógép a távoli megfigyelési megoldásgyorsító központi telepítése. Ez a megközelítés a mikroszolgáltatások telepíti egy helyi Docker-tároló, és a felhőben az IoT-központ, a Cosmos-adatbázis és az Azure storage szolgáltatások használja. A megoldás gyorsítók (számítógépek) parancssori felület telepítése az Azure felhőszolgáltatások használhatja.

## <a name="prerequisites"></a>Előfeltételek

A távoli megfigyelési megoldásgyorsító által használt Azure-szolgáltatásokhoz központi telepítéséhez aktív Azure-előfizetés szükséges.

Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](http://azure.microsoft.com/pricing/free-trial/).

A helyi központi telepítés befejezéséhez szüksége van a helyi fejlesztői gépen telepítve van a következő eszközöket:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Docker compose](https://docs.docker.com/compose/install/)
* [NODE.js](https://nodejs.org/) -ezt a szoftvert a számítógépek CLI előfeltétele.
* SZÁMÍTÓGÉPEK PARANCSSORI FELÜLET
* A forráskód helyi tárház

> [!NOTE]
> Ezek az eszközök számos platformon, beleértve a Windows, Linux és iOS érhetők el.

### <a name="install-the-pcs-cli"></a>A számítógépek parancssori felület telepítése

A számítógépek CLI npm telepítéséhez futtassa a következő parancsot a parancssori környezetben:

```cmd/sh
npm install iot-solutions -g
```

A parancssori Felülettel kapcsolatos további információkért lásd: [a CLI használata](https://github.com/Azure/pcs-cli/blob/master/README.md).

### <a name="download-the-source-code"></a>A forráskód letöltése

 A távoli megfigyelési forráskódraktárban magában foglalja a Docker konfigurációs fájlokat kell letölteni, konfigurálásához és futtatásához a Docker-lemezképeket, amelyek tartalmazzák a mikroszolgáltatások létrehozására. Klónozza, és a tárház helyi verziót hoz létre, keresse meg a megfelelő mappát a helyi számítógépen a kedvenc parancssor vagy a Terminálszolgáltatások keresztül, és futtassa a következő parancsok egyikét:

A Java implementációja a mikroszolgáltatások telepítéséhez futtassa:

```cmd/sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-java
```

A mikroszolgáltatások implementációja a .net telepítéséhez futtassa:

```cmd\sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet
```

Távoli Monioring előre konfigurált megoldás tárház & submodules [ [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java) | [.Net](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) ]

> [!NOTE]
> Ezek a parancsok a mikroszolgáltatások forráskódja letöltése. A forráskód akkor hasznos, ha később szeretne az előkonfigurált megoldás módosítása és tesztelése a módosításokat a helyi, nincs szüksége a forráskódra futtassa a mikroszolgáltatások Docker, bár.

## <a name="deploy-the-azure-services"></a>Az Azure-szolgáltatások telepítése

Bár ez a cikk bemutatja, hogyan futtassa helyileg a mikroszolgáltatások létrehozására, a felhőben futó három Azure-szolgáltatások függenek. Az Azure-szolgáltatások telepítése [manuálisan az Azure portálon keresztül](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Manual-steps-to-create-azure-resources-for-local-setup), vagy a számítógépek CLI-t használja. Ez a cikk bemutatja, hogyan használható a `pcs` eszköz.

### <a name="sign-in-to-the-cli"></a>Jelentkezzen be a parancssori felület

A megoldásgyorsító telepítése előtt be kell jelentkeznie az Azure-előfizetéshez a parancssori felület használatával az alábbiak szerint:

```cmd/sh
pcs login
```

Kövesse a képernyőn megjelenő utasításokat a bejelentkezési folyamat befejezéséhez. Győződjön meg arról, hogy nem kattint bárhol a belső, a parancssori felületen vagy a bejelentkezés sikertelen lehet. Ha befejezte a bejelentkezési látni fogja a CLI sikeres bejelentkezés üzenetet. 

### <a name="run-a-local-deployment"></a>Egy helyi központi telepítés

Használja a következő parancsot a helyi központi telepítésének elindítása. Ez a szükséges azure-erőforrások létrehozása, és nyomtassa ki a konzol environemnt változókat. 

```cmd/pcs
pcs -s local
```

A parancsprogram kéri a következő információkat:

* A megoldás neve.
* A használandó előfizetés.
* Az Azure-adatközpontban használandó helyét.

> [!NOTE]
> A parancsfájl létrehozza az IoT-központ példány, egy Cosmos DB példány és egy Azure storage-fiókot az Azure-előfizetéshez az erőforráscsoportban. Az erőforráscsoport neve nem a választott kulcsfájloknak megoldást nevét a `pcs` fenti eszköz. 

> [!IMPORTANT]
> A parancsfájl futtatásához több percet vesz igénybe. Ha elkészült, megjelenik egy üzenet `Copy the following environment variables to /scripts/local/.env file:`. Másolás le a környezeti változó definíciók, az üzenet a következő használhatja őket egy későbbi lépésben.

## <a name="run-the-microservices-in-docker"></a>Futtassa a mikroszolgáltatások Docker

Futtassa a mikroszolgáltatások Docker, először módosítsa a **parancsfájlok\\helyi\\.env** fájlt a fenti korábbi lépésben klónozott tárház helyi példánya. Cserélje le a fájl teljes tartalmát a kulcsfájloknak feljegyezte környezeti változó definíciókat a `pcs` parancsot az előző lépésben. Ezek a környezeti változók a mikroszolgáltatások létrehozására az a Docker-tároló által létrehozott Azure-szolgáltatásokhoz való csatlakozás engedélyezése a `pcs` eszköz.

Futtassa a megoldásgyorsító, lépjen a **scripts\local** mappa a parancssori környezetben, és futtassa az alábbi parancsot:

```cmd\sh
docker-compose up
```

Ez a parancs első futtatásakor Docker letölti a mikroszolgáltatási képek Docker központ a tárolók helyileg létrehozásához. Utólagosan, Docker a tárolók a rendszer azonnal futtatja.

Egy külön rendszerhéj-tárolójából. a naplók megtekintéséhez használhatja. Először a tárolót azonosító használatával található a `docker ps -a` parancsot. Ezután `docker logs {container-id} --tail 1000` megtekintése a legutóbbi 1000 naplóbejegyzéseket a megadott tároló.

A távoli figyelési megoldást irányítópult eléréséhez lépjen [ http://localhost:8080 ](http://localhost:8080) a böngészőben.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Elkerülése érdekében szükségtelen, ha befejezte a tesztelést, távolítsa el a felhőszolgáltatások az Azure-előfizetéshez. Távolítsa el a szolgáltatásokat a legegyszerűbb módja lehetőségre, és navigáljon a [Azure-portálon](https://ms.portal.azure.com) segítségével létrehozott erőforráscsoport törlése és a `pcs` eszköz.

Használja a `docker-compose down --rmi all` parancs futtatásával távolítsa el a Docker képek és szabadítson fel lemezterületet a helyi számítógépen. A helyi példány, a távoli megfigyelési jön létre, amikor a Githubból a forráskód klónozott tárház is törölheti.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Egy helyi fejlesztési környezet beállítása
> * A megoldásgyorsító konfigurálása
> * A megoldásgyorsító központi telepítése
> * Jelentkezzen be a megoldásgyorsító

A távoli figyelésére szolgáló megoldás telepített, akkor a következő lépéssel fogja [megismerkedhet a megoldás irányítópultja képességeit](iot-accelerators-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->