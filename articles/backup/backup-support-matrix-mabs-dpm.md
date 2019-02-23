---
title: A Microsoft Azure Backup server és System Center DPM-támogatási mátrixa
description: Ez a cikk összegzi az Azure Backup támogatja a helyszíni és Azure Virtuálisgép-erőforrások biztonsági mentésére a Microsoft Azure Backup Server vagy a System Center DPM használatakor.
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 0544782763bebac42b4b68fda42cff80b18ff067
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56670734"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-serversystem-center-dpm"></a>A Microsoft Azure Backup Server és System Center DPM-mel biztonsági mentés támogatási mátrixa

Használhatja a [Azure Backup szolgáltatás](backup-overview.md) biztonsági mentése a helyszíni gépek és a számítási feladatok és az Azure virtuális gépeken. Ez a cikk összegzi a támogatási beállításait és korlátozások gépek Microsoft Azure Backup Server (MABS) vagy a System Center Data Protection Manager (DPM), és az Azure Backup használatával biztonsági mentéséről.

## <a name="about-mabsdpm"></a>About MABS/DPM

[A System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807) egy nagyvállalati megoldás, amely beállítja, megkönnyíti és kezeli a biztonsági mentés és a vállalati gépek és az adatok. Része a [a System Center](https://www.microsoft.com/cloud-platform/system-center-pricing) termékcsomagot.


A Microsoft Azure Backup Server (MABS), amely egy kiszolgáló, amely a helyszíni fizikai kiszolgálóknak, a virtuális gépek (VM) és a, és Azure virtuális gépeken futó alkalmazások biztonsági mentése segítségével.

- MABS-alapú a System Center Data Protection Manager (DPM), és hasonló funkciókat nyújt, azonban néhány eltérés:
    - MABS futtatásához nincs a System Center-licenc szükséges.
    - MABS és a DPM Azure hosszú távú biztonsági mentési tárhelyet biztosít. Emellett a DPM lehetővé teszi, hogy az adatok biztonsági mentése hosszú távú tárolás szalagon. MABS nem biztosítják ezt a funkciót.
- A MABS-kiszolgáló letöltése a [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=57520). A helyszíni, futtatása vagy az Azure-beli virtuális gépen.

A DPM és a MABS támogatja a biztonsági mentést az alkalmazások, és a kiszolgálói és ügyféloldali operációs rendszerek széles. Ezek biztosítanak több biztonsági mentési forgatókönyveket:
    - Készíthető a gépek szintjén rendszerállapot- vagy operációs rendszer nélküli biztonsági mentéssel.
    - Adott kötetek, megosztások, mappák és fájlok készíthető.
    - Konkrét alkalmazások optimalizált app-t támogató beállításokkal készíthető.

## <a name="mabsdpm-backup"></a>MABS/DPM biztonsági mentése

MABS/DPM és az Azure Backup használatával biztonsági mentést a következőképpen történik:

1. A DPM/MABS védelmi ügynök telepítve van az összes olyan számítógépen, hogy mikor készüljön biztonsági másolat. 
2. Gépek és alkalmazások biztonsági mentéséről a helyi tároló a DPM/MABS.
3. A Microsoft Azure Recovery Services-(MARS) ügynök telepítve van a DPM-kiszolgáló/MABS.
4. A MARS-ügynök biztonsági másolatot készít a DPM/MABS lemezeket az Azure-ban, Azure Backup használatával biztonsági mentési Recovery Services-tárolóba.

További információk:
- [További](backup-architecture.md#architecture-back-up-to-dpmmabs) MABS architektúrával kapcsolatban.
- [Felülvizsgálat](backup-support-matrix-mars-agent.md) a MARS-ügynök esetében támogatott műveleteket ismerteti.

## <a name="supported-scenarios"></a>Támogatott esetek 

**Forgatókönyv** | **Ügynök** | **Hely**
--- | --- | ---
**A helyszíni gépeket és számítási feladatainak biztonsági mentése** | A DPM/MABS védelmi ügynököt futtat a kívánt gépekre történő biztonsági mentése<br/><br/> A MARS-ügynök a MABS/DPM-kiszolgálón | MABS/DPM helyszíni kell futnia
**Az Azure virtuális gépeket és számítási feladatok biztonsági mentése** | A DPM/MABS védelmi ügynököt a védett gép<br/><br/> A MABS/DPM MARS-ügynök | A DPM vagy MABS-beli virtuális gépen kell futnia.

## <a name="supported-deployments"></a>Támogatott központi telepítések

A DPM/MABS is telepíthető, a táblázat foglalja össze.

**Üzembe helyezés** | **Támogatás** | **Részletek**
--- | --- | ---
**Üzembe helyezett helyszíni** | Fizikai kiszolgáló<br/><br/>A Hyper-V virtuális gép<br/><br/> VMware virtuális gép | Ha a DPM/MABS csak VMware virtuális gépek és a virtuális gépeken futó számítási feladatok mentését VMware virtuális gépként van telepítve.
**Egy virtuális Gépet az Azure Stack üzembe helyezve** | Csak a MABS | Az Azure Stack virtuális gépek biztonsági mentése a DPM nem használható.
**Az Azure virtuális gépként üzembe helyezett** | Azure virtuális gépek és a virtuális gépeken futó számítási feladatok védi. | Azure-ban futó MABS és a DPM nem biztonsági másolatot a helyszíni gépeket.


## <a name="supported-mabs-and-dpm-operating-systems"></a>Támogatott MABS és a DPM operációs rendszerek

Az Azure Backup is MABS és a DPM biztonsági mentése a következő operációs rendszerek egyikét futtatja. A legújabb szervizcsomaggal és frissítéssel operációs rendszert kell futtatnia.

**Forgatókönyv** | **MABS/DPM** 
--- | --- 
**Egy Azure virtuális Gépen futó MABS** | Windows Server 2012 R2<br/><br/> Windows 2016 Datacenter<br/><br/> Windows 2019 adatközpont<br/><br/> Azt javasoljuk, hogy először a-lemezképet a marketplace webhelyről.<br/><br/> Minimális A2 Standard két maggal és 3,5 GB RAM-MAL. 
**A DPM egy Azure virtuális gépen** | System Center 2012 R2 Update 3 vagy újabb.<br/><br/> Windows operációs rendszer [a System Center által igényelt](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).<br/><br/> Azt javasoljuk, hogy először a-lemezképet a marketplace webhelyről.<br/><br/> Minimális A2 Standard két maggal és 3,5 GB RAM-MAL. 
**Helyszíni MABS** | 64 bites operációs rendszereken támogatott:<br/><br/> MABS v3-as és újabb verziók esetében: Windows Server 2019 (Standard, Datacenter, Essentials) <br/><br/> MABS v2 és újabb verziók esetében: Windows Server 2016 (Standard, Datacenter, Essentials)<br/><br/> Minden MABS-verzió: Windows Server 2012 R2/2012 (Standard, Datacenter, Foundation)<br/><br/>Minden MABS-verzió: A Windows Storage Server 2012 R2 vagy 2012 (Standard/munkacsoport)
**A DPM a helyi** | Fizikai kiszolgáló vagy Hyper-V virtuális gépek: A System Center 2012 SP1 vagy újabb.<br/><br/> A VMware virtuális gépek: A System Center 2012 R2 alkalmazás Update 5 vagy újabb. 



## <a name="management-support"></a>Felügyeleti támogatás
**A probléma** | **Részletek**
--- | ---
**Telepítés** | DPM/MABS egycélú gépen kell telepíteni.<br/><br/> Ne telepítse a DPM/MABS tartományvezérlőn, az alkalmazás kiszolgálói szerepkör telepítése a gépre, az Exchange Server vagy a System Center Operations Manager rendszert futtató gépen vagy egy fürtcsomóponton.<br/><br/> [Felülvizsgálat](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server) minden DPM rendszerkövetelményei.
**Tartomány** | A DPM-kiszolgáló/MABS egy tartományhoz kell csatlakoznia. Először telepítse, és ezután a DPM/MABS csatlakoztathatja egy tartományhoz. A DPM/MABS egy új tartományt a telepítést követő áthelyezése nem támogatott.
**Tárolás** | A modern backup storage (MBP) a DPM 2016/MABS v2 és újabb verziók támogatják. Nem érhető el a MABS 1-es.
**MABS frissítése** | Telepítse a MABS v3-as, vagy frissíti a MABS v2 MABS v3 közvetlenül. [További információk](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**Moving MABS** | MABS áthelyezése új kiszolgálóra a tároló megőrzése MB használata támogatott.<br/><br/> A kiszolgálónak a neve megegyezik az eredeti kell rendelkeznie. A név nem módosítható, ha azt szeretné tartani az ugyanahhoz a tárolókészlethez, és használja ugyanazt a MABS adatbázist adat-helyreállítási pontok tárolásához.<br/><br/> Kell visszaállítani, mert szüksége lesz a MABS-adatbázis biztonsági másolatát.




## <a name="mabs-support-on-azure-stack"></a>MABS-támogatás az Azure Stackben

Telepíthet egy Azure Stack-beli virtuális gépen MABS, hogy az Azure Stack virtuális gépeket és számítási feladatok biztonsági mentésének egyetlen helyről kezelheti.

**Összetevő** | **Részletek**
--- | --- 
**Az Azure Stackben VM MABS** | Legalább A2 méret. Azt javasoljuk, hogy először egy Windows Server 2012 R2 vagy Windows Server 2016 rendszerképet az Azure marketplace-ről.<br/><br/> Ne telepítse bármi más, a MABS virtuális gépen.
**MABS-tároló** | MAB virtuális Gépet egy önálló tárfiókot használja. A MARS-ügynök fut, a MABS szüksége van ideiglenes tárhelyre, a gyorsítótár helyét, és a visszaállítási adatok tárolásához a felhőből.
**MABS-tárolókészlet** | A MABS tárolókészlet méretét száma és a MABS virtuális Géphez csatolt lemezek mérete határozza meg. Minden egyes Azure Stack Virtuálisgép-mérettel rendelkezik lemezek maximális száma. Például az A2 négy lemezek.
**MABS megőrzése** | A helyi lemezeken MABS biztonsági másolat adatainak legalább öt napig nem megőrzése.
**MABS vertikális felskálázás** | A telepítés vertikális felskálázásához növelheti a MABS virtuális gép méretét. Például a-tól a D sorozat.<br/><br/> Ön tehermentesítést adatokat az backup Azure rendszeresen, és szükség esetén telepíthet további MABS-kiszolgálók is gondoskodhat.
A MABS .NET-keretrendszer | A MABS VM szüksége van a .NET-keretrendszer 3.3-as SP1 vagy újabb verziója van telepítve rá.
**MABS domain** | A MABS virtuális Gépet egy tartományhoz kell csatlakoznia. Rendszergazdai jogosultságokkal rendelkező tartományi felhasználó MABS telepíteni kell a virtuális Gépet.
**Az Azure Stack VM-adatok biztonsági mentése** | Fájlok, mappák és alkalmazások készíthető.
**Támogatott biztonsági mentés** | Ezek az operációs rendszerek támogatottak a virtuális gépek biztonsági:<br/><br/> A Windows Server féléves csatorna (Datacenter vagy Enterprise/Standard)<br/><br/> Windows Server 2016/2012 R2/2012/2008 R2.
**Azure Stack virtuális gépeken futó SQL támogatása** | Készítsen biztonsági másolatot az SQL Server 2016/2014/2012 SP1.<br/><br/> Biztonsági mentése és helyreállítása az adatbázis.
**A SharePoint-támogatás az Azure Stack-beli virtuális gépek** | A SharePoint 2016/2013/2010.<br/><br/> Biztonsági mentése és helyreállítása a farm, adatbázis, előtér, webkiszolgáló.
**Biztonsági másolat virtuális gépek hálózati követelményei** | Az Azure Stack számítási feladat összes virtuális gép ugyanazon a vneten legyen, és ugyanahhoz az előfizetéshez tartozik.


## <a name="dpmmabs-networking-support"></a>A DPM/MABS hálózati támogatás

### <a name="url-access"></a>URL-hozzáférés

A DPM-kiszolgáló/MABS URL-hozzáférésre van szüksége:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="dpmmabs-connectivity-to-azure-backup"></a>Az Azure Backup a DPM vagy MABS-kapcsolat

Nem szükséges megfelelő működéséhez a biztonsági mentésekhez a kapcsolat az Azure Backup szolgáltatásba, és lehet, hogy az Azure-előfizetés aktív. Az alábbi táblázat viselkedése Ha e két dolgokat nem okozhat.

**Az Azure-bA MABS** | **Előfizetés** | **Biztonsági mentés/visszaállítás** 
--- | --- | --- 
Csatlakozva | Aktív | A DPM/MABS lemezes biztonsági mentés<br/><br/> Az Azure Backup<br/><br/> Visszaállítás lemezről<br/><br/> Állítsa vissza az Azure-ból
Csatlakozva | Lejárt / – figyelmeztetés megszüntetésről | Nincs biztonsági mentés lemezre vagy az Azure.<br/><br/> Ha az előfizetés lejárt lemezről vagy az Azure visszaállíthatja.<br/><br/> Ha az előfizetés leszerelt lemezről vagy az Azure nem állítható vissza. Az Azure helyreállítási pont törlődik.
Nincs kapcsolat több mint 15 napon keresztül | Aktív | Nincs biztonsági mentés lemezre vagy az Azure.<br/><br/> A lemez- vagy Azure állíthatja vissza.
Nincs kapcsolat több mint 15 napon keresztül | Lejárt / – figyelmeztetés megszüntetésről | Nincs biztonsági mentés lemezre vagy az Azure.<br/><br/> Ha az előfizetés lejárt lemezről vagy az Azure visszaállíthatja.<br/><br/> Ha az előfizetés leszerelt lemezről vagy az Azure nem állítható vissza. Az Azure helyreállítási pont törlődik.



## <a name="dpmmabs-storage-support"></a>A DPM vagy MABS-tároló támogatása

Biztonsági másolat a DPM/MABS adatokat tárolja a helyi lemezes tárterületet. 

**Tárolás** | **Részletek**
--- | ---
**MBS** | A modern backup storage (MBP) a DPM 2016/MABS v2 és újabb verziók támogatják. Nem érhető el a MABS 1-es.
**MABS storage Azure virtuális gépen** | Az Azure-lemezek a DPM/MABS virtuális Géphez csatolt, és a DPM/MABS felügyelt adatokat tárolja. A DPM/MABS tárolókészlethez felhasználható lemezek számát a virtuális gép mérete korlátozza.<br/><br/> A2 VM: 4 lemez; A3 VM: 8 lemez; A4 VM: 16 lemez, az egyes lemezek 1 TB-os maximális mérettel. Ez határozza meg a biztonsági mentési tárolókészlet méretét érhető el.<br/><br/> Készíthet biztonsági mentést adatok mennyisége száma és a csatlakoztatott lemezek méretétől függ.
**Azure virtuális gépen a MABS adatmegőrzés** | Javasoljuk, hogy a DPM/MABS Azure-lemez egy napig megőrizni az adatokat, és a DPM/MABS biztonsági mentése a tárolóba a hosszabb adatmegőrzés megadásához. Ily módon védheti meg egy nagyobb mennyiségű adatot az Azure Backup kiszervezésével.


### <a name="modern-backup-storage-mbs"></a>A modern backup storage (MBP)
A DPM 2016/MABS v2 (Windows Server 2016 fut) és újabb verziók, igénybe veheti a modern backup storage (MBP).

- MB másolatoknak Resilient File System (ReFS) lemezre vonatkozóan.
- MB használja a refs fájlrendszer letiltása a klónozás, és a gyorsabb biztonsági mentési és hatékonyabb használja a tárterületet.
- Kötetek hozzáadása a helyi DPM-vagy MABS-tárolókészlet, amikor konfigurálta azokat csatlakoztatása megtörtént. Konfigurálhatja a tárolási számítási feladatok különböző köteteken.
- Ha az adatok biztonsági másolatát a DPM/MABS védelmi csoportokat hoz létre, kiválaszthatja a használni kívánt meghajtót. Például tárolhatja biztonsági mentések SQL- vagy egyéb magas IOPS-munkaterhelések számára a nagy teljesítményű meghajtót, és a munkaterhelések, amelyekről ritkábban alacsonyabb teljesítményt meghajtón.


## <a name="supported-backups-to-mabs"></a>MABS támogatott biztonsági mentések

A következő táblázat összefoglalja, milyen biztonsági másolat készíthető a MABS a helyszíni gépek és Azure virtuális gépek.


**Biztonsági mentés** | **Verziók** | **MABS** | **Részletek**
--- | --- | --- | ---
**A Windows 10-es, 8.1, 8, 7**<br/><br/>(32 vagy 64 bites) | MABS v3, v2, V1 | Helyszíni követelmények | Kötet vagy megosztás/fájl/mappa<br/><br/> Deduplikált kötetek támogatott<br/><br/> Köteteknek legalább 1 GB Méretűnek és NTFS kell lenniük.
**A Windows Server 2016 (Datacenter, Standard, a Nano nélkül)**<br/><br/> 64 és 32 bites | MABS v3, v2 | Helyszíni/Azure virtuális gép.| Kötet vagy megosztás/fájl/mappa; rendszer-állapot/operációs rendszer nélküli<br/><br/> Deduplikált kötetek támogatott.
**A Windows Server 2012 R2 (Datacenter és Standard)**<br/><br/> 64 és 32 bites | MABS v3, v2, v1 | Helyszíni/Azure virtuális gép. | **Helyszíni védelem**: Kötet vagy megosztás/fájl/mappa; rendszer-állapot/operációs rendszer nélküli<br/><br/> **Az Azure VMprotection**: Kötet vagy megosztás/fájl/mappa<br/><br/> Deduplikált kötetek támogatott.
**A Windows Server 2012 SP1 (Datacenter és Standard)**<br/><br/> 64 és 32 bites | MABS v3, v2, v1<br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) kell telepíteni. | Helyszíni/Azure virtuális gép. | **Helyszíni védelem**: Kötet vagy megosztás/fájl/mappa; rendszer-állapot/operációs rendszer nélküli<br/><br/> **Az Azure virtuális gépek védelmét**: Kötet vagy megosztás/fájl/mappa<br/><br/> Deduplikált kötetek támogatott.
**A Windows Server 2012 (Datacenter és Standard)**<br/><br/> 64 és 32 bites | MABS v1 | Helyszíni/Azure virtuális gép. | **Helyszíni védelem**: Kötet vagy megosztás/fájl/mappa; rendszer-állapot/operációs rendszer nélküli<br/><br/> **Az Azure virtuális gépek védelmét**: Kötet vagy megosztás/fájl/mappa<br/><br/> Deduplikált kötetek támogatott.
**Windows 2008 R2 SP1 (Standard és Enterprise)**<br/><br/> 64 és 32 bites | A MABS v3-as, v2 és v1 támogatja.<br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) kell telepíteni. | Helyszíni/Azure virtuális gép. |   **Helyszíni védelem**: Kötet vagy megosztás/fájl/mappa; rendszer-állapot/operációs rendszer nélküli<br/><br/> **Az Azure virtuális gépek védelmét**: Kötet vagy megosztás/fájl/mappa<br/><br/> Deduplikált kötetek támogatott.
**Windows 2008 R2 (Standard és Enterprise)**<br/><br/> 64 és 32 bites | MABS v1. A MABS az operációs rendszer v2 és v3 kell futnia a SP1. | Helyszíni/Azure virtuális gép. | **Helyszíni védelem**: Kötet vagy megosztás/fájl/mappa; rendszer-állapot/operációs rendszer nélküli<br/><br/> **Az Azure virtuális gépek védelmét**: Kötet vagy megosztás/fájl/mappa<br/><br/> Deduplikált kötetek támogatott.
**A Windows Server 2008 SP2**<br/><br/> 64 és 32 bites | MABS v1, v2, v3 | V1 az egyetlen támogatott MABS egy a helyszíni fizikai gép vagy a Hyper-V virtuális Gépként üzembe helyezésekor.<br/><br/> MABS v1, 2, 3 akkor támogatott, ha a MABS VMware virtuális gépként üzemel.<br/><br/> Nem támogatja a MABS Azure virtuális gépen | Kötet vagy megosztás/fájl/mappa; rendszer-állapot/operációs rendszer nélküli.
**A Windows Storage Server 2008** | MABS v1, v2, v3 | A helyszíni fizikai kiszolgáló vagy Hyper-V virtuális gép MABS | MABS Azure virtuális gépen nem támogatott. | Kötet vagy megosztás/fájl/mappa; rendszer-állapot/operációs rendszer nélküli.
**SQL Server 2017** | MABS v3 | Helyszíni/Azure virtuális gép.| Az SQL Server-adatbázis biztonsági mentése.<br/><br/> SQL Server fürt biztonsági mentését támogatja.<br/><br/>Az adatbázis nem támogatott a megosztott fürtköteteken tárolja.
**Az SQL Server 2016/2016 SP1-gyel** | MABS v3, v2 | Helyszíni/Azure virtuális gép.| Az SQL Server-adatbázis biztonsági mentése.<br/><br/> SQL Server fürt biztonsági mentését támogatja.<br/><br/>Az adatbázis nem támogatott a megosztott fürtköteteken tárolja.
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | MABS v3, v2, v1 | Helyszíni/Azure virtuális gép.| Az SQL Server-adatbázis biztonsági mentése.<br/><br/> SQL Server fürt biztonsági mentését támogatja.<br/><br/>Az adatbázis nem támogatott a megosztott fürtköteteken tárolja.
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | MABS v3, v2, V1 | A helyszíni. | Önálló Exchange-kiszolgáló, adatbázis egy DAG készíteni<br/><br/> Állítsa helyre a postaláda és postaláda-adatbázist egy DAG alatt.<br/><br/> A refs fájlrendszer nem támogatott.<br/><br/> Készítsen biztonsági másolatot nem megosztott lemezfürtöket.<br/><br/> Folyamatos replikálásra konfigurált Exchange-kiszolgálóról biztonsági mentést.
**SharePoint 2016**<br/><br/> **A SharePoint 2013-hoz**<br/><br/> **SharePoint 2010** | MABS v3, v2, v1 | Helyszíni/Azure virtuális gép | Készítsen biztonsági másolatot a farm, előtér-webkiszolgáló.<br/><br/> Farm, adatbázis, webalkalmazás, fájl vagy listaelem, SharePoint-keresés, előtér-webkiszolgáló helyre.<br/><br/> Nem lehet biztonsági másolatot készíteni a tartalom-adatbázisok AlwaysOn használó, farmban.
**A Hyper-V-t a Windows Server 2016-ban**<br/><br/> **A Windows Server 2012 R2 vagy 2012** (Datacenter/Standard)<br/><br/> **Windows Server 2008 R2 (SP1)** | MABS v3, v2, v1 | Helyszíni követelmények | **MABS-ügynököt a Hyper-V-gazdagép**: Készítsen biztonsági másolatot a teljes virtuális gépek és a gazdagép az adatfájlokat. Virtuális gépek biztonsági mentése a helyi storage virtuális gépek a fürt megosztott Fürtkötetes tárolással, virtuális gépek SMB fájlkiszolgáló tárolóval.<br/><br/> **MABS-ügynököt a Vendég virtuális Gépen**: Készítsen biztonsági másolatot a virtuális gépen futó számítási feladatokat. CSV-ket.<br/><br/> **Helyreállítási**: A virtuális gép, virtuális Merevlemezt vagy kötet/mappákat és fájlokat az elemszintű helyreállítás.<br/><br/> **Linux rendszerű virtuális gépek**: Készítsen biztonsági másolatot, ha a Hyper-V fut a Windows Server 2012 R2 és újabb verziók esetében. Linux rendszerű virtuális gépek helyreállítási van a teljes gépre.
**VMware virtuális gépek: vCenter/vSphere ESXi-5.5/6.0/6.5** | MABS v3, v2, v1.<br/><br/> MABS v1 kell 1. kumulatív frissítés) | Helyszíni követelmények | VMware virtuális gépek biztonsági mentése a CSV-k, az NFS és a SAN-tárolás.<br/><br/> Teljes virtuális gép helyreállításához.<br/><br/> Windows/Linux biztonsági mentés.<br/><br/> Elemszintű helyreállítás mappa és fájlok Windows virtuális gépek csak.<br/><br/> A VMware vapp alkalmazásokra nem támogatottak.<br/><br/> Linux rendszerű virtuális gépek helyreállítási van a teljes gépre.



