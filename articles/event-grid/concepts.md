---
title: Az Azure Event Griddel kapcsolatos fogalmak
description: Az Azure Event Gridet és a vele kapcsolatos fogalmakat ismerteti. Event Grid számos főbb összetevőit határozza meg.
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: tomfitz
ms.openlocfilehash: 89f0f5847f157cff59a57f7958508e4f260355c3
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50747558"
---
# <a name="concepts-in-azure-event-grid"></a>Az Azure Event Griddel kapcsolatos fogalmak

Ez a cikk az Azure Event Grid főbb fogalmakat ismerteti.

## <a name="events"></a>Események

Az esemény leíró adatokkal teljes hiba történt a rendszer a legkisebb mennyisége jelenti. Minden esemény rendelkezik közös információkat, például: az esemény forrása idő az esemény tartott a hely és az egyedi azonosítója. Minden esemény is rendelkezik, amely csak az adott típusú eseményre vonatkozó információkat. Például az Azure Storage-ban létrehozott új fájlt rendezvényére részletesen a fájlról, például a `lastTimeModified` értéket. Vagy az Event Hubs esemény rendelkezik a rögzítési fájl URL-CÍMÉT. 

Minden esemény legfeljebb 64 KB méretű adatot.

A tulajdonságok, amelyek egy eseményt küld, lásd: [Azure Event Grid-esemény séma](event-schema.md).

## <a name="publishers"></a>Kiadók

A közzétevő, a felhasználó vagy a szervezet, amely úgy dönt, hogy az események küldése az Event Gridbe. A Microsoft számos Azure-szolgáltatások eseményeket tesz közzé. Események teheti közzé a saját-alkalmazás. Azure-on kívül szolgáltatásokat üzemeltető szervezetek tehetik közzé az eseményeket Event Griden keresztül.

## <a name="event-sources"></a>Eseményforrások

Eseményforrás, ahol az esemény történik. Minden egyes eseményforrás kapcsolódik egy vagy több esemény típusa. Például az Azure Storage az eseményforrás a létrehozott események blob. Az IoT Hub az eseményforrás a létrehozott események eszköz. Az alkalmazás az Ön által meghatározott egyéni események az esemény forrását. Események küldése az Event Grid eseményforrások felelőssége.

A támogatott Event Grid forrásokban megvalósításával kapcsolatos további információkért lásd: [eseményforrások az Azure Event Grid](event-sources.md).

## <a name="topics"></a>Témakörök

Az event grid-témakör tartalmaz egy végpontot, ahol a forrás küldi az eseményeket. A közzétevő az event grid-témakör hoz létre, és úgy dönt, hogy eseményforrás kell-e egynél több vagy egy témakört. A témakör a kapcsolódó események gyűjteménye szolgál. Bizonyos típusú eseményekről válaszolni előfizetők döntse el, melyik témakörök előfizetni.

Rendszer témakörök Azure-szolgáltatások által biztosított beépített témakörök szolgálnak. Rendszer témakörök nem jelenik meg az Azure-előfizetésben, mert a közzétevő a témakörök tulajdonában van, de feliratkozhat rájuk. Előfizetés, adja meg az erőforrással kapcsolatos információkat szeretne kapni az eseményeket. Mindaddig, amíg az erőforrás hozzáférése, előfizethet az eseményeket.

Egyéni témakörök olyan alkalmazás és a külső témaköröket. Amikor hoz létre, vagy egy egyéni témakörre hozzáféréssel rendelkező, láthatja, hogy egyéni témakör az előfizetésében.

Az alkalmazás tervezésekor rugalmasságot hogy hány témakörök létrehozása során. Nagy megoldásokhoz kapcsolódó események kategóriákhoz tartozó egyéni témakör létrehozása. Például vegyünk egy alkalmazást, amely módosítja a felhasználói fiókok és a rendelések feldolgozása kapcsolódó eseményeket. Nem valószínű, bármely eseménykezelő szeretne mindkét események kategóriáját. Két egyéni témakör létrehozása, és lehetővé teszik az őket érdeklő egy előfizetés eseménykezelők. Kis megoldások inkább minden eseményt küldeni egy témakör. Az eseménytípusok, szeretnének előfizetőket is szűrheti.

## <a name="event-subscriptions"></a>Esemény-előfizetések

