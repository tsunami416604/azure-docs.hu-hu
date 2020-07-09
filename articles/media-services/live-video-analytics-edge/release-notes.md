---
title: Élő videó-elemzések IoT Edge kibocsátási megjegyzésekben – Azure
description: Ez a témakör az élő videó-elemzések IoT Edge kiadásokra, javításokra, hibajavításokra és ismert problémákra vonatkozó kibocsátási megjegyzéseit tartalmazza.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: fd607ee027b46cf386180338440e6530da911302
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84261036"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Élő videó-elemzések IoT Edge kibocsátási megjegyzésekben

>Értesítést kaphat arról, hogy mikor kell újra megkeresni ezt az oldalt a frissítésekhez az URL-cím másolásával és beillesztésével: `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` az RSS-hírcsatorna-olvasóba.

Ez a cikk a következő információkat tartalmazza:

* A legújabb kiadások
* Ismert problémák
* Hibajavítások
* Elavult funkciók

## <a name="june-1-2020"></a>2020. június 1.

Ez a kiadás a IoT Edge Live Video Analytics első nyilvános előzetes kiadása. A kiadási címke

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>Támogatott funkciók
* Élő videó streamek elemzése az Ön által választott AI-modulok használatával, és igény szerint rögzíthet videót a peremhálózati eszközön vagy a felhőben
* A IoT Edge által [támogatott](https://docs.microsoft.com/azure/iot-edge/support) Linux amd64 operációs rendszerek használata
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
