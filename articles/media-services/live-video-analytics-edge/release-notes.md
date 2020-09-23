---
title: Élő videó-elemzések IoT Edge kibocsátási megjegyzésekben – Azure
description: Ez a témakör az élő videó-elemzések IoT Edge kiadásokra, javításokra, hibajavításokra és ismert problémákra vonatkozó kibocsátási megjegyzéseit tartalmazza.
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 0bdf11cd99d99067dc53dde7d55fd37b96a382c9
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90882729"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Élő videó-elemzések IoT Edge kibocsátási megjegyzésekben

>Értesítést kaphat arról, hogy mikor kell újra megkeresni ezt az oldalt a frissítésekhez az URL-cím másolásával és beillesztésével: `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` az RSS-hírcsatorna-olvasóba.

Ez a cikk a következő információkat tartalmazza:

* A legújabb kiadások
* Ismert problémák
* Hibajavítások
* Elavult funkciók

<hr width=100%>

## <a name="september-22-2020"></a>Szeptember 22., 2020

Ez a kiadási címke a modul szeptember 2020 frissítéséhez a következő:

```
mcr.microsoft.com/media/live-video-analytics:1.0.4
```

> [!NOTE]
> A rövid útmutatók és oktatóanyagokban az üzembe helyezési jegyzékek 1. címkét használnak (Live-Video-Analytics: 1). Ezért az ilyen jegyzékfájlok újbóli üzembe helyezéséhez frissítenie kell a modult a peremhálózati > eszközökön.

### <a name="module-updates"></a>Modul frissítései

* Az új Graph-bővítmények csomópontja, a [MediaGraphCognitiveServicesVisionExtension](custom-vision-tutorial.md) elérhető a [térbeli elemzési modul](spatial-analysis-tutorial.md)(előzetes verzió) Cognitive Servicesból való integrálásához.
* Linux ARM64-eszközök támogatása – az ilyen eszközökön való üzembe helyezéshez [szükséges manuális lépések](deploy-iot-edge-device.md) használata.

### <a name="documentation-updates"></a>Dokumentációfrissítések

* Az Azure Stack Edge-eszközökön a IoT Edge élő videó-elemzések használatával kapcsolatos [utasítások](deploy-azure-stack-edge-how-to.md) érhetők el.
* Új oktatóanyag a forgatókönyvek adott számítógépes szemléletű modelljeinek [Custom Vision szolgáltatással](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) való fejlesztéséről, valamint az [élő videók valós idejű elemzéséhez](custom-vision-tutorial.md) .

<hr width=100%>

## <a name="august-19-2020"></a>Augusztus 19., 2020

Ez a kiadási címke a modul augusztus 2020 frissítéséhez:

```
mcr.microsoft.com/media/live-video-analytics:1.0.3
```

> [!NOTE]
> A rövid útmutatók és oktatóanyagokban az üzembe helyezési jegyzékek 1. címkét használnak (Live-Video-Analytics: 1). Ezért az ilyen jegyzékfájlok újbóli üzembe helyezéséhez frissítenie kell a modult a peremhálózati > eszközökön.

### <a name="module-updates"></a>Modul frissítései

* Mostantól nagy adatátviteli teljesítményt érhet el az élő videók elemzése IoT Edge és egyéni bővítménye között a gRPC Framework használatával. A kezdéshez tekintse meg [ezt](analyze-live-video-use-your-grpc-model-quickstart.md) a témakört.
* Az élő videó-elemzések szélesebb regionális telepítése, és csak a Cloud Service frissült.  
* Az élő videó Analytics immár 25 további régióban érhető el világszerte. Itt látható az összes elérhető régió [listája](https://azure.microsoft.com/global-infrastructure/services/?products=media-services) .  
* A gyors [üzembe helyezés beállítása](https://aka.ms/lva-edge/setup-resources-for-samples) az új régiók támogatásával is frissült.
    * Nincs olyan művelet, aki már beállított erőforrásokat

### <a name="bug-fixes"></a>Hibajavítások 

* Elavult Azure-bővítmény használatának eltávolítása a parancsfájl beállítása

<hr width=100%>

## <a name="july-13-2020"></a>Július 13., 2020

Ez a kiadási címke a modul júliusi 2020-es frissítéséhez a következő:

```
mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> A rövid útmutatók és oktatóanyagokban az üzembe helyezési jegyzékek 1. címkét használnak (Live-Video-Analytics: 1). Ezért az ilyen jegyzékfájlok újbóli üzembe helyezéséhez frissítenie kell a modult a peremhálózati > eszközökön.

### <a name="module-updates"></a>Modul frissítései

* Most már létrehozhat olyan gráf-topológiákat, amelyek rendelkeznek egy objektum-fogadó csomóponttal, valamint egy file fogadó csomópontot a Signal Gate processzor csomópontján. Példaként tekintse meg [ezt](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files) a témakört.

### <a name="bug-fixes"></a>Hibajavítások

* A kívánt tulajdonságok érvényesítésének fejlesztése

<hr width=100%>

## <a name="june-1-2020"></a>2020. június 1.

Ez a kiadás a IoT Edge Live Video Analytics első nyilvános előzetes kiadása. A kiadási címke

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>Támogatott funkciók

* Élő videó streamek elemzése az Ön által választott AI-modulok használatával, és igény szerint rögzíthet videót a peremhálózati eszközön vagy a felhőben
* A IoT Edge által [támogatott](../../iot-edge/support.md) Linux amd64 operációs rendszerek használata
* A modul üzembe helyezése és konfigurálása a IoT Hub Azure Portal vagy Visual Studio Code használatával
* [Graph-topológiák és gráf-példányok](media-graph-concept.md#media-graph-topologies-and-instances) kezelése távolról vagy helyileg a következő közvetlen metódus-hívásokkal

    *   GraphTopologyGet
    *   GraphTopologySet
    *   GraphTopologyDelete
    *   GraphTopologyList
    *   GraphInstanceGet
    *   GraphInstanceSet
    *   GraphInstanceDelete
    *   GraphInstanceList

## <a name="next-steps"></a>Következő lépések

[Áttekintés](overview.md)