Előfizetés közli az Event Grid mely eseményeket a témakör az Önt érdeklő fogadását. Az előfizetés létrehozásakor adja meg a végpont az esemény kezelésére. Szűrheti a végpontnak küldött eseményeket. Esemény típusa, vagy a tulajdonos minta szerint szűrheti. További információkért lásd: [Event Grid-előfizetés séma](subscription-creation-schema.md).

Az előfizetés-létrehozási példákért lásd:

* [Az Event Griddel kapcsolatos Azure CLI-minták](cli-samples.md)
* [Az Event Griddel kapcsolatos Azure PowerShell-minták](powershell-samples.md)
* [Az Azure Resource Manager-sablonok az Event Gridhez](template-samples.md)

További információ az aktuális event grid-előfizetések első: [lekérdezés Event Grid-előfizetések](query-event-subscriptions.md).

## <a name="event-subscription-expiration"></a>Esemény-előfizetés lejárta

A [Event Grid-bővítmény](/cli/azure/azure-cli-extensions-list) az Azure parancssori felület lehetővé teszi, hogy állítsa be a lejárati dátum, amikor egy esemény-előfizetés létrehozása. Ha a REST API-t használ, használja a `api-version=2018-09-15-preview`

Az esemény-előfizetés ezt követően automatikusan lejárt. Beállíthatja egy esemény-előfizetésekhez, amely csak korlátozott ideig szükséges lejárati idejét, és nem kívánja ezen előfizetések törlése foglalkoznia. Például amikor a forgatókönyv teszteléséhez egy esemény-előfizetést hoz létre, érdemes beállíthatja egy lejárati idejét. 

Egy példa egy lejárati beállítása: [előfizetés a speciális szűrők](how-to-filter-events.md#subscribe-with-advanced-filters).

## <a name="event-handlers"></a>Eseménykezelők

Event Grid szempontból egy eseménykezelő a hely, ahol a rendszer. A kezelő néhány további művelet feldolgozni az eseményt vesz igénybe. Event Grid számos kezelő különböző támogatja. Használhatja a kezelő egy támogatott Azure-szolgáltatás vagy a saját webhook. Kezelő típusától függően az Event Grid követi az események kézbesítésének garantálása érdekében használható különböző mechanizmusokat. A HTTP-webhook eseménykezelőket, az esemény mindaddig, amíg a kezelő állapotkódot adja vissza újrapróbált `200 – OK`. Az Azure Storage-üzenetsor az események megismétlődnek mindaddig, amíg a Queue szolgáltatás sikeresen feldolgozza az üzenetet leküldéses az üzenetsorba.

A támogatott Event Grid-kezelők bármelyikét megvalósításával kapcsolatos további információkért lásd: [eseménykezelők az Azure Event Grid](event-handlers.md).

## <a name="security"></a>Biztonság

Event Grid való előfizetéssel kapcsolatos témakörök, és a témakörök közzététele biztonságot nyújt. Amikor feliratkozik, az erőforrás- vagy event grid-témakör az megfelelő engedélyekkel kell rendelkeznie. A közzétételkor rendelkeznie kell egy SAS-token vagy a témakör kulcsos hitelesítés. További információkért lásd: [Event Grid biztonsági és hitelesítési](security-authentication.md).

## <a name="event-delivery"></a>Eseménykézbesítés

Event Grid nem győződjön meg arról, hogy az előfizető végpontja egy eseményt kapott, ha ez az esemény redelivers. További információkért lásd: [Event Grid az üzenetek kézbesítését, és ismételje meg](delivery-and-retry.md).

## <a name="batching"></a>Kötegelés

Egy egyéni témakör használatakor az események mindig közzé kell tenni a tömbben. Ez lehet egy kötegelt, az alacsony átviteli sebességű forgatókönyvek, azonban a nagy mennyiségű használati esetek, azt javasoljuk, hogy több batch együtt kiszolgálónként események közzététele nagyobb hatékonyság elérése érdekében. Kötegek legfeljebb 1 MB lehet. Minden esemény még mindig nem lehet 64 KB-nál nagyobb.

## <a name="next-steps"></a>További lépések

* Az Event Grid megismeréséhez tekintse meg [az Event Grid bevezetőjét](overview.md).
* Tekintse meg a gyors kezdéshez Event Grid használatával [az Azure Event Griddel egyéni események létrehozása és útvonal](custom-event-quickstart.md).
