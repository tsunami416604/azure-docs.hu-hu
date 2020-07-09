---
title: Mi az élő videó-elemzés a IoT Edgeon – Azure
description: Ez a témakör áttekintést nyújt a IoT Edge élő videók elemzéséről. A platform a IoT-megoldások fejlesztéséhez használható képességet kínálja. Például rögzítheti, rögzítheti és elemezheti az élő videót, és közzéteheti az eredményeket (videó és/vagy videó-elemzés) az Azure-szolgáltatásokban.
ms.topic: overview
ms.date: 05/27/2020
ms.openlocfilehash: 81a67322d0a5e524d75b9bf3e481be7157e09e63
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266797"
---
# <a name="what-is-live-video-analytics-on-iot-edge-preview"></a>Mi a IoT Edge Live Video Analytics? (előzetes verzió)

A IoT Edge élő videó-elemzések platformot biztosítanak a peremhálózat és a felhőre kiterjedő intelligens video-alkalmazások létrehozásához. A platform lehetővé teszi az élő videók rögzítését, rögzítését és elemzését, valamint az eredmények (videó és/vagy videó-elemzés) közzétételét az Azure-szolgáltatásokban (a felhőben és/vagy az Edge-ben). A platform segítségével javíthatja a IoT-megoldásokat a video Analytics használatával. A IoT Edge funkcióban élő videó-elemzések kombinálhatók más Azure IoT Edge-modulokkal, Stream Analytics például a IoT Edge, Cognitive Services a IoT Edge, valamint a felhőben található Azure-szolgáltatásokkal, például az Media Services, az Event hub, Cognitive Services stb. hatékony hibrid (például Edge + Cloud) alkalmazások létrehozásához.

A IoT Edge Live Video Analytics bővíthető platformként lett kialakítva, amely lehetővé teszi a különböző videó-elemzési peremhálózati modulok (például a kognitív Services-tárolók, az Ön által létrehozott egyéni Edge-modulok vagy a saját adataival rendelkező egyéni modellek) összekapcsolását, és az élő videók elemzésének bonyolultságát anélkül, hogy az éles videó folyamatának összetettségét kellene használnia.

## <a name="accelerate-iot-solutions-development"></a>Gyorsítsa fel a IoT-megoldások fejlesztését 

A IoT más IoT érzékelőkből és/vagy üzleti adatokból származó jelekkel kombináló megoldásai segítségével automatizálhatja vagy félig automatizálhatja üzleti döntéseit, ami a termelékenység fejlesztését eredményezi. A IoT Edge élő videó-elemzés lehetővé teszi az ilyen megoldások gyorsabb kiépítését. A videó elemzési moduljainak és a vállalatra jellemző logikájának kiépítésére koncentrálhat, és lehetővé teszi, hogy a platform elrejtse a videós folyamat kezelésének és futtatásának bonyolultságát.

A IoT Edge élő videó-elemzési szolgáltatásával továbbra is használhatja a [CCTV kamerákat](https://en.wikipedia.org/wiki/Closed-circuit_television_camera) a meglévő [videó-felügyeleti rendszerekkel (VM)](https://en.wikipedia.org/wiki/Video_management_system) , és a video Analytics-alkalmazásokat egymástól függetlenül is létrehozhatja. A IoT Edge élő videó-elemzéseket a Computer látási SDK-k és az eszközkészletek együttes használatával is felhasználhatják az élvonalbeli IoT-megoldások létrehozásához. Az alábbi ábra ezt mutatja be.

![IoT-megoldások fejlesztése élő videó-elemzéssel IoT Edge](./media/overview/product-diagram.svg)

## <a name="supported-environments"></a>Támogatott környezetek

A Linux AMD64 és x64 környezetek támogatottak.

## <a name="get-started"></a>Bevezetés

Olvassa el a következő fogalmakat, majd próbáljon ki egy rövid útmutatót a mozgásérzékelők élő videó-hírcsatornán való futtatásához.

### <a name="concepts"></a>Alapelvek

* [Adathordozó-gráf](media-graph-concept.md)
* [Videofelvétel](video-recording-concept.md)
* [Videolejátszás](video-playback-concept.md)
* [Folyamatos videofelvétel](continuous-video-recording-concept.md)
* [Event-alapú videó rögzítése](event-based-video-recording-concept.md)
* [Videó-rögzítés nélküli élő videó-elemzés](analyze-live-video-concept.md)

## <a name="next-steps"></a>További lépések

* Kövesse a gyors útmutató [: élő videó elemzés futtatása saját modell](use-your-model-quickstart.md) használatával című cikket, amelyből megtudhatja, hogyan futtathatja a mozgásérzékelőt élő videó-hírcsatornán.
* [Terminológia](terminology.md) áttekintése
* Fedezze fel az [élő videó Analytics nyílt forráskódú anyagát](https://github.com/Azure/live-video-analytics)

