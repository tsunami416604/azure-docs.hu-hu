---
title: Üzenetek küldése egy MQTT kiszolgálót az Azure MQTT ügyféloldali kódtár |} Microsoft Docs
description: Segítségével a DevKit ügyfélként üzenetek MQTT kiszolgálóhoz
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/02/2018
ms.author: liydu
ms.openlocfilehash: ee8ff1acbaf5d97d62d6811e8e8abc86017b32fe
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632611"
---
# <a name="send-messages-to-an-mqtt-server"></a>Üzenetek küldése egy MQTT kiszolgáló

Az eszközök internetes hálózatát (IoT) rendszerek gyakran időszakos, gyenge minőségi foglalkozik, vagy lassú hálózati kapcsolatok használata esetén. MQTT egy olyan, gépenként (M2M) kapcsolat protokoll, amely ilyen kihívást szem előtt jött létre. 

Az itt használt MQTT ügyféloldali kódtár része a [Eclipse Paho](http://www.eclipse.org/paho/) projekt, amely MQTT több azt jelenti, hogy az átviteli protokollt használó biztosít az API-k.

## <a name="what-you-learn"></a>Ismertetett témák

Ez a projekt elsajátíthatja:
- Hogyan lehet üzenetek küldése egy MQTT broker a MQTT ügyféloldali kódtár segítségével.
- Hogyan konfigurálható a MXChip Iot DevKit MQTT-ügyfélként.

## <a name="what-you-need"></a>Mi szükséges

Befejezés a [– első lépések útmutató](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) számára:

* A Wi-Fi csatlakozik DevKit rendelkezik
* A fejlesztési környezet előkészítése

## <a name="open-the-project-folder"></a>Nyissa meg a projekt mappát

1. Válassza le a DevKit a számítógépről, ha már csatlakoztatva van.

2. Indítsa el a Visual STUDIO Code.

3. A DevKit kapcsolódni a számítógéphez.

## <a name="open-the-mqttclient-sample"></a>Nyissa meg a MQTTClient minta

Bontsa ki a bal oldali **ARDUINO példák** szakaszban, keresse meg a **MXCHIP AZ3166 példák > MQTT**, és válassza ki **MQTTClient**. Egy új Visual STUDIO Code-ablak nyílik meg azt a projekt mappát.

> [!NOTE]
> Példa parancs palettáról is megnyithatja. Használjon `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) nyissa meg a parancs palettát, írja be a következőt **Arduino**, keresése és válassza a **Arduino: Példák**.

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>Build, majd töltse fel a Arduino vázlatot a DevKit

Típus `Ctrl+P` (macOS: `Cmd+P`) futtatásához `task device-upload`. Ha befejeződött a feltöltés, a DevKit újraindul, és a Vázlat futtatja.

![eszköz-feltöltése](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> Jelenhet meg egy "hiba: AZ3166: Ismeretlen csomag" hibaüzenet jelenik meg. Ez akkor fordul elő, ha a tábla csomag index nem frissül megfelelően. Ez a hiba megoldása érdekében tekintse meg a [gyakran ismételt kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>A projekt tesztelése

Visual STUDIO Code az alábbi eljárás segítségével megnyitása és a soros figyelő beállítása:

1. Kattintson a `COM[X]` az állapotsoron a megfelelő COM-port az a word `STMicroelectronics`: ![set-com-port](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. Kattintson a soros figyelő megnyitásához az állapotsor power plug ikonjára: ![soros-figyelő](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. Az állapotsorban kattintva átviteli sebességét, és állítsa az értékét `115200`: ![set-sebesség](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

A soros figyelő a minta vázlat által küldött összes üzenet jeleníti meg. A vázlat a DevKit Wi-Fi kapcsolatot. Ha a Wi-Fi kapcsolat sikeres, a Vázlat üzenetet küld a MQTT broker. Ezt követően a minta két "iot.eclipse.org" üzenetek QoS 0 és QoS 1, illetve ismételten küld.

![o-sorozat](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Ha problémába ütközik, tekintse meg a [– gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) , illetve a következő csatornákon keresztül csatlakozik:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>Lásd még

* [IoT DevKit AZ3166 csatlakozni az Azure IoT Hub a felhőben]({{"/docs/getting-started/" | absolute_url }})
* [Rázó, egy Tweetet a Shake]({{"/docs/projects/shake-shake/" | absolute_url }})

## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan lehet konfigurálni a MXChip Iot DevKit MQTT-ügyfélként, és üzenetek küldése egy MQTT broker a MQTT ügyféloldali kódtár segítségével, az alábbiakban a javasolt lépéseket:

* [Az Azure IoT távoli figyelési megoldást gyorsító áttekintése](https://docs.microsoft.com/azure/iot-suite/)
* [Csatlakoztasson egy MXChip IoT DevKit eszközt az Azure IoT központi alkalmazás](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
