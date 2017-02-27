---
title: "Lemez kizárása a védelem alól Azure Site Recovery használatával | Microsoft Docs"
description: "Ez a cikk azt ismerteti, miért és hogyan zárhat ki virtuálisgép-lemez(eke)t a replikációból a VMware – Azure, valamint Hyper-V – Azure replikációs forgatókönyvek esetén."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 1/24/2017
ms.author: nisoneji
translationtype: Human Translation
ms.sourcegitcommit: af0d66d92ca542f415de779fb638db166ba5f26a
ms.openlocfilehash: 5e0527fb0a41d8892c9e22d6d6d2f252972e69d0


---
#<a name="exclude-disk-from-replication"></a>Lemez kizárása a replikációból
A jelen cikk azt ismerteti, hogyan zárhat ki lemez(eke)t a replikációból a felhasznált replikációs sávszélességet vagy az ilyen lemezek által felhasznált céloldali erőforrások optimalizálása érdekében. A szolgáltatás VMware – Azure és Hyper-V – Azure forgatókönyvek esetén támogatott.

##<a name="prerequisites"></a>Előfeltételek

Alapértelmezés szerint a rendszer a gép minden lemezét replikálja. Ha ki szeretne zárni egy lemezt a replikációból, a replikáció engedélyezése előtt manuálisan telepítenie kell a Mobilitási szolgáltatást a gépre, ha a replikálás a **VMware-ből az Azure-ba** irányul.


## <a name="why-exclude-disks-from-replication"></a>Miért érdemes lemezeket kizárni a replikációból?
A lemezek kizárása a replikációból az alábbiak miatt lehet szükséges:

1. A kizárt lemezen előállított adatok nem fontosak, vagy nincs szükség azok replikálására.

2. Tárterület és hálózati erőforrások takarítható meg az adatforgalom replikálásának kihagyásával.

##<a name="what-are-the-typical-scenarios"></a>Melyek a tipikus forgatókönyvek?
Van néhány konkrét példa olyan adatforgalmakra, amelyek könnyedén azonosíthatók, és remek jelöltek a kizárásra. Ilyenek például az oldalfájl-írások, a Microsoft SQL-kiszolgáló tempdb írásai stb. A számítási feladattól, valamint a tárolási alrendszertől függően az oldalfájl jelentős mértékű adatforgalmat rögzíthet. Az ilyen adatoknak az elsődleges helyről az Azure-ba irányuló replikálása azonban rendkívül erőforrás-igényes lenne. Tehát azon virtuális gépek replikálása, amelyek egyetlen virtuális lemezzel rendelkeznek az operációs rendszer és az oldalfájl számára, az alábbi módon optimalizálhatók:

1. Az egyetlen virtuális lemez felosztása két virtuális lemezre – egy az operációs rendszer, egy pedig az oldalfájl számára
2. Az oldalfájl lemezének kizárása a replikálásból

Hasonlóképpen, azon Microsoft SQL Server-ek replikációja, amelyek tempdb- és rendszeradatbázis-fájlja ugyanazon a lemezen található, az alábbi módon optimalizálhatók:

1. A rendszeradatbázis és a tempdb két különböző lemezen való tárolása
2. A tempdb lemezének kizárása a replikálásból.

##<a name="how-to-exclude-disk-from-replication"></a>Hogyan zárható ki lemez a replikációból?

