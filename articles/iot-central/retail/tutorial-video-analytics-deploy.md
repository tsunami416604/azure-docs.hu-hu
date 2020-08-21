---
title: A video Analytics – Object és Motion Detection Azure IoT Central alkalmazás sablonjának üzembe helyezése
description: Ez az útmutató összefoglalja, hogyan helyezhet üzembe egy Azure IoT Central-alkalmazást a video Analytics-Object és a Motion Detection alkalmazás sablonjának használatával.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: how-to
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: d661df57e4409c1d7fe196c7f136965191421bd4
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719711"
---
# <a name="how-to-deploy-an-iot-central-application-using-the-video-analytics---object-and-motion-detection-application-template"></a>IoT Central alkalmazás üzembe helyezése a video Analytics-Object és a Motion Detection alkalmazás sablonjának használatával

A Key *video Analytics – Object és Motion Detection alkalmazás-* összetevők áttekintését lásd: [Object and Motion Detection video Analytics Application Architecture](architecture-video-analytics.md).

## <a name="deploy-the-application"></a>Az alkalmazás üzembe helyezése

Az alábbi lépések végrehajtásával telepítsen egy IoT Central alkalmazást a video Analytics alkalmazás sablonjának használatával:

1. Végezze el a [video Analytics-alkalmazás létrehozása az Azure-ban IoT Central](tutorial-video-analytics-create-app.md) oktatóanyagot a következőhöz:
    - Azure Media Services-fiók létrehozása
    - Hozza létre a IoT Central alkalmazást a video Analytics-Object és a Motion Detection alkalmazás sablonból.
    - Átjáró-eszköz konfigurálása a IoT Central alkalmazásban. Az átjáró lehetővé teszi, hogy a kamera-eszközök csatlakozzanak az alkalmazáshoz.

1. Végezze el az [IoT Edge-példány létrehozása a video analyticshez (linuxos virtuális gép)](tutorial-video-analytics-iot-edge-vm.md) oktatóanyagot a következőhöz:
    - Hozzon létre egy Azure-beli virtuális gépet a telepített Azure IoT Edge futtatókörnyezettel.-Készítse elő a IoT Edge telepítést a video Analytics modul üzemeltetéséhez.
    - A IoT Edge eszköz csatlakoztatása a IoT Central alkalmazáshoz.

1. Töltse ki a [figyelőt, és kezelje a video Analytics-alkalmazás](tutorial-video-analytics-manage.md) oktatóanyagát a következőre:
    - Vegyen fel objektumokat és mozgásészlelési kamerákat az IoT Central-alkalmazásban található átjáróba.
    - Indítsa el a kamera feldolgozását.
    - A rögzített videók AMS-ben való megtekintéséhez telepítsen egy helyi médialejátszó alkalmazást.
    - Az észlelt objektumokat megjelenítő rögzített videó megtekintése.
    - Rendben.

## <a name="next-steps"></a>További lépések

Most áttekintheti a video Analytics alkalmazás-sablon üzembe helyezésének és használatának lépéseit. a kezdéshez tekintse meg a [video Analytics-alkalmazás létrehozása az Azure-ban IoT Central](tutorial-video-analytics-create-app.md) .
