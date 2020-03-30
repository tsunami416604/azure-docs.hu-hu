---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 08/20/2019
ms.openlocfilehash: c412f7f3e20e4d04083e457bfb245b850b65e126
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "70050398"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

Az ikereszközök JSON-dokumentumok, amelyek az eszközök állapotinformációit, például a metaadatokat, konfigurációkat és állapotokat tárolják. Az IoT Hub minden olyan eszközhöz, amely csatlakozik, egy ikereszköz marad.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Az eszközikrek használata:

* Az eszköz metaadatait a megoldás háttérrendszeréből tárolhatja.

* Jelentse az aktuális állapotinformációkat, például a rendelkezésre álló képességeket és feltételeket, például az eszközalkalmazásból használt kapcsolódási módszert.

* Szinkronizálja a hosszú ideig futó munkafolyamatok, például a belső vezérlőprogram és a konfigurációs frissítések állapotát egy eszközalkalmazás és egy háttéralkalmazás között.

* Az eszköz metaadatainak, konfigurációjának vagy állapotának lekérdezése.

Az eszközikrek szinkronizálásra, valamint az eszközkonfigurációk és -feltételek lekérdezésére szolgálnak. További információ arról, hogy mikor kell használni eszköz ikrek megtalálható [Érti eszköz twins](../articles/iot-hub/iot-hub-devguide-device-twins.md).

Az ikereszközök egy IoT-hubban tárolódnak, és a következő elemeket tartalmazzák:

* **Címkék**. Az eszköz metaadatai csak a megoldás háttérrendszerén keresztül érhetők el.

* **A kívánt tulajdonságokat.** JSON objektumok módosítható a megoldás háttér-, és megfigyelhető az eszköz alkalmazás.

* **Jelentett tulajdonságok**. JSON objektumok módosítható az eszköz alkalmazás és olvasható a megoldás háttér.

A címkék és tulajdonságok nem tartalmazhatnak tömböket, de az objektumok egymásba ágyazhatók.

A következő ábra az ikereszköz-szervezetet mutatja be:

![Az ikereszköz képe a funkciókat mutatja](./media/iot-hub-selector-twin-get-started/twin.png)

Emellett a megoldás háttérrendszerlekérdezheti az eszközök twins a fenti adatok alapján.
Az ikereszközökről további információt az [Eszköztwins ek megértése című](../articles/iot-hub/iot-hub-devguide-device-twins.md)témakörben talál. A lekérdezésről az [IoT Hub lekérdezési nyelve](../articles/iot-hub/iot-hub-devguide-query-language.md)című témakörben talál további információt.


Ez az oktatóanyag a következőket mutatja be:

* Hozzon létre egy háttéralkalmazás, amely címkéket ad hozzá egy ikereszközhöz, és egy szimulált eszközalkalmazást, amely a kapcsolódási csatornáját jelentett tulajdonságként jelenti az ikereszközön.

* Eszközök lekérdezése a háttéralkalmazásból a korábban létrehozott címkék és tulajdonságok szűrőivel.
