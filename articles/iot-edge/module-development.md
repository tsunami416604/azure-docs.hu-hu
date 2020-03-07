---
title: Az Azure IoT Edge-modulok fejlesztése |} A Microsoft Docs
description: Kifejleszthet egyedi modulokat az Azure IoT Edge, amely képes kommunikálni a futtatókörnyezetet és az IoT Hub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 96bd6b461a5374b5f5bc578c5f58dbcd09cd7087
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78358694"
---
# <a name="develop-your-own-iot-edge-modules"></a>Saját IoT Edge-modulok fejlesztése

Azure IoT Edge modulok kapcsolódhatnak más Azure-szolgáltatásokhoz, és hozzájárulnak a nagyobb Felhőbeli adatfolyamathoz. Ez a cikk azt ismerteti, hogyan fejleszthet modulokat a IoT Edge futtatókörnyezettel és IoT Hubával, így az Azure-felhő többi részével folytatott kommunikációhoz.

## <a name="iot-edge-runtime-environment"></a>IoT Edge-futtatókörnyezet

Az IoT Edge-futtatókörnyezet biztosít az infrastruktúra több IoT Edge-modulok funkciójának integrálásához és az IoT Edge-eszközökön való üzembe helyezés helyszíne. Bármely program IoT Edge modulként is becsomagolható. Az IoT Edge kommunikációs és kezelési funkciók teljes kihasználása érdekében egy modulban futó program az Azure IoT-eszköz SDK-val csatlakozhat a helyi IoT Edge hubhoz.

## <a name="using-the-iot-edge-hub"></a>Használja az IoT Edge hubot

Az IoT Edge hub által biztosított két fő funkciói: az IoT Hub és a helyi hírközlő proxy.

### <a name="iot-hub-primitives"></a>Az IoT Hub primitívek

Az IoT Hub adatproblémák egy eszközt, abban az értelemben, hogy egy modul példányt kap, amely:

* Ez egy külön modul, amely külön és elkülönített az [eszköz Twin](../iot-hub/iot-hub-devguide-device-twins.md) és a többi modul ikrek közül.
* képes az [eszközről a felhőbe irányuló üzenetek](../iot-hub/iot-hub-devguide-messaging.md)küldésére;
* közvetlenül a saját identitására irányuló [közvetlen metódusokat](../iot-hub/iot-hub-devguide-direct-methods.md) is fogadhat.

A modulok jelenleg nem fogadhatnak a felhőből az eszközre irányuló üzeneteket, vagy használhatják a fájlfeltöltés funkciót.

Modul írásakor az [Azure IoT-eszköz SDK](../iot-hub/iot-hub-devguide-sdks.md) -val csatlakozhat az IoT Edge hubhoz, és használhatja a fenti funkciókat úgy, mint ha IoT Hubt használ egy eszköz-alkalmazással. Az egyetlen különbség IoT Edge modulok és a IoT között, hogy az eszköz identitása helyett a modul identitására kell hivatkoznia.

### <a name="device-to-cloud-messages"></a>Az eszközről a felhőbe irányuló üzenetek

Az eszközről a felhőbe irányuló üzenetek összetett feldolgozásának engedélyezéséhez IoT Edge hub a modulok, a modulok és a IoT Hub között a deklaratív útválasztást biztosítja. Deklaratív útválasztást lehetővé teszi, hogy a modulok intercept és más modulok által küldött üzenetek feldolgozásával, és átvezeti őket az összetett folyamatok. További információ: [modulok telepítése és útvonalak létrehozása IoT Edgeban](module-composition.md).

Egy IoT Edge modul, amely nem egy normál IoT Hub eszköz-alkalmazás, képes fogadni az eszközről a felhőbe irányuló, a helyi IoT Edge hub által az általa feldolgozható üzeneteket.

IoT Edge hub az üzeneteket a modulba az [üzembe helyezési jegyzékben](module-composition.md)ismertetett deklaratív útvonalak alapján propagálja. Amikor egy IoT Edge-modul fejlesztése, ezeket az üzeneteket fogadhat üzenet kezelők beállításával.

