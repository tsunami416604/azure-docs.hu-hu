---
title: Mi az az Azure Backup?
description: Áttekintést nyújt a Azure Backup szolgáltatásról, valamint arról, hogy miként járul hozzá az üzletmenet-folytonossági és a vész-helyreállítási (BCDR) stratégiához.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 04/24/2019
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: a4fbfeb96d2316ce6af100cb16fcbf0d13f230f2
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737116"
---
# <a name="what-is-the-azure-backup-service"></a>Mi a Azure Backup szolgáltatás?

Az Azure Backup szolgáltatás biztonsági másolatot készít az Microsoft Azure felhőbe. Biztonsági mentést készíthet a helyszíni gépekről és munkaterhelésekről, valamint az Azure-beli virtuális gépekről.


## <a name="why-use-azure-backup"></a>Miért érdemes az Azure Backupot használni?

A Azure Backup a következő kulcsfontosságú előnyöket nyújtja:

- Helyszíni **biztonsági mentés**kiszervezése: Azure Backup egyszerű megoldást kínál a helyszíni erőforrások felhőbe történő biztonsági mentésére. Rövid és hosszú távú biztonsági mentést készíthet, anélkül, hogy bonyolult, helyszíni biztonsági mentési megoldásokat kellene üzembe helyeznie.
- **Azure IaaS virtuális gépek biztonsági mentése**: A Azure Backup független és elkülönített biztonsági mentéseket biztosít az eredeti adatvesztés elleni védelemhez. A biztonsági mentések tárolása egy Recovery Services-tárolóban történik, a helyreállítási pontok beépített felügyelve. A konfiguráció és a méretezhetőség egyszerű, a biztonsági mentések optimalizáltak, és szükség esetén egyszerűen visszaállíthatók.
- **Könnyen méretezhető** – a Azure Backup az Azure-felhő mögöttes teljesítményének és korlátlan skálájának használatával biztosítja a magas rendelkezésre állást karbantartási vagy figyelési terhelés nélkül.
- **Korlátlan adatátviteli adatok**beszerzése: Azure Backup nem korlátozza az átvitt bejövő vagy kimenő adatok mennyiségét, vagy a továbbított adatok díját.
    - Kimenő adatokon a Recovery Services-tárolóból a visszaállítási művelet során átvitt adatok értendők.
    - Ha az Azure import/export szolgáltatással nagy mennyiségű adat importálására szolgáló offline kezdeti biztonsági mentést hajt végre, akkor a rendszer a bejövő adatokhoz tartozó költségeket is felhasználja.  [További információk](backup-azure-backup-import-export.md).
