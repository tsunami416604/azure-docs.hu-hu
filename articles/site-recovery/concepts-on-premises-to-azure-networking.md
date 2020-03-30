---
title: Csatlakozás helyszíni Azure-beli virtuális gépekhez az Azure Site Recovery szolgáltatással
description: A cikk ismerteti, hogyan csatlakozhat az Azure virtuális gépekhez a helyszíni azure-beli azure-beli feladatátvétel után az Azure-ba az Azure Site Recovery használatával
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: f222cdd315b79503b1bdea032f495c71df4682b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281989"
---
# <a name="connect-to-azure-vms-after-failover-from-on-premises"></a>Csatlakozás azure-beli virtuális gépekhez a helyszíni feladatátvétel után 


Ez a cikk ismerteti, hogyan állíthat be kapcsolatot, hogy sikeresen csatlakozhat az Azure virtuális gépek feladatátvétel után.

Amikor beállítja a helyszíni virtuális gépek (VM-ek) és a fizikai kiszolgálók vészutáni helyreállítását az Azure-ba, az [Azure Site Recovery](site-recovery-overview.md) elindítja a gépek replikálását az Azure-ba. Ezt követően, ha kimaradások fordulnak elő, a helyszíni helyről átveheti a feladatát az Azure-ba. Feladatátvétel esetén a Site Recovery létrehozza az Azure virtuális gépeket a replikált helyszíni adatok használatával. A vész-helyreállítási tervezés részeként ki kell találnia, hogyan csatlakozhat az azure-beli virtuális gépeken a feladatátvétel után futó alkalmazásokhoz.

Ebben a cikkben az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A feladatátvétel előtt készítse elő a helyszíni gépeket.
> * Készítse elő az Azure virtuális gépekfeladat-átvétel után. 
> * IP-címek megőrzése az Azure-beli virtuális gépeken feladatátvétel után.
> * Új IP-címek hozzárendelése az Azure virtuális gépekhez feladatátvétel után.

## <a name="prepare-on-premises-machines"></a>Helyszíni gépek előkészítése

Az Azure virtuális gépekhez való csatlakozás biztosítása érdekében készítse elő a helyszíni gépekfeladat-átvétel előtt.

### <a name="prepare-windows-machines"></a>Windows-gépek előkészítése

Helyszíni Windows-gépeken tegye a következőket:

1. A Windows beállításainak konfigurálása. Ezek közé tartozik a statikus állandó útvonalak vagy a WinHTTP-proxy eltávolítása, valamint a lemez SAN-házirendjének **onlineallra**állítása. [Kövesse](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure) ezeket az utasításokat.

2. Ellenőrizze, hogy [futnak-e ezek a szolgáltatások.](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services)

3. Engedélyezze a távoli asztal (RDP) engedélyezését a helyszíni számítógép távoli kapcsolatainak engedélyezéséhez. [Ismerje meg, hogyan](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings) engedélyezheti az RDP-t a PowerShell használatával.

4. Ha feladatátvétel után az interneten keresztül szeretne hozzáférni egy Azure-géphez, a helyszíni gépen lévő Windows tűzfalban engedélyezze a TCP-t és az UDP-t a nyilvános profilban, és állítsa be az RDP-t az összes profil engedélyezett alkalmazásaként.

5. Ha egy Azure-beli virtuális gépet szeretne elérni egy helyek közötti VPN-en a feladatátvétel után, a helyszíni gépen lévő Windows tűzfalban engedélyezze az RDP-t a tartományi és a magánprofilokhoz. [További információ](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules) az RDP-forgalom engedélyezéséről.
6. Győződjön meg arról, hogy nincsenek függőben lévő Windows-frissítések a helyszíni virtuális gép, amikor elindítja a feladatátvételt. Ha vannak, a frissítések előfordulhat, hogy a feladatátvétel után megkezdi a telepítést az Azure virtuális gépre, és nem tud bejelentkezni a virtuális gépbe, amíg a frissítések be nem fejeződnek.

### <a name="prepare-linux-machines"></a>Linux-gépek előkészítése

Helyszíni Linux-gépeken tegye a következőket:

1. Ellenőrizze, hogy a Secure Shell szolgáltatás automatikusan elindul-e a rendszer indításakor.
2. Ellenőrizze, hogy a tűzfalszabályok engedélyezik-e az SSH-kapcsolatot.


## <a name="configure-azure-vms-after-failover"></a>Azure-beli virtuális gépek konfigurálása feladatátvétel után

Feladatátvétel után tegye a következőket az Azure-beli virtuális gépek, amelyek létre.

