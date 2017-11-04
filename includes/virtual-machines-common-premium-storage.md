# <a name="high-performance-premium-storage-and-managed-disks-for-vms"></a>Prémium szintű Storage nagy teljesítményű és a virtuális gépek felügyelt lemezek
Prémium szintű Storage nagy teljesítményű, alacsony késésű támogatása a virtuális gépek (VM) továbbítja a bemeneti/kimeneti (I/O)-igényes munkaterhelések. Prémium szintű Storage használó Virtuálisgép-lemezek SSD-meghajtót (SSD) adatait tárolják. Hogy kihasználják a sebesség és a prémium szintű storage lemezek teljesítményét, prémium szintű Storage áttelepítheti a meglévő virtuális gépek lemezei.

Az Azure több premium tárolólemezek csatolhat egy virtuális géphez. Több lemez használatával biztosít az alkalmazások tárolási virtuális gépenként legfeljebb 256 TB. Prémium szintű Storage az alkalmazások 80000 másodpercenkénti I/O műveletek (IOPS) virtuális gépenként, és legfeljebb 2000 megabájt / másodperc (MB/s) virtuális gépenként lemez átviteli érhető el. Olvasási műveletek nagyon kis késleltetést biztosítanak.

Prémium szintű Storage Azure felajánlja a valóban növekedési-és-shift kibővített vállalati alkalmazások, például Dynamics AX, a Dynamics CRM-hez, a Exchange Server, a SAP Business Suite és a SharePoint-farmok a felhőbe. Teljesítmény-igényes adatbázis-terhelések alkalmazások, például az SQL Server, Oracle, MongoDB, MySQL és Redis, egységes magas teljesítménye és kis késleltetése igénylő futtathat.

> [!NOTE]
> Az alkalmazás a legjobb teljesítmény érdekében javasoljuk, hogy az áttelepített bármely virtuális gép lemezt, amely prémium szintű Storage magas iops értéket igényel. Ha a lemez nem igényli a magas iops értéket, lekapcsolva tartja szabványos Azure Storage segítségével korlát költségek. Standard szintű tárolást, a Virtuálisgép-lemez adatainak (merevlemezes HDD) meghajtók helyett SSD meghajtókon tárolják.
> 

Azure virtuális gépek prémium szintű storage lemezek létrehozásához két lehetőséget kínál:

* **Nem felügyelt lemezek**

    Az eredeti metódus nem kezelt lemezeket használni. Az egy nem felügyelt lemezt kezelhetők a storage-fiókok, amelyek a virtuális merevlemez (VHD) fájlok, amelyek megfelelnek a virtuális gépek lemezei tárolhatja. VHD-fájlokat, az Azure storage-fiókok lapblobokat tárolódnak. 

* **Felügyelt lemezek**

    Ha úgy dönt, [Azure felügyelt lemezek](../articles/virtual-machines/windows/managed-disks-overview.md), Azure kezeli a storage-fiókok, amelyek a virtuális gép lemezeit használja. Megadhatja, hogy a lemez típusát (prémium és Standard) és a mérete a lemez, amelyekre szüksége van. Azure hoz létre, és az Ön kezeli a lemezt. Nincs több tárfiókot biztosításához maradjon a méretezhetőségi korlátok a storage-fiókok helyezi el a lemezek foglalkoznia. Azure kezeli, amely meg.

Azt javasoljuk, hogy ezt a módot felügyelt lemezek, a számos szolgáltatás előnyeit.

Prémium szintű Storage, első lépésként [az ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/). 

Prémium szintű Storage a meglévő virtuális gépek áttelepítéssel kapcsolatos információkért lásd: [alakítsa át a Windows virtuális gép nem felügyelt lemezekből felügyelt](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md) vagy [alakítsa át a Linux virtuális gép nem felügyelt lemezekből felügyelt](../articles/virtual-machines/linux/convert-unmanaged-to-managed-disks.md).

