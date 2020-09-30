---
title: Bevezetés a IoT megoldás-gyorsítók használatába – Azure | Microsoft Docs
description: Megismerheti az Azure IoT-megoldásgyorsítókat. Az IoT-megoldásgyorsítók teljes körű, átfogó, üzembe helyezésre kész IoT-megoldások.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: timlt
ms.openlocfilehash: a8314fed43c8282c536fc6e95e6c3aa877408b09
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91542495"
---
# <a name="what-are-azure-iot-solution-accelerators"></a>Mik az Azure IoT-megoldásgyorsítók?

A felhőalapú IoT-megoldás általában egyéni kódot és felhőalapú szolgáltatásokat használ az eszközök kapcsolatának kezeléséhez, az adatfeldolgozáshoz és az elemzéshez, valamint a megjelenítéshez.

Az IoT-megoldásgyorsítók teljes körű, üzembe helyezésre kész IoT-megoldások, amelyek gyakori IoT-forgatókönyveket implementálnak. A forgatókönyvek közé tartozik a távoli monitorozás, a csatlakoztatott gyár, a prediktív karbantartás és az eszközszimuláció. A megoldásgyorsítók üzembe helyezésekor az üzemelő példány magában foglalja az összes szükséges felhőalapú szolgáltatást és minden szükséges alkalmazáskódot.

A megoldásgyorsítók az Ön saját IoT-megoldásainak kiindulópontjaiként szolgálnak. Az összes megoldásgyorsító forráskódja nyílt, és elérhető a GitHubban. A megoldásgyorsítókat letöltheti és saját igényei szerint testre szabhatja.

A megoldásgyorsítókat tanulási eszközként is használhatja, mielőtt létrehozná saját egyéni IoT-megoldását. A megoldásgyorsítók bevált gyakorlatokat alkalmaznak a felhőalapú IoT-megoldásokhoz, amelyeket követhet.

Az összes megoldásgyorsító alkalmazáskódja tartalmaz egy olyan webalkalmazást, amely lehetővé teszi az alkalmazásgyorsító kezelését.

## <a name="supported-iot-scenarios"></a>Támogatott IoT-forgatókönyvek

Jelenleg négy megoldásgyorsítót helyezhet üzembe:

### <a name="remote-monitoring"></a>Távoli monitorozás

A [távoli figyelési megoldás-gyorsító](iot-accelerators-remote-monitoring-sample-walkthrough.md) segítségével telemetria gyűjthet a távoli eszközökről, és szabályozhatja azokat. A példaeszközök közé tartoznak az ügyfelei telephelyein felszerelt hűtőrendszerek vagy a távoli szivattyútelepeken üzembe helyezett szelepek.

A távoli monitorozási irányítópultot használhatja a csatlakoztatott eszközök telemetriájának megtekintéséhez, új eszközök létrehozásához vagy a csatlakoztatott eszközök belső vezérlőprogramjának frissítéséhez is:

