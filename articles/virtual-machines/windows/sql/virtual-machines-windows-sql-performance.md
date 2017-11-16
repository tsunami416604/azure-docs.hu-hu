---
title: "Az Azure SQL Server ajánlott eljárásai teljesítmény |} Microsoft Docs"
description: "Gyakorlati tanácsokat megfelelően a Microsoft Azure virtuális gépeken futó SQL Server teljesítményének optimalizálásához."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2017
ms.author: jroth
ms.openlocfilehash: 6386678bdac3630f3e003187ff3d12c0ce053b90
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="performance-best-practices-for-sql-server-in-azure-virtual-machines"></a>Ajánlott eljárások az SQL Server teljesítményének Azure Virtual Machines szolgáltatásbeli növeléséhez

## <a name="overview"></a>Áttekintés

Ez a témakör gyakorlati tanácsokat megfelelően a Microsoft Azure virtuális gép az SQL Server teljesítményének optimalizálásához. SQL Server rendszert futtató Azure virtuális gépeken, miközben azt javasoljuk, hogy folytatja a helyszíni környezetben SQL Server megfelelő lehetőségeket teljesítményhangolás ugyanazon adatbázis használatával. A nyilvános felhőben egy relációs adatbázis teljesítménye azonban, például egy virtuális gép méretét, és az adatlemezek konfigurációjának számos tényezőtől függ.

SQL Server-lemezképek létrehozásakor [fontolja meg a virtuális gépek Azure-portálon kiépítés](virtual-machines-windows-portal-sql-server-provision.md). SQL Server virtuális gépen a portálon a Resource Manager üzembe valósítja meg az alábbi gyakorlati tanácsok, beleértve a tárolási konfiguráció.

Ez a cikk témáját arra irányul, hogy a *legjobb* az Azure virtuális gépeken futó SQL Server teljesítményét. Ha a terhelést szabadulnak, minden alábbi optimalizálási nem lehet szükség. Vegye figyelembe a teljesítményigény és a terhelési mintázatok, ezek a javaslatok értékeli.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="quick-check-list"></a>Gyors ellenőrzőlista tételeit

A következő az optimális teljesítmény érdekében az SQL Server Azure virtuális gépeken Gyorsellenőrzés listáját:

