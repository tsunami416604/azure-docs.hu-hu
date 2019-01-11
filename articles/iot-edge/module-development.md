---
title: Az Azure IoT Edge-modulok fejlesztése |} A Microsoft Docs
description: Kifejleszthet egyedi modulokat az Azure IoT Edge, amely képes kommunikálni a futtatókörnyezetet és az IoT Hub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 3dd5005312e1c59fa53a0d917951c89033374192
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54200632"
---
# <a name="develop-your-own-iot-edge-modules"></a>A saját IoT Edge-modulok fejlesztése

Az Azure IoT Edge-modulok csatlakozhat más Azure-szolgáltatásokkal és hozzájárul az nagyobb felhő folyamatot. Ez a cikk bemutatja, hogyan fejleszthet modulok kommunikáljon az IoT Edge-futtatókörnyezet és az IoT Hub, ezért a többi Azure-felhőben. 

## <a name="iot-edge-runtime-environment"></a>IoT Edge-futtatókörnyezet
Az IoT Edge-futtatókörnyezet biztosít az infrastruktúra több IoT Edge-modulok funkciójának integrálásához és az IoT Edge-eszközökön való üzembe helyezés helyszíne. Magas szinten bármely program is csomagolva, mint az IoT Edge-modul. Azonban teljes mértékben kihasználhatja az IoT Edge kommunikációs és felügyeleti funkciók, a program futtatása egy modulban is képes csatlakozni a helyi IoT Edge hub, az IoT Edge-futtatókörnyezet integrálva.

## <a name="using-the-iot-edge-hub"></a>Használja az IoT Edge hubot
Az IoT Edge hub által biztosított két fő funkciói: az IoT Hub és a helyi hírközlő proxy.

### <a name="iot-hub-primitives"></a>Az IoT Hub primitívek
Az IoT Hub adatproblémák egy eszközt, abban az értelemben, hogy egy modul példányt kap, amely:

* rendelkezik egy ikermodul, amely különböző, és elkülönülnek a [ikereszköz](../iot-hub/iot-hub-devguide-device-twins.md) és az egyéb ikermodulokkal; eszközön
* küldhet [eszköz – felhő üzeneteket](../iot-hub/iot-hub-devguide-messaging.md);
* megkaphatja a [közvetlen metódusok](../iot-hub/iot-hub-devguide-direct-methods.md) identitása célozza.

Jelenleg egy modul nem felhőből az eszközre irányuló üzenetek fogadása és nem használja a fájlfeltöltési funkcióval.

Amikor-modul írása, használhatja a [Azure IoT eszközoldali SDK](../iot-hub/iot-hub-devguide-sdks.md) csatlakozni az IoT Edge hubot és a fenti funkciót használja, mint az IoT Hub használata az eszköz alkalmazást, az egyetlen különbség, hogy az alkalmazásból háttér, akkor tekintse meg a modul identitás helyett az eszközidentitást.

### <a name="device-to-cloud-messages"></a>Az eszközről a felhőbe irányuló üzenetek
Ahhoz, hogy összetett eszközt a felhőbe irányuló üzenetek feldolgozása az IoT Edge hub által biztosított deklaratív az üzenetek útválasztását a modulok között, és a modulok és az IoT Hub között. Deklaratív útválasztást lehetővé teszi, hogy a modulok intercept és más modulok által küldött üzenetek feldolgozásával, és átvezeti őket az összetett folyamatok. További információkért lásd: [hogyan helyezhet üzembe modulokat, és ezekkel létesíthetnek útvonalat az IoT Edge](module-composition.md).

Az IoT Edge-modul, ellentétben a szokásos IoT Hub alkalmazást, annak érdekében, hogy fel használ proxyt a helyi IoT Edge hub által éppen eszköz – felhő üzeneteket fogadhat.

IoT Edge hub az üzeneteket a modul deklaratív útvonalak leírtak alapján tölti ki a [manifest nasazení](module-composition.md). Amikor egy IoT Edge-modul fejlesztése, ezeket az üzeneteket fogadhat üzenet kezelők beállításával.

Útvonalak létrehozása egyszerűbb, IoT Edge hozzáadja a modul fogalmát *bemeneti* és *kimeneti* végpontok. Egy modul fogadhat semmilyen bemenetet megadása nélkül irányítja az összes eszköz – felhő üzeneteket, és az eszköz – felhő üzeneteket küldhetnek kimenetet megadása nélkül. Explicit bemeneteit és kimeneteit, azonban révén a útválasztási szabályok egyszerűbb megértéséhez. 

Végül az Edge hub által kezelt eszköz – felhő üzenetek vannak megjelölve a következő tulajdonságai:

| Tulajdonság | Leírás |
| -------- | ----------- |
| $connectionDeviceId | Az ügyfél elküldte az üzenetet, az eszköz azonosítója |
| $connectionModuleId | A modul az üzenetet küldő modul azonosítója |
| $inputName | A bemenet, amely kapta ezt az üzenetet. Üres is lehet. |
| $outputName | A kimenet az üzenet elküldéséhez használt. Üres is lehet. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Csatlakozás az IoT Edge hubot a modulból
Csatlakozás a helyi IoT Edge hubot a modulból két lépésből áll: 
1. Hozzon létre egy ModuleClient példány az alkalmazásban.
2. Ellenőrizze, hogy az alkalmazás fogad el, hogy az eszközről az IoT Edge hub által bemutatott tanúsítványt.

A modul csatlakozni az IoT Edge hub az eszközön, hogyan DeviceClient példányok IoT-eszközök csatlakoztatása az IoT hub hasonló ModuleClient-példány létrehozása. A ModuleClient osztály és a kommunikációs módszer kapcsolatos további információkért tekintse meg az API-referencia elsődleges SDK nyelv: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C és Python](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable), vagy [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).


## <a name="next-steps"></a>További lépések

Miután modul fejlesztése, megtudhatja, hogyan [üzembe helyezés és nagy mennyiségű IoT Edge-modulok figyelése](how-to-deploy-monitor.md).

