---
title: Lemezek kizárása a replikációból, állítson be az Azure Site Recovery szolgáltatással vészhelyreállítás |} A Microsoft Docs
description: Ismerteti, hogyan lehet Virtuálisgép-lemezek kizárása a replikációból a vészhelyreállítás során az Azure-bA.
author: nsoneji
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: nisoneji
ms.openlocfilehash: 851da02ebd7486c0e96451a14086eb95d81968b4
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50213202"
---
# <a name="exclude-disks-from-replication"></a>Lemezek kizárása a replikációból
Ez a cikk azt mutatja be, hogyan zárhatóak ki lemezek a replikációból. A kizárás segítségével optimalizálható a felhasznált replikációs sávszélesség, valamint optimalizálhatók a lemezek által felhasznált céloldali erőforrások.

## <a name="supported-scenarios"></a>Támogatott esetek
**Funkció** | **VMware – Azure** | **Hyper-V – Azure** | **Azure – Azure**| **Hyper-V – Hyper-V** 
--|--|--|--|--
Lemez kizárása | Igen | Igen | Nem | Nem

## <a name="why-exclude-disks-from-replication"></a>Miért érdemes lemezeket kizárni a replikációból?
A lemezek kizárása a replikációból az alábbiak miatt lehet szükséges:

- A kizárt lemezen előforduló adatváltozások nem fontosak, vagy nincs szükség azok replikálására.

- Tárterületet és hálózati erőforrásokat szeretne megtakarítani az adatváltozások replikálásának kihagyásával.

## <a name="what-are-the-typical-scenarios"></a>Melyek a tipikus forgatókönyvek?
Azonosíthatók az adatváltozások egyes típusai, amelyek a leginkább alkalmasak a kizárásra. Ilyen például a lapozófájlba (pagefile.sys) vagy a Microsoft SQL Server tempdb fájljába való írás. A számítási feladattól, valamint a tárolási alrendszertől függően a lapozófájl jelentős mértékű változást rögzíthet. Az ilyen adatoknak az elsődleges helyről az Azure-ba irányuló replikálása azonban rendkívül erőforrás-igényes lenne. Így az alábbi lépésekkel optimalizálhatja azon virtuális gépek replikálását, amelyek egyetlen virtuális lemeze az operációs rendszert és a lapozófájlt is tartalmazza:

1. Ossza fel a virtuális lemezt két virtuális lemezre. Az egyik virtuális lemezen az operációs rendszer, a másikon a lapozófájl található.
2. Zárja ki a lapozófájl lemezét a replikációból.

Ugyanígy az alábbi lépésekkel optimalizálhatja az olyan virtuális gépek replikálását, amelyek a Microsoft SQL Server tempdb fájlját és a rendszeradatbázis-fájlt is tartalmazzák:

1. Tárolja a rendszeradatbázist és a tempdb fájlt két különböző lemezen.
2. Zárja ki a tempdb lemezét a replikációból.

## <a name="how-to-exclude-disks"></a>Lemezek kizárása
Ha az Azure Site Recovery portálról szeretne virtuális gépet védelemmel ellátni, kövesse a [Replikálás engedélyezése](site-recovery-hyper-v-site-to-azure.md) munkafolyamatot. A munkafolyamat negyedik lépésében a **REPLIKÁLNI KÍVÁNT LEMEZ** oszlop használatával zárhat ki lemezeket a replikációból. Alapértelmezés szerint az összes lemez ki van jelölve replikációra. Törölje azoknak a lemezeknek a jelölését, amelyeket ki szeretne zárni a replikációból, majd végezze el a lépéseket a replikáció engedélyezéséhez.

![Lemezek kizárása a replikációból és a Hyper-V replikációjának engedélyezése Azure-beli feladat-visszavételhez](./media/hyper-v-exclude-disk/enable-replication6-with-exclude-disk.png)

>[!NOTE]
>
> * Csak az alaplemezek zárhatók ki a replikációból. Nem zárhatja ki az operációsrendszer-lemezeket. Azt javasoljuk, hogy ne zárja ki a dinamikus lemezeket. Az Azure Site Recovery nem tudja azonosítani, melyik virtuális merevlemez (VHD) alapszintű és melyik dinamikus a vendég virtuális gépen.  Ha nem zárja ki az összes függő dinamikus kötet lemezét, a védett dinamikus lemezek hibás lemezzé válnak a feladatátvételi virtuális gépen, és a lemezen lévő adatok nem lesznek elérhetőek.
> * A replikáció engedélyezése után már nem lehet ahhoz lemezeket hozzáadni, vagy lemezeket eltávolítani belőle. Lemez hozzáadásához vagy eltávolításához le kell tiltania, majd újra engedélyeznie kell a virtuális gép védelmét.
> * Ha kizár egy olyan lemezt, amely valamely alkalmazás működéséhez szükséges, az Azure-ba történő feladatátvétel esetén manuálisan létre kell hoznia a lemezt az Azure-ban, hogy a replikált alkalmazás futtatható legyen. Másik megoldásként integrálhatja az Azure Automationt egy helyreállítási tervbe a lemez a gép feladatátvétele során való létrehozásához.
> * Az Azure-ban manuálisan létrehozott lemezek nem vesznek részt a feladatátvételben. Ha például végrehajtja három lemez feladatátvételét, két lemezt pedig közvetlenül az Azure virtuális gépeken hoz létre, csak a feladatátvételben részt vevő három lemezen lesz végrehajtva az Azure-ból a Hyper-V-re történő feladat-visszavétel. A manuálisan létrehozott lemezek nem vehetők fel a feladat-visszavételbe vagy a Hyper-V-ről az Azure-ba történő visszirányú replikálásba.

