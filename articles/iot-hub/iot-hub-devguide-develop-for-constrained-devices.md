---
title: Azure IoT Hub fejlesztése korlátozott eszközökhöz az IoT Hub C SDK használatával
description: Fejlesztői útmutató – útmutató az Azure IoT SDK-k korlátozott eszközökhöz való használatával történő fejlesztéséhez.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: robinsh
ms.openlocfilehash: a1918a99efcdcc5764140093ad422f7887ca3c88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954695"
---
# <a name="develop-for-constrained-devices-using-azure-iot-c-sdk"></a>Fejlesztés korlátozott eszközökhöz az Azure IoT C SDK használatával

Az Azure IoT Hub C SDK ansi C (C99) nyelven íródott, ami kiválóan alkalmasa különböző platformok működtetésére kis lemez- és memória-lábnyommal. Az ajánlott RAM legalább 64 KB, de a pontos memóriaigény a használt protokolltól, a megnyitott kapcsolatok számától és a megcélzott platformtól függ.
> [!NOTE]
> * Az Azure IoT C SDK rendszeresen közzéteszi az erőforrás-felhasználási információkat a fejlesztés elősegítése érdekében.  Kérjük, látogasson el [a GitHub-adattárba,](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md) és tekintse át a legújabb referenciaértéket.
>

A C SDK az apt-get, a NuGet és az MBED csomagformájában érhető el. A korlátozott eszközök célzásához érdemes lehet az SDK-t helyileg a célplatformhoz készíteni. Ez a dokumentáció bemutatja, hogyan lehet eltávolítani bizonyos funkciókat, hogy csökken a lábnyom a C SDK segítségével [cmake](https://cmake.org/). Ezenkívül ez a dokumentáció ismerteti a korlátozott eszközökkel való munka bevált gyakorlatait.

## <a name="building-the-c-sdk-for-constrained-devices"></a>A C SDK építése a korlátozott eszközökhöz

A C SDK-t korlátozott eszközökhöz kell készítenie.

### <a name="prerequisites"></a>Előfeltételek

Kövesse ezt a [C SDK beállítási útmutatót](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) a fejlesztői környezet előkészítéséhez a C SDK felépítéséhez. Mielőtt eljut a lépés az épület cmake, akkor meghívja cmake zászlók, hogy távolítsa el a nem használt funkciókat.

### <a name="remove-additional-protocol-libraries"></a>További protokolltárak eltávolítása

A C SDK ma öt protokollt támogat: MQTT, MQTT a WebSocket en keresztül, AMQPs, AMQP websocketen keresztül és HTTPS. A legtöbb esetben egy-két, egy ügyfélen futó protokollra van szükség, ezért eltávolíthatja a nem használt protokollkönyvtárat az SDK-ból. A forgatókönyvhöz megfelelő kommunikációs protokoll kiválasztásáról további információt az [IoT Hub kommunikációs protokoll kiválasztása](iot-hub-devguide-protocols.md)című részben talál. Például az MQTT egy könnyű protokoll, amely gyakran jobban megfelel a korlátozott eszközök.

Az AMQP- és HTTP-kódtárak a következő cmake paranccsal távolíthatók el:

```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

### <a name="remove-sdk-logging-capability"></a>Az SDK naplózási képességének eltávolítása

A C SDK széles körű naplózást biztosít a hibakeresés érdekében. Az éles eszközök naplózási képességét a következő cmake paranccsal távolíthatja el:

```
cmake -Dno_logging=OFF <Path_to_cmake>
```

### <a name="remove-upload-to-blob-capability"></a>Feltöltés a blobba funkció eltávolítása

Nagy fájlokat tölthet fel az Azure Storage-ba az SDK beépített funkcióval. Az Azure IoT Hub egy társított Azure Storage-fiók diszpécserjeként működik. Ezzel a funkcióval médiafájlokat, nagy telemetriai kötegeket és naplókat küldhet. Az IoT Hub segítségével a [fájlok feltöltésével](iot-hub-devguide-file-upload.md)kapcsolatban további információkat kaphat. Ha az alkalmazás nem igényli ezt a funkciót, eltávolíthatja ezt a funkciót a következő cmake paranccsal:

```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```

### <a name="running-strip-on-linux-environment"></a>Sáv futtatása Linux környezetben

Ha a bináris fájlok Linux rendszeren futnak, a [csíkparancs](https://en.wikipedia.org/wiki/Strip_(Unix)) kihasználásával csökkentheti a végső alkalmazás méretét az összeállítás után.

```
strip -s <Path_to_executable>
```

## <a name="programming-models-for-constrained-devices"></a>Programozási modellek korlátozott eszközökhöz

Ezután tekintse meg a korlátozott eszközök programozási modelljeit.

### <a name="avoid-using-the-serializer"></a>Ne használja a Szerializáló

A C SDK rendelkezik egy opcionális [C SDK szerializáló,](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer)amely lehetővé teszi, hogy declarative leképezési táblák módszerek és az eszköz iker tulajdonságai. A szerializáló célja, hogy egyszerűsítse a fejlesztést, de növeli a terhelést, ami nem optimális a korlátozott eszközök. Ebben az esetben fontolja meg a primitív ügyfél API-k és a JSON elemzés egy könnyű elemző, például [a parson](https://github.com/kgabis/parson)használatával.

### <a name="use-the-lower-layer-_ll_"></a>Használja az alsó réteget (_LL_)

A C SDK két programozási modellt támogat. Az egyik készlet rendelkezik API-kkal, _ll_ infix-el, amely az alsó réteget jelenti. Az API-k ezen készlete könnyebb, és nem pörgeti fel a munkaszálakat, ami azt jelenti, hogy a felhasználónak manuálisan kell szabályoznia az ütemezést. Az eszközügyfél esetében például az _LL_ API-k ebben a [fejlécfájlban](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h)találhatók. 

Az _LL-index_ nélküli API-k egy másik készletét a kényelmi rétegnek nevezzük, ahol a munkaszál automatikusan megpördül. Az eszközügyfél kényelmi rétegAPI-i például ebben az [IoT Eszközügyfél-fejlécfájlban](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h)találhatók. Olyan korlátozott eszközök esetén, ahol minden további szál a rendszererőforrások jelentős százalékát veheti igénybe, fontolja meg _az LL_ API-k használatát.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Azure IoT C SDK architektúráról:
-   [Azure IoT C SDK forráskód](https://github.com/Azure/azure-iot-sdk-c/)
-   [Az Azure IoT-eszköz SDK C-bemutatkozása](iot-hub-device-sdk-c-intro.md)
