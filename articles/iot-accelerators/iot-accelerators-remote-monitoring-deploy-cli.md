---
title: A távoli figyelési megoldás üzembe helyezése a parancssori felület használatával – Azure | Microsoft Docs
description: Ez a útmutató bemutatja, hogyan építheti ki a távoli figyelési megoldás Gyorssegédjét a parancssori felület használatával.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: ea96b2b996ea79efacdcda50c6370f25e26e0aa2
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78383113"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>A távoli figyelési megoldás gyorsító üzembe helyezése a parancssori felület használatával

Ez a útmutató bemutatja, hogyan helyezheti üzembe a távoli figyelési megoldás gyorsító. A megoldást a parancssori felület használatával helyezheti üzembe. A megoldást a azureiotsolutions.com webalapú felhasználói felületén is üzembe helyezheti, ha többet szeretne tudni erről a lehetőségről, tekintse meg a következő témakört: a [távoli figyelési megoldás telepítése gyorsító](quickstart-remote-monitoring-deploy.md) útmutatója.

## <a name="prerequisites"></a>Előfeltételek

A távoli figyelési megoldás-gyorsító üzembe helyezéséhez aktív Azure-előfizetésre van szükség.

Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

A CLI futtatásához a helyi gépen telepíteni kell a [Node. js fájlt](https://nodejs.org/) .

## <a name="install-the-cli"></a>A CLI telepítése

A CLI telepítéséhez futtassa a következő parancsot a parancssori környezetben:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Bejelentkezés a CLI-be

A megoldás-gyorsító üzembe helyezése előtt be kell jelentkeznie az Azure-előfizetésbe a parancssori felület használatával:

```cmd/sh
pcs login
```

A bejelentkezési folyamat befejezéséhez kövesse a képernyőn megjelenő utasításokat.

## <a name="deployment-options"></a>Üzembe helyezési beállítások

A megoldás-gyorsító telepítésekor több lehetőség is van a telepítési folyamat konfigurálására:

| Beállítás | Értékek | Leírás |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | Az _alapszintű_ üzembe helyezés tesztelési és demonstrációs célokra készült, és egyetlen virtuális gépre telepíti az összes szolgáltatást. A _standard szintű_ üzembe helyezés éles környezetben üzemel, és több virtuális gépre helyezi üzembe a szolgáltatást. A _helyi_ telepítés egy Docker-tárolót állít be, amely a helyi gépen futtatja a-szolgáltatásokat, és az Azure Cloud Servicest (például storage és Cosmos db) használja. |
| Futtatókörnyezet | `dotnet`, `java` | Kiválasztja a webszolgáltatás nyelvi megvalósítását. |

A helyi telepítési lehetőség használatáról további információt [a távoli figyelési megoldás helyi futtatása](iot-accelerators-remote-monitoring-deploy-local.md)című témakörben talál.

## <a name="basic-and-standard-deployments"></a>Alapszintű és standard központi telepítések

Ez a szakasz az alapszintű és a normál üzemelő példány közötti fő különbségeket összegzi.

### <a name="basic"></a>Alapszintű

Az alapszintű telepítést a [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) vagy a parancssori felület használatával végezheti el.

Az alapszintű üzembe helyezés a megoldás bemutatását célozza. A költségek csökkentése érdekében az összes szolgáltatás üzembe helyezése egyetlen virtuális gépen történik. Ez a központi telepítés nem használ éles használatra kész architektúrát.

Az alapszintű üzembe helyezés a következő szolgáltatásokat hozza létre az Azure-előfizetésében:

| Darabszám | Erőforrás                       | Típus         | Használatban |
|-------|--------------------------------|--------------|----------|
| 1     | [Linuxos virtuális gép](https://azure.microsoft.com/services/virtual-machines/) | Standard D1 v2  | Szolgáltatók üzemeltetése |
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                  | S1 – standard szint | Eszközkezelés és kommunikáció |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | Standard        | A konfigurációs, a szabályok, a riasztások és az egyéb hűtőházi tárolók tárolása |  
| 1     | [Azure Storage-tárfiók](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | Standard        | Tároló a virtuális gép és a folyamatos átviteli ellenőrzőpontok számára |
| 1     | [Webalkalmazás](https://azure.microsoft.com/services/app-service/web/)        |                 | Előtér-webalkalmazás üzemeltetése |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Felhasználói identitások és biztonság kezelése |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Az eszköz helyeinek megtekintése |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 egység              | Valós idejű elemzések engedélyezése |
| 1     | [Azure Device kiépítési szolgáltatás](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Eszközök kiépítés nagy méretben |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 egység              | Az üzenetek tárolására szolgáló tárterület, amely lehetővé teszi a részletes telemetria elemzését |

### <a name="standard"></a>Standard

A normál telepítést csak a parancssori felület használatával végezheti el.

A normál üzembe helyezés olyan éles használatra kész környezet, amelyet a fejlesztő testre szabhat és kiterjeszt. A normál üzembe helyezési lehetőség használata, ha készen áll a méretezésre és bővíthetőségre épülő, éles használatra kész architektúra testre szabására. Az Application Service-szolgáltatások a Docker-tárolók és az Azure Kubernetes szolgáltatás használatával telepíthetők. A Kubernetes Orchestrator üzembe helyezi, méretezi és kezeli a-szolgáltatásokat.

A standard szintű üzembe helyezés a következő szolgáltatásokat hozza létre az Azure-előfizetésében:

| Darabszám | Erőforrás                                     | SKU/méret      | Használatban |
|-------|----------------------------------------------|-----------------|----------|
| 1     | [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service)| Teljes körűen felügyelt Kubernetes-tárolói szolgáltatás használata, alapértelmezett érték 3 ügynök|
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                     | S2 – standard szint | Eszközkezelés, parancs és vezérlés |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | Standard        | A konfigurációs és az eszközök telemetria (például szabályok, riasztások és üzenetek) tárolása |
| 5     | [Azure Storage-fiókok](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | Standard        | 4 virtuálisgép-tároláshoz és 1 a folyamatos átviteli ellenőrzőpontokhoz |
| 1     | [APP SERVICE](https://azure.microsoft.com/services/app-service/web/)             | S1 – Standard     | Application Gateway SSL-en keresztül |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Felhasználói identitások és biztonság kezelése |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Az eszköz helyeinek megtekintése |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 egység              | Valós idejű elemzések engedélyezése |
| 1     | [Azure Device kiépítési szolgáltatás](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Eszközök kiépítés nagy méretben |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 egység              | Az üzenetek tárolására szolgáló tárterület, amely lehetővé teszi a részletes telemetria elemzését |

> [!NOTE]
> Ezekről a szolgáltatásokról a [https://azure.microsoft.com/pricing](https://azure.microsoft.com/pricing)címen talál díjszabási információt. Az előfizetésre vonatkozó használati és számlázási adatokat az [Azure Portalon](https://portal.azure.com/)találhatja meg.

## <a name="deploy-the-solution-accelerator"></a>A megoldás-gyorsító üzembe helyezése

Üzembe helyezési példák:

### <a name="example-deploy-net-version"></a>Példa: .NET-verzió telepítése

Az alábbi példa bemutatja, hogyan helyezheti üzembe a távoli figyelési megoldás gyorsító alapszintű, .NET-es verzióját:

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Példa: Java-verzió telepítése

Az alábbi példa bemutatja, hogyan helyezheti üzembe a távoli figyelési megoldás-gyorsító standard, Java-verzióját:

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>számítógépek parancs beállításai

Ha a `pcs` parancsot a megoldás üzembe helyezéséhez futtatja, a rendszer a következőt kéri:

- A megoldás neve. A névnek egyedinek kell lennie.
- A használandó előfizetés.
- Egy hely.
- A szolgáltatást futtató virtuális gépek hitelesítő adatai. Ezeket a hitelesítő adatokat használhatja a virtuális gépek hibaelhárításhoz való hozzáféréséhez.

Amikor az `pcs` parancs befejeződik, megjelenik az új megoldás-gyorsító URL-címe. A `pcs` parancs egy olyan fájl `{deployment-name}-output.json` is létrehoz, amely olyan adatokat tartalmaz, mint például a létrehozott IoT Hub neve.

A parancssori paraméterekkel kapcsolatos további információkért futtassa a következőt:

```cmd/sh
pcs -h
```

További információ a parancssori felületről: [a parancssori felület használata](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="next-steps"></a>Következő lépések

Ebben a útmutatóban megtanulta, hogyan végezheti el a következőket:

> [!div class="checklist"]
> * A megoldásgyorsító konfigurálása
> * A megoldás-gyorsító üzembe helyezése
> * Bejelentkezés a megoldás-gyorsító

Most, hogy üzembe helyezte a távoli figyelési megoldást, a következő lépés a [megoldás irányítópultjának képességeinek megismerése](./quickstart-remote-monitoring-deploy.md).

<!-- Next how-to guides in the sequence -->
