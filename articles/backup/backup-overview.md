---
title: Mi az az Azure Backup?
description: Az Azure Backup szolgáltatással, és hogyan helyezhet üzembe (az üzleti folytonossági és vészhelyreállítási BCDR) stratégia részeként áttekintést nyújt.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 02/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: d1debbcc8f225a0d4608d67b19e5e00aca580ce1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58122012"
---
# <a name="what-is-azure-backup"></a>Mi az az Azure Backup?

Az Azure Backup szolgáltatás a Microsoft Azure felhőbe készít biztonsági adatokat. Képes biztonsági másolatot készíteni a helyszíni gépek és a számítási feladatokat, és az Azure-beli virtuális gépek (VM).


## <a name="why-use-azure-backup"></a>Miért érdemes az Azure Backupot használni?

Az Azure Backup legfontosabb értékelemeket nyújtja:

- **A helyszíni biztonsági mentés kiürítési**: Az Azure Backup egy egyszerű megoldást a helyszíni erőforrások biztonsági mentéséről a felhőbe kínál. Első rövid és hosszú távú biztonsági mentés üzembe helyezése összetett nélkül a helyszíni biztonsági mentési megoldások. 
- **Azure IaaS virtuális gépek biztonsági mentése**: Az Azure Backup az eredeti adatok véletlen megsemmisítése ellen védő független és elkülönített biztonsági mentések biztosít. Biztonsági mentések egy Recovery Services-tároló beépített helyreállítási pontok felügyelt vannak tárolva. Konfiguráció és a méretezhetőség egyszerű, biztonsági mentések vannak optimalizálva, és igény szerint könnyedén visszaállíthatók.
- **Könnyedén méretezheti a** – az Azure Backup a mögöttes teljesítményét és korlátlan méretezhetőségét az Azure-felhő használ a magas rendelkezésre állású megoldásokat fejleszthet a karbantartás és a figyelés többletterhe. 
- **Korlátlan adatátvitel első** – az Azure Backup nem korlátozza a bejövő vagy kimenő adatok átvitele, vagy a célkiszolgálóra továbbított adatokat számítunk fel díjat.
    - Kimenő adatokon a Recovery Services-tárolóból a visszaállítási művelet során átvitt adatok értendők.
    - Az Azure Import/Export szolgáltatás használatával nagy mennyiségű adat importálása offline kezdeti biztonsági mentést hajt végre, ha nincs a bejövő adatok költséggel.  [További információk](backup-azure-backup-import-export.md). 
