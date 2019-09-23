---
title: Azure HPC-gyorsítótár létrehozása (előzetes verzió)
description: Azure HPC cache-példány létrehozása
author: ekpgh
ms.service: hpc-cache
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: 1cc77b24c96514f40c86115f7d611076facd406b
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71181044"
---
# <a name="create-an-azure-hpc-cache-preview"></a>Azure HPC-gyorsítótár létrehozása (előzetes verzió)

A gyorsítótár létrehozásához használja a Azure Portal. 

![képernyőkép a gyorsítótár áttekintéséről Azure Portalban, a létrehozás gombbal alul](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Alapszintű részletek meghatározása

![képernyőkép a Project details lapról Azure Portal](media/hpc-cache-create-basics.png)

A **Project Details (projekt részletei**) területen válassza ki azt az előfizetést és erőforráscsoportot, amely az Azure HPC gyorsítótárát fogja tárolni. Győződjön meg arról, hogy az előfizetés az [előnézeti hozzáférési](hpc-cache-prereqs.md#azure-subscription) listán van.

A **szolgáltatás részletei**területen adja meg a gyorsítótár nevét és a többi attribútumot:

* Hely – válasszon egy [támogatott régiót](hpc-cache-overview.md#region-availability).
* Virtuális hálózat – választhat egy meglévőt, vagy létrehozhat egy új virtuális hálózatot.
* Alhálózat – válasszon ki vagy hozzon létre legalább 64 IP-címmel rendelkező alhálózatot (/24), amelyet csak az Azure HPC-gyorsítótárhoz fog használni.

## <a name="set-cache-capacity"></a>Gyorsítótár kapacitásának beállítása
<!-- referenced from GUI - update aka.ms link if you change this header text -->

A **gyorsítótár** lapon be kell állítania az Azure HPC-gyorsítótár kapacitását. Ez az érték határozza meg, hogy mekkora adatmennyiséget tárolhat a gyorsítótár, és milyen gyorsan képes az ügyfelek kéréseinek kiszolgálására. A nyilvános előzetes verzió időszaka után a kapacitás a gyorsítótár költségeit is érinteni fogja.

A gyorsítótár kapacitása másodpercenkénti bemeneti/kimeneti műveleteknél (IOPS) mérhető. Válassza ki a kapacitást a következő két érték beállításával:

* A gyorsítótár (átviteli sebesség) maximális adatátviteli sebessége GB/másodpercben
* A gyorsítótárazott adathoz lefoglalt tárterület mennyisége (TB)

Válassza ki az elérhető átviteli sebesség és a gyorsítótár tárolási méretének egyikét. A IOPS kapacitását a rendszer kiszámítja, és az érték kiválasztása alatt látható.

Ne feledje, hogy a tényleges adatátviteli sebesség a munkaterhelés, a hálózati sebesség és a tárolási célok típusától függ. Ha egy fájl nincs a gyorsítótárban, vagy elavultként van megjelölve, a szolgáltatás némi átviteli sebességet használ a háttérbeli tárolóból való beolvasáshoz. A kiválasztott érték a teljes gyorsítótár maximális átviteli sebességét állítja be, és nem mindegyik elérhető az ügyfelek kéréseihez.

A gyorsítótár-tároláshoz az Azure HPC cache kezeli a gyorsítótárban tárolt és előre feltöltött fájlokat, hogy maximalizálja a gyorsítótár találati arányát. A gyorsítótár tartalmának folyamatos ellenőrzése megtörténik, és a fájlok átkerülnek a hosszú távú tárolásba, ha ritkábban férnek hozzá. Válasszon egy olyan gyorsítótár-tárolási méretet, amely kényelmesen tárolhatja a munkafájlok aktív készletét, és további helyet biztosít a metaadatok és egyéb terhelések számára.

![a gyorsítótár-méretezés oldalának képernyőképe](media/hpc-cache-create-iops.png)

## <a name="add-storage-targets"></a>Tárolási célok hozzáadása

A tárolási célok a gyorsítótár tartalmának háttér-és hosszú távú tárolására szolgálnak.

A tárolási célokat a gyorsítótár létrehozásakor definiálhatja, de később is hozzáadhatja őket a portál gyorsítótár lapjának **configure (Konfigurálás** ) szakaszának hivatkozásával.

![a tárolási célok oldalának képernyőképe](media/hpc-cache-storage-targets-pop.png)

Kattintson a **tárolási cél hozzáadása hivatkozásra** a háttérbeli tárolók meghatározásához. A tároló lehet Azure Blob-tároló vagy helyszíni NFS-rendszer.

Akár tíz különböző tárolási célt is meghatározhat.

A tárolási célok hozzáadásával kapcsolatos részletes útmutatást a tárolók [hozzáadása](hpc-cache-add-storage.md)című témakör tartalmazza. Az eljárás eltérő a blob Storage-ban vagy az NFS-exportálásokban.

Íme néhány tipp: 

* Mindkét típusú tároló esetében meg kell adnia, hogyan kell megkeresni a háttérrendszer tárolási rendszerét (egy NFS-vagy blob-tároló nevét) és az ügyfél felé irányuló névtér elérési útját.

* BLOB Storage-tároló létrehozásakor győződjön meg arról, hogy a gyorsítótár rendelkezik hozzáférési engedélyekkel a Storage-fiókhoz a [hozzáférés-vezérlési Szerepkörök hozzáadása](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account)című témakörben leírtak szerint. Ha nem biztos abban, hogy a szerepkör-konfiguráció sikeres lesz, először hozza létre a gyorsítótárat, majd utána adja hozzá a blob Storage-tárolót.

* NFS-tárolási cél létrehozásakor meg kell adnia egy [használati modellt](hpc-cache-add-storage.md#choose-a-usage-model). A használati modell beállítása segít a gyorsítótár optimalizálásában a munkafolyamatot.

## <a name="add-resource-tags-optional"></a>Erőforrás-Címkék hozzáadása (nem kötelező)

A **címkék** lapon hozzáadhat erőforrás- [CÍMKÉKET](https://go.microsoft.com/fwlink/?linkid=873112) az Azure HPC-gyorsítótárához. 

## <a name="finish-creating-the-cache"></a>A gyorsítótár létrehozásának befejezése

Az új gyorsítótár konfigurálása után kattintson a **felülvizsgálat + létrehozás** fülre. A portál ellenőrzi a beállításokat, és lehetővé teszi a lehetőségek áttekintését. Ha minden helyes, kattintson a **Létrehozás**gombra. 

A gyorsítótár létrehozása körülbelül 10 percet vesz igénybe. A folyamat nyomon követhető a Azure Portal értesítések paneljén. 

![képernyőkép a gyorsítótár létrehozásáról "üzembe helyezés folyamatban" és "értesítések" oldalain a portálon](media/hpc-cache-deploy-status.png)

A létrehozás befejeződése után egy értesítés jelenik meg az új Azure HPC cache-példányra mutató hivatkozással, a gyorsítótár pedig megjelenik az előfizetés **erőforrások** listájában. 

![képernyőkép az Azure HPC cache-példányról Azure Portal](media/hpc-cache-new-overview.png)

## <a name="next-steps"></a>További lépések

Miután a gyorsítótár megjelenik az **erőforrások** listájában, csatlakoztathatja azt az ügyfél-hozzáféréshez, ezzel áthelyezheti a munkakészlete adatait egy új Azure Blob Storage-tárolóba, vagy további adatforrásokat is meghatározhat.

* [Az Azure HPC-gyorsítótár csatlakoztatása](hpc-cache-mount.md)
* [Adatáthelyezés az Azure Blob Storage-ba az Azure HPC cache szolgáltatásba](hpc-cache-ingest.md)
* [Tárolási célok hozzáadása](hpc-cache-add-storage.md)
