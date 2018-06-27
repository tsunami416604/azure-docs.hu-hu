---
title: A Java távoli figyelésére szolgáló megoldás - Azure telepítése |} Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan lehet kiépíteni a távoli megfigyelési megoldásgyorsító a parancssori felület használatával.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: 736d0394b61bd2830a155d6ad714a2a8d19af82b
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37017509"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>Telepítheti a távoli megfigyelési megoldásgyorsító a parancssori felület használatával

Ez az oktatóanyag bemutatja, hogyan a távoli megfigyelési megoldásgyorsító kiépítéséhez. A megoldás a parancssori felület használatával telepít. A webes felhasználói felület használata azureiotsuite.com, a további tudnivalók a beállítás további részletekért lásd a megoldás is telepíthet [telepíteni a távoli megfigyelési megoldásgyorsító](iot-accelerators-remote-monitoring-deploy.md).

## <a name="prerequisites"></a>Előfeltételek

A távoli megfigyelési megoldásgyorsító központi telepítéséhez aktív Azure-előfizetés szükséges.

Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](http://azure.microsoft.com/pricing/free-trial/).

A parancssori felület futtatásához szükséges [Node.js](https://nodejs.org/) a helyi gépen telepítve van.

## <a name="install-the-cli"></a>A CLI telepítése

A parancssori felület telepítése, a következő parancsot a parancssori környezetben:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Jelentkezzen be a parancssori felület

A megoldásgyorsító telepítése előtt be kell jelentkeznie az Azure-előfizetéshez a parancssori felület használatával az alábbiak szerint:

```cmd/sh
pcs login
```

Kövesse a képernyőn megjelenő utasításokat a bejelentkezési folyamat befejezéséhez.

## <a name="deployment-options"></a>Üzembe helyezési lehetőségek

A megoldásgyorsító központi telepítésekor többféle módon, hogy a telepítési folyamat konfigurálása:

| Beállítás | Értékek | Leírás |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | A _alapvető_ központi telepítés a célja a teszt-és bemutatók, egyetlen virtuális gépre telepíti a mikroszolgáltatások létrehozására. A _szabványos_ központi telepítés a célja az üzemi, több virtuális gép telepíti a mikroszolgáltatások létrehozására. A _helyi_ telepítési konfigurál egy Docker-tároló a helyi számítógépen a mikroszolgáltatások futtatásához, és használja az Azure-szolgáltatások, például a tárolási és Cosmos DB, a felhőben. |
| Futtatókörnyezet | `dotnet`, `java` | Kiválasztja a mikroszolgáltatások nyelvi végrehajtására. |

A helyi telepítés használatával kapcsolatos további tudnivalókért lásd: [helyben fut a távoli figyelésére szolgáló megoldás](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Running-the-Remote-Monitoring-Solution-Locally#deploy-azure-services-and-set-environment-variables).

## <a name="basic-vs-standard-deployments"></a>Alapszintű Visual Studio. Normál központi telepítések

### <a name="basic"></a>Alapszintű
Alapszintű központi telepítést célközönségre területeknek a megoldás. Ebben a bemutatóban költségeinek csökkentése érdekében a mikroszolgáltatások telepített egyetlen virtuális gép; Ez egy éles használatra kész architektúra nem tekinthető.

A szokásos központi telepítési beállítást kell használni, amikor készen áll egy éles használatra kész architektúra, a méretezés és a bővítési beépített testreszabásához.

Alapvető létrehozása az Azure-szolgáltatásokat az Azure-előfizetéshez költségekkel be telepítése folyamatban eredményezi: 

| Darabszám | Erőforrás                       | Típus         | A használt |
|-------|--------------------------------|--------------|----------|
| 1     | [Linux virtuális gép](https://azure.microsoft.com/services/virtual-machines/) | Standard D1 V2  | Üzemeltetési mikroszolgáltatások |
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                  | S1 – Standard csomag | Eszközkezelés és kommunikáció |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | Standard        | Konfigurációs adatok és a telemetriát például szabályok, a riasztások és az üzenetek tárolásához |  
| 1     | [Azure Storage-fiók](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | Standard        | A virtuális gép és ellenőrzőpontokat streaming tárolási |
| 1     | [Webalkalmazás](https://azure.microsoft.com/services/app-service/web/)        |                 | Üzemeltetési előtér-webalkalmazás |

### <a name="standard"></a>Standard
A normál telepítése éles használatra kész központi telepítés egy fejlesztő testreszabása, és terjessze ki a saját igényeihez. A megbízhatóság és a skála alkalmazás mikroszolgáltatások Docker tárolóként épülnek, és telepített egy orchestrator ([Kubernetes](https://kubernetes.io/) alapértelmezés szerint). A koordináló rendszer feladata telepítés, a méretezés és a az alkalmazás felügyelete szempontjából.

A szokásos megoldás létrehozása az Azure-szolgáltatásokat az Azure-előfizetéshez költségekkel be telepítése folyamatban eredményezi:

| Darabszám | Erőforrás                                     | SKU / Size      | A használt |
|-------|----------------------------------------------|-----------------|----------|
| 4     | [Linux rendszerű virtuális gépek](https://azure.microsoft.com/services/virtual-machines/)   | Standard D2 V2  | 1 főkiszolgáló és mikroszolgáltatások üzemeltetéséhez redundanciával 3 ügynökök |
| 1     | [Az Azure Tárolószolgáltatásban](https://azure.microsoft.com/services/container-service/) |                 | [Kubernetes](https://kubernetes.io) orchestrator |
| 1     | [Az azure IoT Hub] [https://azure.microsoft.com/services/iot-hub/]                     | S2 – Standard csomag | Eszközök felügyelete, a parancs és a vezérlő |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | Standard        | Konfigurációs adatok és a telemetriát például szabályok, a riasztások és az üzenetek tárolásához |
| 5     | [Az Azure Storage-fiókok](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | Standard        | a Virtuálisgép-tároló és az adatfolyam-továbbítási ellenőrzőpontok 1 4 |
| 1     | [APP SERVICE](https://azure.microsoft.com/services/app-service/web/)             | S1 – Standard     | Alkalmazásátjáró SSL-en keresztül |

> Díjszabási információkért a szolgáltatásokról találhatók [Itt](https://azure.microsoft.com/pricing). Használati kvótákról és az előfizetés számlázási részletek megtalálhatók a [Azure Portal](https://portal.azure.com/).

## <a name="deploy-the-solution-accelerator"></a>A megoldásgyorsító központi telepítése

### <a name="example-deploy-net-version"></a>Példa: telepítse a .NET-verziót

A következő példa bemutatja, hogyan telepítse a távoli megfigyelési megoldásgyorsító alapvető, .NET verzióját:

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Példa: a Java-verzió telepítése

A következő példa bemutatja, hogyan telepítse a távoli megfigyelési megoldásgyorsító szabványos, Java verzióját:

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>számítógépek parancs beállításai

Amikor futtatja a `pcs` parancs a megoldás üzembe helyezéséhez meg kell adnia azokat a:

- A megoldás nevét. A névnek egyedinek kell lennie.
- A használandó előfizetés.
- Egy hely.
- A virtuális gépek hitelesítő adatait, a mikroszolgáltatások üzemeltetésére. A virtuális gépek hibaelhárítási eléréséhez használhatja ezeket a hitelesítő adatokat.

Ha a `pcs` parancs végrehajtása, az új megoldás gyorsító telepítési URL-CÍMÉT jeleníti meg. A `pcs` parancs létrehoz egy fájlt is `{deployment-name}-output.json` az IoT Hub, amely lett kiépítve, például a nevét, további információkkal.

A parancssori paraméterekkel kapcsolatos további információért futtatni:

```cmd/sh
pcs -h
```

A parancssori Felülettel kapcsolatos további információkért lásd: [a CLI használata](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A megoldásgyorsító konfigurálása
> * A megoldásgyorsító központi telepítése
> * Jelentkezzen be a megoldásgyorsító

A távoli figyelésére szolgáló megoldás telepített, akkor a következő lépéssel fogja [megismerkedhet a megoldás irányítópultja képességeit](./iot-accelerators-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->
