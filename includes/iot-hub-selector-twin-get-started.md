---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.author: dobett
ms.openlocfilehash: 304637422c0b8fd4dfa99e2e434e13d12f1fb342
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164371"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

Az ikereszközök JSON-dokumentumok, amelyek az eszközök állapotinformációit (metaadatokat, konfigurációkat és állapotokat) tárolják. Az IoT Hub továbbra is fennáll, az eszközök ikereszköze a minden eszközön, amelyhez kapcsolódik hozzá.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Az ikereszközök használata:

* Store az eszköz metaadatait a megoldás háttérrendszerének.

* Például az elérhető funkciókat és feltételeket (például a kapcsolat használt módszer) aktuális állapotadatokat jelentést az eszköz alkalmazásból.

* Hosszan futó munkafolyamatokat (például a belső vezérlőprogram és konfigurációja frissítések) között egy eszközalkalmazás és a egy háttér-alkalmazás állapotának szinkronizálása.

* Az eszközök metaadatait, konfigurációs vagy állapot lekérdezése.

Ikereszközök úgy tervezték, a szinkronizáláshoz és eszközkonfigurációkat és feltételek lekérdezéséhez. További információ az ikereszközök használata található [Ikereszközök](../articles/iot-hub/iot-hub-devguide-device-twins.md).

Ikereszközök tárolják az IoT hub és kell tartalmaznia:

* *a címkék*, amelyhez csak a megoldás háttérrendszere; eszköz metaadatait

* *kívánt tulajdonságok*, JSON-objektumok módosítható a megoldás háttérrendszere és több rendszernek megfigyelhetőnek az eszköz alkalmazás; és

* *jelentett tulajdonságokként*, JSON-objektumok az eszközalkalmazások által módosítható, és a megoldás háttérrendszere által is olvasható. Címkék és tulajdonságok tömbök nem tartalmazhat, de objektumok ágyazhatók be.

![Eszköz ikereszköz kép megjelenítése funkció](./media/iot-hub-selector-twin-get-started/twin.png)

Ezenkívül a megoldás háttérrendszere lekérdezheti az ikereszközök a fenti adatok alapján.
Tekintse meg [Ikereszközök](../articles/iot-hub/iot-hub-devguide-device-twins.md) ikereszközök, és a további információt a [IoT Hub lekérdezési nyelv](../articles/iot-hub/iot-hub-devguide-query-language.md) lekérdezése referenciája.


Ez az oktatóanyag a következőket mutatja be:

* Hozzon létre egy háttér-alkalmazást, amely hozzáadja *címkék* az ikereszközök és a egy szimulált eszközalkalmazás által jelentett, a kapcsolat csatorna egy *jelentett tulajdonság* az ikereszközben.

* A lekérdezés a szűrők használata a címkék és a korábban létrehozott tulajdonságok a háttéralkalmazás-eszközök.
