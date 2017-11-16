---
title: "Az Azure IoT peremhálózati modulok fejlesztése |} Microsoft Docs"
description: "További tudnivalók az egyéni modulok létrehozása az Azure IoT peremhálózati"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 95b1d5d4e5e11f96b6abb17f0aeba935cc65512d
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="understand-the-requirements-and-tools-for-developing-iot-edge-modules---preview"></a>Követelmények és fejlesztési IoT peremhálózati modulok eszközök – előzetes

Ez a cikk azt ismerteti, milyen funkciói érhetők el, ha IoT peremhálózati modulként futó alkalmazások és azok előnyeit.

## <a name="iot-edge-runtime-environment"></a>Az IoT-Edge futásidejű környezet
Az IoT-Edge futásidejű teszi több IoT peremhálózati modul funkcióinak integrálását, és telepítheti őket az IoT-peremeszközök alakzatot infrastruktúrát. Magas szinten a program hozzáadható egy IoT peremhálózati modulként. Azonban teljes körű kihasználása IoT peremhálózati kommunikációs és felügyeleti funkciók, a program futtatása egy modulban is képes csatlakozni a helyi IoT peremhálózati hub, az IoT-Edge futásidejű integrálva.

## <a name="using-the-iot-edge-hub"></a>A peremhálózati IoT hub használatával
A peremhálózati IoT hub biztosít két fő funkciói: proxy IoT-központot, és helyi kommunikáció.

### <a name="iot-hub-primitives"></a>Az IoT-központ primitívek
Az IoT Hub láthatják a modul példány adatproblémák eszközre, abban az értelemben, hogy az informatikai:

* rendelkezik a modul a két, különböző és elkülönül a [eszköz iker] [ lnk-devicetwin] és az egyéb modul twins az adott eszköz;
* küldésére [eszköz a felhőbe küldött üzeneteket][lnk-iothub-messaging];
* megkaphatja a [módszerek közvetlen] [ lnk-methods] identitása irányul.

Jelenleg egy modul nem felhő eszközre üzeneteket fogadni, sem a fájl feltöltése a szolgáltatással.

A modul írásakor segítségével egyszerűen a [Azure IoT eszköz SDK] [ lnk-devicesdk] a peremhálózati IoT hub és a fenti funkciót használja, mint egy alkalmazást, az IoT-központ használatakor az egyetlen különbség, az alkalmazás háttérszolgáltatáshoz, hogy utal, a modul identitása helyett az eszközidentitást.

Lásd: [fejlesztése és egy peremhálózati IoT-modul telepítése a szimulált eszköz] [ lnk-tutorial2] egy modul alkalmazás, amely eszköz-a-felhőbe küldött üzeneteket küld, és használja a modul a két példát.

### <a name="device-to-cloud-messages"></a>Az eszközről a felhőbe irányuló üzenetek
Ahhoz, hogy bonyolult az eszközről a felhőbe üzenetek feldolgozása peremhálózati IoT hub biztosít deklaratív üzenetek modulokat, valamint modulok és az IoT-központ között.
Ez lehetővé teszi, hogy a intercept-modulok és a folyamat üzenetek más modulok által küldött, és továbbíthatja őket az összetett folyamatok.
A cikk [modul-összeállítást] [ lnk-module-comp] ismerteti, hogyan állítható össze a modulok az összetett folyamatok útvonalak használatával.

Egy IoT peremhálózati modul működnek, mint egy normál IoT Hub eszköz alkalmazás tudnak eszközről a felhőbe üzeneteket fogadni, amelyeket a helyi biztonsági IoT hubból proxy ahhoz, hogy dolgozza fel őket.

Peremhálózati IoT hub az üzenetek a modul deklaratív útvonalak leírtak alapján tölti ki a [modul-összeállítást] [ lnk-module-comp] cikk. Az IoT-Edge modul fejlesztésekor fogadhat ezek az üzenetek üzenet kezelők beállításával látható módon az oktatóanyag [fejlesztése és egy peremhálózati IoT-modul telepítése a szimulált eszköz][lnk-tutorial2].

Útvonalak létrehozásának egyszerűsítése érdekében IoT peremhálózati hozzáadja a modul fogalmát *bemeneti* és *kimeneti* végpontok. Egy modul csatlakoznak bármely bemeneti megadása nélkül eszköz-a-felhőbe küldött üzeneteket fogadhat, és az eszköz a felhőbe küldött üzeneteket küldhet kimenetet megadása nélkül.
Explicit bemenetekhez és kimenetekhez, azonban révén útválasztási szabályokat egyszerűbb megértéséhez. Lásd: [modul-összeállítást] [ lnk-module-comp] további információt az útválasztási szabályokat és a modulok bemeneti és kimeneti végpontok.

Végezetül a peremhálózati központ által kezelt eszköz-felhő üzenetek vannak megjelölve a következő tulajdonságai:

| Tulajdonság | Leírás |
| -------- | ----------- |
| $connectionDeviceId | Az üzenetet küldő ügyfél az eszköz azonosítója |
| $connectionModuleId | A modul az üzenetet küldő modul azonosítója |
| $inputName | A bemeneti, ezt az üzenetet kapott. Üres is lehet. |
| $outputName | A kimenet az üzenet. Üres is lehet. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Peremhálózati IoT hub csatlakozik egy modulból
Egy modult a helyi biztonsági IoT hub csatlakozás két lépésből áll: a kapcsolati karakterlánc használhatja, ha az IoT-Edge futásidejű a modul indításakor, és győződjön meg arról, hogy az alkalmazás elfogadja a tanúsítványt a peremhálózati IoT hub azokon az eszközökön.

A kapcsolati karakterlánca a környezeti változóban IoT Edge-futtatókörnyezet van beszúrta `EdgeHubConnectionString`. Így minden programot, amely kívánja használni, akkor érhető el.

Adatproblémák, a peremhálózati IoT hub kapcsolat használni kívánt tanúsítványt nézetmodellbe, a peremhálózati IoT futtatókörnyezet egy fájl elérési úton érhető el a környezeti változó `EdgeModuleCACertificateFile`.

Az oktatóanyag [fejlesztése és egy peremhálózati IoT-modul telepítése a szimulált eszköz] [ lnk-tutorial2] bemutatja, hogyan győződjön meg arról, hogy van-e a tanúsítvány a számítógép tárolójában, a modul alkalmazásban. Egyértelműen bármilyen más módon megbízhatósági kapcsolatok tanúsítvány munka használatával.

## <a name="packaging-as-an-image"></a>Csomagolás képként
Az IoT-Edge modulok Docker képként vannak csomagolva.
Docker toolchain közvetlenül, vagy a Visual Studio Code is használhatja a látható módon az oktatóanyag [fejlesztése és egy peremhálózati IoT-modul telepítése a szimulált eszköz][lnk-tutorial2].

## <a name="next-steps"></a>Következő lépések

Egy modul elkészítéséhez után megtudhatja, hogyan [telepítés IoT peremhálózati modulok léptékű figyelése és][lnk-howto-deploy].

[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-devicetwin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-messaging]: ../iot-hub/iot-hub-devguide-messaging.md
[lnk-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-tutorial2]: tutorial-csharp-module.md
[lnk-module-comp]: module-composition.md
[lnk-howto-deploy]: how-to-deploy-monitor.md
