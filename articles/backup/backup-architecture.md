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
ms.openlocfilehash: 98ffe145103b4be04014627ed04d04dcf7542015
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368953"
---
# <a name="azure-backup-architecture"></a>Az Azure Backup-architektúra

Használhatja a [Azure Backup szolgáltatás](backup-overview.md) az adatok biztonsági mentésére a Microsoft Azure felhőplatformon. Ez a cikk összefoglalja az Azure Backup architektúrájának, összetevőket és folyamatokat. 

## <a name="what-does-azure-backup-do"></a>Mire használható az Azure Backup?

Az Azure Backup biztonsági mentést készít az adatok, gép állapotát, és a helyszíni gépek és Azure virtuális gép (VM) instances futó számítási feladatokat. Számos Azure Backup-forgatókönyvekhez.

## <a name="how-does-azure-backup-work"></a>Hogyan működik az Azure Backup?

Biztonsági másolatot készíthet gépek és az adatok többféle használatával:

- **A helyszíni gépek biztonsági mentése**:
    - Akkor is készítsen biztonsági másolatot a helyi Windows-számítógépek közvetlenül az Azure-bA az Azure Backup a Microsoft Azure Recovery Services (MARS) ügynök használatával. Linux rendszerű gépek nem támogatottak.
    - A helyszíni gépek készíthető biztonsági mentési kiszolgálóra (a System Center Data Protection Manager (DPM) vagy a Microsoft Azure Backup Server (MABS)). Majd a biztonsági mentési-kiszolgálóról biztonsági mentést a Recovery Services-tárolót az Azure-ban.

- **Azure virtuális gépek biztonsági mentése**:
    - Biztonsági másolatot készíthet az Azure virtuális gépek közvetlenül. Az Azure Backup biztonsági mentési bővítményt telepíti az Azure-beli Virtuálisgép-ügynök, amely a virtuális gép fut-e. Ez a bővítmény a teljes virtuális gép biztonsági mentését.
    - Is adott fájlok és mappák biztonsági mentése az Azure virtuális gépen a MARS-ügynök futtatásával.
    - Biztonsági másolatot készíthet Azure virtuális gépek, a MABS, amely az Azure-ban fut, és a egy Recovery Services-tárolót, majd készíthető a MABS.

Tudjon meg többet [milyen biztonsági másolatot készíthet](backup-overview.md) és [támogatott biztonsági mentési forgatókönyveket](backup-support-matrix.md).

## <a name="where-is-data-backed-up"></a>Ha adatokat készül?

Az Azure Backup biztonsági másolatot a Recovery Services-tároló tárolja. A tároló egy olyan online tárhelyen entitás, az Azure-ban, amely adatok, például a biztonsági mentési szabályzatok, biztonsági másolatok és helyreállítási pontok tárolására szolgál.

Recovery Services-tárolók a következő jellemzőkkel rendelkeznek:

