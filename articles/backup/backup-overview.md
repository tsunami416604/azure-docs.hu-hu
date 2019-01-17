---
title: Mi az az Azure Backup?
description: Az Azure Backup szolgáltatással, és hogyan helyezhet üzembe (az üzleti folytonossági és vészhelyreállítási BCDR) stratégia részeként áttekintést nyújt.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 01/09/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 7156042243b1ba28cea712dc3722600b9fc46c42
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360219"
---
# <a name="what-is-azure-backup"></a>Mi az az Azure Backup?

Az Azure Backup szolgáltatás a Microsoft Azure felhőbe készít biztonsági adatokat. Képes biztonsági másolatot készíteni a helyszíni gépek és a számítási feladatokat, és az Azure-beli virtuális gépek (VM).


## <a name="why-use-azure-backup"></a>Miért érdemes az Azure Backupot használni?

Az Azure Backup legfontosabb értékelemeket nyújtja:

- **A helyszíni biztonsági mentés kiürítési**: Az Azure Backup egy egyszerű megoldást a helyszíni erőforrások biztonsági mentéséről a felhőbe kínál. Első rövid és hosszú távú biztonsági mentés üzembe helyezése összetett nélkül a helyszíni biztonsági mentési megoldások. Szalag vagy a telephelyen kívüli biztonsági mentési szükségtelenné teszi.
- **Azure IaaS virtuális gépek biztonsági mentése**: Az Azure Backup az eredeti adatok véletlen megsemmisítése ellen védő független és elkülönített biztonsági mentések biztosít. Biztonsági mentések egy Recovery Services-tároló beépített helyreállítási pontok felügyelt vannak tárolva. Konfiguráció és a méretezhetőség egyszerű, biztonsági mentések vannak optimalizálva, és igény szerint könnyedén visszaállíthatók.
- **Könnyedén méretezheti a** – Azure Backup használja a mögöttes teljesítményét és korlátlan méretezhetőségét az Azure-felhő magas rendelkezésre állású megoldásokat fejleszthet – a karbantartás és a figyelés többletterhe. Beállíthat riasztásokat az egyes eseményekre vonatkozó információszolgáltatás céljából, de nem kell aggódnia a felhőben tárolt adatainak magas rendelkezésre állása miatt.
- **Korlátlan adatátvitel első** – az Azure Backup nem korlátozza a bejövő vagy kimenő adatok. Az Azure Backup nem számol fel díjat sem az átvitt adatokért. Azonban, ha nagy mennyiségű adat importálására az Azure Import/Export szolgáltatást használja, a bejövő adatoknak van bizonyos költségvonzatuk. Ennek költségére vonatkozóan [az offline biztonsági mentésnek az Azure Backup szolgáltatásban alkalmazott munkafolyamatát](backup-azure-backup-import-export.md) ismertető cikkben talál bővebb információt. Kimenő adatokon a Recovery Services-tárolóból a visszaállítási művelet során átvitt adatok értendők.
- **A biztonságos**: Adatok titkosítása lehetővé teszi a biztonságos átvitelét és tárolását az adatok a nyilvános felhőben. A titkosítás jelszavát helyileg tárolja, és soha nem kerül az Azure-ba, illetve nincs ott tárolva. Ha vissza kell állítani valamilyen adatot, csak Ön rendelkezik a titkosítási jelszóval vagy a kulccsal.
- **Alkalmazáskonzisztens biztonsági másolatok beolvasása**: Az alkalmazáskonzisztens biztonsági mentés azt jelenti, hogy a helyreállítási pont rendelkezik az összes szükséges adattal a biztonsági másolat visszaállításához. Az Azure Backup olyan alkalmazáskonzisztens biztonsági mentést nyújt, amely garantálja, hogy ne legyen szükség további javításokra az adatok visszaállításához. Az alkalmazáskonzisztens adatok visszaállítása rövidebb idő alatt végrehajtható, így gyorsan visszatérhet egy működőképes állapotba.
- **Rövid és hosszú távú megőrzési első**: **Hosszú távú megőrzés** – A Recovery Services-tárolókat rövid távú és hosszú távú adatmegőrzéshez használhatja. Az Azure nem korlátozza az adatok megőrzési idejét a Recovery Services-tárolókban. Tetszőleges ideig őrizheti meg az adatokat a tárolókban. Az Azure Backup védett példányonként 9999 helyreállítási pontos felső határral rendelkezik. A cikk [Biztonsági mentés és megőrzés](backup-introduction-to-azure-backup.md#backup-and-retention) című részéből megtudhatja, milyen hatással lehet ez a korlát a biztonsági mentési igényeire.
- **Automatikus tárolófelügyelet** – A hibrid környezetek gyakran megkövetelik a heterogén tárhely alkalmazását – valamennyi tárhely szükséges a helyszínen, valamennyi a felhőben. Az Azure Backup szolgáltatással nem kell költenie helyszíni tárolóeszközökre. Az Azure Backup automatikusan foglalja le és kezeli a Backup-tárolót, és használatalapú modellt alkalmaz. A használatalapú elszámolás azt jelenti, hogy csak a felhasznált tárhelyért kell fizetnie. További információkat az [Azure díjszabására vonatkozó cikkben](https://azure.microsoft.com/pricing/details/backup) talál.
- **Többféle tárolási lehetőség** – A magas rendelkezésre állás egyik megoldása a tárolóreplikáció. Az Azure Backup két replikációtípust nyújt: [helyileg redundáns tárolást](../storage/common/storage-redundancy-lrs.md) és [georedundáns tárolást](../storage/common/storage-redundancy-grs.md). A biztonságimásolat-tárolási lehetőségek közül válassza az Önnek megfelelőt:
    - A helyileg redundáns tárolás (LRS) háromszor replikálja az adatokat (az adatok három másolatát hozza létre) egy adatközpontban lévő tárolóskálázási egységben. Az adatok összes másolata ugyanabban a régióban található. Az LRS egy alacsony költségű megoldás az adatok védelmére a helyi hardveres hibák esetén.
    - Az alapértelmezett és ajánlott replikációs lehetőség a georedundáns tárolás (GRS). A GRS az adatait egy másodlagos régióba replikálja (több száz kilométerre a forrásadatok elsődleges helyétől). A GRS módszer költségesebb, mint az LRS, de adatainak megőrzését magasabb szinten biztosítja, még regionális szolgáltatáskiesés esetére is.


## <a name="whats-the-difference-between-azure-backup-and-azure-site-recovery"></a>Mi a különbség az Azure Backup és az Azure Site Recovery?

Az Azure Backup és az Azure Site Recovery services egy üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégia az hozzájárulhat az üzleti. BCDR két széleskörű célok áll:

- Tartsa az üzleti adatok biztonságáról és helyreállíthatóságáról gondoskodik leállások esetén.
- Bízhatja az alkalmazások és számítási feladatok tervezett és nem tervezett leállás során.

Mindkét szolgáltatás különböző, de egymást kiegészítő funkciót kínál.

- **Az Azure Site Recovery**: Site Recovery egy vész-helyreállítási megoldást biztosít, a helyszíni gépek és az Azure virtuális gépek. Replikálhat gépeket az elsődleges helyről egy másodlagos. Vészhelyreállítási feladatokat, amikor a szervezeti egység gépek átadja a feladatokat a másodlagos helyen, és ahonnan érheti el őket. Ha minden rendben működő általában újra, hogy sikertelen gépek biztonsági helyreállítási azokat az elsődleges helyen.
- **Az Azure Backup**: Az Azure Backup szolgáltatás a helyszíni gépek és Azure-beli virtuális lemezein lévő adatokat. Adatok biztonsági mentése, illetve hogy olyan részletességgel, többek között a biztonsági mentést, fájlok, mappák, gép rendszerállapot és alkalmazás-felismerésre képes az adatok biztonsági mentése. Az Azure Backup az adatokat, mint a Site Recovery egy részletesebb szinten kezeli. Például ha laptopon lévő bemutató megsérült, használhatja az Azure Backup állíthatja vissza a bemutatót. Ha meg szeretné tartani a virtuális gép konfigurációs és adatok, biztonságos és elérhető-e, a Site Recovery használhatja.  

A tábla pontok használatával segít azonosítani a BCDR-igényeinek. 

**Objective** | **Részletek** | **Összehasonlítás**
--- | --- | --- | --- |
**Adatok biztonsági mentése és megőrzése** | Biztonsági mentési adatok őrződnek meg, illetve tárolt napig, hónapok vagy évek még ha a megfelelőség szempontjából szükséges. | Például az Azure Backup biztonsági mentési megoldások lehetővé teszik, hogy válasszon a készíteni kívánt adatokat, és hogy a biztonsági mentési és adatmegőrzési szabályzatok hangolása céljából.<br/><br/> A Site Recovery nem teszi lehetővé az azonos finomhangolása.
**Helyreállítási időkorlát (RPO)** | Az elfogadható adatveszteség mennyisége, ha helyreállítást kell végezni. | Biztonsági másolatok több változó RPO rendelkezik.<br/><br/> Virtuális gép biztonsági mentéseinek általában rendelkeznek egy nap az RPO, míg adatbázisok biztonsági mentései rpo-k akár 15 percet is.<br/><br/> A Site recoveryben alacsony RPO-e a replikáció folyamatos vagy gyakori, úgy, hogy a különbözeti között a forrás és a replika másolás kis óta.
**A helyreállítási időre vonatkozó célkitűzés (RTO)** |A helyreállítás vagy visszaállítás elvégzéséhez szükséges idő. | A nagyobb RPO miatt a biztonsági mentési megoldások által feldolgozandó adatmennyiség általában sokkal nagyobb, ami hosszabb RTO-khoz vezet. Napokba telhet például az adatok szalagokról való visszaállítása attól függően, hogy mennyi ideig tart a szalag szállítása egy külső helyről. | Például a Site Recovery a vészhelyreállítási megoldások alacsony RPO lehet, mert a replikáció folyamatos/gyakran használják általában azt jelenti, hogy, hogy a cél a szigorúbban szinkronizálva van a forrás. |

## <a name="what-backup-scenarios-are-supported"></a>Milyen biztonsági mentési forgatókönyvek támogatottak?

Az Azure Backup a helyszíni gépek és Azure virtuális gépek készíthető.

**Gép** | **Készítsen biztonsági másolatot a forgatókönyv**
--- | ---
**A helyszíni gépek (fizikai vagy virtuális)** |  Az egyes helyszíni gépek készíthető.<br/><br/>A helyszíni gépeket a System Center Data Protection Manager (DPM) védett készíthető<br/><br/> A Microsoft Azure Backup Server (MABS) által védett a helyszíni gépek készíthető...
**Az Azure virtuális gépek** | Az egyes Azure-beli virtuális gépek készíthető.<br/><br/> Azure virtuális gépeket véd a DPM- vagy MABS készíthető.

### <a name="back-up-servers"></a>-Kiszolgálók biztonsági mentése

Előfordulhat, hogy szeretne biztonsági másolatot a helyszíni kiszolgálók és a számítási feladatok, vagy Azure virtuális gépek és felskálázhatják, először egy biztonsági mentési kiszolgálóra, majd a Recovery Services-tároló. 

**Biztonsági mentési kiszolgálóra** | **Részletek**
--- | ---
**A System Center Data Protection Manager (DPM)** | Az Azure Backup használatával biztonsági másolatot készíteni a DPM által védett adatok:<br/><br/> – A DPM a helyszínen futó lehet (fizikai vagy virtuális) vagy az Azure-ban.<br/><br/> -Védheti meg adatokat a helyszíni gépek és Azure virtuális gépeken futó elkészítheti az adatok a DPM-kiszolgáló a különböző típusú.<br/><br/> Ezután a DPM-kiszolgáló biztonsági mentésre alkalmas, egy Recovery Services-tárolót az Azure Backup szolgáltatással.<br/><br/> A DPM-kiszolgáló és az általa védett gépek ugyanabban a hálózatban kell lennie. A helyszíni gépek csak egy a helyszíni DPM-kiszolgáló védi. Ehhez hasonlóan az Azure virtuális gépek csak védi az Azure-ban futó DPM.
**A Microsoft Azure Backup server (MABS)** | Az Azure Backup segítségével a MABS által védett adatok biztonsági mentéséhez:<br/><br/> -MABS futtathat a helyszínen (fizikai vagy virtuális) vagy az Azure-ban.<br/><br/> Milyen típusú adatokat a helyszíni gépek és Azure virtuális gépeken futó elkészítheti az adatok a MABS-védelmét.<br/><br/> -Viszont MABS biztonsági mentésre alkalmas, egy Recovery Services-tárolót az Azure Backup szolgáltatással.<br/><br/> -MABS hasonló funkciókat biztosít a dpm, azzal a különbséggel, hogy a MABS használatával a szalagra történő biztonsági mentés. MABS nincs szükség a System Center-licencre.<br/><br/> A dpm a helyszíni gépek csak védi egy helyszíni MABS. Az Azure virtuális gépek az Azure-ban a MABS csak védi.

A DPM/MABS, majd tárolóba való biztonsági mentésével első előnyei a következők:

- Biztonsági mentést a DPM/MAB alkalmazás együttműködő biztonsági másolat készíthető, a gyakori alkalmazások, például az SQL Server, Exchange és SharePoint-optimalizált biztosít további fájl/mappa-/ kötetszintű biztonsági mentések és a gép a rendszerállapot biztonsági mentésének (operációs rendszer nélküli, rendszerállapot).
- Nem kell az Azure Backup ügynök telepítése az összes olyan számítógépen, készítsen biztonsági másolatot szeretne. Egyes gépek fut a DPM/MABS védelmi ügynököt, és az Azure Backup a Microsoft Azure Recovery Services ügynököt a DPM kiszolgáló/MABS csak futtat.
- Nagyobb rugalmasságot és részletes ütemezési beállításait a biztonsági mentések futtatása van.
- Biztonsági másolatok több gép, amely egyetlen konzolon védelmi csoportokba rendezheti is kezelheti. Ez akkor különösen hasznos, ha alkalmazásokat több gépen keresztül számítógépen rétegzett, és szeretne róluk biztonsági másolatot együtt.

## <a name="what-can-be-backed-up"></a>Mi is készíthető biztonsági mentés?

**Gép** | **Biztonsági mentési kiszolgálóra** | **Biztonsági mentés**
--- | --- | ---
A helyszíni Windows virtuális gépek | Nem készült biztonsági másolat a DPM- vagy MABS | Készítsen biztonsági másolatot a fájlok, mappák, a rendszer állapotát.
Azure virtuális gépek (Windows és Linux) | Nem készült biztonsági másolat a DPM- vagy MABS | Készítsen biztonsági másolatot a fájlok, mappák, a rendszer állapotát.<br/><br/> Biztonsági másolatok app-t támogató Windows-gépek és a fájl-kompatibilis Linuxos gépek.
A helyszíni virtuális gépek vagy az Azure virtuális gépek | A DPM által védett | Készítsen biztonsági mentést semmit a DPM által védett beleértve a fájlok/mappák és megosztások vagy kötetek és az alkalmazás-specifikus adatok. [Ismerje meg,](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807) milyen biztonsági mentéseket.
A helyszíni virtuális gépek vagy az Azure virtuális gépek | Védi a MABS | Készítsen biztonsági mentést tetszőleges MABS által védett többek között fájlok/mappák és megosztások vagy kötetek és az alkalmazás-specifikus adatok. [Ismerje meg,](backup-mabs-protection-matrix.md) miről MABS is biztonsági másolatot készíteni.

## <a name="what-backup-agents-do-i-need"></a>Milyen backup-ügynököket kell?
**Forgatókönyv** | **Ügynök** | **Részletek**
--- | --- | ---
A helyszíni gépek (nincs biztonsági mentési kiszolgálóra) | A Microsoft Azure Recovery Services-(MARS) ügynök a Windows-gépen futtatja. | Töltse le és telepítse a MARS-ügynök az Azure Backup üzembe helyezése során.<br/><br/> Csak a Windows-gépek támogatása.
Az Azure virtuális gépek (nincs biztonsági mentési kiszolgálóra) | Nincs explicit ügynök szükséges. Az Azure VM-bővítmény biztonsági mentés futtatása az Azure virtuális gépen. | A bővítmény telepítve van az Azure virtuális gép első biztonsági mentés futtatásakor.<br/><br/> Támogatás a Windows és Linux-támogatás.
A helyszíni gépek vagy az Azure-beli virtuális gépek DPM által védett. | A MARS-ügynök fut, a DPM-kiszolgálón. | Az egyes gépek MARS-ügynököt nem szükséges.<br/><br/> A DPM telepítésekor a DPM védelmi ügynök telepítve van az összes olyan védett számítógépen. 
A helyszíni gépek és a MABS-által védett Azure virtuális gépek biztonsági mentése | A MARS-ügynök a MABS futtat. | Az egyes gépek MARS-ügynököt nem szükséges.<br/><br/> MABS központi telepítésekor a MABS védelmi ügynök telepítve van az összes olyan védett számítógépen. 


## <a name="which-backup-agent-should-i-use"></a>Mely biztonságimásolat-készítő ügynök használjam?

**Biztonsági mentés** | **Megoldás** | **Korlátozás**
--- | --- | ---
A helyi Windows-számítógépek biztonsági szeretnék. Gépek nem védett DPM- vagy MABS | A MARS-ügynök telepítése a gépen. | Az Azure-ba is készítsen biztonsági másolatot a fájlok, mappák és rendszerállapot. Biztonsági mentések nem alkalmazás-kompatibilis.
Szeretnék helyszíni Linux rendszerű gépek biztonsági mentése. DPM- vagy MABS gépek nem védett. | Kell telepíteni a DPM- vagy MABS biztonsági mentése az Azure-bA.
El szeretne biztonsági másolatot készíteni a helyi Windows-gépeken futó alkalmazások | Alkalmazás együttműködő biztonsági másolat készíthető a Windows-gépek DPM- vagy MABS kell védeni.
Szeretném az Azure virtuális gépek biztonsági mentése | Egy Azure Backup használatával biztonsági mentést futtatni. A biztonsági mentési bővítményt automatikusan konfigurálják a Windows vagy Linux rendszerű Azure virtuális Gépen. | A Virtuálisgép-lemezek biztonsági másolat készül.<br/><br/> Windows virtuális gépek alkalmazáskonzisztens a biztonsági mentés. Linux esetén a biztonsági mentés: fájlkonzisztens. Ha alkalmazás-kompatibilis kell konfigurálhatja ezt az egyéni parancsfájlokkal rendelkezik.
Azure virtuális gépek biztonsági mentése biztonsági mentési és helyreállítási beállítások részletes rugalmas szeretnék | A DPM- vagy MABS további rugalmasságot biztosít a biztonsági mentési ütemezés és a teljes rugalmasság a védelmét, és fájlokat, mappa, kötet, alkalmazások és rendszerállapot visszaállítása az Azure-ban futó Azure virtuális gépek védelmét.


## <a name="next-steps"></a>További lépések

- [Felülvizsgálat](backup-architecture.md) az architektúra és összetevők különböző biztonsági mentési forgatókönyveket.
- [Győződjön meg arról](backup-support-matrix.md) szolgáltatásainak és beállításainak biztonsági mentés esetén támogatott.

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png

