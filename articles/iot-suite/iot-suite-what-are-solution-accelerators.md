---
title: Az Azure IoT-megoldásgyorsítók áttekintése | Microsoft Docs
description: Az Azure IoT-megoldásgyorsítók és architektúrájuk leírása további forrásanyagokra mutató hivatkozásokkal.
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 59009f37-9ba0-4e17-a189-7ea354a858a2
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 17/01/2018
ms.author: dobett
ms.openlocfilehash: 2dc286dd228b1990e0307476d3623ffc91489f98
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="what-are-the-iot-solution-accelerators"></a>Mi az IoT-megoldásgyorsító?

Az Azure IoT-_megoldásgyorsító_ olyan megoldás, amely:

* Percek alatt üzembe helyezhető
* Segít a gyors elindulásban
* Testreszabható az igényeinek megfelelően

A megoldásgyorsítók mind azonos elvek és célok szerint lettek megtervezve.

A következő videó a távoli monitorozás megoldásgyorsítójának áttekintését nyújtja:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Meet-the-new-Remote-Monitoring-accelerator-for-Azure-IoT/Player]

## <a name="solution-accelerators-overview"></a>Megoldásgyorsítók áttekintése

A megoldásgyorsító egy általános IoT-megoldásminta nyílt forráskódú megvalósítása, amelyet az előfizetésével üzembe helyezhet az Azure-ban. Az egyes megoldásgyorsítók egyéni kód és Azure-szolgáltatások kombinációival valósítanak meg adott IoT-forgatókönyvet vagy -forgatókönyveket. Ezeket a forgatókönyveket testreszabhatja az igényeinek megfelelően. Ezek a forgatókönyvek a következőket biztosítják:

* Adatok megjelenítése egy informatív irányítópulton a részletes elemzéshez és a megoldás állapotához.
* Szabályok és riasztások konfigurálása élő IoT-eszköztelemetrián.
* Eszközfelügyeleti feladatok, például a szoftver és a konfiguráció frissítéseinek ütemezése.
* A saját egyéni fizikai vagy szimulált eszközök kiépítése.
* Az IoT-eszközcsoportokon belüli problémák hibaelhárítása és orvoslása.

Mindegyik megoldásgyorsító teljes körű megvalósítás, amelyben szimulált fizikai eszközök hoznak létre telemetriai adatokat. A megoldásgyorsítókat a következőkhöz használhatja:

* Kiindulási pont biztosítása a saját IoT-megoldásokhoz.
* Megismerheti az IoT-megoldások tervezésekor és fejlesztésekor előforduló általános mintákat.

Jelenleg három megoldásgyorsító érhető el:

* [Távoli monitorozás](iot-suite-remote-monitoring-explore.md)
* [Prediktív karbantartás](iot-suite-predictive-overview.md)
* [Csatlakoztatott gyár](iot-suite-connected-factory-overview.md)

A következő táblázatban láthatja, hogyan képezhetők le a megoldások adott IoT-funkciókra:

| Megoldás | Adatfeldolgozás | Eszközidentitás | Eszközfelügyelet | Edge feldolgozás | Parancs és vezérlés | Szabályok és műveletek | Prediktív elemzés |
| ------------------------------------------------------------ | -- | -- | -- | -- | -- | -- | -- |
| [Távoli monitorozás](iot-suite-remote-monitoring-explore.md)  |Igen |Igen |Igen |-   |Igen |Igen |-   |
| [Prediktív karbantartás](iot-suite-predictive-overview.md)   |Igen |Igen |-   |-   |Igen |Igen |Igen |
| [Csatlakoztatott gyár](iot-suite-connected-factory-overview.md) |Igen |- |- |Igen |Igen |Igen |-   |

* *Adatfeldolgozás*: A felhőbe érkező nagy mennyiségű adatforgalom.
* *Eszközidentitás*: Egyedi eszközidentitások kezelése és eszközhozzáférés biztosítása a megoldáshoz.
* *Eszközfelügyelet*: Eszköz metaadatainak kezelése és olyan műveletek elvégzése, mint az eszközök újraindítása és a belső vezérlőprogramok frissítése.
* *Parancs és vezérlés*: Üzenetküldés egy eszközre a felhőből, hogy az eszköz végrehajtson egy műveletet.
* *Szabályok és műveletek*: A megoldás háttérrendszere felhasználói szabályokkal reagál adott, eszközről a felhőbe küldött adatokra.
* *Prediktív elemzés*: A megoldás háttérrendszere az eszközről a felhőbe küldött adatok elemzésével jelzi előre bizonyos műveletek időpontját. A repülőmotor telemetriájának elemzésével például meghatározható, mikor kell karbantartást végezni a motoron.

