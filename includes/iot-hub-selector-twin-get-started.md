---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: eef073f5f4d1eb39fd5ccd8dafacd7074158fa37
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667841"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

Az ikereszközök JSON-dokumentumok, amelyek az eszközök állapotinformációit (metaadatokat, konfigurációkat és állapotokat) tárolják. A IoT Hub minden olyan eszközön megtartja a különálló eszközt, amely csatlakozik hozzá.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Az eszközökhöz tartozó ikrek használata:

* Tárolja az eszköz metaadatait a megoldás hátterében.

* A jelenlegi állapotadatok, például a rendelkezésre álló képességek és feltételek (például a használt kapcsolati módszer) jelentése az eszköz alkalmazásában.

* Szinkronizálhatja a hosszan futó munkafolyamatok (például a belső vezérlőprogram és a konfigurációs frissítések) állapotát egy eszköz-alkalmazás és egy háttérbeli alkalmazás között.

* Az eszköz metaadatait, konfigurációját vagy állapotát kérdezheti le.

Az eszközökhöz készült ikrek szinkronizálásra és az eszközök konfigurációjának és feltételeit kérdezik le. További információ az eszköz-ikrek használatáról: az [eszközök megismerése](../articles/iot-hub/iot-hub-devguide-device-twins.md).

Az eszközök ikrek egy IoT-központban tárolódnak, és a következőket tartalmazzák:

* *címkék*, az eszköz metaadatai csak a megoldás hátterében érhetők el;

* a *kívánt tulajdonságok*, JSON-objektumok a megoldás háttérbeli és az eszköz alkalmazás által Megfigyelhető módon módosíthatók. és

* *jelentett tulajdonságok*, a JSON-objektumok az eszköz alkalmazás által módosítható, és a megoldás hátterében olvashatók. A címkék és a tulajdonságok nem tartalmazhatnak tömböket, az objektumok pedig ágyazhatók egymásba.

![Eszköz különálló képe, amely funkcionalitást mutat](./media/iot-hub-selector-twin-get-started/twin.png)

Emellett a megoldás háttérbe állítása az eszközökhöz tartozó ikreket is lekérdezheti az összes fenti adattal függően.
További információ [](../articles/iot-hub/iot-hub-devguide-device-twins.md) az eszközökről az ikrekről, valamint a lekérdezési [IoT hub lekérdezési nyelvre](../articles/iot-hub/iot-hub-devguide-query-language.md) való hivatkozással kapcsolatban.


Ez az oktatóanyag a következőket mutatja be:

* Hozzon létre egy háttérbeli alkalmazást, amely *címkéket* tesz a Twin eszközhöz, valamint egy szimulált eszköz alkalmazást, amely a kapcsolati csatornát *jelentett tulajdonságként* jelenti a Twin eszközön.

* A korábban létrehozott címkék és tulajdonságok szűrőinek használatával lekérdezheti a háttérbeli alkalmazásból származó eszközöket.