> [!NOTE]
> Prémium szintű Storage a legtöbb régióban érhető el. Elérhető régiók listáját a [Azure-szolgáltatások régiónként](https://azure.microsoft.com/regions/#services), nézze meg a régiókban, ahol támogatott prémium szintű támogatási mérete sorozatú virtuális gépek (DS-méretek, DSV2-méretek, GS sorozatnak és Fs sorozatú virtuális gépek) támogatottak.
> 

## <a name="features"></a>Szolgáltatások

Az alábbiakban néhány, a prémium szintű Storage szolgáltatásait:

* **Prémium szintű storage-lemezekhez**

    Prémium szintű Storage támogatja a megadott méret sorozatú virtuális gépekhez csatolt virtuális gépek lemezei. Prémium szintű Storage támogatja a DS-méretek, DSv2-méretek, GS-méretek, Ls-sorozat és Fs sorozatú virtuális gépeket. Megválaszthatja, a hét lemezméret: P4 (32GB), P6 (64 GB-os), P10 (128GB), P20 (512GB), P30 (1024GB), P40 (2048GB), P50 (4095GB). P4 és P6 lemezméret csak még támogatottak felügyelt lemezek. Minden lemez mérete a saját teljesítmény specifikációi. Attól függően, hogy az alkalmazás követelményeinek csatolhat egy vagy több lemezt a virtuális Gépet. Azt ismerteti részletesen specifikációk [prémium szintű Storage méretezhetőségi és Teljesítménycélok](#scalability-and-performance-targets).

* **Prémium szintű lapblobokat**

    Prémium szintű Storage támogatja a lapblobokat. Állandó, nem felügyelt lemezek tárolására prémium szintű Storage a virtuális gépek lapblobokat használnak. Eltérően Azure standard szintű tárolást prémium szintű Storage nem támogatja a blokkblobokat, hozzáfűző blobokat, fájlok, táblák vagy várólisták. Prémium szintű lapblobokat támogatja P50 és P60 P10 a hat méretet (8191GiB). Virtuális gép lemezként csatolandó P60 prémium oldalakra vonatkozó blob nem támogatott. 

    Minden objektum, a prémium szintű tárfiók helyezett oldalakra vonatkozó blob lesz. Az oldalakra vonatkozó blob egy, a támogatott kiosztott méretű illesztése. Ezért a prémium szintű tárfiók nem célja, hogy apró blobok tárolására.

* **Prémium szintű storage-fiók**

    Prémium szintű Storage használatának megkezdéséhez a nem felügyelt lemezeket, prémium szintű storage-fiók létrehozása. Az a [Azure-portálon](https://portal.azure.com), a prémium szintű storage-fiók létrehozása, válassza ki a **prémium** teljesítményszinttel. Válassza ki a **helyileg redundáns tárolás (LRS)** replikációs beállítás. Is létrehozhat a prémium szintű tárfiók típusát értékre állításával **Premium_LRS** a következő helyek egyikére:
    * [Storage REST API felülete](https://docs.microsoft.com/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference) (2014-02-14-es vagy újabb verziója)
    * [Szolgáltatásfelügyelet REST API](http://msdn.microsoft.com/library/azure/ee460799.aspx) (2014-10-01 verzió vagy újabb verzióra, az Azure klasszikus üzembe helyezés)
    * [Az Azure Storage erőforrás szolgáltató REST API](https://docs.microsoft.com/rest/api/storagerp) (az Azure Resource Manager üzembe helyezések)
    * [Az Azure PowerShell](/powershell/azureps-cmdlets-docs.md) (0.8.10 verzió vagy újabb)

    További információ a prémium szintű tárfiókok korlátairól, lásd: [prémium szintű Storage méretezhetőségi és Teljesítménycélok](#premium-storage-scalability-and-performance-targets).

* **Prémium szintű helyileg redundáns tárolás**

    A prémium szintű tárfiók csak a helyileg redundáns tárolás támogatja a következő replikálási beállítás között. Helyileg redundáns tárolás három másolatot az adatok tartja egyetlen régión belül. Regionális katasztrófa utáni helyreállítás, biztonsági másolatot kell készíteni a virtuális gép lemezeit, egy másik régióban használatával [Azure biztonsági mentés](../articles/backup/backup-introduction-to-azure-backup.md). A georedundáns tárolás (GRS) fiókot és a biztonsági mentési tárolónak kell használnia. 

    Azure a tárfiók tárolóként a nem kezelt lemezeken használ. Ha hoz létre egy Azure DS-méretek, DSv2-méretek, GS-méretek, vagy Fs sorozatú virtuális gép, és nem felügyelt lemezeket, és válassza ki a prémium szintű storage-fiók, az operációs rendszer, és adatlemezek vannak tárolva, hogy a tárfiók.

## <a name="supported-vms"></a>Támogatott virtuális gépek
Prémium szintű Storage támogatja a DS-méretek, DSv2-méretek, GS-méretek, Ls-sorozat és Fs sorozatú virtuális gépeket. Az ilyen méretű standard és prémium szintű storage lemezek is használhatók. Nem használhat a prémium szintű storage lemezekhez Virtuálisgép-sorozat, amelyek nem prémium szintű Storage-kompatibilis.

További információ a Virtuálisgép-típusokon és a Windows Azure-méretek: [Windows Virtuálisgép-méretek](../articles/virtual-machines/windows/sizes.md). Linux virtuális gép típusát és méretét, az Azure-ban kapcsolatos információkért lásd: [Linux Virtuálisgép-méretek](../articles/virtual-machines/linux/sizes.md).

Íme néhány a DS-méretek, DSv2-méretek, GS-méretek, a szolgáltatásait Ls-sorozat és Fs sorozatú virtuális gépek:

* **A felhőalapú szolgáltatás**

    DS sorozatnak virtuális gépeket adhat hozzá egy felhőszolgáltatás, amely csak a DS sorozatnak virtuális gépeket tartalmaz. Ne vegyen fel a DS sorozatnak virtuális gépek egy meglévő felhőalapú szolgáltatást típustól eltérő, DS sorozatnak virtuális gépeket. Új felhőalapú szolgáltatás csak a DS sorozatnak virtuális gépeken futó áttelepítheti a meglévő VHD-k. Ha az új felhőalapú szolgáltatás, amelyen a DS sorozatnak virtuális gépekre, használja az ugyanazon virtuális IP-címet használni kívánt [fenntartott IP-cím](../articles/virtual-network/virtual-networks-instance-level-public-ip.md). GS sorozatnak virtuális gépeket lehet hozzáadni egy meglévő felhőalapú szolgáltatást csak a GS sorozatnak virtuális gépeket.

* **Operációsrendszer-lemez**

    A prémium szintű Storage virtuális Gépet állíthat be egy prémium szintű vagy egy szabványos operációsrendszer-lemez használatával. A legjobb használhatóság érdekében ajánlott prémium szintű Storage-alapú operációs rendszer lemezt használ.

* **Adatlemezek**

    Prémium szintű Storage azonos virtuális gép prémium és standard lemezek használható. Prémium szintű Storage a virtuális gép kiépítéséhez, és több állandó adatlemezt csatolni a virtuális Gépet. Szükség esetén kapacitást és teljesítményt a kötet, növelje meg a lemezeken is paritásos.

    > [!NOTE]
    > Ha a prémium szintű storage adatlemezek meg paritásos [tárolóhelyek](http://technet.microsoft.com/library/hh831739.aspx), állítsa be a tárolóhelyek az egyes lemezek használt 1 oszlop. Ellenkező esetben a csíkozott kötet általános teljesítménye lehet kisebb, mint a várt forgalom egyenetlen eloszlását miatt a lemezeken. Alapértelmezés szerint a Kiszolgálókezelőben állíthat be a lemezeket, legfeljebb 8 oszlopok. Ha több mint 8 lemez, a PowerShell segítségével hozza létre a kötetet. Adja meg manuálisan az oszlopok számát. Ellenkező esetben a kiszolgáló-kezelő felhasználói felületén továbbra is használja a 8 oszlopok, még akkor is, ha több lemezt rendelkezik. Ha egy egyetlen paritásos készlet 32 lemezre, például 32 oszlopok megadása. A virtuális lemezt használ, az oszlopok száma a [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) PowerShell-parancsmag használatát a *numberofcolumns tulajdonsághoz* paraméter. További információkért lásd: [tárolóhelyek – áttekintés](http://technet.microsoft.com/library/hh831739.aspx) és [tárolási tárolóhelyek – gyakori kérdések](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx).
    >
    > 

* **Gyorsítótár**

    A méret sorozat, amely támogatja a prémium szintű Storage virtuális gépek egyedi gyorsítótárazási lehetővé teszi a magas szintű teljesítményt és a késés rendelkezik. A gyorsítótárazási funkció meghaladja az alapul szolgáló prémium szintű tároló lemez teljesítménye. Beállíthatja, hogy a lemez gyorsítótárazási házirend prémium szintű storage lemezein **ReadOnly**, **ReadWrite**, vagy **nincs**. A gyorsítótárazási házirend alapértelmezett lemez **ReadOnly** minden prémium adatlemezekhez és **ReadWrite** operációsrendszer-lemezek. Az alkalmazás optimális teljesítmény érdekében használja a megfelelő gyorsítótár-beállítást. Például az olvasási műveleteket, vagy csak olvasható adatlemezek, például az SQL Server-adatfájlok, állítsa a gyorsítótárazási házirend a lemez **ReadOnly**. Írási műveleteket vagy a csak írható adatlemezek, például az SQL Server naplófájlokat, állítsa be a gyorsítótárazási házirend, hogy a lemez **nincs**. Prémium szintű Storage a Tervező optimalizálása kapcsolatos további információkért lásd: [terv teljesítmény a prémium szintű Storage](../articles/virtual-machines/windows/premium-storage-performance.md).

* **Elemzés**

    Virtuális gép Teljesítményelemzés lemezek használatával a prémium szintű Storage, kapcsolja be a Virtuálisgép-diagnosztika a [Azure-portálon](https://portal.azure.com). További információkért lásd: [Azure Diagnostics kiterjesztésű monitoring Azure virtuális gép](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/). 

    Lemez teljesítménye megtekintéséhez operációs rendszerű eszközök, például [Windows Teljesítményfigyelő](https://technet.microsoft.com/library/cc749249.aspx) Windows virtuális gépek és a [iostat](http://linux.die.net/man/1/iostat) Linux virtuális gépek parancsot.

* **VM skálázási korlátai és teljesítmény**

    Minden prémium szintű Storage-támogatott Virtuálisgép-méretet rendelkezik méretkorlátai és teljesítmény specifikációk IOPS, sávszélesség és a virtuális gépenként csatolható lemezek számát. Prémium szintű storage lemezek virtuális gépek használatakor győződjön meg arról, hogy nincs-e elegendő iops-érték és a virtuális Gépet, a meghajtó lemez forgalom sávszélességét.

    Például a STANDARD_DS1 virtuális gépek sávszélessége dedikált 32 MB/s a prémium szintű tároló lemez forgalmához. Egy P10 prémium szintű tároló lemez a 100 MB/s sávszélességet biztosít. Ha egy P10 prémium szintű tároló lemez a virtuális Géphez van csatolva, csak folytathatja legfeljebb 32 MB/s. A maximális 100 MB/s, amely a P10 lemez nyújt nem használható.

    A legnagyobb virtuális Gépet a DS-sorozat jelenleg a Standard_DS15_v2. A Standard_DS15_v2 legfeljebb 960 MB/s is biztosít az összes lemez. A GS sorozatnak legnagyobb virtuális gép a Standard_GS5. A Standard_GS5 legfeljebb 2000 MB/s is biztosít az összes lemez.

    Vegye figyelembe, hogy ezek a korlátozások csak a lemez forgalom. Ezek a korlátozások nem tartalmaznak, találatot eredményező gyorsítótárbeli kereséseinek és a hálózati forgalom. Egy külön sávszélesség érhető el virtuális gépek hálózati forgalmához. A hálózati forgalom a sávszélesség eltér a prémium szintű storage lemezek által használt dedikált sávszélesség.

    Prémium szintű Storage által támogatott virtuális gépek maximális iops-érték és átviteli sebesség (sávszélesség) kapcsolatos legfrissebb információk: [Windows Virtuálisgép-méretek](../articles/virtual-machines/windows/sizes.md) vagy [Linux Virtuálisgép-méretek](../articles/virtual-machines/linux/sizes.md).

    Prémium szintű storage lemezek és az iops-érték és az átviteli Sebességkorlát kapcsolatos további információkért lásd: a tábla a következő szakaszban.

## <a name="scalability-and-performance-targets"></a>Méretezhetőségi és teljesítménycélok
Ez a szakasz azt a prémium szintű Storage használatakor vegye figyelembe a méretezhetőségi és Teljesítménycélok írják le.

Prémium szintű storage-fiókok a következő méretezhetőségi célok rendelkezik:

| Teljes kapacitásával | A helyileg redundáns tárolás fiók teljes sávszélesség |
| --- | --- | 
| Lemez kapacitás: 35 TB <br>Pillanatkép-kapacitás: 10 TB | Másolatot 50 Gigabit / másodperc, a bejövő<sup>1</sup> + kimenő<sup>2</sup> |

<sup>1</sup> tárfiók küldött minden adat (kérelmek)

<sup>2</sup> tárfiók érkező összes adat (válasz)

További információkért lásd: [Azure Storage méretezhetőségi és Teljesítménycélok](../articles/storage/common/storage-scalability-targets.md).

Ha prémium szintű tárfiókok nem felügyelt lemezek használ, és az alkalmazás meghaladja a méretezhetőségi célok egyetlen tárfiók, előfordulhat, hogy az áttelepítést szeretné kezelt lemezre. Ha nem szeretné felügyelt lemezek áttelepítése, összeállítása a több tárfiókot használni kívánt alkalmazást. Ezt követően partícióazonosító az adatok adott tárfiókok között. Például ha azt szeretné, 51-TB lemezek között több virtuális gép csatlakoztatni, elosztva őket két storage-fiókok. 35 TB egy prémium szintű tárfiókok esetén a korlátot. Győződjön meg arról, hogy egy prémium szintű tárfiók soha nem legfeljebb 35 TB-nyi kiosztott lemezeket.

### <a name="premium-storage-disk-limits"></a>Prémium szintű Tárfiókok lemez korlátai
Ha a prémium szintű tároló lemez, a lemez mérete határozza meg, a maximális iops-érték és átviteli sebesség (sávszélesség). Az Azure támogatási tárolólemezek hét típusú kínál: P4 (kezelt lemezek csak), P6 (kezelt lemezek csak), P10, P20, P30, P40 és P50. Minden prémium típusú adott korlátai vannak a iops-érték és a teljesítményt. A lemez legyen kijelölve határértékei az alábbi táblázatban ismertetjük:

| Prémium szintű lemezek típusa  | P4    | P6    | P10   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Lemezméret           | 32 GB| 64 GB| 128 GB| 512 GB            | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPS-érték lemezenként       | 120   | 240   | 500   | 2300              | 5000              | 7500              | 7500              | 
| Adattovábbítás lemezenként | 25 MB / s  | 50 MB / s  | 100 MB / s | 150 MB / s | 200 MB / s | 250 MB / s | 250 MB / s | 

> [!NOTE]
> Győződjön meg arról, hogy elegendő sávszélesség érhető el a virtuális Gépet, a meghajtó lemez forgalom, lásd: [prémium szintű Storage által támogatott virtuális gépek](#premium-storage-supported-vms). Ellenkező esetben a lemez adatátviteli sebessége és IOPS értékek csökkenthető van korlátozva. Maximális átviteli sebesség és IOPS VM használati korlátait, nem az előző táblázatban ismertetett lemez korlátokat alapul.  
> 
> 

Az alábbiakban néhány fontos dolgot tudnia a prémium szintű Storage méretezhetőségi és Teljesítménycélok:

* **Kiosztott kapacitást és teljesítményt**

    Amikor egy prémium szintű tároló lemez, Standard szintű tárolást, ellentétben a kapacitás, IOPS és átviteli sebessége a lemezen levő kézbesítése garantáltan. Például ha P50 lemez létrehozása, Azure kiépítését 4095 GB-os tárolási kapacitás, 7500 IOPS és 250 MB/s átviteli az, hogy a lemez. Az alkalmazás használhat egyetlen kapacitást és teljesítményt részét sem.

* **Lemez mérete**

    Azure leképezve a legközelebbi prémium szintű tároló lemez lehetőséget, a táblának az előző szakaszban meghatározottak szerint a lemez mérete (kerekítve). Például a lemez mérete 100 GB-os besorolásának P10 beállításként. Legfeljebb 500 IOPS, akár 100 MB/s átviteli sebességgel műveleteket hajthat végre. Hasonlóképpen ekkora lemezt 400 GB egy P20 lesz minősítve. Legfeljebb 2,300 IOPS 150 MB/s átviteli sebességgel műveleteket hajthat végre.
    
    > [!NOTE]
    > Könnyen növelhető a meglévő lemezek mérete. Például érdemes 128 GB-os, vagy akár 1 TB-os 30 GB-os lemez méretének növelése céljából. Vagy előfordulhat, hogy szeretné a P20 lemez konvertálása P30 lemezt, mert további kapacitást vagy további IOPS és átviteli van szükség. 
    > 
 
* **I/o-mérete**

    Az i/o mérete 256 KB. Ha az átvitt adatok kisebb, mint 256 KB, ez 1 i/o-egység tekintendő. Nagyobb i/o-méretek számlálási több i/o mérete 256 KB. Például 1100 KB i/o 5 i/o-egységek számítanak.

* **Átviteli sebesség**

    Az átviteli sebesség korlátot a lemezre írás tartalmazza, és azt lemez olvasási műveletek nem kiszolgálása a gyorsítótárból. Például egy P10 lemez rendelkezik 100 MB/s átviteli lemezenként. Néhány példa az érvényes P10 lemez adatátviteli sebességét az alábbi táblázatban láthatók:

    | Maximális átviteli sebesség P10 lemezenként | Nem-gyorsítótár beolvasása a lemezről | Nem-gyorsítótár lemez írási műveletek |
    | --- | --- | --- |
    | 100 MB/s | 100 MB/s | 0 |
    | 100 MB/s | 0 | 100 MB/s |
    | 100 MB/s | 60 MB/s | 40 MB/s |

* **Találatot eredményező gyorsítótárbeli kereséseinek**

    A lefoglalt IOPS vagy a lemez átviteli találatot eredményező gyorsítótárbeli kereséseinek nincs korlátozva. Például azonosítójú adatlemez használatakor egy **ReadOnly** gyorsítótár beállítása a prémium szintű Storage, a gyorsítótárból szolgáltatott olvasások által támogatott virtuális gép nem tartoznak az iops-érték és a teljesítményt biztosít, amelyet a lemezen. Ha egy lemez munkaterhelés túlnyomórészt tesz az olvasási és nagyon nagy átviteli kapacitás kaphat. A gyorsítótár az külön IOPS és átviteli sebességének korlátai, a virtuális gép szintjén, a virtuális gép mérete alapján. DS sorozatnak virtuális gépek körülbelül 4000 iops-érték és a központi gyorsítótár és a helyi SSD i/o-/ 33 MB/s átviteli rendelkezik. GS sorozatnak virtuális gépek legfeljebb 5000 iops teljesítményt és a központi gyorsítótár és a helyi SSD i/o-/ 50 MB/s átviteli rendelkezik. 

## <a name="throttling"></a>Szabályozás
Sávszélesség-szabályozás fordulhatnak elő, ha az alkalmazás IOPS vagy átviteli meghaladja a prémium szintű storage lemezként lefoglalt korlátok. Sávszélesség-szabályozás is akkor fordulhat elő, ha a lemez teljes forgalom a virtuális Gépen lévő összes lemezeken túllépi ezt a lemezt sávszélesség érhető el a virtuális gép számára. Sávszélesség-szabályozás elkerülése azt javasoljuk, hogy korlátozza-e a függőben lévő a lemez i/o-kérelmek számát. Használja a határértéket, a lemez ellátta méretezhetőségi és Teljesítménycélok, és a lemez sávszélesség álljon rendelkezésre a virtuális gép alapján.  

Az alkalmazás érhető el a legkisebb mértékű késleltetést, ha úgy van kialakítva, hogy elkerülheti a sávszélesség-szabályozás. Azonban ha függőben lévő a lemez i/o-kérelmek száma túl kicsi, az alkalmazás nem használja fel a maximális IOPS és átviteli sebességekhez, a lemez számára elérhető.

Az alábbi példák bemutatják, hogyan lehet szabályozási szintek kiszámításához. Minden számítás egy i/o foglalásiegység-méret 256 KB-os alapulnak.

### <a name="example-1"></a>1. példa
Az alkalmazás egy második P10 lemezen feldolgozott 495 i/o-egység 16 KB-os méret. Az i/o-egységek számítanak 495 iops-érték. Ha a második kísérli meg egy 2 MB i/o ugyanazon, i/o-egységek az összes 495 + 8 gyakoriságnál. Ennek az az oka 2 MB i/o = 2048 KB / 256 KB-os = 8 i/o-egység, ha az i/o-egység mérete 256 KB. 495 + 8 hosszúsága meghaladja a 500 IOPS-korláttal lemezként, mert a sávszélesség-szabályozás akkor következik be.

### <a name="example-2"></a>2. példa
Az alkalmazás feldolgozott 400 i/o-egység P10 lemezen 256 KB-os méret. A teljes sávszélesség felhasznált (400 &#215; 256) / 1 024 KB = 100 MB/s. Egy P10 lemez van 100 MB/s átviteli sebesség korlátozva. Ha az alkalmazás próbálja a második több i/o-műveletet hajt végre, mert az nagyobb a lefoglalt maximális van szabályozva.

### <a name="example-3"></a>3. példa
DS4 virtuális gép csatlakoztatott két P30 lemezzel rendelkezik. Minden egyes P30 lemez képes a 200 MB/s átviteli sebesség. DS4 virtuális gép azonban a teljes sávszélesség lemezkapacitás 256 MB/s van. Ön nem meghajtója mindkét csatlakoztatott lemezek maximális átviteli DS4 virtuális Gépet egy időben. A probléma megoldásához, 200 MB/s egy lemezen és 56 MB/s a lemezen forgalmát képes elviselni. Ha a lemez forgalom összege 256 MB/s keresztül kerül, a lemez forgalom folyamatban van.

> [!NOTE]
> Ha a lemez forgalom legtöbbször a kis i/o-méretek, az alkalmazás valószínűleg elért átviteli sebesség előbb IOPS-korláttal. Azonban ha a lemez forgalom leginkább a nagy i/o-méretek, az alkalmazás valószínűleg elért átviteli sebesség korlát először az IOPS-korláttal helyett. Optimális i/o-méretek segítségével maximalizálhatja az alkalmazás IOPS és átviteli sebesség. Emellett korlátozhatja a függőben lévő lemez i/o-kérelmek számát.
> 

További információk a magas teljesítmény designing prémium szintű Storage használatával kapcsolatban lásd: [terv teljesítmény a prémium szintű Storage](../articles/virtual-machines/windows/premium-storage-performance.md).

## <a name="snapshots-and-copy-blob"></a>A pillanatképek és a Blob másolása

A VHD-fájlt a társzolgáltatás nem oldalakra vonatkozó blob. Pillanatképek készítése a lapblobokat, és másolja őket egy másik helyre, például egy másik tárfiókhoz.

### <a name="unmanaged-disks"></a>Nem felügyelt lemezek

Hozzon létre [növekményes pillanatképek](../articles/virtual-machines/linux/incremental-snapshots.md) a nem felügyelt premium lemezek, a megszokott módon teszi lehetővé a pillanatképek standard szintű tárolóval. Prémium szintű Storage támogatja a következő replikálási beállítás csak a helyileg redundáns tárolás. Azt javasoljuk, hogy pillanatképeket, és másolja a pillanatképek egy standard georedundáns tárfiókot. További információkért lásd: [Azure Storage redundancia beállítások](../articles/storage/common/storage-redundancy.md).

Egy lemezt a virtuális Géphez van csatolva, ha a lemezen lévő egyes API műveletek nem engedélyezettek. Például nem hajtható végre egy [másolási Blob](/rest/api/storageservices/Copy-Blob) , hogy a blob, ha a lemez csatolva van egy virtuális Gépen a műveletet. Ehelyett először létre kell hoznia egy pillanatképet, hogy a blob használatával a [pillanatkép Blob](/rest/api/storageservices/Snapshot-Blob) REST API-t. Ezután hajtson végre a [másolási Blob](/rest/api/storageservices/Copy-Blob) másolni a csatlakoztatott lemez a pillanatkép. Alternatív megoldásként válassza le a lemezt, és végezze el a szükséges műveleteket.

Prémium szintű storage-blob pillanatfelvételekkel a következő korlátozások vonatkoznak:

| Prémium szintű tárolási kapacitása | Érték |
| --- | --- |
| Egy blob pillanatképek maximális száma | 100 |
| A pillanatképek tárfiókok kapacitásával<br>(Csak a pillanatképek adatokat tartalmazza. Nem tartalmaz adatokat alap BLOB.) | 10 TB |
| Egymást követő pillanatképek közötti minimális időtartam | 10 perc |

A pillanatképek georedundáns másolatait átmásolhatja pillanatképek a prémium szintű storage-fiók egy standard georedundáns tárfiókot az AzCopy vagy a Blob másolása. További információkért lásd: [adatátvitel az AzCopy parancssori segédprogram](../articles/storage/common/storage-use-azcopy.md) és [másolási Blob](/rest/api/storageservices/Copy-Blob).

A prémium szintű storage-fiók ellen lapblobokat REST műveleteinek kapcsolatos részletes információkért lásd: [szolgáltatási műveletek prémium szintű Azure Storage Blob](http://go.microsoft.com/fwlink/?LinkId=521969).

### <a name="managed-disks"></a>Felügyelt lemezek

A felügyelt lemezes pillanatképet a felügyelt lemezes csak olvasható másolatát. A pillanatkép egy standard szintű felügyelt lemezes tárolja. Jelenleg [növekményes pillanatképek](../articles/virtual-machines/windows/incremental-snapshots.md) felügyelt lemezek használata nem támogatott. A felügyelt lemezként pillanatképének elkészítéséhez, lásd: [egy Azure kezelt állapotúként tárolt virtuális merevlemez másolatának létrehozása lemez által felügyelt pillanatképek használata a Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md) vagy [egy Azure kezelt állapotúként tárolt virtuális merevlemez másolatának létrehozása lemez használatával felügyelt pillanatfelvételek Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md).

Ha egy felügyelt lemezt egy virtuális Géphez van csatolva, a lemezen lévő egyes API műveletek nem engedélyezettek. Például egy közös hozzáférésű jogosultságkód (SAS), a másolási műveletek végrehajtásához, amíg a lemez csatolva van egy virtuális gép nem hozható létre. Ehelyett először hozzon létre egy pillanatképet a lemezen, és végezze el a pillanatkép példányát. Alternatív megoldásként válassza le a lemezt, és előállít egy SAS-t, a másolási művelet végrehajtására.


## <a name="premium-storage-for-linux-vms"></a>Prémium szintű Storage Linux virtuális gépekhez
Az alábbi információk segítségével a Linux virtuális gépek a prémium szintű Storage beállításához:

Prémium szintű Storage, a méretezhetőségi célok eléréséhez, az összes prémium tárolólemezek gyorsítótárával állítsa **ReadOnly** vagy **nincs**, le kell tiltani "korlátok", a fájlrendszer csatlakoztatásakor. Ebben a forgatókönyvben korlátok nem szükséges, mivel az írási műveleteket ad ki premium tárolólemezek tartós vonatkozó beállítások. Amikor az írási kérelem sikeresen befejeződik, a program az adatok bejegyzésre az állandó tároló. "Korlátok" letiltásához használja a következő módszerek egyikét. A fájlrendszer közül választhat:
  
* A **reiserFS**, az korlátok letiltásához használja a `barrier=none` csatlakoztatási lehetőséget. (Korlátok engedélyezéséhez az `barrier=flush`.)
* A **ext3/ext4**, az korlátok letiltásához használja a `barrier=0` csatlakoztatási lehetőséget. (Korlátok engedélyezéséhez az `barrier=1`.)
* A **XFS**, az korlátok letiltásához használja a `nobarrier` csatlakoztatási lehetőséget. (Korlátok engedélyezéséhez az `barrier`.)
* Prémium szintű storage a lemezek, amelyek gyorsítótár be **ReadWrite**, engedélyezze az írási tartóssága korlátok.
* A kötet feliratainak megőrzéséhez a virtuális gép újraindítása után frissítenie kell az /etc/fstab a lemezek univerzálisan egyedi azonosítóval (UUID) hivatkozik. További információkért lásd: [Linux virtuális gép hozzáadása egy felügyelt lemezes](../articles/virtual-machines/linux/add-disk.md).

A következő Linux terjesztésekről érvényesítése a prémium szintű Azure Storage. A jobb teljesítmény és stabilitását prémium szintű Storage azt javasoljuk, hogy verzió, legalább egy (vagy újabb verzióra) frissítése a virtuális gépek. A legújabb Linux integrációs szolgáltatások (LIS), v4.0, az Azure-szükséges néhány verziójára. Töltse le, és egy terjesztési telepítése, az alábbi táblázatban szereplő hivatkozásra. A Microsoft lemezképek hozzáadása a listához, azt végezze el az érvényesítés. Vegye figyelembe, hogy az érvényesítést megjelenítése, hogy a teljesítmény változhat az egyes lemezképek. Teljesítmény a munkaterhelés jellemzőit és a lemezkép beállításainak függ. Különböző képek hangolt különböző típusú munkaterhelések.

| Disztribúció | Verzió | Támogatott kernel | Részletek |
| --- | --- | --- | --- |
| Ubuntu | 12.04 | 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-Server-20150119-en-us-30GB |
| Ubuntu | 14.04 | 3.13.0-44.73+ | Ubuntu-14_04_1-LTS-amd64-Server-20150123-en-us-30GB |
| Debian | 7.x, 8.x | 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12| 3.12.36-38.1+| SUSE-sles-12-prioritás-v20150213 <br> SUSE-sles-12-v20150213 |
| SUSE | SLES 11 SP4 | 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+| 3.18.4+ | CoreOS 584.0.0 |
| CentOS | 6.5, 6.6, 6.7, 7.0 | &nbsp; | [Szükséges LIS4](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Megjegyzés: a következő szakaszban talál* |
| CentOS | 7.1+ | 3.10.0-229.1.2.el7+ | [Ajánlott LIS4](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Megjegyzés: a következő szakaszban talál* |
| Red Hat Enterprise Linux (RHEL) | 6.8+, 7.2+ | &nbsp; | &nbsp; |
| Oracle | 6.0+, 7.2+ | &nbsp; | UEK4 vagy RHCK |
| Oracle | 7.0-7.1 | &nbsp; | UEK4 vagy RHCK keresztüli rendelkező[LIS 4.1 +](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |
| Oracle | 6.4-6.7 | &nbsp; | UEK4 vagy RHCK keresztüli rendelkező[LIS 4.1 +](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |


### <a name="lis-drivers-for-openlogic-centos"></a>OpenLogic CentOS LIS illesztőprogramok

OpenLogic CentOS virtuális gépek futnak, ha a következő paranccsal telepítse a legújabb illesztőprogramokat:

```
sudo rpm -e hypervkvpd  ## (Might return an error if not installed. That's OK.)
sudo yum install microsoft-hyper-v
```

Aktiválja az új illesztőprogramok, indítsa újra a számítógépet.

## <a name="pricing-and-billing"></a>Árak és számlázás

Prémium szintű Storage használata esetén az alábbi számlázási szempontok érvényesek:

* **Prémium szintű tároló lemez és a blob mérete**

    Prémium szintű tároló lemez vagy blob számlázási kiosztott mérete a lemez vagy a blob függ. Azure a kiépített mérete (kerekítve) van leképezve a legközelebbi prémium szintű tároló lemez lehetőséget. További információkért lásd: a táblázatban szereplő [prémium szintű Storage méretezhetőségi és Teljesítménycélok](#premium-storage-scalability-and-performance-targets). Egyes lemezek egy támogatott kiépített lemez méretének van leképezve, és ennek megfelelően van-e terhelve. A kiépített lemez számlázási van arányosítva óránként, a prémium szintű Storage-szolgáltatásokat a havi díjakon használatával. Például ha kiépített P10 lemezt, és 20 óra múlva törli azt, kell fizetni az az P10 elérhető arányosítva 20 óra. Ettől függetlenül a tényleges adatok írása a lemezre vagy az iops-érték és a használt átviteli sebesség mennyisége.

* **Prémium szintű nem felügyelt lemezek pillanatképek**

    Nem felügyelt premium lemezek pillanatfelvételeket számlázása a további kapacitást, a pillanatképek használják. A pillanatképek kapcsolatos további információkért lásd: [pillanatkép létrehozása a blob](/rest/api/storageservices/Snapshot-Blob).

* **Prémium szintű lemezek pillanatképek felügyelt**

    Felügyelt lemezes pillanatképet a lemezen csak olvasható másolatát. Standard szintű felügyelt lemezes a lemezen tárolja. Pillanatkép költségek azonos szabványos lemez kezeli. Például ha egy pillanatképet, 128 GB-os prémium szintű felügyelt lemezes, a költség, a pillanatkép megegyezik a 128 GB-os standard szintű felügyelt lemezes.  

* **Kimenő adatátvitel**

    [Kimenő adatátvitel](https://azure.microsoft.com/pricing/details/data-transfers/) (adatok kilépő Azure adatközpontjaiban) gigabájtalapú sávszélesség-használat.

Prémium szintű Storage, a prémium szintű Storage által támogatott virtuális gépek és a felügyelt lemezek árazással kapcsolatos részletes információkért lásd: ezek a cikkek:

* [Az Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/)
* [Virtuális gépek díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Felügyelt lemezek díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="azure-backup-support"></a>Az Azure Backup-támogatás 

Regionális katasztrófa utáni helyreállítás, biztonsági másolatot kell készíteni a virtuális gép lemezeit, egy másik régióban használatával [Azure biztonsági mentési](../articles/backup/backup-introduction-to-azure-backup.md) és egy biztonsági mentési tárolóval Georedundáns tárfiókot.

Segítségével hozhat létre egy biztonsági mentési feladat időalapú biztonsági könnyű VM-helyreállítás és biztonsági mentési adatmegőrzési, Azure Backup szolgáltatással. Nem felügyelt és a felügyelt biztonsági mentés is használható. További információkért lásd: [Azure biztonsági mentés nem felügyelt lemezzel rendelkező virtuális gépek](../articles/backup/backup-azure-vms-first-look-arm.md) és [felügyelt lemezzel rendelkező virtuális gépek Azure Backup](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup). 

## <a name="next-steps"></a>Következő lépések
Prémium szintű Storage kapcsolatos további információkért tekintse meg a következő cikkekben talál.

### <a name="design-and-implement-with-premium-storage"></a>Tervezési és a prémium szintű Storage végrehajtása
* [A prémium szintű Storage teljesítmény tervezése](../articles/virtual-machines/windows/premium-storage-performance.md)
* [Prémium szintű Storage BLOB storage műveletek](http://go.microsoft.com/fwlink/?LinkId=521969)

### <a name="operational-guidance"></a>Műveleti útmutató
* [Prémium szintű Azure-tárhelyre végzi az áttelepítést](../articles/storage/common/storage-migration-to-premium-storage.md)

### <a name="blog-posts"></a>Blogbejegyzések
* [Prémium szintű Storage általánosan elérhető](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/)
* [A GS sorozatnak bejelentése: prémium szintű Storage hozzáadása a támogatási szolgálathoz a legnagyobb virtuális gépeket a nyilvános felhőben](https://azure.microsoft.com/blog/azure-has-the-most-powerful-vms-in-the-public-cloud/)