- **Biztonság megőrzése**: Azure Backup megoldásokat biztosít az adatátvitelhez és a nyugalmi állapotban lévő adatok biztonságossá tételéhez.
- **Alkalmazás-konzisztens biztonsági másolatok**beolvasása: Az alkalmazás-konzisztens biztonsági mentés azt jelenti, hogy egy helyreállítási pont rendelkezik az összes szükséges adattal a biztonsági másolat visszaállításához. Az Azure Backup olyan alkalmazáskonzisztens biztonsági mentést nyújt, amely garantálja, hogy ne legyen szükség további javításokra az adatok visszaállításához. Az alkalmazáskonzisztens adatok visszaállítása rövidebb idő alatt végrehajtható, így gyorsan visszatérhet egy működőképes állapotba.
- **Rövid és hosszú távú adat megőrzése**: A rövid és hosszú távú adatmegőrzéshez Recovery Services-tárolókat használhat. Az Azure nem korlátozza az adatok megőrzési idejét a Recovery Services-tárolókban. Ha szeretné, megtarthatja. Az Azure Backup védett példányonként 9999 helyreállítási pontos felső határral rendelkezik. 
- **Automatikus tárolófelügyelet** – A hibrid környezetek gyakran megkövetelik a heterogén tárhely alkalmazását – valamennyi tárhely szükséges a helyszínen, valamennyi a felhőben. Az Azure Backup szolgáltatással nem kell költenie helyszíni tárolóeszközökre. Azure Backup automatikusan lefoglalja és felügyeli a biztonsági mentési tárolót, és a használaton kívüli modellt használja, így csak a felhasznált tárterületért kell fizetnie. [További](https://azure.microsoft.com/pricing/details/backup) információ a díjszabásról.
- **Több tárolási lehetőség** – a Azure Backup kétféle replikálási lehetőséget kínál a tárterület/az adatmennyiség nagyfokú rendelkezésre állásának megőrzésére.
    - A [helyileg redundáns tárolás (LRS)](../storage/common/storage-redundancy-lrs.md) háromszor replikálja az adatait (az adatait három példányban hozza létre) egy adatközpont tárolási méretezési egységében. Az adatok összes másolata ugyanabban a régióban található. Az LRS egy alacsony költségű megoldás az adatok védelmére a helyi hardveres hibák esetén.
    - A [geo-redundáns tárolás (GRS)](../storage/common/storage-redundancy-grs.md) az alapértelmezett és ajánlott replikációs lehetőség. A GRS az adatait egy másodlagos régióba replikálja (több száz kilométerre a forrásadatok elsődleges helyétől). A GRS módszer költségesebb, mint az LRS, de adatainak megőrzését magasabb szinten biztosítja, még regionális szolgáltatáskiesés esetére is.


## <a name="whats-the-difference-between-azure-backup-and-azure-site-recovery"></a>Mi a különbség a Azure Backup és az Azure Site Recovery között?

A Azure Backup és Azure Site Recovery szolgáltatások mind az üzletmenet folytonosságát, mind a vész-helyreállítási (BCDR) stratégiát segítik a vállalatnál. A BCDR két fő célkitűzésből áll:

- Az üzleti adatai biztonságban maradhatnak, és leállás esetén helyreállítható.
- A tervezett és nem tervezett leállások során megtarthatja alkalmazásait és munkaterheléseit.

Mindkét szolgáltatás kiegészítő, de eltérő funkciókat biztosít.

- **Azure site Recovery**: A Site Recovery vész-helyreállítási megoldást biztosít a helyszíni gépekhez és az Azure-beli virtuális gépekhez. A gépeket egy elsődleges helyről egy másodlagosra replikálja. Katasztrófa esetén a rendszer a gépet a másodlagos helyre hajtja végre, és onnan fér hozzá. Ha minden a szokásos módon újra működik, az elsődleges helyen visszaállíthatja a számítógépeket.
- **Azure Backup**: A Azure Backup szolgáltatás biztonsági másolatot készít a helyszíni gépekről és az Azure-beli virtuális gépekről. Az adatokat a rendszer részletesen, részletesen helyreállíthatja, beleértve a fájlok, mappák, számítógéprendszer-állapot és az alkalmazások által készített biztonsági mentés biztonsági mentését is. A Azure Backup részletesebben kezeli az adatokat, mint a Site Recovery. Ha például egy bemutató a laptopon megsérült, akkor a Azure Backup használatával visszaállíthatja a bemutatót. Ha meg szeretné őrizni a virtuális gép konfigurációját és az adatvédelmet, akkor használhatja a Site Recovery.  

A táblázat pontok segítségével megtalálhatja a BCDR igényeit.

**Objective** | **Részletek** | **Összehasonlítás**
--- | --- | ---
**Adatok biztonsági mentése/megőrzése** | A biztonsági mentési adatok napok, hónapok vagy akár évek esetén is megtekinthetők és tárolhatók, ha azok megfelelőségi szempontból szükségesek. | A biztonsági mentési megoldások, például a Azure Backup lehetővé teszik az adatok biztonsági mentését, valamint a biztonsági mentési és adatmegőrzési szabályzatok finomhangolását.<br/><br/> A Site Recovery nem teszi lehetővé ugyanazt a finomhangolást.
**Helyreállítási pont célkitűzése (RPO)** | Az elfogadható adatveszteség mennyisége, ha helyreállítást kell végezni. | A biztonsági mentések több változót RPO.<br/><br/> A virtuális gépek biztonsági mentései általában egy nap RPO rendelkeznek, míg az adatbázis biztonsági mentései 15 percnél RPO vannak.<br/><br/> Site Recovery alacsony RPO biztosít, mivel a replikáció folyamatos vagy gyakori, így a forrás-és a replika-másolás közötti különbözet kicsi.
**Helyreállítási időre vonatkozó célkitűzés (RTO)** |A helyreállítás vagy visszaállítás elvégzéséhez szükséges idő. | A nagyobb RPO miatt a biztonsági mentési megoldások által feldolgozandó adatmennyiség általában sokkal nagyobb, ami hosszabb RTO-khoz vezet. Napokba telhet például az adatok szalagokról való visszaállítása attól függően, hogy mennyi ideig tart a szalag szállítása egy külső helyről.

## <a name="what-backup-scenarios-are-supported"></a>Milyen biztonsági mentési forgatókönyvek támogatottak?

Azure Backup a helyszíni és az Azure-beli virtuális gépekről is készíthet biztonsági mentést.

**Gép** | **Biztonsági mentés forgatókönyve**
--- | ---
**Helyszíni biztonsági mentés** |  1.) futtassa a Azure Backup Microsoft Azure Recovery Services (MARS) ügynököt a helyszíni Windows rendszerű számítógépeken az egyes fájlok és a rendszerállapotok biztonsági mentéséhez. <br/><br/>2.) készítsen biztonsági mentést a helyszíni gépekről egy biztonsági mentési kiszolgálóra (System Center Data Protection Manager (DPM) vagy Microsoft Azure Backup Server (MABS)), majd konfigurálja úgy a biztonsági mentési kiszolgálót, hogy biztonsági másolatot készítsen az Azure-beli Azure Backup Recovery Services-tárolóra.
**Azure-beli virtuális gépek** | 1.) az egyes Azure-beli virtuális gépek biztonsági mentésének engedélyezése. Ha engedélyezi a biztonsági mentést, Azure Backup telepít egy bővítményt a virtuális gépen futó Azure VM-ügynökre. Az ügynök biztonsági másolatot készít a teljes virtuális gépről.<br/><br/> 2.) futtassa a MARS-ügynököt egy Azure-beli virtuális gépen. Ez akkor hasznos, ha az egyes fájlokról és mappákról szeretne biztonsági másolatot készíteni a virtuális gépen.<br/><br/> 3.) egy Azure-beli virtuális gép biztonsági mentése az Azure-ban futó DPM-kiszolgálóra vagy MABS. Ezután biztonsági mentést készíthet a DPM-kiszolgálóról/MABS egy tárolóra a Azure Backup használatával.


