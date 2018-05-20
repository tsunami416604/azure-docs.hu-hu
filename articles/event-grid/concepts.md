---
title: Az Azure Event rács fogalmak
description: Azure Event rács és a fogalmakat ismerteti. Határozza meg az esemény rács több kulcsfontosságú összetevők.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: babanisa
ms.openlocfilehash: fd82d163ba8407a3dfa088cd322f3e236be5d7ea
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="concepts-in-azure-event-grid"></a>Az Azure Event rácsban fogalmak

Ez a cikk ismerteti az alapvető fogalmakat Azure esemény rácsban.

## <a name="events"></a>Események

Az esemény teljes leíró adatokat valami, és ismételje meg a rendszer a legkevesebb jelenti. Minden eseményhez tartozik az általános információkat, például: az esemény forrását időt vett igénybe az esemény, érvényes és egyedi azonosítója. Minden esemény is rendelkezik, amely csak az adott típusú eseményre vonatkozó információkkal. Például egy eseményt, egy új fájl létrehozását az Azure Storage kapcsolatos részleteket tartalmaz a fájlról, mint a `lastTimeModified` érték. Vagy az Event Hubs esemény rendelkezik a rögzítési fájl URL-CÍMÉT. Minden esemény 64 KB adatot korlátozódik.

## <a name="event-sourcespublishers"></a>Esemény források-közzétevők

Egy eseményforrás, ahol az esemény akkor fordul elő. Például az Azure Storage az eseményforrás a létrehozott események BLOB. Az Azure virtuális gép háló a forrás virtuális gép események. Eseményforrások felelősek esemény rácshoz események közzététele.

További információ a támogatott esemény rács forrásokban végrehajtási: [eseményforrások Azure esemény rácsban](event-sources.md).

## <a name="topics"></a>Témakörök

Közzétevők események kategorizálása témakörökre. Az esemény a rács témakör tartalmaz egy végpontot, ahol a közzétevő eseményeket küldi. Bizonyos típusú eseményekről válaszolni előfizetők döntse el, melyik témakörök előfizetni. Témakörök a egy esemény séma is tartalmaznak, így előfizetők megtudhatja, hogyan az események felhasználásához megfelelően.

Rendszer témakörök Azure szolgáltatás által biztosított beépített témakörök szolgálnak. Egyéni témakörök alkalmazás és a külső témakörök szolgálnak.

Az alkalmazás tervezésekor beleszólása van létrehozásához hány témakörök meghatározásakor. A nagy megoldások hozzon létre egy egyéni témához kapcsolódó események kategóriákhoz tartozó. Vegye figyelembe például egy alkalmazás által a felhasználói fiókok módosítása és megrendelések feldolgozása eseményeket. Nem valószínű, bármely eseménykezelő szeretne mindkét események kategóriáit. Hozzon létre két egyéni témaköröket, és lehetővé teszik a érdeklő őket egy előfizetés eseménykezelők. A kis megoldások előfordulhat, hogy inkább összes eseményt küldeni egy témakör. Esemény előfizetők szűrheti a kívánják-esemény esetében.

## <a name="event-subscriptions"></a>Esemény-előfizetések

Előfizetés arra utasítja a témakör az eseményeket az előfizető olyan fogadás iránt érdeklődik esemény rács. Előfizetés is tartalmazza a hogyan események kézbesítési az előfizető.

## <a name="event-handlers"></a>Eseménykezelők

Esemény rács szempontból eseménykezelő az a hely, ahol az esemény küldése. A kezelő bontja feldolgozni az eseményt néhány további művelet. Esemény rács több előfizető-típusokat támogatja. Attól függően, hogy milyen típusú előfizető esemény rács különböző mechanizmusok garantálja a kézbesítési esemény következik. A HTTP-webhook eseménykezelők, az eseményt a rendszer ismét megkísérli mindaddig, amíg a kezelő egy állapot kódot ad vissza `200 – OK`. Az Azure Storage Üzenetsorába az események ismétlődnek, amíg a várólista-szolgáltatás nem tudja sikeresen feldolgozni az üzenetet leküldéses a várólistán.

További információ a támogatott esemény rács kezelők bármelyikét végrehajtási: [eseménykezelők Azure esemény rácsban](event-handlers.md).

## <a name="filters"></a>Szűrők

Amikor előfizet egy esemény rács témakör, szűrhetők az események a végpontnak küldött. Esemény típusa, vagy a tulajdonos minta szerint szűrheti. További információkért lásd: [esemény rács előfizetés séma](subscription-creation-schema.md).

## <a name="security"></a>Biztonság

Esemény rács témakörök előfizetés, és a témakörök közzététele biztonságot nyújt. Előfizetés, amikor az erőforrás vagy esemény rács témakör megfelelő engedélyekkel kell rendelkeznie. Közzétételekor, rendelkeznie kell egy SAS-jogkivonat vagy a témakör kulcsos hitelesítéséhez. További információkért lásd: [esemény rács biztonsági és hitelesítési](security-authentication.md).

## <a name="failed-delivery"></a>Sikertelen kézbesítés

Ha esemény rács nem tudják megerősíteni, hogy az előfizető végpont egy eseményt kapott, ez az esemény redelivers. További információkért lásd: [esemény rács üzenetkézbesítést, és próbálkozzon újra](delivery-and-retry.md).

## <a name="next-steps"></a>További lépések

* Esemény rácshoz ismertetőért lásd: [esemény rács](overview.md).
* Ha gyorsan esemény rács segítségével, lásd: [Azure esemény rácshoz hozza létre és útvonal egyéni események](custom-event-quickstart.md).