- **A biztonságos**: Adatok titkosítása lehetővé teszi a biztonságos átvitelét és tárolását az adatok a nyilvános felhőben. A titkosítás jelszavát helyileg tárolja, és soha nem kerül az Azure-ba, illetve nincs ott tárolva. Ha vissza kell állítani valamilyen adatot, csak Ön rendelkezik a titkosítási jelszóval vagy a kulccsal.
- **Alkalmazáskonzisztens biztonsági másolatok beolvasása**: Az alkalmazáskonzisztens biztonsági mentés azt jelenti, hogy a helyreállítási pont rendelkezik az összes szükséges adattal a biztonsági másolat visszaállításához. Az Azure Backup olyan alkalmazáskonzisztens biztonsági mentést nyújt, amely garantálja, hogy ne legyen szükség további javításokra az adatok visszaállításához. Az alkalmazáskonzisztens adatok visszaállítása rövidebb idő alatt végrehajtható, így gyorsan visszatérhet egy működőképes állapotba.
- **Rövid és hosszú távú megőrzése**: Recovery Services-tárolókat rövid és hosszú távú adatmegőrzés is használhat. Az Azure nem korlátozza az adatok megőrzési idejét a Recovery Services-tárolókban. Mindaddig, amíg Ön, mint a tárolhatja. Az Azure Backup védett példányonként 9999 helyreállítási pontos felső határral rendelkezik. [További](backup-introduction-to-azure-backup.md#backup-and-retention)kapcsolatban, hogy ezt a korlátot milyen hatással van a biztonsági mentési igényeire.
- **Automatikus tárolófelügyelet** – A hibrid környezetek gyakran megkövetelik a heterogén tárhely alkalmazását – valamennyi tárhely szükséges a helyszínen, valamennyi a felhőben. Az Azure Backup szolgáltatással nem kell költenie helyszíni tárolóeszközökre. Az Azure Backup automatikusan foglalja le és kezeli a biztonsági mentési tárolót, és a egy használatalapú mint-akkor-használható modellt használ, hogy csak a kell fizetnie a felhasznált tárterületet. [További](https://azure.microsoft.com/pricing/details/backup) információ a díjszabásról.
- **Többféle tárolási lehetőség** – az Azure Backup két replikációtípust nyújt a tárolás és adatok magas rendelkezésre állású.
    - [Helyileg redundáns tárolás (LRS)](../storage/common/storage-redundancy-lrs.md) replikálja az adatokat három alkalommal (hozza létre az adatok három másolatát) egy adatközpontban lévő tárolóskálázási egységben. Az adatok összes másolata ugyanabban a régióban található. Az LRS egy alacsony költségű megoldás az adatok védelmére a helyi hardveres hibák esetén.
    - [Georedundáns tárolás (GRS)](../storage/common/storage-redundancy-grs.md) az alapértelmezett és ajánlott Replikációs lehetőség. A GRS az adatait egy másodlagos régióba replikálja (több száz kilométerre a forrásadatok elsődleges helyétől). A GRS módszer költségesebb, mint az LRS, de adatainak megőrzését magasabb szinten biztosítja, még regionális szolgáltatáskiesés esetére is.


## <a name="whats-the-difference-between-azure-backup-and-azure-site-recovery"></a>Mi a különbség az Azure Backup és az Azure Site Recovery?

Az Azure Backup és az Azure Site Recovery services egy üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégia az hozzájárulhat az üzleti. BCDR két széleskörű célok áll:

- Tartsa az üzleti adatok biztonságáról és helyreállíthatóságáról gondoskodik leállások esetén.
- Bízhatja az alkalmazások és számítási feladatok tervezett és nem tervezett leállás során.

Mindkét szolgáltatás különböző, de egymást kiegészítő funkciót kínál.

- **Az Azure Site Recovery**: Site Recovery egy vész-helyreállítási megoldást biztosít, a helyszíni gépek és az Azure virtuális gépek. Replikálhat gépeket az elsődleges helyről egy másodlagos. Ha katasztrófa utáni feladatokat, gépek átadja a feladatokat a másodlagos helyen, és ahonnan érheti el őket. Ha minden rendben működő általában újra, hogy sikertelen gépek vissza az elsődleges helyen helyreállítani őket.
- **Az Azure Backup**: Az Azure Backup szolgáltatás a helyszíni gépek és Azure-beli virtuális lemezein lévő adatokat. Adatok biztonsági mentése, illetve hogy olyan részletességgel, beleértve a fájlok, mappák, a gép rendszerállapot biztonsági mentése és alkalmazás-felismerésre képes az adatok biztonsági mentése. Az Azure Backup az adatokat, mint a Site Recovery egy részletesebb szinten kezeli. Például ha laptopon lévő bemutató megsérült, használhatja az Azure Backup állíthatja vissza a bemutatót. Ha meg szeretné tartani a virtuális gép konfigurációs és adatok, biztonságos és elérhető-e, a Site Recovery használhatja.  

A tábla pontok használatával segít azonosítani a BCDR-igényeinek. 

**Objective** | **Részletek** | **Összehasonlítás**
--- | --- | --- 
**Adatok biztonsági mentése és megőrzése** | Biztonsági mentési adatok őrződnek meg, illetve tárolt napig, hónapok vagy évek még ha a megfelelőség szempontjából szükséges. | Például az Azure Backup biztonsági mentési megoldások lehetővé teszik, hogy válasszon a készíteni kívánt adatokat, és hogy a biztonsági mentési és adatmegőrzési szabályzatok hangolása céljából.<br/><br/> A Site Recovery nem teszi lehetővé az azonos finomhangolása.
**Helyreállítási időkorlát (RPO)** | Az elfogadható adatveszteség mennyisége, ha helyreállítást kell végezni. | Biztonsági másolatok több változó RPO rendelkezik.<br/><br/> Virtuális gép biztonsági mentéseinek általában rendelkeznek egy nap az RPO, míg adatbázisok biztonsági mentései rpo-k akár 15 percet is.<br/><br/> A Site recoveryben alacsony RPO-e a replikáció folyamatos vagy gyakori, úgy, hogy a különbözeti között a forrás és a replika másolás kis óta.
**A helyreállítási időre vonatkozó célkitűzés (RTO)** |A helyreállítás vagy visszaállítás elvégzéséhez szükséges idő. | A nagyobb RPO miatt a biztonsági mentési megoldások által feldolgozandó adatmennyiség általában sokkal nagyobb, ami hosszabb RTO-khoz vezet. Napokba telhet például az adatok szalagokról való visszaállítása attól függően, hogy mennyi ideig tart a szalag szállítása egy külső helyről. 

## <a name="what-backup-scenarios-are-supported"></a>Milyen biztonsági mentési forgatókönyvek támogatottak?

Az Azure Backup a helyszíni gépek és Azure virtuális gépek készíthető.

**Gép** | **Készítsen biztonsági másolatot a forgatókönyv**
--- | ---
**A helyszíni biztonsági mentés** |  1.) futtassa az Azure Backup a Microsoft Azure Recovery Services (MARS) ügynök a helyi Windows-gépek egyedi fájlokat és a rendszerállapot biztonsági mentése. <br/><br/>2.) biztonsági mentése a helyszíni gépek biztonsági mentési kiszolgálóra (a System Center Data Protection Manager (DPM) vagy a Microsoft Azure Backup Server (MABS)), és konfigurálja a biztonsági mentési kiszolgálóra, egy Azure Backup helyreállítási tárba történő biztonsági mentés az Azure-ban.
**Az Azure virtuális gépek** | 1.) a biztonsági mentés engedélyezése az egyes Azure virtuális gépek. Ha engedélyezi a biztonsági mentés, az Azure Backup egy kiterjesztést telepít az Azure-beli Virtuálisgép-ügynök, amely a virtuális gép fut-e. Az ügynök teljes virtuális gép biztonsági mentését.<br/><br/> 2.) futtassa a MARS-ügynök egy Azure virtuális Gépen. Ez akkor hasznos, ha egyes fájlok és mappák biztonsági mentése a virtuális gépen.<br/><br/> 3.) biztonsági mentése Azure virtuális gép egy DPM-kiszolgáló vagy az Azure-ban futó MABS. Használja az Azure Backup-tárolóba, majd biztonsági mentése a DPM-kiszolgáló/MABS. 


