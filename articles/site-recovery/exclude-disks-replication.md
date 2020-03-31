---
title: Lemezek kizárása a replikációból az Azure Site Recovery szolgáltatással
description: Hogyan zárhatja ki a lemezeket az Azure-ba az Azure Site Recovery használatával.
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 57bf06f0fde85714530c06cbd008db08de7460d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281846"
---
# <a name="exclude-disks-from-disaster-recovery"></a>Lemezek kizárása a vészhelyreállításból

Ez a cikk azt ismerteti, hogyan zárhatja ki a lemezeket a replikációból a helyszíni és az Azure-beli vészhelyreállítás során az [Azure-ban az Azure-ban.](site-recovery-overview.md) A lemezeket több okból is kizárhatja a replikációból:

- Annak ellenére, hogy a kizárt lemezen nem fontos adatok nem replikálódik.
- A felhasznált replikációs sávszélesség vagy a céloldali erőforrások optimalizálása.
- Tároló- és hálózati erőforrások mentése a szükségtelen adatok replikálásának elneme nélküli adatokkal.
- Az Azure virtuális gépei elérték a Site Recovery replikációs korlátait.


## <a name="supported-scenarios"></a>Támogatott esetek

A lemezeket a táblázatban összegzett módon kizárhatja a replikációból.

**Azure – Azure** | **VMware – Azure** | **Hyper-V – Azure** 
--- | --- | ---
Igen (a PowerShell használatával) | Igen | Igen 

## <a name="exclude-limitations"></a>Korlátozások kizárása

**Korlátozás** | **Azure-beli virtuális gépek** | **VMware virtuális gépek** | **Hyper-V virtuális gépek**
--- | --- | ---
**Lemeztípusok** | Az alaplemezek kizárhatók a replikációból.<br/><br/> Az operációs rendszer lemezeit és dinamikus lemezeit nem zárhatja ki. A hőmérsékleti lemezek alapértelmezés szerint ki vannak zárva. | Az alaplemezek kizárhatók a replikációból.<br/><br/> Az operációs rendszer lemezeit és dinamikus lemezeit nem zárhatja ki. | Az alaplemezek kizárhatók a replikációból.<br/><br/> Nem zárhatja ki az operációsrendszer-lemezeket. Azt javasoljuk, hogy ne zárja ki a dinamikus lemezeket. A Site Recovery nem tudja azonosítani, hogy melyik VHS alapszintű vagy dinamikus a vendég virtuális gépben. Ha az összes függő dinamikus kötetlemezek nem zárható ki, a védett dinamikus lemez lesz egy sikertelen lemez egy feladatátvevő virtuális gép, és az adatok a lemezen nem érhető el.
**Lemez replikálása** | A replikálólemez nem zárható ki.<br/><br/> Tiltsa le és engedélyezze újra a replikációt a virtuális gép számára. |  A replikálólemez nem zárható ki. |  A replikálólemez nem zárható ki.
**Mobilitási szolgáltatás (VMware)** | Nem releváns | Csak olyan virtuális gépeken zárhatja ki a lemezeket, amelyeken telepítve van a Mobilitás szolgáltatás.<br/><br/> Ez azt jelenti, hogy manuálisan kell telepítenie a Mobilitás szolgáltatást azokra a virtuális gépekre, amelyeklemezeit ki szeretné zárni. A leküldéses telepítési mechanizmus nem használható, mert csak a replikáció engedélyezése után telepíti a Mobilitás szolgáltatást. | Nem releváns.
**Hozzáadás/eltávolítás** | A felügyelt lemezekkel rendelkező Azure virtuális gépeken lemezeket adhat hozzá és távolíthat el. | A replikáció engedélyezése után nem lehet lemezeket hozzáadni vagy eltávolítani. Tiltsa le, majd engedélyezze újra a replikációt lemez hozzáadásához. | A replikáció engedélyezése után nem lehet lemezeket hozzáadni vagy eltávolítani. Tiltsa le, majd engedélyezze újra a replikációt.
**Feladatátvétel** | Ha egy alkalmazásnak olyan lemezre van szüksége, amelyet kizárt, a feladatátvétel után manuálisan kell létrehoznia a lemezt, hogy a replikált alkalmazás futtatható anamtalán.<br/><br/> Azt is megteheti, hogy virtuális gép feladatátvétel során hozza létre a lemezt az Azure automation helyreállítási tervbe való integrálásával. | Ha kizár egy lemezt, amelyre egy alkalmazásnak szüksége van, manuálisan hozza létre az Azure-ban feladatátvétel után. | Ha kizár egy lemezt, amelyre egy alkalmazásnak szüksége van, manuálisan hozza létre az Azure-ban feladatátvétel után.
**A helyszíni feladat-visszavételi lemezek manuálisan létrehozva** | Nem releváns | **Windows virtuális gépek:** Az Azure-ban manuálisan létrehozott lemezek nem lesznek visszavételek. Ha például három lemezt sikertelenül, és két lemezt hoz létre közvetlenül egy Azure-beli virtuális gépen, csak a három lemez, amely feladatátvételt követően a feladatátvételután.<br/><br/> **Linux virtuális gépek:** Az Azure-ban manuálisan létrehozott lemezek nem sikerül vissza. Ha például három lemezt sikertelenül, és hozzon létre két lemezt egy Azure-beli virtuális gép, mind az öt sikertelen lesz vissza. A manuálisan létrehozott lemezek nem zárhatók ki a feladat-visszavételből. | Az Azure-ban manuálisan létrehozott lemezek nem hibásak. Ha például három lemezt sikertelenül, és két lemezt hoz létre közvetlenül egy Azure virtuális gépen, csak három lemez, amely feladatátvételre került, lesz feladatátvétel.
**Helyszíni feladat-visszavétel-kizárt lemezek** | Nem releváns | Ha a feladat-vissza az eredeti gép, a feladat-visszavételi virtuális gép konfigurációja nem tartalmazza a kizárt lemezeket. A VMware-ből az Azure replikációba kizárt lemezek nem érhetők el a feladat-visszavételi virtuális gépen. | Ha a feladat-visszavétel az eredeti Hyper-V helyre, a feladat-visszavételi virtuális gép konfigurációja ugyanaz marad, mint az eredeti forrás virtuális gép lemez. A Hyper-V helyről az Azure replikációba kizárt lemezek a feladat-visszavételi virtuális gépen érhetők el.



