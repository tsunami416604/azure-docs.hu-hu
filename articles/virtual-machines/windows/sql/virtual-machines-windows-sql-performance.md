---
title: Az Azure-beli SQL Server teljesítményre vonatkozó irányelvei | Microsoft dokumentumok
description: Az SQL Server teljesítményének microsoft Azure-beli virtuális gépeken való optimalizálásának irányelvei.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/18/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 880f1c601cf4132fdec9e5d25b1bf1f2ff175ab7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650537"
---
# <a name="performance-guidelines-for-sql-server-in-azure-virtual-machines"></a>Teljesítményre vonatkozó irányelvek SQL Serverhez az Azure Virtual Machines szolgáltatásban

## <a name="overview"></a>Áttekintés

Ez a cikk útmutatást nyújt az SQL Server teljesítményének optimalizálásához a Microsoft Azure virtuális gépben. Az SQL Server Azure Virtual Machinesben való futtatása során azt javasoljuk, hogy továbbra is az adatbázis-teljesítmény azon hangolási beállításait használja, amelyek a helyszíni kiszolgálókörnyezetben az SQL Server esetében meg vannak adva. A relációs adatbázisok nyilvános felhőbeli teljesítménye azonban számos tényezőtől függ, például a virtuális gép méretétől és az adatlemezek konfigurációjától.

[Az Azure Portalon kiépített SQL Server-lemezképek az](quickstart-sql-vm-create-portal.md) általános tárolási konfigurációs gyakorlati tanácsokat követik (a tárolás konfigurálásával kapcsolatos további tudnivalókért lásd: [Sql Server virtuális gépek tárolási konfigurációja).](virtual-machines-windows-sql-server-storage-configuration.md) Kiépítés után fontolja meg a cikkben tárgyalt egyéb optimalizálások alkalmazását. A számítási feladatok alapján a lehetőségeket a teszteléssel ellenőrizheti.

> [!TIP]
> Általában kompromisszumot kell kötni a költségek optimalizálása és a teljesítményoptimalizálás között. Ez a cikk az SQL Server *legjobb* teljesítményének az Azure-beli virtuális gépeken való elérése. Ha a munkaterhelés kevésbé igényes, előfordulhat, hogy nem kell minden optimalizálás az alább felsorolt. A javaslatok kiértékelése korfigyelembe veszi a teljesítményigényeket, a költségeket és a számítási feladatok mintáit.

## <a name="quick-check-list"></a>Gyors ellenőrző lista

Az alábbiakban az SQL Server azure-beli virtuális gépeken való optimális teljesítményének gyors ellenőrző listája látható:

