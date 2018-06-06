---
title: SQL Server Azure verem virtuális gépek teljesítménye ajánlott eljárásai
description: Gyakorlati tanácsokat megfelelően a Microsoft Azure verem virtuális gépek SQL Server teljesítményének optimalizálásához.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: mabrigg
ms.reviewer: anajod
ms.openlocfilehash: 00c67503f5b9e0027cbb62520e392f56420a75e6
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34701386"
---
# <a name="optimize-sql-server-performance"></a>SQL Server teljesítményének optimalizálásához

Ez a cikk útmutatást megfelelően verem a Microsoft Azure virtuális gépeken SQL Server teljesítményének optimalizálásához. A verem Azure virtuális gépeken futó SQL Server, használja az ugyanazon adatbázis teljesítményének hangolása beállítások SQL Server helyszíni kiszolgáló környezetben. Egy Azure verem felhőben egy relációs adatbázis teljesítménye számos tényezőtől függ. Tényezők közé tartozik a virtuális gép termékcsalád méretét és az adatlemezek konfigurációját.

SQL Server-lemezképek létrehozásakor [fontolja meg a virtuális gépeket az Azure-verem portálon kiépítés](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). Az SQL IaaS-bővítmény letöltése a piactér felügyelet a verem Azure felügyeleti portálon, és a választott SQL virtuális gép virtuális merevlemez-meghajtók (VHD) letöltése. Ezek közé tartozik a SQL2014SP2 SQL2016SP1 és SQL2017.

> [!NOTE]  
> A cikk ismerteti a SQL Server rendszerű virtuális gép globális Azure portál használatával, amíg a útmutatást is vonatkozik Azure verem a következő eltérésekkel: SSD nem érhető el az operációsrendszer-lemez, felügyelt lemezek nem érhetők el, és tárolási konfiguráció kisebb különbségek vannak.

Getting a *legjobb* verem Azure virtuális gépeken az SQL Server teljesítménye Ez a cikk a fókuszt. Ha a terhelést szabadulnak, minden ajánlott optimalizálási nincs szüksége lehet. Vegye figyelembe a teljesítményigény és a terhelési mintázatok, ezek a javaslatok értékeli.

> [!NOTE]  
> Teljesítmény az SQL Server Azure virtuális gépeken, című témakörben olvashat útmutatót a [Ez a cikk](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).

## <a name="before-you-begin"></a>Előkészületek
Az alábbi ellenőrzőlista az optimális teljesítmény az SQL Server verem Azure virtuális gépeken van:


|Terület|Optimalizálás.|
|-----|-----|
|Virtuális gép mérete |[DS3](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) vagy újabb SQL Server Enterprise Edition.<br><br>[DS2](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) vagy SQL Server Standard edition és a Web edition magasabb.|
|Storage |Használjon, amely támogatja a virtuális gép családba [prémium szintű storage](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-acs-differences).|
|Lemezek |Legalább két adatlemezek (naplófájlok egyet), majd egy adatfájlt és a TempDB használja, és válassza a lemez mérete a kapacitásigények alapján. Az alapértelmezett fájlhelyek adatok ezeknek a lemezeknek során be az SQL Server telepítése.<br><br>Ne használja az operációs rendszer vagy ideiglenes lemezek adatbázistár vagy naplózás.<br>A paritásos tárolóhelyek használatával nagyobb IO átviteli sebesség eléréséhez több Azure adatlemezek.<br><br>Formázza a dokumentált lemezfoglalás méretét.|
|I/O|Az adatfájlok azonnali fájlinicializálása engedélyezése.<br><br>A ésszerűen kis rögzített lépésekkel (64 MB - 256 MB) az adatbázis automatikus növekedésre korlátozza.<br><br>Tiltsa le az adatbázis autoshrink.<br><br>Állítsa be alapértelmezett biztonsági másolat és az adatbázis tárolási helyének adatok lemezeken, nem az operációs rendszer tárolására.<br><br>Zárolt lapok engedélyezése.<br><br>SQL Server szervizcsomagok és összesített frissítések alkalmazásához.|
|Szolgáltatás-specifikus|Készítsen biztonsági másolatot közvetlenül a blob storage (Ha használja az SQL Server verziója által támogatott).|
|||

További információ a *hogyan* és *miért* ahhoz, hogy ezek az optimalizálások, tekintse át az adatokat, és útmutatást a következő szakaszok ismertetik.

