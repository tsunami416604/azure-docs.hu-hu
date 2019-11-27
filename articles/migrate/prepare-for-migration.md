---
title: Gépek előkészítése a Azure Migrate való áttelepítéshez
description: Megtudhatja, hogyan készítheti elő a helyszíni gépeket a Azure Migrate való áttelepítéshez.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 2c13bc79fccc74da431ba1a6399870b261b24c57
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196422"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Helyszíni gépek előkészítése az Azure-ba való áttelepítésre

Ez a cikk azt ismerteti, hogyan készítse elő a helyszíni gépeket az Azure-ba való Migrálás megkezdése [Azure Migrate kiszolgáló áttelepítése](migrate-services-overview.md#azure-migrate-server-migration-tool)előtt.


Ebben a cikkben:
> [!div class="checklist"]
> * Ellenőrizze az áttelepítési korlátozásokat.
> * Keresse meg az operációs rendszerre vonatkozó követelményeket és a támogatási korlátozásokat.
> * Tekintse át az áttelepíteni kívánt gépek URL-címét és porthoz való hozzáférését.
> * Tekintse át az áttelepítés megkezdése előtt szükségessé vált módosításokat.
> * Konfigurálja úgy a beállításokat, hogy a meghajtóbetűjelek megmaradjanak az áttelepítés után.
> * Készítse elő a gépeket, hogy az áttelepítés után csatlakozni lehessen az Azure-beli virtuális gépekhez.


## <a name="verify-migration-limitations"></a>Áttelepítési korlátozások ellenőrzése

- Azure Migrate kiszolgáló áttelepítésével akár 35 000 VMware virtuális gépet/Hyper-V virtuális gépet is felhasználhat egyetlen Azure Migrate-projektben. Egy projekt kombinálhatja a VMware virtuális gépeket és a Hyper-V virtuális gépeket is, az egyes korlátokig.
- Az áttelepítéshez egyszerre legfeljebb 10 virtuális gépet választhat. Ha további replikálásra van szüksége, replikálja a 10. csoportba.
- A VMware ügynök nélküli Migrálás esetében akár 100 replikálást is futtathat egyszerre.

## <a name="verify-operating-system-requirements"></a>Operációs rendszerre vonatkozó követelmények ellenőrzése

- Ellenőrizze, hogy a [Windows operációs rendszerek](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) támogatottak-e az Azure-ban.
- Ellenőrizze, hogy a [Linux-disztribúciók](../virtual-machines/linux/endorsed-distros.md) támogatottak-e az Azure-ban.


## <a name="check-whats-supported"></a>A támogatott elemek kikeresése

- A VMware virtuális gépek esetében Azure Migrate kiszolgáló áttelepítése az [ügynök nélküli vagy az ügynökön alapuló áttelepítést](server-migrate-overview.md)támogatja. Ellenőrizze a VMware virtuális gépekre vonatkozó követelményeket/támogatást az [ügynök](migrate-support-matrix-vmware.md#migration---limitations) nélküli és az [ügynökön alapuló](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) áttelepítéshez.
- Ellenőrizze az [áttelepítési követelményeket és](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) a Hyper-V virtuális gépek támogatását.
- Ellenőrizze az [áttelepítési követelményeket és](migrate-support-matrix-physical.md) a helyszíni fizikai gépek vagy más virtualizált kiszolgálók támogatását. 




## <a name="review-urlport-access"></a>URL-cím/port elérésének áttekintése

Előfordulhat, hogy a számítógépeken az áttelepítés során internet-hozzáférés szükséges.

- Tekintse át azokat az URL-címeket, amelyekhez a VMware virtuális gépeknek az [ügynök](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) nélküli vagy az [ügynökön alapuló](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements) áttelepítés során szüksége van
- Tekintse át, hogy a Hyper-V-gazdagépeknek milyen URL-címeket kell elérniük az áttelepítés során. A Hyper-V virtuális gépeknek nincs szükségük internet-hozzáférésre.
- [Tekintse át](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements) , hogy a fizikai gépek vagy más virtualizált kiszolgálók milyen URL-címeket kell elérniük az áttelepítés során.
- A VMware virtuális gépek/fizikai kiszolgálók ügynök-alapú áttelepítése esetén a gépen futó mobilitási szolgáltatásnak hozzá kell férnie Azure Migrate-összetevőkhöz. A replikálás kezeléséhez a gépen futó szolgáltatás a HTTPS 443 bejövő porton keresztül kommunikál a helyszíni Azure Migrate replikációs berendezéssel. A gépek replikációs adatküldést küldenek a Azure Migrate Process Servernek a HTTPS 9443 bejövő porton. Ez a port módosítható.


## <a name="verify-required-changes-before-migration"></a>A szükséges módosítások ellenőrzése az áttelepítés előtt

Előfordulhat, hogy egyes virtuális gépek módosításokat igényelnek, hogy az Azure-ban is futtathatók legyenek. A Azure Migrate a következő operációs rendszereket futtató virtuális gépek esetében automatikusan végrehajtja ezeket a módosításokat:
- Red Hat Enterprise Linux 6.5 +, 7.0 +
- CentOS 6.5 +, 7.0 +
- SUSE Linux Enterprise Server 12 SP1 +
- Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS
- Debian 7, 8

Más operációs rendszerek esetén manuálisan kell előkészítenie a gépeket az áttelepítés előtt. 

### <a name="prepare-windows-machines"></a>Windows rendszerű gépek előkészítése

Ha Windows rendszerű gépet telepít át, hajtsa végre ezeket a módosításokat az áttelepítés előtt. Ha a módosítások végrehajtása előtt telepíti át a virtuális gépet, előfordulhat, hogy a virtuális gép nem indul el az Azure-ban.

1. [Engedélyezze az Azure soros hozzáférési konzolt](../virtual-machines/troubleshooting/serial-console-windows.md) az Azure-beli virtuális géphez. Ez segít a hibaelhárításban. Nem kell újraindítani a virtuális gépet. Az Azure-beli virtuális gép a lemezkép használatával fog elindulni. Ez egyenértékű az új virtuális gép újraindításával. 
2. Ha Windows Server 2003 rendszert futtató gépeket telepít át, telepítse a Hyper-V vendég integrációs szolgáltatásait a VM operációs rendszerbe. [További információk](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).

### <a name="prepare-linux-machines"></a>Linuxos gépek előkészítése

1. Telepítse a Hyper-V Linux Integration Services szolgáltatást. A Linux-disztribúciók legtöbb új verziója ezt alapértelmezés szerint tartalmazza.
2. Hozza létre újra a Linux init-rendszerképet, hogy az tartalmazza a szükséges Hyper-V-illesztőprogramokat. Ez biztosítja, hogy a virtuális gép az Azure-ban induljon el, és csak bizonyos disztribúciókban szükséges.
3. Az [Azure soros konzol naplózásának engedélyezése](../virtual-machines/troubleshooting/serial-console-linux.md). Ez segít a hibaelhárításban. Nem kell újraindítani a virtuális gépet. Az Azure-beli virtuális gép a lemezkép használatával fog elindulni. Ez egyenértékű az új virtuális gép újraindításával.
4. Frissítse az eszköz-hozzárendelési fájlt az eszköz nevével a mennyiségi társításokhoz, hogy állandó eszköz-azonosítókat használjon.
5. Az fstab-bejegyzések frissítése állandó kötet-azonosítók használatára.
6. Távolítson el minden olyan udev-szabályt, amely MAC-címeken alapuló kapcsolati neveket foglal le stb.
7. A hálózati adapterek frissítése a DHCP IP-címének fogadására.
8. [További](../virtual-machines/linux/create-upload-generic.md) információ a Linux RENDSZERű virtuális gépek Azure-ban való futtatásához szükséges lépésekről, valamint a népszerű Linux-disztribúciók használati utasításait ismertető cikkben talál.

## <a name="preserve-drive-letters-after-migration"></a>Meghajtóbetűjelek megőrzése az áttelepítés után

Amikor helyszíni gépet telepít át Microsoft Azurere, a további adatlemezek meghajtóbetűjelei változhatnak a korábbi értékek alapján. Alapértelmezés szerint az Azure-beli virtuális gépeket a D meghajtó rendeli hozzá ideiglenes tárolóként való használatra. Ez a meghajtó-hozzárendelés azt eredményezi, hogy az összes többi csatlakoztatott tárolási meghajtó-hozzárendelés egy betűvel növekszik.

Ha például a helyszíni telepítés olyan adatlemezt használ, amely az alkalmazás telepítéséhez a D meghajtóhoz van rendelve, a meghajtó hozzárendelése a virtuális gép Azure-ba való áttelepítése után az E meghajtóra növekszik. Ha meg szeretné akadályozni ezt az automatikus hozzárendelést, és győződjön meg arról, hogy az Azure a következő szabad meghajtóbetűjelet rendeli hozzá az ideiglenes kötethez, állítsa a Tárolóhálózati (SAN) házirendet OnlineAll, a következőképpen:

1. A helyszíni gépen (nem a gazdagépen) nyisson meg egy rendszergazda jogú parancssort.
2. Írja be a **DiskPart**programot.
3. Írja be a **San**típust. Ha a vendég operációs rendszer meghajtóbetűjele nincs karbantartva, a rendszer **offline állapotba** helyezi az összes vagy **Offline megosztást** .
4. A **DiskPart** parancssorba írja be a következőt: **SANPOLICY = ONLINEALL**. Ez a beállítás biztosítja, hogy a lemezek online állapotba kerüljenek, és egyaránt olvashatók és írhatók.
5. A teszt áttelepítése során ellenőrizheti, hogy a meghajtóbetűjelek megmaradnak-e.


## <a name="check-azure-vm-requirements"></a>Az Azure-beli virtuális gépekre vonatkozó követelmények keresése

Az Azure-ba replikált helyszíni számítógépeknek meg kell felelniük az operációs rendszerre és az architektúrára, a lemezekre, a hálózati beállításokra és a virtuális gépek elnevezésére vonatkozó Azure-beli virtuális gépek követelményeinek. Ellenőrizze a [VMWare virtuális gépek/fizikai kiszolgálók](migrate-support-matrix-vmware.md#azure-vm-requirements)és a [Hyper-V virtuális gépek](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) követelményeit az áttelepítés előtt.


## <a name="prepare-to-connect-after-migration"></a>Felkészülés a csatlakozásra az áttelepítés után

Az Azure-beli virtuális gépek az Azure-ba való áttelepítés során jönnek létre. Az áttelepítés után képesnek kell lennie csatlakozni az új Azure-beli virtuális gépekhez. A sikeres kapcsolódáshoz számos lépés szükséges.

### <a name="prepare-to-connect-to-windows-azure-vms"></a>Felkészülés a Windows Azure-beli virtuális gépekhez való csatlakozásra

Helyszíni Windows rendszerű gépeken tegye a következőket:

1. Konfigurálja a Windows beállításait. Ilyenek például a statikus állandó útvonalak vagy a WinHTTP-proxyk eltávolítása.
2. Győződjön meg arról, hogy [ezek a szolgáltatások](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) futnak.
3. Engedélyezze a távoli asztal (RDP) szolgáltatást, hogy engedélyezze a távoli kapcsolatokat a helyszíni géppel. [Ismerje meg, hogyan](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings) engedélyezheti az RDP-t a PowerShell használatával.
4. Ha egy Azure-beli virtuális gépet az áttelepítés után az interneten keresztül szeretne elérni, a helyszíni gépen lévő Windows tűzfalon engedélyezze a TCP és az UDP protokollt a nyilvános profilban, és állítsa be az RDP-t engedélyezett alkalmazásként az összes profilhoz.
5. Ha az áttelepítés után egy helyek közötti VPN-en keresztül szeretné elérni az Azure virtuális gépet, a helyszíni gépen lévő Windows tűzfalon engedélyezze az RDP-t a tartomány és a privát profilok számára. [Megtudhatja](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules) , hogyan engedélyezheti az RDP-forgalmat. 
6. A Migrálás során győződjön meg arról, hogy a helyszíni virtuális gépen nincsenek függőben lévő Windows-frissítések. Ha vannak, előfordulhat, hogy a frissítések az áttelepítés után megkezdik az Azure virtuális gépen való telepítését, és a frissítések befejezéséig nem tud majd bejelentkezni a virtuális gépre.


### <a name="prepare-to-connect-with-linux-azure-vms"></a>Felkészülés a Linux Azure-beli virtuális gépekkel való csatlakozásra

A helyszíni Linux rendszerű gépeken tegye a következőket:

1. Győződjön meg arról, hogy a Secure Shell szolgáltatás úgy van beállítva, hogy automatikusan induljon el a rendszerindításkor.
2. Ellenőrizze, hogy a tűzfalszabályok engedélyezik-e az SSH-kapcsolatot.

### <a name="configure-azure-vms-after-migration"></a>Azure-beli virtuális gépek konfigurálása Migrálás után

Az áttelepítés után tegye a következőket a létrehozott Azure-beli virtuális gépeken.

1. Ha az interneten keresztül szeretne csatlakozni a virtuális géphez, rendeljen egy nyilvános IP-címet a virtuális géphez. Nem használhatja ugyanazt a nyilvános IP-címet a helyszíni géphez használt Azure-beli virtuális géphez. [További információk](../virtual-network/virtual-network-public-ip-address.md).
2. Győződjön meg arról, hogy a virtuális gép hálózati biztonsági csoport (NSG) szabályai engedélyezik a bejövő kapcsolatokat az RDP-vagy SSH-porton.
3. A virtuális gép megtekintéséhez tekintse meg a [rendszerindítási diagnosztikát](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) .

> [!NOTE]
> Az Azure Bastion szolgáltatás privát RDP-és SSH-hozzáférést biztosít az Azure-beli virtuális gépekhez. [További](../bastion/bastion-overview.md) információ erről a szolgáltatásról.



## <a name="next-steps"></a>Következő lépések

Döntse el, hogy melyik módszert szeretné használni a [VMWare virtuális gépek Azure-ba való áttelepítéséhez](server-migrate-overview.md) , vagy a [Hyper-V virtuális gépek](tutorial-migrate-hyper-v.md) vagy [fizikai kiszolgálók vagy virtualizált/Felhőbeli virtuális gépek](tutorial-migrate-physical-virtual-machines.md)áttelepítésének megkezdéséhez.
