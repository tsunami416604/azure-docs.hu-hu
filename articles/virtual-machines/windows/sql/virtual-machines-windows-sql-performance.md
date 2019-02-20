---
title: Teljesítmény-irányelvek az SQL Server az Azure-ban |} A Microsoft Docs
description: Útmutatást nyújt a Microsoft Azure virtuális gépeken futó SQL Server teljesítményének optimalizálása.
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
ms.date: 09/26/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: bb9b90ca239ff03f44b76a7ee5754eb7872caa31
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415901"
---
# <a name="performance-guidelines-for-sql-server-in-azure-virtual-machines"></a>Teljesítmény-irányelvek az SQL Server Azure Virtual Machines szolgáltatásban

## <a name="overview"></a>Áttekintés

Ez a cikk nyújt útmutatást a Microsoft Azure virtuális gép az SQL Server teljesítményének optimalizálásához. Az Azure Virtual Machines szolgáltatásban futó SQL Server, miközben azt javasoljuk, hogy folytatja az azonos adatbázis teljesítmény-finomhangolási a helyszíni környezetben, az SQL Server megfelelő lehetőségeket használatával. Azonban a nyilvános felhőben egy relációs adatbázis teljesítménye számos tényezőtől, például a virtuális gép méretét és az adatlemezek konfigurációja függ.

[SQL Server-rendszerképeket az Azure Portalon üzembe helyezett](quickstart-sql-vm-create-portal.md) kövesse az általános tárolási konfigurálási eljárásoknak megfelelő beállításában (hogyan tároló úgy van konfigurálva a további információkért lásd: [tárolási konfigurációt az SQL Server virtuális gépek](virtual-machines-windows-sql-server-storage-configuration.md)). Kiépítés után célszerű megfontolni más optimalizálási lehetőségek a cikkben leírtak szerint. A kiválasztott beállításokat alapja a számítási feladatok, és ellenőrizze a tesztelés útján.

> [!TIP]
> Általában a költségek optimalizálása és a teljesítmény optimalizálása között. Ez a cikk első összpontosít a *ajánlott* teljesítmény az SQL Server Azure virtuális gépeken. Ha a számítási feladatok nem szabadulnak, nem igénylik az alább felsorolt minden optimalizálás. Fontolja meg a sorozatot, a költségek és a számítási feladatok minták, ezekkel az ajánlásokkal kipróbálása.

## <a name="quick-check-list"></a>Gyors ellenőrzőlista

A következő lista egy gyors ellenőrzés az optimális teljesítmény érdekében az SQL Server Azure virtuális gépeken:

