---
title: "A replikáció Azure az Azure Site Recovery segítségével Előfeltételek |} Microsoft Docs"
description: "További információk a virtuális gépek és fizikai gépek replikálása az Azure-bA az Azure Site Recovery szolgáltatás használatával előfeltételeit."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/19/2017
ms.author: raynew
ms.openlocfilehash: cb7c3892dc0acbffdec636e5e7b3ce063660153c
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2017
---
#  <a name="prerequisites-for-replication-from-on-premises-to-azure-by-using-site-recovery"></a>A replikáció a helyszíni az Azure Site Recovery segítségével Előfeltételek

> [!div class="op_single_selector"]
> * [Az Azure-ból az Azure-bA replikálása](site-recovery-azure-to-azure-prereq.md)
> * [A helyszíni replikálása Azure-bA](site-recovery-prereq.md)

Az Azure Site Recovery segíthet az üzletmenet folytonosságát és a vész-helyreállítási (BCDR) stratégia megvalósításában egy Azure virtuális gép (VM) replikációs felelhet meg egy másik Azure-régiót. A Site Recovery is replikálja a helyszíni fizikai kiszolgálóknak és virtuális gépek az Azure-felhőbe vagy egy másodlagos adatközpontba. Nem tervezett kimaradás esetén az elsődleges helyen, akkor átveheti egy másodlagos helyre, így az alkalmazások és számítási feladatok nem állnak. Ha az elsődleges hely tér vissza a normál működés sikertelen lehet az elsődleges helyre. További információ a Site Recovery: [Mi az Site Recovery?](site-recovery-overview.md).

Ebben a cikkben azt egy a helyi számítógépen, az Azure Site Recovery replikáció megkezdése előfeltételeit foglalja össze.

