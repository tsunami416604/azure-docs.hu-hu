---
title: Az Azure Backup-architektúra
description: Az architektúra, összetevők és az Azure Backup szolgáltatás által használt folyamatok áttekintése.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: 4be483994bd7bc5bd97b1e59df230f66e9b4e24e
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430346"
---
# <a name="azure-backup-architecture"></a>Az Azure Backup-architektúra

Használhatja a [Azure Backup szolgáltatás](backup-overview.md) a biztonsági másolatokat a Microsoft Azure felhőbe. Ez a cikk összefoglalja az Azure Backup architektúrájának, összetevőket és folyamatokat. 


## <a name="what-does-azure-backup-do"></a>Mire használható az Azure Backup?

Az Azure Backup készít biztonsági adatokat, a gép állapota és a helyszíni gépek és Azure virtuális gépeken futó számítási feladatokat. Számos Azure Backup-forgatókönyvekhez.

## <a name="how-does-azure-backup-work"></a>Hogyan működik az Azure Backup?

Gépek és az adatok többféle készíthető.

- **A helyszíni gépek biztonsági mentése**:
    - A helyi Windows-számítógépek közvetlenül az Azure-bA az Azure Backup a Microsoft Azure Recovery Services (MARS) ügynök készíthető. Linux rendszerű gépek nem támogatottak.
    - A helyszíni gépek készíteni egy biztonsági mentési kiszolgálóra (a System Center Data Protection Manager (DPM) vagy a Microsoft Azure Backup Server (MABS)), és ezután viszont biztonsági mentése biztonsági mentési kiszolgálóra egy Azure biztonsági mentés a Recovery Services-tárolóba az Azure-ban.
- **Azure virtuális gépek biztonsági mentése**:
    - Biztonsági másolatot készíthet az Azure virtuális gépek közvetlenül. Az Azure Backup biztonsági mentési bővítményt az ehhez a virtuális gépen futó Azure-beli Virtuálisgép-ügynök telepítése. A teljes virtuális gép biztonsági mentését.
    - Is adott fájlok és mappák biztonsági mentése az Azure virtuális gépen a MARS-ügynök futtatásával.
    - A MABS Azure-ban futó Azure virtuális gépek biztonsági mentését, és ezután viszont biztonsági mentése MABS-tárolóba.

Tudjon meg többet [milyen biztonsági másolatot készíthet](backup-overview.md), és [támogatott biztonsági mentési forgatókönyveket](backup-support-matrix.md).


## <a name="where-is-data-backed-up"></a>Ha adatokat készül?

Az Azure Backup-tárolók biztonsági mentése Recovery Services-tárolóban lévő adatok. Egy tároló olyan online tárolóentitás az Azure-ban, amely adatok, például a biztonsági mentési szabályzatok, biztonsági másolatok és helyreállítási pontok tárolására szolgál.

- Tárolók megkönnyítik a biztonsági mentési adatok rendezése közben minimálisra csökkentik a munkaterhelést.
- Minden egyes Azure-előfizetésben legfeljebb 500 tároló hozható létre.
- Egy tároló, beleértve a helyszíni és Azure-beli virtuális gépek biztonsági másolati elem követheti nyomon.
- Kezelheti az Azure-ral a jelszótárolóhoz való hozzáféréshez [szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
- Azt adja meg, hogy a redundancia biztosítása érdekében a tárolóban lévő adatok replikálási módját:
    - **LRS**: Helyileg redundáns tárolás (LRS) segítségével védi az egy kínai adatközpont leállása ellen. LRS tárolóskálázási egységben replikálja az adatokat. [További információk](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs).
    - **GRS**: Georedundáns tárolás (GRS) is használhatja: Régióra kiterjedő szolgáltatáskimaradás nyújt védelmet. Egy másodlagos régióba replikálja azt az adatokat. [További információk](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs). 
    - Alapértelmezés szerint a biztonsági mentés a Recovery Services-tárolók GRS használják. 




### <a name="backup-agents"></a>Backup-ügynököket

Az Azure Backup biztonsági mentési típusától függően különböző ügynökök biztosít.

**Ügynök** | **Részletek** 
--- | --- 
**A Microsoft Azure Recovery Services-(MARS) ügynök** | (1.) az egyes helyszíni Windows-kiszolgálók fájlok, mappák és rendszerállapot biztonsági mentése fut<br/><br/> 2.) fut, fájlok, mappák és rendszerállapot biztonsági mentése Azure virtuális gépeken.<br/><br/>  3.) fut a DPM vagy MABS-kiszolgálók biztonsági mentése a DPM/MABS helyi tárolólemezt az Azure-bA. 
**Az Azure Virtuálisgép-bővítmény** | Készítsen róluk biztonsági másolatot egy tárolót az Azure virtuális gépeken fut.