| Terület | Optimalizálási lehetőségek |
| --- | --- |
| [Virtuális gép mérete](#vm-size-guidance) | - [DS3_v2](../sizes-general.md) vagy újabb verziója az SQL Enterprise Edition.<br/><br/> - [DS2_v2](../sizes-general.md) vagy újabb SQL Standard és Web kiadások. |
| [Tárolás](#storage-guidance) | – Használja [prémium szintű SSD-k](../disks-types.md). Fejlesztési-tesztelési csak standard szintű storage ajánlott.<br/><br/> – Folyamatosan a [tárfiók](../../../storage/common/storage-create-storage-account.md) és az SQL Server rendszerű virtuális gép ugyanabban a régióban.<br/><br/> * Tiltsa le az Azure [georedundáns tárolás](../../../storage/common/storage-redundancy.md) (georeplikáció) a tárfiókban. |
| [Lemezek](#disks-guidance) | – Használja legalább 2 [P30 lemez](../disks-types.md#premium-ssd) (1. a naplófájlok és 1-adatfájlok többek között a TempDB). Körülbelül 50 000 iops-t igénylő számítási feladatokhoz érdemes lehet egy Ultra SSD. <br/><br/> -Elkerülése érdekében használja az operációs rendszer vagy az ideiglenes lemezek adatbázistár vagy a naplózás.<br/><br/> -Enable olvasási gyorsítótárat üzemeltető, az adatok és a TempDB-adatfájlok (eke) t a.<br/><br/> – Ne engedélyezze a gyorsítótárazás üzemeltetési a naplófájl (eke) t.  **Fontos**: Állítsa le az SQL Server szolgáltatást, ha egy Azure-beli Virtuálisgép-lemez gyorsítótár beállításainak módosítása.<br/><br/> -Stripe-több Azure-adatlemezek a jobb i/o-átviteli sebesség lekérdezése.<br/><br/> -Formátumban, dokumentált foglalási mérettel. <br/><br/> -A kritikus fontosságú az SQL Server számítási feladatokhoz (Miután kiválasztotta a megfelelő virtuális gép mérete) helyi SSD hely a TempDB. |
| [I/O](#io-guidance) |-Adatbázis oldal tömörítésének engedélyezéséhez.<br/><br/> -Adatfájlok azonnali fájl inicializálása engedélyezése.<br/><br/> – Az adatbázis ejtésével korlátozza.<br/><br/> – Tiltsa le az adatbázis többletterheléshez.<br/><br/> -Összes adatbázis áthelyezése az adatlemezeket, beleértve a rendszeradatbázisokban.<br/><br/> – Helyezze át az SQL Server hiba napló- és nyomkövetési fájl könyvtárak adatlemezeket.<br/><br/> -Telepítés az alapértelmezett biztonsági mentés és adatbázis-fájlok helye.<br/><br/> -Engedélyezése zárolt lapok vannak.<br/><br/> -Érvényes SQL Server teljesítményének javításait. |
| [A szolgáltatás-specifikus](#feature-specific-guidance) | – Biztonsági másolat készítése közvetlenül a blob storage-bA. |

További információ a *hogyan* és *miért* ahhoz, hogy ezek az optimalizációk, tekintse át a részletek és a következő szakaszok útmutatást.

## <a name="vm-size-guidance"></a>Útmutató a virtuális gép mérete

Érzékeny alkalmazások teljesítménye, javasoljuk, hogy használja-e a következő [virtuálisgép-méretekről](../sizes.md):

* **Az SQL Server Enterprise Edition**: DS3_v2 vagy újabb
* **Az SQL Server Standard és Web kiadások**: DS2_v2 vagy újabb

[DSv2 sorozat](../sizes-general.md#dsv2-series) virtuális gépeket támogatja a premium storage, amely a legjobb teljesítmény érdekében javasoljuk. A méretek ajánlott Íme az alapkonfigurációkat, de a tényleges mérete, válassza ki a számítási feladat igényeinek függ. DSv2 sorozatú virtuális gépek olyan általános célú virtuális gépek, amelyek jók a különböző számítási feladatok, míg más méretekről adott munkaterhelés-típusok vannak optimalizálva. Ha például a [M-sorozat](../sizes-memory.md#m-series) kínálja a legnagyobb vCPU-számot és a memória a legnagyobb SQL Server munkaterhelésekhez. A [GS-sorozat](../sizes-memory.md#gs-series) és [DSv2-sorozat 11-15](../sizes-memory.md#dsv2-series-11-15) nagy memóriakövetelményei vannak optimalizálva. E sorozat mindkét is rendelkezésre állnak a [core méretek korlátozott](../../windows/constrained-vcpu.md), pénzt menti számítási feladatokkal és alacsonyabb számítási igényekre. A [Ls-sorozat](../sizes-storage.md) gépek magas lemez-adatátviteli és i/o vannak optimalizálva. Fontos, fontolja meg az adott SQL Server számítási és a alkalmazni egy Virtuálisgép-sorozatok és a méret a választott.

## <a name="storage-guidance"></a>Storage útmutatója

(Valamint a dsv2 és GS-sorozat) DS sorozatú virtuális gépek által támogatott [prémium szintű SSD-k](../disks-types.md). Prémium szintű SSD-k használata akkor javasolt az összes éles számítási feladatokhoz.

> [!WARNING]
> Standard HDD és SSD-k különböző késéseket és a sávszélesség, és csak fejlesztési és tesztelési számítási feladatok esetében ajánlott. Éles számítási feladatokhoz a prémium szintű SSD-k kell használnia.

Emellett javasoljuk, hogy az Azure storage-fiók létrehozása az SQL Server virtuális gépek átviteli késések csökkentése érdekében az ugyanabban az adatközpontban. Storage-fiók létrehozásakor, tiltsa le a georeplikáció, több lemezre kiterjedő konzisztens írási sorrend nem garantált. Ehelyett érdemes konfigurálása egy SQL Server katasztrófa utáni helyreállítás technológia két Azure-beli adatközpontok között. További információkért lásd: [magas rendelkezésre állású és vész-helyreállítási az SQL Server az Azure Virtual machines gépeken](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Lemezek útmutató

Egy Azure-beli virtuális gépen három fő lemez típusa van:

* **Operációsrendszer-lemez**: Egy Azure virtuális gép létrehozásakor a platform lesz-e legalább egy lemez csatolása (kerülhetnek a **C** meghajtó) az operációsrendszer-lemez a virtuális géphez. Ez a lemez egy storage-ban lapblobként tárolt VHD.
* **Ideiglenes lemez**: Az Azure virtual machines tartalmazhat egy újabb lemezt az ideiglenes lemez neve (kerülhetnek a **D**: meghajtó). Ez az egy lemezt a csomóponton, amelyek ideiglenes terület is használható.
* **Adatlemezek**: Is csatlakoztathat további lemezeket a virtuális gép, adatlemez, és ezek lesz tárolva tárolási lapblobként.

A következő szakaszok ismertetik a különböző lemezek használatához.

### <a name="operating-system-disk"></a>Operációsrendszer-lemez

Operációsrendszer-lemez, amelyek rendszerindító és a egy futó operációs rendszer verziója, csatlakoztassa a VHD-t és a rendszer a következő címkét: **C** meghajtó.

Alapértelmezett szabályzat az operációsrendszer-lemez gyorsítótárazási rendszer **olvasási/írási**. Érzékeny alkalmazások teljesítménye javasoljuk, adatlemezek használata helyett az operációsrendszer-lemez. Lásd az alábbi adatlemezeket.

### <a name="temporary-disk"></a>Ideiglenes lemez

Az ideiglenes meghajtó, a következő címkét: a **D**: meghajtó, az Azure blob storage-nincs megőrizve. Ne tárolja a felhasználói adatbázis-fájlokat vagy a felhasználó tranzakciós naplófájlokat a **D**: meghajtó.

A D-sorozat, a Dv2 sorozat és a G sorozatú virtuális gépek az ideiglenes meghajtó ezeken a virtuális gépeken az SSD-alapú. Ha a számítási feladatok (például az ideiglenes objektumok vagy bonyolult illesztésekre), a TempDB (nagy erőforrásigényű) használatát a TempDB tárolja a **D** meghajtó sikerült nagyobb TempDB átviteli sebességet eredményez, és a TempDB késés csökkentése. Példaforgatókönyv tanulmányozza a TempDB a következő blogbejegyzésben található: [Tárolási konfiguráció irányelvek az SQL Server Azure virtuális Gépen futó](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm).

Virtuális gépek, amelyek támogatják a prémium szintű SSD-k (DS-sorozat, dsv2 és GS-sorozat) javasoljuk a TempDB tárolja, amely támogatja a prémium szintű SSD-k engedélyezve van olvasási gyorsítótárazás lemezre vonatkozóan.

Ez a javaslat egyetlen kivétel van: _írási-igényes a TempDB használat esetén nagyobb teljesítményt érhet el, a TempDB tárolása a helyi **D** meghajtó, amely egyben ezen gépméretek az SSD-alapú._

### <a name="data-disks"></a>Adatlemezek

* **Adatlemezek használata az adatok és a naplófájlok**: Ha lemez csíkozást nem használ, használja a két prémium szintű SSD P30 lemez ahol egy lemezt a naplófájlokon pedig a másik az adatok és a TempDB fájl tartalmaz. Minden egyes prémium szintű SSD számos IOPs és sávszélesség (MB/s) annak méretétől függően a cikkben bemutatott [válassza ki a lemez típusát](../disks-types.md). Ha egy lemez csíkozást technika, például a tárolóhelyek használ, optimális teljesítményt kihozni létesíteni két készletek, egyet a naplófájlokhoz, a másik pedig az adatfájlokat. Azonban ha azt tervezi, az SQL Server feladatátvevő fürt példány (FCI) használni, konfigurálnia kell egy készletet.

   > [!TIP]
   > - A vizsgálati eredmények különböző lemez vagy a számítási feladatok konfigurációját tekintse meg a következő blogbejegyzésben található: [Tárolási konfiguráció irányelvek az SQL Server Azure virtuális Gépen futó](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/).
   > - A működés szempontjából kritikus fontosságú teljesítményt igénylő SQL-kiszolgálók körülbelül 50 000 IOPS, fontolja meg, és cserélje le egy Ultra SSD-10 - P30 lemez. További információkért tekintse meg a következő blogbejegyzésben található: [Üzleti szempontból alapvető Ultranagy SSD a kritikus teljesítménymutatókat](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/).

   > [!NOTE]
   > Amikor üzembe helyez egy SQL Server virtuális Gépet a portálon, lehetősége van a tárolókonfiguráció szerkesztésekor. A konfigurációtól függően az Azure úgy konfigurálja egy vagy több lemezt. Több lemez mostantól egyetlen tárolókészlet-szétosztottsága befolyásolhatja. Az adathoz és naplófájlhoz együtt találhatók ebben a konfigurációban. További információkért lásd: [tárolási konfigurációt az SQL Server virtuális gépek](virtual-machines-windows-sql-server-storage-configuration.md).

* **Lemez csíkozást**: Nagyobb átviteli sebességet adjon hozzá további lemezeket, és használja a lemez szétosztottsága befolyásolhatja. Adatlemezek száma határozza meg, hogy kell elemeznie IOPS és sávszélesség szükséges a naplófájlokhoz, és az adatok és a TempDB-fájlok száma. Figyelje meg, hogy különböző méretű virtuális gépek eltérő korlátokkal rendelkeznek IOPs és sávszélesség támogatott számát, tekintse meg a táblák IOPS [Virtuálisgép-méret](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Kövesse az alábbi irányelveket:

  * Használja a Windows 8 és Windows Server 2012 vagy újabb, [tárolóhelyek](https://technet.microsoft.com/library/hh831739.aspx) az alábbi útmutatókat:

      1. Állítsa be a szektorszórás (stripe mérete) 64 KB-os (65 536 bájt) OLTP számítási feladatokhoz és a sávegységek miatt teljesítményromlás megelőzése érdekében az adattárházak munkaterhelése (262 144 bájt) 256 KB. Ez a PowerShell használatával kell beállítani.
      2. Állítsa be az oszlopok száma = fizikai lemezek számát. PowerShell-lel való konfigurálásakor, legfeljebb 8 lemezek (nem a Server Manager felhasználói felületén). 

    Például a következő PowerShell-a szektorszórás mérete 64 KB és 2. az oszlopok száma egy új tárolókészletet hoz létre:

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Windows 2008 R2 vagy korábbi használhatja a dinamikus lemezek (operációs rendszer csíkozott kötetek), és a stripe mérete nem mindig 64 KB-os. Vegye figyelembe, hogy ez a beállítás a Windows 8 vagy Windows Server 2012-től elavult. Információkért lásd a támogatási nyilatkozatát [Virtuálislemez-szolgáltatás a Windows tárolókezelési API való](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

  * Ha használ [közvetlen tárolóhelyek (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) a [SQL Server feladatátvételi fürtbeli példányok](virtual-machines-windows-portal-sql-create-failover-cluster.md), konfigurálnia kell egy egyetlen készletet. Vegye figyelembe, hogy más köteteket is létrehozhatók, hogy egyetlen készleten, bár az összes hallgatósággal azonos jellemzőkkel, például ugyanaz a gyorsítótárazási házirend.

  * A betöltés elvárásainak alapuló tárolókészlet társított lemezek számát határozza meg. Ne feledje, hogy a különböző méretű virtuális gépek lehetővé teszik a különböző számú csatlakoztatott adatlemezekkel. További információkért lásd: [virtuális gépek méretei](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Ha prémium szintű SSD-k (fejlesztési és tesztelési célra) nem használ, az ajánlás az, hogy adja hozzá az adatlemezek által támogatott maximális száma a [Virtuálisgép-méret](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) és lemez szétosztottsága befolyásolhatja.

* **Gyorsítótárazási házirend**: Vegye figyelembe a következő ajánlások gyorsítótárazási házirend a tároló-konfigurációtól függően.

  * Az adatok és a naplófájlok külön lemezeket használ, ha az adatok és a TempDB-adatfájlok üzemeltető adatlemezekre olvasási gyorsítótárazás engedélyezése. Ez jelentős teljesítménybeli előnyt eredményezhet. Ne engedélyezze a gyorsítótárazás a naplófájl rendelkezés, ennek hatására a kisebb csökkent teljesítményt.

  * Ha lemez szétosztottsága befolyásolhatja az egyetlen tárolókészlet használ, a legtöbb számítási feladatok élvezheti olvasási gyorsítótárazás. Ha külön tárolókészleteket a napló- és fájlokat, az adatfájlok számára a tárolókészletben a csak olvasási gyorsítótárazás engedélyezése. Bizonyos nagy írási számítási feladatok, a jobb teljesítmény érdekében előfordulhat, hogy mondható nincs gyorsítótárazás. Ez csak lehet meghatározni tesztelés útján.

  * A fenti javaslatok vonatkozik a prémium szintű SSD-kkel. Ha prémium szintű SSD-k nem használ, ne engedélyezze a bármely gyorsítótárazást az adatlemezt.

  * A lemezek gyorsítótárazása konfigurálásával kapcsolatos útmutatásért lásd az alábbi cikkeket. A klasszikus (ASM) telepítési modell lásd: [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) és [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx). Az Azure Resource Manager-alapú modell lásd: [Set-AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk?view=azurermps-4.4.1) és [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk?view=azurermps-4.4.1).

     > [!WARNING]
     > Állítsa le az SQL Server szolgáltatást, ha módosítja az ügyfélgyorsítótár beállítása Azure-beli Virtuálisgép-lemezek lehetőségét, bármilyen adatbázis-sérülés elkerülése érdekében.

* **NTFS foglalási egységek mérete**: Az adatlemez formázásakor javasoljuk, hogy használja-e egy 64 KB-os foglalási egységek mérete az adatok és a naplófájlok, valamint a TempDB.

* **Szolgáltatásfelügyeleti ajánlott eljárásainak lemez**: Adatlemez eltávolítása vagy módosítása a gyorsítótár beírni, állítsa le az SQL Server szolgáltatás módosítása során. Az operációsrendszer-lemez gyorsítótárazási beállításait módosítják, amikor Azure leállítja a virtuális Gépet, a gyorsítótár típusa megváltozik, és újraindítja a virtuális Gépet. A gyorsítótár-beállításait, adatlemez módosítják, amikor a virtuális gép nem áll le, de az adatlemez leválasztása a virtuális gépről módosítása során, és majd csatolni.

  > [!WARNING]
  > Ezek a műveletek során az SQL Server szolgáltatás leállítása sikertelen adatbázis-sérülést okozhat.


## <a name="io-guidance"></a>I/o-útmutató

* A legjobb eredmények elérése érdekében a prémium szintű SSD-k érhetők el, ha az alkalmazás és a kérések párhuzamosíthatja. Prémium szintű SSD-k olyan forgatókönyvekben, ahol az i/o-várólistájának mélysége meghaladja az 1, így soros kérelmek egyszálas alig vagy egyáltalán nem teljesítménynövekedést megjelenik (akkor is, ha nagy számításigényű storage) lettek kialakítva. Ide tartozhat például teljesítményét elemző eszközök, például az SQLIO egyszálas vizsgálat eredményeit.

* Fontolja meg [adatbázis-oldal tömörítési](https://msdn.microsoft.com/library/cc280449.aspx) is segít, i/o-igényes munkaterhelések teljesítményét. Azonban az adatok tömörítésének növelheti a CPU-használat a kiszolgálón.

* Érdemes megfontolni a azonnali fájl inicializálása a kezdeti fájl kiosztható szükséges idő csökkentése érdekében. Azonnali fájl inicializálása kihasználásához, adja meg az SQL Server (MSSQLSERVER) szolgáltatás fiók SE_MANAGE_VOLUME_NAME, majd adja hozzá a az **kötet-karbantartási feladatok végrehajtása** biztonsági házirend. Ha egy SQL Server platform-lemezképet használ az Azure-hoz, az alapértelmezett szolgáltatásfiók (NT Service\MSSQLSERVER) nem adódik hozzá a **kötet-karbantartási feladatok végrehajtása** biztonsági házirend. Más szóval azonnali fájl inicializálása nincs engedélyezve az SQL Server Azure platformlemezkép. Az SQL Server szolgáltatási fiókja való hozzáadása után a **kötet-karbantartási feladatok végrehajtása** biztonsági házirend, indítsa újra az SQL Server szolgáltatást. Ez a funkció használatára vonatkozó biztonsági szempontokat is lehet. További információkért lásd: [adatbázis fájl inicializálása](https://msdn.microsoft.com/library/ms175935.aspx).

* **automatikus növekedésre** csak váratlan növekedés okokból tekinthető. Az adat- és naplófájlok növekedésének napi elszámolással, az adatbázisnál nem felügyeli. Automatikus növekedésre használatos, ha előre, növelje meg a fájl mérete kapcsoló használata.

* Győződjön meg arról, hogy **többletterheléshez** elkerülése érdekében a felesleges terhelését, amelyek hátrányosan befolyásolhatja a teljesítményt le van tiltva.

* Minden adatbázis áthelyezése az adatlemezeket, beleértve a rendszeradatbázisokban. További információkért lásd: [rendszeradatbázisok áthelyezése](https://msdn.microsoft.com/library/ms345408.aspx).

* Helyezze át az SQL Server hiba napló- és nyomkövetési fájl könyvtárak adatlemezeket. Ezt megteheti az SQL Server Configuration Manager kattintson a jobb gombbal az SQL Server-példányhoz, és a Tulajdonságok parancsra kattintva. A hiba napló- és nyomkövetési fájl beállításait lehet módosítani a **indítási paraméterei** fülre. A Dump könyvtárban van megadva a **speciális** fülre. Az alábbi képernyőképen látható keresse meg a hiba a napló indítási paraméter.

    ![Az SQL hibanaplóban képernyőképe](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* A telepítő alapértelmezett biztonsági mentés és adatbázis-fájlok helye. A javaslatok ebben a cikkben található, és a kiszolgálói tulajdonságok ablakában végezze el a módosításokat. Útmutatásért lásd: [megtekinteni vagy módosítani az alapértelmezett helyek az adat- és naplófájlok (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Az alábbi képernyőképen azt mutatja be, hogy ezek a változások hol.

    ![Az SQL Data napló- és biztonsági mentési fájlok](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* Zárolt lapok csökkenteni az i/o- és lapozási tevékenységeket engedélyezése. További információkért lásd: [engedélyezése a memórialapok zárolása a memória beállítás (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

* Ha futtatja az SQL Server 2012, telepítse a Service Pack 1 összegző frissítés 10. A frissítés tartalmazza a javítást, i/o gyenge teljesítményt, válassza ki az ideiglenes tábla utasítás SQL Server 2012-ben való futtatásakor. Információ: Ez [Tudásbázis](https://support.microsoft.com/kb/2958012).

* Vegye figyelembe, hogy minden adat fájlok tömörítése a bejövő és kimenő átviteléhez az Azure.

## <a name="feature-specific-guidance"></a>Szolgáltatásspecifikus útmutató

Egyes központi telepítések további teljesítménybeli előnyök speciális konfigurációs technikákkal elérése érdekében. Az alábbi listában néhány SQL Server-szolgáltatások, amelyek segítségével jobb teljesítményt érhet el emeli ki:

* **Az Azure storage biztonsági mentési**: Ha a biztonsági mentések végrehajtásához Azure-beli virtuális gépeken futó SQL Server, [URL-címét az SQL Server biztonsági másolat](https://msdn.microsoft.com/library/dn435916.aspx). Ez a funkció az SQL Server 2012 SP1 CU2 kezdődően elérhető, és a biztonsági mentés a csatlakoztatott adatlemezekkel ajánlott. Ha Ön biztonsági mentési és visszaállítási és- tárolókról az Azure storage, kövesse az adja meg a javaslatok [SQL Server biztonsági másolat URL-cím ajánlott eljárások és hibaelhárítási és visszaállítása az Azure Storage-ban tárolt biztonsági másolatok](https://msdn.microsoft.com/library/jj919149.aspx). Ezeket a biztonsági másolatokat használatával is automatizálható [automatikus biztonsági mentés az SQL Server az Azure Virtual machines gépeken](virtual-machines-windows-sql-automated-backup.md).

    Az SQL Server 2012 megjelenése előtt használható [SQL Server Backup to Azure eszköz](https://www.microsoft.com/download/details.aspx?id=40740). Ez az eszköz segítségével növeléséhez a biztonsági mentési több biztonsági mentési stripe-cél használatával.

* **Az SQL Server-adatfájlok az Azure-ban**: Ez az új funkció, [SQL Server-adatfájlok az Azure-ban](https://msdn.microsoft.com/library/dn385720.aspx), az SQL Server 2014 kezdve érhető el. Hasonló teljesítményt nyújt, mint Azure-adatlemezek használata az adatfájlokat az Azure-ban az SQL Server rendszert futtató mutatja be.

## <a name="next-steps"></a>További lépések

Tárolás és teljesítmény kapcsolatos további információkért lásd: [tárolási konfiguráció irányelvek az SQL Server Azure virtuális gépen](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)

Ajánlott biztonsági eljárások, lásd: [SQL Server Azure virtuális gépeken történő futtatásának biztonsági szempontjai](virtual-machines-windows-sql-security.md).

Tekintse át a többi SQL Server virtuális gép cikkek, [SQL Server on Azure Virtual Machines – áttekintés](virtual-machines-windows-sql-server-iaas-overview.md). Ha kérdése van az SQL Servert futtató virtuális gépek használatával kapcsolatban, tekintse meg a [gyakori kérdéseket](virtual-machines-windows-sql-server-iaas-faq.md).
