---
ms.openlocfilehash: fa0c2f5bb00122b40fb4f4ea06b7cf55c0248904
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91025177"
---

![Áttekintés](../../../media/quickstarts/gRPC-extension.svg)

Ez az ábra azt mutatja be, hogyan áramlik be a gyors útmutatóban szereplő jelek. Az [Edge-modulok](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) egy valós idejű Streaming Protocol-(RTSP-) kiszolgálót üzemeltető IP-kamerát szimulálnak. Az [RTSP-forrás](../../../media-graph-concept.md#rtsp-source) csomópontja lekéri a videó csatornáját a kiszolgálóról, és a video-képkockákat a [mozgásérzékelő processzor](../../../media-graph-concept.md#motion-detection-processor) -csomópontjára küldi. Ez a processzor észleli a mozgást, és az észlelés után leküldi a képkockákat a [gRPC-bővítmény processzor](../../../media-graph-concept.md#grpc-extension-processor) -csomópontjára.

A gRPC-bővítmény csomópontja egy proxy szerepét játssza le. A képkockákat a megadott képtípusra konvertálja. Ezt követően továbbítja a képet a gRPC-on keresztül egy másik Edge-modulnak, amely egy gRPC-végpont mögött futó AI-modellt futtat egy [megosztott memórián](https://en.wikipedia.org/wiki/Shared_memory)keresztül. Ebben a példában az Edge-modul a [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) modell használatával készült, amely számos típusú objektumot képes érzékelni. A gRPC-bővítmény processzor-csomópontja összegyűjti az észlelés eredményeit, és közzéteszi az eseményeket a [IoT hub](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/media-graph-concept#iot-hub-message-sink) fogadó csomópontban. A csomópont ezután elküldi ezeket az eseményeket [IoT Edge hubhoz](https://docs.microsoft.com/azure/iot-edge/iot-edge-glossary#iot-edge-hub).

Ebben a rövid útmutatóban a következőket fogja megtekinteni:

1. A Media Graph létrehozása és üzembe helyezése.
1. Az eredmények értelmezése.
1. Az erőforrások eltávolítása.
