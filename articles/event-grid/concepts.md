---
title: Azure Event Grid fogalmak
description: Az Azure Event Gridet és a vele kapcsolatos fogalmakat ismerteti. A Event Grid számos kulcsfontosságú összetevőjét határozza meg.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: spelluru
ms.openlocfilehash: 0821c749a6cb718e1b8abb74a2925bc041850eaf
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359438"
---
# <a name="concepts-in-azure-event-grid"></a>Fogalmak a Azure Event Grid

Ez a cikk a Azure Event Grid főbb fogalmait ismerteti.

## <a name="events"></a>Események

Az esemény az a legkisebb mennyiségű információ, amely teljes mértékben leírja a rendszeren történteket. Minden eseményhez olyan általános információk tartoznak, mint például: az esemény forrása, az esemény időpontja és egyedi azonosító. Minden eseményhez konkrét információ is tartozik, amely csak az adott típusú eseményre vonatkozik. Például az Azure Storage-ban létrehozott új fájlra vonatkozó esemény a fájl részleteit, például a `lastTimeModified` értéket. Vagy egy Event Hubs esemény rendelkezik a rögzítési fájl URL-címével. 

A 64 KB-ig terjedő méretű események általánosan elérhetők (GA) szolgáltatói szerződés (SLA). A legfeljebb 1 MB méretű esemény támogatása jelenleg előzetes verzióban érhető el. Az 64 KB-nál nagyobb számú esemény díja 64 KB. 


Az eseményekben eljuttatott tulajdonságokért lásd: [Azure Event Grid Event Schema](event-schema.md).

## <a name="publishers"></a>Kiadók

A közzétevő az a felhasználó vagy szervezet, aki úgy dönt, hogy elküldi az eseményeket Event Gridba. A Microsoft számos Azure-szolgáltatáshoz tesz közzé eseményeket. Az eseményeket a saját alkalmazásból teheti közzé. Azok a szervezetek, amelyek az Azure-on kívüli szolgáltatásokat üzemeltetnek, Event Gridon keresztül tehetik közzé az eseményeket.

## <a name="event-sources"></a>Eseményforrások

Az esemény forrása az esemény. Minden eseményforrás egy vagy több eseménytípus kapcsolódik. Az Azure Storage például a blob által létrehozott események eseményforrás. A IoT Hub az eszköz által létrehozott események eseményforrás. Az alkalmazás a definiált egyéni események eseményforrás. Az esemény-források felelősek az események Event Grid való elküldéséhez.

A támogatott Event Grid források megvalósításával kapcsolatos további információkért lásd: [eseményforrás a Azure Event Grid](event-sources.md).

## <a name="topics"></a>Témakörök

Az Event Grid-témakör olyan végpontot biztosít, amelyben a forrás eseményeket küld. A közzétevő létrehozza az Event Grid-témakört, és eldönti, hogy egy adott eseményforrás egy vagy több témakörre van-e szüksége. A témakörök a kapcsolódó események gyűjteményéhez használatosak. Bizonyos típusú események megválaszolásához az előfizetők eldönthetik, hogy mely témakörökre kell előfizetni.

A rendszertémakörök az Azure-szolgáltatások által biztosított beépített témakörök. Nem látja a rendszertémaköröket az Azure-előfizetésében, mert a kiadó a témakörök tulajdonosa, de feliratkozhat rájuk. Az előfizetéshez meg kell adnia egy információt arról az erőforrásról, amelyről eseményeket szeretne kapni. Ha hozzáfér az erőforráshoz, előfizethet az eseményeire.

Az egyéni témakörök az alkalmazások és a harmadik féltől származó témakörök. Ha egyéni témakörhöz hoz létre vagy rendel hozzá hozzáférést, az előfizetésében szereplő egyéni témakör jelenik meg.

Az alkalmazás tervezésekor rugalmasságot biztosít, amikor eldönti, hány témakört kell létrehoznia. Nagyméretű megoldások esetében hozzon létre egy egyéni témakört a kapcsolódó események egyes kategóriáira. Vegyünk például egy olyan alkalmazást, amely a felhasználói fiókok és a feldolgozási rendelések módosításával kapcsolatos eseményeket küld. Nem valószínű, hogy az eseménykezelő mindkét típusú eseményt szeretné használni. Hozzon létre két egyéni témakört, és engedje, hogy az eseménykezelők előfizessenek az őket érdeklő személyre. Kis megoldások esetében érdemes lehet az összes eseményt egyetlen témakörbe elküldeni. Az esemény-előfizetők szűrhetik a kívánt típusú eseményeket.