## <a name="typical-scenarios"></a>Tipikus forgatókönyvek

A kizárásra nagy valószínűségű adatforgalom például a lapozófájlba (pagefile.sys) történő írások és a Microsoft SQL Server tempdb-fájljába történő írás. A számítási feladattól és a tárolási alrendszertől függően a lapozási és tempdb fájlok jelentős mennyiségű lemorzsolódást regisztrálhatnak. Az ilyen típusú adatok Replikálása az Azure-ba erőforrás-igényes.

- Ha optimalizálni szeretné a replikációt egyetlen virtuális lemezzel rendelkező virtuális gépre, amely az operációs rendszert és a lapozófájlt is tartalmazza, a következőket teheti:
    1. Ossza fel a virtuális lemezt két virtuális lemezre. Az egyik virtuális lemezen az operációs rendszer, a másikon a lapozófájl található.
    2. Zárja ki a lapozófájl lemezét a replikációból.

- A Microsoft SQL Server tempdb fájlt és a rendszeradatbázis-fájlt egyaránt tartalmazó lemez replikációjának optimalizálásához a következőket teheti:
    1. Tárolja a rendszeradatbázist és a tempdb fájlt két különböző lemezen.
    2. Zárja ki a tempdb lemezét a replikációból.

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>1. példa: SQL Server tempdb adatbázist tartalmazó lemezének kizárása

Nézzük meg, hogyan kell kezelni a lemezkizárást, a feladatátvételt és a feladatátvételt egy sql server Windows VM - **SalesDB*** forrásesetén, amelyhez ki szeretnénk zárni a tempdb-t. 

### <a name="exclude-disks-from-replication"></a>Lemezek kizárása a replikációból

Mi van ezek a lemezek a forrás windows VM SalesDB.

**Lemez neve** | **Vendég operációsrendszer-lemez** | **Meghajtó betűjele** | **Lemez adattípusa**
--- | --- | --- | ---
DB-Disk0-OS | Lemez0 | C:\ | Operációs rendszer lemeze.
DB-Disk1| Disk1 | D:\ | SQL rendszeradatbázis és felhasználói adatbázis1.
DB-Disk2 (a lemez ki lett zárva a védelemből) | Disk2 | E:\ | Ideiglenes fájlok.
DB-Disk3 (a lemez ki lett zárva a védelemből) | Disk3 | F:\ | SQL tempdb adatbázis.<br/><br/> Mappa elérési útja - F:\MSSQL\Data\. Jegyezze fel a mappa elérési útját a feladatátvétel előtt.
DB-Disk4 | Disk4 |G:\ | 2. felhasználói adatbázis

