---
title: Azure IoT Hub üzenet végrehajtott információbeolvasás áttekintése
description: Üzenet végrehajtott információbeolvasás az Azure IoT Hub-üzenetek áttekintése
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 6ddfd90956fde0c57e14083a3704cbf365456c75
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735163"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages-preview"></a>Üzenet végrehajtott információbeolvasás az eszköz a felhőbe irányuló IoT Hub-üzenetek (előzetes verzió)

*Üzenet végrehajtott információbeolvasás* rendszer azon képessége, az IoT Hub *stamp* az üzeneteket a kijelölt végponthoz való elküldése előtt további információkat tartalmazó üzenetek. Egy üzenet végrehajtott információbeolvasás használandó oka tartalmazza, amelyek segítségével egyszerűsítheti az alsóbb feldolgozási adatokat. Például eszköz telemetriai üzeneteket, egy eszköz ikereszköz bővítését csökkentheti ügyfeleik számára, hogy az API-khoz ezt az információt ikereszköz terhelését.

![Üzenet végrehajtott információbeolvasás folyamat](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

Egy üzenet Adatbővítés van három fontos elemét:

* Adatbővítés nevét vagy kulcs

* Egy érték

* Egy vagy több [végpontok](iot-hub-devguide-endpoints.md) számára a felderítési bővítést meg kell alkalmazni.

A kulcs bármilyen karakterlánc lehet.

Az érték a következő példák egyike lehet:

* Bármely statikus karakterlánc. Például a feltételeket, logikai, műveleteket és függvények a dinamikus értékek nem engedélyezettek. Például ha egy ügyfél által használt SaaS-alkalmazás fejlesztése, rendelje hozzá egy azonosítót minden ügyfél szabadon adott azonosítója, az alkalmazás elérhetővé tétele. Az alkalmazás futtatásakor az IoT Hub fog időbélyegző az eszköz telemetriai üzeneteket az ügyfél-azonosítóval, lehetővé téve az egyes vásárlók eltérően a-üzenetek feldolgozása.

* Információk az ikereszközről, például a elérési útvonalát. Példák lenne *$twin.tags.field* és *$twin.tags.latitude*.

* Az IoT hub, az üzenet elküldésekor neve. Ez az érték *$iothubname*.

## <a name="applying-enrichments"></a>Végrehajtott információbeolvasás alkalmazása

Az üzenetek által támogatott bármely adatforrásból származhatnak [IoT Hub üzenet-útválasztása](iot-hub-devguide-messages-d2c.md), többek között az alábbi példák:

* eszköz telemetriai adatokat, például a hőmérséklet és nyomás
* eszköz ikereszköz-változási értesítések – módosítások az ikereszköz
* eszköz életciklus-események, például amikor az eszköz létrehozása vagy törlése

Végrehajtott információbeolvasás adhat hozzá, amelyek a beépített végpont az IoT Hub-üzenetek, vagy üzeneteket, amelyek az egyedi végpontok például az Azure Blob storage, Service Bus-üzenetsorban vagy egy Service Bus-témakörbe történő továbbítása.

Végrehajtott információbeolvasás is üzeneteket, amelyek közzététele folyamatban van az Event Gridbe Event Grid, a végpont kiválasztásával adhat hozzá. További információkért lásd: [az Iot Hub és az Event Grid](iot-hub-event-grid.md).

Végrehajtott információbeolvasás végpontonként érvényesek. Ha megad egy adott végpont le kell öt végrehajtott információbeolvasás, az azonos öt végrehajtott információbeolvasás fogja, hogy a végpont összes üzenet vannak megjelölve.

Hogyan próbálhatja ki üzenet végrehajtott információbeolvasás, olvassa el a [üzenet végrehajtott információbeolvasás oktatóanyag](tutorial-message-enrichments.md)

## <a name="limitations"></a>Korlátozások

* Ezeket a hub az IoT Hub egy legfeljebb 10 végrehajtott információbeolvasás standard vagy alapszintűre szinten is hozzáadhat. Az IoT hubok az ingyenes szinten legfeljebb 2 végrehajtott információbeolvasás is hozzáadhat.

* Bizonyos esetekben-felderítési bővítést alkalmazzák a a érték, egy címke vagy tulajdonság az ikereszközön, ha az érték lesz kell megjelölve karakterláncként. Például-felderítési bővítést értéke $twin.tags.field, ha az üzeneteket fogja kell megjelölve a karakterlánc "$twin.tags.field" ahelyett, hogy a mező értékét az ikereszköz az. Ez akkor fordul elő a következő esetekben:

   * Az IoT Hub az alapszintű csomag szerepel. Alapszintű IoT-központok nem támogatja az ikereszközök.

   * Az IoT Hub a standard szintű, de nem az ikereszközben van az eszközön, az üzenet elküldésekor.

   * Az IoT Hub standard szintű csomagjában, de az érték a felderítési bővítést az eszköz ikereszköz elérési út nem létezik. Például ha a felderítési bővítést értéke $twin.tags.location, és az ikereszköz nem rendelkezik a location tulajdonság címkék alapján, az üzenet van megjelölve a "$twin.tags.location" karakterlánc. 

* Az ikereszközök frissítései megjelennek a megfelelő felderítési bővítést érték akár öt percet is igénybe vehet.

* A teljes üzenetmérete, beleértve a végrehajtott információbeolvasás nem lehet hosszabb 256 KB. Ha egy üzenet mérete meghaladja a 256 KB-os, az IoT Hub eldobja az üzenetet. Használhat [az IoT Hub-metrikák](iot-hub-metrics.md) alapján azonosíthatja és elháríthatja a hibákat, amikor az üzeneteket a rendszer elveti. Ha például d2c.telemetry.egress.invalid követheti nyomon.

## <a name="pricing"></a>Díjszabás

Üzenet végrehajtott információbeolvasás díjmentesen érhetők el. Díjkötelesek jelenleg, amikor egy IoT hubra egy üzenetet küld. Csak díjkötelesek egyszer, az üzenet akkor is, ha az üzenet több végpont kerül.

## <a name="availability"></a>Rendelkezésre állás

Ez a funkció előzetes verzióban érhető el, és érhető el minden régióban, kivéve az USA keleti RÉGIÓJA, USA nyugati RÉGIÓJA, Nyugat-Európa, [Azure Government](/azure/azure-government/documentation-government-welcome), [Azure China 21Vianet](/azure/china/china-welcome), és [Azure Germany](https://azure.microsoft.com/global-infrastructure/germany/).

## <a name="next-steps"></a>További lépések

Tekintse meg az alábbi cikkek útválasztási üzeneteket egy IoT hubra további információ:

* [Használja az IoT Hub üzenet-útválasztása eszköz – felhő üzeneteket küldeni a különböző végpontok](iot-hub-devguide-messages-d2c.md)

* [Oktatóanyag: Az IoT Hub-Útválasztás](tutorial-routing.md)