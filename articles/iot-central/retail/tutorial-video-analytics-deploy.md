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
ms.openlocfilehash: decfa7020be7778e8ca64a9fb0cb4aac1657da27
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91873336"
---
# <a name="how-to-deploy-an-iot-central-application-using-the-video-analytics---object-and-motion-detection-application-template"></a>IoT Central alkalmazás üzembe helyezése a video Analytics-Object és a Motion Detection alkalmazás sablonjának használatával

A Key *video Analytics – Object és Motion Detection alkalmazás-* összetevők áttekintését lásd: [Object and Motion Detection video Analytics Application Architecture](architecture-video-analytics.md).

A következő videó bemutatja, hogyan használható a _video Analytics-Object és a Motion Detection alkalmazás sablonja_ egy IoT Central-megoldás üzembe helyezéséhez:

> [!VIDEO https://www.youtube.com/embed/Bo3FziU9bSA]

## <a name="deploy-the-application"></a>Az alkalmazás üzembe helyezése

Az alábbi lépések végrehajtásával telepítsen egy IoT Central alkalmazást a video Analytics alkalmazás sablonjának használatával:

1. Hajtsa végre a [video Analytics-alkalmazás létrehozása az Azure-ban IoT Central (YOLO v3)](tutorial-video-analytics-create-app-yolo-v3.md) vagy a [videó létrehozása az azure-ban IoT Central (OpenVINO &trade; )](tutorial-video-analytics-create-app-openvino.md) oktatóanyagot a következőkhöz:
    - Azure Media Services-fiók létrehozása
    - Hozza létre a IoT Central alkalmazást a video Analytics-Object és a Motion Detection alkalmazás sablonból.
    - Átjáró-eszköz konfigurálása a IoT Central alkalmazásban. Az átjáró lehetővé teszi, hogy a kamera-eszközök csatlakozzanak az alkalmazáshoz.

1. Hajtsa végre a következőt: [IoT Edge-példány létrehozása videó-elemzéshez (linuxos virtuális gép)](tutorial-video-analytics-iot-edge-vm.md) vagy az [oktatóanyag: IoT Edge-példány létrehozása a video Analytics (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md) oktatóanyaghoz a következőkhöz:
    - Hozzon létre egy Azure-beli virtuális gépet a telepített Azure IoT Edge futtatókörnyezettel.-Készítse elő a IoT Edge telepítést a video Analytics modul üzemeltetéséhez.
    - A IoT Edge eszköz csatlakoztatása a IoT Central alkalmazáshoz.

1. Töltse ki a [figyelőt, és kezelje a video Analytics-alkalmazás](tutorial-video-analytics-manage.md) oktatóanyagát a következőre:
    - Vegyen fel objektumokat és mozgásészlelési kamerákat az IoT Central-alkalmazásban található átjáróba.
    - Indítsa el a kamera feldolgozását.
    - A rögzített videók AMS-ben való megtekintéséhez telepítsen egy helyi médialejátszó alkalmazást.
    - Az észlelt objektumokat megjelenítő rögzített videó megtekintése.
    - Rendben.

## <a name="next-steps"></a>Következő lépések

Most áttekintheti a video Analytics alkalmazás-sablon üzembe helyezésének és használatának lépéseit. lásd: [video Analytics-alkalmazás létrehozása az azure IoT Centralban (YOLO v3)](tutorial-video-analytics-create-app-yolo-v3.md) , vagy [hozzon létre egy videó-elemzést az Azure IoT Central (OpenVINO &trade; )](tutorial-video-analytics-create-app-openvino.md) a kezdéshez.
