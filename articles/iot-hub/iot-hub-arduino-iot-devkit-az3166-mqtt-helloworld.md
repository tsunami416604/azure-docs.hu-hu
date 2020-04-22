---
title: Üzenetek küldése MQTT-kiszolgálóra az Azure MQTT-ügyfélkönyvtárhasználatával
description: Ismerje meg, hogyan használhatja az MQTT ügyfélkönyvtárat üzenetek küldésére egy MQTT brókernek. Azt is megtudhatja, hogyan konfigurálhatja az mXChip IoT DevKit-et MQTT-ügyfélként.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/02/2018
ms.author: liydu
ms.custom: mqtt
ms.openlocfilehash: 600e64ef5bc3329f0116359066bdcdaf42c13e2e
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733495"
---
# <a name="send-messages-to-an-mqtt-server"></a>Üzenetek küldése MQTT-kiszolgálóra

Az IEt-hálózatok (IoT) rendszerei gyakran időszakos, rossz minőségű vagy lassú internetkapcsolattal foglalkoznak. Az MQTT egy gép-gép (M2M) csatlakozási protokoll, amelyet ilyen kihívások szem előtt tartásával fejlesztettek ki. 

Az itt használt MQTT ügyfélkönyvtár az [Eclipse Paho](https://www.eclipse.org/paho/) projekt része, amely API-kat biztosít az MQTT több közlekedési eszközön történő használatára.

## <a name="what-you-learn"></a>Ismertetett témák

Ebben a projektben megtanulod:
- Az MQTT ügyfélkönyvtár használata üzenetek küldésére egy MQTT brókernek.
- Az MXChip Iot DevKit konfigurálása MQTT ügyfélként.

## <a name="what-you-need"></a>Mi szükséges

Az [első lépések útmutatójának](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) befejezése:

* A DevKit csatlakoztatása Wi-Fi-hálózathoz
* A fejlesztési környezet előkészítése

## <a name="open-the-project-folder"></a>A projektmappa megnyitása

1. Ha a DevKit már csatlakozik a számítógéphez, válassza le.

2. Indítsa el a VS Code-ot.

3. Csatlakoztassa a DevKit-et a számítógéphez.

## <a name="open-the-mqttclient-sample"></a>Az MQTTClient minta megnyitása

Bontsa ki a bal oldali **ARDUINO EXAMPLES szakaszt,** keresse meg **az MXCHIP AZ3166 példák > MQTT**elemet, és válassza az **MQTTClient lehetőséget.** Megnyílik egy új VS-kód ablak, amelyben egy projektmappa található.

> [!NOTE]
> A példát a parancspalettából is megnyithatja. A `Ctrl+Shift+P` parancspaletta megnyitásához használja a (macOS: `Cmd+Shift+P`) parancsot, írja be az **Arduino**parancsot, majd keresse meg és válassza az **Arduino: Examples lehetőséget.**

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>Az Arduino vázlat létrehozása és feltöltése a DevKitbe

Írja `Ctrl+P` be a `Cmd+P`futtatásához `task device-upload`(macOS: ) a futtatásához. A feltöltés befejezése után a DevKit újraindul, és futtatja a vázlatot.

![eszköz-feltöltés](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> A következő hibaüzenet jelenhet meg: "Hiba: AZ3166: Ismeretlen csomag" hibaüzenet jelenhet meg. Ez a hiba akkor fordul elő, ha a táblacsomag indexe nem frissül megfelelően. A hiba megoldásához tekintse meg [az IoT DevKit gyakori kérdések fejlesztési szakaszát.](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development)

## <a name="test-the-project"></a>A projekt tesztelése

A VS-kód mezőben hajtsa végre az alábbi eljárást a Soros figyelő megnyitásához és beállításához:

1. Kattintson `COM[X]` az állapotsoron lévő szóra a `STMicroelectronics` ![megfelelő COM-port beállításához: set-com-port](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. Kattintson az állapotsor hálózati csatlakozójának ikonjára a Soros monitor megnyitásához: ![soros monitor](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. Az állapotsoron kattintson az átviteli sebességre vonatkozó számra, ![és állítsa a következőre: `115200`set-baud-rate](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

A Soros-figyelő megjeleníti a mintavázlat által küldött összes üzenetet. A vázlat összeköti a DevKit-et a Wi-Fi-vel. Miután a Wi-Fi kapcsolat sikeres, a vázlat üzenetet küld az MQTT brókernek. Ezt követően a minta ismételten két "iot.eclipse.org" üzenetet küld a QoS 0 és a QoS 1 használatával.

![soros kimenet](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Problémák és visszajelzések

Ha problémákat tapasztal, olvassa el az [IoT DevKit gyakori kérdéseket,](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) vagy csatlakozzon a következő csatornákon:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>Lásd még

* [Csatlakoztassa az IoT DevKit AZ3166-ot az Azure IoT Hubhoz a felhőben](iot-hub-arduino-iot-devkit-az3166-get-started.md)
* [Rázza, rázza a Tweet](iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message.md)

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan konfigurálhatja az MXChip Iot DevKit-et MQTT ügyfélként, és az MQTT ügyfélkönyvtár segítségével küldhet üzeneteket egy MQTT brókernek, az alábbiak at a következő javasolt lépések közül olvashat:

* [Az Azure IoT remote monitoring megoldásgyorsító – áttekintés](https://docs.microsoft.com/azure/iot-suite/)
* [MXChip IoT DevKit-eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz](/azure/iot-central/core/howto-connect-devkit)