1. Engedélyezzük a salesdb virtuális gép replikációját.
2. A Disk2 és a Disk3 adatbázisát kizárjuk a replikációból, mert az adatforgalom ideiglenes. 


### <a name="handle-disks-during-failover"></a>Lemezek lebonyolítása feladatátvétel közben

Mivel a lemezek nem replikálódik, ha feladatátvételt az Azure-ba ezek a lemezek nem jelennek meg az Azure virtuális gép a feladatátvétel után létrehozott. Az Azure virtuális gép a lemezek ebben a táblában összesítve.

**Vendég operációsrendszer-lemez** | **Meghajtó betűjele** | **Lemez adattípusa**
--- | --- | ---
Lemez0 | C:\ | Operációs rendszer lemeze.
Disk1 | E:\ | Ideiglenes tárolás<br/><br/>Az Azure hozzáadja ezt a lemezt. Mivel a Disk2 és a Disk3 kivételt kapott a replikációból, az E: az első meghajtóbetűjel a rendelkezésre álló listából. Az Azure hozzárendeli az E: betűjelet az ideiglenes tárolókötethez. A replikált lemezek egyéb meghajtóbetűi változatlanok maradnak.
Disk2 | D:\ | SQL-rendszeradatbázis és 1. felhasználói adatbázis
Disk3 | G:\ | 2. felhasználói adatbázis

A példában, mivel a Disk3, az SQL tempdb lemez, ki lett zárva a replikációból, és nem érhető el az Azure virtuális gép, az SQL szolgáltatás leállított állapotban van, és szüksége van az F:\MSSQL\Data elérési utat. Ezt az elérési utat többféleképpen is létrehozhatja: 

- A feladatátvétel után adjon hozzá egy új lemezt, és rendelje hozzá a tempdb mappa elérési útját.
- Meglévő ideiglenes tárolólemez használatával a tempdb mappa elérési útjaként.

#### <a name="add-a-new-disk-after-failover"></a>Új lemez hozzáadása feladatátvétel után

1. A feladatátvétel előtt írja le az SQL tempdb.mdf és tempdb.ldf fájljainak elérési útját.
2. Az Azure Portalon adjon hozzá egy új lemezt a feladatátvételi Azure virtuális gép. A lemez méretének (vagy nagyobbnak) kell lennie, mint a forrás SQL tempdb lemeznek (Disk3).
3. Jelentkezzen be az Azure virtuális gépbe.
4. A lemezkezelési (diskmgmt.msc) konzolból inicializálja és formázza az újonnan hozzáadott lemezt.
5. Az SQL tempdb lemez által használt meghajtóbetűjel hozzárendelése (F:)
6. Hozza létre a tempdb mappát az F: köteten (F:\MSSQL\Data).
7. Indítsa el az SQL szolgáltatást a szolgáltatáskonzolról.

#### <a name="use-an-existing-temporary-storage-disk"></a>Meglévő ideiglenes tárolólemez használata 

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



### <a name="vmware-vms-disks-during-failback-to-original-location"></a>VMware virtuális gépek: Lemezek feladat-visszavétel kor az eredeti helyre

Most nézzük meg, hogyan kezelje a lemezeket a VMware virtuális gépeken, ha az eredeti helyszíni helyre visszaadja a feladatát.

- **Az Azure-ban létrehozott lemezek:** Mivel a példában egy Windows virtuális gép, az Azure-ban manuálisan létrehozott lemezek nem replikálódik vissza a webhelyre, ha feladat-vissza, vagy újra egy virtuális gép.
- **Ideiglenes tárolólemez az Azure-ban:** Az ideiglenes tárolólemez nem replikálódik vissza a helyszíni gazdagépekre.
- **Kizárt lemezek:** A VMware-ből az Azure replikációba kizárt lemezek nem érhetők el a helyszíni virtuális gépen a feladat-visszavétel után.

Mielőtt visszaadná a VMware virtuális gépek az eredeti helyre, az Azure VM lemez beállításai a következők.

