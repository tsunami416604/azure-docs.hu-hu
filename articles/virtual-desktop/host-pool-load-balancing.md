---
title: Windows virtuális asztali gazdakészlet terheléselosztása – Azure
description: A gazdakészlet terheléselosztási módszerei Windows virtuális asztali környezethez.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 193821ed0df09b87f19e45a82ca42026405a0dc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127858"
---
# <a name="host-pool-load-balancing-methods"></a>Gazdagépcsoport terheléselosztási módszerei

A Windows virtuális asztal két terheléselosztási módszert támogat. Az egyes módszerek határozzák meg, hogy melyik munkamenet-gazdagép fogja üzemeltetni a felhasználó munkamenetét, amikor egy gazdakészleterőforráshoz csatlakoznak.

A Windows Virtual Desktop rendszerben a következő terheléselosztási módszerek érhetők el:

- A szélesség-első terheléselosztás lehetővé teszi a felhasználói munkamenetek egyenletes elosztását a munkamenet-gazdagépek között egy gazdakészletben.
- A mélység-első terheléselosztás lehetővé teszi a munkamenet-gazdagép telítését a gazdakészletfelhasználói munkameneteivel. Amint az első munkamenet eléri a munkamenet-korlát küszöbértékét, a terheléselosztó minden új felhasználói kapcsolatot a gazdakészlet következő munkamenet-állomásához irányít, amíg el nem éri a korlátot, és így tovább.

Minden állomáskészlet csak egy adott terheléselosztási típust konfigurálhat. Azonban mindkét terheléselosztási módszer a következő viselkedéseket osztja meg, függetlenül attól, hogy melyik gazdakészletben vannak:

- Ha egy felhasználó már rendelkezik munkamenettel a gazdakészletben, és újra csatlakozik a munkamenethez, a terheléselosztó sikeresen átirányítja őket a munkamenet-gazdagéphez a meglévő munkamenettel. Ez a viselkedés akkor is érvényes, ha a munkamenetgazda AllowNewConnections tulajdonsága Hamis.
- Ha a felhasználó még nem rendelkezik munkamenettel a gazdakészletben, akkor a terheléselosztó nem veszi figyelembe azokat a munkamenet-állomásokat, amelyek AllowNewConnections tulajdonsága Hamis a terheléselosztás során.

## <a name="breadth-first-load-balancing-method"></a>A szélesség-első terheléselosztási módszer

A szélesség-első terheléselosztási módszer lehetővé teszi a felhasználói kapcsolatok elosztását az erre a forgatókönyvre optimalizálva. Ez a módszer ideális olyan szervezetek számára, amelyek a legjobb felhasználói élményt szeretnék biztosítani a közös virtuális asztali környezetükhöz csatlakozó felhasználók számára.

A szélesség-első módszer első lekérdezések munkamenet gazdagépek, amelyek lehetővé teszik az új kapcsolatokat. A metódus ezután kiválasztja a munkamenet-gazdatestet a legkevesebb munkamenettel. Ha van döntetlen, a metódus kiválasztja a lekérdezés első munkamenet-gazdagépét.

## <a name="depth-first-load-balancing-method"></a>Mélység-első terheléselosztási módszer

A mélység-első terheléselosztási módszer lehetővé teszi, hogy egyszerre egy munkamenet-gazdagép telítse el az erre a forgatókönyvre optimalizálni. Ez a módszer ideális olyan költségtudatos szervezetek számára, amelyek részletesebb vezérlést szeretnének a gazdakészlethez lefoglalt virtuális gépek száma felett.

A mélység-első módszer első lekérdezések munkamenet gazdagépek, amelyek lehetővé teszik az új kapcsolatokat, és nem ment túl a maximális munkamenet-korlátot. A metódus ezután kiválasztja a munkamenet-gazdat, amely a legtöbb munkamenetet adja meg. Ha van egy döntetlen, a metódus kiválasztja az első munkamenet-gazdagép a lekérdezésben.