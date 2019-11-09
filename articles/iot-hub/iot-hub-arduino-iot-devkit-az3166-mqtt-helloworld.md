---
title: Üzenetek küldése egy MQTT-kiszolgálónak az Azure MQTT ügyféloldali kódtár használatával | Microsoft Docs
description: Ismerje meg, hogyan küldhet üzeneteket egy MQTT-közvetítőnek a MQTT ügyféloldali kódtár használatával. Azt is megtudhatja, hogyan konfigurálhatja a mXChip IoT-fejlesztői készlet MQTT-ügyfélként.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/02/2018
ms.author: liydu
ms.openlocfilehash: 4de8b0910a2be48cc626775c5c9bc873c6e780dc
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889550"
---
# <a name="send-messages-to-an-mqtt-server"></a>Üzenetek küldése egy MQTT-kiszolgálónak

A eszközök internetes hálózata-(IoT-) rendszerek gyakran időszakos, gyenge minőségű vagy lassú internetkapcsolattal foglalkoznak. A MQTT egy, a gép és a gép közötti (MESTERi) kapcsolati protokoll, amely az ilyen jellegű kihívásokkal lett kifejlesztve. 

Az itt használt MQTT-ügyfél-függvénytár az [Eclipse PAHO](https://www.eclipse.org/paho/) projekt része, amely API-kat biztosít a MQTT több szállítóeszközön való használatához.

## <a name="what-you-learn"></a>Ismertetett témák

Ebben a projektben a következőket tanulhatja meg:
- A MQTT ügyféloldali kódtár használata üzenetek küldésére egy MQTT-közvetítőnek.
- A MXChip IOT fejlesztői készlet konfigurálása MQTT-ügyfélként.

## <a name="what-you-need"></a>Mi szükséges

Fejezze be a [első lépések útmutatót](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) a következőre:

* Csatlakoztassa a fejlesztői készlet a Wi-Fi-hez
* A fejlesztési környezet előkészítése

## <a name="open-the-project-folder"></a>A Project mappa megnyitása

1. Ha a fejlesztői készlet már csatlakozik a számítógéphez, válassza le azt.

2. Indítsa el a VS Code-ot.

3. Kapcsolódjon a fejlesztői készlet a számítógéphez.

## <a name="open-the-mqttclient-sample"></a>A MQTTClient minta megnyitása

Bontsa ki a bal oldali **ARDUINO-példák** szakaszt, és keresse meg a **PÉLDÁKAT a MXCHIP AZ3166 > MQTT**, és válassza a **MQTTClient**lehetőséget. Megnyílik egy új VS Code ablak a projekt mappájával.

> [!NOTE]
> Megnyithatja például a parancssorból is. `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) használatával nyissa meg a parancssort, írja be az **Arduino**parancsot, majd keresse meg és válassza ki az **Arduino: példákat**.

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>Az Arduino-vázlat felépítése és feltöltése a fejlesztői készlet

A `task device-upload`futtatásához írja be a `Ctrl+P` (macOS: `Cmd+P`) parancsot. A feltöltés befejezése után a fejlesztői készlet újraindítja és futtatja a vázlatot.

![eszköz – feltöltés](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> A "hiba: AZ3166: ismeretlen csomag" hibaüzenet jelenhet meg. Ez a hiba akkor fordul elő, ha a tábla csomagjának indexe nem frissül megfelelően. A hiba megoldásához tekintse meg a [IoT fejlesztői készlet – gyakori kérdések című témakör fejlesztés szakaszát](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>A projekt tesztelése

A VS Code-ban kövesse ezt az eljárást a soros figyelő megnyitásához és beállításához:

1. Kattintson az állapotsorban található `COM[X]` szóra a megfelelő COM-port beállításához `STMicroelectronics`: ![set-com-port](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. Kattintson az állapotsorban található Power plugin ikonra a soros figyelő megnyitásához: ![soros figyelő](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. Az állapotsoron kattintson arra a számra, amely a Baud arányt jelöli, majd állítsa be `115200`re: ![set-Baud-Rate](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

A soros figyelő megjeleníti a minta vázlat által küldött összes üzenetet. A vázlat csatlakoztatja a fejlesztői készlet a Wi-Fi-hez. A Wi-Fi-kapcsolat sikeressége után a vázlat üzenetet küld a MQTT-közvetítőnek. Ezután a minta ismételten két "iot.eclipse.org" üzenetet küld a QoS 0 és a QoS 1 használatával.

![soros kimenet](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Ha problémákba ütközik, tekintse meg a [IoT fejlesztői készlet kapcsolatos gyakori kérdéseket](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) , vagy kapcsolódjon a következő csatornákon keresztül:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>Lásd még:

* [A IoT fejlesztői készlet AZ3166 összekötése az Azure IoT Hub a felhőben](iot-hub-arduino-iot-devkit-az3166-get-started.md)
* [Shake, Shake for a tweet](iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message.md)

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan konfigurálhatja a MXChip IOT fejlesztői készlet MQTT-ügyfélként, és az MQTT-ügyfél kódtár használatával küldhet üzeneteket egy MQTT-közvetítőnek, a javasolt következő lépések:

* [Az Azure IoT távoli monitorozási megoldásának gyorsítása – áttekintés](https://docs.microsoft.com/azure/iot-suite/)
* [MXChip-IoT fejlesztői készlet-eszköz csatlakoztatása az Azure IoT Central-alkalmazáshoz](/azure/iot-central/core/howto-connect-devkit)