A megjegyzéseket, a cikk alján. A műszaki kérdéseit is teheti fel a [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="azure-requirements"></a>Azure-követelmények

**Követelmény** | **Részletek**
--- | ---
**Azure-fiók** | A [Microsoft Azure-fiók](http://azure.microsoft.com/). Kezdésként használhatja az [ingyenes próbaverziót](https://azure.microsoft.com/pricing/free-trial/) is.
**Site Recovery szolgáltatás** | További információ az Azure Site Recovery szolgáltatás díjszabása: [Site Recovery díjszabásáról](https://azure.microsoft.com/pricing/details/site-recovery/). |
**Azure Storage** | A replikált adatok tárolásához Azure Storage-fiók van szüksége. A tárfióknak és az Azure Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie. Azure virtuális gépek jönnek létre, ha feladatátvétel történik.<br/><br/> Attól függően, hogy az Azure virtuális gép feladatátvételi használni kívánt erőforrás-modellje, beállíthat egy fiók használatával a [Azure Resource Manager üzembe helyezési modellben](../storage/common/storage-create-storage-account.md) vagy a [klasszikus üzembe helyezési modellel](../storage/common/storage-create-storage-account.md).<br/><br/>[Georedundáns tárolást](../storage/common/storage-redundancy.md#geo-redundant-storage) és helyileg redundáns tárolást is használhat. A georedundáns tárolást javasoljuk. A georedundáns tárolás akár regionális kimaradás során, illetve az elsődleges régió helyreállíthatatlansága esetében is gondoskodik az adatok hibatűréséről.<br/><br/> Használhatja a szabványos Azure storage-fiók, vagy a prémium szintű Azure Storage is használhat. [Prémium szintű storage](https://docs.microsoft.com/azure/storage/storage-premium-storage) jellemzően akkor alkalmazzák egy i/o-teljesítmény egységesen magas és alacsony késést igénylő virtuális gépekhez. Prémium szintű Storage a virtuális gépek üzemeltethet I/O-igényes munkaterhelések. Ha Prémium szintű tárolást használ a replikált adatokhoz, Standard szintű tárfiókra is szüksége van. A Standard szintű tárfiók a helyszíni adatokban bekövetkező folyamatos változásokat rögzítő replikációs naplókat tárolja.<br/><br/>
**Tárolási korlátai** | A Site Recovery számára egy másik erőforráscsoportban található, vagy helyezze át vagy egy másik előfizetés tárfiókok nem helyezhető át.<br/><br/> Jelenleg a közép-Indiában, Dél-India és a prémium szintű tárfiókokba történő replikálása nem érhető el.
**Azure-hálózat** | Az Azure-hálózatot, amelyhez Azure virtuális gépek a feladatátvételt követően csatlakozni kell. Az Azure-hálózatnak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie.<br/><br/> Az Azure-portálon létrehozhat egy Azure-hálózat használatával a [Resource Manager üzembe helyezési modellben](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) vagy a [klasszikus üzembe helyezési modellel](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).<br/><br/> Ha a System Center Virtual Machine Manager (VMM) replikálása Azure-ba, be kell állítania a hálózatra való leképezést a VMM Virtuálisgép-hálózatok és az Azure-hálózatok között. Ez biztosítja, hogy Azure virtuális gépek hálózatokhoz való kapcsolódásának megfelelő feladatátvételt követően.
**Hálózati korlátozásai** | A Site Recovery számára egy másik erőforráscsoportban található, vagy helyezze át vagy egy másik előfizetés hálózati fiókok nem helyezhető át.
**Hálózatleképezés** | Ha a Microsoft Hyper-V virtuális gépek VMM-felhőkben replikálja, be kell állítania a hálózat leképezését. Ez biztosítja, hogy a Azure virtuális gépek megfelelő hálózatokhoz való kapcsolódásának, ha a feladatátvételt követően jönnek létre.

> [!NOTE]
> A következő szakaszok ismertetik a felhasználói környezet különböző összetevői előfeltételeit. Specifikus konfigurációk-támogatással kapcsolatos további információkért lásd: a [támogatási mátrix](site-recovery-support-matrix.md).
>

## <a name="disaster-recovery-of-vmware-vms-or-physical-windows-or-linux-servers-to-azure"></a>VMware virtuális gépek vagy fizikai Windows vagy Linux rendszerű kiszolgálók az Azure-bA katasztrófa utáni helyreállítás
A következő összetevők szükségesek a VMware virtuális gépek vagy windowsos vagy Linuxos fizikai kiszolgálók vész-helyreállítási. Ezek a mellett a ismertetettekhez [Azure-követelményeknek](#azure-requirements).


### <a name="configuration-server-or-additional-process-server"></a>Konfigurációs kiszolgáló, vagy további folyamatkiszolgáló

Egy a helyi számítógépen, a konfigurációs kiszolgálónak beállítva kell levezényelni a helyszíni hely és az Azure közötti kommunikáció. Az alább táblázat megbeszélések a rendszer és szoftverkövetelményeivel kapcsolatos konfigurációs-kiszolgálóként konfigurált virtuális gép.

> [!IMPORTANT]
> VMware virtuális gépek védelme a konfigurációs kiszolgáló telepítésekor ajánlott telepíteni azt egy **magas rendelkezésre ÁLLÁS** virtuális gépet.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

### <a name="vmware-vcenter-or-vsphere-host-prerequisites"></a>VMware vCenter vagy vSphere-gazdagép Előfeltételek

| **Összetevő** | **Követelmények** |
| --- | --- |
| **vSphere** | Egy vagy több VMware vSphere hipervizorok kell rendelkeznie.<br/><br/>Hipervizorok verziót kell futtatnia vSphere 6.0, 5.5 vagy 5.1, a legújabb frissítésekkel.<br/><br/>Azt javasoljuk, hogy a vSphere gazdagépek és vCenter-kiszolgáló ugyanazon a hálózaton, a folyamat-kiszolgálóként is. Kivéve, ha beállította egy dedikált folyamatkiszolgálót, ez az a hálózat, ahol a konfigurációs kiszolgáló. |
| **vCenter** | Azt javasoljuk, hogy a vSphere-gazdagép kezeléséhez a VMware vCenter-kiszolgálót telepít. Az verziót kell futtatnia vCenter 6.0 vagy 5.5, a legújabb frissítésekkel.<br/><br/>**A korlátozás**: a Site Recovery nem támogatja a replikációt vCenter vMotion példányai között. Tárolás DRS és a Storage vmotion szolgáltatások használata is nem támogatottak a fő célkiszolgáló virtuális gép védelem-újrabeállítási művelet után.|

### <a name="replicated-machine-prerequisites"></a>A replikált gép előfeltételei

| **Összetevő** | **Követelmények** |
| --- | --- |
| **A helyszíni gépeket** (a VMware virtuális gépek) | A replikált virtuális gépek telepítve és fut a VMware-eszközök kell rendelkeznie.<br/><br/> Meg kell felelnie a virtuális gépek [Azure-Előfeltételek](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) egy Azure virtuális gép létrehozásához.<br/><br/>Minden védett számítógépen a lemezkapacitás 1,023 GB-nál több nem lehet. <br/><br/>A minimális 2 GB szabad terület a rendszermeghajtón összetevő telepítése szükséges.<br/><br/>Ha szeretné engedélyezni a virtuális Gépre kiterjedő konzisztencia, 20004 portot kell megnyitni a virtuális gép helyi tűzfalon.<br/><br/>Számítógép nevének 1 és 63 karakter között (használható betűket, számokat és kötőjeleket) kell lennie. A név kell kezdődnie, betűvel vagy számmal, és betűvel vagy számmal végződhet. <br/><br/>Követően a gép replikációjának bekapcsolását, módosíthatja az Azure nevét.<br/><br/> |
| **Windows-alapú gépek** (fizikai vagy VMware) | A gép a következő támogatott 64 bites operációs rendszerek valamelyikét kell futtatnia: <br/>-Windows Server 2016 (Server Core, az asztali élmény kiszolgáló)<br/>-Windows Server 2012 R2 rendszerben<br/>-Windows Server 2012-ben<br/>-Windows Server 2008 R2 SP1 vagy újabb verzió<br/><br/> Az operációs rendszer telepítenie kell a c meghajtót jelenti. Az operációsrendszer-lemezképet Windows alaplemeznek kell lennie, és nem dinamikus. Az adatok lemez lehet dinamikus.<br/><br/>|
| **Linux-gépek** (fizikai vagy VMware) | A gép a következő támogatott 64 bites operációs rendszerek valamelyikét kell futtatnia: <br/>-Red Hat Enterprise Linux 5.2-5.11 6.1 való 6.9, 7.0 való 7.3<br/>-CentOS 5.2-5.11 6.1 való 6.9, 7.0 való 7.3<br/>-Kiszolgáló Ubuntu 14.04 LTS (Ubuntu támogatott kernel-verziók listáját lásd: [támogatott operációs rendszerek](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions))<br/>-Ubuntu 16.04 LTS server (Ubuntu támogatott kernel-verziók listáját lásd: [támogatott operációs rendszerek](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions))<br/>-Debian 7 vagy Debian 8<br/>-Oracle vállalati 6.5-ös vagy 6.4 futó Linux vagy a Red Hat-kompatibilis kernel, vagy a szoros vállalati Kernel Release 3 (UEK3)<br/>-SUSE Linux Enterprise Server 11 SP4 vagy SUSE Linux Enterprise Server 11 SP3<br/><br/>A/Etc/Hosts fájlt a védett gépek bejegyzéseit, amelyek társított összes hálózati adapter IP-címek hozzárendelését a helyi állomás nevét kell rendelkeznie.<br/><br/>A feladatátvétel után ha szeretné csatlakoztatni egy Azure virtuális gépre, a Secure Shell (SSH) ügyfél Linux rendszert futtató, győződjön meg arról, hogy az SSH szolgáltatást a védett számítógépen a rendszer kezdőlapon automatikus indításra van beállítva. Győződjön meg arról is, hogy a tűzfalszabályok engedélyezik-e az SSH-kapcsolatot a védett számítógépet.<br/><br/>Az állomásnév, csatlakoztatási pontokat, eszköz, és a Linux rendszer elérési utak és fájl nevét (például a /etc/ és a/usr) kell angolul csak.<br/><br/>A következő könyvtárak (Ha külön partíciókként beállítása vagy fájlrendszerek) kell lennie azon a lemezen (az operációs rendszer lemezének) a forráskiszolgálón:<br/>- / (root)<br/>- / rendszerindító<br/>-/ usr<br/>-/ usr/helyi<br/>-/var<br/>- / stb<br/><br/>Jelenleg XFS v5 funkciók – például a metaadatok ellenőrzőösszeg nem támogatottak a Site Recovery által XFS fájlrendszeren. Győződjön meg arról, hogy a XFS fájlrendszerek nem használják az v5 szolgáltatásokat. A xfs_info segédprogram segítségével ellenőrizze a XFS superblock a partíció. Ha **ftype** értéke **1**, XFS v5 szolgáltatásokat használják.<br/><br/>Red Hat Enterprise Linux 7 és a CentOS 7 kiszolgálókon az lsof segédprogram telepítve és elérhetőnek kell lenniük.<br/><br/>


## <a name="disaster-recovery-of-hyper-v-vms-to-azure-no-vmm"></a>Vész-helyreállítási Hyper-V virtuális gépek Azure-ba (VMM nélkül)

A következő összetevők szükségesek a vész-helyreállítási Hyper-V virtuális gépek VMM-felhőkben. Ezek a mellett a ismertetettekhez [Azure-követelményeknek](#azure-requirements).

| **Előfeltétel** | **Részletek** |
| --- | --- |
| **Hyper-V gazdagép** |Egy vagy több helyszíni kiszolgálók futnia kell a legújabb frissítéseket és a Hyper-V szerepkör engedélyezve van a Windows Server 2012 R2 vagy Microsoft Hyper-V Server 2012 R2.<br/><br/>Hyper-V kiszolgálók rendelkeznie kell egy vagy több virtuális gépeket.<br/><br/>Hyper-V kiszolgálók kapcsolódnia kell az Internet közvetlen vagy proxyn keresztülmenő.<br/><br/>Hyper-V-kiszolgálóknak rendelkezniük kell a Tudásbázis-cikkben ismertetett javítások [2961977](https://support.microsoft.com/kb/2961977) telepítve.
|**Provider és Agent**| Site Recovery üzembe helyezése során az Azure Site Recovery provider telepítése. A szolgáltató telepítését is telepíti az Azure Recovery Services Agent ügynököt minden védeni kívánt virtuális gépeket futtató Hyper-V kiszolgálón. <br/><br/>A Site Recovery-tárolóban található összes Hyper-V kiszolgáló rendelkeznie kell a szolgáltató és az ügynök azonos verzióit.<br/><br/>A szolgáltató kapcsolódnia kell a Site Recovery az interneten keresztül. Közvetlen vagy proxyn keresztüli forgalmat lehet küldeni. HTTPS-alapú proxyk nem támogatott. A proxykiszolgálóhoz a következő URL-címek hozzáférést kell engedélyeznie:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/>Ha IP-cím alapuló tűzfalszabályok működnek a kiszolgálón, győződjön meg arról, hogy a szabályok engedélyezik-e a kommunikációt az Azure-bA.<br/><br/> Engedélyezi a [Azure datacenter IP-címtartományok](https://www.microsoft.com/download/confirmation.aspx?id=41653), és a HTTPS (443-as port).<br/><br/> Lehetővé teszi az IP-címtartományok az Azure-régió, az előfizetés, és a nyugati USA (hozzáférés-vezérlés és identitás kezelésre szolgáló).


## <a name="disaster-recovery-of-hyper-v-vms-in-vmm-clouds-to-azure"></a>Az Azure-bA Hyper-V virtuális gépek VMM-felhőkben vész-helyreállítási

A következő összetevők szükségesek a vész-helyreállítási Hyper-V virtuális gépek VMM-felhőkben. Ezek a mellett a ismertetettekhez [Azure-követelményeknek](#azure-requirements).

| **Előfeltétel** | **Részletek** |
| --- | --- |
| **A Virtual Machine Manager** |Rendelkeznie kell legalább egy VMM-kiszolgálókon futó System Center 2012 R2 vagy újabb verzió. Minden VMM-kiszolgálót kell rendelkeznie a konfigurált egy vagy több felhőt. <br/><br/>Felhő kell rendelkeznie:<br/>– Egy vagy több VMM-gazdagépcsoportot.<br/>– Egy vagy több Hyper-V gazdakiszolgálók vagy fürtök minden gazdagépcsoportban.<br/><br/>VMM-felhőkben beállításával kapcsolatos további információkért lásd: [felhő létrehozása a Virtual Machine Manager 2012](http://social.technet.microsoft.com/wiki/contents/articles/2729.how-to-create-a-cloud-in-vmm-2012.aspx). |
| **Hyper-V** |A Hyper-V-kiszolgálókon legalább a Windows Server 2012 R2 verziónak kell futnia engedélyezett Hyper-V szerepkörrel vagy a Microsoft Hyper-V Server 2012 R2 verziónak. Telepíteni kell a legújabb frissítéseket.<br/><br/> A Hyper-V server rendelkeznie kell egy vagy több virtuális gépeket.<br/><br/> A Hyper-V gazdakiszolgáló vagy fürt, amely tartalmazza a replikálni kívánt virtuális gépek VMM-felhőben kell kezelni.<br/><br/>Hyper-V kiszolgálók kapcsolódnia kell az Internet közvetlen vagy proxyn keresztülmenő.<br/><br/>Hyper-V-kiszolgálóknak rendelkezniük kell a Tudásbázis-cikkben ismertetett javítások [2961977](https://support.microsoft.com/kb/2961977) telepítve.<br/><br/>Hyper-V gazdakiszolgálók Internet-hozzáférés szükséges replikálása az Azure-bA. |
| **Provider és Agent** |Azure Site Recovery a telepítés során telepítse az Azure Site Recovery Provider a VMM-kiszolgálón. Recovery Services Agent telepítése a Hyper-V gazdagépekre. A provider és agent közvetlenül az interneten keresztül vagy proxyn keresztüli Azure csatlakozni kell. A HTTPS-alapú proxyk nem támogatottak. A VMM-kiszolgáló és a Hyper-V gazdagépeken használt proxykiszolgálónak engedélyeznie kell a hozzáférést: <br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>Ha IP-cím alapú tűzfal-szabályokat a VMM-kiszolgálón, győződjön meg arról, hogy a szabályok engedélyezik-e a kommunikációt az Azure-bA.<br/><br/> Engedélyezi a [Azure datacenter IP-címtartományok](https://www.microsoft.com/download/confirmation.aspx?id=41653) és a HTTPS (443-as port).<br/><br/>Engedélyezi az Azure-régió az előfizetés, és a nyugati USA (hozzáférés-vezérlés és identitás kezelésre szolgáló) IP-címtartományát.<br/><br/> |

### <a name="replicated-machine-prerequisites"></a>A replikált gép előfeltételei

| **Összetevő** | **Részletek** |
| --- | --- |
| **Védett virtuális gépek** | A Site Recovery támogatja az összes operációs rendszer által támogatott [Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).<br/><br/>Virtuális gépek meg kell felelnie a [Azure-Előfeltételek](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) egy Azure virtuális gép létrehozásához. Számítógép nevének 1 és 63 karakter között (használható betűket, számokat és kötőjeleket) kell lennie. A név kell kezdődnie, betűvel vagy számmal, és betűvel vagy számmal végződhet. <br/><br/>A virtuális gép replikációjának bekapcsolását követően módosíthatja a virtuális gép nevét. <br/><br/> Minden védett gép lemezkapacitás 1,023 GB-nál több nem lehet. Egy virtuális Géphez legfeljebb 16 lemez lehet (legfeljebb 16 TB).<br/><br/>


## <a name="disaster-recovery-of-hyper-v-vms-in-vmm-clouds-to-a-customer-owned-site"></a>Vész-helyreállítási Hyper-V virtuális gépek VMM-felhőkben felhasználói tulajdonú helyhez

A következő összetevők szükségesek a vész-helyreállítási Hyper-V virtuális gépek VMM-felhőkben felhasználói tulajdonú helyhez. Ezek a mellett a ismertetettekhez [Azure-követelményeknek](#azure-requirements).

| **Összetevő** | **Részletek** |
| --- | --- |
| **A Virtual Machine Manager** |  Azt javasoljuk, hogy a VMM-kiszolgáló az elsődleges hely és a másodlagos helyet telepít.<br/><br/> Egyetlen VMM-kiszolgálón felhők közötti replikációt, legalább két, a VMM-kiszolgálón konfigurált felhők van szükség.<br/><br/> VMM-kiszolgálókon legalább futnia kell a System Center 2012 SP1, a legújabb frissítésekkel.<br/><br/> Minden VMM-kiszolgálót rendelkeznie kell egy vagy több felhőt. Az összes felhő rendelkeznie kell a Hyper-V kapacitás-készletet. <br/><br/>Felhők rendelkeznie kell legalább egy VMM-gazdagépcsoportot. VMM-felhőkben beállításával kapcsolatos további információkért lásd: [Azure Site Recovery üzembe helyezésének előkészítése](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric). |
| **Hyper-V** | Hyper-V kiszolgálók legalább futnia kell a Windows Server 2012-ben a Hyper-V szerepkör engedélyezve van, és a legújabb frissítések telepítve van.<br/><br/> A Hyper-V server rendelkeznie kell egy vagy több virtuális gépeket.<br/><br/>  Hyper-V gazdakiszolgálók gazdagépcsoportokban az elsődleges és másodlagos VMM-felhőkben kell elhelyezkedniük.<br/><br/> Ha a Hyper-V fürt, a Windows Server 2012 R2 rendszeren futtatja, azt javasoljuk, hogy a frissítés a Tudásbázis következő cikke [2961977](https://support.microsoft.com/kb/2961977).<br/><br/> Ha a Hyper-V fürt futó Windows Server 2012 és a statikus IP cím alapú fürtöt, nem a fürtszervező automatikusan jön létre. A fürtszervező manuálisan kell konfigurálni. A fürt-Szervezővel kapcsolatos további információkért lásd: [konfigurálja a replika broker szerepkört a fürtök közötti replikáció](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx). |
| **Szolgáltató** | Site Recovery üzembe helyezése során telepítse az Azure Site Recovery szolgáltató VMM-kiszolgálókon. A szolgáltató replikációra HTTPS (443-as port) PROTOKOLLON keresztül kommunikál a Site Recovery. Adatok replikáció az elsődleges és másodlagos Hyper-V kiszolgálók között a helyi hálózaton keresztül, vagy egy VPN-kapcsolaton keresztül.<br/><br/> A VMM-kiszolgálón futó szolgáltató a következő URL-hozzáférésre van szüksége:<br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>A Site Recovery provider engedélyeznie kell a VMM-kiszolgáló a tűzfal-kommunikációt a [Azure datacenter IP-címtartományok](https://www.microsoft.com/download/confirmation.aspx?id=41653), és engedélyezze a HTTPS (443-as port) protokollt. |


## <a name="url-access"></a>URL hozzáférés
Az URL-címek VMware, a VMM és a Hyper-V gazdakiszolgálók elérhetőnek kell lennie:

|**URL-CÍME** | **A VMM VMM** | **VMM-replikáció Azure-ba** | **Hyper-V – Azure** | **VMware – Azure** |
|--- | --- | --- | --- | --- |
|``*.accesscontrol.windows.net`` | Engedélyezés | Engedélyezés | Engedélyezés | Engedélyezés |
|``*.backup.windowsazure.com`` | Nem szükséges | Engedélyezés | Engedélyezés | Engedélyezés |
|``*.hypervrecoverymanager.windowsazure.com`` | Engedélyezés | Engedélyezés | Engedélyezés | Engedélyezés |
|``*.store.core.windows.net`` | Engedélyezés | Engedélyezés | Engedélyezés | Engedélyezés |
|``*.blob.core.windows.net`` | Nem szükséges | Engedélyezés | Engedélyezés | Engedélyezés |
|``https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi`` | Nem szükséges | Nem szükséges | Nem szükséges | SQL letölthető engedélyezése |
|``time.windows.com`` | Engedélyezés | Engedélyezés | Engedélyezés | Engedélyezés|
|``time.nist.gov`` | Engedélyezés | Engedélyezés | Engedélyezés | Engedélyezés |
