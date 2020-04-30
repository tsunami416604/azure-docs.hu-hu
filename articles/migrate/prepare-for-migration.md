---
title: Gépek előkészítése a Azure Migrate való áttelepítéshez
description: Megtudhatja, hogyan készítheti elő a helyszíni gépeket a Azure Migrate való áttelepítéshez.
ms.topic: tutorial
ms.date: 02/17/2020
ms.custom: MVC
ms.openlocfilehash: eba177a254606bb847e0866ae48281a889c53f9b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "78927468"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Helyszíni gépek előkészítése az Azure-ba való áttelepítésre

Ez a cikk azt ismerteti, hogyan készítse elő a helyszíni gépeket az Azure-ba való Migrálás megkezdése előtt a [Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool)paranccsal.

Ebben a cikkben:
> [!div class="checklist"]
> * Ellenőrizze az áttelepítési korlátozásokat.
> * Keresse meg az operációs rendszerre vonatkozó követelményeket és a támogatási korlátozásokat.
> * Tekintse át az áttelepíteni kívánt gépek URL-címét és porthoz való hozzáférését.
> * Tekintse át az áttelepítés megkezdése előtt szükségessé vált módosításokat.
> * Konfigurálja a beállításokat, így a meghajtóbetűjelek megmaradnak az áttelepítés után.
> * Készítse elő a gépeket, hogy az áttelepítés után csatlakozni lehessen az Azure virtuális gépekhez.

## <a name="verify-migration-limitations"></a>Áttelepítési korlátozások ellenőrzése

- Azure Migrate kiszolgáló áttelepítésével akár 35 000 VMware virtuális gépet/Hyper-V virtuális gépet is felhasználhat egyetlen Azure Migrate-projektben. Egy projekt kombinálhatja a VMware virtuális gépeket és a Hyper-V virtuális gépeket, akár az egyes korlátokra is.
- Egyszerre legfeljebb 10 virtuális gépet választhat az áttelepítéshez. Ha további replikálásra van szüksége, replikálja a 10. csoportba.
- A VMware ügynök nélküli Migrálás esetében akár 100 replikálást is futtathat egyszerre.

## <a name="verify-operating-system-requirements"></a>Operációs rendszerre vonatkozó követelmények ellenőrzése

- Ellenőrizze, hogy a [Windows operációs rendszerek](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) támogatottak-e az Azure-ban.
- Ellenőrizze, hogy a [Linux-disztribúciók](../virtual-machines/linux/endorsed-distros.md) támogatottak-e az Azure-ban.

## <a name="see-whats-supported"></a>Lásd: mi támogatott

A VMware virtuális gépek esetében a kiszolgáló áttelepítése az [ügynök nélküli vagy az ügynök-alapú áttelepítést](server-migrate-overview.md)is támogatja.

- **VMWare virtuális gépek**: Ellenőrizze az [áttelepítési követelményeket és](migrate-support-matrix-vmware-migration.md) a VMWare virtuális gépek támogatását.
- **Hyper-v virtuális gépek**: Ellenőrizze az [áttelepítési követelményeket és](migrate-support-matrix-hyper-v-migration.md) a Hyper-v virtuális gépek támogatását.
- **Fizikai gépek**: Ellenőrizze az [áttelepítési követelményeket és](migrate-support-matrix-physical-migration.md) a helyszíni fizikai gépek és más virtualizált kiszolgálók támogatását. 

## <a name="review-url-and-port-access"></a>URL-cím és a port elérésének áttekintése

Előfordulhat, hogy a számítógépeken az áttelepítés során internet-hozzáférés szükséges.

