---
title: Tárterület hozzáadása egy Azure HPC-gyorsítótárhoz (előzetes verzió)
description: Tárolási célok meghatározása úgy, hogy az Azure HPC-gyorsítótára használhassa a helyszíni NFS-rendszert vagy az Azure Blob-tárolókat a hosszú távú fájlok tárolásához
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: v-erkell
ms.openlocfilehash: 7df0727a58f3d70289c5060175572dac1bbb4abb
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300027"
---
# <a name="add-storage-targets"></a>Céltárak hozzáadása

A *tárolási célok* az Azure HPC cache-példányon keresztül elért fájlok háttérbeli tárolói. Hozzáadhat NFS-tárhelyet, például egy helyszíni hardvereszközt, vagy tárolhatja az Azure-blobban tárolt adattárakat.

Akár tíz különböző tárolási célt is meghatározhat egy gyorsítótárhoz. A gyorsítótár egy összesített névtérben jeleníti meg az összes tárolási célt.

Ne feledje, hogy a tárolók exportálásának elérhetőnek kell lennie a gyorsítótár virtuális hálózatáról. A helyszíni hardveres tároláshoz előfordulhat, hogy olyan DNS-kiszolgálót kell beállítania, amely képes az NFS-tároló elérésére szolgáló gazdagépek feloldására. További információk: [DNS-hozzáférés](hpc-cache-prereqs.md#dns-access).

A gyorsítótár létrehozásakor vagy később is hozzáadhat tárolási célokat. Az eljárás némileg eltérő attól függően, hogy az Azure Blob Storage-t vagy egy NFS-exportálást ad hozzá. A részleteket az alábbiakban találja.

## <a name="add-storage-targets-while-creating-the-cache"></a>Tárolási célok hozzáadása a gyorsítótár létrehozásakor

Az Azure HPC gyorsítótár-létrehozási varázsló **tárolási célok** lapján definiálhatja a tárterületet, amikor létrehozza a gyorsítótár-példányt.

![a tárolási célok oldalának képernyőképe](media/hpc-cache-storage-targets-pop.png)

A tárterület hozzáadásához kattintson a **tároló hozzáadása** hivatkozásra.

## <a name="add-storage-targets-from-the-cache"></a>Tárolási célok hozzáadása a gyorsítótárból

A Azure Portal nyissa meg a gyorsítótár-példányt, és kattintson a bal oldali oldalsávon található **tárolási célok** elemre. A tárolási cél lap felsorolja az összes meglévő célt, és egy hivatkozást ad hozzá egy újat.

![képernyőfelvétel a Storage Targets hivatkozásáról az oldalsávon, a configure (Konfigurálás) fejléc alatt, amely a kategória fejlécek beállításai és figyelése között található.](media/hpc-cache-storage-targets-sidebar.png)

## <a name="add-a-new-azure-blob-storage-target"></a>Új Azure Blob Storage-cél hozzáadása

Egy új blob Storage-célnak szüksége van egy üres blob-tárolóra vagy egy olyan tárolóra, amely az Azure HPC cache Cloud File System formátumában található adatokkal van feltöltve. További információ a blob-tárolók [Azure Blob Storage-ba való áthelyezésének](hpc-cache-ingest.md)előzetes betöltéséről.

Azure Blob-tároló definiálásához adja meg ezt az információt.

![képernyőfelvétel a Storage-cél hozzáadása oldalról, amely az új Azure Blob Storage-tárolóra vonatkozó információkkal van feltöltve](media/hpc-cache-add-blob.png)

<!-- need to replace screenshot after note text is updated with both required RBAC roles -->

* **Tárolási cél neve** – adjon meg egy nevet, amely azonosítja ezt a tárolási célt az Azure HPC cache-ben.
* **Cél típusa** – válassza a **blob**lehetőséget.
* **Storage-fiók** – válassza ki azt a fiókot, amelyhez a tároló hivatkozik.

  Engedélyeznie kell a gyorsítótár-példányt a Storage-fiók eléréséhez a [hozzáférési Szerepkörök hozzáadása](#add-the-access-control-roles-to-your-account)című témakörben leírtak szerint.
* **Storage-tároló** – válassza ki a cél blob-tárolóját.

* **Virtuális névtér elérési útja** – a tárolási cél ügyféloldali elérési útjának beállítása. A virtuális névtér szolgáltatással kapcsolatos további tudnivalókért olvassa el az [összesített névtér konfigurálása](hpc-cache-namespace.md) című témakört.

Ha elkészült, kattintson az **OK** gombra a tárolási cél hozzáadásához.

### <a name="add-the-access-control-roles-to-your-account"></a>Hozzáférés-vezérlési Szerepkörök hozzáadása a fiókhoz

Az Azure HPC cache [szerepköralapú hozzáférés-vezérlést (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/index) használ a gyorsítótár-alkalmazás engedélyezéséhez az Azure Blob Storage-célokhoz tartozó Storage-fiók eléréséhez.

A Storage-fiók tulajdonosának explicit módon hozzá kell adnia a roles [Storage-fiók közreműködőjét](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor) és a [Storage blob adatközreműködőit](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) a "StorageCache erőforrás-szolgáltató" felhasználóhoz.

Ezt előre elvégezheti, vagy egy hivatkozásra kattintva megadhatja a blob Storage-tárolót tartalmazó oldalon.

A RBAC szerepkörök hozzáadásának lépései:

1. Nyissa meg a Storage-fiókhoz tartozó **hozzáférés-vezérlés (iam)** lapot. (A **tároló hozzáadása** lapon lévő hivatkozás automatikusan megnyitja ezt a lapot a kiválasztott fiókhoz.)

1. Kattintson a lap tetején található elemre, majd válassza a **szerepkör-hozzárendelés hozzáadása**lehetőséget. **+**

1. Válassza ki a "Storage-fiók közreműködője" szerepkört a listából.

1. A **hozzáférés kiosztása** mezőben hagyja meg a kiválasztott alapértelmezett értéket ("Azure ad-felhasználó, csoport vagy szolgáltatásnév").  

1. A **Select (kiválasztás** ) mezőben keressen rá a "storagecache" kifejezésre.  Ennek a karakterláncnak meg kell egyeznie egy rendszerbiztonsági tag, a "HPC cache erőforrás-szolgáltató" névvel. Kattintson erre a résztvevőre a kiválasztásához.

1. Kattintson a **Save (Mentés** ) gombra a szerepkör-hozzárendelés a Storage-fiókhoz való hozzáadásához.

1. Ismételje meg ezt a folyamatot a "Storage blob-adatközreműködői" szerepkör hozzárendeléséhez.  

![a szerepkör-hozzárendelés grafikus felhasználói felületének hozzáadása képernyőkép](media/hpc-cache-add-role.png)

## <a name="add-a-new-nfs-storage-target"></a>Új NFS-tárolási cél hozzáadása

Az NFS-tárolási cél további mezőkkel rendelkezik, amelyekkel megtudhatja, hogyan érheti el a tároló-exportálást, és hogyan kezelheti hatékonyan az adatait. Emellett több névtér elérési útját is létrehozhatja egy NFS-gazdagépről, ha egynél több exportálás érhető el.

![Képernyőfelvétel a Storage-cél hozzáadása oldalról a megadott NFS-célként](media/hpc-cache-add-nfs-target.png)

Adja meg ezt az információt egy NFS-alapú tárolási cél számára:

* **Tárolási cél neve** – adjon meg egy nevet, amely azonosítja ezt a tárolási célt az Azure HPC cache-ben.

* **Cél típusa** – válassza az **NFS**lehetőséget.

* **Állomásnév** – adja meg az NFS-tárolási rendszer IP-címét vagy teljesen minősített tartománynevét. (Csak akkor használjon tartománynevet, ha a gyorsítótár egy olyan DNS-kiszolgálóhoz fér hozzá, amely fel tudja oldani a nevet.)

* **Használati modell** – válassza ki az egyik adatgyorsítótárazási profilt a munkafolyamat alapján, az [alábbi, használati modell kiválasztása](#choose-a-usage-model)részben leírtak szerint.

### <a name="nfs-namespace-paths"></a>NFS-névtér elérési útjai

Egy NFS-tárolási cél több virtuális útvonallal is rendelkezhet, ha az egyes elérési utak ugyanazon a tárolási rendszeren eltérő exportálási vagy alkönyvtárat jelölnek.

Hozza létre az összes elérési utat egy tárolási tárolóból.
<!-- You can create multiple namespace paths to represent different exports on the same NFS storage system, but you must create them all from one storage target. -->

Adja meg ezeket az értékeket az egyes névterek elérési útjához: 

* **Virtuális névtér elérési útja** – a tárolási cél ügyféloldali elérési útjának beállítása. A virtuális névtér szolgáltatással kapcsolatos további tudnivalókért olvassa el az [összesített névtér konfigurálása](hpc-cache-namespace.md) című témakört.

<!--  The virtual path should start with a slash ``/``. -->

* **NFS-exportálási útvonal** – adja meg az NFS-exportálás elérési útját.

* **Alkönyvtár elérési útja** – ha az Exportálás egy adott alkönyvtárát szeretné csatlakoztatni, írja be ide. Ha nem, hagyja üresen ezt a mezőt. 

Ha elkészült, kattintson az **OK** gombra a tárolási cél hozzáadásához.

### <a name="choose-a-usage-model"></a>Használati modell kiválasztása
<!-- referenced from GUI - update aka.ms link if you change this heading -->

Amikor egy NFS Storage rendszerre mutató tárolási célt hoz létre, ki kell választania az adott cél *használati modelljét* . Ez a modell határozza meg, hogyan gyorsítótárazza az adatait.

* Beolvasás – ha többnyire a cache használatával felgyorsítja az adatolvasási hozzáférést, válassza ezt a lehetőséget. 

* Olvasás/írás – ha az ügyfelek a gyorsítótárat használják olvasásra és írásra, válassza ezt a lehetőséget.

* A gyorsítótár megkerülése az ügyfelek számára – akkor válassza ezt a lehetőséget, ha az ügyfelek közvetlenül a Storage rendszerbe írnak az adatait anélkül, hogy először a gyorsítótárba írna.

## <a name="next-steps"></a>További lépések

A tárolási célok létrehozása után vegye figyelembe a következő feladatok egyikét:

* [Az Azure HPC-gyorsítótár csatlakoztatása](hpc-cache-mount.md)
* [Az Azure Blob Storage-ba irányuló adatáthelyezés](hpc-cache-ingest.md)