| Terület | Optimalizálás. |
| --- | --- |
| [Virtuálisgép-mérettel](#vm-size-guidance) |[DS3](../../virtual-machines-windows-sizes-memory.md) vagy újabb SQL Enterprise Edition.<br/><br/>[DS2](../../virtual-machines-windows-sizes-memory.md) vagy újabb SQL Standard és Web kiadások. |
| [Tárolás](#storage-guidance) |Használjon [prémium szintű Storage](../premium-storage.md). Standard szintű tárolót csak fejlesztési és tesztelési célú ajánlott.<br/><br/>Tartsa a [tárfiók](../../../storage/common/storage-create-storage-account.md) és az SQL Server virtuális gép ugyanabban a régióban.<br/><br/>Tiltsa le az Azure [georedundáns tárolás](../../../storage/common/storage-redundancy.md) (georeplikáció) a tárfiók. |
| [Lemezek](#disks-guidance) |Legalább 2 használja [P30 lemezek](../premium-storage.md#scalability-and-performance-targets) (1. a naplófájlok; 1. az adatfájlok és a TempDB).<br/><br/>Ne használja az operációs rendszer vagy ideiglenes lemezek adatbázistár vagy naplózás.<br/><br/>Enable olvassa el a lemez(ek) az adatfájlok és a TempDB üzemeltető gyorsítótárazás.<br/><br/>Ne engedélyezze a naplófájl üzemeltető lemez(ek) gyorsítótárazás.<br/><br/>Fontos: Az SQL Server szolgáltatás leállítása egy Azure virtuális lemezt a gyorsítótár beállításainak módosításakor.<br/><br/>Paritásos több Azure adatlemezek nagyobb IO átviteli sebesség eléréséhez.<br/><br/>Formázza a dokumentált lemezfoglalás méretét. |
| [I/O](#io-guidance) |Adatbázis lap tömörítésének engedélyezéséhez.<br/><br/>Az adatfájlok azonnali fájlinicializálása engedélyezése.<br/><br/>Korlátozható, vagy tiltsa le az adatbázis automatikus növekedésre.<br/><br/>Tiltsa le az adatbázis autoshrink.<br/><br/>Összes adatbázis áthelyezése adatlemezek, beleértve a rendszer-adatbázisokat.<br/><br/>Helyezze át az SQL Server hiba naplózásához és követéséhez könyvtárak adatlemezek.<br/><br/>A telepítő biztonsági másolat és az adatbázis alapértelmezett tárolási helyeit.<br/><br/>Zárolt lapok engedélyezése.<br/><br/>SQL Server teljesítményét javítások alkalmazása. |
| [Funkcióspecifikus](#feature-specific-guidance) |Készítsen biztonsági másolatot a közvetlenül a blob storage. |

További információ a *hogyan* és *miért* ahhoz, hogy ezek az optimalizálások, tekintse át a részletek és a következő szakaszokban található útmutatást.

## <a name="vm-size-guidance"></a>Virtuális gép mérete útmutató

Bizalmas alkalmazások teljesítménye, javasoljuk, hogy használja-e a következő [virtuális gépek méretét](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json):

* **SQL Server Enterprise Edition**: DS3 vagy újabb
* **SQL Server Standard és Web kiadások**: DS2 vagy újabb

## <a name="storage-guidance"></a>Tárolási útmutatásokkal

DS sorozatnak (valamint sorozatú DSv2 és GS sorozatnak) virtuális gépek támogatási [prémium szintű Storage](../premium-storage.md). Prémium szintű Storage ajánlott minden termelési számítási feladatokhoz.

> [!WARNING]
> Standard szintű tárolást különböző késések és sávszélesség rendelkezik, és csak ajánlott fejlesztési/tesztelési feladatokat. Termelési számítási feladatokhoz a prémium szintű Storage kell használnia.

Emellett ajánlott ugyanabba az adatközpontba átviteli késések csökkentése érdekében az SQL Server virtuális gépként az Azure storage-fiók létrehozása. A storage-fiók létrehozásakor tiltsa le a georeplikáció, több lemezre konzisztens írási sorrendje nem garantált. Ehelyett fontolja meg, hogy egy SQL Server katasztrófa utáni helyreállítás technológia két Azure-adatközpont között. További információkért lásd: [magas rendelkezésre álláshoz és Vészhelyreállításhoz az SQL Server Azure virtuális gépek](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Lemezek útmutató

Egy Azure virtuális gépen három fő lemez típusa van:

* **Az operációsrendszer-lemez**: az Azure virtuális gép létrehozásakor a platform kapcsolódni fog-e legalább egy lemez (címkézve a **C** meghajtó) az az operációsrendszer-lemez a virtuális géphez. Ez a lemez tárolási oldalakra vonatkozó blob tárolt virtuális merevlemez.
* **Ideiglenes lemez**: Azure virtuális gépek tartalmazza az ideiglenes lemez nevű másik lemezre (címkézve a **D**: meghajtó). Ez az egy lemezt a csomóponton, az ideiglenes területet használható.
* **Az adatlemezek**: is csatolhat lemezt a virtuális gép adatok lemezként, és ezek fogja tárolni Storage lapblobokat.

A következő szakaszok ismertetik a különböző lemezek használatához.

### <a name="operating-system-disk"></a>Operációsrendszer-lemez

Operációsrendszer-lemez a virtuális Merevlemezt, amely a rendszerindító és a csatlakoztatási az operációs rendszer fut verzióként és van-e címkézve **C** meghajtó.

Alapértelmezés szerint a gyorsítótárazási házirend, az operációs rendszer lemezen lévő **olvasási/írási**. Bizalmas alkalmazások teljesítménye javasoljuk, adatlemezek használata helyett az operációs rendszer tárolására. Lásd az alábbi adatlemezek.

### <a name="temporary-disk"></a>Ideiglenes lemez

Az ideiglenes tárolási meghajtó címkézve a **D**: meghajtó, nem őrzi meg az Azure blob storage. Ne tárolja a felhasználói adatbázis-fájlokat vagy a felhasználó tranzakciós naplófájlok a **D**: meghajtó.

D sorozatú, Dv2-sorozat és G sorozatú virtuális gépeket a virtuális gépeken az ideiglenes meghajtón, SSD-alapú. Ha a terhelést a TempDB nehéz használatát (pl. az ideiglenes objektumok vagy bonyolult illesztésekre), a TempDB tárolja a **D** meghajtó nem sikerült újabb TempDB átviteli sebességet eredményez, és a TempDB késés csökkentése.

Virtuális gépekhez, amely támogatja a prémium szintű Storage (DS-méretek, DSv2-sorozat és GS sorozatnak) azt javasoljuk, amely támogatja a prémium szintű Storage engedélyezve olvasási gyorsítótárazás lemezre TempDB tárolására. Nincs az egyetlen kivétel ez a javaslat; Ha a TempDB használata írási igényű, nagyobb teljesítményt érhet el, a TempDB tárolja a helyi **D** meghajtót, amely egyben a ezek méreteket SSD-alapú.

### <a name="data-disks"></a>Az adatlemezek

* **Adatlemezek használja az adatok és a naplófájlok**: legalább 2 prémium szintű Storage használata [P30 lemezek](../premium-storage.md#scalability-and-performance-targets) ahol egy lemezt a naplófájl (oka) t tartalmaz, és a másik az adatokat és a TempDB adatbázis (oka) t tartalmaz. Minden prémium szintű tároló lemez számos iops-érték és a sávszélesség (MB/s) attól függően, hogy annak méretét, a következő cikkben leírtak szerint: [prémium szintű Storage használatával lemezek](../premium-storage.md).

* **Lemez csíkozást**: további átviteli sebesség eléréséhez, adhat hozzá további adatlemezt és lemez csíkozást használja. Az adatlemezek számának megállapításához kell elemeznie iops-érték és a sávszélességre van szüksége a naplófájl (oka) t, és az adatok és a TempDB adatbázis (oka) t. Figyelje meg, hogy más Virtuálisgép-méretek iops-érték és a támogatott sávszélesség számára különböző korlátokkal rendelkeznek, tekintse meg a táblák iops-értéket a [Virtuálisgép-méretet](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Kövesse az alábbi iránymutatásokat:

  * A Windows 8 és Windows Server 2012 vagy újabb, használjon [tárolóhelyek](https://technet.microsoft.com/library/hh831739.aspx) az alábbi útmutatókat:

      1. Állítsa be a szektorszórás (paritásos méret) 64 KB-os (65 536 bájt) OLTP-munkaterhelések és 256 KB (262 144 bájt) adatok vámraktározási munkaterhelések partíció hibás illesztés hibákat miatt a teljesítményre gyakorolt hatás elkerülése érdekében. Ez a PowerShell használatával kell beállítani.
      1. Állítsa be az oszlopok száma = fizikai lemezek számát. A PowerShell szolgáltatás több mint 8 lemez (nem a kiszolgáló-kezelő felhasználói felületén) konfigurálásakor. 

    Például a következő PowerShell a szektorszórás méretű 64 KB és 2 oszlopainak számával egy új tárolókészletet hoz létre:

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Windows 2008 R2 vagy korábbi használhatja a dinamikus lemezek (az operációs rendszer csíkozott kötetek) és a paritásos mérete mindig 64 KB. Vegye figyelembe, hogy ez a beállítás Windows 8 és Windows Server 2012-től elavult. Információkért lásd: a következő támogatási nyilatkozattal [Virtuálislemez-szolgáltatás a Windows tárolókezelési API tér át](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

  * Ha a számítási feladatok nem intenzív naplót, és nem kell külön IOPs, beállíthatja a csak egy tárolókészletet. Máskülönben hozzon létre két tárolókészletek, egyet a naplófájl (oka) t, és egy másik tárolókészlettel adatfájllal és a TempDB. Határozza meg a társított minden egyes betöltés elvárásainak alapuló tárolókészlet lemezek számát. Ne feledje, hogy más Virtuálisgép-méretek lehetővé teszik a különböző számú adatlemezt csatolni. További információkért lásd: [virtuális gépek méretei](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Használatakor nem prémium szintű Storage (fejlesztési és tesztelési célú forgatókönyv), az ajánlás az adatlemezek által támogatott maximális számának hozzáadása, a [Virtuálisgép-méretet](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) és lemez csíkozást használja.

* **Gyorsítótárazási házirend**: adatlemezek a prémium szintű Storage, engedélyezze az adatfájlok és a TempDB csak üzemeltető adatlemezek olvasási gyorsítótárazás. Használatakor nem prémium szintű Storage, nem engedélyezi a gyorsítótárazást az adatok lemezzel. Lemez gyorsítótárazásának konfigurálása, lásd: a következő témaköröket. A klasszikus (ASM) üzembe helyezési modellel lásd: [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) és [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx). Az Azure Resource Manager telepítési modell lásd: [Set-AzureRMOSDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk?view=azurermps-4.4.1) és [Set-AzureRMVMDataDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmdatadisk?view=azurermps-4.4.1).

  > [!WARNING]
  > Állítsa le az SQL Server szolgáltatás, amikor a lehetőségét, amely bármilyen adatbázis-sérülés elkerülése érdekében az Azure virtuális gépek lemezei gyorsítótár beállításainak megváltoztatása.

* **NTFS foglalásiegység-méret**: az adatok lemez formázása esetén ajánlott használni, amikor egy 64 KB-os foglalásiegység-méret adatok és a naplófájlok, valamint a TempDB adatbázisban.

* **Gyakorlati tanácsok a jelszókezeléshez lemez**: Ha adatlemezt vagy nem módosíthatja a gyorsítótár típusa, állítsa le az SQL Server szolgáltatás módosítása során. Megváltozásakor a gyorsítótárazási beállítások vannak az operációsrendszer-lemezképet, a Azure leállítja a virtuális gép, megváltozik a gyorsítótár típusa és a virtuális gép újraindul. Adatlemez gyorsítótár beállításainak módosításakor a virtuális gép nem állt le, de a adatok lemez leválasztása a virtuális gépről a módosítás során, majd objektumkörnyezetben.

  > [!WARNING]
  > Ezek a műveletek során az SQL Server szolgáltatás leállítása sikertelen sérülésével járhat.

## <a name="io-guidance"></a>I/o-útmutató

* Prémium szintű Storage a legjobb eredmények érhetők el, ha az alkalmazás és a kérelmek parallelize. Prémium szintű Storage a forgatókönyvekben, ahol a IO várólistamélység nagyobb, mint 1, így látni fogja a soros kérelmek egyszálas kevéssé vagy egyáltalán ne teljesítménynövekedéshez (még akkor is, ha azok a tárolási intenzív) szolgál. Ide tartozhat például teljesítmény elemzésére szolgáló eszközöket, például SQLIO egyszálas vizsgálat eredményeit.

* Érdemes lehet [adatbázis-oldal tömörítési](https://msdn.microsoft.com/library/cc280449.aspx) , akkor javíthatja intenzív i/o-munkaterhelések teljesítményét. Az adatok tömörítésének azonban növelheti a CPU-használat a kiszolgálón.

* Érdemes megfontolni a azonnali fájlinicializálása a kezdeti fájl kiosztható szükséges idő csökkentése érdekében. Azonnali fájlinicializálása előnyeit, adja meg az SQL Server (MSSQLSERVER) szolgáltatás fiók SE_MANAGE_VOLUME_NAME, és hozzá kell adnia a **kötet karbantartási feladatok elvégzése** biztonsági házirend. Ha egy SQL Server platformlemezképet használ az Azure-ba, az alapértelmezett szolgáltatásfiók (NT Service\MSSQLSERVER) nem adódik hozzá a **kötet karbantartási feladatok elvégzése** biztonsági házirend. Ez azt jelenti azonnali fájlinicializálása nincs engedélyezve az SQL Server Azure platform lemezképek. Az SQL Server szolgáltatás fiók hozzáadása után a **kötet karbantartási feladatok elvégzése** biztonsági házirend, indítsa újra az SQL Server szolgáltatást. Ez a szolgáltatás használatára vonatkozó biztonsági szempontokat is lehet. További információkért lásd: [adatbázis Fájlinicializálása](https://msdn.microsoft.com/library/ms175935.aspx).

* **automatikus növekedésre** számít, csupán a váratlan növekedésre eseményre. Az automatikus növekedésre folyamatosan a adatainak és naplókönyvtárainak növekedési nem kezeli. Automatikus növekedésre használatos, ha előre mérete kapcsoló használata a fájl megcélzott.

* Győződjön meg arról, hogy **autoshrink** negatívan befolyásolhatja a teljesítményt szükségtelen terhet elkerülése érdekében le van tiltva.

* Összes adatbázis áthelyezése adatlemezek, beleértve a rendszer-adatbázisokat. További információkért lásd: [rendszer adatbázisok áthelyezése](https://msdn.microsoft.com/library/ms345408.aspx).

* Helyezze át az SQL Server hiba naplózásához és követéséhez könyvtárak adatlemezek. Ezt megteheti az SQL Server Configuration Manager kattintson a jobb gombbal az SQL Server-példányt, majd válassza a tulajdonságok. A hiba naplózásához és követéséhez fájlbeállításainak módosítható a **indítási paraméterek** fülre. A memóriakép könyvtára van megadva a **speciális** fülre. Az alábbi képernyőfelvételen látható, ha a hiba a napló indítási paraméter keresnie.

    ![Az SQL hibanaplóban képernyőképe](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* A telepítő biztonsági másolat és az adatbázis alapértelmezett tárolási helyeit. Ebben a témakörben a javaslatokat használja, és végezze el a módosításokat a kiszolgáló tulajdonságai ablakban. Útmutatásért lásd: [megtekinteni vagy módosítani az alapértelmezett helyek az adatok és naplófájlok (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Az alábbi képernyőfelvételen a változtatások where mutatja be.

    ![SQL Data napló-és biztonsági mentése](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* Engedélyezze a zárolt lapok IO és lapozás tevékenységeket csökkentése érdekében. További információkért lásd: [engedélyezése a zárolási lapok memória beállítás (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

* Ha SQL Server 2012 rendszer használata esetén telepítse a Service Pack 1 összegző frissítés 10. A frissítés tartalmazza a javítás i/o gyenge teljesítményt, válassza ki azokat az SQL Server 2012 ideiglenes table utasítás végrehajtása esetén. További információ: Ez [Tudásbázis](http://support.microsoft.com/kb/2958012).

* Vegye figyelembe az adatfájlokról tömörítés vagy kikapcsolása átviteléhez az Azure.

## <a name="feature-specific-guidance"></a>A szolgáltatás konkrét útmutatást

Egyes központi telepítések további teljesítménybeli előnyökben speciális konfigurációs technikák segítségével elérése érdekében. Az alábbi listában néhány SQL Server szolgáltatásokat tartalmaz, amely segítségével a jobb teljesítmény érdekében információk találhatók:

* **Az Azure storage történő biztonsági mentés**: az Azure virtuális gépeken futó SQL Server biztonsági mentés végrehajtásához, használhatja [SQL Server biztonsági másolat URL-címre](https://msdn.microsoft.com/library/dn435916.aspx). Ez a szolgáltatás lett bevezetve az SQL Server 2012 SP1 CU2 és ajánlott a biztonsági mentés a mellékelt adatok lemezeket. Ha Ön biztonsági mentés/visszaállítás az Azure-tárolót, kövesse a megadott [SQL Server biztonsági másolat URL-cím ajánlott eljárások és hibaelhárítás és visszaállítása az Azure Storage-ban tárolt biztonsági másolatok](https://msdn.microsoft.com/library/jj919149.aspx). Ezek a biztonsági mentéseket is automatizálhatja [automatikus biztonsági mentés az SQL Server Azure virtuális gépek](virtual-machines-windows-sql-automated-backup.md).

    SQL Server 2012 megjelenése előtt használható [SQL Server biztonsági másolat segítségével Azure eszköz](https://www.microsoft.com/download/details.aspx?id=40740). Ez az eszköz segítségével több biztonsági mentési paritásos cél használó biztonsági mentési átviteli sebesség növelése.

* **SQL Server-adatfájlok az Azure-ban**: ezen új szolgáltatás [SQL Server-adatfájlok az Azure-ban](https://msdn.microsoft.com/library/dn385720.aspx), az SQL Server 2014 kezdve érhető el. Hasonló teljesítményt nyújt, mint az Azure data lemezek használata az Azure-adatfájlok SQL Servert futtató mutatja be.

## <a name="next-steps"></a>Következő lépések

Ajánlott biztonsági eljárások, lásd: [biztonsági szempontok az SQL Server Azure virtuális gépek](virtual-machines-windows-sql-security.md).

Tekintse át a többi SQL Server virtuális gép témakörei [SQL Server Azure virtuális gépek – áttekintés](virtual-machines-windows-sql-server-iaas-overview.md).