## <a name="virtual-machine-size-guidance"></a>Virtuális gép mérete útmutató

A teljesítmény-érzékeny alkalmazások, a következő [virtuálisgép-méretek](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) ajánlott:

- **SQL Server Enterprise edition:** DS3 vagy újabb

- **SQL Server Standard edition és a Web edition:** DS2 vagy újabb

Azure verem esetén a DS-ben és a DS_v2 virtuális gép termékcsalád adatsorozat teljesítménybeli különbségek.

## <a name="storage-guidance"></a>Tárolási útmutatásokkal

DS sorozatnak (együtt DSv2-sorozat) virtuális gépek Azure-készletben adja meg az operációs rendszer maximális lemez- és lemez átviteli (IOPS). Egy virtuális gépet a DS vagy DSv2 adatsorozat legfeljebb 1000 IOPS biztosít, az az operációsrendszer-lemez, és legfeljebb 2,300 iops-értéket adatlemez, függetlenül attól, a típus vagy a kiválasztott lemez méretét.

Lemez adatátvitelt a virtuális gép termékcsalád adatsorozat egyedileg alapján határozza meg. Is [tekintse meg a cikk](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) családba tartozó virtuálisgép-Adatsoronként adatátvitelt lemez azonosításához.

> [!NOTE]  
> A termelési számítási feladatokhoz válassza ki a DS sorozatnak vagy DSv2-sorozat virtuális gépet a lehetséges maximális iops értéket biztosítanak az operációs rendszer a lemez és adatlemezek számára.

Azure-készletben a storage-fiók létrehozásakor a georeplikáció beállítás nincs hatása, mert ez a funkció nem érhető el Azure-készletben.

## <a name="disks-guidance"></a>Lemezek útmutató

Egy Azure verem virtuális gépen három fő lemez típusa van:

- **Operációsrendszer-lemez:** egy Azure verem virtuális gép létrehozásakor a platform csatol legalább egy lemez (címkézve a **C** meghajtó) az az operációsrendszer-lemez a virtuális géphez. Ez a lemez tárolási oldalakra vonatkozó blob tárolt virtuális merevlemez.

- **Ideiglenes lemez:** verem Azure virtuális gépek tartalmazza az ideiglenes lemez nevű másik lemezre (címkézve a **D** meghajtó). Ez az egy lemezt a csomóponton, az ideiglenes területet használható.

- **Az adatlemezek:** csatolhat lemezt a virtuális gép adatok lemezként, és ezeknek a lemezeknek lapblobokat, megőrzés tárolódnak.

A következő szakaszok ismertetik a különböző lemezek használatához.

### <a name="operating-system-disk"></a>Operációsrendszer-lemez

Operációsrendszer-lemez a virtuális Merevlemezt, amely a rendszerindító és a csatlakoztatási az operációs rendszer fut verzióként és van-e címkézve **C** meghajtó.

### <a name="temporary-disk"></a>Ideiglenes lemez

Az ideiglenes tárolási meghajtó címkézve a **D** meghajtó, nem őrzi meg. Ne tároljon bármely hajtja elveszti, például a felhasználói adatbázis-fájlokat vagy a tranzakciós naplófájlok felhasználó, az adatok a **D** meghajtó.

Azt javasoljuk, hogy tárolja a TempDB adatlemezt minden adatlemez legfeljebb 2,300 iops-értéket adatlemez legfeljebb biztosít.

### <a name="data-disks"></a>Adatlemezek

- **Adatlemezek használja az adatok és a naplófájlokat.** Ha lemez csíkozást nem használ, két adatok lemez használata a virtuális gép, amely támogatja a prémium szintű storage, ahol egy lemezt a naplófájlokat tartalmazó, és egyéb tartalmazza az adatokat és a TempDB fájljai. Minden egyes adatlemez számos iops-érték és a sávszélesség (MB/s) attól függően, hogy a virtuális gép termékcsalád leírtak [Azure verem használható virtuálisgép-méretek](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes). Ha egy lemez-csíkozást technika, például a tárolóhelyek használ, helyezze az összes adatainak és naplókönyvtárainak ugyanazon a meghajtón (beleértve a TempDB). Ez a konfiguráció lehetővé teszi az SQL Server felhasználását, függetlenül attól, milyen fájl kell őket egy adott időpontban elérhető IOPS maximális számát.

