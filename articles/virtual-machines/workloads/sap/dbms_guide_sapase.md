---
title: SAP ASE Azure virtuális gépek DBMS üzembe helyezése az SAP-munkaterheléshez | Microsoft dokumentumok
description: SAP ASE Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 25d911869c95baba6ac9db3b893292e702e9c0e9
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273205"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>SAP ASE Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz

Ebben a dokumentumban több különböző területet is figyelembe kell venni az SAP ASE azure IaaS-ben való üzembe helyezésekor. A dokumentum előfeltételeként el kell olvasnia a [dokumentumot Az Azure virtual machines DBMS üzembe helyezésével kapcsolatos szempontok sap-számítási feladatokhoz](dbms_guide_general.md) és az [Azure dokumentációsap-munkaterhelésében](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)található egyéb útmutatókhoz. Ez a dokumentum a Linuxés Windows operációs rendszereken futó SAP ASE-t ismerteti. Az Azure minimálisan támogatott kiadása az SAP ASE 16.0.02 (Release 16 Support Pack 2). Javasoljuk, hogy telepítse az SAP legújabb verzióját és a legújabb javítási szintet.  Legalább AZ SAP ASE 16.0.03.07 (Release 16 Support Pack 3 Patch Level 7) használata ajánlott.  Az SAP legújabb verziója megtalálható a [Célzott ASE 16.0 kiadási ütemezésés CR lista információk](https://wiki.scn.sap.com/wiki/display/SYBASE/Targeted+ASE+16.0+Release+Schedule+and+CR+list+Information).

Az SAP-alkalmazások vagy a telepítési adathordozó helyével kapcsolatos kiadási támogatásról az SAP termékelérhetőségi mátrixa mellett az alábbi helyeken talál további információt:

- [SAP támogatási megjegyzés #2134316](https://launchpad.support.sap.com/#/notes/2134316)
- [SAP támogatási megjegyzés #1941500](https://launchpad.support.sap.com/#/notes/1941500)
- [SAP támogatási megjegyzés #1590719](https://launchpad.support.sap.com/#/notes/1590719)
- [SAP támogatási megjegyzés #1973241](https://launchpad.support.sap.com/#/notes/1973241)

Megjegyzés: Az SAP-világon belüli és kívüli dokumentációban a termék neve Sybase ASE vagy SAP ASE vagy bizonyos esetekben mindkettő. Annak érdekében, hogy konzisztens maradjon, ebben a dokumentációban az **SAP ASE** nevet használjuk.

## <a name="operating-system-support"></a>Az operációs rendszer támogatása
Az SAP-termék rendelkezésre állási mátrix tartalmazza a támogatott operációs rendszer és az SAP kernel kombinációk minden SAP-alkalmazások.  Linux disztribúciók SUSE 12.x, SUSE 15.x, Red Hat 7.x teljes mértékben támogatott.  Az Oracle Linux mint AZ SAP ASE operációs rendszere nem támogatott.  Javasoljuk, hogy használja a legújabb Elérhető Linux-kiadásokat. A Windows-ügyfelek nek Windows Server 2016 vagy Windows Server 2019 kiadásokat kell használniuk.  A Windows régebbi kiadásai, például a Windows 2012 technikailag támogatottak, de a legújabb Windows-verzió mindig ajánlott.


## <a name="specifics-to-sap-ase-on-windows"></a>Az SAP ASE sajátosságai A Windows rendszeren
A Microsoft Azure-ral kezdve áttelepítheti meglévő SAP ASE-alkalmazásokat az Azure virtuális gépekre. Az Azure virtuális gépen található SAP ASE lehetővé teszi, hogy csökkentse a vállalati széles alkalmazások üzembe helyezésének, felügyeletének és karbantartásának teljes tulajdonjogát azáltal, hogy ezeket az alkalmazásokat egyszerűen áttelepíti a Microsoft Azure-ba. Az SAP ASE egy Azure virtuális gép, a rendszergazdák és a fejlesztők továbbra is használhatja ugyanazokat a fejlesztési és felügyeleti eszközöket, amelyek a helyszínen elérhető.

A Microsoft Azure számos különböző virtuálisgép-típust kínál, amelyek lehetővé teszik a legkisebb SAP-rendszerek és tájak futtatását akár a nagy SAP-rendszerekig és tájakig több ezer felhasználóval. A különböző SAP-tanúsítvánnyal rendelkező VM-skus-ok SAP-sk-számait az [SAP támogatási megjegyzése #1928533](https://launchpad.support.sap.com/#/notes/1928533)biztosítja.

Az SAP ASE Windows rendszeren történő telepítéséhez szükséges dokumentáció megtalálható az [SAP ASE Windows telepítési útmutatójában](https://help.sap.com/viewer/36031975851a4f82b1022a9df877280b/16.0.3.7/en-US/a660d3f1bc2b101487cbdbf10069c3ac.html)

A memória lapjainak zárolása olyan beállítás, amely megakadályozza az SAP ASE adatbázispuffer lapozását.  Ez a beállítás nagy méretű, sok memóriával rendelkező, foglalt rendszerek esetén hasznos. További információért forduljon a BC-DB-SYB-hez. 


## <a name="linux-operating-system-specific-settings"></a>Linux operációs rendszer specifikus beállítások
Linux os virtuális `saptune` gépeken az SAP-ASE Linux Huge Pages profillal való futtatást alapértelmezés szerint engedélyezni kell, és parancssal ellenőrizhető  

`cat /proc/meminfo` 

Az oldalméret általában 2048 KB. A részletekért lásd a [cikket Hatalmas oldalak Linux](https://help.sap.com/viewer/ecbccd52e7024feaa12f4e780b43bc3b/16.0.3.7/en-US/a703d580bc2b10149695f7d838203fad.html) 


## <a name="recommendations-on-vm-and-disk-structure-for-sap-ase-deployments"></a>Javaslatok a virtuális gép és a lemez szerkezete az SAP ASE-telepítések

Az SAP ASE az SAP NetWeaver Applications számára az [SAP támogatási megjegyzésében](https://launchpad.support.sap.com/#/notes/1928533) felsorolt bármely virtuális géptípusban támogatott, #1928533 a közepes méretű SAP ASE adatbázis-kiszolgálókhoz használt tipikus virtuálisgép-típusok közé tartozik az Esv3.  A nagy, több terabájtos adatbázisok m sorozatú virtuálisgép-típusokat használhatnak. Az SAP ASE tranzakciós naplólemez írási teljesítménye javítható az M sorozatú írásgyorsító engedélyezésével. Írásgyorsító gondosan kell vizsgálni az SAP ASE miatt, ahogy az SAP ASE elvégzi a napló írások.  Tekintse át [az SAP támogatási #2816580,](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator) és fontolja meg egy teljesítményteszt futtatását.  
Az írásgyorsító csak tranzakciónapló-lemezhez készült. A lemezszintű gyorsítótárat NONE-ra kell állítani. Ne lepődjön meg, ha az Azure Write Accelerator nem mutat hasonló fejlesztéseket, mint más DBMS. Az SAP ASE tranzakciós naplóba írási módja alapján lehet, hogy az Azure Write Accelerator kevés vagy egyáltalán nem gyorsul.
Az adateszközök és a naplóeszközök számára külön lemezek használata ajánlott.  A rendszer adatbázisok sybsecurity és `saptools` nem igényel dedikált lemezeket, és lehet helyezni a lemezeket tartalmazó SAP adatbázis adatok és naplóeszközök 

![Az SAP ASE tárolási konfigurációja](./media/dbms-guide-sap-ase/sap-ase-disk-structure.png)

### <a name="file-systems-stripe-size--io-balancing"></a>Fájlrendszerek, csíkméret & IO kiegyensúlyozás 
Az SAP ASE egymás után írja az adatokat a lemeztároló eszközökre, hacsak másképp nem konfigurálják. Ez azt jelenti, hogy egy üres SAP ASE-adatbázis négy eszközzel csak az első eszközbe ír adatokat.  A többi lemezeszköz csak akkor lesz beírva, ha az első eszköz megtelt.  Az egyes SAP ASE-eszközök olvasási és írási io-mennyisége valószínűleg eltérő lesz. A lemez IO-jának az összes rendelkezésre álló Azure-lemez közötti egyensúlyba hozásához windowsos tárolóhelyeket vagy Linux LVM2-t kell használni. Linux on, ajánlott használni XFS fájlrendszer formátuma a lemezeket. Az LVM-csíkméretet teljesítményvizsgálattal kell vizsgálni. 128 KB-os csíkméret jó kiindulási pont. Windows rendszerben az NTFS-foglalási egység méretét (AUS) kell tesztelni. A 64 KB kiindulási értékként használható. 

Az [SAP Adaptive Server Enterprise](https://blogs.sap.com/2014/07/09/configuring-automatic-database-space-expansion-in-sap-adaptive-server-enterprise/) és az SAP támogatási [megjegyzése #1815695](https://launchpad.support.sap.com/#/notes/1815695)című cikkben ismertetett módon ajánlott konfigurálni az automatikus adatbázis-bővítést. 

### <a name="sample-sap-ase-on-azure-virtual-machine-disk-and-file-system-configurations"></a>Minta SAP ASE az Azure virtuális gépen, lemez- és fájlrendszer-konfigurációk 
Az alábbi sablonok a Linux és a Windows mintakonfigurációit mutatják. A virtuális gép és a lemez konfigurációjának megerősítése előtt győződjön meg arról, hogy az egyes virtuális gépek hálózati és tárolási sávszélesség-kvótái elegendőek az üzleti követelmények teljesítéséhez. Azt is vegye szem előtt, hogy a különböző Azure virtuálisgép-típusok különböző maximális számú lemez, amely a virtuális géphez csatolható. Például egy E4s_v3 virtuális gép rendelkezik egy korlát 48 MB/sec tárolási IO átviteli sebesség. Ha az adatbázis biztonsági mentési tevékenységéhez szükséges tárolási átviteli sebesség 48 MB/mp-nél többet igényel, akkor elkerülhetetlen egy nagyobb virtuálisgép-típus nagyobb tárolási sávszélesség-átviteli sebességgel. Az Azure storage konfigurálásakor azt is szem előtt kell tartania, hogy különösen az [Azure Premium storage-ban](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance) az átviteli és iOPS/GB kapacitás nem változik. Erről a témakörről bővebben a [Milyen lemeztípusok érhetők el az Azure-ban?](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types). Az adott Azure virtuálisgép-típusok kvótáit a [memória optimalizált virtuálisgép-méretek](https://docs.microsoft.com/azure/virtual-machines/sizes-memory) és cikkek csatolt cikk ismerteti. 

> [!NOTE]
>  Ha egy DBMS-rendszer a helyszíni Azure-ból kerül áthelyezésre, javasoljuk, hogy a virtuális gép figyelése, és értékelje a CPU, a memória, az IOPS és a tárolási átviteli teljesítmény. Hasonlítsa össze a megfigyelt csúcsértékeket a fent említett cikkekben dokumentált virtuális gép kvóta-határértékekkel

Az alábbi példák szemléltető célokat szolgálnak, és az egyéni igények alapján módosíthatók. Az SAP ASE kialakítása miatt az adateszközök száma nem olyan kritikus, mint más adatbázisok. A dokumentumban részletezett adateszközök száma csak útmutató. 

Egy 50 GB – 250 GB közötti adatbázisméretű SAP ASE DB-kiszolgáló konfigurációjának példája, például az SAP-megoldáskezelő

| Konfiguráció | Windows | Linux | Megjegyzések |
| --- | --- | --- | --- |
| Virtuális gép típusa | E4s_v3 (4 vCPU/32 GB RAM) | E4s_v3 (4 vCPU/32 GB RAM) | --- |
| Gyorsított hálózatkezelés | Bekapcsolás | Bekapcsolás | ---|
| SAP ASE verzió | 16.0.03.07 vagy magasabb | 16.0.03.07 vagy magasabb | --- |
| Adateszközök száma | 4 | 4 | ---|
| Naplóeszközök száma | 1 | 1 | --- |
| Az ideiglenes eszközök száma | 1 | 1 | további SAP BW munkaterhelés |
| Operációs rendszer | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 vagy RHEL 7.6 | --- |
| Lemezösszesítés | Tárolóhelyek | LVM2 között | --- |
| Fájlrendszer | NTFS | Xfs |
| Blokkméret formázása | munkaterhelés-tesztelésre van szüksége | munkaterhelés-tesztelésre van szüksége | --- |
| Adatlemezek száma és típusa | Prémium szintű tárhely: 2 x P10 (RAID0) | Prémium szintű tárhely: 2 x P10 (RAID0)| Gyorsítótár = Írásvédett |
| # és típusú naplólemezek | Prémium szintű tárolás: 1 x P20  | Prémium szintű tárolás: 1 x P20 | Gyorsítótár = NINCS |
| ASE MaxMemory paraméter | A fizikai RAM 90%-a | A fizikai RAM 90%-a | egyetlen példányt feltételezve |
| A biztonsági mentést készítő eszközök száma | 4 | 4| --- |
| # és típusú biztonsági másolat lemezek | 1 | 1 | --- |


Egy 250 GB – 750 GB közötti adatbázisméretű közepes SAP ASE DB-kiszolgáló konfigurációjának, például egy kisebb SAP Business Suite rendszernek a konfigurációja ily ként nézhet ki

| Konfiguráció | Windows | Linux | Megjegyzések |
| --- | --- | --- | --- |
| Virtuális gép típusa | E16s_v3 (16 vCPU/128 GB RAM) | E16s_v3 (16 vCPU/128 GB RAM) | --- |
| Gyorsított hálózatkezelés | Bekapcsolás | Bekapcsolás | ---|
| SAP ASE verzió | 16.0.03.07 vagy magasabb | 16.0.03.07 vagy magasabb | --- |
| Adateszközök száma | 8 | 8 | ---|
| Naplóeszközök száma | 1 | 1 | --- |
| Az ideiglenes eszközök száma | 1 | 1 | további SAP BW munkaterhelés |
| Operációs rendszer | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 vagy RHEL 7.6 | --- |
| Lemezösszesítés | Tárolóhelyek | LVM2 között | --- |
| Fájlrendszer | NTFS | Xfs |
| Blokkméret formázása | munkaterhelés-tesztelésre van szüksége | munkaterhelés-tesztelésre van szüksége | --- |
| Adatlemezek száma és típusa | Prémium szintű tárhely: 4 x P20 (RAID0) | Prémium szintű tárhely: 4 x P20 (RAID0)| Gyorsítótár = Írásvédett |
| # és típusú naplólemezek | Prémium szintű tárolás: 1 x P20  | Prémium szintű tárolás: 1 x P20 | Gyorsítótár = NINCS |
| ASE MaxMemory paraméter | A fizikai RAM 90%-a | A fizikai RAM 90%-a | egyetlen példányt feltételezve |
| A biztonsági mentést készítő eszközök száma | 4 | 4| --- |
| # és típusú biztonsági másolat lemezek | 1 | 1 | --- |

Egy 750 GB – 2000 GB közötti adatbázisméretű sap ASE DB-kiszolgáló konfigurációjának, például egy nagyobb SAP Business Suite rendszernek a konfigurációja ily ként nézhet ki

| Konfiguráció | Windows | Linux | Megjegyzések |
| --- | --- | --- | --- |
| Virtuális gép típusa | E64s_v3 (64 vCPU/432 GB RAM) | E64s_v3 (64 vCPU/432 GB RAM) | --- |
| Gyorsított hálózatkezelés | Bekapcsolás | Bekapcsolás | ---|
| SAP ASE verzió | 16.0.03.07 vagy magasabb | 16.0.03.07 vagy magasabb | --- |
| Adateszközök száma | 16 | 16 | ---|
| Naplóeszközök száma | 1 | 1 | --- |
| Az ideiglenes eszközök száma | 1 | 1 | további SAP BW munkaterhelés |
| Operációs rendszer | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 vagy RHEL 7.6 | --- |
| Lemezösszesítés | Tárolóhelyek | LVM2 között | --- |
| Fájlrendszer | NTFS | Xfs |
| Blokkméret formázása | munkaterhelés-tesztelésre van szüksége | munkaterhelés-tesztelésre van szüksége | --- |
| Adatlemezek száma és típusa | Prémium szintű tárhely: 4 x P30 (RAID0) | Prémium szintű tárhely: 4 x P30 (RAID0)| Gyorsítótár = Írásvédett |
| # és típusú naplólemezek | Prémium szintű tárolás: 1 x P20  | Prémium szintű tárolás: 1 x P20 | Gyorsítótár = NINCS |
| ASE MaxMemory paraméter | A fizikai RAM 90%-a | A fizikai RAM 90%-a | egyetlen példányt feltételezve |
| A biztonsági mentést készítő eszközök száma | 4 | 4| --- |
| # és típusú biztonsági másolat lemezek | 1 | 1 | --- |


Egy 2 TB+ méretű sap ASE DB-kiszolgáló konfigurációjának, például egy nagyobb, globálisan használt SAP Business Suite rendszerkonfigurációnak a

| Konfiguráció | Windows | Linux | Megjegyzések |
| --- | --- | --- | --- |
| Virtuális gép típusa | M-sorozat (1,0-4,0 TB RAM)  | M-sorozat (1,0-4,0 TB RAM) | --- |
| Gyorsított hálózatkezelés | Bekapcsolás | Bekapcsolás | ---|
| SAP ASE verzió | 16.0.03.07 vagy magasabb | 16.0.03.07 vagy magasabb | --- |
| Adateszközök száma | 32 | 32 | ---|
| Naplóeszközök száma | 1 | 1 | --- |
| Az ideiglenes eszközök száma | 1 | 1 | további SAP BW munkaterhelés |
| Operációs rendszer | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 vagy RHEL 7.6 | --- |
| Lemezösszesítés | Tárolóhelyek | LVM2 között | --- |
| Fájlrendszer | NTFS | Xfs |
| Blokkméret formázása | munkaterhelés-tesztelésre van szüksége | munkaterhelés-tesztelésre van szüksége | --- |
| Adatlemezek száma és típusa | Prémium tárhely: 4+ x P30 (RAID0) | Prémium tárhely: 4+ x P30 (RAID0)| Gyorsítótár = Írásvédett, Fontolja meg az Azure Ultra lemezt |
| # és típusú naplólemezek | Prémium szintű tárolás: 1 x P20  | Prémium szintű tárolás: 1 x P20 | Gyorsítótár = NINCS, Fontolja meg az Azure Ultra lemezt |
| ASE MaxMemory paraméter | A fizikai RAM 90%-a | A fizikai RAM 90%-a | egyetlen példányt feltételezve |
| A biztonsági mentést készítő eszközök száma | 16 | 16 | --- |
| # és típusú biztonsági másolat lemezek | 4 | 4 | LVM2/Tárolóhelyek használata |


### <a name="backup--restore-considerations-for-sap-ase-on-azure"></a>Az SAP ASE biztonsági & visszaállítási szempontjai az Azure-ban
Az adatok és a biztonsági mentési eszközök számának növelése növeli a biztonsági mentés és visszaállítás teljesítményét. Javasoljuk, hogy csíkaz Az Azure-lemezek, amelyek az SAP ASE biztonsági mentési eszköz, ahogy az a korábban látható táblákban látható. Ügyelni kell arra, hogy egyensúlyt a biztonsági mentési eszközök és lemezek száma, és biztosítja, hogy a biztonsági mentési átviteli nem haladhatja meg a 40%50%, a teljes virtuális gép átviteli kvótát. Az SAP biztonsági mentési tömörítés használata ajánlott alapértelmezettként. További részletek találhatók a cikkekben:

- [SAP támogatási megjegyzés #1588316](https://launchpad.support.sap.com/#/notes/1588316)
- [SAP támogatási megjegyzés #1801984](https://launchpad.support.sap.com/#/notes/1801984)
- [SAP támogatási megjegyzés #1585981](https://launchpad.support.sap.com/#/notes/1585981) 

Ne használja a D:\ meghajtót vagy /temp space adatbázis- vagy naplókiírási célként.

### <a name="impact-of-database-compression"></a>Az adatbázis-tömörítés hatása
Olyan konfigurációkban, ahol az I/O-sávszélesség korlátozó tényezővé válhat, az IOPS-t csökkentő mértékek segíthetnek a munkaterhelés megnyújtásában, amely egy IaaS-forgatókönyvben, például az Azure-ban futtatható. Ezért ajánlott győződjön meg arról, hogy az SAP ASE-tömörítés használata egy meglévő SAP-adatbázis feltöltése előtt.

Az Azure-ba való feltöltés előtt a tömörítés alkalmazására vonatkozó javaslat több okból is kiáll:

* Az Azure-ba feltöltendő adatok mennyisége alacsonyabb
* A tömörítés végrehajtásának időtartama rövidebb, feltételezve, hogy erősebb hardvert használhat, amely több PROCESSZORRAL vagy nagyobb I/O sávszélességgel vagy kisebb I/O-késleltetéssel rendelkezik a helyszínen
* A kisebb adatbázisméretek a lemezfoglalás költségeinek csökkentéséhez vezethetnek

Az adatok és a LOB-tömörítés az Azure virtuális gépeken üzemeltetett virtuális gépeken dolgozik, ugyanúgy, mint a helyszíni. Ha további részleteket szeretne tudni arról, hogy a tömörítés már használatban van-e egy meglévő SAP ASE-adatbázisban, olvassa el az [SAP 1750510 támogatási megjegyzését.](https://launchpad.support.sap.com/#/notes/1750510) Az SAP ASE adatbázis-tömörítéssel kapcsolatos további részletekért ellenőrizze [az SAP támogatási megjegyzését #2121797](https://launchpad.support.sap.com/#/notes/2121797)

## <a name="high-availability-of-sap-ase-on-azure"></a>Az SAP ASE magas rendelkezésre állása az Azure-ban 
A HADR felhasználói útmutató részletezi a 2 csomópontos SAP ASE "Always-on" megoldás beállítását és konfigurációját.  Emellett egy harmadik vész-helyreállítási csomópont is támogatott. Az SAP ASE számos magas rendelkezésre álló konfigurációt támogat, beleértve a megosztott lemezt és a natív operációsrendszer-fürtözést (lebegő IP). Az azure-beli egyetlen támogatott konfiguráció a Hibakezelő lebegő IP nélküli használata.  A lebegő IP-cím módszer nem fog működni az Azure-ban.  Az SAP Kernel egy "HA Aware" alkalmazás, és ismeri az elsődleges és másodlagos SAP ASE-kiszolgálók. Nincsenek szoros integrációk az SAP ASE és az Azure között, az Azure belső terheléselosztó nem használatos. Ezért a szabványos SAP ASE dokumentációt az [SAP ASE HADR felhasználói útmutatóval](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.7/en-US/a6645e28bc2b1014b54b8815a64b87ba.html) kezdve kell követni 

> [!NOTE]
> Az azure-beli egyetlen támogatott konfiguráció a Hibakezelő lebegő IP nélküli használata.  A lebegő IP-cím módszer nem fog működni az Azure-ban. 

### <a name="third-node-for-disaster-recovery"></a>Harmadik csomópont a vészhelyreállításhoz
Az SAP ASE mindig bekapcsolt helyi magas rendelkezésre állású használatával túl érdemes lehet kiterjeszteni a konfigurációt egy másik Azure-régióban egy aszinkron replikált csomópontra. Dokumentáció egy ilyen forgatókönyv megtalálható [itt](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199).

## <a name="sap-ase-database-encryption--ssl"></a>SAP ASE adatbázis-titkosítási & SSL 
Az SAP szoftverkiépítési kezelője (SWPM) lehetőséget ad az adatbázis titkosítására a telepítés során.  Ha titkosítást szeretne használni, ajánlott az SAP Full Database Encryption használata.  Lásd a következő dokumentumban dokumentált részleteket:

- [SAP támogatási megjegyzés #2556658](https://launchpad.support.sap.com/#/notes/2556658)
- [SAP támogatási megjegyzés #2224138](https://launchpad.support.sap.com/#/notes/2224138)
- [SAP támogatási megjegyzés #2401066](https://launchpad.support.sap.com/#/notes/2401066)
- [SAP támogatási megjegyzés #2593925](https://launchpad.support.sap.com/#/notes/2593925) 

> [!NOTE]
> Ha egy SAP ASE adatbázis titkosítva van, akkor a biztonsági másolat dump tömörítése nem fog működni. Lásd még: [SAP támogatási megjegyzés #2680905](https://launchpad.support.sap.com/#/notes/2680905) 

## <a name="sap-ase-on-azure-deployment-checklist"></a>SAP ASE az Azure központi telepítésének ellenőrzőlistáján
 
- Sap ASE 16.0.03.07 vagy újabb telepítése
- Frissítés a FaultManager és az SAPHostAgent legújabb verziójára és javításaira
- Telepítés a legújabb minősített operációs rendszeren, például a Windows 2019, a Suse 15.1 vagy a Redhat 7.6 vagy újabb verzión
- SAP-tanúsítvánnyal rendelkező virtuális gépek használata – nagy memóriával rendelkező Azure VM skusok, például Es_v3 vagy x-nagy rendszerek hez M sorozatú VM ska-k ajánlottak
- Egyezik a lemez IOPS és a virtuális gép összesítő átviteli kvótáját a lemez tervezés.  Elegendő számú lemez telepítése
- Lemezek összesítése windowsos tárolóhelyekkel vagy Linux LVM2 használatával, megfelelő csíkmérettel és fájlrendszerrel
- Elegendő számú eszköz létrehozása az adatok, a napló, az ideiglenes és a biztonsági mentés céljából
- Fontolja meg az UltraDisk használatát x-nagy méretű rendszerekhez 
- SAP-ASE futtatása `saptune` Linux operációs rendszeren 
- Az adatbázis biztonságossá tétele db-titkosítással – kulcsok manuális tárolása az Azure Key Vaultban 
- Az SAP befejezése [az Azure ellenőrzőlistájában](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist) 
- Naplóbiztonsági mentés és teljes biztonsági mentés konfigurálása 
- Ha/DR tesztelése, biztonsági mentés, visszaállítás és teljesítmény & kötetteszt 
- Annak ellenőrzése, hogy működik-e az automatikus adatbázis-bővítmény 

## <a name="using-dbacockpit-to-monitor-database-instances"></a>Adatbázispéldányok figyelése a DBACockpit segítségével
Az SAP-rendszerek, amelyek az SAP ASE adatbázis-platformként használják, a DBACockpit érhető el beágyazott böngészőablakok ként a DBACockpit tranzakcióban vagy Webdynpro ként. Az adatbázis figyelésének és felügyeletének teljes funkcionalitása azonban csak a DBACockpit Webdynpro implementációjában érhető el.

A helyszíni rendszerekhez ugyanúgy, mint a helyszíni rendszerek, több lépésre van szükség a DBACockpit Webdynpro implementációja által használt összes SAP NetWeaver funkció engedélyezéséhez. Kövesse [az SAP támogatási megjegyzését #1245200](https://launchpad.support.sap.com/#/notes/1245200) a webdynpros használatának engedélyezéséhez és a szükséges ek létrehozásához. Ha a fenti megjegyzésekben található utasításokat követi, konfigurálja az Internet Communication Managert (`ICM`) a http- és https-kapcsolatokhoz használandó portokkal együtt. A http alapértelmezett beállítása így néz ki:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

és a DBACockpit tranzakcióban létrehozott linkek hasonlóak a következőkhöz:

> https:\//\<fullyqualifiedhostname>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//\<fullyqualifiedhostname>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Attól függően, hogy az SAP-rendszert üzemeltető Azure virtuális gép hogyan csatlakozik az AD-hez és a DNS-hez, győződjön meg arról, hogy az ICM egy teljesen minősített állomásnevet használ, amely feloldható azon a gépen, amelyről megnyitja a DBACockpit-t. Tekintse meg [az SAP támogatási megjegyzését #773830](https://launchpad.support.sap.com/#/notes/773830) annak megértéséhez, hogy az ICM hogyan határozza meg a teljesen minősített állomásnevet a profilparaméterek alapján, és szükség esetén explicit módon állítsa be az icm/host_name_full paramétert.

Ha a virtuális gép üzembe helyezése csak felhőalapú forgatókönyvben, a helyszíni és az Azure közötti több helyiségközötti `domainlabel`kapcsolat nélkül, meg kell adnia egy nyilvános IP-címet és egy. A virtuális gép nyilvános DNS-nevének formátuma így néz ki:

> `<custom domainlabel`>. `<azure region`>.cloudapp.azure.com
> 
> 

A DNS-névvel kapcsolatos további részletek [itt][virtual-machines-azurerm-versus-azuresm].

Az ICM/host_name_full SAP-profilparaméter beállítása az Azure-beli virtuális gép DNS-nevére:

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

Ebben az esetben meg kell győződnie arról, hogy:

* Bejövő szabályok hozzáadása a hálózati biztonsági csoporthoz az Azure Portalon az ICM-mel való kommunikációhoz használt TCP/IP-portokhoz
* Bejövő szabályok hozzáadása a Windows tűzfal konfigurációjához az ICM-mel való kommunikációhoz használt TCP/IP-portokhoz

Az összes elérhető javítás automatikus importálása esetén ajánlott rendszeresen alkalmazni az SAP-verzióra vonatkozó SAP-feljegyzésjavító gyűjteményt:

* [SAP támogatási megjegyzés #1558958](https://launchpad.support.sap.com/#/notes/1558958)
* [SAP támogatási megjegyzés #1619967](https://launchpad.support.sap.com/#/notes/1619967)
* [SAP támogatási megjegyzés #1882376](https://launchpad.support.sap.com/#/notes/1882376)

További információ a DBA Cockpit for SAP ASE megtalálható a következő SAP megjegyzések:

* [SAP támogatási megjegyzés #1605680](https://launchpad.support.sap.com/#/notes/1605680)
* [SAP támogatási megjegyzés #1757924](https://launchpad.support.sap.com/#/notes/1757924)
* [SAP támogatási megjegyzés #1757928](https://launchpad.support.sap.com/#/notes/1757928)
* [SAP támogatási megjegyzés #1758182](https://launchpad.support.sap.com/#/notes/1758182)
* [SAP támogatási megjegyzés #1758496](https://launchpad.support.sap.com/#/notes/1758496)    
* [SAP támogatási megjegyzés #1814258](https://launchpad.support.sap.com/#/notes/1814258)
* [SAP támogatási megjegyzés #1922555](https://launchpad.support.sap.com/#/notes/1922555)
* [SAP támogatási megjegyzés #1956005](https://launchpad.support.sap.com/#/notes/1956005)


## <a name="useful-links-notes--whitepapers-for-sap-ase"></a>Hasznos linkek, megjegyzések & az SAP ASE-hez
Az SAP [ASE 16.0.03.07 Dokumentáció](https://help.sap.com/viewer/product/SAP_ASE/16.0.3.7/en-US) kezdőoldala különböző dokumentumokra mutató hivatkozásokat ad, amelyek dokumentumai:

- SAP ASE Learning Journey - Adminisztráció & monitoring
- SAP ASE Learning Journey - Telepítés & frissítés

hasznosak. Egy másik hasznos dokumentum az [SAP Applications on SAP Adaptive Server Enterprise gyakorlati tanácsok áttelepítésés futásidejű](https://assets.cdn.sap.com/sapcom/docs/2016/06/26450353-767c-0010-82c7-eda71af511fa.pdf).

Egyéb hasznos SAP támogatási megjegyzések a következők:

- [SAP támogatási megjegyzés #2134316](https://launchpad.support.sap.com/#/notes/2134316) 
- [SAP támogatási megjegyzés #1748888](https://launchpad.support.sap.com/#/notes/1748888) 
- [SAP támogatási megjegyzés #2588660](https://launchpad.support.sap.com/#/notes/2588660) 
- [SAP támogatási megjegyzés #1680803](https://launchpad.support.sap.com/#/notes/1680803) 
- [SAP támogatási megjegyzés #1724091](https://launchpad.support.sap.com/#/notes/1724091) 
- [SAP támogatási megjegyzés #1775764](https://launchpad.support.sap.com/#/notes/1775764) 
- [SAP támogatási megjegyzés #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [SAP támogatási megjegyzés #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- [SAP támogatási megjegyzés #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- [SAP támogatási megjegyzés #1750510](https://launchpad.support.sap.com/#/notes/1750510) 
- [SAP támogatási megjegyzés #1752266](https://launchpad.support.sap.com/#/notes/1752266) 
- [SAP támogatási megjegyzés #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [SAP támogatási megjegyzés #1588316](https://launchpad.support.sap.com/#/notes/158831) 

Egyéb információkat a 

- [SAP-alkalmazások az SAP Adaptive Server Enterprise szolgáltatásban](https://community.sap.com/topics/applications-on-ase)
- [SAP ASE információs központ](http://infocenter.sybase.com/help/index.jsp) 
- [AZ SAP ASE mindig bekapcsolva a harmadik DR-csomópont beállításával](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)

A havi hírlevél az [SAP támogatási megjegyzésén](https://launchpad.support.sap.com/#/notes/2381575) keresztül jelenik meg #2381575 


## <a name="next-steps"></a>További lépések
Tekintse meg a [cikkSAP számítási feladatok az Azure-ban: tervezési és üzembe helyezési ellenőrzőlista](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)