**Vendég operációsrendszer-lemez** | **Meghajtó betűjele** | **Lemez adattípusa**
--- | --- | ---
Lemez0 | C:\ | Operációs rendszer lemeze.
Disk1 | E:\ | Ideiglenes tároló.
Disk2 | D:\ | SQL rendszeradatbázis és felhasználói adatbázis1.
Disk3 | G:\ | Felhasználói adatbázis2.

A feladat-visszavétel után a VMware vm az eredeti helyen a lemezek a táblázatban összegzett.

**Vendég operációsrendszer-lemez** | **Meghajtó betűjele** | **Lemez adattípusa**
--- | --- | ---
Lemez0 | C:\ | Operációs rendszer lemeze.
Disk1 | D:\ | SQL rendszeradatbázis és felhasználói adatbázis1.
Disk2 | G:\ | Felhasználói adatbázis2.


### <a name="hyper-v-vms-disks-during-failback-to-original-location"></a>Hyper-V virtuális gépek: Lemezek feladat-visszavétel kor az eredeti helyre

Most nézzük meg, hogyan kezelje a lemezeket a Hyper-V virtuális gépeken, ha az eredeti helyszíni helyre visszaadja a feladatát.

- **Az Azure-ban létrehozott lemezek:** Az Azure-ban manuálisan létrehozott lemezek nem replikálódik vissza a webhelyre, ha feladat-vissza, vagy egy virtuális gép ismételt védelme.
- **Ideiglenes tárolólemez az Azure-ban:** Az ideiglenes tárolólemez nem replikálódik vissza a helyszíni gazdagépekre.
- **Kizárt lemezek:** A feladat-visszavétel után a virtuális gép lemezkonfigurációja megegyezik az eredeti virtuálisgép-lemez konfigurációjának konfigurációjának. A Hyper-V-ről az Azure-ra történő replikációból kizárt lemezek a feladat-visszavételi virtuális gépen érhetők el.

Mielőtt visszaadná a Hyper-V virtuális gépek az eredeti helyre, az Azure vm lemez beállításai a következők.

**Vendég operációsrendszer-lemez** | **Meghajtó betűjele** | **Lemez adattípusa**
--- | --- | ---
Lemez0 | C:\ | Operációs rendszer lemeze.
Disk1 | E:\ | Ideiglenes tároló.
Disk2 | D:\ | SQL rendszeradatbázis és felhasználói adatbázis1.
Disk3 | G:\ | Felhasználói adatbázis2.

A tervezett feladatátvétel (feladat-feladat-visszavétel) az Azure-ból a helyszíni Hyper-V, a Hyper-V virtuális gép az eredeti helyen a lemezek a táblázatban összefoglalta.

**Lemez neve** | **Vendég operációs rendszer lemezének száma** | **Meghajtó betűjele** | **Lemez adattípusa**
 --- | --- | --- | ---
DB-Disk0-OS | Lemez0 |   C:\ | Operációs rendszer lemeze.
DB-Disk1 | Disk1 | D:\ | SQL rendszeradatbázis és felhasználói adatbázis1.
DB-Disk2 (kizárt lemez) | Disk2 | E:\ | Ideiglenes fájlok.
DB-Disk3 (kizárt lemez) | Disk3 | F:\ | SQL tempdb adatbázis<br/><br/> Mappa elérési útja (F:\MSSQL\Data\).
DB-Disk4 | Disk4 | G:\ | 2. felhasználói adatbázis


## <a name="example-2-exclude-the-paging-file-disk"></a>2. példa: A lapozófájl lemezének kizárása

Nézzük meg, hogyan kell kezelni a lemezkizárást, a feladatátvételt és a feladatátvételt egy windows os virtuális géphez, amelyhez ki szeretnénk zárni a pagefile.sys fájllemezt mind a D meghajtón, mind egy másik meghajtón.


### <a name="paging-file-on-the-d-drive"></a>Lapozófájl a D meghajtón

Ezek a lemezek a forrás virtuális gép.

**Lemez neve** | **Vendég operációsrendszer-lemez** | **Meghajtó betűjele** | **Lemez adattípusa**
--- | --- | --- | ---
DB-Disk0-OS | Lemez0 | C:\ | Operációsrendszer-lemez
DB-Disk1 (Kizárás a replikációból) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Felhasználói adatok, 1
DB-Disk3 | Disk3 | F:\ | Felhasználói adatok, 2

A lapozófájl beállításai a forrás virtuális gép en a következők:

![Lapozófájl-beállítások a forrás virtuális gépen](./media/exclude-disks-replication/pagefile-d-drive-source-vm.png)

