---
title: A távfigyelési megoldás üzembe helyezése a CLI használatával - Azure | Microsoft dokumentumok
description: Ez az útmutató bemutatja, hogyan építheti ki a távfigyelési megoldásgyorsítót a CLI használatával.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: 501ca51a9542229a14e98a56679837950a82891e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258294"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>A távfigyelési megoldásgyorsító üzembe helyezése a CLI használatával

Ez az útmutató bemutatja, hogyan telepítheti a távoli figyelési megoldásgyorsítót. A megoldást a CLI használatával telepíti. A megoldást a webalapú felhasználói felület használatával is telepítheti a azureiotsolutions.com, hogy megismerje ezt a lehetőséget [a Távoli figyelési megoldás gyorsító telepítésének](quickstart-remote-monitoring-deploy.md) rövid útmutatójában.

## <a name="prerequisites"></a>Előfeltételek

A távoli figyelési megoldásgyorsító üzembe helyezéséhez aktív Azure-előfizetésre van szüksége.

Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

A CLI futtatásához [a Node.js programot](https://nodejs.org/) telepíteni kell a helyi számítógépre.

## <a name="install-the-cli"></a>A CLI telepítése

A CLI telepítéséhez futtassa a következő parancsot a parancssori környezetben:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Bejelentkezés a CLI-be

A megoldásgyorsító üzembe helyezése előtt be kell jelentkeznie az Azure-előfizetésbe a CLI használatával:

```cmd/sh
pcs login
```

Kövesse a képernyőn megjelenő utasításokat a bejelentkezési folyamat befejezéséhez.

## <a name="deployment-options"></a>Üzembe helyezési beállítások

A megoldásgyorsító telepítésekor számos lehetőség konfigurálható a telepítési folyamat:

| Beállítás | Értékek | Leírás |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | Az _alapszintű_ üzembe helyezés tesztelési és bemutatók, telepíti az összes mikroszolgáltatások egyetlen virtuális gépre. A _szabványos_ központi telepítés éles környezetben, a mikroszolgáltatások üzembe helyezése több virtuális gépeken. A _helyi_ központi telepítés konfigurálja a Docker-tároló a mikroszolgáltatások futtatásához a helyi gépen, és az Azure felhőszolgáltatások, például a storage és a Cosmos DB. |
| Futtatókörnyezet | `dotnet`, `java` | Kiválasztja a mikroszolgáltatások nyelvi implementációját. |

A helyi telepítési lehetőség használatáról a [Távfigyelési megoldás helyi futtatása című témakörben](iot-accelerators-remote-monitoring-deploy-local.md)olvashat.

## <a name="basic-and-standard-deployments"></a>Alapszintű és szabványos telepítések

Ez a szakasz összefoglalja az alapszintű és a szabványos központi telepítés közötti legfontosabb különbségeket.

### <a name="basic"></a>Basic

Az alapszintű üzembe helyezést [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) vagy a CLI használatával is elvégezheti.

Az alapszintű üzembe helyezés a megoldás bemutatására irányul. A költségek csökkentése érdekében az összes mikroszolgáltatások egyetlen virtuális gépen üzembe helyezése. Ez a központi telepítés nem használ éles használatra kész architektúrát.

Az alapszintű központi telepítés a következő szolgáltatásokat hozza létre az Azure-előfizetésben:

| Darabszám | Erőforrás                       | Típus         | Használt |
|-------|--------------------------------|--------------|----------|
| 1     | [Linuxos virtuális gép](https://azure.microsoft.com/services/virtual-machines/) | Standard D1 V2  | Mikroszolgáltatások üzemeltetése |
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                  | S1 – Standard szint | Eszközkezelés és kommunikáció |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | Standard        | Konfigurációs adatok, szabályok, riasztások és egyéb hűtőtárolók tárolása |  
| 1     | [Azure storage-fiók](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | Standard        | Virtuális gép és streamelési ellenőrzőpontok tárolása |
| 1     | [Webalkalmazás](https://azure.microsoft.com/services/app-service/web/)        |                 | Front-end webes alkalmazás üzemeltetése |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | A felhasználói identitások és a biztonság kezelése |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Eszközhelyek megtekintése |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 egység              | Valós idejű elemzés engedélyezése |
| 1     | [Azure-eszközkiépítési szolgáltatás](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Eszközök kiépítése nagy méretekben |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 egység              | Üzenetek adatainak tárolása és részletes adatkapcsolat-elemzés engedélyezése |

### <a name="standard"></a>Standard

A szabványos központi telepítést csak a CLI használatával teheti meg.

A szabványos központi telepítés egy éles használatra kész központi telepítés, amelyet a fejlesztő testre szabhat és kiterjeszthet. Használja a szabványos üzembe helyezési lehetőséget, ha készen áll a méretezésre és a bővíthetőségre tervezett éles használatra kész architektúra testreszabására. Az alkalmazás mikroszolgáltatásai Docker-tárolókként vannak kialakítva, és az Azure Kubernetes-szolgáltatás használatával vannak telepítve. A Kubernetes orchestrator üzembe helyezi, skálázja és kezeli a mikroszolgáltatások.

A szabványos központi telepítés a következő szolgáltatásokat hozza létre az Azure-előfizetésben:

| Darabszám | Erőforrás                                     | Termékváltozat / méret      | Használt |
|-------|----------------------------------------------|-----------------|----------|
| 1     | [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service)| Teljes körűen felügyelt Kubernetes-tároló vezénylési szolgáltatás használata, alapértelmezés szerint 3 ügynök|
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                     | S2 – Standard szint | Eszközkezelés, -vezérlés és -vezérlés |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | Standard        | Konfigurációs adatok és eszköztelemetriai adatok, például szabályok, riasztások és üzenetek tárolása |
| 5     | [Azure storage-fiókok](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | Standard        | 4 a virtuális gép tárolásához és 1 a streamelési ellenőrzőpontokhoz |
| 1     | [APP SERVICE](https://azure.microsoft.com/services/app-service/web/)             | S1 – Standard     | Alkalmazásátjáró A TLS-en keresztül |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | A felhasználói identitások és a biztonság kezelése |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Eszközhelyek megtekintése |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 egység              | Valós idejű elemzés engedélyezése |
| 1     | [Azure-eszközkiépítési szolgáltatás](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Eszközök kiépítése nagy méretekben |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 egység              | Üzenetek adatainak tárolása és részletes adatkapcsolat-elemzés engedélyezése |

> [!NOTE]
> Ezekhez a szolgáltatásokhoz díjszabási információkat a. [https://azure.microsoft.com/pricing](https://azure.microsoft.com/pricing) Az előfizetés használati és számlázási adatait az [Azure Portalon](https://portal.azure.com/)találja.

## <a name="deploy-the-solution-accelerator"></a>A megoldásgyorsító üzembe helyezése

Telepítési példák:

### <a name="example-deploy-net-version"></a>Példa: a .NET verzió telepítése

A következő példa bemutatja, hogyan telepítheti a távfigyelési megoldásgyorsító alapszintű .

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Példa: Java-verzió telepítése

A következő példa bemutatja, hogyan telepítheti a távoli figyelési megoldásgyorsító szabványos Java-verzióját:

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>pcs parancs beállításai

Amikor futtatja `pcs` a parancsot egy megoldás üzembe helyezéséhez, a rendszer a következőket kéri:

- A megoldás neve. A névnek egyedinek kell lennie.
- A használandó előfizetés.
- Egy helyszín.
- A mikroszolgáltatásokat tároló virtuális gépek hitelesítő adatait. Ezekkel a hitelesítő adatokkal érheti el a virtuális gépeket hibaelhárításhoz.

Amikor `pcs` a parancs befejeződik, megjeleníti az új megoldásgyorsító URL-címét. A `pcs` parancs egy `{deployment-name}-output.json` olyan fájlt is létrehoz, amely olyan információkat tartalmaz, mint például az általa létrehozott IoT Hub neve.

A parancssori paraméterekkel kapcsolatos további tudnivalókért futtassa a következő műveleteket:

```cmd/sh
pcs -h
```

A CLI-ről a [CLI használata](https://github.com/Azure/pcs-cli/blob/master/README.md)című témakörben talál további információt.

## <a name="next-steps"></a>További lépések

Ebben az útmutatóútmutatóban megtanulta, hogyan:

> [!div class="checklist"]
> * A megoldásgyorsító konfigurálása
> * A megoldásgyorsító üzembe helyezése
> * Bejelentkezés a megoldásgyorsítóba

Most, hogy telepítette a távoli figyelési megoldást, a következő lépés [a megoldásirányítópult képességeinek feltárása.](./quickstart-remote-monitoring-deploy.md)

<!-- Next how-to guides in the sequence -->