- **Azure Migrate berendezés**: Tekintse át az [URL-címeket](migrate-appliance.md#url-access) és a [portokat](migrate-support-matrix-vmware-migration.md#agentless-ports) , amelyekre az Azure Migrate berendezésnek szüksége van az ügynök nélküli áttelepítés során.
- **VMware VM-ügynök alapú áttelepítés**: a replikációs berendezés által a VMware VM-ügynökön alapuló áttelepítés során használt [URL-címek](migrate-replication-appliance.md#url-access) és [portok](migrate-replication-appliance.md#port-access) áttekintése. 
- **Hyper-v-gazdagépek**: Ellenőrizze, hogy a Hyper-v-gazdagépeknek milyen [URL-címeket és portokat](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) kell elérniük az áttelepítés során. 
- **Fizikai kiszolgálók**: Tekintse át a replikációs berendezés által a fizikai kiszolgáló áttelepítése során használt [URL-címek](migrate-replication-appliance.md#url-access) és [portok](migrate-replication-appliance.md#port-access) áttekintését.

## <a name="verify-required-changes-before-migrating"></a>A szükséges módosítások ellenőrzése az áttelepítés előtt

Előfordulhat, hogy egyes virtuális gépek módosításokat igényelnek, hogy az Azure-ban is futtathatók legyenek. A Azure Migrate a következő operációs rendszereket futtató virtuális gépek esetében automatikusan végrehajtja ezeket a módosításokat:

- Red Hat Enterprise Linux 7.0 +, 6.5 +
- CentOS 7.0 +, 6.5 +
- SUSE Linux Enterprise Server 12 SP1 +
- Ubuntu 18.04 LTS, 16.04 LTS, 14.04 LTS
- Debian 8, 7

Más operációs rendszerek esetén manuálisan kell előkészítenie a gépeket az áttelepítés előtt. 

### <a name="prepare-windows-machines"></a>Windows rendszerű gépek előkészítése

Ha Windows rendszerű gépet telepít át, végezze el a következő módosításokat az áttelepítés előtt. Ha a módosítások végrehajtása előtt telepíti át a virtuális gépet, előfordulhat, hogy a virtuális gép nem indul el az Azure-ban.

1. [Engedélyezze az Azure soros konzolt](../virtual-machines/troubleshooting/serial-console-windows.md) az Azure-beli virtuális gépen. A konzol engedélyezése segít a hibakeresésben. Nem kell újraindítani a virtuális gépet. Az Azure-beli virtuális gép a lemezkép használatával fog elindulni. A lemezkép rendszerindítási értéke megegyezik az új virtuális gép újraindításával. 
2. Ha Windows Server 2003 rendszert futtató gépeket telepít át, telepítse a Hyper-V vendég-integrációs szolgáltatásokat a VM operációs rendszeren. [További információ](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).

### <a name="prepare-linux-machines"></a>Linuxos gépek előkészítése

1. Telepítse a Hyper-V Linux Integration Services szolgáltatást. A Linux-disztribúciók legtöbb új verziója tartalmazza a Hyper-V Linux Integration Services szolgáltatást.
2. Építse újra a Linux init-rendszerképet, hogy tartalmazza a szükséges Hyper-V-illesztőprogramokat. Az init-rendszerkép újraépítése biztosítja, hogy a virtuális gép az Azure-ban induljon (csak bizonyos disztribúciókban szükséges).
3. Az [Azure soros konzol naplózásának engedélyezése](../virtual-machines/troubleshooting/serial-console-linux.md). A konzol naplózásának engedélyezése segít a hibakeresésben. Nem kell újraindítani a virtuális gépet. Az Azure-beli virtuális gép a lemezkép használatával fog elindulni. A lemezkép rendszerindítási értéke megegyezik az új virtuális gép újraindításával.
4. Frissítse az eszköz-hozzárendelési fájlt az eszköz név – kötet társításával, így állandó eszköz-azonosítókat használhat.
5. Frissítse az fstab fájlban lévő bejegyzéseket az állandó kötet-azonosítók használatára.
6. Távolítson el minden olyan udev-szabályt, amely a MAC-címeken alapuló kapcsolati neveket foglal le, és így tovább.
7. A hálózati adapterek frissítése a DHCP IP-címének fogadására.

További információ a [Linux rendszerű virtuális gépek Azure-ban való futtatásának lépéseiről](../virtual-machines/linux/create-upload-generic.md), valamint a népszerű Linux-disztribúciók használati utasításai.

## <a name="preserve-drive-letters-after-migration"></a>Meghajtóbetűjelek megőrzése az áttelepítés után

Amikor helyszíni gépet telepít át Microsoft Azurere, a további adatlemezek meghajtóbetűjelei megváltozhatnak az eredeti értékükre. 

Alapértelmezés szerint az Azure-beli virtuális gépek a D meghajtót használják ideiglenes tárolóként való használatra. Ez a meghajtó-hozzárendelés azt eredményezi, hogy az összes többi csatlakoztatott tárolási meghajtó-hozzárendelés egy betűvel növekszik. Ha például a helyszíni telepítés olyan adatlemezt használ, amely az alkalmazás telepítéséhez a D meghajtóhoz van rendelve, a meghajtó hozzárendelése a virtuális gép Azure-ba való áttelepítése után az E meghajtóra növekszik. Ha meg szeretné akadályozni az automatikus hozzárendelést, és győződjön meg arról, hogy az Azure a következő szabad meghajtóbetűjelet rendeli hozzá az ideiglenes kötethez, állítsa a Tárolóhálózati (SAN) házirendet **OnlineAll**:

1. Nyisson meg egy rendszergazda jogú parancssort a helyszíni gépen (nem a gazdagépen).
2. Adja meg a **diskpartt**.
3. Adja meg a **San**értéket. Ha a vendég operációs rendszer meghajtóbetűjele nincs karbantartva, a rendszer **offline állapotba** helyezi az összes vagy **Offline megosztást** .
4. A **DiskPart** parancssorába írja be a következőt: **San Policy = OnlineAll**. Ez a beállítás biztosítja, hogy a lemezek online állapotba kerüljenek, és lehetővé teszi a lemezek olvasását és írását is.
5. A teszt áttelepítése során ellenőrizheti, hogy a meghajtóbetűjelek megmaradnak-e.

## <a name="check-azure-vm-requirements"></a>Az Azure-beli virtuális gépekre vonatkozó követelmények keresése

Az Azure-ba replikált helyszíni számítógépeknek meg kell felelniük az operációs rendszerre és az architektúrára, a lemezekre, a hálózati beállításokra és a virtuális gépek elnevezésére vonatkozó Azure-beli virtuális gépek követelményeinek. A Migrálás előtt ellenőrizze a [VMWare virtuális gépek és a fizikai kiszolgálók](migrate-support-matrix-vmware-migration.md#azure-vm-requirements), valamint a [Hyper-V virtuális gépek](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements) követelményeit.

## <a name="prepare-to-connect-after-migration"></a>Felkészülés a csatlakozásra az áttelepítés után

Az Azure-beli virtuális gépek az Azure-ba való áttelepítés során jönnek létre. Az áttelepítés után képesnek kell lennie csatlakozni az új Azure-beli virtuális gépekhez. A sikeres kapcsolódáshoz több lépés szükséges.

### <a name="prepare-to-connect-to-azure-windows-vms"></a>Felkészülés az Azure Windows rendszerű virtuális gépekhez való csatlakozásra

Helyszíni Windows rendszerű gépeken:

1. Konfigurálja a Windows beállításait. A beállítások közé tartozik a statikus állandó útvonalak vagy a WinHTTP-proxy eltávolítása.
2. Győződjön meg arról, hogy a [szükséges szolgáltatások](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) futnak.
3. Engedélyezze a távoli asztal (RDP) szolgáltatást, hogy engedélyezze a távoli kapcsolatokat a helyszíni géppel. Megtudhatja, hogyan [engedélyezheti az RDP használatát a PowerShell használatával](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings).
4. Ha egy Azure-beli virtuális gépet az áttelepítés után az interneten keresztül szeretne elérni, a helyszíni gépen lévő Windows tűzfalon engedélyezze a TCP és az UDP protokollt a nyilvános profilban, és állítsa be az RDP-t engedélyezett alkalmazásként az összes profilhoz.
5. Ha az áttelepítés után egy helyek közötti VPN-en keresztül szeretné elérni az Azure virtuális gépet, a helyszíni gépen lévő Windows tűzfalon engedélyezze az RDP-t a tartomány és a privát profilok számára. Megtudhatja, hogyan [engedélyezheti az RDP-forgalmat](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules). 
6. A Migrálás során győződjön meg arról, hogy a helyszíni virtuális gépen nincsenek függőben lévő Windows-frissítések. Ha vannak, előfordulhat, hogy a frissítések az áttelepítés után megkezdik az Azure virtuális gépen való telepítését, és a frissítések befejezéséig nem tud majd bejelentkezni a virtuális gépre.

### <a name="prepare-to-connect-with-linux-azure-vms"></a>Felkészülés a Linux Azure-beli virtuális gépekkel való csatlakozásra

Helyszíni Linux rendszerű gépeken:

1. Győződjön meg arról, hogy a Secure Shell szolgáltatás úgy van beállítva, hogy automatikusan induljon el a rendszerindításkor.
2. Ellenőrizze, hogy a tűzfalszabályok engedélyezik-e az SSH-kapcsolatot.

### <a name="configure-azure-vms-after-migration"></a>Azure-beli virtuális gépek konfigurálása Migrálás után

Az áttelepítés után hajtsa végre a következő lépéseket a létrehozott Azure-beli virtuális gépeken:

1. Ha az interneten keresztül szeretne csatlakozni a virtuális géphez, rendeljen egy nyilvános IP-címet a virtuális géphez. Az Azure-beli virtuális gép számára eltérő nyilvános IP-címet kell használnia, mint amelyet a helyszíni géphez használt. [További információ](../virtual-network/virtual-network-public-ip-address.md).
2. Győződjön meg arról, hogy a virtuális gép hálózati biztonsági csoport (NSG) szabályai engedélyezik a bejövő kapcsolatokat az RDP-vagy SSH-porton.
3. A virtuális gép megtekintéséhez tekintse meg a [rendszerindítási diagnosztikát](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) .

> [!NOTE]
> Az Azure Bastion szolgáltatás privát RDP-és SSH-hozzáférést biztosít az Azure-beli virtuális gépekhez. [További](../bastion/bastion-overview.md) információ erről a szolgáltatásról.

## <a name="next-steps"></a>További lépések

Döntse el, hogy melyik módszert szeretné használni a [VMWare virtuális gépek Azure-ba való áttelepítéséhez](server-migrate-overview.md) , vagy a [Hyper-V virtuális gépek](tutorial-migrate-hyper-v.md) vagy [fizikai kiszolgálók, illetve virtualizált vagy felhőalapú virtuális gépek](tutorial-migrate-physical-virtual-machines.md)áttelepítésének megkezdéséhez.
