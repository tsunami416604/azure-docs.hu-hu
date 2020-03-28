---
title: Gépek előkészítése az áttelepítéshez az Azure Migrate segítségével
description: Ismerje meg, hogyan készítheti elő a helyszíni gépeket az Azure Migrate áttelepítése esetén.
ms.topic: tutorial
ms.date: 02/17/2020
ms.custom: MVC
ms.openlocfilehash: eba177a254606bb847e0866ae48281a889c53f9b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78927468"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Helyszíni gépek előkészítése az Azure-ba való áttelepítéshez

Ez a cikk azt ismerteti, hogyan készítheti el a helyszíni gépeket, mielőtt az Azure-ba való áttelepítést az [Azure Áttelepítés: Kiszolgálóáttelepítés](migrate-services-overview.md#azure-migrate-server-migration-tool)használatával elkezdené.

Ebben a cikkben:
> [!div class="checklist"]
> * Ellenőrizze az áttelepítési korlátozásokat.
> * Ellenőrizze az operációs rendszer követelményeit és a támogatási korlátozásokat.
> * Tekintse át az áttelepíteni kívánt gépek URL-címét és porthoz való hozzáférését.
> * Az áttelepítés megkezdése előtt esetleg áttekintheti az áttelepítés megkezdése előtt szükséges módosításokat.
> * Konfigurálja a beállításokat úgy, hogy a meghajtóbetűjelek az áttelepítés után is megmaradjanak.
> * Készítsen elő gépeket, hogy az áttelepítés után csatlakozhassanak az Azure-beli virtuális gépekhez.

## <a name="verify-migration-limitations"></a>Áttelepítési korlátozások ellenőrzése

- Egyetlen Azure Migrate-programprojekt ben legfeljebb 35 000 VMware virtuális gép/Hyper-V VM-et mérhet fel egyetlen Azure Migrate-projektben az Azure Migrate Server Migration használatával. Egy projekt kombinálhatja a VMware virtuális gépeket és a Hyper-V virtuális gépeket, az egyes virtuális gépek korlátaiig.
- Egyszerre legfeljebb 10 virtuális gépet választhat ki az áttelepítéshez. Ha több replikálnia kell, replikálja a 10-es csoportokban.
- A VMware ügynök nélküli áttelepítése esetén egyszerre legfeljebb 100 replikáció futtatható.

## <a name="verify-operating-system-requirements"></a>Az operációs rendszer követelményeinek ellenőrzése

- Ellenőrizze, hogy a [Windows operációs rendszerek](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) támogatottak-e az Azure-ban.
- Ellenőrizze, hogy a [Linux-disztribúciók támogatottak-e](../virtual-machines/linux/endorsed-distros.md) az Azure-ban.

## <a name="see-whats-supported"></a>Tekintse meg, mi támogatott

VMware virtuális gépek esetén a Kiszolgálóáttelepítés támogatja [az ügynök nélküli vagy ügynökalapú áttelepítést.](server-migrate-overview.md)

- **VMware virtuális gépek:** Ellenőrizze [az áttelepítési követelményeket és](migrate-support-matrix-vmware-migration.md) a VMware virtuális gépek támogatását.
- **Hyper-V virtuális gépek:** Ellenőrizze [az áttelepítési követelményeket és](migrate-support-matrix-hyper-v-migration.md) a Hyper-V virtuális gépek támogatását.
- **Fizikai gépek**: Ellenőrizze [az áttelepítési követelményeket és](migrate-support-matrix-physical-migration.md) a helyszíni fizikai gépek és más virtualizált kiszolgálók támogatását. 

## <a name="review-url-and-port-access"></a>URL- és porthozzáférés áttekintése

Előfordulhat, hogy a gépeknek internet-hozzáférésre van szükségük az áttelepítés során.

- **Azure Migrate appliance:** Tekintse át [az URL-címeket](migrate-appliance.md#url-access) és [portokat,](migrate-support-matrix-vmware-migration.md#agentless-ports) amelyeket az Azure Migrate-készüléknek el kell érnie az ügynök nélküli áttelepítés során.
- **VMware VM-ügynök alapú áttelepítés:** Tekintse át a replikációs készülék által a VMware VM-ügynök alapú áttelepítés során használt [URL-címeket](migrate-replication-appliance.md#url-access) és [portokat.](migrate-replication-appliance.md#port-access) 
- **Hyper-V gazdagépek**: Tekintse át [azurl-eket és portokat, amelyeket](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) a Hyper-V-állomásoknak az áttelepítés során el kell érnie. 
- **Fizikai kiszolgálók**: Tekintse át a replikációs eszköz által a kiszolgáló fizikai áttelepítése során használt [URL-eket](migrate-replication-appliance.md#url-access) és [portokat.](migrate-replication-appliance.md#port-access)

## <a name="verify-required-changes-before-migrating"></a>A szükséges módosítások ellenőrzése áttelepítés előtt

Egyes virtuális gépek szükség lehet a módosításokat, hogy azok az Azure-ban futtatható. Az Azure Migrate automatikusan módosítja ezeket a módosításokat az operációs rendszereket futtató virtuális gépeken:

- Red Hat Enterprise Linux 7.0+, 6.5+
- CentOS 7.0+, 6.5+
- SUSE Linux Enterprise Server 12 SP1+
- Ubuntu 18.04LTS, 16.04LTS, 14.04LTS
- Debian 8, 7

Más operációs rendszerek esetében manuálisan kell előkészíteni a gépeket az áttelepítés előtt. 

### <a name="prepare-windows-machines"></a>Windows-gépek előkészítése

Ha Windows-gépet telepít át, az áttelepítés előtt hajtsa végre a következő módosításokat. Ha a módosítások végrehajtása előtt áttelepíti a virtuális gép, előfordulhat, hogy a virtuális gép nem indul el az Azure-ban.

1. [Engedélyezze](../virtual-machines/troubleshooting/serial-console-windows.md) az Azure Soros konzolt az Azure virtuális géphez. A konzol engedélyezése segít a hibaelhárításban. Nem kell újraindítania a virtuális gépet. Az Azure virtuális gép a lemezkép használatával indul. A lemezkép indítása egyenértékű az új virtuális gép újraindításával. 
2. Windows Server 2003 rendszert futtató gépek áttelepítése esetén telepítse a Hyper-V vendégintegrációs szolgáltatásokat a virtuális gép operációs rendszerére. [További információ](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).

### <a name="prepare-linux-machines"></a>Linux-gépek előkészítése

1. Telepítse a Hyper-V Linux integrációs szolgáltatásokat. A Linux disztribúciók legtöbb új verziója alapértelmezés szerint hyper-V Linux integrációs szolgáltatásokat tartalmaz.
2. Építse újra a Linux init lemezképet, így tartalmazza a szükséges Hyper-V illesztőprogramokat. Az init lemezkép újraépítése biztosítja, hogy a virtuális gép elindul az Azure-ban (csak bizonyos disztribúciók esetén szükséges).
3. [Engedélyezze az Azure soros konzolnaplózását.](../virtual-machines/troubleshooting/serial-console-linux.md) A konzolnaplózás engedélyezése segít a hibaelhárításban. Nem kell újraindítania a virtuális gépet. Az Azure virtuális gép a lemezkép használatával indul. A lemezkép indítása egyenértékű az új virtuális gép újraindításával.
4. Frissítse az eszközleképezési fájlt az eszköznév-kötet eksztúcokkal, hogy állandó eszközazonosítókat használjon.
5. Az fstab fájl bejegyzéseinek frissítése állandó kötetazonosítók használatára.
6. Távolítsa el azokat az udev-szabályokat, amelyek mac-cím alapján foglalják le a kapcsolatneveket, és így tovább.
7. Frissítse a hálózati adaptereket, hogy IP-címet kapjon a DHCP-től.

További információ a [Linux virtuális gépek Azure-beli futtatásának](../virtual-machines/linux/create-upload-generic.md)lépéseiről, és a népszerű Linux-disztribúciók néhány útmutatójának betöltéséről.

## <a name="preserve-drive-letters-after-migration"></a>Meghajtóbetűjelek megőrzése áttelepítés után

Amikor áttelepít egy helyszíni gépet a Microsoft Azure-ba, a további adatlemezek meghajtóbetűi az eredeti értékekhez képest változhatnak. 

Alapértelmezés szerint az Azure-beli virtuális gépek vannak rendelve a D meghajtó t ideiglenes tárolóként való használatra. Ez a meghajtó-hozzárendelés hatására az összes többi csatlakoztatott tárolómeghajtó-hozzárendelés egy betűvel növekszik. Ha például a helyszíni telepítés egy olyan adatlemezt használ, amely a D meghajtóhoz van rendelve az alkalmazástelepítésekhez, a meghajtó hozzárendelése az E meghajtóhoz, miután áttelepítette a virtuális gépaz Azure-ba. Az automatikus hozzárendelés megakadályozása és annak biztosítása érdekében, hogy az Azure a következő szabad meghajtóbetűjelet az ideiglenes kötetéhez rendelje, állítsa a tárolóhálózati (SAN) házirendet **OnlineAll**értékre:

1. A helyszíni gépen (nem a gazdakiszolgálón) nyisson meg egy rendszergazda jogú parancssort.
2. Írja be **a diskpart**értéket.
3. Írja be **a SAN**értéket. Ha a vendég operációs rendszer meghajtóbetűjele nem karbanmarad, a rendszer az **összes kapcsolat nélküli** vagy offline **megosztást adja** vissza.
4. A **DISKPART** parancssorba írja be a **SAN Policy=OnlineAll**című részt. Ez a beállítás biztosítja, hogy a lemezek online állapotba kerülnek, és biztosítja, hogy mindkét lemezre lehessen olvasni és írni.
5. A tesztáttelepítés során ellenőrizheti, hogy a meghajtóbetűjelek megőrződtek-e.

## <a name="check-azure-vm-requirements"></a>Az Azure virtuális gép követelményeinek ellenőrzése

Az Azure-ba replikáló helyszíni gépeknek meg kell felelniük az Azure virtuális gépre vonatkozó követelményeknek az operációs rendszer és az architektúra, a lemezek, a hálózati beállítások és a virtuális gépek elnevezése tekintetében. Az áttelepítés előtt ellenőrizze a [VMware virtuális gépek re és a fizikai kiszolgálókra,](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)valamint a [Hyper-V virtuális gépekre](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements) vonatkozó követelményeket.

## <a name="prepare-to-connect-after-migration"></a>Felkészülés az áttelepítés utáni csatlakozásra

Az Azure virtuális gépek az Azure-ba való migrálás során jönnek létre. Áttelepítés után, képesnek kell lennie arra, hogy csatlakozzon az új Azure-beli virtuális gépek. A sikeres csatlakozáshoz több lépésre van szükség.

### <a name="prepare-to-connect-to-azure-windows-vms"></a>Felkészülés az Azure Windows virtuális gépekhez való csatlakozásra

Helyszíni Windows-gépeken:

1. A Windows beállításainak konfigurálása. A beállítások közé tartozik a statikus állandó útvonalak vagy a WinHTTP proxy eltávolítása.
2. Győződjön meg arról, hogy a [szükséges szolgáltatások](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) futnak.
3. Engedélyezze a távoli asztal (RDP) engedélyezését a helyszíni számítógép távoli kapcsolatainak engedélyezéséhez. Megtudhatja, hogy miként engedélyezheti a [PowerShellt az RDP engedélyezéséhez.](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings)
4. Ha az áttelepítés után az interneten keresztül szeretne hozzáférni egy Azure-géphez, a helyszíni gépen lévő Windows tűzfalban engedélyezze a TCP-t és az UDP-t a nyilvános profilban, és állítsa be az RDP-t az összes profil engedélyezett alkalmazásaként.
5. Ha az áttelepítés után egy azure-beli VPN-en keresztül szeretne hozzáférni egy Azure-beli VPN-en keresztül, a helyszíni gépen lévő Windows tűzfalban engedélyezze az RDP szolgáltatást a tartományi és a magánprofilokhoz. További információ az [RDP-forgalom engedélyezéséről.](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules) 
6. Győződjön meg arról, hogy nincsenek függőben lévő Windows-frissítések a helyszíni virtuális gép áttelepítésekor. Ha vannak, a frissítések az áttelepítés után megkezdhetik a telepítést az Azure virtuális gépre, és nem tud bejelentkezni a virtuális gépbe, amíg a frissítések be nem fejeződnek.

### <a name="prepare-to-connect-with-linux-azure-vms"></a>Felkészülés a Linux Azure virtuális gépekhez való csatlakozásra

Helyszíni Linux-gépeken:

1. Ellenőrizze, hogy a Secure Shell szolgáltatás automatikusan elindul-e a rendszer indításakor.
2. Ellenőrizze, hogy a tűzfalszabályok engedélyezik-e az SSH-kapcsolatot.

### <a name="configure-azure-vms-after-migration"></a>Azure-beli virtuális gépek konfigurálása áttelepítés után

Áttelepítés után hajtsa végre az alábbi lépéseket a létrehozott Azure-beli virtuális gépeken:

1. Ha az interneten keresztül szeretne csatlakozni a virtuális géphez, rendeljen hozzá egy nyilvános IP-címet a virtuális géphez. Az Azure-géphez egy másik nyilvános IP-címet kell használnia, mint a helyszíni géphez. [További információ](../virtual-network/virtual-network-public-ip-address.md).
2. Ellenőrizze, hogy a hálózati biztonsági csoport (NSG) szabályok a virtuális gép lehetővé teszi a bejövő kapcsolatokat az RDP vagy SSH port.
3. Ellenőrizze [a rendszerindítási diagnosztikát](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) a virtuális gép megtekintéséhez.

> [!NOTE]
> Az Azure Bastion szolgáltatás privát RDP és SSH hozzáférést biztosít az Azure virtuális gépekhez. [További információ](../bastion/bastion-overview.md) erről a szolgáltatásról.

## <a name="next-steps"></a>További lépések

Döntse el, hogy melyik módszert szeretné [a VMware virtuális gépek](server-migrate-overview.md) Azure-ba való áttelepítéséhez használni, vagy kezdje meg a [Hyper-V virtuális gépek](tutorial-migrate-hyper-v.md) vagy a fizikai [kiszolgálók, illetve a virtualizált vagy a felhőalapú virtuális gépek áttelepítését.](tutorial-migrate-physical-virtual-machines.md)
