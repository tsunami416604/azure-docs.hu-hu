---
title: Az Azure IoT Hub üzenetgazdagításainak áttekintése
description: Ez a cikk az üzenetek bővítését mutatja be, amelyek lehetővé teszik az IoT Hub számára, hogy az üzeneteket további információkkal pecsételje le, mielőtt az üzeneteket a kijelölt végpontra küldenék.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: c3dbd01faf61c164c88f09b0da03c07be4abd187
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75429122"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages"></a>Az eszközök felhőbe irányuló IoT Hub-üzenetek üzenetgazdagításai

*Az üzenetek dúsítása* az IoT Hub azon képessége, hogy az üzeneteket a kijelölt végpontra való küldés előtt további információkkal *bélyegezze* le. Az üzenetek bővítésének egyik oka az, hogy olyan adatokat is tartalmaz, amelyek egyszerűsíthetik az alsóbb rétegbeli feldolgozást. Például az eszköz telemetriai üzenetek egy eszköz ikercímke csökkentheti a terhelést az ügyfelek számára, hogy az eszköz iker API-hívások ezt az információt.

![Üzenetgazdagítási folyamat](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

Az üzenetek gazdagításának három fő eleme van:

* Dúsítás ineve vagy kulcsa

* Egy érték

* Egy vagy több [olyan végpont,](iot-hub-devguide-endpoints.md) amelyre a dúsítást alkalmazni kell.

A **kulcs** egy karakterlánc. Egy kulcs csak alfanumerikus karaktereket tartalmazhat,`-`vagy ezek`_`a speciális`.`karakterek: kötőjel ( ), aláhúzás ( ) és pont ( ).

Az **érték** a következő példák bármelyike lehet:

* Bármilyen statikus karakterlánc. Dinamikus értékek, például feltételek, logika, műveletek és függvények nem engedélyezettek. Ha például több ügyfél által használt SaaS-alkalmazást fejleszt, minden ügyfélhez hozzárendelhet egy azonosítót, és elérhetővé teheti az azonosítót az alkalmazásban. Az alkalmazás futtatásakor az IoT Hub lebélyegzi az eszköz telemetriai üzeneteket az ügyfél azonosítójával, lehetővé téve az üzenetek eltérő feldolgozását az egyes ügyfelek számára.

* Az üzenetet küldő IoT-központ neve. Ez az érték *$iothubname*.

* Az ikereszközadatai, például az elérési útja. Ilyen például *a $twin.tags.field* és *a $twin.tags.latitude*.

   > [!NOTE]
   > Jelenleg csak $iothubname, $twin.tags, $twin.properties.desired és $twin.properties.reported támogatott változók az üzenetek bővítéséhez.

Az üzenetbővítések alkalmazástulajdonságokként kerülnek hozzáadásra a kiválasztott végpont(ok)ba küldött üzenetekhez.  

## <a name="applying-enrichments"></a>Dúsítások alkalmazása

Az üzenetek az [IoT Hub üzenet-útválasztása](iot-hub-devguide-messages-d2c.md)által támogatott bármely adatforrásból származhatnak, beleértve a következő példákat:

* készülék telemetriai adatai, például hőmérséklet vagy nyomás
* eszköz iker változásértesítések - változások az eszköz iker
* életciklus-eseményeit, például amikor az eszközt létrehozták vagy törölték,

Az IoT Hub beépített végpontjára irányuló üzenetekhez dúsításokat adhat hozzá, vagy olyan üzeneteket, amelyek egyéni végpontokra, például az Azure Blob storage-ba, a Service Bus-várólistába vagy a Service Bus-témakörbe kerülnek.

Az Eseményrácsban közzétett üzenetekhez az Eseményrácson közzétett műveletekhez a végpont eseményrácsként való kiválasztásával adhat hozzá dúsításokat. Az Event Grid-előfizetés alapján létrehozunk egy alapértelmezett útvonalat az IoT Hubban az eszköztelemetriához. Ez az egyetlen útvonal képes kezelni az összes Event Grid-előfizetést. Az eseményrács végpontjának dúsításait konfigurálhatja, miután létrehozta az eseményrács-előfizetést az eszköz telemetriai adataihoz. További információ: [Iot Hub and Event Grid](iot-hub-event-grid.md).

A dúsítások végpontonként kerülnek alkalmazásra. Ha öt dúsítást ad meg egy adott végponthoz, akkor a végpontra irányuló összes üzenet ugyanazzal az öt dúsítással lesz lepecsételve.

A dúsítások a következő módszerekkel konfigurálhatók:

| **Módszer** | **Parancs** |
| ----- | -----| 
| Portál | [Azure-portál](https://portal.azure.com) | Tekintse meg az [üzenetgazdagítások oktatóanyagát](tutorial-message-enrichments.md) | 
| Azure CLI   | [az iot hub üzenetbővítés](https://docs.microsoft.com/cli/azure/iot/hub/message-enrichment?view=azure-cli-latest) |
| Azure PowerShell | [Add-AzIotHubMessageEnrichment](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubmessageenrichment?view=azps-2.8.0) |

Az üzenetek bővítésének hozzáadása nem növeli a késést az üzenetútválasztáshoz.

Az üzenetek bővítésének kipróbálásához tekintse meg az [üzenetgazdagítások című oktatóanyagot](tutorial-message-enrichments.md)

## <a name="limitations"></a>Korlátozások

* Az IoT Hubonként legfeljebb 10 dúsítást adhat hozzá a standard vagy alapszintű szolgáltatásokhoz. Az ingyenes szinten az IoT Hubs legfeljebb 2 dúsítást adhat hozzá.

* Bizonyos esetekben, ha egy dúsítást alkalmaz egy címkére vagy tulajdonságra beállított értékkel az ikereszközben, az érték karakterlánc-értékként lesz lepecsételve. Ha például egy dúsítási érték $twin.tags.field értékre van állítva, az üzenetek et a "$twin.tags.field" karakterlánc cal lesz lebélyegezve, nem pedig az ikermező értékét. Ez a következő esetekben fordul elő:

   * Az IoT Hub az alapszintű. Az alapvető szintű IoT-központok nem támogatják az eszköz twins.

   * Az IoT Hub a standard szinten, de az eszköz az üzenetet küldő nem rendelkezik ikereszköz.

   * Az IoT Hub a standard szinten, de az eszköz két elérési út a dúsítás értéke nem létezik. Ha például a dúsítási érték $twin.tags.location értékre van állítva, és az ikereszköz nek nincs helytulajdonsága a címkék alatt, az üzenet a "$twin.tags.location" karakterlánccal lesz lepecsételve. 

* Az ikereszköz frissítései akár öt percet is igénybe vehetnek, hogy megjelenjenek a megfelelő dúsítási értékben.

* Az üzenetek teljes mérete, beleértve a dúsításokat is, nem haladhatja meg a 256 KB-ot. Ha egy üzenet mérete meghaladja a 256 KB-ot, az IoT Hub eldobja az üzenetet. [Az IoT Hub-metrikák](iot-hub-metrics.md) segítségével azonosíthatja és debug hibák, amikor az üzenetek eldobása. Figyelheti például a d2c.telemetry.egress.invalid.For example, d2c.telemetry.egress.invalid.

* Az üzenetek gazdagítása nem vonatkozik a digitális ikermódosítási eseményekre (az [IoT Plug and Play nyilvános előnézetének](../iot-pnp/overview-iot-plug-and-play.md)része).

## <a name="pricing"></a>Díjszabás

Az üzenetek további díjmentesen elérhetők. Jelenleg akkor kell fizetnie, amikor üzenetet küld egy IoT Hub. Az üzenetért csak egyszer kell fizetnie, még akkor is, ha az üzenet több végpontra kerül.

## <a name="next-steps"></a>További lépések

Tekintse meg ezeket a cikkeket az üzenetek IoT Hubba történő útválasztásáról:

* [Az üzenetek gazdagításának oktatóanyaga](tutorial-message-enrichments.md)

* [Az IoT Hub üzenet-útválasztásának használata eszközről felhőbe irányuló üzenetek küldésére különböző végpontokra](iot-hub-devguide-messages-d2c.md)

* [Oktatóanyag: IoT Hub útválasztás](tutorial-routing.md)
