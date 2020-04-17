---
title: Tároló hozzáadása Az Azure HPC-gyorsítótárhoz
description: Tárolási célok definiálása, hogy az Azure HPC-gyorsítótár a helyszíni NFS-rendszer vagy az Azure Blob-tárolók at hosszú távú fájltároláshoz használhassa
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: rohogue
ms.openlocfilehash: cecafd9209b095270e9a06ca59ffef162326efc2
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538001"
---
# <a name="add-storage-targets"></a>Céltárak hozzáadása

*A tárolási célok* az Azure HPC-gyorsítótáron keresztül elérhető fájlok háttér-tárolói. NFS-tárolót (például egy helyszíni hardverrendszert) adhat hozzá, vagy adatokat tárolhat az Azure Blobban.

Egy gyorsítótárhoz legfeljebb tíz különböző tárolási célt határozhat meg. A gyorsítótár egyetlen összesített névtérben mutatja be az összes tárolási célt.

Ne feledje, hogy a tárolóexportálásnak elérhetőnek kell lennie a gyorsítótár virtuális hálózatáról. Helyszíni hardveres tárolás esetén előfordulhat, hogy olyan DNS-kiszolgálót kell beállítania, amely fel tudja oldani az NFS-tárolók állomásneveit. Tovább a [DNS-hozzáférés](hpc-cache-prereqs.md#dns-access).

A gyorsítótár létrehozása után adja hozzá a tárolási célokat. Az eljárás némileg eltér attól függően, hogy Azure Blob storage vagy NFS-exportálás hozzáadása. Az egyes részletek az alábbiakban találhatók.

## <a name="open-the-storage-targets-page"></a>A tárolócélok lap megnyitása

Az Azure Portalon nyissa meg a gyorsítótár-példányt, és kattintson a **tárolási célok** a bal oldalsávon. A tárolási célok lap felsorolja az összes meglévő célokat, és ad egy linket, hogy adjunk egy újat.

![képernyőképet a tárolási célok linkről az oldalsávon, a Beállítás cím alatt, amely a Beállítások és a Figyelés kategóriafejlécek között található](media/hpc-cache-storage-targets-sidebar.png)

## <a name="add-a-new-azure-blob-storage-target"></a>Új Azure Blob-tárolócél hozzáadása

Egy új Blob storage-tároló szüksége van egy üres Blob-tároló vagy egy tároló, amely az Azure HPC Cache felhőalapú fájlrendszer formátumban feltöltött adatokkal van feltöltve. További információ a Blob-tároló előzetes betöltéséről az [Adatok áthelyezése az Azure Blob storage-ba szolgáltatásban.](hpc-cache-ingest.md)

Közvetlenül a hozzáadás előtt új tárolót hozhat létre erről a lapról.

![képernyőkép a tárolási cél hozzáadása lapról, amely egy új Azure Blob-tároló céladataival van feltöltve](media/hpc-cache-add-blob.png)

Azure Blob-tároló definiálásához adja meg ezt az információt.

* **Tárolási cél név** – Állítson be egy nevet, amely azonosítja ezt a tárolási célt az Azure HPC-gyorsítótárban.
* **Céltípusa** - Válassza **a Blob**lehetőséget.
* **Tárfiók** – Válassza ki a használni kívánt fiókot.

  A tárfiók eléréséhez engedélyeznie kell a gyorsítótár-példányt a hozzáférési szerepkörök hozzáadása című részben [leírtak szerint.](#add-the-access-control-roles-to-your-account)

  A használható tárfiók használatáról a [Blob-tárolási követelmények című](hpc-cache-prereqs.md#blob-storage-requirements)információt olvassa el.

* **Tároló –** Válassza ki a blobtárolót ehhez a célhoz, vagy kattintson **az Új létrehozása gombra.**

  ![képernyőkép a párbeszédpanelről az új tároló nevének és hozzáférési szintjének megadásához](media/add-blob-new-container.png)

* **Virtuális névtér elérési útja** – Állítsa be a tárolócél ügyfélfelé néző fájlelérési útját. Olvassa [el Az összesített névtér konfigurálása](hpc-cache-namespace.md) című olvasni, ha többet szeretne megtudni a virtuális névtér szolgáltatásról.

Ha végzett, kattintson az **OK** gombra a tárolási cél hozzáadásához.

> [!NOTE]
> Ha a tárfiók tűzfala úgy van beállítva, hogy csak a "kijelölt hálózatokra" korlátozza a hozzáférést, használja a [Munkaterület tárfiók tűzfalbeállításaiban](hpc-cache-blob-firewall-fix.md)dokumentált ideiglenes kerülő lehetőséget.

### <a name="add-the-access-control-roles-to-your-account"></a>Hozzáférés-vezérlési szerepkörök hozzáadása a fiókhoz

Az Azure HPC cache [szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/index) használatával engedélyezi a gyorsítótár-szolgáltatást az Azure Blob storage-tárolók tárfiókjának eléréséhez.

A tárfiók tulajdonosának explicit módon hozzá kell adnia a [storage-fiók közreműködői szerepköröket](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor) és a [Storage Blob Data Contributor szerepköröket](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) a "HPC cache erőforrás-szolgáltató" felhasználóhoz.

Ezt előre megteheti, vagy kattintson egy hivatkozásra azon a lapon, ahol blob tárolási célt ad hozzá. Ne feledje, hogy akár öt percet is igénybe vehet, amíg a szerepkör-beállítások propagálják az Azure-környezetben, ezért várjon néhány percet a szerepkörök hozzáadása után, mielőtt létrehozna egy tárolási célt.

Az RBAC-szerepkörök hozzáadásának lépései:

1. Nyissa meg a **tárfiók hozzáférés-vezérlési (IAM)** lapját. (A **Tárhozzáadása céllapon** lévő hivatkozás automatikusan megnyitja ezt a lapot a kijelölt fiókhoz.)

1. Kattintson **+** a lap tetejére, és válassza **a Szerepkör-hozzárendelés hozzáadása**lehetőséget.

1. Válassza ki a "Tárfiók közreműködője" szerepkört a listából.

1. A **Hozzáférés hozzárendelése a** mezőben hagyja az alapértelmezett értéket ("Azure AD-felhasználó, csoport vagy egyszerű szolgáltatás").  

1. A **Kijelölés** mezőben keressen rá a "hpc" kifejezésre.  Ennek a karakterláncnak egy egyszerű szolgáltatásnak kell megegyeznie, amelynek neve "HPC cache erőforrás-szolgáltató". Kattintással jelölje ki a felhasználót.

   > [!NOTE]
   > Ha a "hpc" kifejezésre való keresés nem működik, próbálja meg inkább a "storagecache" karakterláncot használni. Az előzetesekben (ga előtt) részt vevő felhasználóknak előfordulhat, hogy a régi nevet kell használniuk az egyszerű szolgáltatáshoz.

1. Kattintson a **Mentés** gombra az alján.

1. Ismételje meg ezt a folyamatot a "Storage Blob Data Contributor" szerepkör hozzárendeléséhez.  

![képernyőkép a szerepkör-hozzárendelés grafikus felhasználói felületének hozzáadásáról](media/hpc-cache-add-role.png)

## <a name="add-a-new-nfs-storage-target"></a>Új NFS-tárolócél hozzáadása

Egy NFS-tároló cél több mezőt, mint a Blob storage cél. Ezek a mezők határozzák meg, hogyan érheti el a tárolóexportálást, és hogyan lehet hatékonyan gyorsítótárazni az adatokat. Emellett egy NFS-tárolócél lehetővé teszi több névtérelérési út létrehozását, ha az NFS-állomás egynél több exportálással rendelkezik.

![Képernyőkép: A tárterület céloldalának hozzáadása nfs-céllal](media/add-nfs-target.png)

> [!NOTE]
> NFS-tárolócél létrehozása előtt győződjön meg arról, hogy a tárolórendszer elérhető az Azure HPC-gyorsítótárból, és megfelel az engedélykövetelményeknek. A tárolási cél létrehozása sikertelen lesz, ha a gyorsítótár nem tud hozzáférni a tárolórendszerhez. Olvassa el [az NFS tárolási követelményeit,](hpc-cache-prereqs.md#nfs-storage-requirements) és a részletekért [hárítsa el a NAS-konfigurációt és az NFS-tárolási célproblémákat.](troubleshoot-nas.md)

Adja meg ezt az információt egy NFS-támogatott tárolási célhoz:

* **Tárolási cél név** – Állítson be egy nevet, amely azonosítja ezt a tárolási célt az Azure HPC-gyorsítótárban.

* **Céltípusa** - Válassza az **NFS**lehetőséget.

* **Állomásnév** – Adja meg az NFS-tárolórendszer IP-címét vagy teljesen minősített tartománynevét. (Csak akkor használjon tartománynevet, ha a gyorsítótár hozzáfér egy DNS-kiszolgálóhoz, amely fel tudja oldani a nevet.)

* **Használati modell** – Válasszon egyet a munkafolyamat on alapuló adat-gyorsítótárazási profilok közül, amelyet az alábbi [Használati modell kiválasztása](#choose-a-usage-model)című részben ismertetett.

### <a name="nfs-namespace-paths"></a>NFS névtérelérési utak

Az NFS-tárolócéljának több virtuális elérési útja is lehet, feltéve, hogy minden elérési út egy másik exportálást vagy alkönyvtárat jelöl ugyanazon a tárolórendszeren.

Hozza létre az összes elérési utat egy tárolási célból.

A [tárolótároló-tárolóknévtér-elérési útjait](hpc-cache-edit-storage.md) bármikor hozzáadhatja és szerkesztheti.

Töltse ki ezeket az értékeket az egyes névtérelérési utakhoz:

* **Virtuális névtér elérési útja** – Állítsa be a tárolócél ügyfélfelé néző fájlelérési útját. Olvassa [el Az összesített névtér konfigurálása](hpc-cache-namespace.md) című olvasni, ha többet szeretne megtudni a virtuális névtér szolgáltatásról.

* **NFS exportálási útvonal** – Adja meg az NFS-exportálás elérési útját.

* **Alkönyvtár elérési útja** – Ha az exportálás egy adott alkönyvtárát szeretné csatlakoztatni, írja be ide. Ha nem, hagyja üresen ezt a mezőt.

Ha végzett, kattintson az **OK** gombra a tárolási cél hozzáadásához.

### <a name="choose-a-usage-model"></a>Használati modell kiválasztása
<!-- referenced from GUI - update aka.ms link if you change this heading -->

Amikor olyan tárolási célt hoz létre, amely egy NFS-tárolórendszerre mutat, ki kell választania az adott cél használati modelljét. Ez a modell határozza meg az adatok gyorsítótárazásának módját.

Három beállítás érhető el:

* **Olvasson nehéz, ritkán írásokat** – Akkor használja ezt a beállítást, ha fel szeretné gyorsítani a statikus vagy ritkán módosított fájlok olvasási hozzáférését.

  Ez a beállítás gyorsítótárazza az ügyfelek által olvasott fájlokat, de azonnal átjut a háttértárolóba. A gyorsítótárban tárolt fájlokat a rendszer soha nem hasonlítja össze az NFS tárolóköteten lévő fájlokkal.

  Ne használja ezt a beállítást, ha fennáll annak a veszélye, hogy egy fájl közvetlenül a tárolórendszeren módosul anélkül, hogy először a gyorsítótárba írna. Ebben az esetben a fájl gyorsítótárazott verziója soha nem frissül a háttérrendszer módosításaival, és az adatkészlet inkonzisztenssé válhat.

* **Több mint 15%-os írási sebesség** – Ez a beállítás felgyorsítja az olvasási és írási teljesítményt. Ha ezt a beállítást használja, minden ügyfélnek az Azure HPC-gyorsítótáron keresztül kell hozzáférnie a fájlokhoz, és nem kell közvetlenül a háttértárolót hozzácsatlakoztatnia. A gyorsítótárazott fájlok legutóbbi módosításai nem a háttérrendszeren tárolódnak.

  Ebben a használati modellben a gyorsítótárban lévő fájlok nincsenek összevetve a háttértárolóban lévő fájlokkal. A fájl gyorsítótárazott verziója aktuálisabbnak tekinthető. A gyorsítótárban lévő módosított fájl a háttértároló rendszerbe kerül, miután egy órán keresztül a gyorsítótárban volt, további módosítások nélkül.

* **Az ügyfelek az NFS-tárolóba írnak, megkerülve a gyorsítótárat** – Válassza ezt a lehetőséget, ha a munkafolyamatbármely ügyfele közvetlenül a tárolórendszerbe írja az adatokat anélkül, hogy először a gyorsítótárba írna. Az ügyfelek által kért fájlok gyorsítótárba kerülnek, de az ügyféltől származó fájlok módosításai azonnal visszakerülnek a háttértároló rendszerbe.

  Ezzel a használati modellel a gyorsítótárban lévő fájlokat gyakran ellenőrzik a frissítések háttérverzióival. Ez az ellenőrzés lehetővé teszi a fájlok módosítását a gyorsítótáron kívül, miközben az adatok konzisztenciáját.

Ez a táblázat a használati modell különbségeit foglalja össze:

| Használati modell | Gyorsítótárazási mód | Háttérellenőrzés | Maximális visszaírási késleltetés |
| ---- | ---- | ---- | ---- |
| Olvasnehéz, ritkán írások | Olvasás | Never (Soha) | None |
| Több mint 15%-os írások | Olvasás/írás | Never (Soha) | 1 óra |
| Az ügyfelek megkerülik a gyorsítótárat | Olvasás | 30 másodperc | None |

## <a name="next-steps"></a>További lépések

A tárolási célok létrehozása után vegye figyelembe az alábbi feladatok egyikét:

* [Az Azure HPC-gyorsítótár csatlakoztatása](hpc-cache-mount.md)
* [Adatok áthelyezése az Azure Blob storage-ba](hpc-cache-ingest.md)

Ha frissítenie kell a beállításokat, [szerkesztheti a tárolási célt.](hpc-cache-edit-storage.md)