| Terület | Optimalizálás |
| --- | --- |
| [Virtuális gép mérete](#vm-size-guidance) | - Használjon vm-méreteket 4 vagy több vCPU-val, például [E4S_v3](../../ev3-esv3-series.md) vagy magasabb, vagy [DS12_v2](../../dv2-dsv2-series-memory.md) vagy magasabb.<br/><br/> - [Az Es, Eas, Ds és Das sorozat](../../sizes-general.md) az OLTP munkaterhelési teljesítményéhez szükséges optimális memória-vCPU arányt kínálja. <br/><br/> - [Az M sorozat](../../m-series.md) a kritikus fontosságú teljesítményhez szükséges legmagasabb memória-vCPU arányt kínálja, és ideális az adattárház-munkaterhelésekhez. <br/><br/> - Gyűjtse össze a célszámítási feladat [IOPS,](../premium-storage-performance.md#iops) [átviteli és](../premium-storage-performance.md#throughput) [késési](../premium-storage-performance.md#latency) követelmények csúcsidőben az [alkalmazás teljesítménykövetelményeinek ellenőrzőlistájának](../premium-storage-performance.md#application-performance-requirements-checklist) követésével, majd válassza ki a [virtuális gép méretét,](../sizes-general.md) amely a számítási feladatok teljesítménykövetelményeihez skálázható.|
| [Tárterület](#storage-guidance) | - Az SQL Server teljesítményének részletes tesztelését a TPC-E és TPC_C referenciaértékekkel rendelkező Azure-beli virtuális gépeken az [OLTP teljesítményének optimalizálása](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)című blogban talál. <br/><br/> - Használja [prémium SSD](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794) a legjobb ár / teljesítmény előnyeit. Az adatfájlok hoz a [Csak olvasási gyorsítótár](../premium-storage-performance.md#disk-caching) konfigurálása, a naplófájlhoz pedig nincs gyorsítótár. <br/><br/> - Használja [az Ultra Disks,](../disks-types.md#ultra-disk) ha kevesebb, mint 1 ms tárolási késések van szükség a munkaterhelés. <br/><br/> - Gyűjtse össze az SQL Server-adatok, napló és Temp DB fájlok tárolási késési követelményeit [az alkalmazás figyelésével](../premium-storage-performance.md#application-performance-requirements-checklist) a lemeztípus kiválasztása előtt. Ha <1 ms tárolási késések szükségesek, majd használja az Ultra Disks, egyébként prémium szintű SSD-t használjon. Ha alacsony késés csak a naplófájlhoz van szükség, adatfájlokhoz nem, akkor [az Ultra Disk](../disks-enable-ultra-ssd.md) a szükséges IOPS-on és átviteli szinteken csak a naplófájlhoz legyen szükség. <br/><br/> -  [Prémium fájlmegosztások](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) ajánlott megosztott tárolóként egy SQL Server feladatátvevő fürtpéldány. A prémium fájlmegosztások nem támogatják a gyorsítótárazást, és a prémium SSD-lemezekhez képest korlátozott teljesítményt nyújtanak. Válasszon prémium szintű SSD-vel kezelt lemezeket a prémium fájlmegosztásokon keresztül az önálló SQL-példányokhoz; prémium fájlmegosztásokat azonban a feladatátvevő fürtpéldány megosztott tárolásához használja ki a karbantartás megkönnyítése és a rugalmas méretezhetőség érdekében. <br/><br/> - A szabványos tárolás csak fejlesztési és tesztelési célokra vagy biztonsági másolatfájlokhoz ajánlott, és nem használható éles számítási feladatokhoz. <br/><br/> - Tartsa a [tárfiókot](../../../storage/common/storage-create-storage-account.md) és az SQL Server virtuális gép ugyanabban a régióban.<br/><br/> - Tiltsa le az Azure [georedundáns tárolás](../../../storage/common/storage-redundancy.md) (georeplikáció) a tárfiókban.  |
| [Lemezek](#disks-guidance) | - Használjon legalább 2 [prémium SSD lemezt](../disks-types.md#premium-ssd) (1 a naplófájlhoz és 1 az adatfájlokhoz). <br/><br/> - Az 1 ms-os IO-késések <et igénylő munkaterhelések esetén engedélyezze az írásgyorsítót az M sorozathoz, és fontolja meg az Ultra SSD-lemezek használatát az Es és DS sorozatok hoz. <br/><br/> - Engedélyezze [az írásvédett gyorsítótárazást](../premium-storage-performance.md#disk-caching) az adatfájlokat tároló lemez(ek)en.<br/><br/> - További 20% prémium IOPS/átviteli kapacitás hozzáadása, mint amit a munkaterhelés igényel [az SQL Server-adatok, napló- és TempDB-fájlok tárolásának konfigurálásakor](virtual-machines-windows-sql-server-storage-configuration.md) <br/><br/> - Ne használjon operációs rendszert vagy ideiglenes lemezeket adatbázis-tároláshoz vagy naplózáshoz.<br/><br/> - A naplófájlt tároló lemez(ek)en ne engedélyezze a gyorsítótárazást.  **Fontos:** Állítsa le az SQL Server szolgáltatás, ha egy Azure VM-lemez gyorsítótár-beállításait módosítja.<br/><br/> - Csík több Azure adatlemezek, hogy nagyobb tárolási átviteli.<br/><br/> - Formátum dokumentált felosztási méretekkel. <br/><br/> - Helyezze a TempDB-t `D:\` a helyi SSD-meghajtóra a kritikus SQL Server-munkaterhelések esetén (a megfelelő virtuális gépméret kiválasztása után). Ha létrehozza a virtuális gép az Azure Portalon vagy az Azure-ban gyorsindítási sablonokat, és [helyezze temp DB a helyi lemezen,](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583) akkor nem kell további lépéseket; minden más esetben kövesse az [SSD-k használatának](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) blogjában leírt lépéseket a TempDB tárolásához az újraindítás utáni hibák megelőzése érdekében. Ha a helyi meghajtó kapacitása nem elegendő a Temp DB méretéhez, akkor helyezze a Temp DB-t egy prémium szintű SSD-lemezekre [lecsupaszított](../premium-storage-performance.md) tárolókészletre, [írásvédett gyorsítótárazással.](../premium-storage-performance.md#disk-caching) |
| [I/o](#io-guidance) |- Adatbázislap tömörítésének engedélyezése.<br/><br/> - Azonnali fájlinicializálás engedélyezése adatfájlokhoz.<br/><br/> - Korlátozza az adatbázis autogrowth.<br/><br/> - Tiltsa le az adatbázis automatikus zsugorítását.<br/><br/> - Helyezze át az összes adatbázist az adatlemezekre, beleértve a rendszeradatbázisokat is.<br/><br/> - Helyezze át az SQL Server hibanaplóját és a fájlkönyvtárak nyomon követését adatlemezekre.<br/><br/> - Állítsa be az alapértelmezett biztonsági mentési és adatbázisfájl-helyeket.<br/><br/> - [Zárolt lapok engedélyezése a memóriában](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows?view=sql-server-2017).<br/><br/> - Sql Server teljesítményjavítások alkalmazása. |
| [Szolgáltatásspecifikus](#feature-specific-guidance) | - Biztonsági másolatot közvetlenül a blob storage.<br/><br/>- A 12 TB-nál nagyobb [adatbázisokhoz használjon fájlpillanatkép-biztonsági mentéseket.](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) <br/><br/>- Használjon több Temp DB fájlt, 1 fájlt magonként, legfeljebb 8 fájlt.<br/><br/>- Állítsa max szerver memória 90%, vagy legfeljebb 50 GB maradt az operációs rendszer. <br/><br/>- Lágy NUMA engedélyezése. |

Ha többet szeretne tudni *arról,* hogyan és *miért* kell ezeket az optimalizálásokat elkészíteni, kérjük, olvassa el a következő szakaszokban található részleteket és útmutatást.

## <a name="vm-size-guidance"></a>Virtuális gép méretével kapcsolatos útmutatás

Kezdje azzal, hogy csúcsidőben gyűjti a számítási feladatok processzor-, memória- és tárolási átviteli követelményeit. A \LogicalDisk\Disk Reads/Sec és a \LogicalDisk\Disk Writes/Sec teljesítményszámlálók olvasási és írási IOPS-követelmények, a \LogicalDisk\Disk Bytes/Sec számláló pedig az adatok, a napló és a temp DB fájlok [tárolási átviteli követelményeinek](../premium-storage-performance.md#disk-caching) gyűjtésére használható. Miután IOPS és átviteli követelmények csúcsértéken definiálva, majd kiértékeli a virtuális gép méretei kínálja ezt a kapacitást. Ha például a számítási feladatok hoz 20 K olvasási IOPS és 10K írási IOPS csúcsidőben, választhat E16s_v3 (legfeljebb 32 K gyorsítótárazott és 25600 nem gyorsítótárazott IOPS) vagy M16_s (legfeljebb 20 K gyorsítótárazott és 10K uncached IOPS) 2 P30 lemez. Győződjön meg arról, hogy a számítási feladatok átviteli és IOPS-követelményeit is megismerheti, mivel a virtuális gépek különböző méretezési korlátokkal rendelkeznek az IOPS-hoz és az átviteli igényhez.<br/><br/>[DSv_3](../../dv3-dsv3-series.md) és [Es_v3 sorozatú](../../ev3-esv3-series.md) házigazdája általános célú hardver intel Haswell vagy Broadwell processzorok. Az M sorozat a legnagyobb [vCPU-számot](../../m-series.md) és memóriát kínálja a legnagyobb SQL Server-munkaterhelésekhez, és a Skylake processzorcsaládmal rendelkező, memóriaoptimalizált hardveren üzemelteti. Ezek a virtuális gép sorozat támogatja a prémium szintű tárolás, amely ajánlott a legjobb teljesítményt a gazdagép szintű olvasási gyorsítótár. Mind a Es_v3, mind az M sorozat [korlátozott magméretekben](../../windows/constrained-vcpu.md)is elérhető, ami pénzt takarít meg az alacsonyabb számítási és nagy tárolókapacitással rendelkező munkaterhelések számára. 

## <a name="storage-guidance"></a>Storage – útmutató

Az SQL Server teljesítményének részletes tesztelését tpc-E és TPC_C referenciaértékekkel rendelkező Azure-virtuális gépeken az [OLTP teljesítményének optimalizálása](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)című blogban talál. 

Azure blob cache prémium szintű SSD-k ajánlott minden éles számítási feladatokhoz ajánlott. 

> [!WARNING]
> A szabványos HDD-k és SSD-k különböző késésekkel és sávszélességgel rendelkeznek, és csak fejlesztési és tesztelési számítási feladatokhoz ajánlottak. Az éles számítási feladatoknak prémium szintű SSD-ket kell használniuk.

Emellett azt javasoljuk, hogy hozza létre az Azure storage-fiók ugyanabban az adatközpontban, mint az SQL Server virtuális gépek átviteli késések csökkentése érdekében. Tárfiók létrehozásakor tiltsa le a georeplikációt, mivel a több lemezen lévő konzisztens írási sorrend nem garantált. Ehelyett fontolja meg egy SQL Server vész-helyreállítási technológia konfigurálása két Azure-adatközpontok között. További információ: [Magas rendelkezésre állású és vész-helyreállítási SQL Server az Azure virtuális gépek.](virtual-machines-windows-sql-high-availability-dr.md)

## <a name="disks-guidance"></a>Útmutató a lemezekhez

Az Azure virtuális gépen három fő lemeztípus található:

* **Operációs rendszer lemeze:** Amikor létrehoz egy Azure virtuális gépet, a platform csatolja legalább egy lemez (c-meghajtóként címkézve) a virtuális gép az operációs rendszer lemezén. **C** Ez a lemez egy virtuális merevlemez, amelyet a storage-ban lapblobként tárolnak.
* **Ideiglenes lemez**: Az Azure virtuális gépek tartalmaznak egy másik lemezt, az ideiglenes lemezt **(D**: meghajtóként címkézve). Ez egy lemez a csomóponton, amely használható a munkaterületen.
* **Adatlemezek:** Adatlemezként további lemezeket is csatlakoztathat a virtuális géphez, és ezek lapblobként tárolódnak a tárolóban.

Az alábbi szakaszok a különböző lemezek használatára vonatkozó javaslatokat ismertetik.

### <a name="operating-system-disk"></a>Operációsrendszer-lemez

Az operációs rendszer lemeze olyan virtuális merevlemez, amelyet az operációs rendszer futó verziójaként indíthat el és csatlakoztathat, és **c** meghajtóként van címkézve.

Az operációs rendszer lemezén az alapértelmezett gyorsítótárazási házirend az **Olvasás/írás**. A teljesítményérzékeny alkalmazások esetében azt javasoljuk, hogy az operációs rendszer lemeze helyett adatlemezeket használjon. Lásd az alábbi adatlemezekről szóló szakaszt.

### <a name="temporary-disk"></a>Ideiglenes lemez

Az ideiglenes tároló, **d-meghajtóként** megjelölt, nem marad meg az Azure blob storage. Ne tárolja a felhasználói adatbázisfájlokat vagy a felhasználói tranzakciós naplófájlokat a **D**: meghajtón.

Helyezze a TempDB-t `D:\` a helyi SSD-meghajtóra a kritikus SQL Server-munkaterhelések esetén (a megfelelő virtuális gépméret kiválasztása után). Ha létrehozza a virtuális gép az Azure Portalon vagy az Azure-ban gyorsindítási sablonokat, és [helyezze temp DB a helyi lemezen,](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583)akkor nem kell további lépéseket; minden más esetben kövesse az [SSD-k használatának](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) blogjában leírt lépéseket a TempDB tárolásához az újraindítás utáni hibák megelőzése érdekében. Ha a helyi meghajtó kapacitása nem elegendő a Temp DB méretéhez, akkor helyezze a Temp DB-t egy prémium szintű SSD-lemezekre [lecsupaszított](../premium-storage-performance.md) tárolókészletre, [írásvédett gyorsítótárazással.](../premium-storage-performance.md#disk-caching)

A prémium szintű SSD-ket támogató virtuális gépek esetében a TempDB-t olyan lemezen is tárolhatja, amely támogatja a prémium szintű SSD-ket, ha engedélyezve van az olvasási gyorsítótár.


### <a name="data-disks"></a>Adatlemezek

* **Prémium szintű SSD-lemezek használata adat- és naplófájlokhoz:** Ha nem használ lemezcsíkozást, használjon két prémium szintű SSD-lemezt, ahol az egyik lemez tartalmazza a naplófájlt, a másik pedig az adatokat. Minden prémium szintű SSD számos IOPS-t és sávszélességet (MB/s) biztosít a méretétől függően, ahogy az a cikkben, [a Válasszon lemeztípust](../disks-types.md). Ha lemezcsíkozási technikát használ, például tárolóhelyeket, optimális teljesítményt érhet el, ha két készlettel rendelkezik, az egyik a naplófájl(ok)hoz, a másik pedig az adatfájlokhoz. Ha azonban az SQL Server feladatátvevő fürtpéldányait (FCI) kívánja használni, konfigurálnia kell egy készletet, vagy [prémium fájlmegosztásokat](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) kell használnia.

   > [!TIP]
   > - A különböző lemez- és számításifeladatok-konfigurációk teszteredményeit a következő blogbejegyzésben talál: Tárolási konfigurációs irányelvek az [SQL Server hez az Azure VM-ben.](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)
   > - A ~ 50 000 IOPS-t igénylő SQL-kiszolgálók kritikus teljesítményéhez érdemes 10 -P30 lemezt ultra SSD-re cserélni. További információt a következő blogbejegyzésben talál: Kritikus teljesítmény az [Ultra SSD-vel.](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/)

   > [!NOTE]
   > Amikor egy SQL Server virtuális gép a portálon, lehetősége van a tárolási konfiguráció szerkesztésére. A konfigurációtól függően az Azure egy vagy több lemezt konfigurál. Több lemez egyetlen tárolókészletbe van kombinálva csíkozással. Az adatok és a naplófájlok is együtt vannak ebben a konfigurációban. További információt az SQL Server virtuális gépek tárolási konfigurációja című [témakörben talál.](virtual-machines-windows-sql-server-storage-configuration.md)

* **Lemezcsíkozás:** A nagyobb átviteli kapacitás érdekében további adatlemezeket adhat hozzá, és használhatja a Lemezcsíkozást. Az adatlemezek számának meghatározásához elemeznie kell a naplófájl(ok)hoz, valamint az adatokhoz és a TempDB-fájl(ok)hoz szükséges IOPS-számot és sávszélességet. Figyelje meg, hogy a különböző virtuálisgép-méretek különböző korlátokkal rendelkeznek az IOP-ok és a támogatott sávszélesség számára vonatkozóan, tekintse meg az IOPS [virtuálisgép-méretenkénti tábláit.](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Használja az alábbi irányelveket:

  * Windows 8/Windows Server 2012 vagy újabb rendszer esetén használja a [Tárolóhelyeket](https://technet.microsoft.com/library/hh831739.aspx) az alábbi irányelvek szerint:

      1. Állítsa az interleave (csík méret) 64 KB (65 536 bájt) az OLTP számítási feladatok és 256 KB (262144 bájt) adattárház számítási feladatok teljesítményhatás ának elkerülése érdekében a partíció kondiálása miatt. Ezt a PowerShell-nel kell beállítani.
      2. Oszlopszám beállítása = fizikai lemezek száma. A PowerShell használata 8-nál több lemez (nem a Kiszolgálókezelő felhasználói felülete) konfigurálásakor. 

    Például a következő PowerShell létrehoz egy új tárolókészletet a 64 KB-os átkapcsolási mérettel és az oszlopok számával 2-ig:

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Windows 2008 R2 vagy korábbi rendszerben dinamikus lemezek (operációs rendszercsíkos kötetek) használhatók, és a csík méret mindig 64 KB. Ez a beállítás a Windows 8/Windows Server 2012 rendszerhez megegyezik. További információt a Virtual Disk Service támogatási nyilatkozata [a Windows Storage Management API-ra való áttéréssorán talál.](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx)

  * Ha a [Közvetlen tárolóhelyeket (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) használja [az SQL Server feladatátvevő fürtpéldányaival,](virtual-machines-windows-portal-sql-create-failover-cluster.md)egyetlen készletet kell konfigurálnia. Bár az adott készleten különböző kötetek hozhatók létre, mindegyik ugyanazokat a jellemzőket fogja megosztani, például ugyanazt a gyorsítótárazási házirendet.

  * Határozza meg a tárolókészlethez társított lemezek számát a terhelési elvárások alapján. Ne feledje, hogy a különböző virtuálisgép-méretek különböző számú csatolt adatlemezek lehetővé teszik. További információ: [Méretek a virtuális gépekhez.](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

  * Ha nem prémium szintű SSD-ket (fejlesztési és tesztelési forgatókönyveket) használ, a javaslat az, hogy adja hozzá a [virtuális gép mérete](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) által támogatott adatlemezek maximális számát, és használja a lemezcsíkozást.

* **Gyorsítótárazási házirend:** Vegye figyelembe az alábbi javaslatokat a gyorsítótárazási házirendhez a tárolási konfigurációtól függően.

  * Ha külön lemezeket használ az adatokhoz és a naplófájlokhoz, engedélyezze az olvasási gyorsítótárazást az adatfájlokat és a TempDB adatfájlokat tároló adatlemezeken. Ez jelentős teljesítményelőnyt eredményezhet. Ne engedélyezze a gyorsítótárazást a naplófájlt tároló lemezen, mert ez a teljesítmény kisebb csökkenését okozza.

  * Ha egyetlen tárolókészletben használja a lemezcsíkozást, a legtöbb számítási feladat számára előnyös lesz az olvasási gyorsítótárazás. Ha külön tárolókészleteket tartalmaz a napló- és adatfájlokhoz, engedélyezze az olvasási gyorsítótárazást csak az adatfájlok tárolókészletében. Bizonyos nagy írási számítási feladatok esetén jobb teljesítmény érhető el gyorsítótárazás nélkül. Ezt csak teszteléssel lehet meghatározni.

  * A korábbi ajánlások a prémium SSD-kre vonatkoznak. Ha nem prémium szintű SSD-ket használ, ne engedélyezze a gyorsítótárazást semmilyen adatlemezen.

  * A lemezgyorsítótárazás konfigurálásával kapcsolatos tudnivalókat az alábbi cikkekben találja. A klasszikus (ASM) telepítési modellről a [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) és [a Set-AzureDataDisk című témakörben talál.](https://msdn.microsoft.com/library/azure/jj152851.aspx) Az Azure Resource Manager telepítési modelljéről a [Set-AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) és [a Set-AzVMDataDisk című témakörben talál.](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk)

     > [!WARNING]
     > Állítsa le az SQL Server szolgáltatás, ha módosítja a gyorsítótár-beállítás az Azure vm lemezek, hogy elkerüljék az adatbázis sérülésének lehetőségét.

* **NTFS-foglalási egység mérete**: Az adatlemez formázásakor ajánlott 64 kb-os foglalási egységméretet használni az adatokhoz és a naplófájlokhoz, valamint a TempDB-t. Ha a TempDB az ideiglenes lemezre kerül (D:\ meghajtó) a meghajtó kihasználásával szerzett teljesítmény meghaladja a 64K-s kiosztási egység méretének szükségességét. 

* **Lemezkezeléssel kapcsolatos gyakorlati tanácsok:** Az adatlemez eltávolításakor vagy a gyorsítótár típusának módosításakor állítsa le az SQL Server szolgáltatást a módosítás során. Amikor a gyorsítótárazási beállítások módosulnak az operációs rendszer lemezén, az Azure leállítja a virtuális gépet, módosítja a gyorsítótár típusát, és újraindítja a virtuális gépet. Az adatlemez gyorsítótár-beállításainak módosításakor a virtuális gép nem áll le, de az adatlemez leválik a virtuális gépről a módosítás során, majd újra csatolja.

  > [!WARNING]
  > Ha nem sikerül leállítani az SQL Server szolgáltatást a műveletek során, az adatbázis sérülését okozhatja.


## <a name="io-guidance"></a>I/O-útmutató

* A legjobb eredményeket a prémium szintű SSD-k akkor érik el, ha párhuzamosítja az alkalmazást és a kérelmeket. A prémium szintű SSD-k olyan esetekhez készültek, ahol az IO-várólista mélysége nagyobb, mint 1, így az egyszálas soros kérelmek esetében alig vagy egyáltalán nem fog teljesítménynövekedést látni (még akkor is, ha azok nagy tárolóigényesek). Ez hatással lehet például a teljesítményelemző eszközök, például az SQLIO egyszálas teszteredményeire.

* Fontolja meg [az adatbázislap-tömörítés](https://msdn.microsoft.com/library/cc280449.aspx) használatát, mivel ez segíthet az I/O-igényes munkaterhelések teljesítményének javításában. Az adattömörítés azonban növelheti az adatbázis-kiszolgáló processzorfelhasználását.

* Fontolja meg az azonnali fájlinicializálás engedélyezését a fájl kezdeti lefoglalásához szükséges idő csökkentése érdekében. Az azonnali fájlinicializálás előnyeinek kihasználásához adja meg az SQL Server (MSSQLSERVER) szolgáltatásfiókot SE_MANAGE_VOLUME_NAME, és adja hozzá a **Kötetkarbantartási feladatok végrehajtása** biztonsági házirendhez. Ha SQL Server platformlemezképet használ az Azure-hoz, az alapértelmezett szolgáltatásfiók (NT Service\MSSQLSERVER) nem kerül be a **Kötet-karbantartási feladatok biztonsági** házirendbe. Más szóval az azonnali fájlinicializálás nincs engedélyezve az SQL Server Azure platformlemezkép. Miután hozzáadja az SQL Server szolgáltatásfiókot a **Kötetkarbantartási feladatok végrehajtása** biztonsági házirendhez, indítsa újra az SQL Server szolgáltatást. A szolgáltatás használata biztonsági szempontokat is figyelembe vehet. További információt az [Adatbázisfájl inicializálása](https://msdn.microsoft.com/library/ms175935.aspx)című témakörben talál.

* **autogrow** tekinthető csupán egy készenléti váratlan növekedés. Ne kezelje az adatokat, és jelentkezzen be a növekedés napi rendszerességgel autogrow. Ha automatikus növekményt használ, a Méret kapcsolóval előre növelje a fájlt.

* Győződjön meg arról, hogy az **automatikus zsugorítás** le van tiltva, hogy elkerülje a szükségtelen többletterhelést, amely negatívan befolyásolhatja a teljesítményt.

* Az összes adatbázis áthelyezése adatlemezekre, beleértve a rendszeradatbázisokat is. További információt a [Rendszeradatbázisok áthelyezése című témakörben talál.](https://msdn.microsoft.com/library/ms345408.aspx)

* SQL Server hibanapló és fájlkönyvtárak adatlemezekre való áthelyezése. Ezt az SQL Server Configuration Manager programban teheti meg, ha a jobb gombbal az SQL Server-példányra kattint, és kiválasztja a tulajdonságokat. A hibanapló és a nyomkövetési fájl beállításai az Indítási paraméterek lapon **módosíthatók.** A memóriaképi könyvtár a **Speciális** lapon van megadva. A következő képernyőkép bemutatja, hogy hol kell keresni a hibanapló indítási paraméterét.

    ![SQL ErrorLog képernyőkép](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* A biztonsági másolat és az adatbázisfájl alapértelmezett helyeinek beállítása. Használja a cikkben található javaslatokat, és hajtsa végre a módosításokat a Kiszolgáló tulajdonságai ablakban. További információt [az Adatok és naplófájlok alapértelmezett helyeinek megtekintése és módosítása (SQL Server Management Studio) című témakörben talál.](https://msdn.microsoft.com/library/dd206993.aspx) A következő képernyőkép bemutatja, hogy hol hajtsa végre ezeket a módosításokat.

    ![SQL-adatnapló- és biztonságimásolat-fájlok](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* Zárolt lapok engedélyezése az Io és a lapozási tevékenységek csökkentése érdekében. További információt [a Lapok zárolása a memóriában beállítás (Windows) című témakörben talál.](https://msdn.microsoft.com/library/ms190730.aspx)

* Ha az SQL Server 2012-t futtatja, telepítse a Service Pack 1 cumulatives update 10 alkalmazást. Ez a frissítés tartalmazza a javítást a gyenge teljesítmény az I/O végrehajtásakor válassza ki az ideiglenes tábla utasítást az SQL Server 2012. További információt ebben a [tudásbáziscikkben](https://support.microsoft.com/kb/2958012)talál.

* Fontolja meg az adatfájlok tömörítését az Azure-ból való be- és kiszállításkor.

## <a name="feature-specific-guidance"></a>Szolgáltatásspecifikus útmutatás

Egyes központi telepítések további teljesítményelőnyöket érhetnek el a fejlettebb konfigurációs technikák használatával. Az alábbi lista az SQL Server néhány olyan szolgáltatását ismerteti, amelyek segíthetnek a jobb teljesítmény elérésében:

### <a name="back-up-to-azure-storage"></a>Biztonsági másolatot az Azure Storage-ról
Ha az Azure virtuális gépeken futó SQL Server biztonsági másolatokat hajt végre, az SQL Server biztonsági másolat használatával [URL-címre](https://msdn.microsoft.com/library/dn435916.aspx)használható. Ez a szolgáltatás az SQL Server 2012 SP1 CU2 szervizcsomaggal kezdve érhető el, és a csatolt adatlemezekre való biztonsági mentéshez ajánlott. Amikor biztonsági mentést/visszaállítást hajt végre az Azure Storage szolgáltatásba/onnan, kövesse az SQL Server biztonsági másolatában található javaslatokat az [URL-lel kapcsolatos gyakorlati tanácsokhoz, valamint az Azure Storage-ban tárolt biztonsági másolatok ból történő hibaelhárításhoz és -visszaállításhoz.](https://msdn.microsoft.com/library/jj919149.aspx) Ezeket a biztonsági mentéseket [automatizálhatja az Azure virtuális gépeken az SQL Server automatikus biztonsági mentésével](virtual-machines-windows-sql-automated-backup.md)is.

Az SQL Server 2012 előtt használhatja az [SQL Server biztonsági mentést az Azure Tool eszközhöz.](https://www.microsoft.com/download/details.aspx?id=40740) Ez az eszköz több biztonságimásolat-csíktároló használatával növelheti a biztonsági mentési átviteli áteresztőát.

### <a name="sql-server-data-files-in-azure"></a>SQL Server adatfájlok az Azure-ban

Ez az új funkció, az SQL Server Data Files az [Azure-ban](https://msdn.microsoft.com/library/dn385720.aspx)az SQL Server 2014-től kezdve érhető el. Az SQL Server azure-beli adatfájlokkal való futtatása az Azure-adatlemezekhez hasonló teljesítményjellemzőket mutat be.

### <a name="failover-cluster-instance-and-storage-spaces"></a>Feladatátvevő fürtpéldány és tárolóhelyek

Ha tárolóhelyeket használ, amikor csomópontokat ad hozzá a fürthöz a **Megerősítés** lapon, törölje a jelet az **Összes jogosult tároló hozzáadása a fürthöz jelölőnégyzetből.** 

![A jogosult tárhely bejelölése](media/virtual-machines-windows-sql-performance/uncheck-eligible-cluster-storage.png)

Ha tárolóhelyeket használ, és nem törölje a jelet **az Összes jogosult tároló hozzáadása a fürthöz**jelölőnégyzetből, a Windows leválasztja a virtuális lemezeket a fürtözési folyamat során. Ennek eredményeképpen nem jelennek meg a Lemezkezelőben vagy az Intézőben, amíg a tárolóhelyeket el nem távolítja a fürtből, és a PowerShell használatával újra nem csatolták. A Tárolóhelyek több lemezt csoportosít a tárolókészletekbe. További információ: [Tárolóhelyek](/windows-server/storage/storage-spaces/overview).

## <a name="next-steps"></a>További lépések

A tárolásról és a teljesítményről további információt az [SQL Server tárolási konfigurációs irányelvei az Azure VM-ben című](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/) témakörben talál.

A biztonsági gyakorlati tanácsokról az Azure virtuális gépek SQL Server szolgáltatásának biztonsági szempontjai című [témakörben található.](virtual-machines-windows-sql-security.md)

Tekintse át az SQL Server virtuális gép egyéb cikkeit [az SQL Server ben az Azure virtuális gépeken – áttekintés .](virtual-machines-windows-sql-server-iaas-overview.md) Ha kérdése van az SQL Servert futtató virtuális gépek használatával kapcsolatban, tekintse meg a [gyakori kérdéseket](virtual-machines-windows-sql-server-iaas-faq.md).