## <a name="why-use-a-backup-server"></a>Miért érdemes biztonsági mentési kiszolgálót használni?
A gépek és alkalmazások MABS-vagy DPM-tárolóba történő biztonsági mentésének előnyei, majd a DPM/MABS-tárolók biztonsági mentése az alábbiak szerint történik:

- A MABS/DPM-re történő biztonsági mentés a gyakori alkalmazások, például a SQL Server, az Exchange és a SharePoint számára optimalizált, app-kompatibilis biztonsági mentéseket tartalmaz, a fájlok, mappák/kötetek biztonsági mentése és a gépi állapot biztonsági mentése (operációs rendszer nélküli, rendszerállapot).
- A helyszíni gépek esetében nem kell telepítenie a MARS-ügynököt minden olyan gépen, amelyről biztonsági másolatot szeretne készíteni. Mindegyik gépen fut a DPM/MABS védelmi ügynök, és a MARS-ügynök csak a MABS/DPM fut.
- Nagyobb rugalmasságot és részletes ütemezési lehetőségeket biztosít a biztonsági mentések futtatásához.
- A biztonsági mentések több, a védelmi csoportokban gyűjtött, egyetlen konzolon kezelt gépre is kezelhetők. Ez különösen akkor hasznos, ha az alkalmazások több gépre vannak felhasználva, és együtt szeretné őket biztonsági másolatot készíteni.

