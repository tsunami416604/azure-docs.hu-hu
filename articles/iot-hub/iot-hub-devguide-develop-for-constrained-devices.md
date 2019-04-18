---
title: Az Azure IoT Hub fejlesztése korlátozott eszközök IoT Hub C SDK-val |} A Microsoft Docs
description: Fejlesztői útmutató – hogyan fejleszthet az Azure IoT SDK-k segítségével korlátozott eszközök.
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: yizhon
ms.openlocfilehash: 7788bca621a59ec8cdfe36edf73a99efca8c460c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59261394"
---
# <a name="develop-for-constrained-devices-using-azure-iot-c-sdk"></a>Fejlesztés az Azure IoT C SDK-val korlátozott eszközök

Az Azure IoT Hub C SDK ANSI C (C99), ami lehetővé teszi az kiválóan működjön, többféle platformon a kis méretű lemez- és erőforrás-igényű nyelven van megírva. A javasolt RAM-MAL legalább 64 KB-nál, de a pontos memóriaigényét attól függ, a használt protokoll, a megnyitott kapcsolatok számát, valamint a megcélzott platform.
> [!NOTE]
> * Az Azure IoT C SDK rendszeresen közzéteszi az erőforrás-felhasználási adatokat fejlesztési kiküszöbölni.  Látogasson el a [GitHub-adattár](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md) , és tekintse át a legújabb teljesítményteszt.
>

C SDK-t az apt-get paranccsal végzi, NuGet és MBED csomag formájában érhető el. Célozhat korlátozott, érdemes az helyben a célként megadott platformjához tartozó SDK felépítése. Ez a dokumentáció bemutatja, hogyan csökkentheti az C SDK-t használó kapacitásigényéhez bizonyos szolgáltatások eltávolítása [cmake](https://cmake.org/). Emellett ez a dokumentáció ismerteti az ajánlott eljárás programozási modellek használata korlátozott eszközök.

## <a name="building-the-c-sdk-for-constrained-devices"></a>Az eszközök korlátozott C SDK felépítéséhez

Korlátozott eszközök C SDK-t hozhat létre.

### <a name="prerequisites"></a>Előfeltételek

Kövesse ezt [C SDK-beállítási útmutató](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) előkészítése a fejlesztési környezetet az C SDK felépítéséhez. A lépés esetében használhatja a cmake kap, mielőtt hívhatja meg a cmake jelzőket a fel nem használt szolgáltatások eltávolítása.

### <a name="remove-additional-protocol-libraries"></a>Távolítsa el a további protokoll kódtárakat

C SDK-t még ma öt protokollokat támogatja: Mqtt-ről, WebSocket, AMQPs, AMQP WebSocket, valamint a HTTPS keresztüli MQTT. A legtöbb forgatókönyvek igényelnek, az ügyfélen futó egy vagy két protokollt, ezért távolíthatja el a protokoll-könyvtár nem használja az SDK-t. További információ a megfelelő kommunikációs protokollt választja, a forgatókönyv találja a [válassza ki az IoT Hub-kommunikációs protokoll](iot-hub-devguide-protocols.md). Például MQTT egy egyszerűsített protokoll, amely gyakran jobban megfelelő a korlátozott eszközök.

Az amqp-t és HTTP kódtárakat, az alábbi cmake paranccsal távolíthatja el:

```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

### <a name="remove-sdk-logging-capability"></a>Távolítsa el az SDK naplózási képesség

Az C SDK-t biztosít széles körű naplózás során a hibakeresés érdekében. A naplózási képesség éles eszközökhöz az alábbi cmake paranccsal távolíthatja el:

```
cmake -Dno_logging=OFF <Path_to_cmake>
```

### <a name="remove-upload-to-blob-capability"></a>Távolítsa el a képesség BLOB feltöltése

Az Azure Storage SDK-ban a beépített funkció használatával nagy méretű fájlokat tölthet fel. Az Azure IoT Hub egy társított Azure Storage-fiókba dispatcher funkcionál. Ez a funkció segítségével médiafájlok, nagy telemetriai kötegek és a naplók küldése. További információkat szerezhet [az IoT Hub fájlok feltöltése](iot-hub-devguide-file-upload.md). Ha az alkalmazás nem követeli meg ezt a funkciót, ez a funkció az alábbi cmake paranccsal távolíthatja el:

```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```

### <a name="running-strip-on-linux-environment"></a>A Linux környezet futó sáv

Ha a bináris fájlok Linux rendszeren futtatja, használhatja a [parancs sáv](https://en.wikipedia.org/wiki/Strip_(Unix)) összeállítása után az utolsó alkalmazás méretének csökkentése érdekében.

```
strip -s <Path_to_executable>
```

## <a name="programming-models-for-constrained-devices"></a>Programozási modellek, korlátozott eszközökhöz

Ezután tekintse meg programozási modellek, korlátozott eszközökhöz.

### <a name="avoid-using-the-serializer"></a>Kerülje a szerializáló

Az C SDK-val rendelkezik egy nem kötelező [C SDK-szerializáló](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer), amely lehetővé teszi, hogy deklaratív hozzárendelési táblák és eszköz-ikertulajdonságok határozhatja meg. A szerializáló úgy tervezték, hogy leegyszerűsítheti a fejlesztést, de azt többletterheléssel, která není optimális korlátozott eszközökhöz. Ebben az esetben fontolja meg az egyszerű ügyfél API-k és a egy egyszerűsített elemző használatával JSON elemzése [parson](https://github.com/kgabis/parson).

### <a name="use-the-lower-layer-ll"></a>Használja az alsóbb rétegek (_LL_)

Az C SDK két programozási modellen támogatja. Egy készletnek az API-k egy _LL_ vpony, amely az alsóbb rétegek rövidítése. Ez olyan API-k, feladatorientált felhasználók által használt súlyozási, és nem helyezhet üzembe munkaszálak, ami azt jelenti, hogy a felhasználó manuálisan kell ellenőrzés ütemezése. Például, ha az ügyfél a _LL_ ebben található API-k [fejlécfájlt](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h). 

API-k nélkül egy másik készletét a _LL_ index nevezzük a kényelem réteg, ha egy munkavégző szál automatikusan hoz. Például a kényelem réteg API-k az eszközügyfél ebben található [IoT Device Client fejlécfájlt](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h). A korlátozott eszközök, ahol minden egyes extra szálat is igénybe vehet az erőforrásokat, jelentős százalékos érdemes _LL_ API-k.

## <a name="next-steps"></a>További lépések

További információ az Azure IoT C SDK-architektúra:
-   [Az Azure IoT C SDK forráskódját](https://github.com/Azure/azure-iot-sdk-c/)
-   [Az Azure IoT eszközoldali SDK-t a C bemutatása](iot-hub-device-sdk-c-intro.md)
