---
title: Modulok fejlesztése az Azure IoT Edge-hez | Microsoft dokumentumok
description: Egyéni modulok at fejleszthet az Azure IoT Edge-hez, amelyek kommunikálhatnak a futásidejűvel és az IoT Hubbal
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 96bd6b461a5374b5f5bc578c5f58dbcd09cd7087
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271381"
---
# <a name="develop-your-own-iot-edge-modules"></a>Saját IoT Edge-modulok kifejlesztése

Az Azure IoT Edge-modulok más Azure-szolgáltatásokkal is csatlakozhatnak, és hozzájárulhatnak a nagyobb felhőalapú adatfolyamathoz. Ez a cikk ismerteti, hogyan fejleszthet modulokat az IoT Edge futásidejű és az IoT Hub, és így az Azure-felhő többi részével való kommunikációhoz.

## <a name="iot-edge-runtime-environment"></a>IoT Edge futásidejű környezet

Az IoT Edge futásidejű biztosítja az infrastruktúrát, hogy integrálja a funkcionalitást több IoT Edge-modulok és üzembe helyezése az IoT Edge-eszközökre. Bármely program iot edge-modulként csomagolható. Az IoT Edge kommunikációs és felügyeleti funkcióinak teljes kihasználása érdekében egy modulban futó program használhatja az Azure IoT Device SDK-t a helyi IoT Edge hubhoz való csatlakozáshoz.

## <a name="using-the-iot-edge-hub"></a>Az IoT Edge hub használata

Az IoT Edge hub két fő funkciót biztosít: proxy az IoT Hub, és a helyi kommunikáció.

### <a name="iot-hub-primitives"></a>IoT Hub primitívek

Az IoT Hub egy modulpéldányt az eszközhöz hasonlóan lát, abban az értelemben, hogy:

* van egy ikermodulja, amely elkülönül és elkülönül az [eszköz ikereszközjétől,](../iot-hub/iot-hub-devguide-device-twins.md) és a másik modul ikerpárja;
* [képes eszközről felhőbe](../iot-hub/iot-hub-devguide-messaging.md)irányuló üzeneteket küldeni;
* közvetlen, kifejezetten a személyazonosságára irányuló [módszereket](../iot-hub/iot-hub-devguide-direct-methods.md) kaphat.

Jelenleg a modulok nem tudnak felhőből eszközre irányuló üzeneteket fogadni, és nem használhatják a fájlfeltöltési funkciót.

Modul írásakor az [Azure IoT Device SDK](../iot-hub/iot-hub-devguide-sdks.md) segítségével csatlakozhat az IoT Edge hubhoz, és a fenti funkciókat úgy használhatja, mint az IoT Hub eszközalkalmazással való használata. Az egyetlen különbség az IoT Edge-modulok és az IoT-eszközalkalmazások között az, hogy az eszközidentitás helyett a modulidentitásra kell hivatkoznia.

### <a name="device-to-cloud-messages"></a>Az eszközről a felhőbe irányuló üzenetek

Az eszközök közötti üzenetek összetett feldolgozásának engedélyezéséhez az IoT Edge hub deklaratív üzenettovábbítást biztosít a modulok, valamint a modulok és az IoT Hub között. A deklaratív útválasztás lehetővé teszi, hogy a modulok elfogják és feldolgozzák a más modulok által küldött üzeneteket, és összetett folyamatokba propasszodják őket. További információ: [modulok üzembe helyezése és útvonalak létrehozása az IoT Edge-ben.](module-composition.md)

Az IoT Edge-modul, szemben a normál IoT Hub-eszközalkalmazás, képes fogadni az eszköz-felhő üzeneteket, amelyek a helyi IoT Edge hub feldolgozása.

Az IoT Edge hub a [központi telepítési jegyzékben](module-composition.md)leírt deklaratív útvonalak alapján továbbítja az üzeneteket a modulba. IoT Edge-modul fejlesztése során ezeket az üzeneteket üzenetkezelők beállításával kaphatja meg.