További információ arról, hogy a biztonsági [Mentés hogyan működik](backup-architecture.md#architecture-back-up-to-dpmmabs) a biztonsági mentési kiszolgáló használatakor, valamint a biztonsági mentési kiszolgálók [támogatási követelményeit](backup-support-matrix-mabs-dpm.md) .

## <a name="what-can-i-back-up"></a>Mire használhatom a biztonsági mentést?

**Gép** | **Biztonsági mentési módszer** | **Biztonsági mentés**
--- | --- | ---
**Helyszíni Windows rendszerű virtuális gépek** | MARS-ügynök futtatása | Fájlok, mappák, rendszerállapotok biztonsági mentése.<br/><br/> A Linux rendszerű gépek nem támogatottak.
**Helyszíni gépek** | Biztonsági mentés a DPM/MABS | A [DPM](backup-support-matrix-mabs-dpm.md#supported-backups-to-dpm) vagy [MABS](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs)által védett bármit biztonsági mentést készíthet, beleértve a fájlokat/mappákat/megosztásokat/köteteket és az alkalmazásspecifikus adatmennyiségeket.
**Azure-beli virtuális gépek** | Azure VM-ügynök biztonsági mentési bővítményének futtatása | Teljes virtuális gép biztonsági mentése
**Azure-beli virtuális gépek** | MARS-ügynök futtatása | Fájlok, mappák, rendszerállapotok biztonsági mentése.<br/><br/> A Linux rendszerű gépek nem támogatottak.
**Azure-beli virtuális gépek** | Biztonsági mentés az Azure-ban futó MABS/DPM | A [MABS](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs) vagy [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807) által védett bármit biztonsági mentést készíthet, beleértve a fájlok/mappák/Megosztások/kötetek és az alkalmazásspecifikus adat védelmét.

## <a name="what-backup-agents-do-i-need"></a>Milyen biztonsági mentési ügynökökre van szükségem?

**Forgatókönyv** | **Ügynök**
--- | ---
**Azure-beli virtuális gépek biztonsági mentése** | Nincs szükség ügynökre. A biztonsági mentéshez készült Azure virtuálisgép-bővítmény telepítve van az Azure-beli virtuális gépen az első Azure-beli virtuális gép biztonsági mentésének futtatásakor.<br/><br/> Támogatás Windows-és Linux-támogatáshoz.
**Helyszíni Windows rendszerű gépek biztonsági mentése** | Töltse le, telepítse és futtassa a MARS-ügynököt közvetlenül a gépen.
**Azure-beli virtuális gépek biztonsági mentése a MARS-ügynökkel** | Töltse le, telepítse és futtassa a MARS-ügynököt közvetlenül a gépen. A MARS-ügynök a biztonsági mentési bővítmény mellett is futtatható.
**Helyszíni gépek és Azure-beli virtuális gépek biztonsági mentése DPM/MABS** | A DPM vagy a MABS védelmi ügynök a védelemmel ellátni kívánt gépeken fut. A MARS-ügynök a DPM-kiszolgálón/MABS fut az Azure-ba való biztonsági mentéshez.

## <a name="which-backup-agent-should-i-use"></a>Melyik biztonsági mentési ügynököt használjam?

**Biztonsági mentés** | **Megoldás** | **Korlátozás**
--- | --- | ---
**Egy teljes Azure-beli virtuális gépen szeretnék biztonsági mentést készíteni** | Engedélyezze a virtuális gép biztonsági mentését. A biztonsági mentési bővítmény automatikusan be lesz állítva a Windows vagy Linux rendszerű Azure-beli virtuális gépen. | A teljes virtuális gép biztonsági mentése <br/><br/> Windows rendszerű virtuális gépek esetén a biztonsági mentés alkalmazás-konzisztens. Linux rendszeren a biztonsági mentés fájl-konzisztens. Ha a Linux rendszerű virtuális gépek esetében app-awarere van szüksége, ezt egyéni parancsfájlokkal kell konfigurálnia.
**Egy Azure-beli virtuális gépen szeretnék biztonsági mentést készíteni adott fájlokról/mappákról** | Telepítse a MARS-ügynököt a virtuális gépre.
**Közvetlenül a helyszíni Windows-gépeket szeretnék visszavenni** | Telepítse a MARS-ügynököt a gépre. | A fájlok, mappák és rendszerállapotok biztonsági mentését az Azure-ba is végezheti. A biztonsági mentések nem kompatibilisek az alkalmazással.
**Szeretnék közvetlenül biztonsági mentést készíteni a helyszíni Linux rendszerű gépekről** | Az Azure-ba történő biztonsági mentéshez telepítenie kell a DPM vagy a MABS. | A Linux-gazdagép biztonsági mentése nem támogatott, csak a Hyper-V-vagy VMWare-alapú Linux rendszerű vendégekről készíthet biztonsági mentést.
**Helyszíni rendszeren futó alkalmazásokról szeretnék biztonsági mentést készíteni.** | Az alkalmazással kompatibilis biztonsági mentési gépeket a DPM vagy a MABS védelemmel kell eltárolni.
**Részletes és rugalmas biztonsági mentési és helyreállítási beállításokat szeretnék az Azure-beli virtuális gépekhez** | Azure-beli virtuális gépek védelme az Azure-ban futó MABS/DPM, a biztonsági mentési ütemezés további rugalmassága érdekében, valamint a fájlok, mappák, kötetek, alkalmazások és rendszerállapotok védelmének és helyreállításának teljes rugalmassága.

## <a name="backup-and-retention"></a>Biztonsági mentés és megőrzés

Az Azure Backup *védett példányonként* 9999 helyreállítási pontos felső határral rendelkezik. Ezeket a helyreállítási pontokat biztonsági másolatoknak, illetve pillanatképeknek is hívják.

- A védett példányok olyan számítógépek, kiszolgálók (fizikai vagy virtuális) vagy számítási feladatok, amelyek úgy vannak konfigurálva, hogy biztonsági mentést végezzenek az Azure-ba. Az egyes példányok védelméhez biztonsági másolatot kell készíteni az adatokról.
- Az adatok biztonsági másolata maga a védelem. Ha a forrásadatok elvesznek vagy sérülnek, a biztonsági másolatból vissza lehet állítani azokat.

Az alábbi táblázat az egyes összetevők maximális biztonsági mentési gyakoriságát mutatja be. A biztonsági mentési szabályzat beállítása határozza meg, hogy milyen gyorsan használja fel a helyreállítási pontokat. Ha például minden nap létrehoz egy helyreállítási pontot, akkor 27 év után fogynának el a helyreállítási pontjai. Ha havonta csak egy helyreállítási pontot használ fel, akkor helyreállítási pontjai 833 év után fogynának el. A Backup szolgáltatás nem társít lejárati időt a helyreállítási pontokhoz.

|  | Az Azure Backup ügynöke | System Center DPM | Azure Backup Server | Azure IaaS virtuális gép biztonsági mentése |
| --- | --- | --- | --- | --- |
| Biztonsági mentés gyakorisága<br/> (a Recovery Services-tárolóba) |Napi három biztonsági mentés |Napi két biztonsági mentés |Napi két biztonsági mentés |Napi egy biztonsági mentés |
| Biztonsági mentés gyakorisága<br/> (lemezre) |Nem alkalmazható |15 percenként az SQL Serverhez<br/><br/> Minden órában más számítási feladatokhoz |15 percenként az SQL Serverhez<br/><br/> Minden órában más számítási feladatokhoz |Nem alkalmazható |
| Megőrzési beállítások |Napi, heti, havi, éves |Napi, heti, havi, éves |Napi, heti, havi, éves |Napi, heti, havi, éves |
| Helyreállítási pontok maximális száma védett példányonként |9999|9999|9999|9999|
| Maximális megőrzési időtartam |A biztonsági mentés gyakoriságától függően változik |A biztonsági mentés gyakoriságától függően változik |A biztonsági mentés gyakoriságától függően változik |A biztonsági mentés gyakoriságától függően változik |
| Helyreállítási pontok a helyi lemezen |Nem alkalmazható | 64 fájlkiszolgálók esetén<br/><br/> 448 alkalmazáskiszolgálókhoz | 64 fájlkiszolgálók esetén<br/><br/> 448 alkalmazáskiszolgálókhoz |Nem alkalmazható |
| Helyreállítási pontok a szalagon |Nem alkalmazható |Korlátlan |Nem alkalmazható |Nem alkalmazható |

## <a name="how-does-azure-backup-work-with-encryption"></a>Hogyan működik Azure Backup a titkosítás?

**Titkosítás** | **Helyszíni biztonsági mentés** | **Azure-beli virtuális gépek biztonsági mentése** | **Az SQL biztonsági mentése Azure-beli virtuális gépeken**
--- | --- | --- | ---
Titkosítás inaktív állapotban<br/> (Az adattitkosítás, ahol a szolgáltatás megőrzött/tárolva van) | Az ügyfél által megadott hozzáférési kód az adattitkosításra szolgál | Az Azure [Storage Service encryption (SSE)](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) a tárolóban tárolt adattitkosításra szolgál.<br/><br/> A Backup automatikusan titkosítja az adataikat a tárolás előtt. Az Azure Storage visszafejti az adatok beolvasása előtt. Az SSE-hez az ügyfél által felügyelt kulcsok használata jelenleg nem támogatott.<br/><br/> Az [Azure Disk Encryption (ADE)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) használatával biztonsági mentést készíthet az operációs rendszer és az adatlemezek titkosítására szolgáló virtuális gépekről. Azure Backup támogatja a csak BEK-vel titkosított virtuális gépeket, valamint a BEK és a [KEK](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/)-et is. Tekintse [](backup-azure-vms-encryption.md#encryption-support)át a korlátozásokat. | Azure Backup támogatja SQL Server adatbázisok vagy kiszolgálók biztonsági mentését, ha a [TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) engedélyezve van. A Backup támogatja a TDE az Azure által kezelt kulcsokkal, illetve az ügyfél által felügyelt kulcsokkal (BYOK).<br/><br/> A biztonsági mentés nem végez SQL-titkosítást a biztonsági mentési folyamat részeként.
Titkosítás átvitel közben<br/> (Az egyik helyről a másikra áthelyezett adatok titkosítása) | Az adattitkosítás a AES256 használatával történik, és az Azure-beli tárolóba küldi a HTTPS protokollon keresztül | Az Azure-on belül az Azure Storage és a tároló közötti adatvédelmet HTTPS védi. Ezek az adatközpontok az Azure gerinc hálózatán maradnak.<br/><br/> A fájlok helyreállításakor az iSCSI gondoskodik a tároló és az Azure-beli virtuális gép között továbbított adatok védelméről. A biztonságos bújtatás védi az iSCSI-csatornát. | Az Azure-on belül az Azure Storage és a tároló közötti adatvédelmet HTTPS védi.<br/><br/> A fájl helyreállítása nem vonatkozik az SQL-re.

## <a name="next-steps"></a>További lépések

- [Tekintse át](backup-architecture.md) a különböző biztonsági mentési forgatókönyvek architektúráját és összetevőit.
- [Ellenőrizze](backup-support-matrix.md) a biztonsági mentés támogatási követelményeit és korlátozásait, valamint az [Azure virtuális gépek biztonsági mentését](backup-support-matrix-iaas.md).

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png