## <a name="event-subscriptions"></a>Esemény-előfizetések

Az előfizetés azt mutatja be, Event Grid hogy mely események érdeklik egy adott témakörben. Az előfizetés létrehozásakor meg kell adnia egy végpontot az esemény kezelésére. A végpontnak elküldett eseményeket szűrheti. A szűrést eseménytípus szerint vagy a tárgy mintája alapján végezheti el. További információ: [Event Grid előfizetés sémája](subscription-creation-schema.md).

Az előfizetések létrehozására példákat a következő témakörben talál:

* [Azure CLI-minták a Event Gridhoz](cli-samples.md)
* [Azure PowerShell minták a Event Grid](powershell-samples.md)
* [Event Grid Azure Resource Manager sablonjai](template-samples.md)

További információ az aktuális Event Grid-előfizetések beszerzéséről: [Event Grid-előfizetések lekérdezése](query-event-subscriptions.md).

## <a name="event-subscription-expiration"></a>Esemény-előfizetés lejárata
Az esemény-előfizetés a megadott dátumot követően automatikusan lejár. Olyan esemény-előfizetések elévülésének beállítása, amelyek csak korlátozott ideig szükségesek, és nem szeretne aggódni az előfizetések tisztítása során. Ha például esemény-előfizetést hoz létre egy forgatókönyv teszteléséhez, érdemes lehet lejáratot beállítani. 

A lejárati idő beállítására példa: [előfizetés speciális szűrőkkel](how-to-filter-events.md#subscribe-with-advanced-filters).

## <a name="event-handlers"></a>Eseménykezelők

Egy Event Grid perspektívából az eseménykezelő az a hely, ahol az eseményt elküldjék. A kezelő további műveletet hajt végre az esemény feldolgozásához. Event Grid számos kezelő típust támogat. A kezelőként támogatott Azure-szolgáltatást vagy saját webhookot is használhat. A kezelő típusától függően a Event Grid különböző mechanizmusokat követ az esemény kézbesítésének garantálása érdekében. A HTTP webhook-eseménykezelők esetében az eseményt a rendszer újra megkísérli, amíg a kezelő `200 – OK`állapotkódot ad vissza. Az Azure Storage-üzenetsor esetében az eseményeket a rendszer újra megkísérli, amíg a Queue szolgáltatás sikeresen feldolgozza az üzenetet a várólistába.

A támogatott Event Grid kezelők megvalósításával kapcsolatos további információkért lásd: [eseménykezelők a Azure Event Gridban](event-handlers.md).

## <a name="security"></a>Biztonság

Event Grid biztosít biztonságot a témakörökre való feliratkozáshoz és a témakörök közzétételéhez. Feliratkozáskor megfelelő engedélyekkel kell rendelkeznie az erőforrás vagy az Event Grid témakörben. Közzétételkor SAS-jogkivonattal vagy kulcs-hitelesítéssel kell rendelkeznie a témakörhöz. További információ: [Event Grid biztonság és hitelesítés](security-authentication.md).

## <a name="event-delivery"></a>Esemény kézbesítése

Ha Event Grid nem tudja megerősíteni, hogy az előfizető végpontja megkapta-e az eseményt, az visszaadja az eseményt. További információ: [Event Grid üzenet kézbesítése, és próbálkozzon újra](delivery-and-retry.md).

## <a name="batching"></a>Kötegelés

Egyéni témakör használatakor az eseményeket mindig közzé kell tenni egy tömbben. Ez lehet egy köteg az alacsony átviteli sebességű forgatókönyvek esetében, azonban a nagy mennyiségű használati esetek esetében ajánlott egyszerre több eseményt felvenni egy közzétételsel, hogy nagyobb hatékonyságot érjenek el. A kötegek legfeljebb 1 MB méretűek lehetnek. Minden eseménynek még nem szabad nagyobbnak lennie, mint 64 KB (általánosan elérhető) vagy 1 MB (előzetes verzió).

> [!NOTE]
> A 64 KB-ig terjedő méretű események általánosan elérhetők (GA) szolgáltatói szerződés (SLA). A legfeljebb 1 MB méretű esemény támogatása jelenleg előzetes verzióban érhető el. Az 64 KB-nál nagyobb események díját 64 KB-os növekményekben számoljuk el. 

## <a name="next-steps"></a>Következő lépések

* Az Event Grid megismeréséhez tekintse meg [az Event Grid bevezetőjét](overview.md).
* Az Event Grid használatának gyors megkezdéséhez tekintse meg [az egyéni események létrehozása és irányítása Azure Event Grid](custom-event-quickstart.md)használatával című témakört.