###<a name="vmware-to-azure"></a>VMware – Azure
Ha az Azure Site Recovery portálról szeretne virtuális gépet védelemmel ellátni, kövesse a [Replikálás engedélyezése](site-recovery-vmware-to-azure.md#enable-replication) munkafolyamatot. A 4., Replikáció engedélyezése nevű lépésben van egy oszlop (**REPLIKÁLNI KÍVÁNT LEMEZ**), amely használatával kizárhat lemezeket a replikációból. Alapértelmezés szerint az összes lemez ki van jelölve. Törölje annak a lemeznek a jelölését, amelyet ki szeretne hagyni a replikációból, majd fejezze be a lépéseket a replikáció engedélyezéséhez. 

![A replikáció engedélyezése](./media/site-recovery-exclude-disk/v2a-enable-replication-exclude-disk1.png)
    
    
>[!NOTE]
> 
> * Csak olyan lemezeket zárhat ki, amelyeken már telepítve van a Mobilitási szolgáltatás. A Mobilitási szolgáltatást manuálisan kell telepítenie, mivel a szolgáltatás csak a replikáció engedélyezése után, a leküldéses mechanizmus használatával lesz telepítve.
> * Csak az alaplemezek zárhatók ki a replikációból. Operációsrendszer- vagy dinamikus lemezek nem zárhatók ki.
> * A replikáció engedélyezése után már nem lehet ahhoz lemezeket hozzáadni, vagy lemezeket eltávolítani belőle. Lemez hozzáadásához vagy eltávolításához le kell tiltania, majd újra engedélyeznie kell a gép védelmét.
> * Ha kizár egy olyan lemezt, amely valamely alkalmazás működéséhez szükséges, az Azure-ba történő feladatátvétel esetén manuálisan létre kell majd hozni azt az Azure-ban, hogy a replikált alkalmazás futtatható legyen. Másik megoldásként integrálhatja az Azure Automationt egy helyreállítási tervbe, hogy a gép feladatátvétele során létrehozza a lemezt.
> * Windows rendszerű virtuális gép: Az Azure-ban manuálisan létrehozott lemezek nem vesznek részt a feladatátvételben. Ha például végrehajtja három lemez feladatátvételét, kettőt pedig közvetlenül az Azure virtuális gépen hoz létre, csak a feladatátvételben részt vevő három lemezen lesz végrehajtva a feladat-visszavétel. A manuálisan létrehozott lemezek nem vehetők fel a feladat-visszavételbe vagy a helyszínről az Azure-ba történő védelem-újrabeállításba.
> * Linux rendszerű virtuális gép: Az Azure-ban manuálisan létrehozott lemezek részt vesznek a feladat-visszavételben. Ha például végrehajtja három lemez feladatátvételét, kettőt pedig közvetlenül az Azure virtuális gépen hoz létre, mind az öt lemezen végre lesz hajtva a feladat-visszavétel. A manuálisan létrehozott lemezek nem zárhatók ki a feladat-visszavételből.
> 

###<a name="hyper-v-to-azure"></a>Hyper-V – Azure
Ha az Azure Site Recovery portálról szeretne virtuális gépet védelemmel ellátni, kövesse a [Replikálás engedélyezése](site-recovery-hyper-v-site-to-azure.md#step-6-enable-replication) munkafolyamatot. A 4., Replikáció engedélyezése nevű lépésben van egy oszlop (**REPLIKÁLNI KÍVÁNT LEMEZ**), amely használatával kizárhat lemezeket a replikációból. Alapértelmezés szerint az összes lemez ki van jelölve replikációra. Törölje annak a lemeznek a jelölését, amelyet ki szeretne hagyni a replikációból, majd fejezze be a lépéseket a replikáció engedélyezéséhez. 

![A replikáció engedélyezése](./media/site-recovery-vmm-to-azure/enable-replication6-with-exclude-disk.png)
    
>[!NOTE]
> 
> * Csak az alaplemezek zárhatók ki a replikációból. Az operációs rendszert tartalmazó lemezt nem lehetséges, a dinamikus lemezeket pedig nem ajánlott kizárni. Az ASR nem lépes azonosítani, hogy melyik VHD-lemez alap- vagy dinamikus lemez a vendég virtuális gépen belül.  Ha nem zárja ki az összes függő dinamikus kötet lemezét, a védett dinamikus lemezek hibás lemezként szerepelnek majd a feladatátvételi virtuális gépen, és a lemezen lévő adatok nem lesznek elérhetőek.    
> * A replikáció engedélyezése után már nem lehet ahhoz lemezeket hozzáadni, vagy lemezeket eltávolítani belőle. Lemez hozzáadásához vagy eltávolításához le kell tiltania, majd újra kell engedélyeznie a virtuális gép védelmét.
> * Ha kizár egy olyan lemezt, amely valamely alkalmazás működéséhez szükséges, az Azure-ba történő feladatátvétel esetén manuálisan létre kell majd hozni azt az Azure-ban, hogy a replikált alkalmazás futtatható legyen. Másik megoldásként integrálhatja az Azure Automationt egy helyreállítási tervbe, hogy a gép feladatátvétele során létrehozza a lemezt.
> * Az Azure-ban manuálisan létrehozott lemezek nem vesznek részt a feladatátvételben. Ha például végrehajtja három lemez feladatátvételét, kettőt pedig közvetlenül az Azure virtuális gépen hoz létre, csak a feladatátvételben részt vevő három lemezen lesz végrehajtva az Azure-ból a Hyper-V-re történő feladat-visszavétel. A manuálisan létrehozott lemezek nem vehetők fel a feladat-visszavételbe vagy a Hyper-V-ről az Azure-ba történő visszirányú replikálásba.
 


##<a name="end-to-end-scenarios-of-exclude-disks"></a>Lemezek kizárásának teljes körű forgatókönyvei
Az alábbiakban két forgatókönyvet találhat a lemezek kizárása funkció jobb megértése érdekében.

1. SQL Server tempdb lemeze
2. Oldalfájl lemeze

###<a name="excluding-the-sql-server-tempdb-disk"></a>SQL Server tempdb-adatbázist tartalmazó lemezének kizárása
Vegyünk egy SQL Server virtuális gépet, amely tempdb-adatbázisa kizárható.

A virtuális gép neve: SalesDB A forrás virtuális gépen található lemezek:


**Lemez neve** | **Vendég operációs rendszer lemezének száma** | **Meghajtó betűjele** | **A lemez adattípusa**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Operációsrendszer-lemez
DB-Disk1| Disk1 | D:\ | SQL-rendszeradatbázis és&1;. felhasználói adatbázis
DB-Disk2 (a lemez ki lett zárva a védelemből) | Disk2 | E:\ | Ideiglenes fájlok
DB-Disk3 (a lemez ki lett zárva a védelemből) | Disk3 | F:\ | SQL tempdb-adatbázis (mappa elérési útja (F:\MSSQL\Data\) –> a feladatátvétel előbb írja le a mappa elérési útját
DB-Disk4 | Disk4 |G:\ |2. felhasználói adatbázis

Mivel a virtuális gép két lemezén is ideiglenes jellegű adatforgalom található, a SalesDB nevű virtuális gép védelme során zárja ki a „Disk2” és „Disk3” lemezeket a replikációból. Az Azure Site Recovery nem replikálja ezeket a lemezeket, és a feladatvétel során ezek a lemezek nem lesznek jelen a feladatátvételi virtuális gépen az Azure-ban

Lemezek az Azure virtuális gépen a feladatátvételt követően:

**Vendég operációs rendszer lemezének száma** | **Meghajtó betűjele** | **A lemez adattípusa**
--- | --- | ---
DISK0 |    C:\ | Operációsrendszer-lemez
Disk1 |    E:\ | Ideiglenes tároló [ezt a lemezt az Azure adja hozzá, és az első elérhető betűjellel látja el]
Disk2 | D:\ | SQL-rendszeradatbázis és&1;. felhasználói adatbázis
Disk3 | G:\ | 2. felhasználói adatbázis

Mivel a Disk2 és Disk3 lemezek ki lettek zárva a SalesDB virtuális gépből, az E: az első elérhető meghajtó-betűjel a listában. Az Azure hozzárendeli az E: betűjelet az ideiglenes tárolókötethez. A meghajtó betűjele minden replikált lemez esetében ugyanaz marad.

A Disk3 lemez, amely az SQL-kiszolgáló tempdb-adatbázisát tartalmazta (tempdb mappa elérési útja: F:\MSSQL\Data\), és amely ki lett zárva a replikációból, nem lesz elérhető a feladatátvételi virtuális gépen. Ennek következtében az SQL-szolgáltatás leállított állapotba kerül, és az F:\MSSQL\Data elérési utat igényli.

Ez az elérési út két különböző módon hozható létre.

1. Új lemez hozzáadásával és a tempdb mappa elérési útjának hozzárendelésével, vagy
2. Meglévő ideiglenes tárolólemez használatával a tempdb mappa elérési útjaként

####<a name="add-a-new-disk"></a>Új lemez hozzáadása:

1. A feladatátvétel előtt jegyezze fel az SQL tempdb.mdf és tempdb.ldf fájljainak elérési útját.
2. Az Azure Portalon adjon hozzá egy új lemezt a feladatátvételi virtuális géphez, amelynek mérete legalább akkora, mint a forrásként szolgáló SQL-kiszolgáló tempdb-adatbázisát tartalmazó lemezé (Disk3).
3. Jelentkezzen be az Azure virtuális gépre. A lemezkezelési (diskmgmt.msc) konzolból inicializálja és formázza az újonnan hozzáadott lemezt.
4. Rendelje hozzá ugyanazt a meghajtó-betűjelet, amelyet az SQL-kiszolgáló tempdb-adatbázisát tartalmazó lemez (F:) használt.
5. Hozza létre a tempdb mappát az F: köteten (F:\MSSQL\Data).
6. Indítja el az SQL-szolgáltatást a szolgáltatás konzoljából.

####<a name="use-existing-temporary-storage-disk-for-sql-tempdb-folder-path"></a>Meglévő ideiglenes tárolólemez használata az SQL-kiszolgáló tempdb mappájának elérési útjaként:

1. Nyisson meg egy parancssori konzolt
2. Futtassa az SQL-kiszolgálót helyreállítási módban a parancssori konzolból

        Net start MSSQLSERVER /f / T3608

3. Futtassa a következő sqlcmd parancsot a tempdb elérési útjának az új elérési útra történő módosításához

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

Az ideiglenes tárolólemezzel kapcsolatos Azure-irányelvekről az alábbi cikkekben olvashat

* SSD-meghajtók használata Azure virtuális gépeken az SQL Server tempdb-adatbázisának és pufferkészlet-bővítményeinek tárolására
* Ajánlott eljárások az SQL Server teljesítményének Azure Virtual Machines szolgáltatásbeli növeléséhez

###<a name="failback-from-azure-to-on-premises"></a>Feladat-visszavétel (Azure-ból helyszíni rendszerbe)
Most pedig tekintsük át, melyik lemezek lesznek replikálva, amikor feladatátvételt hajt végre Azure-ból helyi VMware vagy Hyper-V gazdagépre. Az Azure szolgáltatásban manuálisan létrehozott lemezek nem lesznek replikálva. Ha például végrehajtja három lemez feladatátvételét, kettőt pedig közvetlenül az Azure virtuális gépen hoz létre, csak a feladatátvételben részt vevő három lemezen lesz végrehajtva a feladat-visszavétel. A manuálisan létrehozott lemezek nem vehetők fel a feladat-visszavételbe vagy a helyszínről az Azure-ba történő védelem-újrabeállításba. A rendszer továbbá az ideiglenes tárolólemezeket sem replikálja a helyszíni rendszerbe.

####<a name="failback-to-original-location-recovery-olr"></a>Feladat-visszavétel eredeti helyre való helyreállítás céljából

A fenti példában leírt Azure virtuális gép lemezkonfigurációja:

**Vendég operációs rendszer lemezének száma** | **Meghajtó betűjele** | **A lemez adattípusa** 
--- | --- | --- 
DISK0 | C:\ | Operációsrendszer-lemez
Disk1 |    E:\ | Ideiglenes tároló [ezt a lemezt az Azure adja hozzá, és az első elérhető betűjellel látja el]
Disk2 |    D:\ | SQL-rendszeradatbázis és&1;. felhasználói adatbázis
Disk3 |    G:\ | 2. felhasználói adatbázis


####<a name="vmware-to-azure"></a>VMware – Azure
Amikor a feladat-visszavétel az eredeti helyre történik, a feladat-visszavételi virtuális gép lemezkonfigurációja nem tartalmaz kizárt lemezt. Ez azt jelenti, hogy a VMware – Azure irányú replikációból kizárt lemezek nem lesznek elérhetők a feladat-visszavételi virtuális gépen. 

A tervezett, Azure-ból helyszíni VMware-re történő feladatátvétel után a VMware virtuális gépen (eredeti hely) található lemezek:

**Vendég operációs rendszer lemezének száma** | **Meghajtó betűjele** | **A lemez adattípusa** 
--- | --- | --- 
DISK0 | C:\ | Operációsrendszer-lemez
Disk1 |    D:\ | SQL-rendszeradatbázis és&1;. felhasználói adatbázis
Disk2 |    G:\ | 2. felhasználói adatbázis

####<a name="hyper-v-to-azure"></a>Hyper-V – Azure
Amikor a feladat-visszavétel az eredeti helyre történik, a feladat-visszavételi virtuális gép lemezkonfigurációja ugyanaz marad, mint a Hyper-V eredeti virtuálisgép-lemezkonfigurációé. Ez azt jelenti, hogy a Hyper-V hely – Azure irányú replikációból kizárt lemezek elérhetők lesznek a feladat-visszavételi virtuális gépen.

A tervezett, Azure-ból helyszíni Hyper-V-re történő feladatátvétel után a Hyper-V virtuális gépen (eredeti hely) található lemezek:

**Lemez neve** | **Vendég operációs rendszer lemezének száma** | **Meghajtó betűjele** | **A lemez adattípusa**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 |    C:\ | Operációsrendszer-lemez
DB-Disk1 | Disk1 | D:\ | SQL-rendszeradatbázis és&1;. felhasználói adatbázis
DB-Disk2 (kizárt lemez) | Disk2 | E:\ | Ideiglenes fájlok
DB-Disk3 (kizárt lemez) | Disk3 | F:\ | SQL tempdb-adatbázis (mappa elérési útja (F:\MSSQL\Data\)
DB-Disk4 | Disk4 | G:\ | 2. felhasználói adatbázis


####<a name="exclude-paging-file-disk"></a>Lapozófájlok lemezének kizárása

Vegyünk egy virtuális gépet, amely lapozófájl-lemeze kizárható.
Két eset létezik:

####<a name="case-1-pagefile-is-configured-on-the-d-drive"></a>1. eset: A lapozófájl a D: meghajtón van konfigurálva
Lemezkonfiguráció:


**Lemez neve** | **Vendég operációs rendszer lemezének száma** | **Meghajtó betűjele** | **A lemez adattípusa**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Operációsrendszer-lemez
DB-Disk1 (a lemez ki lett zárva a védelemből) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Felhasználói adatok, 1
DB-Disk3 | Disk3 | F:\ | Felhasználói adatok, 2

Lapozófájl-beállítások a forrás virtuális gépen:

![A replikáció engedélyezése](./media/site-recovery-exclude-disk/pagefile-on-d-drive-sourceVM.png)
    

A VMware – Azure vagy Hyper-V – Azure irányú feladatátvétel után az Azure virtuális gépen található lemezek:
**Lemez neve** | **Vendég operációs rendszer lemezének száma** | **Meghajtó betűjele** | **A lemez adattípusa**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Operációsrendszer-lemez
DB-Disk1 | Disk1 | D:\ | Ideiglenes tároló –> pagefile.sys
DB-Disk2 | Disk2 | E:\ | Felhasználói adatok, 1
DB-Disk3 | Disk3 | F:\ | Felhasználói adatok, 2

Mivel a Disk1 nevű lemez (D:) ki lett zárva, a D: az első elérhető meghajtó-betűjel a listában, ezért az Azure a D: betűjelet rendeli hozzá az ideiglenes tárolókötethez.  Mivel a D: meghajtó elérhető az Azure virtuális gépen, a virtuális gép lapozófájl-beállításai nem változnak.

Lapozófájl-beállítások az Azure virtuális gépen:

![A replikáció engedélyezése](./media/site-recovery-exclude-disk/pagefile-on-Azure-vm-after-failover.png)

####<a name="case-2-pagefile-file-is-configured-on-any-other-driveother-than-d-drive"></a>2. eset: A lapozófájl bármely másik meghajtón van konfigurálva (nem a D: meghajtón)

Forrás virtuális gép lemezkonfigurációja:

**Lemez neve** | **Vendég operációs rendszer lemezének száma** | **Meghajtó betűjele** | **A lemez adattípusa**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Operációsrendszer-lemez
DB-Disk1 (a lemez ki lett zárva a védelemből) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Felhasználói adatok, 1
DB-Disk3 | Disk3 | F:\ | Felhasználói adatok, 2

Lapozófájl-beállítások a helyszíni virtuális gépen:

![A replikáció engedélyezése](./media/site-recovery-exclude-disk/pagefile-on-g-drive-sourceVM.png)

A VMware/Hyper-V – Azure irányú feladatátvétel után az Azure virtuális gépen található lemezek:

**Lemez neve**| **Vendég operációs rendszer lemezének száma**| **Meghajtó betűjele** | **A lemez adattípusa**
--- | --- | --- | ---
DB-Disk0-OS | DISK0  |C:\ |Operációsrendszer-lemez
DB-Disk1 | Disk1 | D:\ | Ideiglenes tároló –> pagefile.sys
DB-Disk2 | Disk2 | E:\ | Felhasználói adatok, 1
DB-Disk3 | Disk3 | F:\ | Felhasználói adatok, 2

Mivel a D: az első elérhető meghajtó-betűjel a listában, az Azure ezt a betűjelet rendeli hozzá az ideiglenes tárolókötethez. A meghajtó betűjele minden replikált lemez esetében ugyanaz marad. Mivel a G: lemez nem érhető el, a rendszer a C: meghajtót használja a lapozófájlhoz.

Lapozófájl-beállítások az Azure virtuális gépen:

![A replikáció engedélyezése](./media/site-recovery-exclude-disk/pagefile-on-Azure-vm-after-failover-2.png)

## <a name="next-steps"></a>Következő lépések
Ha sikerült beállítania és elindítani az üzemelő példányt, [ismerkedjen meg részletesebben](site-recovery-failover.md) a feladatátvételi különféle típusaival.



<!--HONumber=Feb17_HO3-->


