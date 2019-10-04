---
title: Teljesítményre vonatkozó irányelvek az Azure-beli SQL Serverhoz | Microsoft Docs
description: Útmutatást nyújt a SQL Server teljesítményének optimalizálásához Microsoft Azure virtuális gépeken.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/26/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 6a386096d8a94c240e9a00457d87d04254e02920
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102007"
---
# <a name="performance-guidelines-for-sql-server-in-azure-virtual-machines"></a>Teljesítményre vonatkozó irányelvek az Azure-beli SQL Server Virtual Machines

## <a name="overview"></a>Áttekintés

Ez a cikk útmutatást nyújt a Microsoft Azure virtuális gép SQL Server teljesítményének optimalizálásához. SQL Server Azure Virtual Machines-ban való futtatásakor javasoljuk, hogy a helyszíni kiszolgáló környezetében SQL Server esetében is ugyanazokat az adatbázis-teljesítmény-hangolási beállításokat használja. A relációs adatbázisok nyilvános felhőbeli teljesítménye azonban számos tényezőtől függ, például a virtuális gép méretétől és az adatlemezek konfigurációjától.

[SQL Server a Azure Portal kiépített rendszerképek az](quickstart-sql-vm-create-portal.md) általános tárolási konfigurációval kapcsolatos ajánlott eljárásokat követve (a tároló konfigurálásával kapcsolatos további információkért lásd: [SQL Server virtuális gépek tárolási konfigurációja](virtual-machines-windows-sql-server-storage-configuration.md)). A kiépítés után érdemes lehet a cikkben ismertetett egyéb optimalizálásokat alkalmazni. Kiválaszthatja a számítási feladatokat, és ellenőrizheti a tesztelést.

> [!TIP]
> Általában a költségek optimalizálása és a teljesítmény optimalizálása közötti kompromisszumok állnak fenn. Ez a cikk az Azure-beli virtuális gépek SQL Server *legjobb* teljesítményének beszerzésére koncentrál. Ha a számítási feladat kevésbé igényes, előfordulhat, hogy az alábbiakban felsorolt összes optimalizálás nem szükséges. A javaslatok kiértékelése során vegye figyelembe a teljesítményre vonatkozó igényeket, a költségeket és a számítási feladatok mintáit.

## <a name="quick-check-list"></a>Gyors ellenőrzési lista

Az Azure Virtual Machines SQL Server optimális teljesítményének gyors listáját az alábbi lista tartalmazza:

