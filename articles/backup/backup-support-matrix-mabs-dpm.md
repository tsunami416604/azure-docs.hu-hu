---
title: MABS & System Center DPM támogatási mátrixa
description: Ez a cikk összefoglalja az Azure Backup támogatását, amikor a Microsoft Azure Backup Server (MABS) vagy a System Center DPM használatával készít biztonsági másolatot a helyszíni és az Azure virtuális gép erőforrásairól.
ms.date: 02/17/2019
ms.topic: conceptual
ms.openlocfilehash: 6664f7b226b75b364fd1c83f2abc56b5a275eff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77582653"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Támogatási mátrix a Microsoft Azure Backup Server vagy system center DPM segítségével

Az Azure [Backup szolgáltatás](backup-overview.md) segítségével biztonsági másolatot készíthet a helyszíni gépekről és számítási feladatokról, valamint az Azure virtuális gépeiről (VM-ek). Ez a cikk összefoglalja a támogatási beállításokat és a gépek biztonsági mentésének korlátait a Microsoft Azure Backup Server (MABS) vagy a System Center Data Protection Manager (DPM) és az Azure Backup használatával.

## <a name="about-dpmmabs"></a>A DPM/MABS-ről

[A System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807) egy vállalati megoldás, amely konfigurálja, megkönnyíti és kezeli a vállalati gépek és adatok biztonsági mentését és helyreállítását. Ez része a [System Center](https://www.microsoft.com/cloud-platform/system-center-pricing) termékcsomagnak.

A MABS egy kiszolgálói termék, amely a helyszíni fizikai kiszolgálók, virtuális gépek és a rajtuk futó alkalmazások biztonsági másolatot tud adni.

A MABS a System Center DPM-en alapul, és hasonló funkciókat biztosít néhány különbséggel:

- A MABS futtatásához nincs szükség Rendszerközpont-licencre.
- Az Azure mind a MABS, mind a DPM számára hosszú távú biztonsági mentési tárhelyet biztosít. Ezenkívül a DPM lehetővé teszi az adatok biztonsági elégszóval történő biztonsági elégségedet a szalagon történő hosszú távú tároláshoz. A MABS nem biztosítja ezt a funkciót.
- Az elsődleges DPM-kiszolgálóról másodlagos DPM-kiszolgálóval lehet biztonsági másolatot készítő biztonsági másolatot. A másodlagos kiszolgáló biztosítja az elsődleges kiszolgáló adatbázisának és az elsődleges kiszolgálón tárolt adatforrás-replikáknak a védelmét. Az elsődleges kiszolgáló meghibásodása esetén a másodlagos kiszolgáló folytathatja az elsődleges kiszolgáló által védett munkaterhelések védelmét, amíg az elsődleges kiszolgáló ismét elérhető nem lesz.  A MABS nem biztosítja ezt a funkciót.

A MABS letöltése a [Microsoft letöltőközpontjából.](https://www.microsoft.com/download/details.aspx?id=57520) Futtatható a helyszínen vagy egy Azure-beli virtuális gép.

A DPM és a MABS támogatja az alkalmazások, valamint a kiszolgálói és ügyféloperációs rendszerek széles körének biztonsági mentését. Több biztonsági mentési forgatókönyvet biztosítanak:

- A gép szintjén rendszerállapottal vagy csupasz fém biztonsági mentéssel készíthet biztonsági mentést.
- Adott kötetekről, megosztásokról, mappákról és fájlokról is biztonsági másolatot lehet fésülni.
- Az optimalizált alkalmazásbarát beállításokkal biztonsági másolatot lehet fésülni bizonyos alkalmazásokról.

## <a name="dpmmabs-backup"></a>DPM/MABS biztonsági mentés

Biztonsági mentés a DPM/MABS és az Azure Backup használatával az alábbiak szerint működik:

1. A DPM/MABS védelmi ügynök minden olyan számítógépen telepítve van, amelyről biztonsági másolatot kell.
1. A gépekről és az alkalmazásokról a DPM/MABS helyi tárolója készül.
1. A Microsoft Azure Recovery Services (MARS) ügynök telepítve van a DPM-kiszolgálóra/MABS.The Microsoft Azure Recovery Services (MARS) agent is installed on the DPM server/MABS.
1. A MARS-ügynök biztonsági másolatot készít a DPM/MABS lemezekről egy biztonsági mentési helyreállítási szolgáltatások tárolójában az Azure-ban az Azure Backup használatával.

További információk:

- [További információ](backup-architecture.md#architecture-back-up-to-dpmmabs) a MABS architektúráról.
- [Tekintse át, hogy mi támogatott](backup-support-matrix-mars-agent.md) a MARS-ügynök számára.

## <a name="supported-scenarios"></a>Támogatott esetek

**Forgatókönyv** | **Ügynök** | **Helyen**
--- | --- | ---
**Helyszíni gépek/számítási feladatok biztonsági üzemi biztonsági és biztonsági** | A DPM/MABS védelmi ügynök azokon a gépeken fut, amelyekről biztonsági másolatot szeretne szerezni.<br/><br/> A DPM/MABS kiszolgáló MARS-ügynöke.<br/> A szolgáltatás engedélyezéséhez szükséges Microsoft Azure Recovery Services ügynök vagy Azure Backup ügynök minimális verziója a 2.0.8719.0.  | A DPM/MABS-nek a helyszínen kell futnia.

## <a name="supported-deployments"></a>Támogatott telepítések

A DPM/MABS az alábbi táblázatban összefoglalva telepíthető.

**Környezet** | **Támogatás** | **Részletek**
--- | --- | ---
**Üzembe helyezett helyszíni** | Fizikai kiszolgáló<br/><br/>Hyper-V virtuális gép<br/><br/> VMware VM | Ha a DPM/MABS vmware virtuális gépként van telepítve, csak a VMware virtuális gépekről és az ezeken a virtuális gépeken futó munkaterhelésekről biztonsági másolatot ad.
**Üzembe helyezve, mint egy Azure Stack virtuális gép** | Csak MABS | A DPM nem használható az Azure Stack virtuális gépek biztonsági támogatására.
**Üzembe helyezve, mint egy Azure virtuális gép** | Védi az Azure virtuális gépeket és az ezeken a virtuális gépeken futó számítási feladatokat | Az Azure-ban futó DPM/MABS nem tud biztonsági másolatot találni a helyszíni gépekről.

## <a name="supported-mabs-and-dpm-operating-systems"></a>Támogatott MABS és DPM operációs rendszerek

Az Azure Backup biztonsági másolatot készíthet az alábbi operációs rendszerek bármelyikét futtató DPM/MABS-példányokról. Az operációs rendszereknek a legújabb szervizcsomagokat és frissítéseket kell futtatniuk.

**Forgatókönyv** | **DPM/MABS**
--- | ---
**MABS egy Azure virtuális gép** |  Windows 2016 Datacenter.<br/><br/> Windows 2019 Datacenter.<br/><br/> Azt javasoljuk, hogy a piactérről származó lemezképpel kezdje.<br/><br/> Minimum Standard_A4_v2 négy maggal és 8 GB RAM-mal.
**DPM egy Azure virtuális gép** | System Center 2012 R2 3-as vagy újabb frissítéssel.<br/><br/> A Windows operációs rendszer [a System Center rendszer nek megfelelően.](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server)<br/><br/> Azt javasoljuk, hogy a piactérről származó lemezképpel kezdje.<br/><br/> Minimum Standard_A4_v2 négy maggal és 8 GB RAM-mal.
**MABS helyszíni** |  MABS 3-as és újabb verzió: Windows Server 2016 vagy Windows Server 2019
**DPM helyszíni** | Fizikai kiszolgáló/Hyper-V VM: System Center 2012 SP1 vagy újabb.<br/><br/> VMware VM: System Center 2012 R2 5-ös vagy újabb frissítéssel.

>[!NOTE]
>Az Azure Backup Server telepítése nem támogatott Windows Server Core vagy Microsoft Hyper-V Server rendszerben.

## <a name="management-support"></a>Felügyeleti támogatás

**Probléma** | **Részletek**
--- | ---
**Telepítés** | Telepítse a DPM/MABS-t egyegycélú gépre.<br/><br/> Ne telepítse a DPM/MABS fájlrendszert tartományvezérlőre, alkalmazáskiszolgálói szerepkörrel rendelkező számítógépre, Microsoft Exchange Server vagy System Center Operations Manager rendszert futtató számítógépre vagy fürtcsomópontra.<br/><br/> [Tekintse át az összes DPM rendszerkövetelményt](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).
**Tartomány** | A DPM/MABS-t tartományhoz kell csatlakoztatni. Először telepítse, majd csatlakozzon a DPM/MABS tartományhoz. A DPM/MABS áthelyezése egy új tartományba a telepítés után nem támogatott.
**Tárterület** | A modern biztonsági mentési tároló (MBS) a DPM 2016/MABS 2-es és újabb verzióiból támogatott. Nem érhető el a MABS v1.It isn't available for MABS v1.
**MABS frissítés** | Közvetlenül telepítheti a MABS v3-at, vagy frissíthet a MABS v3-ra a MABS v2-ről. [További információ](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**Mozgó MABS** | MbS használata esetén a MABS áthelyezése egy új kiszolgálóra a tároló megtartása mellett.<br/><br/> A kiszolgáló nevének meg kell egyeznie az eredeti nevével. A név nem módosítható, ha ugyanazt a tárolókészletet szeretné megtartani, és ugyanazt a MABS-adatbázist szeretné használni az adat-helyreállítási pontok tárolásához.<br/><br/> Szüksége lesz a MABS-adatbázis biztonsági másolatára, mert vissza kell állítania.

## <a name="mabs-support-on-azure-stack"></a>MABS-támogatás az Azure Stacken

A MABS-t üzembe helyezheti egy Azure Stack virtuális gépen, így egyetlen helyről kezelheti az Azure Stack virtuális gépek és számítási feladatok biztonsági mentését.

**Összetevő** | **Részletek**
--- | ---
**MABS az Azure Stack virtuális gépén** | Legalább A2-es méret. Azt javasoljuk, hogy az Azure Marketplace-ről származó Windows Server 2012 R2 vagy Windows Server 2016-lemezképpel kezdje.<br/><br/> Ne telepítsen semmi mást a MABS virtuális gépre.
**MABS tároló** | Használjon külön tárfiókot a MABS virtuális géphez. A MABS-en futó MARS-ügynöknek ideiglenes tárolásra van szüksége a gyorsítótár helyéhez, és a felhőből visszaállított adatok tárolásához.
**MABS tárolókészlet** | A MABS tárolókészlet méretét a MABS virtuális géphez csatlakoztatott lemezek száma és mérete határozza meg. Minden Egyes Azure Stack virtuális gép mérete rendelkezik a lemezek maximális száma. Az A2 például négy lemez.
**MABS-megőrzés** | Ne őrizze meg a biztonsági másolatot a helyi MABS-lemezeken öt napnál tovább.
**MABS felskálázás** | A központi telepítés nagyméretezéséhez növelheti a MABS virtuális gép méretét. Például átválthat A-ról D-sorozatra.<br/><br/> Azt is biztosíthatja, hogy az adatokat rendszeresen kitöltse az Azure-ba biztonsági mentéssel. Szükség esetén további MABS-kiszolgálókat is telepíthet.
**.NET keretrendszer a MABS-en** | A MABS virtuális géphez a .NET Framework 3.3 SP1 vagy újabb szervizcsomag van telepítve.
**MABS tartomány** | A MABS virtuális gép kell csatlakozni a tartományhoz. A rendszergazdai jogosultságokkal rendelkező tartományi felhasználóknak telepíteniük kell a MABS-t a virtuális gépre.
**Az Azure Stack virtuálisgép-adatok biztonsági mentése** | Biztonsági másolatot adhat a fájlokról, mappákról és alkalmazásokról.
**Támogatott biztonsági mentés** | Ezek az operációs rendszerek támogatottak a biztonsági másolatot létrehozni kívánt virtuális gépeken:<br/><br/> Windows Server féléves csatorna (adatközpont, nagyvállalati, standard)<br/><br/> Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2
**Az SQL Server támogatása az Azure Stack virtuális gépekhez** | Biztonsági másolatot az SQL Server 2016, SQL Server 2014, SQL Server 2012 SP1 szervizcsomagról.<br/><br/> Biztonsági másolatot és adatbázis helyreállítása.
**SharePoint-támogatás az Azure Stack virtuális gépekhez** | SharePoint 2016, SharePoint 2013, SharePoint 2010.<br/><br/> Farm, adatbázis, előtér és webkiszolgáló biztonsági mentése és helyreállítása.
**A biztonsági másolatok hoz a virtuális gépek hálózati követelményei** | Az Azure Stack-munkaterhelésben lévő összes virtuális gépnek ugyanahhoz a virtuális hálózathoz kell tartoznia, és ugyanahhoz az előfizetéshez kell tartoznia.

## <a name="dpmmabs-networking-support"></a>DPM/MABS hálózati támogatás

### <a name="url-access"></a>URL-hozzáférés

A DPM-kiszolgálónak/MABS-nek hozzá kell férnie ezekhez az URL-címekhez:

- `http://www.msftncsi.com/ncsi.txt`
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="azure-expressroute-support"></a>Az Azure ExpressRoute támogatása

Az Azure ExpressRoute-on keresztül is biztonsági másolatot kaphat az adatokról nyilvános társviszony-létesítéssel (a régi áramkörök számára elérhető) és a Microsoft-társviszony-létesítéssel. A privát társviszony-létesítés biztonsági mentése nem támogatott.

Nyilvános társviszony-létesítés: Hozzáférés biztosítása a következő tartományokhoz/címekhez:

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

A Microsoft társviszony-létesítésével válassza ki a következő szolgáltatásokat/régiókat és a megfelelő közösségi értékeket:

* Azure Active Directory (12076:5060)
* Microsoft Azure-régió (a Recovery Services-tároló helyének megfelelően)
* Azure Storage (a Recovery Services-tároló helyének megfelelően)

További információt az [ExpressRoute útválasztási követelményei című témakörben talál.](https://docs.microsoft.com/azure/expressroute/expressroute-routing)

>[!NOTE]
>A nyilvános társviszony-létesítés elavult az új áramkörök esetében.

### <a name="dpmmabs-connectivity-to-azure-backup"></a>DPM/MABS-kapcsolat az Azure Backup szolgáltatással

A biztonsági mentések megfelelő működéséhez az Azure Backup szolgáltatáshoz való kapcsolódás szükséges, és az Azure-előfizetésnek aktívnak kell lennie. Az alábbi táblázat a viselkedést mutatja be, ha ez a két dolog nem fordul elő.

**MABS az Azure-ba** | **Előfizetés** | **Biztonsági mentés/visszaállítás**
--- | --- | ---
Csatlakozva | Aktív | Biztonsági másolatot készíteni a DPM/MABS lemezre.<br/><br/> Biztonsági másolatot az Azure-hoz.<br/><br/> Visszaállítás lemezről.<br/><br/> Visszaállítás az Azure-ból.
Csatlakozva | Lejárt/megszüntetett | Nincs biztonsági mentés a lemezre vagy az Azure-ba.<br/><br/> Ha az előfizetés lejárt, visszaállíthatja a lemezről vagy az Azure-ból.<br/><br/> Ha az előfizetés le van állítva, nem lehet visszaállítani a lemezről vagy az Azure-ból. Az Azure helyreállítási pontok törlődnek.
Nincs kapcsolat 15 napnál tovább | Aktív | Nincs biztonsági mentés a lemezre vagy az Azure-ba.<br/><br/> Visszaállíthatja a lemezről vagy az Azure-ból.
Nincs kapcsolat 15 napnál tovább | Lejárt/megszüntetett | Nincs biztonsági mentés a lemezre vagy az Azure-ba.<br/><br/> Ha az előfizetés lejárt, visszaállíthatja a lemezről vagy az Azure-ból.<br/><br/> Ha az előfizetés le van állítva, nem lehet visszaállítani a lemezről vagy az Azure-ból. Az Azure helyreállítási pontok törlődnek.

## <a name="dpmmabs-storage-support"></a>DPM/MABS tárhely támogatás

A DPM/MABS-re biztonsági másolatot tartalmazó adatokat a helyi lemeztároló tárolja.

**Tárterület** | **Részletek**
--- | ---
**Mbs** | A modern biztonsági mentési tároló (MBS) a DPM 2016/MABS 2-es és újabb verzióiból támogatott. Nem érhető el a MABS v1.It isn't available for MABS v1.
**MABS-tároló az Azure VM-en** | Az adatok a DPM/MABS virtuális géphez csatlakoztatott És DPM/MABS-ben kezelt Azure-lemezeken tárolódnak. A DPM/MABS tárolókészlethez használható lemezek számát a virtuális gép mérete korlátozza.<br/><br/> A2 virtuális gép: 4 lemez; A3 virtuális gép: 8 lemez; A4 virtuális gép: 16 lemez, minden lemez maximális mérete 1 TB. Ez határozza meg a teljes biztonsági mentési tárolókészlet, amely elérhető.<br/><br/> A biztonsági másolatot készíteni kívánt adatok mennyisége a csatolt lemezek számától és méretétől függ.
**MABS-adatmegőrzés az Azure VM-en** | Azt javasoljuk, hogy az adatokat egy napig a DPM/MABS Azure-lemezen őrizze meg, és készítsen biztonsági másolatot a DPM/MABS-ről a tárolóba a hosszabb megőrzés érdekében. Így nagyobb mennyiségű adatot védhet meg az Azure Backup szolgáltatásba való kiszervezéssel.

### <a name="modern-backup-storage-mbs"></a>Modern biztonsági mentési tároló (MBS)

A DPM 2016/MABS v2 (Windows Server 2016 rendszeren futó) és újabb verzióiból kihasználhatja a modern biztonsági mentési tároló (MBS) előnyeit.

- Az MBS biztonsági mentések rugalmas fájlrendszer (ReFS) lemezen tárolódnak.
- Az MBS refs blokkklónozást használ a gyorsabb biztonsági mentés és a tárhely hatékonyabb kihasználása érdekében.
- Amikor köteteket ad hozzá a helyi DPM/MABS tárolókészlethez, meghajtóbetűjeleksegítségével konfigurálja őket. Ezután konfigurálhatja a számítási feladatok tárolási különböző köteteken.
- Amikor védelmi csoportokat hoz létre az adatok DPM/MABS-re való biztonsági készítéséhez, kiválaszthatja a használni kívánt meghajtót. Például előfordulhat, hogy tárolja az SQL vagy más magas IOPS-számítási feladatok biztonsági mentéseit egy nagy teljesítményű meghajtón, és tárolja azokat a számítási feladatokat, amelyekről ritkábban biztonsági mentéskészül egy alacsonyabb teljesítményű meghajtón.

## <a name="supported-backups-to-mabs"></a>Támogatott biztonsági mentések a MABS-hez

Az Azure Backup Server segítségével védhető különböző kiszolgálókról és számítási feladatokról az [Azure Backup Server Protection Matrix](https://docs.microsoft.com/azure/backup/backup-mabs-protection-matrix#protection-support-matrix)című dokumentumban tájékozódhat.

## <a name="supported-backups-to-dpm"></a>Támogatott biztonsági mentések a DPM-hez

Az Adatvédelmi kezelővel védhető különböző kiszolgálókról és munkaterhelésekről a Mit tud a [DPM biztonsági másolatot adni?](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2019).

- A DPM/MABS által támogatott fürtözött számítási feladatoknak ugyanabban a tartományban kell lenniük, mint a DPM/MABS, vagy egy gyermek/megbízható tartományban.
- Az NTLM/tanúsítványhitelesítés segítségével nem megbízható tartományokban vagy munkacsoportokban lévő adatokról is biztonsági másolatot kaphat.

## <a name="next-steps"></a>További lépések

- [További információ](backup-architecture.md#architecture-back-up-to-dpmmabs) a MABS architektúráról.
- [Tekintse át,](backup-support-matrix-mars-agent.md) hogy mi támogatott a MARS-ügynök számára.
- [MABS-kiszolgáló beállítása.](backup-azure-microsoft-azure-backup.md)
- [Állítsa be a DPM-et](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180).
