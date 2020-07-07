---
title: Windows rendszerű virtuális asztali gazdagép-készlet terheléselosztása – Azure
description: Gazdagépek terheléselosztási módszerei a Windows rendszerű virtuális asztali környezetekhez.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e2bfbd580d0e114cf4a135879340745107183b6a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82614810"
---
# <a name="host-pool-load-balancing-methods"></a>Gazdagépcsoport terheléselosztási módszerei

>[!IMPORTANT]
>Ez a tartalom a Fall 2019 kiadásra vonatkozik, amely nem támogatja a Windows rendszerű virtuális asztali objektumokat Azure Resource Manager. Ha a Spring 2020 Update szolgáltatásban bevezetett Azure Resource Manager Windows rendszerű virtuális asztali objektumokat szeretne felügyelni, tekintse meg [ezt a cikket](../host-pool-load-balancing.md).

A Windows rendszerű virtuális asztali gépek két terheléselosztási módszert támogatnak. Az egyes módszerek határozzák meg, hogy melyik munkamenet-állomás fogja tárolni a felhasználó munkamenetét, amikor egy gazdagépen lévő erőforráshoz csatlakoznak.

A következő terheléselosztási módszerek érhetők el a Windows rendszerű virtuális asztali gépen:

- A szélesség – első terheléselosztás lehetővé teszi a felhasználói munkamenetek egyenletes elosztását a gazdagépen lévő munkamenet-gazdagépek között.
- Mélység – az első terheléselosztás lehetővé teszi, hogy a munkamenet-gazdagépet a gazdagépen lévő felhasználói munkamenetekkel telítettül. Miután az első munkamenet eléri a munkamenet-korlátot, a terheléselosztó az új felhasználói kapcsolatokat a gazdagépen a következő munkamenet-állomásra irányítja, amíg meg nem éri a korlátot, és így tovább.

Az egyes gazdagépek készletei csak egy ilyen terheléselosztási típust állíthatnak be. A terheléselosztási módszerek azonban a következő viselkedéseket is megoszthatják, függetlenül attól, hogy melyik gazdagépen vannak:

- Ha a felhasználó már rendelkezik egy munkamenettel a gazdagépen, és újra csatlakozik ehhez a munkamenethez, akkor a terheléselosztó sikeresen átirányítja azokat a munkamenet-gazdagépre a meglévő munkamenettel. Ez a viselkedés akkor is érvényes, ha a munkamenet-állomás AllowNewConnections tulajdonsága hamis értékre van állítva.
- Ha a felhasználó még nem rendelkezik munkamenettel a gazdagépen, akkor a terheléselosztó nem veszi figyelembe, hogy a AllowNewConnections tulajdonság értéke false a terheléselosztás során.

## <a name="breadth-first-load-balancing-method"></a>Szélesség – első terheléselosztási módszer

A szélesség – első terheléselosztási módszer lehetővé teszi a felhasználói kapcsolatok terjesztését a forgatókönyv optimalizálásához. Ez a módszer ideális azoknak a szervezeteknek, akik a legjobb élményt szeretnék biztosítani a készletezett virtuális asztali környezethez csatlakozó felhasználók számára.

A szélesség – első módszer először a munkamenet azon gazdagépeit kérdezi le, amelyek engedélyezik az új kapcsolatokat. A metódus ezután kiválasztja a munkamenet-gazdagépet a legkevesebb munkamenettel. Döntetlen esetén a metódus kiválasztja az első munkamenet-állomást a lekérdezésben.

## <a name="depth-first-load-balancing-method"></a>Mélység – első terheléselosztási módszer

A mélység – első terheléselosztási módszer lehetővé teszi, hogy egyszerre egy munkamenet-gazdagépet telítetten, hogy optimalizálja ezt a forgatókönyvet. Ez a módszer ideális olyan költséghatékony szervezeteknek, amelyek részletesebben szabályozzák a gazdagépek számára lefoglalt virtuális gépek számát.

A mélység – első módszer először a munkamenet azon gazdagépeit kérdezi le, amelyek engedélyezik az új kapcsolatokat, és nem mentek át a maximális munkamenet-korláton A metódus ezután kiválasztja a munkamenet-gazdagépet a legnagyobb számú munkamenettel. Ha döntetlen van, a metódus kiválasztja az első munkamenet-állomást a lekérdezésben.