## <a name="why-use-a-backup-server"></a>Miért érdemes használni a biztonsági mentési kiszolgálóra?




Biztonsági másolatot a gépek és alkalmazások MABS/DPM-tároló, majd biztonsági mentése a DPM/MABS storage-tárolóba előnyei a következők:

- MABS/DPM biztonsági mentést biztosít alkalmazás együttműködő biztonsági másolat készíthető, a gyakori alkalmazások, például az SQL Server, Exchange és SharePoint-optimalizált további fájl/mappa-/ kötetszintű biztonsági mentések és a gép a rendszerállapot biztonsági mentésének (operációs rendszer nélküli, rendszerállapot).
- A helyszíni gépek esetében nem kell a MARS-ügynök telepítése az összes olyan számítógépen, készítsen biztonsági másolatot szeretne. Egyes gépek fut a DPM/MABS védelmi ügynököt, és a MARS-ügynök fut, a MABS és a DPM csak.
- Nagyobb rugalmasságot és részletes ütemezési beállításait a biztonsági mentések futtatása van.
- Biztonsági másolatok több gép, amely egyetlen konzolon védelmi csoportokba rendezheti is kezelheti. Ez akkor különösen hasznos, ha alkalmazásokat több gépen keresztül számítógépen rétegzett, és szeretne róluk biztonsági másolatot együtt.

