---
title: Azure HPC-gyorsítótár létrehozása (előzetes verzió)
description: Azure HPC cache-példány létrehozása
author: ekpgh
ms.service: hpc-cache
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: rohogue
ms.openlocfilehash: 7052b88a24ff5353656a71a7bfb044922ae1415c
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709975"
---
# <a name="create-an-azure-hpc-cache-preview"></a>Azure HPC-gyorsítótár létrehozása (előzetes verzió)

A gyorsítótár létrehozásához használja a Azure Portal.

![képernyőkép a gyorsítótár áttekintéséről Azure Portalban, a létrehozás gombbal alul](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Alapszintű részletek meghatározása

![képernyőkép a Project details lapról Azure Portal](media/hpc-cache-create-basics.png)

A **projekt részletei**területen válassza ki azt az előfizetést és erőforráscsoportot, amely a gyorsítótárat fogja tárolni. Győződjön meg arról, hogy az előfizetés az [előnézeti hozzáférési](hpc-cache-prereqs.md#azure-subscription) listán van.

A **szolgáltatás részletei**területen adja meg a gyorsítótár nevét és a többi attribútumot:

* Hely – válasszon egy [támogatott régiót](hpc-cache-overview.md#region-availability).
* Virtuális hálózat – választhat egy meglévőt, vagy létrehozhat egy új virtuális hálózatot.
* Alhálózat – válasszon ki vagy hozzon létre legalább 64 IP-címmel rendelkező alhálózatot (/24), amelyet csak ehhez az Azure HPC cache-példányhoz fog használni.

## <a name="set-cache-capacity"></a>Gyorsítótár kapacitásának beállítása
<!-- referenced from GUI - update aka.ms link if you change this header text -->

A **gyorsítótár** lapon be kell állítania a gyorsítótár kapacitását. Az itt megadott értékek határozzák meg, hogy a gyorsítótár milyen mennyiségű adattal rendelkezhet, és hogy milyen gyorsan lehet az ügyfelek kéréseinek kiszolgálására.

A nyilvános előzetes verzió időszaka után a kapacitás a gyorsítótár költségeit is érinteni fogja.

Válassza ki a kapacitást a következő két érték beállításával:

* A gyorsítótár (átviteli sebesség) maximális adatátviteli sebessége GB/másodpercben
* A gyorsítótárazott adathoz lefoglalt tárterület mennyisége (TB)

Válassza ki az elérhető átviteli sebesség és a gyorsítótár tárolási méretének egyikét.

Ne feledje, hogy a tényleges adatátviteli sebesség a munkaterhelés, a hálózati sebesség és a tárolási célok típusától függ. A megadott értékek a teljes gyorsítótárrendszer maximális átviteli sebességét határozzák meg, de ezek közül néhányat a rendszer a terhelési feladatokhoz használ. Ha például egy ügyfél olyan fájlt kér, amely még nem található meg a gyorsítótárban, vagy ha a fájl elavultként van megjelölve, a gyorsítótár a háttérbeli tárolóból beolvassa az átviteli sebességét.

Az Azure HPC gyorsítótára felügyeli, hogy mely fájlok vannak gyorsítótárazva és előre betöltve a gyorsítótár találati arányának maximalizálása érdekében. A gyorsítótár tartalmának folyamatos ellenőrzése megtörténik, és a fájlok átkerülnek a hosszú távú tárolásba, ha ritkábban férnek hozzá. Válasszon egy olyan gyorsítótár-tárolási méretet, amely kényelmesen tárolhatja a munkafájlok aktív készletét, és további helyet biztosít a metaadatok és egyéb terhelések számára.

![a gyorsítótár-méretezés oldalának képernyőképe](media/hpc-cache-create-capacity.png)

## <a name="add-resource-tags-optional"></a>Erőforrás-Címkék hozzáadása (nem kötelező)

A **címkék** lapon hozzáadhat erőforrás- [CÍMKÉKET](https://go.microsoft.com/fwlink/?linkid=873112) az Azure HPC cache-példányhoz.

## <a name="finish-creating-the-cache"></a>A gyorsítótár létrehozásának befejezése

Az új gyorsítótár konfigurálása után kattintson a **felülvizsgálat + létrehozás** fülre. A portál ellenőrzi a beállításokat, és lehetővé teszi a lehetőségek áttekintését. Ha minden helyes, kattintson a **Létrehozás**gombra.

A gyorsítótár létrehozása körülbelül 10 percet vesz igénybe. A folyamat nyomon követhető a Azure Portal értesítések paneljén.

![képernyőkép a gyorsítótár létrehozásáról "üzembe helyezés folyamatban" és "értesítések" oldalain a portálon](media/hpc-cache-deploy-status.png)

A létrehozás befejeződése után egy értesítés jelenik meg az új Azure HPC cache-példányra mutató hivatkozással, a gyorsítótár pedig megjelenik az előfizetés **erőforrások** listájában.

![képernyőkép az Azure HPC cache-példányról Azure Portal](media/hpc-cache-new-overview.png)

## <a name="next-steps"></a>További lépések

Miután a gyorsítótár megjelenik az **erőforrások** listában, adja meg a tárolási célokat, hogy a gyorsítótár hozzáférjen az adatforrásokhoz.

* [Tárolási célok hozzáadása](hpc-cache-add-storage.md)