1. Engedélyezzük a virtuális gép replikációját.
2. Kizárjuk a DB-Disk1-et a replikációból.

#### <a name="disks-after-failover"></a>Lemezek feladatátvétel után

Feladatátvétel után az Azure virtuális gép a lemezek a táblázatban összegzett.

**Lemez neve** | **Vendég operációsrendszer-lemez száma** | **Meghajtó betűjele** | **A lemez adattípusa**
--- | --- | --- | ---
DB-Disk0-OS | Lemez0 | C:\ | Operációsrendszer-lemez
DB-Disk1 | Disk1 | D:\ | Ideiglenes tárolás/oldalfájl.sys <br/><br/> Mivel a DB-Disk1 (D:) kizárták, D: az első meghajtóbetűjel a rendelkezésre álló listából.<br/><br/> Az Azure a D: betűjelet rendeli hozzá az ideiglenes tárolókötethez.<br/><br/> Mivel a D: elérhető, a virtuális gép lapozófájl-beállítása ugyanaz marad).
DB-Disk2 | Disk2 | E:\ | Felhasználói adatok, 1
DB-Disk3 | Disk3 | F:\ | Felhasználói adatok, 2

A lapozófájl beállításai az Azure virtuális gép a következők:

![Lapozófájl-beállítások az Azure virtuális gépen](./media/exclude-disks-replication/pagefile-azure-vm-after-failover.png)

### <a name="paging-file-on-another-drive-not-d"></a>Lapozófájl másik meghajtón (nem D:)

Nézzünk meg egy példát, amelyben a lapozófájl nem a D meghajtón van.  

Ezek a lemezek a forrás virtuális gép.

**Lemez neve** | **Vendég operációsrendszer-lemez** | **Meghajtó betűjele** | **Lemez adattípusa**
--- | --- | --- | ---
DB-Disk0-OS | Lemez0 | C:\ | Operációsrendszer-lemez
DB-Disk1 (Kizárás a replikációból) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Felhasználói adatok, 1
DB-Disk3 | Disk3 | F:\ | Felhasználói adatok, 2

A helyszíni virtuális gép lapozófájl-beállításai a következők:

![Lapozófájl-beállítások a helyszíni virtuális gépen](./media/exclude-disks-replication/pagefile-g-drive-source-vm.png)

1. Engedélyezzük a virtuális gép replikációját.
2. Kizárjuk a DB-Disk1-et a replikációból.

#### <a name="disks-after-failover"></a>Lemezek feladatátvétel után

Feladatátvétel után az Azure virtuális gép a lemezek a táblázatban összegzett.

**Lemez neve** | **Vendég operációs rendszer lemezének száma** | **Meghajtó betűjele** | **Lemez adattípusa**
--- | --- | --- | ---
DB-Disk0-OS | Lemez0  |C:\ | Operációsrendszer-lemez
DB-Disk1 | Disk1 | D:\ | Ideiglenes tárolás<br/><br/> Mivel a D: az első elérhető meghajtóbetűjel a listában, az Azure ezt a betűjelet rendeli hozzá az ideiglenes tárolókötethez.<br/><br/> A meghajtó betűjele minden replikált lemez esetében ugyanaz marad.<br/><br/> Mivel a G: lemez nem érhető el, a rendszer a C: meghajtót fogja használni a lapozófájlhoz.
DB-Disk2 | Disk2 | E:\ | Felhasználói adatok, 1
DB-Disk3 | Disk3 | F:\ | Felhasználói adatok, 2

A lapozófájl beállításai az Azure virtuális gép a következők:

![Lapozófájl-beállítások az Azure virtuális gépen](./media/exclude-disks-replication/pagefile-azure-vm-after-failover-2.png)


## <a name="next-steps"></a>További lépések

- További információ az ideiglenes tárolólemezre vonatkozó irányelvekről:
    - Információ az SSD-k [használatáról](https://blogs.technet.microsoft.com/dataplatforminsider/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) az Azure-beli virtuális gépeken az SQL Server TempDB és a Buffer Pool Extensions tárolására
    - [Tekintse át](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance) az SQL Server azure-beli virtuális gépeken való teljesítménygyakorlati tanácsait.
- Ha sikerült beállítania és elindítani az üzemelő példányt, [ismerkedjen meg részletesebben](failover-failback-overview.md) a feladatátvételi különféle típusaival.

