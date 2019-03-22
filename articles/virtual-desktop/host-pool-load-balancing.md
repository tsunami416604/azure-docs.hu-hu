---
title: Gazdagép készlet terheléselosztási módszerek (előzetes verzió) – Azure
description: Gazdagép készlet terheléselosztási módszerek Windows virtuális asztali környezetben.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 0a07406c8bad4ad0bef2949103d1f2c78e7dd8af
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58318509"
---
# <a name="host-pool-load-balancing-methods"></a>Gazdagép készlet terheléselosztási módszerek

Windows virtuális asztal (előzetes verzió) két terheléselosztási módszert támogat. Az egyes módszerek határozza meg, melyik munkamenetgazda fogja tárolni a felhasználói munkamenet, amikor csatlakoznak a gazdagép-készletben lévő egyes erőforrásokhoz.

A következő terheléselosztási módszerek a Windows virtuális asztal érhetők el:

- Szélesség-és felhőközpontú terheléselosztás lehetővé teszi a segítségével egyenlően osztható el a felhasználói munkamenetek a gazdagép készlet munkamenet gazdagépeken.
- Terheléselosztás mélysége-és felhőközpontú lehetővé teszi egy munkamenetgazda telítsük a felhasználói munkamenetek a gazdagép készletben. Ha az első munkamenet elérte az munkamenet korlát küszöböt, a terheléselosztó a következő munkamenetgazda, a gazdagép-készletben lévő új felhasználói kapcsolatok irányítja, amíg eléri a korlátot, és így tovább.

Minden gazdagép készlet csak konfigurálható a terheléselosztó egy adott típusú adott hozzá. Azonban az alábbi viselkedés tapasztalható, függetlenül attól, amely a készlet gazdagép terheléselosztási módszer mindkét megosztás, az Ön által használt:

- Ha a felhasználó már rendelkezik egy munkamenettel a gazdagép-készletben, és a munkamenet éppen újracsatlakozik, a terheléselosztó sikeresen átirányítja őket a a munkamenetgazda, a meglévő munkamenethez. Ez a viselkedés érvényes, ha a munkamenet üzemeltető AllowNewConnections tulajdonsága hamis értékre van beállítva.
- Ha a felhasználó még nem rendelkezik a munkamenet a gazdagép-készlet, a terheléselosztó nem megfontolása, amelynek AllowNewConnections tulajdonsága hamis értékre van beállítva során terheléselosztás munkamenet gazdagépek.

## <a name="breadth-first-load-balancing-method"></a>Szélesség-és felhőközpontú terheléselosztási módszer

A szélesség-és felhőközpontú terheléselosztási módszer lehetővé teszi, hogy között oszthatja el az ebben a forgatókönyvben optimalizálhatja a felhasználói kapcsolatok. Ez a módszer ideális szervezeteknek, amelyek a legjobb élményt biztosíthat a felhasználóknak a készletezett virtuális asztali környezetben kapcsolódni szeretne.

A szélesség-és felhőközpontú metódus először lekérdezi a munkamenet-gazdagépek, amelyek lehetővé teszik az új kapcsolatokat. A módszer, majd kiválasztja a munkamenetgazda, a legalacsonyabb munkamenetek száma. Egy holtversenyben esetén a módszer kiválasztja az első munkamenetgazda a lekérdezésben.

## <a name="depth-first-load-balancing-method"></a>Várólistamélység-és felhőközpontú terheléselosztási módszer

A mélység-és felhőközpontú terheléselosztási módszer lehetővé teszi egy munkamenetgazda telítsük optimalizálása ebben a forgatókönyvben egyszerre. Ez a módszer ideális a költségérzékeny szervezetek, amelyek pontosabban szabályozhatja azok hozzárendelte egy gazdagép készlet virtuális gépek száma.

A mélység-és felhőközpontú metódus először kérdezi le a munkamenet-gazdagépek, amelyek lehetővé teszik az új kapcsolatokat, és még nem ment keresztül maximális munkamenet maximális számát. A metódus ezután kiválasztja a munkamenetgazda-munkamenetek számát vesszük figyelembe. Egy holtversenyben esetén a módszer kiválasztja az első munkamenetgazda a lekérdezésben.