> [!NOTE]  
> Ha Ön egy SQL Server rendszerű virtuális gép a portálon, lehetősége van módosítani a tárolási konfigurációt. A konfigurációtól függően Azure verem egy vagy több lemezt állít be. Több lemez is van egyesítve, egyetlen tárolókészlet. Az adat- és naplófájlok fájlok találhatók együtt ebben a konfigurációban.

- **Lemez csíkozást:** további átviteli sebesség eléréséhez, adhat hozzá további adatlemezt és lemez csíkozást használja. Van szüksége az adatlemezek számának megállapításához vizsgálja meg iops-érték és a sávszélességre van szüksége a naplófájlokat pedig az adatok és a TempDB fájlok száma. Figyelje meg, hogy a virtuális gép adatsorozat termékcsalád alapul, és nem a virtuális gép mérete alapján adatok lemezenként vannak IOPS-korlátok vonatkoznak. Hálózati sávszélesség korlátja, azonban a alapul a virtuális gép méretét. A táblázatok látható [virtuális gép méretének Azure verem](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) további részletek. Kövesse az alábbi iránymutatásokat:

    - A Windows Server 2012 vagy újabb, illetve használjon [tárolóhelyek](https://technet.microsoft.com/library/hh831739.aspx) az alábbi útmutatókat:

        1.  A szektorszórás (paritásos méretének) beállítása 64 KB (65 536 bájt) az online tranzakció-feldolgozási (OLTP) munkaterhelések és 256 KB (262 144 bájt) adatok vámraktározási munkaterhelések partíció hibás illesztés hibákat miatt a teljesítményre gyakorolt hatás elkerülése érdekében. Ez a PowerShell használatával kell beállítani.

        2.  Állítsa be az oszlopok száma = fizikai lemezek számát. A PowerShell legfeljebb nyolc lemezek (nem a kiszolgáló-kezelő felhasználói felületén) konfigurálásakor.

            Például a következő PowerShell 64 KB és 2 oszlopainak számával szektorszórás méretű egy új tárolókészletet hoz létre:

          ```PowerShell  
          $PoolCount = Get-PhysicalDisk -CanPool $True
          $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

          New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false
          ```

- A betöltési elvárásainak alapuló tárolókészlet hozzárendelt számának meghatározásához. Ne feledje, hogy más virtuálisgép-méretek lehetővé teszik a különböző számú adatlemezt csatolni. További információkért lásd: [Azure verem használható virtuálisgép-méretek](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes).
- A lehetséges maximális IOPS adatlemezek eléréséhez a javaslat hoz adhat hozzá adatlemezt által támogatott maximális számát a [virtuálisgép-méret](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) és lemez csíkozást használja.
- **NTFS foglalásiegység-méret:** az adatok lemez formázása esetén ajánlott használni, amikor egy 64 KB-os foglalásiegység-méret adatok és a naplófájlok, valamint a TempDB adatbázisban.
- **Lemez gyakorlat:** adatlemezt eltávolításakor állítsa le az SQL Server szolgáltatás módosítása során. Is nem változnak a gyorsítótár beállításait a lemezeket, nem biztosít semmilyen teljesítménynövekedést.

> [!WARNING]  
> Ezek a műveletek során az SQL szolgáltatás leállítása sikertelen sérülésével járhat.

## <a name="io-guidance"></a>I/o-útmutató

- Érdemes megfontolni a azonnali fájlinicializálása a kezdeti fájl kiosztható szükséges idő csökkentése érdekében. Azonnali fájlinicializálása előnyeit, az SQL Server (MSSQLSERVER) szolgáltatás fiók számára biztosítson **SE_MANAGE_VOLUME_NAME** és hozzá kell adnia a **kötet karbantartási feladatok elvégzése** biztonsági házirend. Ha egy SQL Server platformlemezképet használ az Azure-ba, az alapértelmezett szolgáltatás fiókja (**NT Service\MSSQLSERVER**) nem adódik hozzá a **kötet karbantartási feladatok elvégzése** biztonsági házirend. Ez azt jelenti azonnali fájlinicializálása nincs engedélyezve az SQL Server Azure platform lemezképek. Az SQL Server szolgáltatás fiók hozzáadása után a **kötet karbantartási feladatok elvégzése** biztonsági házirend, indítsa újra az SQL Server szolgáltatást. Ez a szolgáltatás használatára vonatkozó biztonsági szempontokat is lehet. További információkért lásd: [adatbázis Fájlinicializálása](https://msdn.microsoft.com/library/ms175935.aspx).
- **Automatikus növekedésre** eseményre a váratlan növekedésre van. Az automatikus növekedésre folyamatosan a adatainak és naplókönyvtárainak növekedési nem kezeli. Automatikus növekedésre használatos, ha előre nő a fájl használata a **mérete** váltani.
- Győződjön meg arról, hogy **autoshrink** negatívan befolyásolhatja a teljesítményt szükségtelen terhet elkerülése érdekében le van tiltva.
- A telepítő biztonsági másolat és az adatbázis alapértelmezett tárolási helyeit. Ebben a cikkben a javaslatokat használja, és végezze el a módosításokat a kiszolgáló tulajdonságai ablakban. Útmutatásért lásd: [megtekinteni vagy módosítani az alapértelmezett helyek az adatok és naplófájlok (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Az alábbi képernyőfelvételen a változtatások where mutatja be:

    > ![Megtekinteni vagy módosítani az alapértelmezett helyek](./media/sql-server-vm-considerations/image1.png)

- Engedélyezze a zárolt lapok IO és lapozás tevékenységeket csökkentése érdekében. További információkért lásd: [engedélyezése a zárolási lapok memória beállítás (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

- Vegye figyelembe az adatfájlokról tömörítés, Azure-vermet, és biztonsági másolatot, vagy kikapcsolása átvitelekor.

## <a name="feature-specific-guidance"></a>A szolgáltatás jellemző útmutatást

Egyes központi telepítések további teljesítménybeli előnyökben speciális konfigurációs technikák segítségével elérése érdekében. Az alábbi listában néhány SQL Server-funkció, amely segítenek jobb teljesítmény érdekében információk találhatók:

- **Azure biztonsági mentés** **tároló.** A verem Azure virtuális gépeken futó SQL Server biztonsági mentés végrehajtásához, is használhatja az SQL Server biztonsági másolat URL-címre. Ez a szolgáltatás lett bevezetve az SQL Server 2012 SP1 CU2 és ajánlott a biztonsági mentés a mellékelt adatok lemezeket.

    Ha a biztonsági mentési vagy visszaállítási Azure-tárolót, kövesse a szereplő ajánlás [SQL Server biztonsági másolat URL-cím ajánlott eljárások és hibaelhárítás](https://msdn.microsoft.com/library/jj919149.aspx) és [visszaállítása a tárolja a biztonsági másolatokat a Microsoft Azure](https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure?view=sql-server-2017). Ezek a biztonsági mentéseket is automatizálhatja [automatikus biztonsági mentés az SQL Server Azure virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup).

-   **Készítsen biztonsági másolatot az Azure-verem tárhelyre.** Akkor is biztonsági másolatot Azure verem tárolási hasonló módon, mint a biztonsági mentés Azure Storage. Amikor létrehoz egy biztonsági mentés SQL Server Management Studio (SSMS) belül, manuálisan adja meg a konfigurációs adatokat szeretne. A tároló vagy a közös hozzáférésű Jogosultságkód létrehozása SSMS nem használható. SSMS csak Azure-előfizetések, nem a verem Azure-előfizetések csatlakozik. Ehelyett szeretne létrehozni a tárfiókot, a tároló és a közös hozzáférésű Jogosultságkód a verem Azure portálon vagy a PowerShell használatával.

    Ha hová kívánja helyezni az adatokat az SQL Server biztonsági másolat párbeszédpanelen be:

    ![SQL Server biztonsági másolat](./media/sql-server-vm-considerations/image3.png)

    > [!NOTE]  
    > A közös hozzáférésű Jogosultságkód az SAS-jogkivonat a verem Azure portálról anélkül a "?" a karakterláncban. A másolási funkcióhoz a portálról használatakor kell törölnie a bevezető "?" a jogkivonat működéséhez az SQL Serverben.

    A biztonsági másolat helye beállítása követően, és az SQL Server konfigurálva, majd biztonsági másolatot készíthet a verem Azure blob Storage.

## <a name="next-steps"></a>További lépések

[Szolgáltatások használatával vagy az alkalmazások Azure verem](azure-stack-considerations.md)