> [!NOTE]
> Megoldásgyorsító üzembe helyezéséhez és a testreszabás részleteiről lásd: [Microsoft Azure IoT megoldásgyorsítók](https://www.azureiotsuite.com/).

## <a name="azure-services"></a>Azure-szolgáltatások

A megoldásgyorsító üzembe helyezésekor a kiépítési folyamat számos Azure-szolgáltatást konfigurál. Az alábbi táblázat ismerteti a megoldásgyorsítókban használt szolgáltatásokat:

|                      | Távoli monitorozás  | Prediktív karbantartás | Csatlakoztatott gyár |
| -------------------- | ------------------ | ---------------------- | ----------------- |
| IoT Hub              | Igen                | Igen                    | Igen               |
| Event Hubs           |                    | Igen                    |                   |
| Time Series Insights |                    |                        | Igen               |
| Tárolószolgáltatások   | Igen                |                        |                   |
| Stream Analytics     |                    | Igen                    |                   |
| Web Apps             | Igen                | Igen                    | Igen               |
| Cosmos DB            | Igen                | Igen                    |                    |
| Azure Storage         |                    | Igen                    | Igen               |

> [!NOTE]
> A távoli monitorozás megoldásgyorsítójában üzembe helyezett erőforrásokkal kapcsolatos további információért tekintse meg ezt a [cikket](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/blob/master/README.md#basic-vs-standard-deployments) a GitHubon.

* [Azure IoT Hub](../iot-hub/index.yml). Ez a szolgáltatás az eszközökről a felhőbe irányuló és a felhőből az eszközre irányuló üzenetküldési képességeket nyújt, és a felhő és a többi fontos megoldásgyorsító szolgáltatás átjárójaként működik. A szolgáltatás lehetővé teszi, hogy nagy mennyiségű üzenetet fogadjon az eszközökről, és parancsokat küldjön az eszközökre. A szolgáltatással [felügyelheti is az eszközöket](../iot-hub/iot-hub-device-management-overview.md). Konfigurálhat vagy újraindíthat például az IoT Hubhoz csatlakozó egy vagy több eszközt, illetve visszaállíthatja rajtuk a gyári beállításokat.
* [Azure Event Hubs](../event-hubs/index.md). Ez a szolgáltatás nagy mennyiségű eseményfeldolgozást biztosít a felhőben. Lásd [az Azure IoT Hub és az Azure Event Hubs összehasonlítását](../iot-hub/iot-hub-compare-event-hubs.md).
* [Azure Time Series Insights](../time-series-insights/index.yml). A megoldásgyorsítók ezt a szolgáltatást használják az eszközökről származó telemetriaadatok elemzésére és megjelenítésére.
* [Azure Container Service](../container-service/index.yml). Ez a szolgáltatás a megoldásgyorsítókban üzemelteti és felügyeli a mikroszolgáltatásokat.
* [Azure Cosmos DB](../cosmos-db/index.yml) és [Azure Storage](../storage/index.yml) adattároláshoz.
* [Azure Stream Analytics](../stream-analytics/index.yml). A prediktív karbantartás előre konfigurált megoldás ezzel a szolgáltatással dolgozza fel a bejövő telemetriát, végez összesítést és észleli az eseményeket. Ez az előre konfigurált megoldás is a Stream Analyticsszel dolgozza fel azokat a tájékoztató üzeneteket, amelyek például metaadatokat vagy eszközök parancsválaszait tartalmazzák.
* [Azure Web Apps](../app-service/index.yml) az előre konfigurált megoldások egyéni alkalmazáskódjának tárolására.

A tipikus IoT-megoldások architektúrájának áttekintéséért lásd a [Microsoft Azure-t és az eszközök internetes hálózatát (IoT)](iot-suite-what-is-azure-iot.md) ismertető cikket.

## <a name="whats-new-in-solution-accelerators"></a>A megoldásgyorsítók újdonságai

A Microsoft új mikroszolgáltatás-alapú architektúrájúra frissíti a megoldásgyorsítókat. Az alábbi táblázat ismerteti a megoldásgyorsítók jelenlegi állapotát:

| Megoldásgyorsító | Architektúra  | Nyelvek     |
| ---------------------- | ------------- | ------------- |
| Távoli monitorozás      | Mikroszolgáltatások | Java és .NET |
| Prediktív karbantartás | MVC           | .NET          |
| Csatlakoztatott gyár      | MVC           | .NET          |

Az alábbi szakaszok ismertetik a mikroszolgáltatás-alapú megoldásgyorsítók újdonságait:

### <a name="microservices"></a>Mikroszolgáltatások

A távoli monitorozás megoldásgyorsítójának új verziója egy mikroszolgáltatási architektúrát használ. Ez a megoldásgyorsító több mikroszolgáltatásból, például egy *IoT Hub-kezelőből* és egy *Storage-kezelőből* áll. Az egyes mikroszolgáltatások Java- és .NET-verziói is letölthetők a kapcsolódó fejlesztői dokumentációval együtt. További információ a mikroszolgáltatásokról: [Távoli monitorozási architektúra](iot-suite-remote-monitoring-sample-walkthrough.md).

Ez a mikroszolgáltatási architektúra egy bizonyítottan bevált minta felhőalapú megoldásokhoz, amely:

* Skálázható.
* Lehetővé teszi a bővítést.
* Könnyen áttekinthető.
* Lehetővé teszi az egyes szolgáltatások kicserélését alternatívákra.

> [!TIP]
> További információk a mikroszolgáltatás-architektúrákról: [.NET-alkalmazás architektúrája](https://www.microsoft.com/net/learn/architecture) és [Mikroszolgáltatások: egy felhőben zajló alkalmazásforradalom](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

Amikor üzembe helyezi a távoli figyelés új verzióját, az alábbi üzembe helyezési lehetőségek közül választhat:

* **Alapszintű**: Csökkentett költségű verzió bemutató célokra vagy az üzembe helyezés teszteléséhez. Mindegyik mikroszolgáltatás üzembe helyezhető egy Azure-beli virtuális gépen.
* **Standard:** Bővített infrastruktúra üzembe helyezése éles környezet fejlesztéséhez. Az Azure Container Service üzembe helyezi a mikroszolgáltatásokat több Azure-beli virtuális gépen. A Kubernetes koordinálja az egyes mikroszolgáltatásokat üzemeltető Docker-tárolókat.

### <a name="language-choices-java-and-net"></a>Nyelvválaszték: Java és .NET

Az egyes mikroszolgáltatások megvalósításai Java és .NET nyelven is elérhetők. A .NET-kódhoz hasonlóan a Java-forráskód is nyílt forráskódú, így testreszabhatja azt az adott igényeknek megfelelően:

* [Távoli monitorozás – .NET GitHub-adattár](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Távoli monitorozás – Java GitHub-adattár](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

Ha szeretné más nyelven is megtekinteni a megvalósítást, vegyen fel egy kérést az [Azure IoT felhasználói visszajelzési webhelyén](https://feedback.azure.com/forums/321918-azure-iot).

### <a name="react-user-interface-framework"></a>React felhasználói felületi keretrendszer

A felhasználói felület a [React](https://facebook.github.io/react/) javascript-könyvtár használatával lett létrehozva. A forráskódja nyílt forráskódú, így letölthető és testreszabható.

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette az IoT megoldásgyorsítókat, íme a javasolt következő lépések az egyes megoldásgyorsítókhoz:

* [Távoli monitorozási megoldás felfedezése](iot-suite-remote-monitoring-explore.md).
* [Prediktív karbantartás megoldásgyorsítójának áttekintése](iot-suite-predictive-overview.md).
* [Bevezetés a Csatlakoztatott gyár megoldásgyorsítójába](iot-suite-connected-factory-overview.md).

További információ az IoT-megoldásarchitektúrákról: [Microsoft Azure IoT-szolgáltatások: referenciaarchitektúra](http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf).
