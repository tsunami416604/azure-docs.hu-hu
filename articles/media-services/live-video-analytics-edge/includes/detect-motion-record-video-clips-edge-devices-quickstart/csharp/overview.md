---
ms.openlocfilehash: 768e79c6a2471715b336f90748ad97ecfcc4bbc2
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682139"
---

![Áttekintés](../../../media/quickstarts/overview-qs4.png)

Az előző ábrán látható, hogyan áramlik a jelek a rövid útmutatóban. [Az Edge-modulok](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) egy valós idejű Streaming Protocol-(RTSP-) kiszolgálót futtató IP-kamerát szimulálnak. Az [RTSP-forrás](../../../media-graph-concept.md#rtsp-source) csomópontja lekéri a videó csatornáját a kiszolgálóról, és a video-képkockákat a [mozgásérzékelő processzor](../../../media-graph-concept.md#motion-detection-processor) -csomópontjára küldi. Az RTSP-forrás ugyanazt a képkockákat küldi el egy [Signal Gate processzor](../../../media-graph-concept.md#signal-gate-processor) -csomópontnak, amely mindaddig be van zárva, amíg egy esemény nem indít el.

Ha a mozgásérzékelő processzora észleli a videót, egy eseményt küld a Signal Gate processzor-csomópontba, és elindítja azt. A kapu a beállított időtartamra nyílik meg, és képkockákat küld a [file mosogató](../../../media-graph-concept.md#file-sink) csomópontba. Ez a fogadó csomópont MP4-fájlként rögzíti a videót a peremhálózati eszköz helyi fájlrendszerén. A fájlt a rendszer a konfigurált helyre menti.

Ebben a rövid útmutatóban a következőket fogja megtekinteni:

1. A Media Graph létrehozása és üzembe helyezése.
1. Az eredmények értelmezése.
1. Az erőforrások eltávolítása.
