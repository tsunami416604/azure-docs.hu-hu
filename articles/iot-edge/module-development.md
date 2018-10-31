---
title: Az Azure IoT Edge-modulok fejlesztése |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egyéni modulok az Azure IoT Edge-hez
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 761485de4bf52b7261ac8f1f8c3d937486c66546
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50248000"
---
# <a name="understand-the-requirements-and-tools-for-developing-iot-edge-modules"></a>A követelmények és az eszközök IoT Edge-modulok megismerése

Ez a cikk azt ismerteti, milyen funkciók érhetők el az alkalmazásokat, amelyek IoT Edge-modul futtató írásakor és azok előnyeit.

## <a name="iot-edge-runtime-environment"></a>IoT Edge-futtatókörnyezet
Az IoT Edge-futtatókörnyezet biztosít az infrastruktúra több IoT Edge-modulok funkciójának integrálásához és az IoT Edge-eszközökön való üzembe helyezés helyszíne. Magas szinten bármely program is csomagolva, mint az IoT Edge-modul. Azonban teljes mértékben kihasználhatja az IoT Edge kommunikációs és felügyeleti funkciók, a program futtatása egy modulban is képes csatlakozni a helyi IoT Edge hub, az IoT Edge-futtatókörnyezet integrálva.

## <a name="using-the-iot-edge-hub"></a>Használja az IoT Edge hubot
Az IoT Edge hub által biztosított két fő funkciói: az IoT Hub és a helyi hírközlő proxy.

### <a name="iot-hub-primitives"></a>Az IoT Hub primitívek
IoT Hub számára egy modul példány adatproblémák eszközre, abban az értelemben, hogy az informatikai:

* ikermodul, amely különböző, és elkülönülnek a rendelkezik a [ikereszköz](../iot-hub/iot-hub-devguide-device-twins.md) és az egyéb ikermodulokkal; eszközön
* küldhet [eszköz – felhő üzeneteket](../iot-hub/iot-hub-devguide-messaging.md);
* megkaphatja a [közvetlen metódusok](../iot-hub/iot-hub-devguide-direct-methods.md) identitása célozza.

Jelenleg egy modul nem felhőből az eszközre irányuló üzenetek fogadása és nem használja a fájlfeltöltési funkcióval.

Amikor-modul írása, egyszerűen használhatja a [Azure IoT eszközoldali SDK](../iot-hub/iot-hub-devguide-sdks.md) csatlakozni az IoT Edge hubot és a fenti funkciót használja, mint egy alkalmazást, az egyetlen különbség, hogy az IoT Hub használata esetén a alkalmazás-alapú háttérrendszerrel, akkor tekintse meg a modul identitás helyett az eszközidentitást.

Lásd: [fejlesztés és üzembe helyezése az IoT Edge-modul egy szimulált eszköz](tutorial-csharp-module.md) egy modul alkalmazás, amely eszköz – felhő üzeneteket, és az ikermodul használ példaként.

### <a name="device-to-cloud-messages"></a>Az eszközről a felhőbe irányuló üzenetek
Ahhoz, hogy összetett eszközt a felhőbe irányuló üzenetek feldolgozása az IoT Edge hub által biztosított deklaratív az üzenetek útválasztását a modulok között, és a modulok és az IoT Hub között.
Ez lehetővé teszi, hogy a modulok intercept és más modulok által küldött üzenetek feldolgozásával, és átvezeti őket az összetett folyamatok.
A cikk [modul-összeállítását](module-composition.md) modulok compose az útvonalak használatával összetett folyamatokat ismerteti.

Az IoT Edge-modul működnek, mint a szokásos IoT Hub eszközalkalmazás kapnak annak érdekében, hogy fel használ proxyt a helyi IoT Edge hub által éppen eszköz – felhő üzeneteket.

IoT Edge hub az üzeneteket a modul deklaratív útvonalak leírtak alapján tölti ki a [modul-összeállítását](module-composition.md) cikk. Amikor egy IoT Edge-modul fejlesztése, fogadhat ezeket az üzeneteket üzenet kezelők, beállításával, ahogyan az oktatóanyag [fejlesztés és üzembe helyezése az IoT Edge-modul egy szimulált eszköz] [lnk-tutorial2].

Annak érdekében, hogy az útvonalak létrehozása egyszerűbb, IoT Edge hozzáadja a modul fogalmát *bemeneti* és *kimeneti* végpontok. Egy modul fogadhat semmilyen bemenetet megadása nélkül irányítja az összes eszköz – felhő üzeneteket, és az eszköz – felhő üzeneteket küldhetnek kimenetet megadása nélkül.
Explicit bemeneteit és kimeneteit, azonban révén a útválasztási szabályok egyszerűbb megértéséhez. Lásd: [modul-összeállítását](module-composition.md) további információt az útválasztási szabályokat és modulok bemeneti és kimeneti végpontok.

Végül az Edge hub által kezelt eszköz – felhő üzenetek vannak megjelölve a következő tulajdonságai:

| Tulajdonság | Leírás |
| -------- | ----------- |
| $connectionDeviceId | Az ügyfél elküldte az üzenetet, az eszköz azonosítója |
| $connectionModuleId | A modul az üzenetet küldő modul azonosítója |
| $inputName | A bemenet, amely kapta ezt az üzenetet. Üres is lehet. |
| $outputName | A kimenet az üzenet elküldéséhez használt. Üres is lehet. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Csatlakozás az IoT Edge hubot a modulból
Csatlakozás a helyi IoT Edge hubot a modulból két lépésből áll: a kapcsolati karakterláncot használja az IoT Edge-futtatókörnyezet, a modul indításakor, és ellenőrizze, hogy az alkalmazás fogad el, hogy az eszközről az IoT Edge hub által bemutatott tanúsítványt a megadott.

A használandó karakterláncban szúrhatja be a környezeti változó az IoT Edge-futtatókörnyezete által `EdgeHubConnectionString`. Így elérhető minden olyan alkalmazást használni szeretné.

A tanúsítványt használja, az IoT Edge hub-kapcsolat ellenőrzése adatproblémák szúrhatja be egy fájlban, amelynek elérési útja környezeti változóban érhető el az IoT Edge-futtatókörnyezete által `EdgeModuleCACertificateFile`.

Az oktatóanyag [fejlesztés és üzembe helyezése az IoT Edge-modul egy szimulált eszköz] [lnk-tutorial2] mutatja be, ügyeljen arra, hogy a tanúsítvány a számítógép tárolójában, a modul alkalmazásában. Világosan minden más módszer megbízható a tanúsítvány munkahelyi kapcsolatokat.

## <a name="packaging-as-an-image"></a>A csomagolási képként
IoT Edge-modulok vannak csomagolva, mint a Docker-rendszerképeket.
Közvetlenül a Docker eszközlánc, vagy a Visual Studio Code módon használhatja a jelen oktatóanyagban ismertetett [fejlesztés és üzembe helyezése az IoT Edge-modul egy szimulált eszköz] [lnk-tutorial2].

## <a name="next-steps"></a>További lépések

Miután modul fejlesztése, megtudhatja, hogyan [üzembe helyezés és nagy mennyiségű IoT Edge-modulok figyelése](how-to-deploy-monitor.md).