| Terület | Optimalizálás |
| --- | --- |
| [Virtuális gép mérete](#vm-size-guidance) | - [DS3_v2](../sizes-general.md) vagy újabb az SQL Enterprise kiadáshoz.<br/><br/> - [DS2_v2](../sizes-general.md) vagy magasabb az SQL standard és a web kiadásaihoz. |
| [Storage](#storage-guidance) | – [Prémium SSD](../disks-types.md)-k használata. A standard szintű tárolás csak fejlesztési és tesztelési célokra ajánlott.<br/><br/> – A [Storage-fiók](../../../storage/common/storage-create-storage-account.md) és a SQL Server VM megtartása ugyanabban a régióban.<br/><br/> * Tiltsa le az Azure [geo-redundáns tárterületet](../../../storage/common/storage-redundancy.md) (Geo-replikáció) a Storage-fiókon. |
| [Lemezek](#disks-guidance) | – Használjon legalább 2 P30- [lemezt](../disks-types.md#premium-ssd) (1 a naplófájlokhoz és 1 az adatfájlokhoz, beleértve a tempdb is). A ~ 50 000 IOPS-t igénylő munkaterhelések esetében érdemes ultra SSD használni. <br/><br/> -Ne használjon operációs rendszert vagy ideiglenes lemezeket az adatbázis-tároláshoz vagy a naplózáshoz.<br/><br/> – Engedélyezze az olvasási gyorsítótárazást az adatfájlokat és a TempDB tartalmazó lemezeken.<br/><br/> -Ne engedélyezze a naplófájlt futtató lemez (ek) gyorsítótárazását.  **Fontos**: Állítsa le a SQL Server szolgáltatást egy Azure-beli virtuálisgép-lemez gyorsítótár-beállításainak módosításakor.<br/><br/> – Több Azure-adatlemez több, az i/o-átviteli sebességének növelésére.<br/><br/> – Dokumentált kiosztási méretekkel rendelkező formátum. <br/><br/> – Helyezze el a tempdb a helyi `D:\` SSD-meghajtón a kritikus fontosságú SQL Server számítási feladatokhoz (a virtuális gép méretének kiválasztása után). További információ a blogban az [SSD-k használatával a tempdb tárolásához](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/).  |
| [I/O](#io-guidance) |– Adatbázis-oldal tömörítésének engedélyezése.<br/><br/> – Az azonnali fájl inicializálásának engedélyezése az adatfájlokhoz.<br/><br/> – Korlátozza az adatbázis újranövekedését.<br/><br/> – Tiltsa le az adatbázis autozsugorodását.<br/><br/> – Az összes adatbázis áthelyezése adatlemezekre, beleértve a rendszeradatbázisokat is.<br/><br/> – Helyezze át SQL Server a hibanapló és a nyomkövetési fájl könyvtárait az adatlemezekre.<br/><br/> – Az alapértelmezett biztonsági mentési és adatbázisfájl-tárolóhelyek beállítása.<br/><br/> – Engedélyezze a zárolt lapokat.<br/><br/> – SQL Server teljesítmény-javítások alkalmazása. |
| [Szolgáltatás-specifikus](#feature-specific-guidance) | – Közvetlenül a blob Storage-ba történő biztonsági mentés. |

Ha többet szeretne megtudni ezekről az optimalizálásokról, tekintse át a következő részben ismertetett részleteket és útmutatást.

## <a name="vm-size-guidance"></a>VIRTUÁLIS gépek méretére vonatkozó útmutató

A teljesítményre érzékeny alkalmazások esetében ajánlott a következő [virtuálisgép-méretek](../sizes.md)használata:

* **SQL Server Enterprise Edition**: DS3_v2 vagy újabb
* **SQL Server standard és webes kiadások**: DS2_v2 vagy újabb

[DSv2 sorozat](../sizes-general.md#dsv2-series) A virtuális gépek támogatják a Premium Storage-t, ami a legjobb teljesítmény érdekében ajánlott. Az itt javasolt méretek az alapkonfigurációk, de a kiválasztott virtuálisgép-méret a számítási feladatok követelményeitől függ. A DSv2 sorozatú virtuális gépek olyan általános célú virtuális gépek, amelyek különböző számítási feladatokhoz jók, míg más gépek méreteit adott számítási feladatokra optimalizáltuk. Az [M sorozat](../sizes-memory.md#m-series) például a legnagyobb SQL Server számítási feladatokhoz a legmagasabb vCPU-darabszámot és memóriát kínálja. A [GS-sorozat](../sizes-previous-gen.md#gs-series) és a [DSv2 sorozat 11-15](../sizes-memory.md#dsv2-series-11-15) nagy memória-követelményekre van optimalizálva. Mindkét sorozat korlátozott [méretű](../../windows/constrained-vcpu.md)alapméretekben is elérhető, ami csökkenti a számítási feladatokhoz szükséges pénzmennyiséget. Az [ls-sorozatú](../sizes-storage.md) gépek nagy méretű átviteli sebességre és IO-ra vannak optimalizálva. Fontos, hogy figyelembe vegye az adott SQL Server számítási feladatait, és alkalmazza ezt a virtuálisgép-sorozatok és-méretek kiválasztására.

## <a name="storage-guidance"></a>Storage – útmutató

A DS-sorozat (valamint a DSv2 sorozat és a GS-sorozat) a [prémium SSD](../disks-types.md)-ket támogatja. A prémium SSD-k minden éles számítási feladathoz ajánlottak.

> [!WARNING]
> A szabványos HDD-k és SSD-k eltérő késéssel és sávszélességgel rendelkeznek, és csak fejlesztési/tesztelési feladatokhoz ajánlottak. Az éles számítási feladatoknak prémium SSD-ket kell használniuk.

Azt javasoljuk továbbá, hogy az Azure Storage-fiókot ugyanabban az adatközpontban hozza létre, mint a SQL Server virtuális gépek az átvitel késésének csökkentése érdekében. A Storage-fiók létrehozásakor a földrajzi replikálást a több lemezre kiterjedő konzisztens írási sorrendként tiltsa le. Ehelyett érdemes lehet egy SQL Server vész-helyreállítási technológiát beállítani két Azure-adatközpont között. További információ: a [magas rendelkezésre állás és a vész-helyreállítás az Azure Virtual Machines SQL Server](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Útmutató a lemezekhez

Az Azure-beli virtuális gépeken három fő lemez típus létezik:

* **Operációsrendszer-lemez**: Azure-beli virtuális gép létrehozásakor a platform legalább egy lemezt ( **C** meghajtóként címkézett) helyez el a virtuális géphez az operációs rendszer lemezéhez. Ez a lemez egy, a tárolóban blobként tárolt VHD.
* **Ideiglenes lemez**: Az Azure Virtual Machines egy másik lemezt tartalmaz, amelyet az ideiglenes lemez ( **D**: meghajtóként jelölt) nevezett. Ez egy lemez a csomóponton, amely felhasználható a tárhelyhez.
* Adatlemezek: Adatlemezként további lemezeket is csatlakoztathat a virtuális géphez, ezeket a rendszer a Storage-ban blobként tárolja.

A következő szakaszok a különböző lemezek használatára vonatkozó javaslatokat ismertetik.

### <a name="operating-system-disk"></a>Operációsrendszer-lemez

Az operációsrendszer-lemez olyan virtuális merevlemez, amely az operációs rendszer futó verziójaként indítható el és csatlakoztatható, és **C** meghajtóként van megjelölve.

Az operációs rendszer lemezének alapértelmezett gyorsítótárazási házirendje **írható/olvasható**. A teljesítményre érzékeny alkalmazások esetében javasoljuk, hogy az operációs rendszer lemeze helyett adatlemezeket használjon. Tekintse meg az alábbi adatlemezek című szakaszt.

### <a name="temporary-disk"></a>Ideiglenes lemez

A **D**: meghajtóként megjelölt ideiglenes tárolóeszköz nem marad meg az Azure Blob Storage-ban. Ne tárolja a felhasználói adatbázis fájljait vagy a felhasználói tranzakció naplófájljait a **D**: meghajtón.

A D sorozat, a Dv2 sorozat és a G sorozatú virtuális gépek esetében az ideiglenes meghajtó a virtuális gépeken SSD-alapú. Ha a számítási feladat nagy mennyiségű TempDB (például ideiglenes objektumokat vagy összetett illesztéseket) tesz lehetővé, a **D** meghajtón tárolt TempDB magasabb tempdb átviteli sebességhez és alacsonyabb tempdb késéshez vezethet. Példaként tekintse meg a TempDB-vitát a következő blogbejegyzésben: [Tárolási konfigurációs irányelvek az Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm)-beli virtuális gépek SQL Serverához.

A prémium SSD-ket (DS-sorozatot, DSv2 és GS-sorozatot) támogató virtuális gépek esetében javasoljuk, hogy a TempDB egy olyan lemezen tárolja, amely támogatja a prémium SSD-ket, és engedélyezze az olvasási gyorsítótárazást.

Erre a javaslatra egyetlen kivétel vonatkozik: _Ha a tempdb használata írási igényű, nagyobb teljesítmény érhető el, ha a tempdb a helyi D meghajtón tárolja, amely az SSD- **n** alapul._ További információkért tekintse át az SSD-k [használata a tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) blogban című témakört. 

### <a name="data-disks"></a>Adatlemezek

* Adatlemezek használata adatfájlokhoz **és naplófájlokhoz**: Ha nem használ lemezes csíkozást, használjon két prémium szintű SSD P30-lemezt, amelyekben az egyik lemez tartalmazza a naplófájl (oka) t, a másik pedig az adatok és a TempDB (kivéve a kritikus és a nagy mennyiségű, a fentiekben említett) feladatokat. A prémium szintű SSD-k számos IOPs és sávszélességet (MB/s) biztosítanak a mérettől függően, a cikkben látható módon, a [lemez típusának](../disks-types.md)kiválasztásával. Ha lemezes csíkozási technikát (például tárolóhelyeket) használ, az optimális teljesítményt úgy érheti el, hogy két készlettel rendelkezik, egyet a naplófájl (ok) hoz, és a másikat az adatfájlokhoz. Ha azonban azt tervezi, hogy SQL Server feladatátvételi fürtszolgáltatást használ, konfigurálnia kell egy készletet.

   > [!TIP]
   > - A különböző lemezekre és munkaterhelés-konfigurációkra vonatkozó tesztelési eredményekért tekintse meg a következő blogbejegyzést: [Tárolási konfigurációs irányelvek az Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)-beli virtuális gépek SQL Serverához.
   > - A ~ 50 000 IOPS-t igénylő SQL-kiszolgálók esetében a kritikus fontosságú teljesítmény érdekében érdemes megfontolni a 10 P30 lemez cseréjét egy ultra SSD. További információt a következő blogbejegyzésben talál: [Kritikus teljesítmény a Ultra SSDokkal](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/).

   > [!NOTE]
   > Amikor kiépít egy SQL Server VM a portálon, lehetősége van a tárolási konfiguráció szerkesztésére. A konfigurációtól függően az Azure egy vagy több lemezt konfigurál. Több lemez egyetlen, csíkozásos tárolóba van összevonva. Ebben a konfigurációban az adatfájlok és a naplófájlok is együtt találhatók. További információ: [SQL Server virtuális gépek tárolási konfigurációja](virtual-machines-windows-sql-server-storage-configuration.md).

* **Lemezek csíkozása**: További átviteli sebesség esetén további adatlemezeket adhat hozzá, és a lemezek csíkozását is használhatja. Az adatlemezek számának meghatározásához elemezni kell a naplófájl (ok) hoz szükséges IOPS és sávszélességet, valamint az adatait és a TempDB-fájl (oka) t. Figyelje meg, hogy a különböző virtuálisgép-méretek eltérő korlátokkal rendelkeznek a támogatott IOPs és sávszélességgel kapcsolatban: a IOPS lévő táblák a [virtuális gépek méretétől](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)függően. Használja az alábbi irányelveket:

  * Windows 8/Windows Server 2012 vagy újabb rendszer esetén a [tárolóhelyek](https://technet.microsoft.com/library/hh831739.aspx) a következő irányelvekkel használhatók:

      1. Az adattárház-számítási feladatokhoz a OLTP számítási feladatainak és a 256 KB 262144 (65536 bájt) értékének beállításával állítsa be az adattárolási munkaterhelések teljesítményének romlását, hogy elkerülje 64 a partíciók hibás igazítása miatti hatást. Ezt a PowerShell-lel kell beállítani.
      2. Oszlopok számának beállítása = fizikai lemezek száma. A PowerShell használata a több mint 8 lemez konfigurálásához (a Kiszolgálókezelő felhasználói felülete nem). 

    A következő PowerShell például létrehoz egy új tárolót, amely az összevont méretet 64 KB-ra, az oszlopok száma pedig 2:

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Windows 2008 R2 vagy korábbi verzió esetén a dinamikus lemezeket (operációsrendszer-csíkozott kötetek) is használhatja, és a sáv mérete mindig 64 KB. Vegye figyelembe, hogy ez a beállítás a Windows 8/Windows Server 2012 rendszertől kezdve elavult. További információ: a virtuális lemez szolgáltatás támogatási nyilatkozata a [Windows Storage Management API-ra való áttérést](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx)ismerteti.

  * Ha [közvetlen tárolóhelyekt (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) használ [SQL Server feladatátvevő fürt példányaival](virtual-machines-windows-portal-sql-create-failover-cluster.md), egyetlen készletet kell konfigurálnia. Vegye figyelembe, hogy bár a különböző kötetek hozhatók létre az adott készleten, mind ugyanazok a jellemzők, mint például a gyorsítótárazási házirend.

  * Határozza meg a tárolási készlethez társított lemezek számát a betöltési várakozások alapján. Ne feledje, hogy a különböző virtuálisgép-méretek különböző számú csatlakoztatott adatlemezt tesznek lehetővé. További információ: [Virtual Machines méretei](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Ha nem a prémium szintű SSD-ket (fejlesztési/tesztelési forgatókönyvek) használja, a javaslat a [virtuális gép mérete](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) által támogatott adatlemezek maximális számának hozzáadására és a lemezes csíkozás használatára.

* **Gyorsítótárazási házirend**: Vegye figyelembe a következő javaslatokat a gyorsítótárazási házirendhez a tárolási konfigurációtól függően.

  * Ha külön lemezeket használ az adatfájlok és a naplófájlok számára, engedélyezze az olvasási gyorsítótárazást az adatfájlokat tároló adatlemezeken és a TempDB-adatfájlokban. Ez jelentős teljesítménybeli haszonhoz vezethet. A naplófájlt tároló lemezen ne engedélyezze a gyorsítótárazást, mivel ez kisebb mértékű csökkenést okoz a teljesítményben.

  * Ha egyetlen tárolóban használ lemezes szalagot, a legtöbb számítási feladat kihasználhatja az olvasási gyorsítótárazást. Ha a napló-és adatfájlok külön tárolóval rendelkeznek, engedélyezze az olvasási gyorsítótárazást csak az adatfájlok tárolási készletén. Bizonyos nagy mennyiségű írási feladatokban a jobb teljesítmény a gyorsítótárazás nélkül is megvalósítható. Ezt csak teszteléssel lehet meghatározni.

  * Az előző javaslatok a prémium SSD-k esetében érvényesek. Ha nem használ prémium SSD-ket, ne engedélyezzen semmilyen gyorsítótárazást az adatlemezeken.

  * A lemezek gyorsítótárazásának konfigurálásával kapcsolatos utasításokért tekintse meg a következő cikkeket. A klasszikus (ASM) üzembe helyezési modell esetében lásd: [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) és [set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx). A Azure Resource Manager telepítési modellnél lásd: [Set-AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) és [set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Állítsa le a SQL Server szolgáltatást az Azure-beli virtuálisgép-lemezek gyorsítótár-beállításainak módosításakor, hogy elkerülje az adatbázis sérülésének lehetőségét.

* **NTFS-foglalási egység mérete**: Az adatlemez formázása esetén ajánlott 64 KB-os foglalási egység méretet használni az adatfájlok és a naplófájlok, valamint a TempDB esetében.

* **Lemezkezelés – ajánlott eljárások**: Amikor eltávolít egy adatlemezt, vagy megváltoztatja a gyorsítótár típusát, állítsa le a SQL Server szolgáltatást a módosítás során. Ha megváltoznak a gyorsítótárazási beállítások az operációsrendszer-lemezen, az Azure leállítja a virtuális gépet, megváltoztatja a gyorsítótár típusát, és újraindítja a virtuális gépet. Az adatlemezek gyorsítótár-beállításainak módosításakor a virtuális gép nem áll le, de az adatlemez le van választva a virtuális gépről a módosítás során, majd újra csatolva lesz.

  > [!WARNING]
  > Ha nem sikerül leállítani a SQL Server szolgáltatást ezen műveletek során, az adatbázis sérülését okozhatja.


## <a name="io-guidance"></a>I/O-útmutatás

* Az alkalmazás és a kérések integrálással a legjobb prémium SSD-k eredményei érhetők el. A prémium SSD-k olyan forgatókönyvek esetén lettek kialakítva, ahol az IO-várólista mélysége 1-nél nagyobb, így az egyszálas soros kérések esetében kevés vagy semmilyen teljesítménybeli nyereség fog megjelenni (még akkor is, ha a tárterület-igényes). Ez hatással lehet például a teljesítmény-elemzési eszközök, például a SQLIO egyszálas tesztelési eredményeire.

* Érdemes lehet az [adatbázis-lapok tömörítését](https://msdn.microsoft.com/library/cc280449.aspx) használni, mivel az nagy I/O-igényű számítási feladatok teljesítményének javításához is hozzájárul. Előfordulhat azonban, hogy az adattömörítés növelheti a CPU-felhasználást az adatbázis-kiszolgálón.

* Érdemes lehet engedélyezni az azonnali fájl inicializálását, hogy csökkentse a kezdeti fájl lefoglalásához szükséges időt. Az azonnali fájl inicializálásának kihasználásához adja meg az SQL Server (MSSQLSERVER) szolgáltatásfiókot a SE_MANAGE_VOLUME_NAME, és vegye fel a **kötet-karbantartási feladatok** elvégzése biztonsági szabályzatba. Ha SQL Server platform-rendszerképet használ az Azure-hoz, a rendszer nem adja hozzá az alapértelmezett szolgáltatásfiókot (NT Service\MSSQLSERVER) a **kötet-karbantartási feladatok végrehajtása** biztonsági házirendhez. Más szóval az azonnali fájl inicializálása nincs engedélyezve egy SQL Server Azure platform-rendszerképben. Miután hozzáadta a SQL Server szolgáltatásfiókot a **kötet-karbantartási feladatok** elvégzése biztonsági házirendhez, indítsa újra a SQL Server szolgáltatást. Ennek a funkciónak a használatához biztonsági megfontolásokat lehet használni. További információ: [adatbázisfájlok inicializálása](https://msdn.microsoft.com/library/ms175935.aspx).

* az automatikus növekedés csak a váratlan növekedést okozó készenléti értéknek minősül. Az automatikus növekedéssel nem kezelheti az adatait, és napi rendszerességgel is naplózhatja a növekedést. Ha az automatikus növekedés használatban van, a méret kapcsoló használatával növelje a fájl előzetes növekedését.

* Ügyeljen arra , hogy a szükségtelen terhelés elkerülése érdekében az AutoShrink le legyen tiltva, ami negatív hatással lehet a teljesítményre.

* Az összes adatbázis áthelyezése adatlemezekre, beleértve a rendszeradatbázisokat is. További információ: rendszeradatbázisok [áthelyezése](https://msdn.microsoft.com/library/ms345408.aspx).

* Helyezze át SQL Server a hibanapló és a nyomkövetési fájl könyvtárait az adatlemezekre. Ezt a SQL Server Konfigurációkezelő az SQL Server példányra jobb gombbal kattintva, majd a tulajdonságok elemet választva végezheti el. A hibanapló és a nyomkövetési fájl beállításai módosíthatók az **Indítási paraméterek** lapon. A memóriakép könyvtára a **speciális** lapon van megadva. Az alábbi képernyőfelvételen a hibanapló indítási paraméterének megkeresése látható.

    ![Az SQL ErrorLog képernyőképe](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* Alapértelmezett biztonsági mentési és adatbázisfájl-tárolóhelyek beállítása. Használja az ebben a cikkben szereplő javaslatokat, majd hajtsa végre a módosításokat a kiszolgáló tulajdonságai ablakban. Útmutatásért lásd: az adatfájlok [és naplófájlok alapértelmezett helyeinek megtekintése vagy módosítása (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Az alábbi képernyőképen a módosításokat hajthatja végre.

    ![Az SQL-adatnapló és a biztonságimásolat-fájlok](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* Az i/o és a lapozási tevékenységek csökkentéséhez engedélyezze a zárolt lapokat. További információ: [a zárolási lapok engedélyezése a memóriában (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

* Ha SQL Server 2012-es verzióját futtatja, telepítse a 10. összegző frissítést. Ez a frissítés azt a javítást tartalmazza, amely gyenge teljesítményt tesz elérhetővé az I/O-ben, ha a SELECT INTO ideiglenes TABLE utasítást SQL Server 2012-ban hajtja végre. További információkért tekintse meg ezt a tudásbázist ismertető [cikket](https://support.microsoft.com/kb/2958012).

* Az Azure-ba történő átvitelkor érdemes lehet bármilyen adatfájlt tömöríteni.

## <a name="feature-specific-guidance"></a>Szolgáltatás-specifikus útmutató

Néhány üzemelő példány további teljesítménybeli előnyöket érhet el fejlettebb konfigurációs módszerek használatával. Az alábbi lista néhány olyan SQL Server funkciót mutat be, amelyek segítségével jobb teljesítményt érhet el:

### <a name="backup-to-azure-storage"></a>Biztonsági mentés az Azure Storage-ba
Az Azure Virtual Machines szolgáltatásban futó SQL Server biztonsági mentésének végrehajtásakor [SQL Server biztonsági mentést is használhat az URL-címre](https://msdn.microsoft.com/library/dn435916.aspx). Ez a funkció SQL Server 2012 SP1 CU2 kezdődően érhető el, és ajánlott a csatlakoztatott adatlemezekre történő biztonsági mentéshez. Az Azure Storage-ba történő biztonsági mentés/visszaállítás során kövesse az Azure [Storage-ban tárolt biztonsági másolatokból származó ajánlott eljárásokat és hibaelhárítási és visszaállítási SQL Server biztonsági mentéssel](https://msdn.microsoft.com/library/jj919149.aspx)kapcsolatos ajánlásokat. Ezeket a biztonsági mentéseket automatizálhatja az [Azure Virtual Machines SQL Server automatikus biztonsági mentésével](virtual-machines-windows-sql-automated-backup.md)is.

SQL Server 2012 előtt a [SQL Server Backup az Azure](https://www.microsoft.com/download/details.aspx?id=40740)-eszközre használható. Ez az eszköz segít a biztonsági másolatok átviteli sebességének növelésében több biztonsági mentési szalagos cél használatával.

### <a name="sql-server-data-files-in-azure"></a>Adatfájlok SQL Server az Azure-ban

Ez az új funkció, [SQL Server adatfájlok az Azure-ban](https://msdn.microsoft.com/library/dn385720.aspx), a SQL Server 2014-es verziótól kezdődően érhető el. Az Azure-beli adatfájlokkal rendelkező SQL Server az Azure-adatlemezeket használó hasonló teljesítménybeli jellemzőkkel bírnak.

### <a name="failover-cluster-instance-and-storage-spaces"></a>Feladatátvevő fürt példánya és tárolóhelyek

Ha tárolóhelyeket használ, amikor csomópontokat ad hozzá a fürthöz a **jóváhagyás** lapon, törölje az **összes megfelelő tároló hozzáadása a fürthöz**jelölőnégyzet jelölését. 

![Jogosult tároló törlése](media/virtual-machines-windows-sql-performance/uncheck-eligible-cluster-storage.png)

Ha tárolóhelyeket használ, és nem törli **az összes megfelelő tároló hozzáadása a fürthöz**lehetőséget, a Windows leválasztja a virtuális lemezeket a fürtözési folyamat során. Ennek eredményeképpen azok nem jelennek meg a Lemezkezelés eszközben vagy az Intézőben, amíg a tárolóhelyek el nem lesznek távolítva a fürtből, és nem kapcsolódnak újra a PowerShell használatával. A tárolóhelyek több lemezt is csoportosítanak a Storage-készletekbe. További információ: tárolóhelyek [](/windows-server/storage/storage-spaces/overview).

## <a name="next-steps"></a>További lépések

További információ a tárolásról és a teljesítményről: a [tárolási konfigurációs irányelvek az Azure-beli virtuális gépen SQL Server](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)

Az ajánlott biztonsági eljárásokért tekintse meg az [Azure Virtual Machines SQL Server biztonsági szempontjait](virtual-machines-windows-sql-security.md).

Tekintse át az Azure-beli [SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)SQL Server virtuális gépekkel kapcsolatos további cikkeket Virtual Machines áttekintését. Ha kérdése van az SQL Servert futtató virtuális gépek használatával kapcsolatban, tekintse meg a [gyakori kérdéseket](virtual-machines-windows-sql-server-iaas-faq.md).
