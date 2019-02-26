---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: ramankumarlive
ms.service: storage
ms.topic: include
ms.date: 09/24/2018
ms.author: ramankum
ms.custom: include file
ms.openlocfilehash: cd29fb968bd73e09c2e3b60e62a478b4e577146d
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805489"
---
# <a name="high-performance-premium-storage-and-managed-disks-for-vms"></a>Nagy teljesítményű Premium Storage és a felügyelt lemezek virtuális gépekhez

Az Azure Premium Storage nagy teljesítményű, kis késleltetésű lemeztámogatás a virtuális gépek (VM) biztosít a bemeneti/kimeneti (I/O)-igényű számítási feladatokhoz. A Premium Storage szolgáltatást használó Virtuálisgép-lemezek tartós állapotú meghajtókhoz (SSD-kkel) adatokat tárolja. A sebesség előnyeit, és a prémium szintű tárolólemezeket teljesítményét is, áttelepítheti a meglévő Virtuálisgép-lemezek prémium szintű Storage.

Az Azure-ban egy virtuális géphez több premium storage-lemez is csatlakoztatható. Több lemez használata lehetővé teszi az alkalmazások legfeljebb 256 TB-nyi tárhelyet, ha az előzetes verzió méretek az alkalmazás legfeljebb körülbelül 2 PiB tárhelyet is rendelkezik. A Premium Storage az alkalmazások másodpercenként (IOPS) virtuális gépenként 80 000 i/o-műveletek és a egy adatátviteli sebességet, akár 2000 megabájt (MB/s) másodpercenként és virtuális gépenként érheti el. Olvasási műveletek rendkívül alacsony késéssel biztosítanak.

A Premium Storage Azure lehetőséget biztosít a valóban lift-and-shift igényű, vállalati alkalmazások például a Dynamics AX, Dynamics CRM, az Exchange Server, SAP Business Suite és a SharePoint-farmok a felhőbe. Teljesítmény-igényes adatbázis-munkaterhelés például az SQL Server, Oracle, MongoDB, MySQL és a Redis, konzisztens magas teljesítménye és kis késleltetése igénylő alkalmazások futtatása.

> [!NOTE]
> Az alkalmazás a legjobb teljesítmény érdekében javasoljuk, hogy minden Virtuálisgép-lemez magas iops-érték a Premium Storage igénylő áttelepített. A lemez nincs szükség a magas iops értéket, ha a standard szintű Azure Storage-ban tartja segíthet korlát költségek. Virtuálisgép-lemez adatait standard szintű tárolóban (merevlemezes HDD) meghajtók helyett SSD meghajtókon tárolják.

Azure-beli virtuális gépek létrehozásához a prémium szintű tárolólemezeket két lehetőséget kínál:

* **Nem felügyelt lemezek**

    Az eredeti metódus nem felügyelt lemezeket használ. A nem felügyelt lemez a storage-fiókok, amellyel a virtuális merevlemez (VHD) fájlok, amelyek megfelelnek a Virtuálisgép-lemezek kezelése. VHD-fájlokat az Azure storage-fiókok lap blobként vannak tárolva. 

* **A felügyelt lemezek**

    Ha úgy dönt [Azure Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md), Azure kezeli a virtuális gépek lemezeihez használt tárfiókok. A lemez típusát (prémium vagy Standard) és a szükséges a lemez kívánt méretét kell megadni. Az Azure létrehozza és felügyeli a lemezt Ön helyett. Nem kell aggódnia a lemezek maradjon a méretezhetőségi korlátok a storage-fiókok biztosítása érdekében több tárfiókban helyezi el. Az Azure kezeli, amely az Ön számára.

Azt javasoljuk, hogy a felügyelt lemezek előnyeit azok számos funkciót kiválasztani.

Ismerkedés a Premium Storage [ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/). 

További információ a meglévő virtuális gépek Premium Storage-ba való migrálás: [nem felügyeltről felügyelt Windows virtuális gép konvertálása a felügyelt lemezek](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md) vagy [átalakítása nem felügyeltről felügyelt Linux rendszerű virtuális gép felügyelt lemezeket](../articles/virtual-machines/linux/convert-unmanaged-to-managed-disks.md).