## <a name="end-to-end-scenarios-of-exclude-disks"></a>Lemezek kizárásának teljes körű forgatókönyvei
Az alábbiakban két forgatókönyvet találhat a lemezek kizárása funkció megértése érdekében:

- SQL Server tempdb lemeze
- Lapozófájl (pagefile.sys) lemeze

## <a name="excample-1-exclude-the-sql-server-tempdb-disk"></a>1. példa: SQL Server tempdb adatbázist tartalmazó lemezének kizárása
Vegyünk egy SQL Server virtuális gépet, amely tempdb-adatbázisa kizárható.

A virtuális gép neve a SalesDB-ben.

A forrás virtuális gépen lévő lemezek a következők:


**Lemez neve** | **Vendég operációsrendszer-lemez száma** | **Meghajtó betűjele** | **A lemez adattípusa**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Operációsrendszer-lemez
DB-Disk1| Disk1 | D:\ | SQL-rendszeradatbázis és 1. felhasználói adatbázis
DB-Disk2 (a lemez ki lett zárva a védelemből) | Disk2 | E:\ | Ideiglenes fájlok
DB-Disk3 (a lemez ki lett zárva a védelemből) | Disk3 | F:\ | SQL tempdb-adatbázis (mappa elérési útja (F:\MSSQL\Data\) </br /> </br />a feladatátvétel előtt írja le a mappa elérési útját.
DB-Disk4 | Disk4 |G:\ |2. felhasználói adatbázis

Mivel a virtuális gép két lemezén ideiglenes az adatváltozás, a SalesDB virtuális gép védelme során zárja ki a Disk2 és a Disk3 lemezt a replikációból. Az Azure Site Recovery nem replikálja ezeket a lemezeket. A feladatátvétel során ezek a lemezek nem lesznek jelen a feladatátvételi virtuális gépen az Azure-ban.

A feladatátvétel után az Azure virtuális gépen lévő lemezek a következők:

**Vendég operációsrendszer-lemez száma** | **Meghajtó betűjele** | **A lemez adattípusa**
--- | --- | ---
DISK0 | C:\ | Operációsrendszer-lemez
Disk1 | E:\ | Ideiglenes tároló</br /> </br />ezt a lemezt az Azure adja hozzá, és az első elérhető betűjellel látja el.
Disk2 | D:\ | SQL-rendszeradatbázis és 1. felhasználói adatbázis
Disk3 | G:\ | 2. felhasználói adatbázis

Mivel a Disk2 és a Disk3 lemez ki lett zárva a SalesDB virtuális gépből, az E: az első elérhető meghajtóbetűjel a listában. Az Azure hozzárendeli az E: betűjelet az ideiglenes tárolókötethez. A meghajtó betűjelei minden replikált lemez esetében ugyanazok maradnak.

A Disk3 lemez, amely az SQL-kiszolgáló tempdb-adatbázisát tartalmazta (tempdb mappa elérési útja: F:\MSSQL\Data\), ki lett zárva a replikációból. A lemez nem lesz elérhető a feladatátvételi virtuális gépen. Ennek következtében az SQL szolgáltatás leállított állapotba kerül, és az F:\MSSQL\Data elérési utat igényli.

Kétféleképpen hozhatja létre ezt az elérési utat:

- Új lemez hozzáadásával és a tempdb mappa elérési útjának hozzárendelésével.
- Meglévő ideiglenes tárolólemez használatával a tempdb mappa elérési útjaként.

### <a name="add-a-new-disk"></a>Új lemez hozzáadása:

1. A feladatátvétel előtt írja le az SQL tempdb.mdf és tempdb.ldf fájljainak elérési útját.
2. Az Azure Portalon adjon hozzá egy új lemezt a feladatátvételi virtuális géphez, amelynek mérete legalább akkora, mint a forrásként szolgáló SQL-kiszolgáló tempdb-adatbázisát tartalmazó lemezé (Disk3).
3. Jelentkezzen be az Azure virtuális gépre. A lemezkezelési (diskmgmt.msc) konzolból inicializálja és formázza az újonnan hozzáadott lemezt.
4. Rendelje hozzá ugyanazt a meghajtóbetűjelet, amelyet az SQL-kiszolgáló tempdb-adatbázisát tartalmazó lemez (F:) használt.
5. Hozza létre a tempdb mappát az F: köteten (F:\MSSQL\Data).
6. Indítsa el az SQL szolgáltatást a szolgáltatáskonzolról.

### <a name="use-an-existing-temporary-storage-disk-for-the-sql-tempdb-folder-path"></a>Használjon meglévő ideiglenes tárolólemezt az SQL tempdb mappa elérési útjaként:

1. Nyisson meg egy parancssort.
2. Futtassa az SQL Servert helyreállítási módban a parancssorból.

        Net start MSSQLSERVER /f / T3608

3. Futtassa a következő sqlcmd parancsot a tempdb elérési útjának az új elérési útra történő módosításához.

        sqlcmd -A -S SalesDB        **Use your SQL DBname**
        USE master;     
        GO      
        ALTER DATABASE tempdb       
        MODIFY FILE (NAME = tempdev, FILENAME = 'E:\MSSQL\tempdata\tempdb.mdf');
        GO      
        ALTER DATABASE tempdb       
        MODIFY FILE (NAME = templog, FILENAME = 'E:\MSSQL\tempdata\templog.ldf');       
        GO


4. Állítsa le a Microsoft SQL Server szolgáltatást.

        Net stop MSSQLSERVER
5. Indítsa el a Microsoft SQL Server szolgáltatást.

        Net start MSSQLSERVER

Az ideiglenes tárolólemezzel kapcsolatos Azure-irányelvekről az alábbi cikkekben olvashat:

* [SSD meghajtók használata Azure virtuális gépeken az SQL Server tempdb-adatbázisának és pufferkészlet-bővítményeinek tárolására](https://blogs.technet.microsoft.com/dataplatforminsider/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)
* [Ajánlott eljárások az SQL Server teljesítményének Azure Virtual Machines szolgáltatásbeli növeléséhez](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance)

## <a name="failback-from-azure-to-an-on-premises-host"></a>Feladat-visszavétel (Azure-ból helyszíni gazdagépre)
Most pedig tekintsük át, melyik lemezek lesznek replikálva, amikor feladatátvételt hajt végre az Azure-ból a helyi Hyper-V-gazdagépre. Az Azure szolgáltatásban manuálisan létrehozott lemezek nem lesznek replikálva. Ha például végrehajtja három lemez feladatátvételét, kettőt pedig közvetlenül az Azure virtuális gépeken hoz létre, csak a feladatátvételben részt vevő három lemezen lesz végrehajtva a feladat-visszavétel. A manuálisan létrehozott lemezek nem vehetők fel a feladat-visszavételbe vagy a helyszínről az Azure-ba történő védelem-újrabeállításba. A rendszer továbbá az ideiglenes tárolólemezeket sem replikálja a helyszíni gazdagépekre.

### <a name="failback-to-original-location-recovery"></a>Feladat-visszavétel eredeti helyre való helyreállítás céljából

Az előző példában az Azure virtuális gép lemezkonfigurációja a következő:

**Vendég operációsrendszer-lemez száma** | **Meghajtó betűjele** | **A lemez adattípusa**
--- | --- | ---
DISK0 | C:\ | Operációsrendszer-lemez
Disk1 | E:\ | Ideiglenes tároló</br /> </br />ezt a lemezt az Azure adja hozzá, és az első elérhető betűjellel látja el.
Disk2 | D:\ | SQL-rendszeradatbázis és 1. felhasználói adatbázis
Disk3 | G:\ | 2. felhasználói adatbázis

Amikor a feladat-visszavétel az eredeti helyre történik, a feladat-visszavételi virtuális gép lemezkonfigurációja ugyanaz marad, mint a Hyper-V eredeti virtuálisgép-lemezkonfigurációja. A Hyper-V hely–Azure replikációból kizárt lemezek elérhetőek a feladat-visszavételi virtuális gépen.

A tervezett, Azure-ból helyszíni Hyper-V-re történő feladatátvétel után a Hyper-V virtuális gépen (eredeti hely) lévő lemezek a következők:

**Lemez neve** | **Vendég operációsrendszer-lemez száma** | **Meghajtó betűjele** | **A lemez adattípusa**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 |   C:\ | Operációsrendszer-lemez
DB-Disk1 | Disk1 | D:\ | SQL-rendszeradatbázis és 1. felhasználói adatbázis
DB-Disk2 (kizárt lemez) | Disk2 | E:\ | Ideiglenes fájlok
DB-Disk3 (kizárt lemez) | Disk3 | F:\ | SQL tempdb-adatbázis (mappa elérési útja (F:\MSSQL\Data\)
DB-Disk4 | Disk4 | G:\ | 2. felhasználói adatbázis

## <a name="example-2-exclude-the-paging-file-pagefilesys-disk"></a>2. példa: A lapozófájl (pagefile.sys) lemezének kizárása

Vegyünk egy virtuális gépet, amely lapozófájllemeze kizárható.
Két eset létezik.

### <a name="case-1-the-paging-file-is-configured-on-the-d-drive"></a>1. eset: A lapozófájl a D: meghajtón van konfigurálva
Itt láthatja a lemezkonfigurációt:

**Lemez neve** | **Vendég operációsrendszer-lemez száma** | **Meghajtó betűjele** | **A lemez adattípusa**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Operációsrendszer-lemez
DB-Disk1 (a lemez ki lett zárva a védelemből) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Felhasználói adatok, 1
DB-Disk3 | Disk3 | F:\ | Felhasználói adatok, 2

Itt láthatja a forrás virtuális gép lapozófájl-beállításait:

![Lapozófájl-beállítások a forrás virtuális gépen](./media/hyper-v-exclude-disk/pagefile-on-d-drive-sourceVM.png)

A virtuális gép Hyper-V–Azure feladatátvétele után az Azure-beli virtuális gépen lévő lemezek a következők:

**Lemez neve** | **Vendég operációsrendszer-lemez száma** | **Meghajtó betűjele** | **A lemez adattípusa**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Operációsrendszer-lemez
DB-Disk1 | Disk1 | D:\ | Ideiglenes tároló</br /> </br />pagefile.sys
DB-Disk2 | Disk2 | E:\ | Felhasználói adatok, 1
DB-Disk3 | Disk3 | F:\ | Felhasználói adatok, 2

Mivel a Disk1 nevű lemez (D:) ki lett zárva, a D: az első elérhető meghajtóbetűjel a listában. Az Azure a D: betűjelet rendeli hozzá az ideiglenes tárolókötethez. Mivel a D: meghajtó elérhető az Azure virtuális gépen, a virtuális gép lapozófájl-beállításai nem változnak.

Itt láthatja az Azure virtuális gép lapozófájl-beállításait:

![Lapozófájl-beállítások az Azure virtuális gépen](./media/hyper-v-exclude-disk/pagefile-on-Azure-vm-after-failover.png)

### <a name="case-2-the-paging-file-is-configured-on-another-drive-other-than-d-drive"></a>2. eset: A lapozófájl másik meghajtón van konfigurálva (nem a D: meghajtón)

Itt láthatja a forrás virtuális gép lemezkonfigurációját:

**Lemez neve** | **Vendég operációsrendszer-lemez száma** | **Meghajtó betűjele** | **A lemez adattípusa**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Operációsrendszer-lemez
DB-Disk1 (a lemez ki lett zárva a védelemből) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Felhasználói adatok, 1
DB-Disk3 | Disk3 | F:\ | Felhasználói adatok, 2

Itt láthatja a helyszíni virtuális gép lapozófájl-beállításait:

![Lapozófájl-beállítások a helyszíni virtuális gépen](./media/hyper-v-exclude-disk/pagefile-on-g-drive-sourceVM.png)

A virtuális gép Hyper-V–Azure feladatátvétele után az Azure-beli virtuális gépen lévő lemezek a következők:

**Lemez neve**| **Vendég operációsrendszer-lemez száma**| **Meghajtó betűjele** | **A lemez adattípusa**
--- | --- | --- | ---
DB-Disk0-OS | DISK0  |C:\ |Operációsrendszer-lemez
DB-Disk1 | Disk1 | D:\ | Ideiglenes tároló</br /> </br />pagefile.sys
DB-Disk2 | Disk2 | E:\ | Felhasználói adatok, 1
DB-Disk3 | Disk3 | F:\ | Felhasználói adatok, 2

Mivel a D: az első elérhető meghajtóbetűjel a listában, az Azure ezt a betűjelet rendeli hozzá az ideiglenes tárolókötethez. A meghajtó betűjele minden replikált lemez esetében ugyanaz marad. Mivel a G: lemez nem érhető el, a rendszer a C: meghajtót használja a lapozófájlhoz.

Itt láthatja az Azure virtuális gép lapozófájl-beállításait:

![Lapozófájl-beállítások az Azure virtuális gépen](./media/hyper-v-exclude-disk/pagefile-on-Azure-vm-after-failover-2.png)

## <a name="next-steps"></a>További lépések
Ha sikerült beállítania és elindítani az üzemelő példányt, [ismerkedjen meg részletesebben](site-recovery-failover.md) a feladatátvételi különféle típusaival.