1. Ha az interneten keresztül szeretne csatlakozni a virtuális géphez, rendeljen hozzá egy nyilvános IP-címet a virtuális géphez. Nem használhatja ugyanazt a nyilvános IP-címet az Azure virtuális géphez, amelyet a helyszíni géphez használt. [További információ](../virtual-network/virtual-network-public-ip-address.md)
2. Ellenőrizze, hogy a hálózati biztonsági csoport (NSG) szabályok a virtuális gép lehetővé teszi a bejövő kapcsolatokat az RDP vagy SSH port.
3. Ellenőrizze [a rendszerindítási diagnosztikát](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) a virtuális gép megtekintéséhez.


> [!NOTE]
> Az Azure Bastion szolgáltatás privát RDP és SSH hozzáférést biztosít az Azure virtuális gépekhez. [További információ](../bastion/bastion-overview.md) erről a szolgáltatásról.

## <a name="set-a-public-ip-address"></a>Nyilvános IP-cím beállítása

A nyilvános IP-cím manuális hozzárendelésének alternatívájaként egy Azure virtuális géphez, a címet a feladatátvétel során egy parancsfájl vagy az Azure automation runbook használatával a Site Recovery [helyreállítási terv,](site-recovery-create-recovery-plans.md)vagy beállíthatja a DNS-szintű útválasztás az Azure Traffic Manager használatával. [További információ](concepts-public-ip-address-with-site-recovery.md) a nyilvános cím beállításáról.


## <a name="assign-an-internal-address"></a>Belső cím hozzárendelése

Az Azure virtuális gép belső IP-címének beállítása feladatátvétel után, van néhány lehetőség:

- **Ugyanazt az IP-címet megőrizheti: Használhatja**ugyanazt az IP-címet az Azure virtuális gépen, mint a helyszíni gép hez rendelt.
- **Használjon másik IP-címet:** Az Azure virtuális géphez használhat másik IP-címet.


## <a name="retain-ip-addresses"></a>IP-címek megőrzése

A Site Recovery lehetővé teszi, hogy ugyanazokat az IP-címeket őrizze meg, amikor az Azure-ba való áttöltést. Ugyanannak az IP-címnek a megtartása elkerüli a feladatátvétel t követő esetleges hálózati problémákat, de némi összetettséget vezet be.

- Ha a cél-Azure virtuális gép ugyanazt az IP-címet/alhálózatot használja, mint a helyszíni hely, nem tud csatlakozni közöttük egy hely közötti VPN-kapcsolat vagy ExpressRoute, mert a cím átfedés. Az alhálózatoknak egyedinek kell lenniük.
- A feladatátvétel után a helyszíni azure-ból az Azure-ba kell létesíteni, hogy az alkalmazások elérhetők-e az Azure virtuális gépeken. Az Azure nem támogatja a feszített VLAN-okat, ezért ha meg szeretné őrizni az IP-címeket, az IP-területet a helyszíni gépen kívül a teljes alhálózat átadásával kell átvinnie az Azure-ba.
- Az alhálózati feladatátvétel biztosítja, hogy egy adott alhálózat nem érhető el egyszerre a helyszínen és az Azure-ban.

Az IP-címek megőrzéséhez a következő lépésekre van szükség:

- A replikált elem & a hálózat tulajdonságaiban állítsa be a hálózati és IP-címzést a cél Azure virtuális géphez a helyszíni beállítás tükrözéséhez.
- Az alhálózatokat a vész-helyreállítási folyamat részeként kell kezelni. Szüksége van egy Azure virtuális hálózat, hogy megfeleljen a helyszíni hálózat, és miután feladatátvevő hálózati útvonalakat módosítani kell, hogy tükrözze, hogy az alhálózat költözött az Azure-ba, és az új IP-cím helyeken.  

### <a name="failover-example"></a>Például feladatátvételi példa

Lássunk erre egy példát.

- A fiktív Woodgrove Bank a helyszíni üzleti alkalmazásait üzemelteti.
- A helyszíni azure-ból a helyek közötti VPN-en keresztül csatlakoznak. 
- A Woodgrove a Site Recovery segítségével replikálja a helyszíni gépeket az Azure-ba.
- A helyszíni alkalmazásaik kódolható IP-címeket használnak, ezért ugyanazokat az IP-címeket szeretnék megtartani az Azure-ban.
- A helyszíni, az alkalmazásokat futtató gépek három alhálózatban futnak:
    - 192.168.1.0/24.
    - 192.168.2.0/24
    - 192.168.3.0/24