> [!NOTE]
> A Premium Storage a legtöbb régióban érhető el. Az elérhető régiók listájáért lásd: sornak **Disk Storage** a [elérhető Azure-termékek régiók szerint](https://azure.microsoft.com/regions/#services).

## <a name="features"></a>Szolgáltatások

Íme néhány a Premium Storage jellemzői:

* **Prémium szintű tárolólemezeket**

    A Premium Storage támogatja a Virtuálisgép-lemezek, amelyek adott méret-sorozat virtuális gépei csatolhatók. A Premium Storage számos különböző Azure virtuális gépeket támogatja. Van nyolc általánosan elérhető lemezek méretei közül választhat:  P4 (32 GiB), P6 (64 GiB), P10 (128 GiB), p15-ös (256 GB), P20 (512 GiB), P30 (1 024 GB), P40 (2048 GiB), P50 (4095 GiB). Valamint három előzetes lemezméretek: A P60 8192 GiB (8 TiB), P70 16,348 GiB (16 TiB), P80 32 767 GiB (32 TiB). P4, P6, P15, P60, P70 és P80 lemezméretek jelenleg csak a Managed Disks esetében támogatott. Minden lemez méretét a saját teljesítmény specifikációi. Az alkalmazás követelményeitől függően egy vagy több lemez is csatlakoztatható a virtuális géphez. A specifikációk részletesebben leírjuk, hogy [prémium szintű Storage méretezhetőségi és teljesítménycéljai](#scalability-and-performance-targets).

* **Prémium szintű lapblobok**

    A Premium Storage támogatja a lapblobokat. Állandó és nem felügyelt lemezek tárolására a Premium Storage-beli virtuális gépek lapblobokat használnak. Ellentétben a standard szintű Azure Storage a Premium Storage nem támogatja a blokkblobokat, hozzáfűző blobok, fájlok, táblák vagy várólisták. Prémium szintű lapblobok támogatja a P10 hat méret és a P50 és P60 (8191GiB). A P60 prémium szintű lapblobok nem támogatott Virtuálisgép-lemezként van csatlakoztatva. 

    Minden objektum premium storage-fiókba elhelyezni egy lapblob lesz. A lapblob egy, a támogatott kiosztott méretű illesztése. Éppen ezért a premium storage-fiók nem célja a nagyon kicsi blobok tárolására használható.

* **Premium storage-fiók**

    A Premium Storage használatának megkezdéséhez hozzon létre a nem felügyelt lemezek prémium szintű tárfiókot. Az a [az Azure portal](https://portal.azure.com), a prémium szintű storage-fiók létrehozása, válassza ki a **prémium** teljesítményszint. Válassza ki a **helyileg redundáns tárolás (LRS)** replikációs beállítás. Is létrehozhat prémium szintű tárfiók úgy, hogy a teljesítményszint **Premium_LRS**. A teljesítmény szint módosításához használja a következő módszerek egyikét:
     
    - [PowerShell az Azure Storage szolgáltatáshoz](../articles/storage/common/storage-powershell-guide-full.md#manage-the-storage-account)
    - [Az Azure CLI az Azure Storage szolgáltatáshoz](../articles/storage/common/storage-azure-cli.md#manage-storage-accounts)
    - [Az Azure Storage Resource Provider REST API](https://docs.microsoft.com/rest/api/storagerp) (az Azure Resource Manageren alapuló üzemelő példányok) vagy az Azure Storage erőforrás-szolgáltatói ügyfélkódtárak valamelyik

    Prémium szintű tárfiókok korlátai kapcsolatos további információkért lásd: [méretezhetőségi és teljesítménycéljai](#scalability-and-performance-targets).

* **Helyileg redundáns Premium tárolóban**

    Premium storage-fiók replikálási beállítását csak helyileg redundáns tárolást támogatja. Helyileg redundáns tárolás biztosítja, hogy az adatok három másolatát egy adott régión belül. A regionális vészhelyreállítás, biztonsági másolatot kell készíteni a Virtuálisgép-lemezek egy másik régióban használatával [Azure Backup](../articles/backup/backup-introduction-to-azure-backup.md). A georedundáns tárolás (GRS) fiókot és a biztonsági mentési tárolónak kell használnia. 

    Az Azure storage-fiókjában tárolójaként a nem felügyelt lemezek esetén használja. Amikor létrehoz egy Azure virtuális Gépen, amely támogatja a nem felügyelt lemezek prémium szintű Storage, és a egy prémium szintű storage-fiókot, az operációs rendszer és az adatlemezek tárolódnak a tárfiók.

## <a name="supported-vms"></a>Támogatott virtuális gépek

A Premium Storage számos különböző Azure-beli virtuális gépek esetén támogatott. Standard és prémium szintű tárolólemezeket az ilyen virtuális Gépet is használhatja. Nem használhat prémium szintű tárolólemezeket a Virtuálisgép-sorozat, amelyek nem prémium szintű Storage-kompatibilis.


További információt az Azure windowsos virtuálisgép-típusairól és -méreteiről a [windowsos virtuális gépek méretét](../articles/virtual-machines/windows/sizes.md) ismertető szakaszban talál. További információt az Azure linuxos virtuálisgép-típusairól és -méreteiről a [linuxos virtuális gépek méretét](../articles/virtual-machines/linux/sizes.md) ismertető szakaszban talál.

Íme néhány a támogatott a prémium szintű funkciók tárolási használó virtuális gépekről:

* **Rendelkezésre állási csoport**

    A példában a DS sorozatú virtuális gépek használata esetén is hozzáadhat a DS sorozatú virtuális gép egy felhőszolgáltatás, amely csak a DS sorozatú virtuális gépeket tartalmaz. Ne adjon hozzá egy meglévő felhőszolgáltatáshoz, amely rendelkezik típustól eltérő, DS sorozatú virtuális gépek DS sorozatú virtuális gépeket. Új felhőalapú szolgáltatás, amely csak a DS sorozatú virtuális gépek a meglévő virtuális merevlemezeket áttelepítheti. Ha az új felhőalapú szolgáltatás, amely futtatja a DS sorozatú virtuális gépek, használja az ugyanazon virtuális IP-címet használni kívánt [fenntartott IP-címek](../articles/virtual-network/virtual-networks-instance-level-public-ip.md).

* **Operációsrendszer-lemez**

    A Premium Storage virtuális gépek állíthat egy prémium szintű vagy egy szabványos operációsrendszer-lemez használatára. A legjobb használhatóság érdekében javasoljuk egy prémium szintű Storage-alapú operációs rendszert tároló lemez használatával.

* **Adatlemezek**

    Az azonos Premium Storage virtuális gépek premium és standard szintű lemezek használhatja. A Premium Storage virtuális gép létrehozása, és több állandó adatlemezeket csatlakoztathat a virtuális Gépet. Ha szükséges, a kapacitás és a kötet teljesítményének növelése érdekében azt is stripe-a lemezek között.

    > [!NOTE]
    > Ha a stripe-e adatokat prémium szintű tárolólemezeket [tárolóhelyek](https://technet.microsoft.com/library/hh831739.aspx), állítsa be a tárolóhelyek 1 oszlop az egyes lemezek, amelyet használhat. Ellenkező esetben a csíkozott kötet általános teljesítményét alacsonyabb, mint a lemezek között miatt a forgalom eloszlása egyenletlen várt lehet. A Kiszolgálókezelőben alapértelmezés szerint állíthat be legfeljebb 8 lemezek oszlopokat. Ha több mint 8 lemez, a PowerShell használatával létrehozni a kötetet. Adja meg manuálisan az oszlopok számát. Ellenkező esetben a Server Manager felhasználói felületén továbbra is használja 8 oszlopokat, ha már rendelkezik további lemezeket. Például ha 32 lemezek egy egyetlen stripe-készlet, adja meg az oszlopok 32. Adja meg a virtuális lemezt használ, az oszlopok számát, a [New-VirtualDisk](https://technet.microsoft.com/library/hh848643.aspx) PowerShell-parancsmagot használja a *NumberOfColumns* paraméter. További információkért lásd: [tárolóhelyek – áttekintés](https://technet.microsoft.com/library/hh831739.aspx) és [Storage Spaces – gyakori kérdések](https://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx).

* **Cache**

    Amely támogatja a Premium Storage virtuális gépek (VM) rendelkezik egy egyedi gyorsítótárazási funkció magasabb szintű teljesítményt és kisebb késést eredményez. A gyorsítótárazási funkció meghaladja az alapul szolgáló prémium szintű tárolólemez-teljesítményt. Nem minden virtuális gépek támogatási gyorsítótárazás, ezért tekintse a Virtuálisgép-specifikációk a Virtuálisgép-méretek is érdeklik a további információt.  Virtuális gépek, amelyek támogatják a gyorsítótárazás jelzi, ez a "Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény" mérték azok specifikációja.  Közvetlenül a virtuális gép cím alatt is megadott.
    
    Gyorsítótár-, beállíthatja a gyorsítótárazási házirend a prémium szintű tárolólemezeket a lemez **ReadOnly**, **ReadWrite**, vagy **nincs**. A gyorsítótárazási házirend alapértelmezett lemez **ReadOnly** minden prémium szintű adatlemezek esetén és **ReadWrite** operációsrendszer-lemezek esetében. Az optimális teljesítmény érdekében az alkalmazás meg a ne felejtse el a megfelelő gyorsítótárba beállítással. 
    
    Például az olvasási vagy csak olvasható az adatlemezeket, például az SQL Server-adatfájlok, gyorsítótárazási szabályzatot állíthat be **ReadOnly**. Írási vagy csak írási adatlemezek, például az SQL Server-naplófájlok, állítsa be a gyorsítótárazási házirend **None**. 
    
    A tervezés a Premium Storage optimalizálása kapcsolatos további információkért lásd: [teljesítménybeli tervezés a Premium Storage-](../articles/virtual-machines/windows/premium-storage-performance.md).

* **Elemzés**

    A virtuális gép teljesítményét elemezheti az lemez prémium szintű Storage-ban, a Virtuálisgép-diagnosztika bekapcsolása a [az Azure portal](https://portal.azure.com). További információkért lásd: [Azure virtuális gép figyelése az Azure Diagnostics bővítmény](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/). 

    Lemez teljesítménye megtekintéséhez használja operációsrendszer-alapú eszközök, például [Windows Performance Monitor](https://technet.microsoft.com/library/cc749249.aspx) Windows virtuális gépek és a [iostat](http://linux.die.net/man/1/iostat) parancsot a Linux rendszerű virtuális gépekhez.

* **Virtuális gépek kibővített skálázási korlátai és teljesítmény**

    Minden prémium szintű Storage által támogatott Virtuálisgép-méret van skálázási korlátait és a teljesítmény specifikációk IOPS, sávszélesség- és a virtuális géphez csatolt lemezek számát. Premium storage-lemezekkel rendelkező virtuális gépek használatakor győződjön meg arról, hogy nincs-e elegendő IOPS és sávszélesség meghajtó lemez forgalmat a virtuális gépen.

    Például egy STANDARD_DS1 virtuális gép rendelkezik egy dedikált sávszélesség 32 MB/s a prémium szintű storage lemez forgalmat. Egy P10 prémium szintű tárolólemez 100 MB/s sávszélességet biztosít. Ha egy P10 premium storage lemezt a virtuális Géphez van csatlakoztatva, csak megnyithatja legfeljebb 32 MB/s. A legfeljebb 100 MB/s a P10 lemez által biztosított nem használható.

    A legnagyobb virtuális Gépet a DS-sorozat jelenleg a Standard_DS15_v2. A Standard_DS15_v2 révén akár 960 MB/s biztosíthat az összes lemez. A GS-sorozat a legnagyobb virtuális Géphez a például a Standard_GS5. A Standard_GS5 révén akár 2000 MB/s biztosíthat az összes lemez.

    Lemez forgalmat csak ezek a korlátok vonatkoznak. Ezek a korlátok nem tartalmazzák a gyorsítótár-találatok és a hálózati forgalmat. Virtuális gép hálózati forgalmának külön sávszélesség érhető el. A hálózati forgalom sávszélessége különbözik a prémium szintű tárolólemezeket által használt dedikált sávszélesség.

    A Premium Storage virtuális gépek maximális IOPS és átviteli sebesség (sávszélesség) kapcsolatos legfrissebb információkat lásd: [Windows Virtuálisgép-méretek](../articles/virtual-machines/windows/sizes.md) vagy [Linux Virtuálisgép-méretek](../articles/virtual-machines/linux/sizes.md).

    További információ a prémium szintű tárolólemezeket és IOPS és átviteli sebesség korlátaikról a tábla a következő szakaszban talál.

## <a name="scalability-and-performance-targets"></a>Méretezhetőségi és teljesítménycélok
Ebben a szakaszban ismertetünk figyelembe kell venni a Premium Storage használata a méretezhetőségi és teljesítménycéljai.

Premium storage-fiókok a következő teljesítménycélokat rendelkezik:

| Teljes számla kapacitás | Helyileg redundáns tárfiókok teljes sávszélesség |
| --- | --- | 
| Lemez kapacitása: 35 TB <br>Pillanatkép-kapacitás: 10 TB | Felfelé 50 Gigabit / másodperc a bejövő<sup>1</sup> + kimenő<sup>2</sup> |

<sup>1</sup> tárfiók küldött összes adatot (kérést)

<sup>2</sup> egy tárfiókból fogadott összes adatot (választ)

További információkért lásd: [Azure Storage méretezhetőségi és teljesítménycéljai](../articles/storage/common/storage-scalability-targets.md).

Ha a premium storage-fiókok nem felügyelt lemezeket használ, és az alkalmazás meghaladja a skálázhatósági célokat, az egy tárfiókban, érdemes migrálása felügyelt lemezekre. Ha nem szeretné migrálása felügyelt lemezekre, hozhat létre az alkalmazás több tárfiók használata. Ezt követően az adatok particionálása ezen a tárfiókon keresztül. Például, ha 51 TB-os lemezek csatolása több virtuális gép között elosztva őket két tárfiókot. 35 TB csak az egyetlen prémium szintű storage-fiókok. Győződjön meg arról, hogy egyetlen premium storage-fiók soha nem rendelkezik több mint 35 TB kiosztott lemezeket.

### <a name="premium-storage-disk-limits"></a>Prémium szintű Storage korlátok
Egy prémium szintű tárolólemez üzembe helyezésekor, a lemez mérete határozza meg, a maximális IOPS és átviteli sebesség (sávszélesség). Az Azure prémium szintű tárolólemezek típusai nyolc GA kínál: P4 (csak felügyelt lemezeket tartalmazó) P6 (csak felügyelt lemezeket tartalmazó) P10, p15-ös (felügyelt lemezek csak), P20, P30, P40 vagy P50. Valamint három előzetes lemezméretek: A P60 P70 és P80. Minden egyes prémium szintű tárolólemez-típusba IOPS és átviteli sebesség bizonyos korlátozások vonatkoznak. Az alábbi táblázat ismerteti a lemeztípusok korlátai:

Csillaggal szintben méretek jelenleg előzetes verzióban érhető el.

| Prémium szintű lemezek típusa  | P4    | P6    | P10    | P15    | P20    | P30              | P40             | P50             | P60 *            | P70 *               | P80 *               |
|---------------------|-------|-------|--------|--------|--------|------------------|-----------------|-----------------|-----------------|--------------------|--------------------|
| Lemezméret           | 32 GiB| 64 GiB| 128 GiB| 256 GiB| 512 GiB| 1024 GiB (1 TiB) | 2048 GiB (2 TiB)| 4095 GiB (4 TiB)| 8192 GiB (8 TiB)| 16384 giB (16 TiB)| 32 767 giB (32 TiB)|
| IOPS-érték lemezenként       | 120   | 240   | 500    | 1100   | 2300   | 5000             | 7500            | 7500            | 12 500          | 15 000             | 20,000             |
| Adattovábbítás lemezenként | 25 MB / s | 50 MB / s | 100 MB / s | 125 MB / s | 150 MB / s | 200 MB / s | 250 MB / s | 250 MB / s | 480 MB / s | 750 MB / s | 750 MB / s |

> [!NOTE]
> Ellenőrizze, hogy elegendő sávszélesség érhető el a virtuális gép meghajtó lemez forgalomra, leírtak szerint [támogatott virtuális gépek](#supported-vms). Ellenkező esetben a lemez adatátviteli sebessége és IOPS je omezeno alacsonyabb értékeket. Maximális átviteli sebesség és iops-t a VM-korlátok, nem pedig az előző táblázatban ismertetett korlátok alapján.  
> Az Azure Premium Storage platform, amely a nagymértékben párhuzamos van kialakítva. Az alkalmazás több szálon futó tervezése segíteni fog a nagy teljesítményű cél vehető igénybe a nagyobb lemezméretet.

Íme néhány fontos tudnivaló a Premium Storage méretezhetőségi és teljesítménycéljai kapcsolatban:

* **Kiosztott kapacitás és teljesítmény**

    Amikor üzembe helyez egy prémium szintű tárolólemez standard szintű tárolás esetén, garantáltan a kapacitás, IOPS és átviteli sebessége a lemezen. Ha például P50 lemez létrehozása, ha Azure látja el 4095 GB-os tárolási kapacitás, 7500 IOPS és 250 MB/s átviteli sebességet, hogy a lemez. Az alkalmazás használhatja a kapacitás és teljesítmény részét vagy egészét. Prémium szintű SSD-lemezeket úgy tervezték, hogy a cél teljesítmény 99,9 %-ában.

* **Lemezméret**

    Az Azure a legközelebbi prémium szintű tárlemezméretre kerekítünk, mint az előző szakaszban a táblázatban megadott képez le a lemez mérete (kerekítve). A lemez mérete 100 GB-os például egy P10 lehetőségként van besorolva. Műveleteket hajthat végre legfeljebb 500 IOPS, a legfeljebb 100 MB/s átviteli sebesség. Hasonlóképpen egy lemezt méretű 400 GB között P20 sorolja be a rendszer. Legfeljebb 150 MB/s átviteli sebességgel 2,300 IOPS ellátásához.

    > [!NOTE]
    > Könnyedén növelheti a meglévő lemezek mérete. Például érdemes 128 GB-os, vagy akár 1 TB-os 30 GB-os lemez méretének növeléséhez. Vagy előfordulhat, hogy szeretné a P20-as lemez konvertálása P30 lemez, mert szüksége további kapacitást vagy további IOPS és átviteli sebességet. 

* **I/o-mérete**

    Az i/o-mérete 256 KB. Ha az átvitt adatok kisebb, mint 256 KB-os, akkor számít 1 i/o-egységek. Nagyobb méretű i/o-méretek számítanak több i/o-méret 256 KB. Ha például 1100 KB i/o 5 i/o-egységek számítanak.

* **Átviteli sebesség**

    Az átviteli sebesség korlát a lemez írási műveletek tartalmazza, az azt lemez olvasási műveletek nem szolgálja ki a gyorsítótárból. Például egy P10 lemezt, lemezenként 100 MB/s átviteli. Néhány példa az érvényes átviteli P10 lemez az alábbi táblázatban láthatók:

    | P10 lemezenkénti maximális átviteli sebesség | Nem gyorsítótárazási beolvasása a lemezről | Nem gyorsítótárazási lemez írási műveletek |
    | --- | --- | --- |
    | 100 MB/s | 100 MB/s | 0 |
    | 100 MB/s | 0 | 100 MB/s |
    | 100 MB/s | 60 MB/s | 40 MB/s |

* **Gyorsítótár-találatok**

    Gyorsítótár-találatok hozzáférésüket nem korlátozza a kiosztott iops-érték vagy átviteli sebessége a lemezen. Például használhatja az adatlemez egy **ReadOnly** az ügyfélgyorsítótár beállítása a prémium szintű Storage, Olvasás a gyorsítótárból fájlnévkiterjesztései által támogatott virtuális gép nem vonatkozik az IOPS és átviteli sebesség caps a lemezen. Ha egy lemez a munkaterhelés túlnyomórészt szól, nagyon nagy átviteli sebességű kaphat. A gyorsítótár el külön IOPS és átviteli sebesség korlátok, a virtuális gép szint alapján a Virtuálisgép-méretet. DS sorozatú virtuális gépek körülbelül 4000 IOPS és a gyorsítótár és helyi SSD-i magonként 33 MB/s átviteli rendelkezik. A GS sorozatú virtuális gépek legfeljebb 5000 IOPS és a gyorsítótár és helyi SSD-i magonként 50 MB/s átviteli.

## <a name="throttling"></a>Throttling

Szabályozás fordulhatnak elő, ha az átviteli sebesség vagy az alkalmazás iops-érték meghaladja a lefoglalt korlátozásokat a prémium szintű storage lemezről. Szabályozás is okozhat, ha a lemez teljes forgalmat a virtuális gép összes lemezen meghaladja a lemez sávszélesség érhető el a virtuális gép számára. Szabályozás elkerülése azt javasoljuk, hogy a függőben lévő a lemez i/o-kérések számának korlátozásához. Használja a korlát a lemez ellátta méretezhetőségi és teljesítménycéljai, és a lemez a virtuális gép számára elérhető sávszélesség alapján.  

Az alkalmazás a legkisebb késés érhető el, ha arra tervezték, szabályozás elkerülése érdekében. Azonban, ha túl kicsi a függőben lévő a lemez i/o-kérések számát, az alkalmazás nem tudja kihasználni a maximális IOPS és átviteli sebességekhez a lemez számára elérhető.

Az alábbi példák bemutatják, hogyan szabályozási szint kiszámításához. Minden előzetes számítás-i/o-egység mérete 256 KB-os alapulnak.

### <a name="example-1"></a>1. példa

Az alkalmazás egy P10 lemez egy második feldolgozta a 16 KB-os méret 495 i/o-egységek. Az i/o-egységek számítanak 495 iops-t. Ha második próbál egy 2 MB-os i/o ugyanazon, az összes i/o-egységek egyenlő 495 + 8 iops-t. Ennek az az oka 2 MB méretű i/o = 2048 KB / 256 KB-os = 8 i/o-egységek, ha az i/o-egység mérete 256 KB. A 495 + 8 meghaladja az 500 IOPS-korlát a lemez, mert a szabályozás akkor fordul elő.

### <a name="example-2"></a>2. példa

Az alkalmazás P10 lemez mérete 256 KB-os, 400 i/o-egységek dolgozott fel. A teljes sávszélesség felhasznált (400 &#215; 256) / 1024 KB-os = 100 MB/s. P10 lemez 100 MB/s átviteli sebesség határral rendelkezik. Ha az alkalmazás további i/o-műveletek végrehajtása a második megpróbál, Tranzakciókorlát, mert túllépi a lefoglalt korlátot.

### <a name="example-3"></a>3. példa

DS4 virtuális gép a két P30 lemez csatolva van. Minden egyes P30 lemez 200 MB/s átviteli sebesség alkalmas állapotban. Viszont DS4 virtuális gép rendelkezik a lemez teljes 256 MB/s sávszélesség-kapacitást. Ön nem meghajtót, mindkét csatlakoztatott lemezek maximális átviteli sebesség a DS4 virtuális gépen egyszerre. A probléma megoldásához, képes elviselni forgalmat egy lemezen 200 MB/s és 56 MB/s a lemezen. Ha a lemez forgalom összege 256 MB/s keresztül haladnak, mely folyamatban van.

> [!NOTE]
> A lemez forgalom többnyire áll, kisméretű i/o-mérete, ha az alkalmazás valószínűleg eléri az átviteli sebességhatár előtt IOPS-korlátját. Azonban ha a lemez forgalom többnyire nagy i/o-méretek tartalmaz, valószínűleg az alkalmazás eléri az átviteli sebességhatár először helyett az IOPS-korlátját. Az alkalmazás IOPS és átviteli kapacitás maximalizálhatja a optimális i/o-méretek használatával. Emellett a függőben lévő lemez i/o-kérések számát korlátozhatja.

A nagy teljesítményű tervezésével kapcsolatos további tudnivalókért a Premium Storage segítségével tekintse meg [teljesítménybeli tervezés a Premium Storage-](../articles/virtual-machines/windows/premium-storage-performance.md).

## <a name="snapshots-and-copy-blob"></a>A pillanatképek és a Blob másolásához

A Storage szolgáltatásban a VHD-fájl egy lapblob. A lapblobok pillanatképeket készíthet, és másolja őket egy másik helyre, például egy másik tárfiókba.

### <a name="unmanaged-disks"></a>Nem felügyelt lemezek

Hozzon létre [növekményes pillanatképek](../articles/virtual-machines/linux/incremental-snapshots.md) a nem felügyelt prémium szintű lemezek pillanatképeket használhatja a standard szintű storage szolgáltatással azonos módon. A Premium Storage támogatja a következő replikálási beállítás csak a helyileg redundáns tárolás. Azt javasoljuk, hogy pillanatképeket létrehozni, és másolja a pillanatképek egy georedundáns standard szintű tárfiókot. További információkért lásd: [Azure Storage redundanciabeállításai](../articles/storage/common/storage-redundancy.md).

Ha egy lemezt egy virtuális Géphez van csatlakoztatva, bizonyos API-műveleteket a lemez használata nem engedélyezett. Például nem végezhető el egy [a Blob másolásához](/rest/api/storageservices/Copy-Blob) műveletet, hogy a blob, ha a lemezt egy virtuális Géphez van csatlakoztatva. Ehelyett először hozzon létre egy adott blob pillanatképe használatával a [Blob pillanatkép](/rest/api/storageservices/Snapshot-Blob) REST API-t. Ezután hajtson végre a [a Blob másolásához](/rest/api/storageservices/Copy-Blob) a pillanatkép másolása a csatlakoztatott lemezen. Másik lehetőségként válassza le a lemezt, és hajtsa végre a szükséges műveleteket.

Az alábbi korlátozások érvényesek a prémium szintű storage blob-pillanatfelvételek:

| Prémium szintű tárolási kapacitása | Érték |
| --- | --- |
| Blobonkénti pillanatképek maximális száma | 100 |
| A pillanatképek a tárfiókok kapacitásával<br>(Az adatok a pillanatképek csak tartalmazza. Nem tartalmaznak adatokat alap blob.) | 10 TB |
| Egymást követő-pillanatképek közötti minimális idő | 10 perc |

A pillanatképek georedundáns másolatait másolhatja a pillanatképek a premium storage-fiók georedundáns standard storage-fiókba az AzCopy és a Blob másolásához. További információkért lásd: [adatátvitel az AzCopy parancssori segédprogram](../articles/storage/common/storage-use-azcopy.md) és [a Blob másolásához](/rest/api/storageservices/Copy-Blob).

A premium storage-fiók ellen a lapblobok REST-műveleteinek végrehajtásáról részletes információkért lásd: [az Azure Premium Storage szolgáltatás Blobműveletei](https://go.microsoft.com/fwlink/?LinkId=521969).

### <a name="managed-disks"></a>Felügyelt lemezek

A felügyelt lemez pillanatképét az a felügyelt lemez csak olvasható példányát. A pillanatkép egy standard szintű felügyelt lemez van tárolva. Jelenleg [növekményes pillanatképek](../articles/virtual-machines/windows/incremental-snapshots.md) nem támogatja a felügyelt lemezeket. A felügyelt lemez pillanatképének elkészítéséhez, lásd: [, egy Azure által felügyelt tárolt VHD másolatának létrehozása lemez által felügyelt pillanatképekkel Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md) vagy [, egy Azure által felügyelt tárolt VHD másolatának létrehozása a felügyelt lemez a Linux pillanatképek](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md).

Ha egy felügyelt lemezt egy virtuális Géphez van csatlakoztatva, bizonyos API-műveleteket a lemez használata nem engedélyezett. Például nem hozható létre egy közös hozzáférésű jogosultságkód (SAS) a másolási műveletek végrehajtásához, amíg a lemezt egy virtuális Géphez van csatlakoztatva. Ehelyett először hozzon létre a lemez pillanatképét, és végezze el a pillanatkép másolatát. Azt is megteheti válassza le a lemezt, és ezután hozza létre az SAS a másolási művelet végrehajtásához.


## <a name="premium-storage-for-linux-vms"></a>Linux rendszerű virtuális gépek Premium Storage
Az alábbi információk segítségével segítségével állítsa be a Linux rendszerű virtuális gépek Premium Storage-ban:

A Premium Storage skálázhatósági célértékét beállítása minden prémium szintű tárolólemezeket a gyorsítótár eléréséhez **ReadOnly** vagy **None**, ha csatlakoztatja a fájlrendszer le kell tiltania "korlátok". Mivel a prémium szintű tárolólemezeket írási műveletek ezeket a gyorsítótár-beállításokat a tartós megszüntesse az ebben a forgatókönyvben nem szükséges. Ha az írási kérelem sikeresen befejeződik, adatok állandó tárolóján lett írva. "Korlátok" letiltásához használja a következő módszerek egyikét. A fájlrendszer közül választhat:
  
* A **reiserFS**, hogy tiltsa le a akadályozó tényezők, használja a `barrier=none` csatlakoztatási lehetőséget. (Korlátok engedélyezze az `barrier=flush`.)
* A **ext3/ext4**, hogy tiltsa le a akadályozó tényezők, használja a `barrier=0` csatlakoztatási lehetőséget. (Korlátok engedélyezze az `barrier=1`.)
* A **XFS**, hogy tiltsa le a akadályozó tényezők, használja a `nobarrier` csatlakoztatási lehetőséget. (Korlátok engedélyezze az `barrier`.)
* A premium storage gyorsítótár-lemezek beállítása **ReadWrite**, engedélyezze a korlátok írási tartósságot biztosítanak.
* A kötet címkéket a virtuális gép újraindítása után is fennállnak frissítenie kell az /etc/fstab a lemezek univerzálisan egyedi azonosítót (UUID) hivatkozik. További információkért lásd: [felügyelt lemez hozzáadása Linux rendszerű virtuális gép](../articles/virtual-machines/linux/add-disk.md).

Az Azure Premium Storage a következő Linux-disztribúciók ellenőrzése. A jobb teljesítmény és a Premium Storage stabilitását azt javasoljuk, frissítse a virtuális gépek ezen verziói legalább egyik (vagy újabb verzióra). A legújabb Linux Integration Services (LIS), 4.0, az Azure-hoz szükséges néhány verziójára. Töltse le, és a egy terjesztési telepítéséhez, az alábbi táblázatban szereplő hivatkozásra. Hogy képek hozzáadása a listához, hogy végezze el az érvényesítési. Vegye figyelembe, hogy az ellenőrzések megjelenítése, hogy a teljesítmény változhat az egyes lemezképek. Teljesítmény függ a számítási feladatok jellemzői, és a lemezkép-beállításokat. Különböző képek amelyek ideálisak a különböző típusú számítási feladatokat.

| Disztribúció | Verzió | Támogatott kernelverzióra | Részletek |
| --- | --- | --- | --- |
| Ubuntu | 12.04 | 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-server-20150119-en-us-30GB |
| Ubuntu | 14.04 | 3.13.0-44.73+ | Ubuntu-14_04_1-LTS-amd64-server-20150123-en-us-30GB |
| Debian | 7.x, 8.x | 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12| 3.12.36-38.1+| suse-sles-12-priority-v20150213 <br> suse-sles-12-v20150213 |
| SUSE | SLES 11 SP4 | 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+| 3.18.4+ | CoreOS 584.0.0 |
| CentOS | 6.5, 6.6, 6.7, 7.0 | &nbsp; | [Szükséges LIS4](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Vegye figyelembe a következő szakaszban talál.* |
| CentOS | 7.1+ | 3.10.0-229.1.2.el7+ | [Ajánlott LIS4](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Vegye figyelembe a következő szakaszban talál.* |
| Red Hat Enterprise Linux (RHEL) | 6.8+, 7.2+ | &nbsp; | &nbsp; |
| Oracle | 6.0+, 7.2+ | &nbsp; | UEK4 vagy RHCK |
| Oracle | 7.0-7.1 | &nbsp; | UEK4 vagy RHCK használatával[4.1 + LIS](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |
| Oracle | 6.4-6.7 | &nbsp; | UEK4 vagy RHCK használatával[4.1 + LIS](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |


### <a name="lis-drivers-for-openlogic-centos"></a>OpenLogic CentOS típusú LIS illesztőprogramok

OpenLogic CentOS virtuális gépek futnak, ha a következő paranccsal telepítse a legújabb illesztőprogramokat:

```
sudo rpm -e hypervkvpd  ## (Might return an error if not installed. That's OK.)
sudo yum install microsoft-hyper-v
```

Aktiválja az új illesztőprogramok, indítsa újra a virtuális Gépet.

## <a name="pricing-and-billing"></a>Árak és számlázás

A Premium Storage használata esetén az alábbi számlázási szempontok érvényesek:

* **Prémium szintű storage disk és a blob mérete**

    Egy prémium szintű tárolólemez-vagy blob számlázása a lemez-vagy blob kiépített méretétől függ. Az Azure a legközelebbi prémium szintű tárlemezméretre kerekítünk képez le a kiépítési méret (kerekítve). További információkért lásd: a tábla [méretezhetőségi és teljesítménycéljai](#scalability-and-performance-targets). Mindegyik lemez képez le egy támogatott kiosztott lemez méretét, és ennek megfelelően történik. Minden üzembe helyezett lemez használata óradíjas a Premium Storage-ajánlat a havi díjak használatával. Például ha üzembe helyezett egy P10 lemezt, és 20 óra múlva törli azt, számítjuk fel a P10 előfizetésért arányosan 20 óra. Ez a tényleges adatok írása a lemezt vagy az IOPS és a használt átviteli sebesség függetlenül.

* **Nem felügyelt lemezek prémium szintű pillanatképek**

    A prémium szintű unmanaged disks pillanatképek számlázása a további kapacitás, a pillanatképek által használt. A pillanatképek kapcsolatos további információkért lásd: [hozzon létre egy pillanatképet egy blobról](/rest/api/storageservices/Snapshot-Blob).

* **Prémium szintű felügyelt lemez-pillanatképek**

    Felügyelt lemez pillanatképét a lemez csak olvasható példányát. A lemez egy standard szintű felügyelt lemez van tárolva. Pillanatkép költségek ugyanaz, mint a standard szintű felügyelt lemez. Például ha egy 128 GB-os prémium szintű felügyelt lemez pillanatképét, a költség, a pillanatkép megegyezik egy 128 GB méretű standard szintű felügyelt lemez.  

* **Kimenő adatforgalom**

    [Kimenő adatforgalom](https://azure.microsoft.com/pricing/details/data-transfers/) (az Azure adatközpontok kimenő adatforgaloma adatok) díjak lépnek fel a sávszélesség-használat.

A Premium Storage, a Premium Storage virtuális gépek és a felügyelt lemezek díjszabása kapcsolatos részletes információkért tanulmányozza a következő cikkeket:

* [Az Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/)
* [Virtual Machines díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [A Managed disks díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="azure-backup-support"></a>Az Azure Backup-támogatás

A regionális vészhelyreállítás, biztonsági másolatot kell készíteni a Virtuálisgép-lemezek egy másik régióban használatával [Azure Backup](../articles/backup/backup-introduction-to-azure-backup.md) és a egy backup-tároló GRS társzolgáltatás fiókját.

Az idő-alapú biztonsági mentések, könnyű VM-helyreállítás és a biztonsági másolatok megőrzési házirendeket, használja az Azure Backup hozzon létre egy biztonsági mentési feladat. Nem felügyelt és felügyelt lemezek biztonsági mentés egyaránt használhatja. További információkért lásd: [nem felügyelt lemezekkel rendelkező virtuális gépek az Azure Backup](../articles/backup/backup-azure-vms-first-look-arm.md) és [felügyelt lemezekkel rendelkező virtuális gépek az Azure Backup](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup). 

## <a name="next-steps"></a>További lépések

Prémium szintű Storage szolgáltatással kapcsolatos további információkért tekintse meg a következő cikkeket.
