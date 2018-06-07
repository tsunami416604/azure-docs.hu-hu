---
title: Az Azure Event rács fogalmak
description: Az Azure Event Gridet és a vele kapcsolatos fogalmakat ismerteti. Határozza meg az esemény rács több kulcsfontosságú összetevők.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/23/2018
ms.author: tomfitz
ms.openlocfilehash: abc1302f0317c8d5ecdc7ddaf8ca6d3a9e82b582
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626035"
---
# <a name="concepts-in-azure-event-grid"></a>Az Azure Event rácsban fogalmak

Ez a cikk ismerteti az alapvető fogalmakat Azure esemény rácsban.

## <a name="events"></a>Események

Az esemény teljes leíró adatokat valami, és ismételje meg a rendszer a legkevesebb jelenti. Minden eseményhez tartozik az általános információkat, például: az esemény forrását időt vett igénybe az esemény, érvényes és egyedi azonosítója. Minden esemény is rendelkezik, amely csak az adott típusú eseményre vonatkozó információkkal. Például egy eseményt, egy új fájl létrehozását az Azure Storage kapcsolatos részleteket tartalmaz a fájlról, mint a `lastTimeModified` érték. Vagy az Event Hubs esemény rendelkezik a rögzítési fájl URL-CÍMÉT. 

Minden esemény 64 KB adatot korlátozódik.

Esemény küldése általában olyan tulajdonságokat, lásd: [Azure esemény rács esemény séma](event-schema.md).

## <a name="publishers"></a>Kiadók

A közzétevő, a felhasználó vagy a szervezet, amely úgy dönt, hogy elküldje az eseményeket esemény rács. Microsoft közzéteszi az eseményeket több Azure-szolgáltatásokhoz. Események közzéteheti a saját-alkalmazás. Azure-on kívüli szolgáltatásokat nyújtó szervezetek esemény rács keresztül is közzéteheti.

## <a name="event-sources"></a>Eseményforrások

Egy eseményforrás, ahol az esemény akkor fordul elő. Minden eseményforrás kapcsolódik egy vagy több esemény típusa. Például az Azure Storage az eseményforrás a létrehozott események BLOB. Az IoT-központ az eseményforrás a létrehozott események eszköz. Az alkalmazás az Ön által meghatározott egyéni események eseményforrás. Eseményforrások események küldése esemény rács felelősek.

További információ a támogatott esemény rács forrásokban végrehajtási: [eseményforrások Azure esemény rácsban](event-sources.md).

## <a name="topics"></a>Témakörök

Az esemény rács a témakör egy végpontot, ahol a forrás eseményeket küldi. A közzétevő esemény rács témakör hoz létre, és úgy dönt, hogy egy eseményforrás kell-e a több mint egy vagy több témakört. A témakör a kapcsolódó események gyűjteménye szolgál. Bizonyos típusú eseményekről válaszolni előfizetők döntse el, melyik témakörök előfizetni.

Rendszer témakörök Azure szolgáltatás által biztosított beépített témakörök szolgálnak. Nem jelenik meg rendszer témakörök az Azure-előfizetéshez, mert a közzétevő tulajdonosa a témaköröket, de előfizethet a számukra. Előfizetés, adja meg az erőforrással kapcsolatos információkat szeretne kapni az események. Mindaddig, amíg az erőforrás rendelkezik hozzáféréssel, előfizethet az események.

Egyéni témakörök alkalmazás és a külső témakörök szolgálnak. Amikor hoz létre, vagy egy egyéni témához hozzáféréssel rendelkező, az előfizetésben látható egyéni témakör.

Az alkalmazás tervezésekor beleszólása van létrehozásához hány témakörök meghatározásakor. A nagy megoldások hozzon létre egy egyéni témához kapcsolódó események kategóriákhoz tartozó. Vegye figyelembe például egy alkalmazás által a felhasználói fiókok módosítása és megrendelések feldolgozása eseményeket. Nem valószínű, bármely eseménykezelő szeretne mindkét események kategóriáit. Hozzon létre két egyéni témaköröket, és lehetővé teszik a érdeklő őket egy előfizetés eseménykezelők. A kis megoldások előfordulhat, hogy inkább összes eseményt küldeni egy témakör. Esemény előfizetők szűrheti a kívánják-esemény esetében.

## <a name="event-subscriptions"></a>Esemény-előfizetések

Előfizetés közli az esemény rács súgótémakör események érdekli fogadását. Az előfizetés létrehozásakor adja meg a végpont az esemény kezelése. Szűrheti a végpontnak küldött eseményeket. Esemény típusa, vagy a tulajdonos minta szerint szűrheti. További információkért lásd: [esemény rács előfizetés séma](subscription-creation-schema.md).

Az előfizetések létrehozása című részben talál példákat:

* [Az esemény rács Azure CLI-minták](cli-samples.md)
* [Az Azure PowerShell-példák esemény rács](powershell-samples.md)
* [Az Azure Resource Manager-sablonok esemény rács](template-samples.md)

További információ az aktuális esemény rács előfizetések első: [lekérdezés esemény rács előfizetések](query-event-subscriptions.md).

## <a name="event-handlers"></a>Eseménykezelők

Esemény rács szempontból eseménykezelő az a hely, ahol az esemény küldése. A kezelő bontja feldolgozni az eseményt néhány további művelet. Esemény rács több kezelő-típusokat támogatja. Használhatja a kezelő egy támogatott Azure-szolgáltatásra vagy a saját webhook. Attól függően, hogy a kezelő milyen típusú esemény rács különböző mechanizmusok garantálja a kézbesítési esemény következik. A HTTP-webhook eseménykezelők, az eseményt a rendszer ismét megkísérli mindaddig, amíg a kezelő egy állapot kódot ad vissza `200 – OK`. Az Azure Storage Üzenetsorába az események ismétlődnek, amíg a várólista-szolgáltatás nem tudja sikeresen feldolgozni az üzenetet leküldéses a várólistán.

További információ a támogatott esemény rács kezelők bármelyikét végrehajtási: [eseménykezelők Azure esemény rácsban](event-handlers.md).

## <a name="security"></a>Biztonság

Esemény rács témakörök előfizetés, és a témakörök közzététele biztonságot nyújt. Előfizetés, amikor az erőforrás vagy esemény rács témakör megfelelő engedélyekkel kell rendelkeznie. Közzétételekor, rendelkeznie kell egy SAS-jogkivonat vagy a témakör kulcsos hitelesítéséhez. További információkért lásd: [esemény rács biztonsági és hitelesítési](security-authentication.md).

## <a name="event-delivery"></a>Esemény kézbesítés

Ha esemény rács nem tudják megerősíteni, hogy az előfizető végpont egy eseményt kapott, ez az esemény redelivers. További információkért lásd: [esemény rács üzenetkézbesítést, és próbálkozzon újra](delivery-and-retry.md).

## <a name="next-steps"></a>További lépések

* Az Event Grid megismeréséhez tekintse meg [az Event Grid bevezetőjét](overview.md).
* Ha gyorsan esemény rács segítségével, lásd: [Azure esemény rácshoz hozza létre és útvonal egyéni események](custom-event-quickstart.md).
