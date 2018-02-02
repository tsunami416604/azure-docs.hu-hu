---
title: "A távoli felügyeleti megoldás - Azure Java telepítése |} Microsoft Docs"
description: "Ez az oktatóanyag bemutatja, hogyan lehet kiépíteni a távoli figyelési előkonfigurált megoldás, a parancssori felület használatával."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/29/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 94c3db3286623264e9df7873962d10dd5cc662d4
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="deploy-the-remote-monitoring-preconfigured-solution-using-the-cli"></a>A távoli figyelési előkonfigurált megoldás, a parancssori felület használatával

Ez az oktatóprogram bemutatja, hogyan építheti ki az előre konfigurált távoli figyelő megoldást. A megoldás a parancssori felület használatával telepít. A webes felhasználói felület használata azureiotsuite.com, a további tudnivalók a beállítás további részletekért lásd a megoldás is telepíthet [a távoli felügyeleti előkonfigurált megoldás üzembe helyezéséhez](iot-suite-remote-monitoring-deploy.md).

## <a name="prerequisites"></a>Előfeltételek

A távoli felügyeleti előkonfigurált megoldás üzembe helyezéséhez, aktív Azure-előfizetés szükséges.

Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](http://azure.microsoft.com/pricing/free-trial/).

A parancssori felület futtatásához szükséges [Node.js](https://nodejs.org/) a helyi gépen telepítve van.

## <a name="install-the-cli"></a>A CLI telepítése

A parancssori felület telepítése, a következő parancsot a parancssori környezetben:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Jelentkezzen be a parancssori felület

Az előkonfigurált megoldás telepítése előtt be kell jelentkeznie az Azure-előfizetéshez a parancssori felület használatával az alábbiak szerint:

```cmd/sh
pcs login
```

Kövesse a képernyőn megjelenő utasításokat a bejelentkezési folyamat befejezéséhez.

## <a name="deployment-options"></a>Üzembe helyezési lehetőségek

Az előkonfigurált megoldás telepítésekor többféle módon, hogy a telepítési folyamat konfigurálása:

| Beállítás | Értékek | Leírás |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | A _alapvető_ központi telepítés a célja a teszt-és bemutatók, egyetlen virtuális gépre telepíti a mikroszolgáltatások létrehozására. A _szabványos_ központi telepítés a célja az üzemi, több virtuális gép telepíti a mikroszolgáltatások létrehozására. A _helyi_ telepítési konfigurál egy Docker-tároló a helyi számítógépen a mikroszolgáltatások futtatásához, és használja az Azure-szolgáltatások, például a tárolási és Cosmos DB, a felhőben. |
| Futtatókörnyezet | `dotnet`, `java` | Kiválasztja a mikroszolgáltatások nyelvi végrehajtására. |

A helyi telepítés használatával kapcsolatos további tudnivalókért lásd: [helyben fut a távoli felügyeleti megoldás](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Running-the-Remote-Monitoring-Solution-Locally#deploy-azure-services-and-set-environment-variables).

## <a name="deploy-the-preconfigured-solution"></a>Az előkonfigurált megoldás üzembe helyezéséhez

### <a name="example-deploy-net-version"></a>Példa: telepítse a .NET-verziót

A következő példa bemutatja, hogyan telepítse a távoli felügyeleti előkonfigurált megoldás alapvető, .NET verzióját:

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Példa: a Java-verzió telepítése

A következő példa bemutatja, hogyan telepítse a távoli felügyeleti előkonfigurált megoldás szabványos, Java verzióját:

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>számítógépek parancs beállításai

Amikor futtatja a `pcs` parancs a megoldás üzembe helyezéséhez meg kell adnia azokat a:

- A megoldás nevét. Ez a név nem egyedi.
- A használandó előfizetés.
- Egy hely.
- A virtuális gépek hitelesítő adatait, a mikroszolgáltatások üzemeltetésére. A virtuális gépek hibaelhárítási eléréséhez használhatja ezeket a hitelesítő adatokat.

Ha a `pcs` parancs végrehajtása, az új előkonfigurált megoldás üzembe helyezése URL-CÍMÉT jeleníti meg. A `pcs` parancs létrehoz egy fájlt is `{deployment-name}-output.json` az IoT Hub, amely lett kiépítve, például a nevét, további információkkal.

A parancssori paraméterekkel kapcsolatos további információért futtatni:

```cmd/sh
pcs -h
```

A parancssori Felülettel kapcsolatos további információkért lásd: [a CLI használata](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az előkonfigurált megoldás konfigurálása
> * Az előkonfigurált megoldás üzembe helyezéséhez
> * Jelentkezzen be az előkonfigurált megoldás

A távoli felügyeleti megoldás telepített, akkor a következő lépéssel fogja [megismerkedhet a megoldás irányítópultja képességeit](./iot-suite-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->