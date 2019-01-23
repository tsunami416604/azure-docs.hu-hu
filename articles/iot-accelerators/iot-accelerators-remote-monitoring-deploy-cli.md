---
title: A távoli figyelési megoldás parancssori felületével – Azure |} A Microsoft Docs
description: Ez az útmutató bemutatja, hogyan építheti ki a távoli figyelési megoldásgyorsító a parancssori felület használatával.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/30/2018
ms.topic: conceptual
ms.openlocfilehash: 61da17d1dc59a08128671da163d8b3ea27f83994
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54463218"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>A távoli figyelési megoldásgyorsító a CLI-vel üzembe helyezése

Ez az útmutató bemutatja, hogyan lehet a távoli figyelési megoldásgyorsító üzembe helyezése. A megoldás a parancssori felület használatával telepít. A megoldás a webes felhasználói felület használatával a azureiotsuite.com, további információ: a beállítás is telepítheti a[a távoli figyelési megoldásgyorsító üzembe helyezése](quickstart-remote-monitoring-deploy.md) rövid.

## <a name="prerequisites"></a>Előfeltételek

A távoli figyelési megoldásgyorsító üzembe helyezése, aktív Azure-előfizetésre van szükség.

Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

Futtassa a parancssori Felületet, kell [Node.js](https://nodejs.org/) telepítve a helyi gépen.

## <a name="install-the-cli"></a>A CLI telepítése

A CLI telepítéséhez futtassa a következő parancsot a parancssori környezetben:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Jelentkezzen be a parancssori felület

A megoldásgyorsító központi telepítése, jelentkezzen be az Azure-előfizetéshez a CLI használatával:

```cmd/sh
pcs login
```

Kövesse a képernyőn megjelenő utasításokat a bejelentkezési folyamat befejezéséhez.

## <a name="deployment-options"></a>Üzembe helyezési beállítások

A megoldásgyorsító központi telepítésekor többféle módon, amely a telepítési folyamat konfigurálása:

| Beállítás | Értékek | Leírás |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | A _alapszintű_ központi telepítés a célja a teszt- és bemutatók, mindegyik mikroszolgáltatás egyetlen virtuális gép üzembe helyezve. A _standard_ telepítési szól ez üzembe helyezi a mikroszolgáltatásokat több virtuális gépek éles környezetben. A _helyi_ telepítési konfigurálja egy Docker-tárolót, a mikroszolgáltatásokat futtathatók a helyi gépen, és használja az Azure felhőszolgáltatások, például a storage és a Cosmos DB. |
| Futtatókörnyezet | `dotnet`, `java` | A mikroszolgáltatások nyelvi végrehajtásának választja ki. |

A helyi üzembe helyezési lehetőség használatával kapcsolatban lásd: [helyben fut a távoli figyelési megoldás](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="basic-and-standard-deployments"></a>Alap és normál központi telepítések

Ez a szakasz az alap és normál központi telepítés közötti lényeges különbségeket foglalja össze.

### <a name="basic"></a>Alapszintű

Az alapszintű telepítés a megoldás bemutatását szolgálja. Költségek csökkentése érdekében minden mikroszolgáltatás egyetlen virtuális gép helyezik üzembe. A központi telepítés nem használ egy éles használatra kész architektúra.

Alapszintű üzembe helyezéséhez az Azure-előfizetésben hoz létre a következő szolgáltatásokat:

| Darabszám | Erőforrás                       | Típus         | Használt |
|-------|--------------------------------|--------------|----------|
| 1     | [Linux rendszerű virtuális gép](https://azure.microsoft.com/services/virtual-machines/) | Standard D1 V2  | Mikroszolgáltatás-alapú üzemeltetéséhez |
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                  | S1 – Standard csomag | Kezelés és kommunikáció |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | Standard        | Konfigurációs adatok tárolását, szabályok, riasztások és más ritka elérésű tárolási |  
| 1     | [Azure Storage-tárfiók](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | Standard        | Virtuális gép és a streamelési ellenőrzőpontok |
| 1     | [Webalkalmazás](https://azure.microsoft.com/services/app-service/web/)        |                 | A webes előtér-alkalmazás üzemeltetése |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Felhasználói identitások kezelése és biztonsági |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Az eszközintelligencia helyek megtekintése |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 egység              | Valós idejű elemzési engedélyezése |
| 1     | [Az Azure Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Üzembe helyezési eszközöket bármilyen méretek |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 egység              | Tárolási üzenetek adatait, és lehetővé teszi, hogy részletesen telemetriai elemzés céljából |

### <a name="standard"></a>Standard

Normál telepítés az, hogy a fejlesztő testreszabhatja és kibővítheti éles használatra kész üzemelő. Használja a normál telepítés lehetőséget, ha készen áll egy éles használatra kész architektúrát, a méretezés és a bővíthetőségi beépített testreszabásához. Alkalmazás mikroszolgáltatások Docker-tárolókként épülnek, és az Azure Kubernetes Service használatával telepíthetők. A Kubernetes vezénylő helyez üzembe, méretezhető és felügyeli a mikroszolgáltatásokat.

Szabványos telepítés a következő szolgáltatásokat az Azure-előfizetésben hoz létre:

| Darabszám | Erőforrás                                     | Termékváltozat / méretezés      | Használt |
|-------|----------------------------------------------|-----------------|----------|
| 1     | [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service)| Egy teljes körűen felügyelt Kubernetes tárolószervező szolgáltatást, az alapértelmezett érték 3 ügynökök használata|
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                     | S2 – Standard csomag | Eszközkezelés, a parancs és vezérlés |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | Standard        | Konfigurációs adatok és eszközök telemetriát, például a szabályok, a riasztások és az üzenetek tárolására |
| 5     | [Az Azure Storage-fiókok](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | Standard        | 4 – Virtuálisgép-tároló, a streamelési ellenőrzőpontokat 1 |
| 1     | [APP SERVICE](https://azure.microsoft.com/services/app-service/web/)             | S1 – Standard     | Az Application gateway SSL-en keresztül |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Felhasználói identitások kezelése és biztonsági |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Az eszközintelligencia helyek megtekintése |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 egység              | Valós idejű elemzési engedélyezése |
| 1     | [Az Azure Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Üzembe helyezési eszközöket bármilyen méretek |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 egység              | Tárolási üzenetek adatait, és lehetővé teszi, hogy részletesen telemetriai elemzés céljából |

> [!NOTE]
> Annak a díjszabásról az alábbi szolgáltatások [ https://azure.microsoft.com/pricing ](https://azure.microsoft.com/pricing). Használati és számlázási részletek az Ön előfizetéséhez az [az Azure Portal](https://portal.azure.com/).

## <a name="deploy-the-solution-accelerator"></a>A megoldásgyorsító üzembe helyezése

Üzembe helyezés példák:

### <a name="example-deploy-net-version"></a>Példa: .NET-verzió telepítése

Az alábbi példa bemutatja, hogyan helyezhet üzembe a basic, a távoli figyelési megoldásgyorsító verziója .NET:

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Példa: a Java-verzió telepítése

Az alábbi példa bemutatja, hogyan helyezhet üzembe a távoli figyelési megoldásgyorsító a standard szintű, Java-verzió:

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>számítógépek parancs beállításai

Ha futtatja a `pcs` parancsot a megoldás üzembe helyezése, kéri a:

- A megoldás nevét. A névnek egyedinek kell lennie.
- A használandó előfizetés.
- Egy helyen.
- Hitelesítő adatok a virtuális gépek, amelyeken a mikroszolgáltatás-alapú. Használhatja ezeket a hitelesítő adatokat a virtuális gépek hibaelhárítási eléréséhez.

Ha a `pcs` parancs végez, az új megoldásgyorsító URL-CÍMÉT jeleníti meg. A `pcs` parancs létrehoz egy fájlt is `{deployment-name}-output.json` , amely az IoT Hub által létrehozott, például a nevét információkat tartalmaz.

A parancssori paraméterekkel kapcsolatos további információkért futtassa:

```cmd/sh
pcs -h
```

A parancssori felület kapcsolatos további információkért lásd: [a parancssori felület használata](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="next-steps"></a>További lépések

Ez az útmutató, megtanulta, hogyan lehet:

> [!div class="checklist"]
> * A megoldásgyorsító konfigurálása
> * A megoldásgyorsító üzembe helyezése
> * Jelentkezzen be a megoldásgyorsító

Most, hogy a távoli figyelési megoldás üzembe helyezte, a következő lépés, hogy [Fedezze fel a megoldás irányítópultján képességeit](./quickstart-remote-monitoring-deploy.md).

<!-- Next how-to guides in the sequence -->