Az útvonalak létrehozásának egyszerűsítése érdekében az IoT Edge hozzáadja a *modulbemeneti* és *kimeneti* végpontok fogalmát. A modul képes fogadni az összes eszközről a felhőbe irányuló üzeneteket, amelyek bármilyen bemenet megadása nélkül továbbíthatók hozzá, és bármilyen kimenet megadása nélkül küldhetnek eszközről felhőbe irányuló üzeneteket. Explicit bemenetek és kimenetek használata azonban egyszerűbbé teszi az útválasztási szabályok megértését.

Végül az Edge hub által kezelt eszközök közötti üzenetek a következő rendszertulajdonságokkal vannak lepecsételve:

| Tulajdonság | Leírás |
| -------- | ----------- |
| $connectionDeviceId | Az üzenetet küldő ügyfél eszközazonosítója |
| $connectionModuleId | Az üzenetet küldő modul modulazonosítója |
| $inputName | Az üzenetet kapott bemenet. Lehet üres. |
| $outputName | Az üzenet küldéséhez használt kimenet. Lehet üres. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Csatlakozás az IoT Edge hubhoz egy modulból

A helyi IoT Edge-hubhoz való csatlakozás egy modulból két lépésből áll:

1. Hozzon létre egy ModuleClient példányt az alkalmazásban.
2. Győződjön meg arról, hogy az alkalmazás elfogadja az IoT Edge hub által az eszközön bemutatott tanúsítványt.

Hozzon létre egy ModuleClient-példányt, amely összeköti a modult az eszközön futó IoT Edge hubhoz, hasonlóan ahhoz, ahogyan a DeviceClient-példányok iot-eszközöket csatlakoztatnak az IoT Hubhoz. A ModuleClient osztályról és annak kommunikációs módszereiről további információt a kívánt SDK-nyelv API-hivatkozási útmutatójában talál: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C,](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h) [Python,](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python) [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)vagy [Node.js.](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

## <a name="language-and-architecture-support"></a>Nyelvi és építészeti támogatás

Az IoT Edge több operációs rendszert, eszközarchitektúrát és fejlesztési nyelvet támogat, így az igényeinek megfelelő forgatókönyvet hozhat létre. Ebben a szakaszban megismerheti az egyéni IoT Edge-modulok fejlesztésére vonatkozó lehetőségeket. Az egyes nyelvek eszköztámogatásáról és követelményeiről [az IoT Edge fejlesztési és tesztelési környezetének előkészítése](development-environment.md)című részben olvashat bővebben.

### <a name="linux"></a>Linux

Az alábbi táblázatban szereplő összes nyelv esetében az IoT Edge támogatja az AMD64 és ARM32 Linux eszközök fejlesztését.

| Fejlesztési nyelv | Fejlesztési eszközök |
| -------------------- | ----------------- |
| C# | Visual Studio Code<br>Visual Studio 2017/2019 |
| C# | Visual Studio Code<br>Visual Studio 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>Az ARM64 Linux-eszközök fejlesztésére és hibakeresésére vonatkozó támogatás [nyilvános előzetes verzióban](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)érhető el. További információ: [ARM64 IoT Edge modulok fejlesztése és hibakeresése a Visual Studio Code programban (előzetes verzió)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

### <a name="windows"></a>Windows

Az alábbi táblázatban szereplő összes nyelv esetében az IoT Edge támogatja az AMD64 Windows-eszközök fejlesztését.

| Fejlesztési nyelv | Fejlesztési eszközök |
| -------------------- | ----------------- |
| C# | Visual Studio 2017/2019 |
| C# | Visual Studio-kód (nincs hibakeresési képesség)<br>Visual Studio 2017/2019 |

## <a name="next-steps"></a>További lépések

[Készítse elő fejlesztési és tesztelési környezetét az IoT Edge számára](development-environment.md)

[C# modulok fejlesztése az IoT Edge-hez a Visual Studió val](how-to-visual-studio-develop-module.md)

[Az IoT Edge moduljainak fejlesztése a Visual Studio Code használatával](how-to-vs-code-develop-module.md)

[Az Azure IoT Hub SDK-k ismertetése és használata](../iot-hub/iot-hub-devguide-sdks.md)
