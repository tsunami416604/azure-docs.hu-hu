---
title: Az architektúra áttekintése
description: Áttekintést nyújt az Azure Backup szolgáltatás által használt architektúráról, összetevőkről és folyamatokról.
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: b093c6702bb26fe537622727fe1b623141bf4160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273617"
---
# <a name="azure-backup-architecture-and-components"></a>Azure biztonsági mentési architektúra és összetevők

Az Azure [Backup szolgáltatás](backup-overview.md) segítségével biztonsági másolatot készíthet az adatokról a Microsoft Azure felhőplatformjára. Ez a cikk összegzi az Azure Biztonsági mentés architektúráját, összetevőit és folyamatait.

## <a name="what-does-azure-backup-do"></a>Mire történik az Azure Backup?

Az Azure Backup biztonsági másolatot készít a helyszíni gépeken és az Azure virtuálisgép-példányokon futó adatokról, gépállapotról és számítási feladatokról. Számos Azure Backup forgatókönyvek.

## <a name="how-does-azure-backup-work"></a>Hogyan működik az Azure Backup?

A gépekről és az adatokról számos módszerrel biztonsági másolatot lehet fésülni:

- **Helyszíni gépek biztonsági és biztonsági másolatot kell létrehoznia:**
  - A helyszíni Windows-gépekről közvetlenül az Azure-ba is készíthet biztonsági másolatot az Azure Backup Microsoft Azure Recovery Services (MARS) ügynök használatával. A Linux-gépek nem támogatottak.
  - A helyszíni gépekről biztonsági másolatot készíthet egy biztonsági másolat kiszolgálójára – vagy a System Center Data Protection Manager (DPM) vagy a Microsoft Azure Backup Server (MABS) kiszolgálóra. Ezután biztonsági másolatot készíthet a biztonsági mentési kiszolgálóról az Azure-beli Recovery Services-tárolóba.

- **Az Azure-beli virtuális gépek biztonsági és biztonsági tartaléka:**
  - Közvetlenül is biztonsági másolatot kaphat az Azure-beli virtuális gépekről. Az Azure Backup egy biztonsági mentési bővítményt telepít a virtuális gépen futó Azure virtuálisgép-ügynökhöz. Ez a bővítmény biztonsági másolatot ad a teljes virtuális gépről.
  - Az Azure-beli virtuális gép adott fájljairól és mappáiról a MARS-ügynök futtatásával biztonsági másolatot lehet fésülni.
  - Biztonsági másolatot az Azure-beli virtuális gépekről az Azure-ban futó MABS-re, majd biztonsági másolatot kaphat a MABS-ről egy Recovery Services-tárolóba.

További információ a biztonsági [mentésről](backup-overview.md) és a [támogatott biztonsági mentési forgatókönyvekről.](backup-support-matrix.md)

## <a name="where-is-data-backed-up"></a>Hol készül biztonsági másolatot az adatokról?

Az Azure Backup biztonsági mentési adatokat tárol a Recovery Services-tárolóban. A tároló egy online tárolási entitás az Azure-ban, amely adatok tárolására szolgál, például biztonsági másolatok, helyreállítási pontok és biztonsági mentési szabályzatok tárolására.

A Recovery Services-tárolók a következő funkciókkal rendelkeznek:

