---
title: A Java távoli figyelési megoldás – Azure |} A Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan építheti ki a távoli figyelési megoldásgyorsító a parancssori felület használatával.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 09/12/2018
ms.topic: conceptual
ms.openlocfilehash: ddb0b5b1a0847200caa7d8d04ecdc9dab4c41d14
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49956697"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>A távoli figyelési megoldásgyorsító a CLI-vel üzembe helyezése

Ez az oktatóanyag bemutatja, hogyan építheti ki a távoli figyelési megoldásgyorsító. A megoldás a parancssori felület használatával telepít. A webes felhasználói felület használatával a azureiotsuite.com, további információ: a beállítás a megoldást is telepíthet [a távoli figyelési megoldásgyorsító üzembe helyezése](quickstart-remote-monitoring-deploy.md).

## <a name="prerequisites"></a>Előfeltételek

A távoli figyelési megoldásgyorsító üzembe helyezése, aktív Azure-előfizetésre van szükség.

Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](http://azure.microsoft.com/pricing/free-trial/).

Futtassa a parancssori Felületet, kell [Node.js](https://nodejs.org/) telepítve a helyi gépen.

## <a name="install-the-cli"></a>A CLI telepítése

A CLI telepítéséhez futtassa a következő parancsot a parancssori környezetben:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Jelentkezzen be a parancssori felület

A megoldásgyorsító központi telepítése, jelentkezzen be az Azure-előfizetéshez a CLI használatával az alábbiak szerint:

```cmd/sh
pcs login
```

Kövesse a képernyőn megjelenő utasításokat a bejelentkezési folyamat befejezéséhez.

## <a name="deployment-options"></a>Üzembe helyezési beállítások

A megoldásgyorsító központi telepítésekor többféle módon, amely a telepítési folyamat konfigurálása:

| Beállítás | Értékek | Leírás |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | A _alapszintű_ központi telepítés a célja a teszt- és bemutatók, mindegyik mikroszolgáltatás egyetlen virtuális gép üzembe helyezve. A _standard_ központi telepítés a célja az éles környezetben ez üzembe helyezi a mikroszolgáltatásokat több virtuális gép. A _helyi_ telepítési konfigurálja egy Docker-tárolót, a mikroszolgáltatásokat futtathatók a helyi gépen, és használja az Azure-szolgáltatások, például a storage és a Cosmos DB, a felhőben. |
| Futtatókörnyezet | `dotnet`, `java` | A mikroszolgáltatások nyelvi végrehajtásának választja ki. |

A helyi üzemelő példányának használatával kapcsolatos tudnivalókért lásd: [helyben fut a távoli figyelési megoldás](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="basic-vs-standard-deployments"></a>Alapszintű vs. Normál telepítések

### <a name="basic"></a>Alapszintű
Alapszintű központi telepítést stíluskalauz amely azt mutatja be a megoldáshoz. Ez a bemutató költségeinek csökkentése érdekében minden mikroszolgáltatás egyetlen virtuális gép; telepítése Ez nem tekinthető meg egy éles használatra kész architektúra.

A szokásos központi telepítési beállítást kell használni, amikor készen áll egy éles használatra kész architektúrát, a méretezés és a bővíthetőségi beépített testreszabásához.

Alapszintű megoldások létrehozásának kiépítése folyamatban az Azure-előfizetésében az alábbi Azure-szolgáltatás eredményez: 

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
A normál telepítés az éles használatra kész üzemelő fejlesztői testreszabhatja és kibővítheti a saját igényeihez. A szokásos központi telepítési beállítást kell használni, amikor készen áll egy éles használatra kész architektúrát, a méretezés és a bővíthetőségi beépített testreszabásához. Alkalmazás mikroszolgáltatások beépített Docker-tárolókként, és az Azure Kubernetes Service (AKS) használatával telepíthetők. Az orchestrator telepítési, méretezését és felügyeletét, az alkalmazás feladata.


Standard szintű megoldások létrehozásának kiépítése folyamatban az Azure-előfizetésében az alábbi Azure-szolgáltatás eredményez:

| Darabszám | Erőforrás                                     | Termékváltozat / méretezés      | Használt |
|-------|----------------------------------------------|-----------------|----------|
| 1     | [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service)| Egy teljes körűen felügyelt Kubernetes tárolószervező szolgáltatást, az alapértelmezett érték 3 ügynökök használata|
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                     | S2 – Standard csomag | Eszközkezelés, a parancs és vezérlés |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | Standard        | Konfigurációs adatok és eszközök telemetriai például szabályok, a riasztások és az üzenetek tárolására |
| 5     | [Az Azure Storage-fiókok](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | Standard        | 4 – Virtuálisgép-tároló, a streamelési ellenőrzőpontokat 1 |
| 1     | [APP SERVICE](https://azure.microsoft.com/services/app-service/web/)             | S1 – Standard     | Az Application gateway SSL-en keresztül |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Felhasználói identitások kezelése és biztonsági |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Az eszközintelligencia helyek megtekintése |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 egység              | Valós idejű elemzési engedélyezése |
| 1     | [Az Azure Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Üzembe helyezési eszközöket bármilyen méretek |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 egység              | Tárolási üzenetek adatait, és lehetővé teszi, hogy részletesen telemetriai elemzés céljából |

> Ezek a szolgáltatások találja a díjszabásról [Itt](https://azure.microsoft.com/pricing). Használati összegek és az előfizetés számlázási adatait tekintheti meg a [az Azure Portal](https://portal.azure.com/).

## <a name="deploy-the-solution-accelerator"></a>A megoldásgyorsító üzembe helyezése

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

Ha futtatja a `pcs` parancsot a megoldás üzembe helyezéséhez megadását kéri:

- A megoldás nevét. A névnek egyedinek kell lennie.
- A használandó előfizetés.
- Egy helyen.
- Hitelesítő adatok a virtuális gépek, amelyeken a mikroszolgáltatás-alapú. Használhatja ezeket a hitelesítő adatokat a virtuális gépek hibaelhárítási eléréséhez.

Ha a `pcs` parancs végez, az új megoldás megoldásgyorsító üzembe helyezése URL-CÍMÉT jeleníti meg. A `pcs` parancs létrehoz egy fájlt is `{deployment-name}-output.json` kiegészítő információkkal, például a nevét, az üzembe helyezett IoT-központ.

A parancssori paraméterekkel kapcsolatos további információkért futtassa:

```cmd/sh
pcs -h
```

A parancssori felület kapcsolatos további információkért lásd: [a parancssori felület használata](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A megoldásgyorsító konfigurálása
> * A megoldásgyorsító üzembe helyezése
> * Jelentkezzen be a megoldásgyorsító

Most, hogy a távoli figyelési megoldás telepített, akkor a következő lépés, hogy [Fedezze fel a megoldás irányítópultján képességeit](./quickstart-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->