- Az Azure-ban futó alkalmazásaik az Azure VNet **Azure Networkben** találhatók két alhálózatban:
- 172.16.1.0/24
- 172.16.2.0/24.

Annak érdekében, hogy megőrizzék a címeket, itt van, mit csinálnak.

1. Amikor engedélyezik a replikációt, azt határozzák meg, hogy a gépeknek replikálniuk kell az **Azure Networkre.**
2. Ők hoznak létre **helyreállítási hálózatot** az Azure-ban. Ez a virtuális hálózat a helyszíni hálózat192.168.1.0/24 alhálózatát tükrözi.
3. Woodgrove virtuális [hálózat-virtuális hálózat kapcsolatot](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) állít be a két hálózat között. 

    > [!NOTE]
    > Az alkalmazáskövetelményeitől függően a virtuális hálózat és a virtuális hálózat közötti kapcsolat a feladatátvétel előtt, a Site Recovery [helyreállítási tervben](site-recovery-create-recovery-plans.md)manuális lépés/parancsfájlalapú lépés/Azure-automatizálási runbookként, vagy a feladatátvétel befejezése után állítható be.

4. A feladatátvétel előtt a számítógép tulajdonságai a Site Recovery, beadják a cél IP-címet a cím a helyszíni gép, a következő eljárásban leírtak szerint.
5. Feladatátvétel után az Azure virtuális gépek jönnek létre ugyanazzal az IP-címmel. Woodgrove csatlakozik az **Azure Network** **helyreállítási hálózati** virtuális hálózat virtuális hálózat segítségével virtuális társviszony-létesítés (a tranzit kapcsolat engedélyezve van).
6. A helyszíni Woodgrove-nak hálózati módosításokat kell végrehajtania, beleértve az útvonalak módosítását, hogy tükrözze, hogy a 192.168.1.0/24 az Azure-ba költözött.  

**Feladatátvétel előtti infrastruktúra**

![Alhálózati feladatátvétel előtt](./media/site-recovery-network-design/network-design7.png)


**Infrastruktúra feladatátvétel után**

![Alhálózati feladatátvétel után](./media/site-recovery-network-design/network-design9.png)


### <a name="set-target-network-settings"></a>Célhálózati beállítások megadása

Feladatátvétel előtt adja meg a hálózati beállításokat és az IP-címet a cél Azure virtuális gép.

1.  A Recovery Services-tárolóban -> **Replikált elemek,** válassza ki a helyszíni gép.
2. A **számítógép számítási és hálózati** lapján kattintson a **Szerkesztés**gombra a cél Azure virtuális gép hálózati és adapterbeállításainak konfigurálásához.
3. A **Hálózati tulajdonságok ban**válassza ki a célhálózatot, amelyben az Azure virtuális gép lesz található, amikor jön létre a feladatátvétel után.
4. A **hálózati adapterek**területen konfigurálja a hálózati adaptereket a célhálózatban. Alapértelmezés szerint a Site Recovery a helyszíni gépen lévő összes észlelt hálózati adaptert megjeleníti.
    - A **Cél hálózati adapter típusa** beállíthatja az egyes hálózati adapterek **elsődleges**, **másodlagos**, vagy **ne hozzon létre,** ha nincs szükség, hogy az adott hálózati adapter a célhálózaton. Egy hálózati adaptert kell elsődlegesként beállítani a feladatátvételhez. Vegye figyelembe, hogy a célhálózat módosítása hatással van az Azure virtuális gép összes hálózati adapterére.
    - Kattintson a hálózati adapter nevére, adja meg azt az alhálózatot, amelyben az Azure virtuális gép üzembe kerül.
    - Dinamikus felülírja a **dinamikus** a privát IP-címet szeretne rendelni a cél Azure virtuális gép. Ha nincs megadva IP-cím, a Site Recovery hozzárendeli az alhálózat következő elérhető IP-címét a feladatátvételkori hálózati adapterhez.
    - [További információ](site-recovery-manage-network-interfaces-on-premises-to-azure.md) a helyszíni feladatátvétel hálózati adaptereinek azure-ba való kezeléséről.


## <a name="get-new-ip-addresses"></a>Új IP-címek begyűjtése

Ebben a forgatókönyvben az Azure virtuális gép egy új IP-címet kap a feladatátvétel után. Dns-frissítés a feladatátvételi rendszer ű gépek rekordjainak frissítéséhez, amelyek az Azure virtuális gép IP-címére mutatnak.



## <a name="next-steps"></a>További lépések
[Ismerje meg](site-recovery-active-directory.md) a helyszíni Active Directory és a DNS Azure-ba történő replikálását.