Az útvonalak létrehozásának egyszerűbbé tétele érdekében IoT Edge hozzáadja a modul *bemeneti* és *kimeneti* végpontjának koncepcióját. Egy modul fogadhat semmilyen bemenetet megadása nélkül irányítja az összes eszköz – felhő üzeneteket, és az eszköz – felhő üzeneteket küldhetnek kimenetet megadása nélkül. Explicit bemeneteit és kimeneteit, azonban révén a útválasztási szabályok egyszerűbb megértéséhez.

Végül az Edge hub által kezelt eszköz – felhő üzenetek vannak megjelölve a következő tulajdonságai:

| Tulajdonság | Leírás |
| -------- | ----------- |
| $connectionDeviceId | Az ügyfél elküldte az üzenetet, az eszköz azonosítója |
| $connectionModuleId | A modul az üzenetet küldő modul azonosítója |
| $inputName | A bemenet, amely kapta ezt az üzenetet. Üres is lehet. |
| $outputName | A kimenet az üzenet elküldéséhez használt. Üres is lehet. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Csatlakozás az IoT Edge hubot a modulból

Csatlakozás a helyi IoT Edge hubot a modulból két lépésből áll:

1. Hozzon létre egy ModuleClient-példányt az alkalmazásban.
2. Ellenőrizze, hogy az alkalmazás fogad el, hogy az eszközről az IoT Edge hub által bemutatott tanúsítványt.

Hozzon létre egy ModuleClient-példányt, amely az eszközön futó IoT Edge hubhoz csatlakoztatja a modult, hasonlóan ahhoz, ahogy a DeviceClient-példányok IoT-eszközök csatlakoztatását IoT Hubhoz. A ModuleClient osztályról és a hozzá tartozó kommunikációs módszerekről a következő témakörben talál további információt: API-hivatkozás [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)az előnyben részesített SDK-nyelvhez:, [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)vagy [Node. js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

## <a name="language-and-architecture-support"></a>Nyelvi és architektúra-támogatás

IoT Edge támogatja több operációs rendszer, eszköz architektúrája és fejlesztői nyelv használatát, így az igényeinek megfelelő forgatókönyvet hozhatja létre. Ebben a szakaszban megismerheti az egyéni IoT Edge-modulok fejlesztésének lehetőségeit. A [fejlesztési és tesztelési környezet előkészítéséhez az IoT Edge](development-environment.md)az egyes nyelvekhez kapcsolódó támogatás és követelmények megismerését ismertető témakörben olvashat bővebben.

### <a name="linux"></a>Linux

Az alábbi táblázatban szereplő összes nyelv esetében IoT Edge támogatja az AMD64-és ARM32-alapú Linux-eszközök fejlesztését.

| Fejlesztési nyelv | Fejlesztési eszközök |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Visual Studio 2017/2019 |
| C# | Visual Studio Code<br>Visual Studio 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>A ARM64 Linux-eszközök fejlesztésének és hibakeresésének támogatása [nyilvános előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verzióban érhető el. További információ: [ARM64 IoT Edge-modulok fejlesztése és hibakeresése a Visual Studio Code-ban (előzetes verzió)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

### <a name="windows"></a>Windows

Az alábbi táblázatban szereplő összes nyelv esetében IoT Edge támogatja az AMD64 Windows-eszközök fejlesztését.

| Fejlesztési nyelv | Fejlesztési eszközök |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | Visual Studio Code (nincs hibakeresési képesség)<br>Visual Studio 2017/2019 |

## <a name="next-steps"></a>Következő lépések

[A fejlesztési és tesztelési környezet előkészítése IoT Edge](development-environment.md)

[A Visual Studio használata a C# IoT Edge moduljainak fejlesztéséhez](how-to-visual-studio-develop-module.md)

[Modulok fejlesztése a Visual Studio Code használatával IoT Edge](how-to-vs-code-develop-module.md)

[Az Azure IoT Hub SDK-k megismerése és használata](../iot-hub/iot-hub-devguide-sdks.md)
