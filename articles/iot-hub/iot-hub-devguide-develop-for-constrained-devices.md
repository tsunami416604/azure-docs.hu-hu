---
title: Az Azure IoT-központ korlátozott eszközök fejlesztése |} Microsoft Docs
description: Fejlesztői útmutató - hogyan fejleszthet Azure IoT SDK-k használatával korlátozott eszközök útmutatást.
services: iot-hub
documentationcenter: c
author: yzhong94
manager: timlt
editor: ''
ms.assetid: 979136db-c92d-4288-870c-f305e8777bdd
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/24/2018
ms.author: yizhon
ms.openlocfilehash: 62065b78e3f8191c6423ba9dba4a8f7d16fad114
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655145"
---
# <a name="develop-for-constrained-devices-using-azure-iot-sdks"></a>Az Azure IoT SDK-k használatával korlátozott eszközök fejlesztése

## <a name="develop-using-azure-iot-hub-c-sdk"></a>Fejlesztés az Azure IoT Hub C SDK használatával
Az Azure IoT Hub C SDK ANSI C (C99), így a különböző platformokról kis lemez- és memória tárhely működnek jól alkalmazható nyelven van megírva.  A javasolt RAM legalább 64 KB-os, de a használt protokoll, megnyitott kapcsolatok számát, valamint a megcélzott platform függ a pontos memóriaigényt.

C SDK apt get NuGet és MBED csomag formában érhető el.  Korlátozott eszközök céloz, érdemes lehet hozhat létre az SDK helyben a célként megadott platformhoz. Ebben a dokumentációban bemutatja, hogyan kell bizonyos szolgáltatások zsugorítani csökkentése a C SDK használatával eltávolítása [cmake][lnk-cmake].  Emellett ebben a dokumentációban ismertetett programozási modellel korlátozott eszközök használata ajánlott.

### <a name="building-the-c-sdk-for-constrained-devices"></a>A C SDK korlátozott eszközök felépítése
#### <a name="prerequisites"></a>Előfeltételek
Folytassa a [beállítási útmutatója] [ lnk-devbox-setup] a fejlesztési környezet létrehozása a C SDK előkészítése.  A lépés az épület a cmake kap, mielőtt hívhat meg cmake jelzők a nem használt szolgáltatások eltávolítása.

#### <a name="remove-additional-protocol-libraries"></a>Távolítsa el a további protokoll függvénytárak
C SDK ma öt protokollt támogat: MQTT, MQTT WebSocket, AMQPs, WebSocket, és HTTPS protokollt használó AMQP keresztül.    Legtöbb esetben egy-két protokollok, az ügyfélen futó szükséges, ezért az SDK-ból eltávolíthatja a protokoll-könyvtár nem használ.  További információt a megfelelő kommunikációs protokollt válassza a helyzetnek találhatók ezen [dokumentum][lnk-choosing-protocol].  Például MQTT egyszerűsített protokoll, amely korlátozott eszközök gyakran jobban megfelelő.

A következő paranccsal cmake AMQP és HTTP-alapú tárak eltávolítása:
```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

#### <a name="remove-sdk-logging-capability"></a>Távolítsa el az SDK naplózási képesség
A C SDK biztosít a részletes naplózás egész a hibakeresés érdekében. A naplózási képesség a következő paranccsal cmake éles eszközök eltávolítása:
```
cmake -Dno_logging=OFF <Path_to_cmake>
```

#### <a name="remove-upload-to-blob-capability"></a>Feltöltése a blob-funkció eltávolítása
Nagy méretű fájlt tölthet Azure Storage a beépített képesség az SDK használatával.  Az Azure IoT Hub úgy működik, mint egy kézbesítő társított Azure Storage-fiók számára.  E szolgáltatás használatával médiafájlok, nagy telemetriai kötegek és naplókat.  Ismerje meg, további információk az IoT hubbal fájlok feltöltése [dokumentum][lnk-hub-file-upload].  Ha az alkalmazás nem igényli ezt a funkciót, eltávolíthatja ezt a funkciót a következő cmake parancs használatával:
```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```
#### <a name="running-strip-on-linux-environment"></a>Linux-környezetben futó sáv
Ha a bináris fájlok Linux rendszeren futtatja, használhatja a [parancs sáv] [ lnk-strip] fordítása után utolsó kérelem méretének csökkentése érdekében.
```
strip -s <Path_to_executable>
```

### <a name="programming-models-for-constrained-devices"></a>Programozási modellek korlátozott eszközökhöz
#### <a name="avoid-using-the-serializer"></a>Kerülje a szerializáló
A C SDK tartalmazza egy nem kötelező [szerializáló][lnk-serializer], amely lehetővé teszi, hogy deklaratív leképezési táblázatok használatával és a két eszköztulajdonságok határozhatja meg.  A szerializáló célja, egyszerűbbé teheti a fejlesztési, de azt többletterheléssel, amely nincs optimális korlátozott eszközökhöz.  Ebben az esetben fontolja meg a primitív ügyfél API-k használatát, és json elemezni egy egyszerűsített elemző használatával [parson][lnk-parson].

#### <a name="use-the-lower-layer-ll"></a>Használja az alsóbb rétegben (_inden_)
A C SDK két programozási modellel támogatja.  Egy készlet van az API-k egy _inden_ infix, amely alacsonyabb réteg jelenti.  Az API-készlet világosabb súly, és nem lépett fel munkaszál, ami azt jelenti, hogy a felhasználó manuálisan kell szabályozása ütemezés.  Például az eszközügyfél a _inden_ ezen található API-k [fejlécfájlt](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h).  Egy másik készlet API-k nélkül a _inden_ index neve a kényelem réteg esetében, ha egy munkavégző szál automatikusan hoz-e.  Például a kényelem réteg API-k a lévő ügyfél ezen található [fejlécfájlt](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h).  Ha minden további szál is igénybe vehet az erőforrásokat, jelentős százalékos korlátozott eszközök inkább használja az _inden_ API-k.

## <a name="next-steps"></a>További lépések
További információt Azure IoT C SDK architektúrája:
-   [Az Azure IoT C SDK forráskódja](https://github.com/Azure/azure-iot-sdk-c/)
-   [Az Azure IoT-eszközök SDK C bevezetéséhez](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-sdk-c-intro)

------
[lnk-cmake]: https://cmake.org/
[lnk-devbox-setup]:  https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md
[lnk-choosing-protocol]: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols
[lnk-hub-file-upload]: https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-file-upload
[lnk-strip]: https://en.wikipedia.org/wiki/Strip_(Unix)
[lnk-serializer]: https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer
[lnk-parson]: https://github.com/kgabis/parson