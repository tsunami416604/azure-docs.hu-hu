---
title: Üzenetek küldése MQTT-kiszolgálóra az Azure MQTT ügyféloldali kódtár használatával |} A Microsoft Docs
description: A fejlesztői készlet használja ügyfélként üzenetek küldése MQTT-kiszolgálóra
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/02/2018
ms.author: liydu
ms.openlocfilehash: fc74613e00adc459f7a7b0a16c6f773fe4bf601d
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39593655"
---
# <a name="send-messages-to-an-mqtt-server"></a>Üzenetek küldése MQTT-kiszolgálóra

Internet of Things (IoT) rendszerek gyakran időszakos, gyenge minőség foglalkozik, és a lassú hálózati kapcsolatok használata esetén. MQTT-gép – gép (M2M) csatlakozási protokoll, amely szem előtt az ilyen kihívások fejlesztettek. 

Az itt használt MQTT ügyféloldali kódtár része a [Eclipse Paho](http://www.eclipse.org/paho/) projekt, amely API-kat biztosít több szállítóeszközt MQTT-val.

## <a name="what-you-learn"></a>Ismertetett témák

Ez a projekt bemutatja:
- Hogyan üzenetek küldése egy MQTT broker az MQTT ügyféloldali kódtár használatával.
- Hogyan konfigurálható az MXChip Iot DevKit MQTT-ügyfélként.

## <a name="what-you-need"></a>Mi szükséges

Fejezze be a [– első lépések útmutató](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) való:

* A fejlesztői készlet Wi-Fi csatlakozik
* A fejlesztési környezet előkészítése

## <a name="open-the-project-folder"></a>A projektmappa fájllistájának megnyitásához.

1. Ha a fejlesztői készlet már a számítógéphez, válassza le azt.

2. Indítsa el a VS Code.

3. A fejlesztői készlet csatlakoztatása a számítógéphez.

## <a name="open-the-mqttclient-sample"></a>Nyissa meg a MQTTClient minta

Bontsa ki a bal oldali **ARDUINO példák** területén keresse meg a **példák az MXCHIP AZ3166 > MQTT**, és válassza ki **MQTTClient**. Egy új VS Code-ablak megnyílik, a projektmappa.

> [!NOTE]
> A példában a parancskatalógus is megnyithatja. Használat `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) a parancskatalógus megnyitásához, írja be a **Arduino**, keresse meg és válassza a **Arduino: Példák**.

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>Hozhat létre és tölt fel az Arduino vázlatot a fejlesztői készlet

Típus `Ctrl+P` (macOS: `Cmd+P`) futtatásához `task device-upload`. Ha a feltöltés befejeződött, a fejlesztői készlet újraindul, és futtatja a Vázlat.

![eszköz-feltöltést](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> Előfordulhat, hogy kap egy "hiba: AZ3166: Ismeretlen csomag" hibaüzenet jelenik meg. Ez akkor fordul elő, ha a tábla csomagindexet nem megfelelően frissül. Ez a hiba elhárításához tekintse meg a [IoT DevKit gyakori kérdések a fejlesztési szakaszban](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>A projekt tesztelése

A VS Code-ban kövesse az alábbi eljárás segítségével nyissa meg, és a soros figyelő beállítása:

1. Kattintson a `COM[X]` szót tartalmaz, a megfelelő COM-port beállítása az állapotsor `STMicroelectronics`: ![com-port beállítása](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. Kattintson az állapotsorban nyissa meg a soros figyelő power beépülő ikonjára: ![soros-figyelő](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. Az állapotsoron kattintson az átviteli sebesség, beállíthatja azt a `115200`: ![set-sebesség](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

A soros figyelő a minta áttekintése által küldött összes üzenetet jeleníti meg. A vázlat a DevKit Wi-Fi kapcsolódik. Ha a Wi-Fi-kapcsolat sikeresen megtörtént, a Vázlat üzenetet küld az MQTT broker. Ezt követően a minta ismételten QoS 0 és 1. a QoS, illetve használatával két "iot.eclipse.org" üzenetet küld.

![soros-kimenet](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Ha problémákat tapasztal, tekintse meg a [IoT DevKit – gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) vagy létesítsen a következő csatornákon:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>Lásd még

* [IoT DevKit AZ3166 csatlakoztatása Azure IoT hubhoz a felhőben](iot-hub-arduino-iot-devkit-az3166-get-started.md)
* [Rázó, egy tweetet rázza meg](iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message.md)

## <a name="next-steps"></a>További lépések

Most, hogy, hogyan konfigurálhatja az MXChip Iot DevKit MQTT-ügyfélként, és az MQTT ügyféloldali kódtár használata üzeneteket küldeni egy MQTT broker, Íme a javasolt következő lépések:

* [Az Azure IoT távoli figyelési megoldásgyorsító áttekintése](https://docs.microsoft.com/azure/iot-suite/)
* [Az MXChip IoT DevKit eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