- Tárolók megkönnyítik a biztonsági mentési adatok rendezése közben minimálisra csökkentik a munkaterhelést.
- Minden egyes Azure-előfizetésben legfeljebb 500 tároló hozható létre.
- Egy tároló tartalmazza, beleértve a helyszíni és Azure-beli virtuális gépek biztonsági másolat elemeinek követheti nyomon.
- Kezelheti az Azure-ral a jelszótárolóhoz való hozzáféréshez [szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
- Azt adja meg, hogy a redundancia biztosítása érdekében a tárolóban lévő adatok replikálási módját:
    - **Helyileg redundáns tárolás (LRS)**: Az egy kínai adatközpont meghibásodása elleni védelem, használhatja az LRS. LRS tárolóskálázási egységben replikálja az adatokat. [További információk](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs).
    - **Georedundáns tárolás (GRS)**: Régióra kiterjedő szolgáltatáskimaradás ellen védelmet biztosító, GRS használhatja. GRS az adatait egy másodlagos régióba replikálja. [További információk](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs). 
    - Alapértelmezés szerint a Recovery Services-tárolók GRS használják. 

## <a name="backup-agents"></a>Backup-ügynököket

Az Azure Backup biztosít különböző backup-ügynököket, a gép típusától függően készül biztonsági másolat:

**Ügynök** | **Részletek** 
--- | --- 
**A MARS-ügynök** | <ul><li>Az egyes helyszíni Windows Server rendszerű gépek biztonsági mentése a fájlok, mappák és a rendszerállapot futtat.</li> <li>Futtat Azure virtuális gépek biztonsági mentése a fájlok, mappák és a rendszer állapotát.</li> <li>A DPM/MABS-kiszolgálók biztonsági mentése a DPM/MABS helyi tárolólemezt az Azure-ban futtatja.</li></ul> 
**Az Azure Virtuálisgép-bővítmény** | Készítsen róluk biztonsági másolatot egy tárolót az Azure virtuális gépeken fut.

## <a name="backup-types"></a>Biztonsági mentési típusok

A következő táblázat ismerteti a különböző típusú biztonsági mentések és használhatók:

**Biztonsági mentés típusa** | **Részletek** | **Használat**
--- | --- | ---
**Teljes** | Egy teljes biztonsági mentést az egész adatforrást tartalmaz. Különbözeti vagy növekményes biztonsági mentések-nál nagyobb hálózati sávszélességet vesz igénybe. | Használja a kezdeti biztonsági mentés.
**Különbségi** |  Különbözeti biztonsági mentése a kezdeti teljes biztonsági mentés óta módosított blokkokat tárolja. Hálózati és tárolási kisebb mennyiségű használ, és nem tartja a változatlan adatok redundáns másolatait.<br/><br/> Nem hatékony, mert a későbbi biztonsági mentések között változatlanul adatblokkok átvitele és tárolt. | Az Azure Backup nem használja.
**Növekményes** | A növekményes biztonsági mentés csak azokat az adatblokkokat az előző biztonsági mentés óta módosított tárolja. Magas tárolási és hálózati hatékonyságát. <br/><br/> A növekményes biztonsági mentés nincs szükség a teljes biztonsági mentések kiegészítésére. | Lemezes biztonsági mentések DPM/MABS által használt, és használja az összes biztonsági mentés az Azure-bA.

## <a name="sql-server-backup-types"></a>Az SQL Server biztonsági mentési típusok

A következő táblázat ismerteti a különböző típusú biztonsági mentések használt SQL Server-adatbázisok, és hogy milyen gyakran használhatók:

**Biztonsági mentés típusa** | **Részletek** | **Használat**
--- | --- | ---
**Teljes biztonsági mentés** | Teljes biztonsági mentés a teljes adatbázis biztonsági mentését. Egy adott adatbázisban vagy a fájlok és fájlcsoportok egy készletben lévő összes adatot tartalmazza. Egy teljes biztonsági mentés elegendő naplók helyreállítani az adatokat is tartalmaz. | Legfeljebb napi egy teljes biztonsági mentés is indíthat.<br/><br/> Ha szeretné, készítsen egy teljes biztonsági mentés napi vagy heti intervallumban.
**Különbségi biztonsági másolat** | A legtöbb legutóbbi, a korábbi teljes-adatok biztonsági mentése egy különbségi biztonsági másolat alapján történik.<br/><br/> Csak a teljes biztonsági mentés óta megváltozott adatokat rögzíti. |  Legfeljebb napi egy különbségi biztonsági mentés is indíthat.<br/><br/> Az azonos napi egy teljes biztonsági mentés és a különbözeti biztonsági mentése nem konfigurálható.
**Tranzakciónapló biztonsági mentését** | A napló biztonsági mentését lehetővé teszi, hogy legfeljebb egy adott második időponthoz visszaállítást. | Egyszerre legfeljebb konfigurálhatja tranzakciós napló biztonsági mentés 15 percenként.

### <a name="comparison-of-backup-types"></a>Biztonsági mentési típusok összehasonlítása

A tárhelyhasználat, a helyreállítási időre vonatkozó célkitűzés (RTO), és a hálózatleterheltség eltér az egyes biztonsági mentés. Az alábbi képen a biztonsági mentési típusok összehasonlítása látható:

- A adatforrás 10 tárolási blokkok A1 – a10-es, amely havonta készül tevődik össze.
- Az A2, az A3, az A4 és az A9 módosul az első hónapban, az A5 pedig a következő hónapban.
- A különbségi biztonsági másolatok a második hónapban a módosult blokkokat, A2, A3, A4 és a9-es készül biztonsági másolat. A harmadik hónapban a módosított A5 blokkal együtt biztonsági másolat készül ugyanezekről a blokkokról. A következő teljes biztonsági mentésig minden alkalommal készül biztonsági másolat a módosított blokkokról.
- A növekményes biztonsági mentést, a második hónapban A2, A3, A4 és a9-es megjelölve blokkok módosulnak, és át. A harmadik hónapban csak az A5 módosított blokk van megjelölve és továbbítva. 

![a biztonsági mentési módszerek összehasonlítását mutató kép](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Biztonsági másolat szolgáltatásai

A következő táblázat összefoglalja a támogatott a különböző típusú biztonsági mentési funkciói:

**Funkció** | **A helyi Windows Server számítógépek (közvetlen)** | **Az Azure virtuális gépek** | **Gépek vagy a DPM vagy MABS-alkalmazások**
--- | --- | --- | ---
Készítsen biztonsági másolatot a tárba | ![Igen][green] | ![Igen][green] | ![Igen][green] 
Készítsen biztonsági másolatot a DPM/MABS lemezre, majd az Azure-bA | | | ![Igen][green] 
A rendszer a biztonsági mentési adatok tömörítése | ![Igen][green] | Nem használ tömörítést adatátvitel során. Tárolási némileg növelve-e, de gyorsabb visszaállítást.  | ![Igen][green] 
A növekményes biztonsági mentés futtatása |![Igen][green] |![Igen][green] |![Igen][green] 
A deduplikált lemezek biztonsági mentése | | | ![Részlegesen][yellow]<br/><br/> A DPM vagy MABS-kiszolgálók csak helyszíni üzembe helyezve. 

![tábla kulcsa](./media/backup-architecture/table-key.png)

## <a name="architecture-direct-backup-of-azure-vms"></a>Architektúra: Közvetlen biztonsági mentést Azure-beli virtuális

1. Biztonsági mentésének engedélyezésekor egy Azure virtuális gép, egy biztonsági mentése fut, a megadott ütemezés szerint.
1. Az első biztonsági mentés során egy biztonsági mentési bővítménynek telepítve van a virtuális gép, ha a virtuális gép fut-e.
    - Windows virtuális gépek esetén a VMSnapshot-bővítmény telepítve van.
    - Linux rendszerű virtuális gépekhez a Linux a VMSnapshot-bővítmény telepítve van.
1. A bővítmény tárolási szintű pillanatképet. 
    - Windows virtuális gépeket futtató, a biztonsági mentés koordinálja a a Windows kötet árnyékmásolata szolgáltatás (VSS) egy alkalmazáskonzisztens pillanatképet készíteni a virtuális gép. Alapértelmezés szerint a biztonsági mentés teljes kötetárnyékmásolat vesz igénybe. Ha a biztonsági mentés nem tudja egy alkalmazáskonzisztens pillanatképet, majd vesz igénybe a fájlkonzisztens pillanatképek.
    - Linux rendszerű virtuális gépekhez biztonsági mentési pillanatképet fájlkonzisztens. Az alkalmazáskonzisztens pillanatképek kell manuálisan testre szabja az előkészítő/utólagos parancsfájl.
    - Biztonsági mentés minden egyes Virtuálisgép-lemez párhuzamos biztonsági mentésével optimalizáltuk. Az egyes lemezek biztonsági mentés alatt álló az Azure Backup beolvassa azokat az adatblokkokat a lemezen, és csak a módosított adatokat tárolja. 
1. Miután a pillanatképet készít, az adatok átkerülnek a tárolóba. 
    - Kizárólag a, amelyek másolja a legutóbbi biztonsági mentés óta módosított blokkokat.
    - Nem titkosítja az adatokat. Az Azure Backup is az Azure virtuális gépek biztonsági mentése az Azure Disk Encryption használatával titkosított.
    - A pillanatkép adatainak előfordulhat, hogy nem kell azonnal-tárolóba másol. Időszakokban a biztonsági mentés órát is igénybe vehet néhány. Virtuális gép teljes biztonsági mentés időpontja a napi biztonsági mentési szabályzatok legfeljebb 24 óra lesz.
1. Után az adatok a tárolóba, a rendszer eltávolítja a pillanatképet, és a egy helyreállítási pont létrehozása.

Az Azure virtuális gépek internet-hozzáférés parancsok van szükség. Ha biztonsági másolatot a virtuális gép (például a SQL Server adatbázisok biztonsági mentése), a háttéralkalmazás adataihoz is kell internet-hozzáféréssel. 

![Az Azure virtuális gépek biztonsági mentése](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Architektúra: Közvetlen biztonsági mentést a helyi Windows Server számítógépek vagy Azure-beli Virtuálisgép-fájlok vagy mappák

1. Állítsa be a forgatókönyv, töltse le, és a MARS-ügynök telepítése a gépre. Ezután válassza ki, hogy miről készüljön biztonsági másolat, mikor fog futni a biztonsági mentések és mennyi ideig azok fogja tárolni az Azure-ban.
1. A kezdeti biztonsági mentés a biztonsági mentési beállításoknak megfelelően fut.
1. A MARS-ügynök időponthoz pillanatkép készítése a biztonsági mentésre kiválasztott kötetek VSS használja.
    - A MARS-ügynök csak a Windows rendszer írási művelet a pillanatkép rögzítéséhez használja.
    - Az ügynök nem minden alkalmazás VSS-írót használ, mert nem rögzíti a alkalmazáskonzisztens pillanatképeket.
1. Miután elvégezte a VSS-pillanatkép, a MARS-ügynök létrehoz egy virtuális merevlemezt (VHD) a gyorsítótármappa, ha konfigurálta a biztonsági mentés megadott. Az ügynök az egyes ellenőrzőösszegek tárolja.
1. Adja meg, az ütemezés szerint futtassa növekményes biztonsági mentéseit, kivéve, ha az ad hoc biztonsági mentés futtatása.
1. A növekményes biztonsági mentést azonosítja a megváltozott fájlokat, és létrejön egy új virtuális Merevlemezt. A virtuális merevlemez van tömörítve és titkosítva, és ezután küldi a tárolóba.
1. A növekményes biztonsági mentés befejezése után az új virtuális merevlemez van egyesítve lesznek a kezdeti replikáció után létrehozott virtuális merevlemez. Az egyesített VHD-t biztosít a legfrissebb állapot folyamatban lévő biztonsági mentés összehasonlításhoz használható.

![A helyszíni Windows Serveres gépek, amelyeken MARS-ügynök biztonsági mentése](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Architektúra: Biztonsági mentés a DPM/MABS

1. A DPM- vagy MABS védelmi ügynököt a védeni kívánt gépekre telepítenie. Majd adja hozzá a gépek a DPM védelmi csoportba.
    - A helyszíni gépek védelme érdekében a DPM- vagy MABS-kiszolgáló a helyszínen kell lennie.
    - Azure virtuális gépek védelme érdekében a MABS-kiszolgáló az Azure-on futó Azure virtuális gépként kell működnie.
    - A DPM/MABS védheti meg biztonsági mentési kötetek, megosztások, fájlok és mappák. Egy gép rendszerállapot (operációs rendszer nélküli) is védheti, és konkrét alkalmazások védelme app-t támogató biztonsági mentési beállítások.
1. Egy gép vagy a DPM/MABS app protection beállításakor választja a MABS/DPM helyi lemezre rövid távú tárolás és az online védelem az Azure biztonsági mentése. Akkor is kell futtatásakor a helyi DPM-vagy MABS-tárhely biztonsági másolat és az online biztonsági mentés az Azure-bA kell futtatásakor adja meg.
1. A lemez, a védett munkaterhelések biztonsági másolatot a helyi MABS/DPM lemezek, a megadott ütemezés szerint.
4. A DPM/MABS lemezek biztonsági mentése a tároló által a MARS-ügynök, amely a DPM vagy MABS-kiszolgálón.

![Gépek és a DPM- vagy MABS által védett munkaterhelések biztonsági mentése](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Az Azure Virtuálisgép-tároló

Az Azure virtuális gépek lemezeket használnak tárolják az operációs rendszer, az alkalmazások és az adatokat. Egyes Azure virtuális gépekhez legalább két lemezt: a lemez az operációs rendszer és a egy ideiglenes lemez. Az Azure virtuális gépek az alkalmazásadatok az adatlemezeket is lehet. Lemezek VHD-ként tárolja.

- Standard vagy prémium szintű tárfiókok az Azure-ban lapblobként tárolt VHD-k:
    - **Standard szintű storage:** Megbízható, költséghatékony lemeztámogatás, amelyek nem a késésre érzékeny számítási feladatokat futtató virtuális gépek számára. Standard szintű storage a standard szintű tartós állapotú meghajtót (SSD) lemezeket vagy a szokásos merevlemezes (HDD) lemezek.
    - **A Premium storage:** Nagy teljesítményű lemezes támogatás. Prémium szintű SSD-lemezeket használ.
- Nincsenek lemezek különböző teljesítményszint:
    - **Standard HDD lemez:** Meghajtókra, és költséghatékony tárolási megoldás használják.
    - **Standard SSD-lemez:** Prémium szintű SSD és a HDD standard szintű lemezek elemeinek egyesíti. Egységes teljesítményéről és megbízhatóságáról, mint a HDD, de továbbra is költséghatékony kínál.
    - **Prémium szintű SSD-lemez:** SSD-k által támogatott, és nagy teljesítményű és kis késleltetést biztosít a nagy I/O-igényű számítási feladatokat futtató virtuális gépekhez.
- Kezelhetők vagy nem felügyelt lemezek:
    - **Nem felügyelt lemezek:** Virtuális gépek által használt lemezek hagyományos típusa. Ezeket a lemezeket a saját tárfiók létrehozásához, és adja meg a lemez létrehozásakor. Ezután ki kell felmérnie, hogyan maximalizálhatja a tárolási erőforrások a virtuális gépek számára.
    - **Felügyelt lemezek:** Azure hoz létre, és a storage-fiókok kezeli az Ön számára. Azt adja meg a lemez méretét és a teljesítmény szint, és az Azure felügyelt lemezeket hoz létre az Ön számára. Lemezek hozzáadása és a virtuális gép méretezése, Azure kezeli a storage-fiókok.

A virtuális gépek disk storage és a rendelkezésre álló lemeztípusok kapcsolatos további információkért tanulmányozza a következő cikkeket:

- [Windows virtuális gépek az Azure managed disksbe](../virtual-machines/windows/managed-disks-overview.md)
- [Linux rendszerű virtuális gépekhez az Azure managed disksbe](../virtual-machines/linux/managed-disks-overview.md)
- [Virtuális gépek számára elérhető lemeztípusok](../virtual-machines/windows/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Biztonsági mentése és helyreállítása Azure virtuális gépek a premium storage 

Biztonsági másolatot készíthet Azure virtuális gépek premium storage az Azure Backup használatával:

- A premium storage virtuális gépek biztonsági mentési folyamat során a Backup szolgáltatás létrehoz egy átmeneti előkészítési hely nevű *AzureBackup -*, a storage-fiókban. Az előkészítési hely mérete megegyezik a helyreállítási pont pillanatképének méretét.
- Győződjön meg arról, hogy a premium storage-fiók rendelkezik-e elegendő szabad terület az átmeneti előkészítési hely. [További információk](../storage/common/storage-scalability-targets.md#premium-performance-storage-account-scale-limits). Ne módosítsa az előkészítési helyet.
- A biztonsági mentési feladat befejezése után a rendszer törli az előkészítési helyet.
- Az előkészítési helyhez használt tároló ára összhangban az [premium storage szolgáltatás díjszabása](../virtual-machines/windows/disks-types.md#billing).

Azure virtuális gépek visszaállításakor a premium storage segítségével visszaállíthatja őket premium vagy standard szintű tárolást. Általában, lenne őket prémium szintű tárolóba visszaállítani. De ha csak a virtuális gépről a fájlok egy alkészletére van szüksége, a standard szintű tárolóba visszaállítani a költséghatékony lehet.

### <a name="back-up-and-restore-managed-disks"></a>Biztonsági mentése és visszaállítása felügyelt lemezeken

Biztonsági másolatot készíthet az Azure virtuális gépek felügyelt lemezekkel rendelkező:

- Azt, hogy végrehajtja-e bármely más Azure virtuális Gépet ugyanúgy felügyelt lemezekkel rendelkező virtuális gépek biztonsági mentése. Biztonsági másolatot készíthet a virtuális gép közvetlenül, a virtuális gép beállításait, vagy engedélyezheti a virtuális gépek biztonsági mentés a Recovery Services-tároló.
- A felügyelt lemezeken található virtuális gépek biztonsági mentését a felügyelt lemezeken kiépített visszaállításipont-gyűjteményekkel végezheti el.
- Az Azure Backup is támogatja az Azure Disk Encryption használatával titkosított, felügyelt lemezekkel rendelkező virtuális gépek biztonsági mentésével.

A felügyelt lemezekkel rendelkező virtuális gépek visszaállításakor visszaállíthatja a teljes felügyelt lemezekkel rendelkező virtuális gép vagy a storage-fiók:

- A visszaállítási folyamat során az Azure kezeli a felügyelt lemezeket. A storage-fiók lehetőséget használja, ha a tárfiókot, a visszaállítási folyamat során létrehozott kezelheti.
- Ha egy felügyelt virtuális gép titkosított, ellenőrizze, hogy a virtuális gép kulcsainak és titkos kulcsok a key vaultban létezik, a visszaállítási folyamat megkezdése előtt.

## <a name="next-steps"></a>További lépések

- Tekintse át a támogatási mátrix [információ a támogatott szolgáltatások és a biztonsági mentési forgatókönyveket vonatkozó korlátozások](backup-support-matrix.md).
- Állítsa be ezeket a forgatókönyveket egyik biztonsági mentés:
    - [Azure virtuális gépek biztonsági mentése](backup-azure-arm-vms-prepare.md).
    - [Közvetlen Windows-gépek biztonsági mentése](tutorial-backup-windows-server-to-azure.md), anélkül, hogy egy biztonsági mentési kiszolgálóra.
    - [Állítsa be a MABS](backup-azure-microsoft-azure-backup.md) Azure adatforrásáig és vissza másolatot a MABS történő biztonsági mentés.
    - [Állítsa be a DPM](backup-azure-dpm-introduction.md) Azure adatforrásáig és vissza másolatot a dpm-hez történő biztonsági mentés.


[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png