## <a name="backup-types"></a>Biztonsági mentési típusok

**Biztonsági mentés típusa** | **Részletek** | **Használat**
--- | --- | ---
**Teljes** | Biztonsági másolat tartalmazza a teljes adatforrásra. Teljes biztonsági mentés nagyobb hálózati sávszélességet vesz igénybe. | Használja a kezdeti biztonsági mentés.
**Különbségi** |  A kezdeti teljes biztonsági mentés óta módosított blokkokat tárolja. Hálózati és tárolási kisebb mennyiségű használ, és nem tartja meg a változatlan adatok redundáns másolatait.<br/><br/> Nem hatékony, mert a későbbi biztonsági mentések között változatlanul adatblokkok átvitele és tárolt. | Az Azure Backup nem használja.
**Növekményes** | Magas tárolási és hálózati hatékonyságát. Csak az előző biztonsági mentés óta módosított adatok adatblokkokat tárolja. <br/><br/> Nincs szükség, a növekményes biztonsági mentés, nem ajánlatos a teljes biztonsági mentések kiegészítésére. | Lemezes biztonsági mentések DPM/MABS által használt, és használja az összes biztonsági mentés az Azure-bA.

## <a name="sql-server-backup-types"></a>Az SQL Server biztonsági mentési típusok

**Biztonsági mentés típusa** | **Részletek** | **Használat**
--- | --- | ---
**Teljes biztonsági mentés** | Teljes biztonsági mentés a teljes adatbázis biztonsági mentését. Az adatok egy adott adatbázisban, vagy fájlcsoport vagy fájlokat, és elég naplók az adatok helyreállítása egy készletét tartalmazza. | Legfeljebb napi egy teljes biztonsági mentés is indíthat.<br/><br/> Ha szeretné igénybe vehet egy teljes biztonsági mentés napi vagy heti időközönként.
**Különbségi biztonsági másolat** | Különbözeti biztonsági mentése a legújabb, az előző teljes biztonsági mentését alapul.<br/><br/> Csak a teljes biztonsági mentés óta megváltozott adatokat rögzíti. |  Legfeljebb napi egy különbségi biztonsági mentés is indíthat.<br/><br/> Az azonos napi egy teljes biztonsági mentés és a különbözeti biztonsági mentése nem konfigurálható.
**Tranzakciónapló biztonsági mentését** | A napló biztonsági mentését lehetővé teszi, hogy legfeljebb egy adott második időponthoz visszaállítást. | Egyszerre legfeljebb konfigurálhatja tranzakciós napló biztonsági mentés 15 percenként.


### <a name="comparison"></a>Összehasonlítás

A tárhelyhasználat, a helyreállítási időre vonatkozó célkitűzés (RTO), és a hálózatleterheltség eltér az egyes biztonsági mentés. Az alábbi képen egy biztonsági mentési típusok összehasonlítása.
- A adatforrás 10 tárolási áll blokkolja az A1 – a10-es, havonta készül.
- Az A2, az A3, az A4 és az A9 módosul az első hónapban, az A5 pedig a következő hónapban.
- A különbségi biztonsági másolatok a második hónapban a módosult blokkokat, A2, A3, A4 és a9-es készül biztonsági másolat. A harmadik hónapban a módosított A5 blokkal együtt biztonsági másolat készül ugyanezekről a blokkokról. A következő teljes biztonsági mentésig minden alkalommal készül biztonsági másolat a módosított blokkokról.
- A növekményes biztonsági mentések, miután elvégezte a teljes biztonsági mentést az első hónapban A2, A3, A4 és a9-es vannak jelölve, és továbbítva lesznek, a második hónapban. A harmadik hónapban csak az A5 módosított blokk van megjelölve és továbbítva. 

