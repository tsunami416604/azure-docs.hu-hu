---
ms.openlocfilehash: 19331f35ea2fa773325ec61e728677e37767ab54
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60346214"
---
> [!div class="op_single_selector"]
> * [Eszköz: Node.js Service: Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [Eszköz: C# Service: C#](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Eszköz: Java Service: Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Eszköz: Python-szolgáltatás: Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

Háttér-alkalmazások használhatják az Azure IoT Hub primitívek, mint például [ikereszköz] [ lnk-devtwin] és [közvetlen metódusok][lnk-c2dmethod], a távolról indítása és monitorozása az eszköz felügyeleti műveleteket az eszközön. Ez az oktatóanyag bemutatja, hogyan egy háttér-alkalmazást és a egy eszközalkalmazás együttműködése hogyan kezdeményezésére és figyelésére a távoli eszköz-újraindítás az IoT Hub használatával.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]
Közvetlen metódus használatával (például újraindítás, gyári beállítások visszaállítása és belső vezérlőprogram frissítése) felügyeleti eszközműveletek kezdeményez egy háttér-alkalmazást a felhőben. Az eszköz felelős:

* Az IoT hubról küldött metódus kérelem kezelése.
* Kezdeményezése a megfelelő eszközre vonatkozó műveletet az eszközön.
* Ügyfélállapot-frissítés keresztül biztosít *jelentett tulajdonságokként* az IoT hubnak.

A háttéralkalmazás használhatja a felhőben az eszköz felügyeleti műveletek állapotának jelentésére ikereszköz-lekérdezések futtatásához.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
