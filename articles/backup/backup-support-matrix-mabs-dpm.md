---
title: A Microsoft Azure Backup-kiszolgáló és a System Center DPM támogatási mátrixa
description: Ez a cikk Azure Backup támogatást összegzi, ha Microsoft Azure Backup-kiszolgálót vagy System Center-DPM használ a helyszíni és az Azure-beli virtuális gépek erőforrásainak biztonsági mentésére.
author: dcurwin
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: dacurwin
manager: carmonm
ms.openlocfilehash: 7f890ddf7aff63189a720f3d604b00610af7a933
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949848"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Támogatási mátrix a Microsoft Azure Backup Server vagy System Center DPM való biztonsági mentéshez

A [Azure Backup szolgáltatás](backup-overview.md) segítségével biztonsági mentést készíthet a helyszíni gépekről és munkaterhelésekről, valamint az Azure-beli virtuális gépekről. Ez a cikk az Microsoft Azure Backup Server (MABS) vagy a System Center Data Protection Manager (DPM) és a Azure Backup használatával ismerteti a gépek biztonsági mentésének támogatási beállításait és korlátozásait.

## <a name="about-dpmmabs"></a>Tudnivalók a DPM/MABS

A [System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807) egy vállalati megoldás, amely a vállalati gépek és az adatmennyiségek biztonsági mentésének és helyreállításának konfigurálását, megkönnyítését és felügyeletét végzi. A [System Center](https://www.microsoft.com/cloud-platform/system-center-pricing) programcsomag részét képezi.

A MABS egy olyan kiszolgálói termék, amely a rajtuk futó helyszíni fizikai kiszolgálók, virtuális gépek és alkalmazások biztonsági mentésére használható.

A MABS a System Center DPM alapul, és hasonló funkcionalitást biztosít néhány különbséggel:
- A MABS futtatásához nincs szükség System Center-licencre.
- A MABS és a DPM esetében az Azure hosszú távú biztonsági mentési tárolót biztosít. Emellett a DPM lehetővé teszi az adatok biztonsági mentését a szalagos hosszú távú tároláshoz. A MABS nem biztosítja ezt a funkciót.

A MABS a [Microsoft letöltőközpontból](https://www.microsoft.com/en-us/download/details.aspx?id=57520)töltheti le. Helyszíni vagy Azure-beli virtuális gépen is futtatható.

A DPM és a MABS számos alkalmazás, valamint a kiszolgáló-és ügyféloldali operációs rendszerek biztonsági mentését támogatja. Több biztonsági mentési forgatókönyvet biztosítanak:

- A számítógép szintjén biztonsági mentést készíthet a rendszerállapot vagy az operációs rendszer nélküli biztonsági mentés használatával.
- Biztonsági mentést készíthet bizonyos kötetekről, megosztásokról, mappákról és fájlokról.
- Az optimalizált, app-Aware beállítások használatával biztonsági mentést készíthet az egyes alkalmazásokról.

## <a name="dpmmabs-backup"></a>DPM/MABS biztonsági mentés

A DPM/MABS és a Azure Backup használatával történő biztonsági mentés a következőképpen működik:

1. A DPM/MABS védelmi ügynök telepítve van minden olyan gépen, amelyről biztonsági mentés készül.
1. A gépek és alkalmazások biztonsági mentése a helyi tárolóba történik a DPM/MABS.
1. A Microsoft Azure Recovery Services (MARS) ügynök telepítve van a DPM-kiszolgálón/MABS.
1. A MARS-ügynök biztonsági mentést készít a DPM-vagy MABS-lemezekről az Azure-beli biztonsági mentési Recovery Services-tárolóba Azure Backup használatával.

További információk:

- [További](backup-architecture.md#architecture-back-up-to-dpmmabs) információ a MABS architektúráról.
- Tekintse át a MARS-ügynök [által támogatott](backup-support-matrix-mars-agent.md) tudnivalókat.

## <a name="supported-scenarios"></a>Támogatott esetek

**Forgatókönyv** | **Ügynök** | **Location**
--- | --- | ---
**Helyszíni gépek/munkaterhelések biztonsági mentése** | A DPM/MABS védelmi ügynök azon gépeken fut, amelyekről biztonsági másolatot szeretne készíteni.<br/><br/> A MARS-ügynök a DPM/MABS-kiszolgálón.<br/> A szolgáltatás engedélyezéséhez szükséges Microsoft Azure Recovery Services ügynök vagy Azure Backup ügynök minimális verziója 2.0.8719.0.  | A DPM/MABS a helyszínen kell futnia.


## <a name="supported-deployments"></a>Támogatott központi telepítések

A DPM/MABS a következő táblázatban összefoglalt módon helyezhető üzembe.

**Üzembe helyezés** | **Támogatás** | **Részletek**
--- | --- | ---
**Helyszíni üzembe helyezés** | Fizikai kiszolgáló<br/><br/>Hyper-V virtuális gép<br/><br/> VMware virtuális gép | Ha a DPM/MABS VMware virtuális gépként van telepítve, csak a virtuális gépeken futó VMware virtuális gépekről és munkaterhelésekről készít biztonsági mentést.
**Azure Stack virtuális gépként van üzembe helyezve** | Csak MABS | A DPM nem használható Azure Stack virtuális gépek biztonsági mentésére.
**Azure-beli virtuális gépként telepítve** | Az ezen a virtuális gépeken futó Azure-beli virtuális gépeket és munkaterheléseket védi | Az Azure-ban futó DPM/MABS nem tud biztonsági másolatot készíteni a helyszíni gépekről.


## <a name="supported-mabs-and-dpm-operating-systems"></a>Támogatott MABS és DPM operációs rendszerek

Azure Backup a következő operációs rendszerek bármelyikét futtató DPM-vagy MABS-példányok biztonsági mentését végezheti el. Az operációs rendszernek a legújabb szervizcsomagokat és frissítéseket kell futtatnia.

**Forgatókönyv** | **DPM/MABS**
--- | ---
**MABS egy Azure-beli virtuális gépen** | Windows Server 2012 R2.<br/><br/> Windows 2016 Datacenter.<br/><br/> Windows 2019 Datacenter.<br/><br/> Javasoljuk, hogy indítson el egy rendszerképet a piactéren.<br/><br/> Minimális a2 standard két maggal és 3,5 GB RAM-mal.
**DPM egy Azure-beli virtuális gépen** | System Center 2012 R2, 3. frissítéssel vagy újabb verzióval.<br/><br/> Windows operációs rendszer a [System Center által igényelt](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server)módon.<br/><br/> Javasoljuk, hogy indítson el egy rendszerképet a piactéren.<br/><br/> Minimális a2 standard két maggal és 3,5 GB RAM-mal.
**Helyszíni MABS** | Támogatott 64 bites operációs rendszerek:<br/><br/> MABS v3 és újabb verziók: Windows Server 2019 (standard, Datacenter, Essentials). <br/><br/> MABS v2 és újabb verziók: Windows Server 2016 (standard, Datacenter, Essentials).<br/><br/> Minden MABS-verzió: Windows Server 2012 R2.<br/><br/>Minden MABS-verzió: Windows Storage Server 2012 R2.
**Helyszíni DPM** | Fizikai kiszolgáló vagy Hyper-V virtuális gép: System Center 2012 SP1 vagy újabb.<br/><br/> VMware virtuális gép: System Center 2012 R2, 5. frissítéssel vagy újabb verzióval.



## <a name="management-support"></a>Kezelési támogatás

**Kérdés** | **Részletek**
--- | ---
**Telepítés** | Telepítse a DPM/MABS egyetlen célra szolgáló gépre.<br/><br/> Ne telepítse a DPM/MABS-t egy tartományvezérlőre, egy olyan gépre, amelyen telepítve van az Alkalmazáskiszolgáló szerepkör, egy Microsoft Exchange Servert vagy System Center Operations Managert futtató gépen vagy egy fürtcsomóponton.<br/><br/> [Tekintse át az összes DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server)rendszerkövetelményt.
**Tartomány** | A DPM/MABS tartományhoz kell csatlakoznia. Először telepítse, majd csatlakoztassa a DPM/MABS-t egy tartományhoz. A DPM/MABS új tartományba való áthelyezése az üzembe helyezést követően nem támogatott.
**Storage** | A modern Backup Storage (MBS) a DPM 2016/MABS v2 és újabb verziók esetében támogatott. A MABS v1 esetében nem érhető el.
**MABS-frissítés** | Közvetlenül telepítheti a MABS v3 verziót, vagy frissíthet a MABS v3 verzióra a MABS v2-ről. [További információk](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**MABS áthelyezése** | Ha a MABS egy új kiszolgálóra helyezi át, a tárterület megőrzése mellett az MBS használata támogatott.<br/><br/> A kiszolgálónak ugyanazzal a névvel kell rendelkeznie, mint az eredetinek. A név nem módosítható, ha ugyanazt a tárolót szeretné megőrizni, és ugyanazt a MABS-adatbázist használja az adathelyreállítási pontok tárolásához.<br/><br/> Szüksége lesz egy biztonsági másolatra a MABS-adatbázisról, mert vissza kell állítania.


## <a name="mabs-support-on-azure-stack"></a>MABS-támogatás Azure Stack

A MABS üzembe helyezhető egy Azure Stack virtuális gépen, így egyetlen helyről kezelheti Azure Stack virtuális gépek és munkaterhelések biztonsági mentését.

**Összetevő** | **Részletek**
--- | ---
**MABS Azure Stack virtuális gépen** | Legalább a2-es méret. Javasoljuk, hogy kezdjen egy Windows Server 2012 R2 vagy Windows Server 2016 rendszerképpel az Azure Marketplace-ről.<br/><br/> A MABS virtuális gépen ne telepítsen semmilyen mást.
**MABS-tároló** | Használjon különálló Storage-fiókot a MABS virtuális géphez. A MABS-on futó MARS-ügynöknek ideiglenes tárterületre van szüksége a gyorsítótár helyéhez, valamint a felhőből visszaállított adatok tárolására.
**MABS Storage-készlet** | Az MABS-tároló méretét a MABS virtuális géphez csatolt lemezek száma és mérete határozza meg. Minden Azure Stack virtuálisgép-méret maximális számú lemezzel rendelkezik. Az a2 például négy lemez.
**MABS megőrzése** | A helyi MABS-lemezeken több mint öt napig ne őrizzen meg biztonsági mentést.
**MABS vertikális felskálázás** | Az üzemelő példány vertikális felskálázásához növelheti a MABS virtuális gép méretét. Például megváltoztathatja az A – D sorozatot.<br/><br/> Azt is megteheti, hogy az Azure-ban rendszeresen biztonsági mentést készít az Azure-ba. Szükség esetén további MABS-kiszolgálókat is telepíthet.
**.NET-keretrendszer a MABS-on** | A MABS virtuális gépnek a .NET-keretrendszer 3,3 SP1 vagy újabb verziójára van szüksége.
**MABS tartomány** | A MABS virtuális gépnek csatlakoznia kell egy tartományhoz. A rendszergazdai jogosultsággal rendelkező tartományi felhasználóknak telepíteniük kell a MABS a virtuális gépre.
**Azure Stack virtuális gép biztonsági mentése** | Biztonsági mentést készíthet a fájlokról, mappákról és alkalmazásokról.
**Támogatott biztonsági mentés** | Ezeket az operációs rendszereket olyan virtuális gépek támogatják, amelyekről biztonsági másolatot szeretne készíteni:<br/><br/> Windows Server féléves csatorna (Datacenter, Enterprise, standard)<br/><br/> Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2
**Azure Stack virtuális gépek SQL Server támogatása** | SQL Server 2016, SQL Server 2014, SQL Server 2012 SP1 biztonsági mentése.<br/><br/> Adatbázis biztonsági mentése és helyreállítása.
**SharePoint-támogatás Azure Stack virtuális gépekhez** | SharePoint 2016, SharePoint 2013, SharePoint 2010.<br/><br/> Egy farm, adatbázis, előtér és webkiszolgáló biztonsági mentése és helyreállítása.
**A virtuális gépek biztonsági mentésének hálózati követelményei** | Azure Stack munkaterhelésben lévő összes virtuális gépnek ugyanahhoz a virtuális hálózathoz kell tartoznia, és ugyanahhoz az előfizetéshez kell tartoznia.

## <a name="dpmmabs-networking-support"></a>DPM/MABS hálózatkezelési támogatás

### <a name="url-access"></a>URL-hozzáférés

A DPM-kiszolgálónak/MABS hozzá kell férnie az alábbi URL-címekhez:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="dpmmabs-connectivity-to-azure-backup"></a>DPM/MABS-kapcsolat Azure Backup

A biztonsági mentések megfelelő működéséhez szükség van a Azure Backup szolgáltatáshoz való kapcsolódásra, az Azure-előfizetésnek pedig aktívnak kell lennie. A következő táblázat a viselkedést mutatja be, ha ez a két dolog nem fordul elő.

**MABS az Azure-ba** | **Előfizetés** | **Biztonsági mentés/visszaállítás**
--- | --- | ---
Csatlakozva | Aktív | Biztonsági mentés DPM-vagy MABS-lemezre.<br/><br/> Biztonsági mentés az Azure-ba.<br/><br/> Visszaállítás a lemezről.<br/><br/> Visszaállítás az Azure-ból.
Csatlakozva | Lejárt/kiépítés | Nincs biztonsági másolat a lemezre vagy az Azure-ra.<br/><br/> Ha az előfizetés lejárt, a lemezről vagy az Azure-ból állíthatja vissza.<br/><br/> Ha az előfizetés le van szerelve, a lemezről vagy az Azure-ból nem lehet visszaállítani. Az Azure helyreállítási pontok törlődnek.
Több mint 15 napja nincs kapcsolat | Aktív | Nincs biztonsági másolat a lemezre vagy az Azure-ra.<br/><br/> A lemezről vagy az Azure-ból is visszaállítható.
Több mint 15 napja nincs kapcsolat | Lejárt/kiépítés | Nincs biztonsági másolat a lemezre vagy az Azure-ra.<br/><br/> Ha az előfizetés lejárt, a lemezről vagy az Azure-ból állíthatja vissza.<br/><br/> Ha az előfizetés le van szerelve, a lemezről vagy az Azure-ból nem lehet visszaállítani. Az Azure helyreállítási pontok törlődnek.

## <a name="dpmmabs-storage-support"></a>DPM/MABS-tároló támogatása

A rendszer a DPM/MABS biztonsági mentést készít a helyi lemezes tárolóban.

**Storage** | **Részletek**
--- | ---
**MBS** | A modern Backup Storage (MBS) a DPM 2016/MABS v2 és újabb verziók esetében támogatott. A MABS v1 esetében nem érhető el.
**MABS-tároló az Azure-beli virtuális gépen** | A rendszer az DPM/MABS virtuális géphez csatolt Azure-lemezeken tárolja az adattárolást, amelyeket a DPM/MABS kezel. A DPM-vagy MABS-tárolóhoz használható lemezek számát a virtuális gép mérete korlátozza.<br/><br/> A2 MÉRETŰ VM: 4 lemez; A3 MÉRETŰ VIRTUÁLIS GÉP: 8 lemez; A4 MÉRETŰ VIRTUÁLIS GÉP: 16 lemez, az egyes lemezek maximális mérete 1 TB. Ez határozza meg az összes rendelkezésre álló biztonsági mentési tár készletét.<br/><br/> Azon adatok mennyisége, amelyekről biztonsági másolatot készíthet, a csatolt lemezek számától és méretétől függ.
**MABS adatmegőrzés az Azure-beli virtuális gépen** | Javasoljuk, hogy a DPM/MABS Azure-lemezen egy napig őrizze meg az adatait, és a DPM/MABS biztonsági mentését a tárolóba a hosszú megőrzés érdekében. Így nagyobb mennyiségű adattal védhető a Azure Backup való kiszervezéssel.


### <a name="modern-backup-storage-mbs"></a>Modern biztonsági mentési tár (MBS)
A DPM 2016/MABS v2 (Windows Server 2016 rendszeren futó) és újabb verziók esetében kihasználhatja a modern Backup Storage (MBS) szolgáltatás előnyeit.

- Az MBS biztonsági mentéseit egy rugalmas fájlrendszerű (ReFS) lemezen tárolja a rendszer.
- Az MBS a ReFS blokkolja a klónozást a gyorsabb biztonsági mentéshez és a tárhely hatékonyabb kihasználásához.
- Amikor köteteket ad hozzá a helyi DPM-vagy MABS-tárolóhoz, a meghajtóbetűjelekkel konfigurálja azokat. Ezt követően különböző köteteken is konfigurálhatja a munkaterhelés-tárolót.
- Amikor védelmi csoportokat hoz létre az DPM/MABS való biztonsági mentéshez, válassza ki a használni kívánt meghajtót. Előfordulhat például, hogy a biztonsági másolatokat az SQL vagy más magas IOPS számítási feladatokhoz nagy teljesítményű meghajtón tárolja, és az alacsonyabb teljesítményű meghajtókon ritkábban készített munkaterheléseket tárol.


## <a name="supported-backups-to-mabs"></a>A MABS által támogatott biztonsági másolatok

Az alábbi táblázat összefoglalja, hogy milyen biztonsági mentés készíthető a helyszíni gépekről és az Azure-beli virtuális gépekről származó MABS.


**Biztonsági mentés** | **Verziók** | **MABS** | **Részletek** |
--- | --- | --- | --- |
**Windows 10<br/>Windows 8,1<br/>Windows 8<br/>Windows 7**<br/><br/>(32/64 bites) | MABS v3, v2 | Helyszíni. | Kötet/megosztás/mappa/fájl.<br/><br/> A deduplikált kötetek támogatottak.<br/><br/> A köteteknek legalább 1 GB és NTFS fájlrendszerrel kell rendelkezniük. |
**Windows Server 2016 (Datacenter, standard, nem Nano)**<br/><br/> 64/32 bites | MABS v3, v2 | Helyszíni/Azure-beli virtuális gép.| Kötet/megosztás/mappa/fájl; rendszerállapot/operációs rendszer nélküli.<br/><br/> A deduplikált kötetek támogatottak. |
**Windows Server 2012 R2 (Datacenter és standard)**<br/><br/> 64/32 bites | MABS v3, v2 | Helyszíni/Azure-beli virtuális gép. | Helyszíni **védelem**: Kötet/megosztás/mappa/fájl; rendszerállapot/operációs rendszer nélküli.<br/><br/> **Azure-VMprotection**: Kötet/megosztás/mappa/fájl.<br/><br/> A deduplikált kötetek támogatottak. |
**Windows Server 2012 SP1 (Datacenter és standard)**<br/><br/> 64/32 bites | MABS v3, v2 <br/><br/> Telepíteni kell a [Windows Management Framework 4,0](https://www.microsoft.com/download/details.aspx?id=40855) alkalmazást. | Helyszíni/Azure-beli virtuális gép. | Helyszíni **védelem**: Kötet/megosztás/mappa/fájl; rendszerállapot/operációs rendszer nélküli.<br/><br/> **Azure**-beli virtuális gépek védelme: Kötet/megosztás/mappa/fájl.<br/><br/> A deduplikált kötetek támogatottak. |
**Windows 2008 R2 SP1 (standard és Enterprise)**<br/><br/> 64/32 bites | Támogatja a MABS v3, v2.<br/><br/> Telepíteni kell a [Windows Management Framework 4,0](https://www.microsoft.com/download/details.aspx?id=40855) alkalmazást. | Helyszíni/Azure-beli virtuális gép. |   Helyszíni **védelem**: Kötet/megosztás/mappa/fájl; rendszerállapot/operációs rendszer nélküli.<br/><br/> **Azure**-beli virtuális gépek védelme: Kötet/megosztás/mappa/fájl.<br/><br/> A deduplikált kötetek támogatottak. |
**Windows 2008 R2 (standard és Enterprise)**<br/><br/> 64/32 bites | A MABS V3 esetében az operációs rendszernek SP1 verziót kell futtatnia. | Helyszíni/Azure-beli virtuális gép. | Helyszíni **védelem**: Kötet/megosztás/mappa/fájl; rendszerállapot/operációs rendszer nélküli.<br/><br/> **Azure**-beli virtuális gépek védelme: Kötet/megosztás/mappa/fájl.<br/><br/> A deduplikált kötetek támogatottak. |
**Windows Server 2008 SP2**<br/><br/> 64/32 bites | MABS v3, v2 | A MABS v2, v3 verziója támogatott, ha a MABS VMware virtuális gépként van telepítve.<br/><br/> Az Azure-beli virtuális gépen futó MABS nem támogatott. | Kötet/megosztás/mappa/fájl; rendszerállapot/operációs rendszer nélküli. |
**Windows Storage Server 2008** | MABS v3, v2 | MABS helyszíni fizikai kiszolgálóként vagy Hyper-V virtuális géphez. <br/><br/> Az Azure-beli virtuális gépen futó MABS nem támogatott. | Kötet/megosztás/mappa/fájl; rendszerállapot/operációs rendszer nélküli.
**SQL Server 2017** | MABS v3 | Helyszíni/Azure-beli virtuális gép.| SQL Server adatbázis biztonsági mentése.<br/><br/> SQL Server fürt biztonsági mentése támogatott.<br/><br/>A CSV tárolt adatbázisok nem támogatottak. |
**2016/2016 SQL Server SP1** | MABS v3, v2 | Helyszíni/Azure-beli virtuális gép.| SQL Server adatbázis biztonsági mentése.<br/><br/> SQL Server fürt biztonsági mentése támogatott.<br/><br/>A CSV tárolt adatbázisok nem támogatottak. |
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | MABS v3, v2 | Helyszíni/Azure-beli virtuális gép.| SQL Server adatbázis biztonsági mentése.<br/><br/> SQL Server fürt biztonsági mentése támogatott.<br/><br/>A CSV tárolt adatbázisok nem támogatottak. |
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | MABS v3, v2 | Helyszíni. | Önálló Exchange-kiszolgáló, egy DAG alatt lévő adatbázis biztonsági mentése.<br/><br/> A postaláda és a postaláda-adatbázis helyreállítása egy DAG alatt.<br/><br/> A ReFS nem támogatott.<br/><br/> A nem megosztott lemezes fürtök biztonsági mentése.<br/><br/> A folyamatos replikálásra konfigurált Exchange Server biztonsági mentése. |
**SharePoint 2016**<br/><br/> **SharePoint 2013**<br/><br/> **SharePoint 2010** | MABS v3, v2 | Helyszíni/Azure-beli virtuális gép. | A farm, az előtér-webkiszolgáló biztonsági mentése.<br/><br/> Farm, adatbázis, webalkalmazás, fájl vagy listaelem, SharePoint-keresés, előtér-webkiszolgáló helyreállítása.<br/><br/> A tartalom-adatbázisok SQL Server AlwaysOn használatával nem lehet biztonsági másolatot készíteni a farmról. |
**Hyper-V a Windows Server 2016 rendszeren**<br/><br/> **Windows Server 2008 R2 (SP1)** | MABS v3, v2 | Helyszíni. | **MABS-ügynök a Hyper-V-gazdagépen**: A teljes virtuális gépek és a gazdagépek adatfájljainak biztonsági mentése. A virtuális gépek biztonsági mentése a helyi tárterülettel, a fürtben lévő virtuális gépek és a CSV-tárolók, valamint az SMB-fájlkiszolgáló tárolására szolgáló virtuális gépek.<br/><br/> **MABS-ügynök a vendég virtuális gépen**: A virtuális gépen futó munkaterhelések biztonsági mentése. CSV.<br/><br/> **Helyreállítás**: Virtuális gép, a VHD/kötet/mappák/fájlok elemszintű helyreállítása.<br/><br/> **Linux rendszerű virtuális gépek**: Biztonsági mentés, ha a Hyper-V a Windows Server 2012 R2-es és újabb verzióin fut. A Linux rendszerű virtuális gépek helyreállítása a teljes gépen történik. |
**VMware virtuális gépek: vCenter/vSphere ESXi 5.5/6.0/6.5** | MABS v3, v2 | Helyszíni. | A VMware virtuális gépek biztonsági mentése a CSV, az NFS-en és a SAN-tárolón.<br/><br/> Teljes virtuális gép helyreállítása.<br/><br/> Windows/Linux biztonsági mentés.<br/><br/> Csak Windows rendszerű virtuális gépekhez tartozó mappák vagy fájlok elemszintű helyreállítása.<br/><br/> A VMware Vapp nem támogatottak.<br/><br/> A Linux rendszerű virtuális gépek helyreállítása a teljes gépen történik. |



## <a name="supported-backups-to-dpm"></a>A DPM által támogatott biztonsági másolatok

Az alábbi táblázat összefoglalja, hogy milyen biztonsági mentés készíthető a helyszíni gépekről és az Azure-beli virtuális gépekről származó DPM.



**Biztonsági mentés** | **DPM** | **Részletek**
--- | --- | ---
**Windows 10<br/>Windows 8,1<br/>Windows 8<br/>Windows 7**<br/><br/>(32/64 bites) | Csak a helyszínen.<br/><br/> A Windows 10 DPM 2012 R2-es verzióra történő biztonsági mentését javasoljuk, hogy telepítse a [11. frissítést](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager). | Kötet/megosztás/mappa/fájl.<br/><br/> A deduplikált kötetek támogatottak.<br/><br/> A köteteknek legalább 1 GB és NTFS fájlrendszerrel kell rendelkezniük.
**Windows Server 2016 (Datacenter, standard, nem Nano)**<br/><br/> 64/32 bites | Helyszíni/Azure-beli virtuális gép.<br/><br/> Csak DPM 2016.| Kötet/megosztás/mappa/fájl; rendszerállapot/operációs rendszer nélküli.<br/><br/> A deduplikált kötetek támogatottak.
**Windows Server 2012 R2 (Datacenter és standard)**<br/><br/> 64/32 bites | Helyszíni/Azure-beli virtuális gép. | Helyszíni **védelem**: Kötet/megosztás/mappa/fájl; rendszerállapot/operációs rendszer nélküli.<br/><br/> **Azure**-beli virtuális gépek védelme: Kötet/megosztás/mappa/fájl.<br/><br/> A deduplikált kötetek a DPM 2012 R2 és újabb verziókkal támogatottak.
**Windows Server 2012 SP1 (Datacenter és standard)**<br/><br/> 64/32 bites | Helyszíni/Azure-beli virtuális gép. | Helyszíni **védelem**: Kötet/megosztás/mappa/fájl; rendszerállapot/operációs rendszer nélküli.<br/><br/> **Azure**-beli virtuális gépek védelme: Kötet/megosztás/mappa/fájl.<br/><br/> A deduplikált kötetek a DPM 2012 R2 és újabb verziókkal támogatottak.
**Windows 2008 R2 SP1 (standard és Enterprise)**<br/><br/> 64/32 bites | Helyszíni/Azure-beli virtuális gép.<br/><br/> Telepíteni kell a [Windows Management Framework 4,0](https://www.microsoft.com/download/details.aspx?id=40855) alkalmazást. |   Helyszíni **védelem**: Kötet/megosztás/mappa/fájl; rendszerállapot/operációs rendszer nélküli.<br/><br/> **Azure**-beli virtuális gépek védelme: Kötet/megosztás/mappa/fájl.
**Windows 2008 R2 (standard és Enterprise)**<br/><br/> 64/32 bites | Helyszíni.<br/><br/> A DPM nem telepíthető VMware virtuális gépként.<br/><br/> Az Azure-beli virtuális gépen futó DPM nem támogatott. | Helyszíni **védelem**: Kötet/megosztás/mappa/fájl; rendszerállapot/operációs rendszer nélküli.
**Windows Server 2008 SP2**<br/><br/> 64/32 bites | Csak a helyszínen.<br/><br/> A DPM VMware virtuális gépként való futtatása esetén támogatott. Fizikai kiszolgálóként vagy Hyper-V virtuális gépenként való futtatás nem támogatott. | Kötet/megosztás/mappa/fájl; rendszerállapot/operációs rendszer nélküli.
**Windows Storage Server 2008** | A helyszíni DPM fizikai kiszolgálóként vagy Hyper-V virtuális gépenként fut. | Kötet/megosztás/mappa/fájl; rendszerállapot/operációs rendszer nélküli.
**SQL Server 2017** | DPM SAC; Az 5. vagy újabb kumulatív frissítést futtató DPM 2016.<br/><br/> Helyszíni/Azure-beli virtuális gép.| SQL Server adatbázis biztonsági mentése.<br/><br/> SQL Server fürt biztonsági mentése támogatott.<br/><br/>A CSV tárolt adatbázisok nem támogatottak.
**2016 SQL Server SP1** | A DPM 2012 R2 esetében nem támogatott; Támogatott a 4. vagy újabb kumulatív frissítéssel rendelkező DPM SAC DPM 2016.<br/><br/> Helyszíni/Azure-beli virtuális gép.| SQL Server adatbázis biztonsági mentése.<br/><br/> SQL Server fürt biztonsági mentése támogatott.<br/><br/>A CSV tárolt adatbázisok nem támogatottak.
**SQL Server 2016** | A DPM 2012 R2 esetében nem támogatott. A 2. vagy újabb kumulatív frissítéssel rendelkező DPM SAC esetében támogatott a DPM 2016.<br/><br/> Helyszíni/Azure-beli virtuális gép.| SQL Server adatbázis biztonsági mentése.<br/><br/> SQL Server fürt biztonsági mentése támogatott.<br/><br/>A CSV tárolt adatbázisok nem támogatottak.
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | SQL Server 2014, DPM 2012 R2, 4. kumulatív frissítéssel vagy újabb verzióval.<br/><br/> Helyszíni/Azure-beli virtuális gép.| SQL Server adatbázis biztonsági mentése.<br/><br/> SQL Server fürt biztonsági mentése támogatott.<br/><br/>A CSV tárolt adatbázisok nem támogatottak.
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | Exchange 2016 esetén a DPM 2012 R2 a 9-es vagy újabb kumulatív frissítést igényli.<br/><br/> Helyszíni | Önálló Exchange-kiszolgáló, egy DAG alatt lévő adatbázis biztonsági mentése.<br/><br/> A postaláda és a postaláda-adatbázis helyreállítása egy DAG alatt.<br/><br/> A ReFS nem támogatott.<br/><br/> A nem megosztott lemezes fürtök biztonsági mentése.<br/><br/> A folyamatos replikálásra konfigurált Exchange Server biztonsági mentése.
**SharePoint 2016**<br/><br/> **SharePoint 2013**<br/><br/> **SharePoint 2010** | SharePoint 2016 a DPM 2016-es és újabb verzióiban.<br/><br/>Helyszíni/Azure-beli virtuális gép. | A farm, az előtér-webkiszolgáló biztonsági mentése.<br/><br/> Farm, adatbázis, webalkalmazás, fájl vagy listaelem, SharePoint-keresés, előtér-webkiszolgáló helyreállítása.<br/><br/> A tartalom-adatbázisok SQL Server AlwaysOn használatával nem lehet biztonsági másolatot készíteni a farmról.
**Hyper-V a Windows Server 2016 rendszeren**<br/><br/> **Windows Server 2012 R2/2012** (Datacenter/standard)<br/><br/> **Windows Server 2008 R2 (SP1)** | A 2016-es és újabb verziójú Hyper-V 2016 DPM támogatott.<br/><br/> Helyszíni. | **MABS-ügynök a Hyper-V-gazdagépen**: A teljes virtuális gépek és a gazdagépek adatfájljainak biztonsági mentése. A virtuális gépek biztonsági mentése a helyi tárterülettel, a fürtben lévő virtuális gépek és a CSV-tárolók, valamint az SMB-fájlkiszolgáló tárolására szolgáló virtuális gépek.<br/><br/> **MABS-ügynök a vendég virtuális gépen**: A virtuális gépen futó munkaterhelések biztonsági mentése. CSV.<br/><br/> **Helyreállítás**: Virtuális gép, a VHD/kötet/mappák/fájlok elemszintű helyreállítása.<br/><br/> **Linux rendszerű virtuális gépek**: Biztonsági mentés, ha a Hyper-V a Windows Server 2012 R2-es és újabb verzióin fut. A Linux rendszerű virtuális gépek helyreállítása a teljes gépen történik.
**VMware virtuális gépek: vCenter/vSphere ESXi 5.5/6.0/6.5** | MABS v3, v2 <br/><br/> A DPM 2012 R2 rendszernek szüksége van a System Center 1. kumulatív frissítésére. <br/><br/>Helyszíni. | A VMware virtuális gépek biztonsági mentése a CSV, az NFS-en és a SAN-tárolón.<br/><br/> Teljes virtuális gép helyreállítása.<br/><br/> Windows/Linux biztonsági mentés.<br/><br/> Csak Windows rendszerű virtuális gépekhez tartozó mappák vagy fájlok elemszintű helyreállítása.<br/><br/> A VMware Vapp nem támogatottak.<br/><br/> A Linux rendszerű virtuális gépek helyreállítása a teljes gépen történik.


- A DPM/MABS által készített fürtözött munkaterheléseknek ugyanabban a tartományban kell lenniük, mint a DPM/MABS, vagy egy alárendelt/megbízható tartományban.
- Az NTLM/tanúsítványalapú hitelesítés használatával biztonsági mentést készíthet a nem megbízható tartományokban vagy munkacsoportokban található adatvédelemről.



## <a name="next-steps"></a>További lépések

- [További](backup-architecture.md#architecture-back-up-to-dpmmabs) információ a MABS architektúráról.
- [Tekintse át](backup-support-matrix-mars-agent.md) a Mars-ügynök által támogatott tudnivalókat.
- [Állítson be](backup-azure-microsoft-azure-backup.md) egy MABS-kiszolgálót.
- [DPM beállítása](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180).