Tudjon meg többet [hogyan biztonsági mentési works](backup-architecture.md#architecture-back-up-to-dpmmabs) használatakor egy biztonsági mentési kiszolgálóra, és a [memóriakonfigurációt](backup-support-matrix-mabs-dpm.md) biztonságimásolat-kiszolgálók.

## <a name="what-can-i-back-up"></a>Mi is biztonsági másolatot?

**Gép** | **Biztonsági mentési módszer** | **Biztonsági mentés**
--- | --- | ---
**A helyszíni Windows virtuális gépek** | Futtassa a MARS-ügynök | Készítsen biztonsági másolatot a fájlok, mappák, a rendszer állapotát.<br/><br/> Linux rendszerű gépek nem támogatott.
**A helyszíni gépek** | Biztonsági mentés a DPM/MABS | Készítsen biztonsági mentést, amelyeket által védett [DPM](backup-support-matrix-mabs-dpm.md#supported-backups-to-dpm) vagy [MABS](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs), például fájlok/mappák és megosztások vagy kötetek és alkalmazás-specifikus adatait. 
**Az Azure virtuális gépek** | Tartalék mellék futtatási Azure VM agent | Készítsen biztonsági másolatot a teljes virtuális gép
**Az Azure virtuális gépek** | Futtassa a MARS-ügynök | Készítsen biztonsági másolatot a fájlok, mappák, a rendszer állapotát.<br/><br/> Linux rendszerű gépek nem támogatott.
**Az Azure virtuális gépek** | Az Azure-ban futó MABS/DPM biztonsági mentése | Készítsen biztonsági mentést, amelyeket által védett [MABS](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs) vagy [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807) fájlok/mappák és megosztások vagy kötetek és alkalmazás-specifikus adatokat is beleértve.

## <a name="what-backup-agents-do-i-need"></a>Milyen backup-ügynököket kell?

**Forgatókönyv** | **Ügynök** 
--- | --- 
**Azure-beli virtuális gépek biztonsági mentése** | Nincs ügynök szükséges. Biztonsági másolat az Azure VM-bővítmény telepítve van az Azure virtuális Gépen az Azure virtuális gép első biztonsági mentés futtatásakor.<br/><br/> Támogatás a Windows és Linux-támogatás.
**A helyi Windows-gépek biztonsági mentése** | Letöltéséhez, telepítéséhez és a MARS-ügynök futtatása közvetlenül a gépen. 
**A MARS-ügynök biztonsági mentési Azure virtuális gépek** | Letöltése, telepítése és a MARS-ügynök futtatása közvetlenül a gépen. A MARS-ügynök mellett a biztonsági mentési bővítményt is futtatható.
**Biztonsági másolatot készíteni a helyszíni gépek és Azure virtuális gépek DPM/MABS** | A DPM- vagy MABS védelmi ügynököt a védeni kívánt gépeket futtat. A DPM kiszolgáló/MABS biztonsági mentése az Azure-bA a MARS-ügynök fut.

## <a name="which-backup-agent-should-i-use"></a>Mely biztonságimásolat-készítő ügynök használjam?

**Biztonsági mentés** | **Megoldás** | **Korlátozás**
--- | --- | ---
**Biztonsági mentése Azure virtuális gép teljes szeretnék** | A virtuális gép biztonsági mentésének engedélyezése. A biztonsági mentési bővítményt automatikusan konfigurálják a Windows vagy Linux rendszerű Azure virtuális Gépen. | Teljes virtuális gép biztonsági mentése <br/><br/> Windows virtuális gépek alkalmazáskonzisztens a biztonsági mentés. Linux esetén a biztonsági mentés: fájlkonzisztens. Ha alkalmazás-kompatibilis kell Linux rendszerű virtuális gépek konfigurálhatja ezt az egyéni parancsfájlokkal rendelkezik.
**Tudok biztonsági másolatot kíván adott fájlok és mappák Azure virtuális gépen** | A MARS-ügynököt a virtuális gép üzembe helyezése.
**A helyi Windows-számítógépek közvetlenül biztonsági** | A MARS-ügynök telepítése a gépen. | Az Azure-ba is készítsen biztonsági másolatot a fájlok, mappák és rendszerállapot. Biztonsági mentések nem alkalmazás-kompatibilis.
**Szeretném közvetlenül biztonsági másolatot készíteni a helyszíni Linuxos gépek** | Kell telepíteni a DPM- vagy MABS biztonsági mentése az Azure-bA.
**Készítsen biztonsági másolatot a helyszínen futó alkalmazások szeretnék** | Alkalmazás együttműködő biztonsági másolat készíthető a gépek DPM- vagy MABS kell védeni.
**Szeretném, hogy az Azure virtuális gépek részletes és rugalmas biztonsági mentési és helyreállítási beállításai** | A MABS és DPM biztonsági mentési ütemezés további rugalmasságának biztosítása érdekében, és a teljes rugalmasság a védelmét, és fájlokat, mappa, kötet, alkalmazások és rendszerállapot visszaállítása az Azure-ban futó Azure virtuális gépek védelmét.


## <a name="next-steps"></a>További lépések

- [Felülvizsgálat](backup-architecture.md) az architektúra és összetevők különböző biztonsági mentési forgatókönyveket.
- [Győződjön meg arról](backup-support-matrix.md) szolgáltatásainak és beállításainak biztonsági mentés esetén támogatott.

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png

