---
title: Élő videó-elemzések IoT Edge kibocsátási megjegyzésekben – Azure
description: Ez a témakör az élő videó-elemzések IoT Edge kiadásokra, javításokra, hibajavításokra és ismert problémákra vonatkozó kibocsátási megjegyzéseit tartalmazza.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 28260728532d9db52b8d36488c2e456bd11803ea
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091779"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Élő videó-elemzések IoT Edge kibocsátási megjegyzésekben

>Értesítést kaphat arról, hogy mikor kell újra megkeresni ezt az oldalt a frissítésekhez az URL-cím másolásával és beillesztésével: `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` az RSS-hírcsatorna-olvasóba.

Ez a cikk a következő információkat tartalmazza:

* A legújabb kiadások
* Ismert problémák
* Hibajavítások
* Elavult funkciók

## <a name="july-13-2020"></a>Július 13., 2020

Ez a kiadási címke a modul júliusi 2020-es frissítéséhez a következő:

```
     mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> A rövid útmutatók és oktatóanyagokban az üzembe helyezési jegyzékek 1. címkét használnak (Live-Video-Analytics: 1). Ezért az ilyen jegyzékfájlok újbóli üzembe helyezéséhez frissítenie kell a modult a peremhálózati > eszközökön.

### <a name="new-features"></a>Új funkciók
* Most már létrehozhat olyan gráf-topológiákat, amelyek rendelkeznek egy objektum-fogadó csomóponttal, valamint egy file fogadó csomópontot a Signal Gate processzor csomópontján. Példaként tekintse meg [ezt](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files) a témakört.

### <a name="bug-fixes"></a>Hibajavítások
* A kívánt tulajdonságok érvényesítésének fejlesztése

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


## <a name="next-steps"></a>További lépések

[Áttekintés](overview.md)
