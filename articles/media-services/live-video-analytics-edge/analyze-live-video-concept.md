---
title: Élő videó elemzése rögzítés nélkül – Azure
description: A Media Graph használatával egyszerűen kinyerheti az elemzéseket egy élő videó streamből, anélkül, hogy azt fel kellene vennie a felhőben. Ez a cikk a koncepciót ismerteti.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: d59e2e9e309f0ed6a65e001557dcd9dd8af90da2
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2020
ms.locfileid: "89566742"
---
# <a name="analyzing-live-video-without-any-recording"></a>Élő videó elemzése rögzítés nélkül

## <a name="suggested-pre-reading"></a>Javasolt előzetes olvasás 

* [A Media Graph koncepciója](media-graph-concept.md)
* [Eseményalapú videófelvétel](event-based-video-recording-concept.md)

## <a name="overview"></a>Áttekintés  

A Media Graph használatával élő videókat is elemezheti anélkül, hogy a videó egyes részeit fájlba vagy objektumra rögzítse. Az alább látható adathordozó-diagramok hasonlóak az [Event-alapú videofelvételeken](event-based-video-recording-concept.md)található cikkekhez, de az eszköz fogadó csomópontja vagy a fájl fogadó csomópontja nélkül is.

### <a name="motion-detection"></a>Mozgás észlelése

Az alábbi ábrán egy [RTSP-forrás](media-graph-concept.md#rtsp-source) csomópont, egy [mozgásészlelési processzor](media-graph-concept.md#motion-detection-processor) csomópont és egy [IoT hub Message](media-graph-concept.md#iot-hub-message-sink) fogadó csomópont áll. Az ilyen adathordozó-gráf gráf-topológiájának JSON-ábrázolása [itt](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/motion-detection/topology.json)található. Ez a gráf lehetővé teszi, hogy érzékelje a mozgást a beérkező élő videó streamben, és továbbítsa a mozgási eseményeket más alkalmazásokba és szolgáltatásokhoz az IoT Hub Message fogadó csomóponton keresztül. A külső alkalmazások vagy szolgáltatások riasztást indíthatnak, vagy értesítést küldhetnek a megfelelő személyzetnek.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="Élő videó-elemzés a mozgásészlelés alapján":::

### <a name="analyzing-video-using-a-custom-vision-model"></a>Videó elemzése egyéni látási modell használatával 

Az alábbi ábra az élő videó streamek elemzését teszi lehetővé egy külön modulban csomagolt egyéni vizuális modell használatával. Az ilyen adathordozó-gráf gráf-topológiájának JSON-ábrázolása [itt](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json)található. [Itt](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) néhány példát láthat a modellek becsomagolásával IoT Edge modulokra, amelyek egy következtetési szolgáltatásként futnak.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detected-frames.svg" alt-text="Élő videó-elemzés egy külső viszonyítási modul alapján":::

Ebben az adathordozó-gráfban a frame rate Filter processzor csomópont csökkenti a beérkező élő videó adatfolyamának képkockáját, mielőtt elküldené azt egy [http-bővítmény processzor](media-graph-concept.md#http-extension-processor) -csomópontjára, amely képkockákat (JPEG, BMP vagy PNG formátumban) küld a REST-alapú külső következtetési szolgáltatásnak. A külső következtetési szolgáltatásból származó eredményeket a HTTP-bővítmény csomópont kérdezi le, és a IoT Edge hubhoz IoT Hub Message mosogató csomóponton keresztül továbbítja. Az ilyen típusú adathordozó-diagramok számos különböző forgatókönyvhöz használhatók, például a gépjárművek idősorozat-eloszlásának megismerése, a kereskedelmi tárolóban lévő fogyasztói forgalmi minta megértése és így tovább.

Ennek a példának a továbbfejlesztése egy mozgásérzékelős processzor használata a frame rate szűrő processzor-csomópontja előtt. Ez csökkenti a következtetést, mivel a rendszer csak akkor használja a szolgáltatás terhelését, ha a videóban mozgási tevékenység van használatban.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/custom-model.png" alt-text="Élő videó-elemzés az észlelt képkockák alapján, külső viszonyítási modul használatával":::

## <a name="next-steps"></a>Következő lépések

[Folyamatos videófelvétel](continuous-video-recording-concept.md)
