---
title: Tároló hozzáadása egy Azure HPC-gyorsítótárhoz
description: Tárolási célok meghatározása úgy, hogy az Azure HPC-gyorsítótára használhassa a helyszíni NFS-rendszert vagy az Azure Blob-tárolókat a hosszú távú fájlok tárolásához
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: v-erkel
ms.openlocfilehash: 9d08526334a9891788df63b156cdf65f55c587ca
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84344934"
---
# <a name="add-storage-targets"></a>Céltárak hozzáadása

A *tárolási célok* az Azure HPC-gyorsítótáron keresztül elért fájlok háttérbeli tárolói. Hozzáadhat NFS-tárolót (például helyszíni hardverrendszer), vagy tárolhat adattárolást az Azure blobban.

Akár tíz különböző tárolási célt is meghatározhat egy gyorsítótárhoz. A gyorsítótár egy összesített névtérben jeleníti meg az összes tárolási célt.

Ne feledje, hogy a tárolók exportálásának elérhetőnek kell lennie a gyorsítótár virtuális hálózatáról. A helyszíni hardveres tároláshoz előfordulhat, hogy olyan DNS-kiszolgálót kell beállítania, amely képes az NFS-tároló elérésére szolgáló gazdagépek feloldására. További információk: [DNS-hozzáférés](hpc-cache-prereqs.md#dns-access).

Adja hozzá a tárolási célokat a gyorsítótár létrehozása után. Az eljárás némileg eltérő attól függően, hogy az Azure Blob Storage-t vagy egy NFS-exportálást ad hozzá. A részleteket az alábbiakban találja.

Az alábbi képre kattintva megtekintheti a gyorsítótár létrehozásának és a tárolási cél hozzáadásának [bemutató videóját](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) .

[![videó miniatűrje: Azure HPC cache: Setup (kattintson ide a videó oldal megtekintéséhez)](media/video4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="open-the-storage-targets-page"></a>A tárolási célok lap megnyitása

A Azure Portal nyissa meg a gyorsítótár-példányt, és kattintson a bal oldali oldalsávon található **tárolási célok** elemre. A tárolási célok lap felsorolja az összes meglévő célt, és egy hivatkozást ad hozzá egy újat.

![képernyőfelvétel a Storage Targets hivatkozásáról az oldalsávon, a configure (Konfigurálás) fejléc alatt, amely a kategória fejlécek beállításai és figyelése között található.](media/hpc-cache-storage-targets-sidebar.png)

## <a name="add-a-new-azure-blob-storage-target"></a>Új Azure Blob Storage-cél hozzáadása

Egy új blob Storage-célnak szüksége van egy üres blob-tárolóra vagy egy olyan tárolóra, amely az Azure HPC cache Cloud File System formátumában található adatokkal van feltöltve. További információ a blob-tárolók [Azure Blob Storage-ba való áthelyezésének](hpc-cache-ingest.md)előzetes betöltéséről.

A lap hozzáadása előtt új tárolót is létrehozhat.

![képernyőfelvétel a Storage-cél hozzáadása oldalról, amely az új Azure Blob Storage-tárolóra vonatkozó információkkal van feltöltve](media/hpc-cache-add-blob.png)

Azure Blob-tároló definiálásához adja meg ezt az információt.

* **Tárolási cél neve** – adjon meg egy nevet, amely azonosítja ezt a tárolási célt az Azure HPC cache-ben.
* **Cél típusa** – válassza a **blob**lehetőséget.
* **Storage-fiók** – válassza ki a használni kívánt fiókot.

  Engedélyeznie kell a gyorsítótár-példányt a Storage-fiók eléréséhez a [hozzáférési Szerepkörök hozzáadása](#add-the-access-control-roles-to-your-account)című témakörben leírtak szerint.

  A használható Storage-fiókkal kapcsolatos információkért olvassa el a [blob Storage-követelmények](hpc-cache-prereqs.md#blob-storage-requirements)című témakört.

* **Storage-tároló** – jelölje ki a cél blob-tárolóját, vagy kattintson az **új létrehozása**gombra.

  ![képernyőkép a párbeszédpanelről az új tárolóhoz tartozó név és hozzáférési szint (Private) megadásához](media/add-blob-new-container.png)

* **Virtuális névtér elérési útja** – a tárolási cél ügyféloldali elérési útjának beállítása. A virtuális névtér szolgáltatással kapcsolatos további tudnivalókért olvassa el az [összesített névtér konfigurálása](hpc-cache-namespace.md) című témakört.

Ha elkészült, kattintson az **OK** gombra a tárolási cél hozzáadásához.

> [!NOTE]
> Ha a Storage-fiók tűzfala úgy van beállítva, hogy csak a "kiválasztott hálózatokra" korlátozza a hozzáférést, használja a [blob Storage-fiók tűzfalának](hpc-cache-blob-firewall-fix.md)használata című dokumentumban ismertetett ideiglenes megkerülő megoldást.

### <a name="add-the-access-control-roles-to-your-account"></a>Hozzáférés-vezérlési Szerepkörök hozzáadása a fiókhoz

Az Azure HPC cache [szerepköralapú hozzáférés-vezérlést (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/index) használ, hogy engedélyezze a gyorsítótár-szolgáltatás számára az Azure Blob Storage-célokhoz való hozzáférést a Storage-fiókhoz.

A Storage-fiók tulajdonosának explicit módon hozzá kell adnia a roles [Storage-fiók közreműködőjét](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor) és a [Storage blob adatközreműködőit](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) a "HPC cache erőforrás-szolgáltató" felhasználóhoz.

Ezt előre elvégezheti, vagy egy hivatkozásra kattintva megadhatja a blob Storage-tárolót tartalmazó oldalon. Ne feledje, hogy akár öt percet is igénybe vehet, hogy a szerepkör-beállítások továbbítsák az Azure-környezetet, ezért néhány percet várnia kell, miután hozzáadta a szerepköröket a tárolási cél létrehozása előtt.

A RBAC szerepkörök hozzáadásának lépései:

1. Nyissa meg a Storage-fiókhoz tartozó **hozzáférés-vezérlés (iam)** lapot. (A **tároló hozzáadása** lapon lévő hivatkozás automatikusan megnyitja ezt a lapot a kiválasztott fiókhoz.)

1. Kattintson a **+** lap tetején található elemre, majd válassza a **szerepkör-hozzárendelés hozzáadása**lehetőséget.

1. Válassza ki a "Storage-fiók közreműködője" szerepkört a listából.

1. A **hozzáférés kiosztása** mezőben hagyja meg a kiválasztott alapértelmezett értéket ("Azure ad-felhasználó, csoport vagy szolgáltatásnév").  

1. A **Select (kiválasztás** ) mezőben keressen rá a "HPC" kifejezésre.  Ennek a karakterláncnak meg kell egyeznie egy "HPC cache erőforrás-szolgáltató" nevű egyszerű szolgáltatással. Kattintson erre a résztvevőre a kiválasztásához.

   > [!NOTE]
   > Ha a "HPC" kifejezés nem működik, próbálja meg helyette a "storagecache" karakterláncot használni. Előfordulhat, hogy az előzetes verzióban részt vevő felhasználóknak (a GA előtt) a szolgáltatásnév régebbi nevét kell használniuk.

1. Kattintson a lap alján található **Save (Mentés** ) gombra.

1. Ismételje meg ezt a folyamatot a "Storage blob-adatközreműködői" szerepkör hozzárendeléséhez.  

![a szerepkör-hozzárendelés grafikus felhasználói felületének hozzáadása képernyőkép](media/hpc-cache-add-role.png)

## <a name="add-a-new-nfs-storage-target"></a>Új NFS-tárolási cél hozzáadása

Az NFS-tárolási cél több mezővel rendelkezik, mint a blob Storage-cél. Ezek a mezők határozzák meg, hogyan érheti el a tároló exportálását, és hogyan lehet hatékonyan gyorsítótárazni az adattárakat. Emellett az NFS-tárolási cél lehetővé teszi több névtér elérési útjának létrehozását, ha az NFS-állomás több exportálási lehetőséggel rendelkezik.

![Képernyőfelvétel a Storage-cél hozzáadása oldalról a megadott NFS-célként](media/add-nfs-target.png)

> [!NOTE]
> Az NFS-tárolási cél létrehozása előtt győződjön meg arról, hogy a tárolási rendszer elérhető az Azure HPC-gyorsítótárból, és megfelel az engedélyek követelményeinek. A tárolási cél létrehozása sikertelen lesz, ha a gyorsítótár nem fér hozzá a tárolási rendszerhez. A részletekért olvassa el az [NFS-tárolási követelmények](hpc-cache-prereqs.md#nfs-storage-requirements) , valamint a [NAS-konfiguráció és az NFS-tárolási cél hibaelhárítása](troubleshoot-nas.md) című cikk

Adja meg ezt az információt egy NFS-alapú tárolási cél számára:

* **Tárolási cél neve** – adjon meg egy nevet, amely azonosítja ezt a tárolási célt az Azure HPC cache-ben.

* **Cél típusa** – válassza az **NFS**lehetőséget.

* **Állomásnév** – adja meg az NFS-tárolási rendszer IP-címét vagy teljesen minősített tartománynevét. (Csak akkor használjon tartománynevet, ha a gyorsítótár egy olyan DNS-kiszolgálóhoz fér hozzá, amely fel tudja oldani a nevet.)

* **Használati modell** – válassza ki az egyik adatgyorsítótárazási profilt a munkafolyamat alapján, az alábbi, [használati modell kiválasztása](#choose-a-usage-model)részben leírtak szerint.

### <a name="nfs-namespace-paths"></a>NFS-névtér elérési útjai

Egy NFS-tárolási cél több virtuális útvonallal is rendelkezhet, ha az egyes elérési utak ugyanazon a tárolási rendszeren eltérő exportálási vagy alkönyvtárat jelölnek.

Hozza létre az összes elérési utat egy tárolási tárolóból.

A [névtér elérési útját bármikor hozzáadhatja és szerkesztheti](hpc-cache-edit-storage.md) a tárolási célra.

Adja meg ezeket az értékeket az egyes névterek elérési útjához:

* **Virtuális névtér elérési útja** – a tárolási cél ügyféloldali elérési útjának beállítása. A virtuális névtér szolgáltatással kapcsolatos további tudnivalókért olvassa el az [összesített névtér konfigurálása](hpc-cache-namespace.md) című témakört.

* **NFS-exportálási útvonal** – adja meg az NFS-exportálás elérési útját.

* **Alkönyvtár elérési útja** – ha az Exportálás egy adott alkönyvtárát szeretné csatlakoztatni, írja be ide. Ha nem, hagyja üresen ezt a mezőt.

Ha elkészült, kattintson az **OK** gombra a tárolási cél hozzáadásához.

### <a name="choose-a-usage-model"></a>Használati modell kiválasztása
<!-- referenced from GUI - update aka.ms link if you change this heading -->

Amikor egy NFS Storage rendszerre mutató tárolási célt hoz létre, ki kell választania az adott cél használati modelljét. Ez a modell határozza meg, hogyan gyorsítótárazza az adatait.

Három beállítás érhető el:

* **Súlyos, ritka írások olvasása** – ezt a beállítást akkor használja, ha a statikus vagy ritkán módosított fájlok olvasási hozzáférését szeretné felgyorsítani.

  Ez a beállítás gyorsítótárazza az ügyfelek által olvasott fájlokat, de azonnal átadja az írást a háttér-tárolóra. A gyorsítótárban tárolt fájlokat a rendszer soha nem hasonlítja össze az NFS-tároló kötetén található fájlokkal.

  Ne használja ezt a beállítást, ha fennáll a kockázata annak, hogy egy fájl közvetlenül a tárolási rendszeren módosul, anélkül, hogy először a gyorsítótárba kellene írni. Ha ez történik, a rendszer soha nem frissíti a fájl gyorsítótárazott verzióját a háttér változásaival, és az adathalmaz inkonzisztens lehet.

* **15%-nál nagyobb írások** – ez a beállítás az olvasási és írási teljesítményt is felgyorsítja. Ha ezt a beállítást használja, az összes ügyfélnek az Azure HPC cache-en keresztül kell hozzáférnie a fájlokhoz ahelyett, hogy közvetlenül a háttér-tárolót kellene csatlakoztatnia. A gyorsítótárazott fájlok legutóbbi módosításai a háttérben nem tárolódnak.

  Ebben a használati modellben a gyorsítótárban lévő fájlokat a rendszer nem ellenőrzi a háttérben tárolt fájlokon. A rendszer azt feltételezi, hogy a fájl gyorsítótárazott verziója nagyobb áramerősséget mutat. A gyorsítótárban lévő módosított fájl a háttér-tárolási rendszerbe kerül, miután a gyorsítótárban egy óra elteltével további módosítások nélkül megtörtént.

* Az **ügyfelek az NFS-célhelyre írhatnak, és megkerülik a gyorsítótárat** – ezt a beállítást akkor válassza, ha a munkafolyamatban lévő bármelyik ügyfél közvetlenül a tárolási rendszerbe írja az adatait anélkül, hogy először a gyorsítótárba írna. Az ügyfelek által igényelt fájlok gyorsítótárazva vannak, de a fájloknak az ügyfélről történő módosításai azonnal visszakerülnek a háttérrendszer-tároló rendszerbe.

  Ezzel a használati modellel a gyorsítótárban lévő fájlokat a rendszer gyakran ellenőrzi a frissítések háttérbeli verzióiban. Ez az ellenőrzés lehetővé teszi, hogy a fájlok a gyorsítótáron kívülre legyenek módosítva az adatkonzisztencia fenntartása mellett.

Ez a táblázat a használati modell eltéréseit foglalja össze:

| Használati modell | Gyorsítótárazási mód | Háttér-ellenőrzés | Maximális írási késleltetés |
| ---- | ---- | ---- | ---- |
| Súlyos, ritka írások olvasása | Olvasás | Soha | None |
| 15%-nál nagyobb írások | Olvasás/írás | Soha | 1 óra |
| Az ügyfelek megkerülik a gyorsítótárat | Olvasás | 30 másodperc | None |

## <a name="next-steps"></a>Következő lépések

A tárolási célok létrehozása után vegye figyelembe a következő feladatok egyikét:

* [Az Azure HPC Cache csatlakoztatása](hpc-cache-mount.md)
* [Az Azure Blob Storage-ba irányuló adatáthelyezés](hpc-cache-ingest.md)

Ha frissítenie kell a beállításokat, [szerkesztheti a tárolási célt](hpc-cache-edit-storage.md).
