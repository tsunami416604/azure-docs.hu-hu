---
ms.openlocfilehash: db0e3bf102feeab6272ac96198d486b51e144d05
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "89570068"
---
> [!div class="mx-imgBorder"]
> :::image type="content" source="../../../media/quickstarts/overview-qs5.svg" alt-text="A jelek folyamata":::

Ez az ábra azt mutatja be, hogyan áramlik be a gyors útmutatóban szereplő jelek. Az [Edge-modulok](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) egy valós idejű Streaming Protocol-(RTSP-) kiszolgálót üzemeltető IP-kamerát szimulálnak. Az [RTSP-forrás](../../../media-graph-concept.md#rtsp-source) csomópontja lekéri a videó csatornáját a kiszolgálóról, és a képkockákat a [frame rate szűrő processzor](../../../media-graph-concept.md#frame-rate-filter-processor) -csomópontjára küldi. Ez a processzor korlátozza a [http-bővítmény processzor](../../../media-graph-concept.md#http-extension-processor) -csomópontját elérő video stream képkockasebességét. 

A HTTP-bővítmény csomópont egy proxy szerepét játssza le. A képkockákat a megadott képtípusra konvertálja. Ezt követően továbbítja a képet a REST-ben egy másik Edge-modulba, amely egy HTTP-végpont mögötti AI-modellt futtat. Ebben a példában az Edge-modul a [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) modell használatával készült, amely számos típusú objektumot képes érzékelni. A HTTP-bővítmény processzor-csomópontja összegyűjti az észlelés eredményeit, és közzéteszi az eseményeket a [IoT hub](../../../media-graph-concept.md#iot-hub-message-sink) fogadó csomópontban. A csomópont ezután elküldi ezeket az eseményeket [IoT Edge hubhoz](../../../../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

Ebben a rövid útmutatóban a következőket fogja megtekinteni:

1. A Media Graph létrehozása és üzembe helyezése.
1. Az eredmények értelmezése.
1. Az erőforrások eltávolítása.