[![Távoli figyelési megoldás irányítópultja](./media/about-iot-accelerators/rm-dashboard-inline.png)](./media/about-iot-accelerators/rm-dashboard-expanded.png#lightbox)

### <a name="connected-factory"></a>Csatlakoztatott gyár

A [Connected Factory megoldás-gyorsító](iot-accelerators-connected-factory-features.md) segítségével telemetria gyűjthet az olyan ipari eszközökről, amelyek egy [OPC egységes architektúra](https://opcfoundation.org/about/opc-technologies/opc-ua/) -felülettel rendelkeznek, és szabályozzák azokat. Az ipari objektumok lehetnek például a gyártósoron található összeszerelő- és tesztelőállomások.

A csatlakoztatott gyár irányítópultjának használatával a következő ipari eszközöket monitorozhatja és kezelheti:

:::image type="content" source="./media/about-iot-accelerators/cf-dashboard-inline.png" alt-text="A csatlakoztatott gyári megoldás irányítópultját bemutató képernyőkép." lightbox="./media/about-iot-accelerators/cf-dashboard-expanded.png":::

### <a name="predictive-maintenance"></a>Prediktív karbantartás

A [prediktív karbantartási megoldás gyorsítása](iot-accelerators-predictive-walkthrough.md) segítségével előre jelezheti, ha egy távoli eszköz meghibásodása várható, hogy az eszköz meghibásodása előtt el tudja végezni a karbantartást. Ez a megoldásgyorsító gépi tanulási algoritmusokkal vizsgálja az eszköz telemetriai adatait, és előrejelzi a meghibásodást. A példaeszközök lehetnek például repülőgép-hajtóművek vagy liftek.

A prediktív karbantartási irányítópult a következő prediktív karbantartási elemzések megtekintésére használható:

:::image type="content" source="./media/about-iot-accelerators/pm-dashboard-inline.png" alt-text="A csatlakoztatott gyári megoldás irányítópultját bemutató képernyőkép." lightbox="./media/about-iot-accelerators/pm-dashboard-expanded.png":::

### <a name="device-simulation"></a>Eszközszimuláció

Az [eszköz-szimulációs megoldás gyorsítása](iot-accelerators-device-simulation-overview.md) használatával reális telemetria létrehozó szimulált eszközöket futtathat. Ez a megoldásgyorsító más megoldásgyorsítók viselkedésének vagy saját IoT-megoldásainak tesztelésére is használható.

Az eszközszimulációs webalkalmazás a következő szimulációk konfigurálására és futtatására használható:

[![Csatlakoztatott gyári megoldás irányítópultja](./media/about-iot-accelerators/ds-dashboard-inline.png)](./media/about-iot-accelerators/ds-dashboard-expanded.png#lightbox)

## <a name="design-principles"></a>Tervezési alapelvek

Minden megoldásgyorsító ugyanazon tervezési elvek és célok figyelembevételével készült. Tervezésük során fontos volt:

* A **méretezhetőség**, így több millió csatlakoztatott eszközt csatlakoztathat és felügyelhet.
* A **bővíthetőség**, hogy saját elvárásainak megfelelően testre szabhassa megoldásgyorsítóit.
* Az **érthetőség**, hogy könnyedén megérthesse a működésüket és a megvalósításukat.
* A **modularitás**, hogy lecserélhessen egyes alkalmazásokat.
* A **biztonság**, amely az Azure biztonsági eszközeit a beépített csatlakozási és eszközbiztonsági funkciókkal kombinálja.

## <a name="architectures-and-languages"></a>Architektúra és nyelvek

Az eredeti megoldásgyorsítók a .NET és model-view-controller (MVC) architektúra használatával készültek. A Microsoft új mikroszolgáltatási architektúra használatára frissíti a megoldásgyorsítókat. Az alábbi táblázat ismerteti a megoldásgyorsítók jelenlegi állapotát, illetve tartalmazza a megfelelő GitHub-adattárakra mutató hivatkozásokat:

| Megoldásgyorsító   | Architektúra  | Nyelvek     |
| ---------------------- | ------------- | ------------- |
| Távoli monitorozás      | Mikroszolgáltatások | [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java) és [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) |
| Prediktív karbantartás | MVC           | [.NET](https://github.com/Azure/azure-iot-predictive-maintenance)          |
| Csatlakoztatott gyár      | MVC           | [.NET](https://github.com/Azure/azure-iot-connected-factory)          |
| Eszközszimuláció      | Mikroszolgáltatások | [.NET](https://github.com/Azure/device-simulation-dotnet)          |

A Services architektúrával kapcsolatos további információkért lásd: [Bevezetés az Azure IoT Reference Architecture](https://docs.microsoft.com/azure/architecture/reference-architectures/iot/).

## <a name="deployment-options"></a>Üzembe helyezési lehetőségek

A megoldásgyorsítókat a [Microsoft Azure IoT-megoldásgyorsítók](https://www.azureiotsolutions.com/Accelerators#) webhelyéről vagy a parancssorból helyezheti üzembe.

A távoli monitorozási megoldásgyorsítót a következő konfigurációkban helyezheti üzembe:

* **Standard:** Bővített infrastruktúra üzembe helyezése éles környezet fejlesztéséhez. A Azure Container Service üzembe helyezi a szolgáltatásait több Azure-beli virtuális gépen. A Kubernetes koordinálja az egyes mikroszolgáltatásokat üzemeltető Docker-tárolókat.
* **Alapszintű**: Csökkentett költségű verzió bemutató célokra vagy az üzembe helyezés teszteléséhez. Mindegyik mikroszolgáltatás üzembe helyezhető egy Azure-beli virtuális gépen.
* **Helyi:** Helyi gépen történő üzembe helyezés tesztelés és fejlesztés céljából. Ez a módszer egy helyi Docker-tárolóban helyezi üzembe a mikroszolgáltatásokat, és csatlakozik az IoT Hub, Azure Cosmos DB és Azure Storage szolgáltatásokhoz a felhőben.

A megoldás-gyorsító futtatásának díja a [mögöttes Azure-szolgáltatások futtatásának összesített díja](https://azure.microsoft.com/pricing). Az igénybe vett Azure-szolgáltatások részleteit az üzembehelyezési beállítások kiválasztásakor tekintheti meg.

## <a name="next-steps"></a>További lépések

Az IoT-megoldásgyorsítók valamelyikének kipróbálásához tekintse meg a következő rövid útmutatókat:

* [Távoli monitorozási megoldás kipróbálása](quickstart-remote-monitoring-deploy.md)
* [Csatlakoztatottgyár-megoldás kipróbálása](quickstart-connected-factory-deploy.md)
* [Prediktív karbantartási megoldás kipróbálása](quickstart-predictive-maintenance-deploy.md)
* [Eszközszimulációs megoldás kipróbálása](quickstart-device-simulation-deploy.md)