- A tárolók megkönnyítik a biztonsági mentési adatok rendszerezését, miközben minimálisra csökkentik a felügyeleti terhelést.
- Minden Azure-előfizetésben legfeljebb 500 tárolót hozhat létre.
- Figyelheti a biztonsági másolatot egy tárolóban, beleértve az Azure virtuális gépek és a helyszíni gépek.
- A tárolóhoz való hozzáférést az Azure [szerepköralapú hozzáférés-vezérléssel (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)kezelheti.
- Megadhatja, hogy a tárolóban lévő adatok hogyan replikálódjanak a redundancia érdekében:
  - **Helyileg redundáns tárolás (LRS)**: Az adatközpontok meghibásodása elleni védelem érdekében használhatja az LRS-t. Az LRS replikálja az adatokat egy tárolóméretezési egységbe. [További információ](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs).
  - **Georedundáns tárolás (GRS):** A régiószintű kimaradások elleni védelem érdekében használhatja a GRS-t. A GRS replikálja az adatokat egy másodlagos régióba. [További információ](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).
  - Alapértelmezés szerint a Recovery Services-tárolók GRS-t használnak.

## <a name="backup-agents"></a>Biztonsági mentési ügynökök

Az Azure Backup különböző biztonsági mentési ügynököket biztosít, attól függően, hogy milyen típusú gépről készül biztonsági másolat:

**Ügynök** | **Részletek**
--- | ---
**MARS ügynök** | <ul><li>Az egyes helyszíni Windows Server-gépeken futtatja a fájlok, mappák és a rendszerállapot biztonsági kapcsolatát.</li> <li>Az Azure-beli virtuális gépeken futtatja a fájlok, mappák és a rendszerállapot biztonsági kapcsolatát.</li> <li>DPM/MABS kiszolgálókon futtatva biztonsági másolatot készíteni a DPM/MABS helyi tárolólemezről az Azure-ba.</li></ul>
**Azure virtuálisgép-bővítmény** | Az Azure-beli virtuális gépeken futtatja őket, hogy biztonsági másolatot ad róluk egy tárolóba.

## <a name="backup-types"></a>Biztonsági másolat típusai

Az alábbi táblázat ismerteti a különböző típusú biztonsági mentések, és mikor használják őket:

**Biztonsági másolat típusa** | **Részletek** | **Használat**
--- | --- | ---
**Összes** | A teljes biztonsági másolat a teljes adatforrást tartalmazza. Több hálózati sávszélességet vesz igénybe, mint a különbözeti vagy növekményes biztonsági mentések. | Kezdeti biztonsági mentéshez használatos.
**Differenciál** |  A különbözeti biztonsági mentés tárolja azokat a blokkokat, amelyek a kezdeti teljes biztonsági mentés óta megváltoztak. Kisebb mennyiségű hálózatot és tárhelyet használ, és nem tartja meg a változatlan adatok redundáns másolatait.<br/><br/> Nem hatékony, mert a későbbi biztonsági mentések között változatlan adatblokkok átvitele és tárolása megtörtén. | Az Azure Backup nem használja.
**Növekvő** | A növekményes biztonsági mentés csak az előző biztonsági mentés óta megváltozott adatblokkokat tárolja. Nagy tárolási és hálózati hatékonyság. <br/><br/> A növekményes biztonsági mentés, nincs szükség a teljes biztonsági mentések kiegészítésére. | A DPM/MABS a lemezbiztonsági mentések, és az Azure-ba minden biztonsági mentésben használható. Nem használható az SQL Server biztonsági mentéséhez.

## <a name="sql-server-backup-types"></a>SQL Server biztonsági mentési típusai

Az alábbi táblázat bemutatja az SQL Server-adatbázisokhoz használt biztonsági mentések különböző típusait és használatuk milyen gyakran történő készítését:

**Biztonsági másolat típusa** | **Részletek** | **Használat**
--- | --- | ---
**Teljes biztonsági mentés** | A teljes adatbázis biztonsági másolata biztonsági másolatot készít a teljes adatbázisról. Egy adott adatbázis ban vagy fájlcsoportok ban vagy fájlokban található összes adatot tartalmazza. A teljes biztonsági mentés is tartalmaz elegendő naplókat az adatok helyreállításához. | Legtöbb, tudod ravasz egy teli hát által nap.<br/><br/> Dönthet úgy, hogy a teljes biztonsági mentést napi vagy heti időközönként készíti el.
**Különbségi biztonsági mentés** | A különbözeti biztonsági mentés a legutóbbi, korábbi teljes adatbiztonsági mentésen alapul.<br/><br/> Csak azokat az adatokat rögzíti, amelyek a teljes biztonsági mentés óta megváltoztak. |  Legtöbb, akkor kiváltó egy differenciál biztonsági mentés naponta.<br/><br/> Nem konfigurálható a teljes biztonsági mentés és a különbözeti biztonsági mentés ugyanazon a napon.
**Tranzakciónapló biztonsági mentése** | A napló biztonsági mentése lehetővé teszi, hogy a pont-az-idő helyreállítása egy adott másodpercig. | Legbelül 15 percenként konfigurálhatja a tranzakciós napló biztonsági mentéseit.

### <a name="comparison-of-backup-types"></a>A biztonsági mentéstípusok összehasonlítása

A tárolási felhasználás, a helyreállítási idő célkitűzés (RTO) és a hálózati felhasználás a biztonsági mentés egyes típusai szerint változik. Az alábbi képen a biztonsági mentés típusainak összehasonlítása látható:

- Az A adatforrás 10 a1-A10-as tárolóblokkból áll, amelyekről havonta készül biztonsági másolatot.
- Az A2, az A3, az A4 és az A9 módosul az első hónapban, az A5 pedig a következő hónapban.
- A különbözeti biztonsági mentések esetében a második hónapban az A2, A3, A4 és A9 blokkokról biztonsági másolat készül. A harmadik hónapban a módosított A5 blokkal együtt biztonsági másolat készül ugyanezekről a blokkokról. A következő teljes biztonsági mentésig minden alkalommal készül biztonsági másolat a módosított blokkokról.
- Növekményes biztonsági mentések esetén a második hónapban az A2, A3, A4 és A9 blokkok módosulva ként vannak megjelölve, és átkerülnek. A harmadik hónapban csak az A5 módosított blokk van megjelölve és továbbítva.

![A biztonsági mentési módszerek összehasonlítását bemutató kép](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Biztonsági mentési funkciók

Az alábbi táblázat összefoglalja a különböző típusú biztonsági mentés támogatott szolgáltatásait:

**Szolgáltatás** | **Fájlok és mappák közvetlen biztonsági mentése (marsügynök használatával)** | **Az Azure virtuális gép biztonsági mentése** | **DPM/MABS-szel rendelkező gépek vagy alkalmazások**
--- | --- | --- | ---
Biztonsági mentés a páncélteremhez | ![Igen][green] | ![Igen][green] | ![Igen][green]
Biztonsági másolatot készíteni a DPM/MABS lemezre, majd az Azure-ba | | | ![Igen][green]
Biztonsági mentésre küldött adatok tömörítése | ![Igen][green] | Az adatok átvitelekekénéskor nem történik tömörítés. A tárolás kissé felfújódik, de a helyreállítás gyorsabb.  | ![Igen][green]
Növekményes biztonsági mentés futtatása |![Igen][green] |![Igen][green] |![Igen][green]
Deduplikált lemezek biztonsági másolatának biztonsági másolata | | | ![Részlegesen][yellow]<br/><br/> Csak a helyszínen telepített DPM/MABS-kiszolgálók esetén.

![Táblakulcs](./media/backup-architecture/table-key.png)

## <a name="backup-policy-essentials"></a>Biztonsági mentési házirend alapvető fontosságú

- A biztonsági mentési szabályzat tárolónként jön létre.
- A következő munkaterhelések biztonsági mentéséhez biztonsági mentési szabályzat hozható létre
  - Azure VM
  - SQL az Azure virtuális gépében
  - Azure-fájlmegosztás
- A házirend számos erőforráshoz rendelhető hozzá. Az Azure virtuális gép biztonsági mentési szabályzata számos Azure-virtuális gép védelmére használható.
- A házirend két összetevőből áll
  - Ütemezés: Mikor kell a biztonsági mentést elkészíteni?
  - Megőrzés: Az egyes biztonsági mentések megőrzési ideje.
- Az ütemezés "napi" vagy "heti" típusúként határozható meg egy adott időpontban.
- Megőrzési lehet határozni a "napi", "heti", "havi", "éves" biztonsági mentési pontok.
- A "heti" a hét egy bizonyos napján biztonsági mentést jelent, a "havi" a hónap egy bizonyos napján biztonsági mentést, az "éves" pedig az év egy bizonyos napján történő biztonsági mentést.
- A "havi", "éves" biztonsági mentési pontok megőrzése a "LongTermRetention" néven van nevezve.
- A tároló létrehozásakor az Azure virtuális gép biztonsági mentések "DefaultPolicy" nevű szabályzat is létrejön, és az Azure virtuális gépek biztonsági mentéséhez használható.

## <a name="architecture-built-in-azure-vm-backup"></a>Architektúra: Beépített Azure vm biztonsági mentés

1. Ha engedélyezi a biztonsági mentést egy Azure-beli virtuális géphez, a biztonsági mentés a megadott ütemezés szerint fut.
1. Az első biztonsági mentés során egy biztonsági mentési bővítmény van telepítve a virtuális gépre, ha a virtuális gép fut.
    - Windows virtuális gépek esetén a VMSnapshot bővítmény telepítve van.
    - Linuxos virtuális gépek esetén a VMSnapshot Linux-bővítmény telepítve van.
1. A bővítmény tárolási szintű pillanatképet készít.
    - Futó Windows-virtuális gépek esetén a Biztonsági másolat a Windows kötet árnyékmásolata szolgáltatásával (VSS) való koordinátákat a virtuális gép alkalmazáskonzisztens pillanatképének készítéséhez. Alapértelmezés szerint a Biztonsági másolat teljes VSS biztonsági mentést készít. Ha a Biztonsági másolat nem tud alkalmazáskonzisztens pillanatképet készíteni, akkor fájlkonzisztens pillanatképet készít.
    - Linuxos virtuális gépek esetén a biztonsági mentés fájlkonzisztens pillanatképet készít. Az alkalmazáskonzisztens pillanatképekhez manuálisan kell testreszabnia az elő-/postai parancsfájlokat.
    - A biztonsági mentés úgy van optimalizálva, hogy az egyes virtuális géplemezeket párhuzamosan biztonsági másolatot készít. Minden egyes lemez biztonsági mentését, az Azure Backup beolvassa a blokkokat a lemezen, és csak a módosított adatokat tárolja.
1. A pillanatkép készítése után az adatok a tárolóba kerülnek.
    - A rendszer csak az utolsó biztonsági mentés óta megváltozott adatblokkokat másolja.
    - Az adatok nincsenek titkosítva. Az Azure Backup biztonsági másolatot készíthet az Azure Disk Encryption használatával titkosított Azure-beli virtuális gépekről.
    - Előfordulhat, hogy a pillanatkép adatok nem azonnal másolja a tárolóba. Csúcsidőben a biztonsági mentés néhány órát is igénybe vehet. A virtuális gép teljes biztonsági mentési ideje kevesebb lesz, mint 24 óra a napi biztonsági mentési szabályzatok esetében.
1. Miután az adatokat elküldte a tárolóba, létrejön egy helyreállítási pont. Alapértelmezés szerint a pillanatképek két napig megmaradnak a törlésük előtt. Ez a funkció lehetővé teszi a pillanatképek visszaállítási műveletét, ezáltal csökkentve a visszaállítási időket. Csökkenti az adatok átalakításához és a tárolóból való visszamásolásához szükséges időt. Lásd: [Azure Backup azonnali visszaállítási funkció.](https://docs.microsoft.com/azure/backup/backup-instant-restore-capability)

Nem kell explicit módon engedélyeznie az internetkapcsolat nak az Azure-beli virtuális gépek biztonsági biztonsági csinálásához.

![Az Azure-beli virtuális gépek biztonsági mentése](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Architektúra: A helyszíni Windows Server-gépek vagy Az Azure virtuálisgép-fájlok vagy -mappák közvetlen biztonsági mentése

1. A forgatókönyv beállításához töltse le és telepítse a MARS-ügynököt a számítógépre. Ezután kiválaszthatja, hogy miről szeretne biztonsági másolatot készíteni, mikor futnak a biztonsági mentések, és mennyi ideig lesznek megtárolva az Azure-ban.
1. A kezdeti biztonsági mentés a biztonsági mentési beállításoknak megfelelően fut.
1. A MARS-ügynök a VSS segítségével pillanatképet készít a biztonsági mentéshez kiválasztott kötetekről.
    - A MARS-ügynök csak a Windows rendszer írási műveletét használja a pillanatkép rögzítéséhez.
    - Mivel az ügynök nem használ semmilyen alkalmazás VSS-írók, nem rögzíti az alkalmazás-konzisztens pillanatképek.
1. Miután elkészült a VSS-szel, a MARS-ügynök létrehoz egy virtuális merevlemezt (VHD) a biztonsági mentés konfigurálásakor megadott gyorsítótármappában. Az ügynök az egyes adatblokkok ellenőrzőösszegeit is tárolja.
1. A növekményes biztonsági mentések a megadott ütemezés szerint futnak, kivéve, ha igény szerinti biztonsági mentést futtat.
1. A növekményes biztonsági mentések, a módosított fájlok azonosítása és egy új virtuális merevlemez jön létre. A virtuális merevlemez tömörített és titkosított, majd elküldi a tárolóba.
1. A növekményes biztonsági mentés befejezése után az új virtuális merevlemez egyesül a kezdeti replikáció után létrehozott virtuális merevlemezsel. Ez az egyesített virtuális merevlemez biztosítja a folyamatban lévő biztonsági mentés összehasonlításához használandó legújabb állapotot.

![Helyszíni Windows Server-gépek biztonsági mentése MARS-ügynökkel](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Architektúra: Biztonsági másolatot aDPM/MABS-re

1. A DPM vagy MABS védelmi ügynököt a védeni kívánt gépekre telepíti. Ezután adja hozzá a gépeket egy DPM védelmi csoporthoz.
    - A helyszíni gépek védelme érdekében a DPM vagy MABS kiszolgálónak a helyszínen kell lennie.
    - Az Azure virtuális gépek védelme érdekében a MABS-kiszolgálónak az Azure-ban kell lennie, és Azure-beli virtuális gépként kell futnia.
    - A DPM/MABS segítségével megvédheti a biztonsági mentési köteteket, megosztásokat, fájlokat és mappákat. A számítógép rendszerállapotát (csupasz fém) is megvédheti, és bizonyos alkalmazásokat az alkalmazásbarát biztonsági mentési beállításokkal is megvédhet.
1. Amikor egy dpm/mabs-ben beállítja egy számítógép vagy alkalmazás védelmét, akkor a rövid távú tárolás érdekében biztonsági másolatot kell készítenie az MABS/DPM helyi lemezről, és az Azure-ba az online védelem érdekében. Azt is megadhatja, hogy a helyi DPM/MABS-tárba történő biztonsági mentés mikor fusson, és mikor fusson az Azure-ba irányuló online biztonsági mentés.
1. A védett számítási feladatok lemezéről a megadott ütemezés nek megfelelően a rendszer biztonsági másolatot készíteni a helyi MABS/DPM lemezekre.
1. A DPM/MABS lemezekről a DPM/MABS kiszolgálón futó MARS-ügynök biztonsági másolatot készíteni a tárolóba.

![A DPM vagy MABS által védett gépek és munkaterhelések biztonsági mentése](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Azure virtuális gép tárolása

Az Azure virtuális gépek lemezek használatával tárolják az operációs rendszer, alkalmazások és adatok tárolására. Minden Egyes Azure virtuális gép rendelkezik legalább két lemez: egy lemezt az operációs rendszer és egy ideiglenes lemez. Az Azure virtuális gépek is rendelkezhetnek az alkalmazásadatok adatlemezei. A lemezek virtuális merevlemezként tárolódnak.

- A Virtuális- és biztonságos helyek lapblobként tárolódnak az Azure-beli standard vagy prémium szintű tárfiókokban:
  - **Normál tárolás:** Megbízható, alacsony költségű lemeztámogatás a késésre nem érzékeny számítási feladatokat futtató virtuális gépek számára. A szabványos tárolónormál ssd-meghajtós (SSD) lemezeket vagy szabványos merevlemez-meghajtókat (HDD) is használhat.
  - **Prémium szintű tárolás:** Nagy teljesítményű lemeztámogatás. Prémium szintű SSD-lemezeket használ.
- A lemezekhez különböző teljesítményszintek tartoznak:
  - **Szabványos HDD lemez:** HDD-k segítségével, költséghatékony tárolásra szolgál.
  - **Szabványos SSD-lemez:** A prémium szintű SSD-lemezek és a szabványos HDD-lemezek elemeit egyesíti. Egységesebb teljesítményt és megbízhatóságot kínál, mint a HDD, de még mindig költséghatékony.
  - **Prémium SSD-lemez:** SSD-k által támogatott, és nagy teljesítményű és alacsony késésű virtuális gépek, amelyek i/o-igényes számítási feladatokat.
- A lemezek kezelhetők vagy nem felügyeltek:
  - **Nem felügyelt lemezek:** A virtuális gépek által használt lemezek hagyományos típusa. Ezekhez a lemezekhez saját tárfiókot hozhat létre, és a lemez létrehozásakor adja meg. Ezután ki kell találnia, hogyan maximalizálhatja a virtuális gépek tárolási erőforrásait.
  - **Felügyelt lemezek:** Az Azure létrehozza és kezeli a tárfiókokat. Megadhatja a lemez méretét és a teljesítményszintet, és az Azure felügyelt lemezeket hoz létre. Lemezek hozzáadásaés a virtuális gépek méretezése, az Azure kezeli a tárfiókok.

A lemeztárolásról és a virtuális gépekhez rendelkezésre álló lemeztípusokról az alábbi cikkekben talál további információt:

- [Azure által kezelt lemezek Windows virtuális gépekhez](../virtual-machines/windows/managed-disks-overview.md)
- [Azure felügyelt lemezek Linuxos virtuális gépekhez](../virtual-machines/linux/managed-disks-overview.md)
- [A virtuális gépekhez rendelkezésre álló lemeztípusok](../virtual-machines/windows/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Az Azure-beli virtuális gépek biztonsági és visszaállítása prémium szintű tárterülettel

Az Azure-beli virtuális gépekről is készíthet biztonsági másolatot a prémium szintű tárhely használatával az Azure Backup szolgáltatással:

- A virtuális gépek prémium szintű tárterülettel történő biztonsági mentésének folyamata során a biztonsági mentési szolgáltatás létrehoz egy *azurebackup-* nevű ideiglenes átmeneti helyet a tárfiókban. Az átmeneti hely mérete megegyezik a helyreállítási pont pillanatképének méretével.
- Győződjön meg arról, hogy a prémium szintű tárfiók megfelelő szabad területtel rendelkezik az ideiglenes átmeneti hely elhelyezéséhez. További információ: [Méretezhetőségi célok a prémium szintű lapblob storage-fiókok.](../storage/blobs/scalability-targets-premium-page-blobs.md) Ne módosítsa az átmeneti helyet.
- A biztonsági mentési feladat befejezése után az átmeneti hely törlődik.
- Az átmeneti helyhez használt tárhely ára összhangban van a [prémium szintű tárolási díjszabással.](../virtual-machines/windows/disks-types.md#billing)

Ha prémium szintű tárhely használatával állítja vissza az Azure-beli virtuális gépeket, visszaállíthatja őket prémium szintű vagy normál szintű tárhelyre. Általában visszaállítja őket a prémium szintű tárba. De ha csak egy részhalmaza fájlokat a virtuális gép, lehet, hogy költséghatékony visszaállítani őket a szabványos tároló.

### <a name="back-up-and-restore-managed-disks"></a>Felügyelt lemezek biztonsági és visszaállítása

Az Azure virtuális gépeiről felügyelt lemezekkel is biztonsági másolatot tud készíteni:

- A felügyelt lemezekkel rendelkező virtuális gépekről ugyanúgy, ahogy bármely más Azure-virtuális gépet is. Biztonsági másolatot készíthet a virtuális gépről közvetlenül a virtuális gép beállításaiból, vagy engedélyezheti a virtuális gépek biztonsági mentését a Helyreállítási szolgáltatások tárolójában.
- A felügyelt lemezeken található virtuális gépek biztonsági mentését a felügyelt lemezeken kiépített visszaállításipont-gyűjteményekkel végezheti el.
- Az Azure Backup is támogatja a virtuális gépek biztonsági mentését az Azure Disk Encryption használatával titkosított felügyelt lemezekkel.

Ha felügyelt lemezekkel állítja vissza a virtuális gépeket, visszaállíthatja a felügyelt lemezekkel rendelkező teljes virtuális gépre vagy egy tárfiókba:

- A visszaállítási folyamat során az Azure kezeli a felügyelt lemezeket. Ha a tárfiók beállítást használja, a visszaállítási folyamat során létrehozott tárfiókot kezeli.
- Ha visszaállít egy felügyelt virtuális gép, amely titkosított, győződjön meg arról, hogy a virtuális gép kulcsai és titkos kulcsai léteznek a key vaultban, mielőtt elindítané a visszaállítási folyamatot.

## <a name="next-steps"></a>További lépések

- Tekintse át a támogatási [mátrixot, és ismerje meg a támogatott funkciókat és a biztonsági mentési forgatókönyvek korlátait.](backup-support-matrix.md)
- Állítsa be a biztonsági mentést az alábbi esetek egyikéhez:
  - [Az Azure-beli virtuális gépek biztonsági másolatot kell kapnia.](backup-azure-arm-vms-prepare.md)
  - [Készítsen biztonsági másolatot közvetlenül a Windows-gépekről,](tutorial-backup-windows-server-to-azure.md)tartalék kiszolgáló nélkül.
  - Állítsa be a [MABS-t](backup-azure-microsoft-azure-backup.md) az Azure-ba való biztonsági mentéshez, majd készítsen biztonsági másolatot a számítási feladatokról a MABS-re.
  - Állítsa be a [DPM-et](backup-azure-dpm-introduction.md) az Azure-ba való biztonsági mentéshez, majd készítsen biztonsági másolatot a számítási feladatokról a DPM-re.

[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png