## <a name="supported-backups-to-dpm"></a>A DPM biztonsági mentések támogatott

A következő táblázat összefoglalja, milyen biztonsági másolat készíthető a dpm-hez a helyszíni gépek és Azure virtuális gépek.



**Biztonsági mentés** | **DPM** | **Részletek**
--- | --- | --- 
**A Windows 10-es, 8.1, 8, 7**<br/><br/>(32 vagy 64 bites) | A helyszíni csak.<br/><br/> A biztonsági mentése a DPM 2012 R2 Windows 10-es ajánlott a telepítése [frissítés 11](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager). | Kötet vagy megosztás/fájl/mappa<br/><br/> Deduplikált kötetek támogatott<br/><br/> Köteteknek legalább 1 GB Méretűnek és NTFS kell lenniük.
**A Windows Server 2016 (Datacenter, Standard, a Nano nélkül)**<br/><br/> 64 és 32 bites | Helyszíni/Azure virtuális gép.<br/><br/> Csak a DPM 2016.| Kötet vagy megosztás/fájl/mappa; rendszer-állapot/operációs rendszer nélküli<br/><br/> Deduplikált kötetek támogatott.
**A Windows Server 2012 R2 (Datacenter és Standard)**<br/><br/> 64 és 32 bites | Helyszíni/Azure virtuális gép | **Helyszíni védelem**: Kötet vagy megosztás/fájl/mappa; rendszer-állapot/operációs rendszer nélküli<br/><br/> **Az Azure virtuális gépek védelmét**: Kötet vagy megosztás/fájl/mappa<br/><br/> Deduplikált kötetek rendelkező DPM 2012 R2 és újabb verziók esetében támogatott.
**A Windows Server 2012 SP1 (Datacenter és Standard)**<br/><br/> 64 és 32 bites | Helyszíni/Azure virtuális gép. | **Helyszíni védelem**: Kötet vagy megosztás/fájl/mappa; rendszer-állapot/operációs rendszer nélküli<br/><br/> **Az Azure virtuális gépek védelmét**: Kötet vagy megosztás/fájl/mappa<br/><br/> Deduplikált kötetek rendelkező DPM 2012 R2 és újabb verziók esetében támogatott.
**A Windows Server 2012 (Datacenter és Standard)**<br/><br/> 64 és 32 bites |  Helyszíni/Azure virtuális gép. | **Helyszíni védelem**: Kötet vagy megosztás/fájl/mappa; rendszer-állapot/operációs rendszer nélküli<br/><br/> **Az Azure virtuális gépek védelmét**: Kötet vagy megosztás/fájl/mappa<br/><br/> Deduplikált kötetek rendelkező DPM 2012 R2 és újabb verziók esetében támogatott.
**Windows 2008 R2 SP1 (Standard és Enterprise)**<br/><br/> 64 és 32 bites | Helyszíni/Azure virtuális gép <br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) kell telepíteni. |   **Helyszíni védelem**: Kötet vagy megosztás/fájl/mappa; rendszer-állapot/operációs rendszer nélküli<br/><br/> **Az Azure virtuális gépek védelmét**: Kötet vagy megosztás/mappa/fájl.
**Windows 2008 R2 (Standard és Enterprise)**<br/><br/> 64 és 32 bites | A helyszíni.<br/><br/> A DPM nem telepíthető a VMware virtuális gépként.<br/><br/> Egy Azure-beli virtuális gépen futó DPM nem támogatott. | **Helyszíni védelem**: Kötet vagy megosztás/fájl/mappa; rendszer-állapot/operációs rendszer nélküli
**A Windows Server 2008 SP2**<br/><br/> 64 és 32 bites | A helyszíni csak.<br/><br/> Ha VMware virtuális gépként futó DPM támogatott. Fizikai kiszolgálóként vagy Hyper-V virtuális gép futtatása nem támogatott. | Kötet vagy megosztás/fájl/mappa; rendszer-állapot/operációs rendszer nélküli.
**A Windows Storage Server 2008** | A DPM a helyszíni fizikai kiszolgálóként vagy Hyper-V virtuális gépként futó. | Kötet vagy megosztás/fájl/mappa; rendszer-állapot/operációs rendszer nélküli.
**SQL Server 2017** | A DPM SAC; A DPM 2016 5-ös vagy újabb kumulatív frissítéssel<br/><br/> Helyszíni/Azure virtuális gép.| Az SQL Server-adatbázis biztonsági mentése.<br/><br/> SQL Server fürt biztonsági mentését támogatja.<br/><br/>Az adatbázis nem támogatott a megosztott fürtköteteken tárolja.
**Az SQL Server 2016 SP1-gyel** | A DPM 2012 R2; nem támogatott A DPM SAC, 4 vagy újabb kumulatív frissítéssel, a DPM 2016 támogatja.<br/><br/> Helyszíni/Azure virtuális gép.| Az SQL Server-adatbázis biztonsági mentése.<br/><br/> SQL Server fürt biztonsági mentését támogatja.<br/><br/>Az adatbázis nem támogatott a megosztott fürtköteteken tárolja.
**SQL Server 2016** | A DPM 2012 R2 esetében nem támogatott. A DPM SAC, a 2. kumulatív frissítésétől kezdve a DPM 2016 támogatja.<br/><br/> Helyszíni/Azure virtuális gép.| Az SQL Server-adatbázis biztonsági mentése.<br/><br/> SQL Server fürt biztonsági mentését támogatja.<br/><br/>Az adatbázis nem támogatott a megosztott fürtköteteken tárolja.
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | Az SQL Server 2014 rendelkező DPM 2012 R2 rendszert futtató 4. kumulatív frissítés és újabb verziók esetében.<br/><br/> Helyszíni/Azure virtuális gép.| Az SQL Server-adatbázis biztonsági mentése.<br/><br/> SQL Server fürt biztonsági mentését támogatja.<br/><br/>Az adatbázis nem támogatott a megosztott fürtköteteken tárolja.
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | Az Exchange 2016 a DPM 2012 R2 kumulatív frissítés kell a 9-es vagy újabb.<br/><br/> Helyszíni követelmények | Önálló Exchange-kiszolgáló, adatbázis egy DAG készíteni<br/><br/> Állítsa helyre a postaláda és postaláda-adatbázist egy DAG alatt.<br/><br/> A refs fájlrendszer nem támogatott.<br/><br/> Készítsen biztonsági másolatot nem megosztott lemezfürtöket.<br/><br/> Folyamatos replikálásra konfigurált Exchange-kiszolgálóról biztonsági mentést.
**SharePoint 2016**<br/><br/> **A SharePoint 2013-hoz**<br/><br/> **SharePoint 2010** | A SharePoint 2016 és újabb verziók esetében a DPM 2016-on.<br/><br/>Helyszíni/Azure virtuális gép | Készítsen biztonsági másolatot a farm, előtér-webkiszolgáló.<br/><br/> Farm, adatbázis, webalkalmazás, fájl vagy listaelem, SharePoint-keresés, előtér-webkiszolgáló helyre.<br/><br/> Nem lehet biztonsági másolatot készíteni a tartalom-adatbázisok AlwaysOn használó, farmban.
**A Hyper-V-t a Windows Server 2016-ban**<br/><br/> **A Windows Server 2012 R2 vagy 2012** (Datacenter/Standard)<br/><br/> **Windows Server 2008 R2 (SP1)** | A Hyper-V-t 2016 és újabb verziók esetében a DPM 2016 támogatja.<br/><br/> Helyszíni követelmények | **MABS-ügynököt a Hyper-V-gazdagép**: Készítsen biztonsági másolatot a teljes virtuális gépek és a gazdagép az adatfájlokat. Virtuális gépek biztonsági mentése a helyi storage virtuális gépek a fürt megosztott Fürtkötetes tárolással, virtuális gépek SMB fájlkiszolgáló tárolóval.<br/><br/> **MABS-ügynököt a Vendég virtuális Gépen**: Készítsen biztonsági másolatot a virtuális gépen futó számítási feladatokat. CSV-ket.<br/><br/> **Helyreállítási**: A virtuális gép, virtuális Merevlemezt vagy kötet/mappákat és fájlokat az elemszintű helyreállítás.<br/><br/> **Linux rendszerű virtuális gépek**: Készítsen biztonsági másolatot, ha a Hyper-V fut a Windows Server 2012 R2 és újabb verziók esetében. Linux rendszerű virtuális gépek helyreállítási van a teljes gépre.
**VMware virtuális gépek: vCenter/vSphere ESXi-5.5/6.0/6.5** | MABS v3, v2, v1.<br/><br/> DPM 2012 R2 needs System Center Update Rollup 1) | Helyszíni követelmények | VMware virtuális gépek biztonsági mentése a CSV-k, az NFS és a SAN-tárolás.<br/><br/> Teljes virtuális gép helyreállításához.<br/><br/> Windows/Linux biztonsági mentés.<br/><br/> Elemszintű helyreállítás mappa és fájlok Windows virtuális gépek csak.<br/><br/> A VMware vapp alkalmazásokra nem támogatottak.<br/><br/> Linux rendszerű virtuális gépek helyreállítási van a teljes gépre.


- Vegye figyelembe, hogy fürtözött számítási feladatok biztonsági mentése a DPM/MABS kell-e a DPM/MABS ugyanabban a tartományban vagy gyermek/megbízható tartomány. 
- Az NTLM/Tanúsítványalapú hitelesítés használatával nem megbízható tartományokban vagy munkacsoportokban található adatok biztonsági mentéséhez.



## <a name="next-steps"></a>További lépések

- [További](backup-architecture.md#architecture-back-up-to-dpmmabs) MABS architektúrával kapcsolatban.
- [Felülvizsgálat](backup-support-matrix-mars-agent.md) a MARS-ügynök esetében támogatott műveleteket ismerteti.
- [Állítsa be a](backup-azure-microsoft-azure-backup.md) a MABS-kiszolgáló.
- [A DPM beállítása](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180)