![a biztonsági mentési módszerek összehasonlítását mutató kép](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Biztonsági másolat szolgáltatásai

Az alábbi táblázat foglalja össze a különböző típusú biztonsági mentési szolgáltatások.

**Funkció** | **A helyi Windows-számítógépek (közvetlen)** | **Az Azure virtuális gépek** | **A DPM vagy MABS-gépek vagy alkalmazások**
--- | --- | --- | ---
Készítsen biztonsági másolatot a tárba | ![Igen][green] | ![Igen][green] | ![Igen][green] 
A DPM/MABS lemezt, majd az Azure Backup | | | ![Igen][green] 
A rendszer a biztonsági mentési adatok tömörítése | ![Igen][green] | Nem használ tömörítést adatátvitel során. Tárolási némileg növelve-e, de gyorsabb visszaállítást.  | ![Igen][green] 
A növekményes biztonsági mentés futtatása |![Igen][green] |![Igen][green] |![Igen][green] 
A deduplikált lemezek biztonsági mentése | | | ![Részlegesen][yellow]<br/><br/> A DPM vagy MABS-kiszolgálók csak helyszíni üzembe helyezve. 

![tábla kulcsa](./media/backup-architecture/table-key.png)





## <a name="architecture-direct-backup-of-azure-vms"></a>Architektúra: Közvetlen biztonsági mentést Azure-beli virtuális

1. Biztonsági mentésének engedélyezésekor egy Azure virtuális gép, egy biztonsági mentése fut, a megadott ütemterv szerint.
2. Az első biztonsági mentés során egy biztonsági mentési bővítményt telepítve van a virtuális Gépen fut, ha.
    - Windows virtuális gépek a VMSnapshot bővítmény telepítve van.
    - Linux rendszerű virtuális gépekhez a Linux a VMSnapshot-bővítmény telepítve van.
3. A bővítmény pillanatképet tárolási szinten. 
    - Windows virtuális gépeket futtató, a biztonsági mentés a virtuális gép alkalmazáskonzisztens pillanatképet a VSS koordinálja. Alapértelmezés szerint a biztonsági mentés teljes kötetárnyékmásolat vesz igénybe. Ha a biztonsági mentés nem tudja egy alkalmazáskonzisztens pillanatképet, majd vesz igénybe a fájlkonzisztens pillanatképek.
    - Linux rendszerű virtuális gépek biztonsági mentési vesz igénybe egy fájlkonzisztens biztonsági mentésre. Az alkalmazáskonzisztens pillanatképek kell manuálisan testre szabja az előkészítő/utólagos parancsfájl.
    - Biztonsági mentés minden egyes Virtuálisgép-lemez párhuzamos biztonsági mentésével optimalizáltuk. Az egyes lemezek biztonsági mentés alatt álló az Azure Backup beolvassa azokat az adatblokkokat a lemezen, és csak a módosított adatokat tárolja. 
4. Miután a pillanatképet készít, az adatok átkerülnek a tárolóba. 
    - Kizárólag a adatok kerülnek a legutóbbi biztonsági mentés óta módosított blokkokat.
    - Nem titkosítja az adatokat. Az Azure Backup is az Azure virtuális gépek biztonsági mentése az Azure Disk Encryption (ADE) használatával titkosított.
    - A pillanatkép adatainak előfordulhat, hogy nem kell azonnal-tárolóba másol. Ez órát is igénybe vehet bizonyos időszakokban. Egy virtuális gép teljes biztonsági mentés ideje lesz, kisebb a napi biztonsági mentési házirendeket, hogy 24 óra.
5. Után adatok a tárolóba, a rendszer eltávolítja a pillanatképet, és létrejön egy helyreállítási pont.

Vegye figyelembe, hogy az Azure virtuális gépek internet-hozzáférés szükséges parancsok. Ha a virtuális gép (például az SQL Server biztonsági másolat) számítási feladatok biztonsági, akkor az adatok is az internetkapcsolatra van szüksége. 

![Az Azure virtuális gépek biztonsági mentése](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders"></a>Architektúra: Közvetlen a helyszíni Windows gépek vagy az Azure virtuális gép fájlok és mappák biztonsági mentése

1. Állítsa be a forgatókönyv, akkor töltse le és a Microsoft Azure Recovery Services-(MARS) ügynök telepítése a gépre, jelölje ki Miről készüljön biztonsági másolat, mikor fog futni a biztonsági mentések és mennyi ideig azok fogja tárolni az Azure-ban.
2. A kezdeti biztonsági mentés futtatja a biztonsági mentési beállításoknak.
3. A MARS-ügynök a Windows kötet árnyékmásolata (VSS) szolgáltatás segítségével a biztonsági mentésre kiválasztott kötet időponthoz pillanatkép készítése.
    - A MARS-ügynök csak használja a Windows rendszer írni a pillanatképek rögzítése.
    - Az ügynök minden olyan alkalmazás VSS-írók nem használ, és így alkalmazáskonzisztens pillanatképek nem rögzíti.
3. Miután elvégezte a VSS-pillanatkép, a MARS-ügynök hoz létre egy virtuális Merevlemezt a gyorsítótármappa, ha konfigurálta a biztonsági mentés, a megadott és az egyes adatblokkok ellenőrzőösszegek tárolja. 
4. Növekményes biztonsági mentések futtatása megfelelően az ütemezést adja meg, kivéve, ha az ad hoc biztonsági mentés futtatása.
5. A növekményes biztonsági mentést azonosítja a megváltozott fájlokat, és létrejön egy új virtuális Merevlemezt. Azt tömörítve és titkosítva, és a tárolóba.
6. A növekményes biztonsági mentés befejezése után az új virtuális merevlemez van egyesítve lesznek a létrehozott virtuális Merevlemezt a kezdeti replikációt követően a folyamatban lévő biztonsági mentés összehasonlításhoz használható legújabb állapot megadása. 

![A MARS-ügynök a helyi Windows server biztonsági mentése](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Architektúra: Biztonsági mentés a DPM/MABS

1. Telepítse a DPM- vagy MABS védelmi ügynököt a védeni kívánt gépeket, és a gépeket ad hozzá egy a DPM védelmi csoportot.
    - A helyszíni gépek védelme érdekében a DPM- vagy MABS-kiszolgáló a helyszínen kell lennie.
    - Azure virtuális gépek védelme érdekében a MABS-kiszolgáló az Azure-on futó Azure virtuális gépként kell működnie.
    - A DPM/MABS használatával védheti biztonsági mentése a kötetek, megosztások, fájlokat és mappát. Gépek rendszer állam/operációs rendszer nélküli védelméhez, és meghatározott alkalmazások védelme az app-t támogató biztonsági mentési beállítások.
2. Ha gép vagy alkalmazást a DPM/MABS védelmet állít be, kiválaszthatja a MABS/DPM helyi lemezre rövid távú tárolás és az Azure-ba (online védelemmel) biztonsági mentése. Is megadhatja a biztonsági mentés a helyi DPM-vagy MABS storage kell futtatásakor, és az online biztonsági mentés az Azure-bA kell futtatásakor.
3. A lemez, a védett munkaterhelések biztonsági másolatot a helyi MABS/DPM lemezek, a megadott ütemterv szerint.
4. A DPM vagy MABS-lemezek biztonsági mentése a tároló által a MARS-ügynök fut a DPM vagy MABS-kiszolgálón.

![DPM- vagy MABS által védett gépek/számítási feladatok biztonsági mentése](./media/backup-architecture/architecture-dpm-mabs.png)







## <a name="azure-vm-storage"></a>Az Azure Virtuálisgép-tároló

- Az Azure virtuális gépek lemezeket használnak tárolják az operációs rendszer, az alkalmazások és az adatokat.
- Az Azure virtuális gépek rendelkeznek legalább két lemezt. Egy, az operációs rendszer, és a egy ideiglenes lemez. Az adatlemezeket az adatokat azok is lehet. Lemezek VHD-ként tárolja.
- Virtuális merevlemezeket lapblobként standard vagy prémium szintű tárfiókok az Azure-ban tárolják.
    - Standard szintű storage: Megbízható, költséghatékony lemeztámogatás, amelyek nem a késésre érzékeny számítási feladatokat futtató virtuális gépek számára. Standard szintű tárolást a standard SSD-lemez vagy HDD a standard szintű lemezek.
    - A Premium storage: Nagy teljesítményű lemezes támogatás. Prémium szintű SSD-lemezeket használ.
- Nincsenek lemezek különböző teljesítményszint:
    - Standard HDD lemez: Meghajtókra, és költséghatékony tárolási megoldás használják.
    - Standard SSD-lemez: Felügyelt lemezek prémium szintű SSD és HDD a standard szintű lemezek, egységes teljesítményéről és megbízhatóságáról, mint a HDD, de továbbra is költséghatékony megoldás elem egyesíti.
    - Prémium szintű SSD-lemez: SSD, nagy teljesítményű és kis késleltetést nyújtó i/o-intenzív számítási feladatokat futtató virtuális gépek esetén támogat.
- Kezelhetők vagy nem felügyelt lemezek:
    - Nem felügyelt lemezek: Virtuális gépek által használt lemezek hagyományos típusú. Ezeket a lemezeket a saját tárfiók létrehozásához, és adja meg a lemez létrehozásakor. Hogy, hogyan maximalizálhatja a tárolási erőforrások a virtuális gépek számára.
    - Felügyelt lemezek: Az Azure kezeli a létrehozása és kezelése a storage-fiókok az Ön számára. A lemez méretét és a teljesítmény szint ad meg, és az Azure létrehozza, -lemezek kezelése az Ön számára. Az Azure storage kezeli a virtuális gépek méretezése és lemezekkel bővíthetem a gépemet.

További információk:

- További információ a lemezes tárolás [Windows](../virtual-machines/windows/managed-disks-overview.md) és [Linux](../virtual-machines/linux/managed-disks-overview.md) virtuális gépeket.
- További információ az elérhető [lemeztípusok](../virtual-machines/windows/disks-types.md) például a standard és prémium.


### <a name="backing-up-and-restoring-azure-vms-with-premium-storage"></a>Biztonsági mentése és visszaállítása az Azure virtuális gépek a premium storage 

Készíthet biztonsági másolatot az Azure virtuális gépek premium Storage tárolást használ az Azure Backup szolgáltatással:

- Biztonsági mentésekor a premium storage virtuális gépek, a Backup szolgáltatás átmeneti előkészítési helyet, ennek a neve "AzureBackup-", a storage-fiókot hoz létre. Az előkészítési hely mérete megegyezik a helyreállítási pont pillanatképének méretével.
- Győződjön meg arról, hogy a premium storage-fiók rendelkezik-e elegendő szabad terület az átmeneti előkészítési hely. [További információk](../storage/common/storage-scalability-targets.md#premium-storage-account-scale-limits). Ne módosítsa az előkészítési helyet.
- A biztonsági mentési feladat befejezése után a rendszer törli az előkészítési helyet.
- Az előkészítési helyhez használt tároló ára összhangban az [premium storage szolgáltatás díjszabása](../virtual-machines/windows/disks-types.md#billing).

Azure virtuális gépek premium Storage tárolást használ visszaállításkor visszaállíthatja őket prémium vagy standard szintű storage. Költséghatékony standard visszaállítása, ha csak a virtuális gépről a fájlok egy alkészletére lehet, azonban általában akkor állítja vissza a prémium szintű.

### <a name="backing-up-and-restoring-managed-disks"></a>A felügyelt lemezek biztonsági mentése és visszaállítása

Azure virtuális gépek felügyelt lemezekkel rendelkező készíthető.
- Azt, hogy végrehajtja-e bármely más Azure virtuális Gépet ugyanúgy felügyelt lemezekkel rendelkező virtuális gépek biztonsági mentése. Biztonsági másolatot készíthet a virtuális gép közvetlenül, a virtuális gép beállításait, vagy engedélyezheti a virtuális gépek biztonsági mentés a Recovery Services-tároló.
- A felügyelt lemezeken található virtuális gépek biztonsági mentését a felügyelt lemezeken kiépített visszaállításipont-gyűjteményekkel végezheti el.
- Az Azure Backup is támogatja a felügyelt lemezes Azure Disk encryption (ADE) használatával titkosított virtuális gépek biztonsági mentését.

A felügyelt lemezekkel rendelkező virtuális gépek visszaállításakor visszaállíthatja a teljes virtuális gép felügyelt lemezekkel rendelkező, illetve egy storage-fiókot.
- Az Azure kezeli a felügyelt lemezek a visszaállítási folyamat során, és a storage-fiók lehetőséget, és a visszaállítás során létrehozott storage-fiók kezelése.
- Ha egy felügyelt virtuális gép titkosított, a virtuális gép kulcsok és titkos kulcsok ki kell lépnie a key vaultban a visszaállítási folyamat megkezdése előtt.




## <a name="next-steps"></a>További lépések

- [Felülvizsgálat](backup-support-matrix.md) támogatott szolgáltatások és a biztonsági mentési forgatókönyveket korlátozásai vonatkozó támogatási mátrixa.
- Állítsa be az egyik biztonsági mentés:
    - [Azure-beli virtuális gépek biztonsági mentése](backup-azure-arm-vms-prepare.md)
    - [Közvetlen Windows-gépek biztonsági mentése](tutorial-backup-windows-server-to-azure.md), anélkül, hogy egy biztonsági mentési kiszolgálóra.
    - [Állítsa be a MABS](backup-azure-microsoft-azure-backup.md) Azure adatforrásáig és vissza másolatot a MABS történő biztonsági mentés.
    - [Állítsa be a DPM](backup-azure-dpm-introduction.md) Azure adatforrásáig és vissza másolatot a dpm-hez történő biztonsági mentés.


[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png

