---
title: Azure virtuális gép feladatátvételi IP-címek megőrzése |} A Microsoft Docs
description: Ismerteti, hogyan lehet IP-cím megőrzése, ha katasztrófa utáni helyreállítás egy másodlagos régióba az Azure Site Recovery az Azure virtuális gépek feladatátvétele
ms.service: site-recovery
ms.date: 10/16/2018
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 86adaa21a069c168b512231ba231940bfa2ef9e8
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50213032"
---
# <a name="ip-address-retention-for-azure-vm-failover"></a>Azure virtuális gép feladatátvételi IP-cím megőrzési

Az Azure Site Recovery az Azure virtuális gépek lehetővé teszi a vészhelyreállítást. Feladatátvétele egy Azure-régióból a másikba, amikor az ügyfelek gyakran igényelnek az IP-konfigurációk megőrzése. Site Recovery alapértelmezés szerint utánozza forrás virtuális hálózati és alhálózati struktúra ezeket az erőforrásokat a célrégióban létrehozásakor. Az Azure virtuális gépek konfigurált statikus magánhálózati IP-címekkel rendelkező a Site Recovery egy ajánlott erőfeszítés, ha az IP-Címre már nem tiltják le egy Azure-erőforrás vagy a replikált virtuális gép kiépítése a cél virtuális Gépre, azonos magánhálózati IP kísérlet is teszi.

Egyszerű alkalmazások a fenti alapértelmezett konfigurációs minden szükséges. Összetettebb, vállalati alkalmazások ügyfelei, győződjön meg arról, hogy az infrastruktúra más összetevőkkel feladatátvétel utáni kapcsolat további hálózati erőforrások kiépítése szükség lehet. Ez a cikk ismerteti feladatátvétele esetén a hálózati követelmények átadott Azure virtuális gépekhez egy adott régióban található, egy másik virtuális gép IP-cím megőrzése.

## <a name="azure-to-azure-connectivity"></a>Azure-ról Azure kapcsolat

Az első forgatókönyv, hogy érdemes lehet **a vállalat** , amely rendelkezik az összes, az Azure-ban futó alkalmazás infrastruktúra. Üzleti folytonosság és a megfelelőségi okokból **a vállalat** úgy dönt, hogy a rajtuk található alkalmazások védelme az Azure Site Recovery használatával.

Adja meg a követelménynek (például alkalmazás-kötések meghajtóbetűjeleket) IP-megőrzést, a vállalat rendelkezik az azonos virtuális hálózatot és alhálózatot struktúra a célrégióban. A helyreállítási időre vonatkozó célkitűzés (RTO), csökkentheti tovább **a vállalat** replikacsomópontokon az SQL Always ON, tartományvezérlők és az egyéb, és ezek csomópontokon vannak elhelyezve egy másik virtuális hálózat a célrégióban a replika már használja. A replika csomópontok egy másik címtér használata lehetővé teszi **a vállalat** forrás- és régiók közötti VPN-helyek közötti kapcsolat létrehozásához, amelyek egyébként nem lenne lehetséges, ha ugyanaz a címtér használatos mindkét végén .

Itt látható, hogy a hálózati architektúra néz ki a feladatátvétel előtt:
- Alkalmazás virtuális gépek az Azure Kelet-Ázsia, kész üzletifolyamat-cím terület 10.1.0.0/16 Azure virtuális hálózatban üzemelnek. Ez a virtuális hálózat neve **forrása VNet**.
- Alkalmazás számítási feladatainak elosztja a három alhálózatot – 10.1.1.0/24, 10.1.2.0/24, 10.1.3.0/24, illetve nevű **alhálózati 1**, **alhálózati 2**, **alhálózati 3**.
- Az Azure Délkelet-Ázsia a célrégióban és egy helyreállítási virtuális hálózatra, amelyhez a forrás címe címteret és az alhálózati beállításai utánozza. Ez a virtuális hálózat neve **helyreállítási virtuális hálózat**.
- Például az Always On, tartományvezérlő és az egyéb szükséges replikacsomópontokon kerülnek cím terület 10.2.0.0/16 alhálózat 4 belül a cím 10.2.4.0/24 rendelkező virtuális hálózaton. A virtuális hálózat neve **Azure virtuális hálózat** , és az Azure Délkelet-Ázsia.
- **A forrás virtuális hálózat** és **Azure virtuális hálózat** site-to-site VPN-kapcsolat révén csatlakoznak.
- **A helyreállítási virtuális hálózat** nem kapcsolódik semmilyen más virtuális hálózattal.
- **A vállalati** replikált elemek cél IP-címet rendel/ellenőrzi. Ebben a példában a cél IP-cím ugyanaz, mint a forrás IP-cím az egyes virtuális Gépekhez.

![A feladatátvétel előtt az Azure – Azure-kapcsolat](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="full-region-failover"></a>Teljes többrégiós feladatátvétel

Egy regionális kimaradás esetén **a vállalat** helyreállíthatja a teljes telepítési gyorsan és egyszerűen az Azure Site Recovery hatékony [helyreállítási tervek](site-recovery-create-recovery-plans.md). Kellene már be van állítva a célként megadott IP-cím az egyes virtuális Gépekhez a feladatátvétel előtt **a vállalat** is feladatátadását, és a helyreállítási virtuális hálózat és az Azure Vnet közötti kapcsolat létrehozásának automatizálása, ahogyan az alábbi ábrán látható.

![Azure-ról Azure kapcsolat teljes többrégiós feladatátvétel](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)

Alkalmazás követelményeitől függően a két virtuális hálózat a célrégióban a közötti kapcsolatokat lehet létrehozott előtt, közben (mint egy köztes lépés) vagy a feladatátvételt követően. Használat [helyreállítási tervek](site-recovery-create-recovery-plans.md) szkriptek hozzáadása, és adja meg a feladatátvételi sorrendjét.

A kiválasztott helyreállítási virtuális hálózat és az Azure VNet közötti kapcsolatot létesíteni virtuális hálózatok közötti társviszony vagy Site-to-Site VPN használatával a vállalat is rendelkezik. A virtuális hálózatok közötti társviszony nem használ VPN-átjárót, és más korlátozásokkal rendelkezik. Emellett a [virtuális hálózatok közötti társviszony díjszabásának](https://azure.microsoft.com/pricing/details/virtual-network) kiszámítása máshogy történik, mint a [virtuális hálózatok közötti VPN-átjáró](https://azure.microsoft.com/pricing/details/vpn-gateway) esetén. A folyamatban lévő feladatátvételi teszteket tanácsos általában attól a forrás-kapcsolatot, beleértve a kapcsolat típusa, azzal kapcsolatban, hogy hálózati módosítások előre nem látható incidensek minimalizálása érdekében.

### <a name="isolated-application-failover"></a>Elkülönített alkalmazás feladatátvétel

Bizonyos körülmények között felhasználók az alkalmazás-infrastruktúra feladatátvételi részeinek szükség lehet. Ilyen például egy adott alkalmazás vagy a szint, amely egy dedikált alhálózaton belül részeként átkapcsol. Bár lehetséges megőrzési IP-alhálózat feladatátvétel, nem tanácsos a legtöbb esetben mert jelentősen növeli kapcsolat inkonzisztenciát. Alhálózati kapcsolat más ugyanabban az Azure virtuális hálózatban található alhálózatokhoz is elvesznek.

Alhálózat-szintű alkalmazáskövetelmények feladatátvételi fiókot annak, hogy másik céloldali IP-címek használata a feladatátvételhez (ha szükséges a forrás virtuális hálózata más alhálózatokra való kapcsolat), illetve meghatározhatja a szereplő minden alkalmazásnak saját dedikált virtuális a forrás hálózati. Az utóbbi megközelítés a forrás közötti hálózati kapcsolatot, és azonos Emulálhatja, ha átadni a feladatokat a célrégióban.

Egyes alkalmazások rugalmasság tervezhet, javasolt helyet biztosít egy alkalmazást a saját dedikált virtuális hálózatban, és szükség szerint a virtuális hálózatok közötti kapcsolatot. Ez lehetővé teszi elkülönített alkalmazás feladatátvételi eredeti magánhálózati IP-címek megőrzése mellett.

Ezután a feladatátvétel előtti konfiguráció a következőképpen néz ki:
- Alkalmazás virtuális gépek futnak, az Azure Kelet-Ázsia, a cím terület 10.1.0.0/16 az első alkalmazás és a 10.2.0.0/16, a második alkalmazás egy Azure virtuális hálózat használatával. A virtuális hálózatok nevesített **forrás VNet1** és **forrás VNet2** az első és második alkalmazás jelölik.
- Minden egyes virtuális hálózat további két alhálózatra van felosztva.
- Az Azure Délkelet-Ázsia a célrégióban és helyreállítási virtuális hálózatok a helyreállítási VNet1 és a helyreállítási VNet2.
- Például az Always On, tartományvezérlő és az egyéb szükséges replikacsomópontokon kerülnek egy virtuális hálózaton belüli cím terület 10.3.0.0/16 **alhálózat 4** cím 10.3.4.0/24 együtt. A virtuális hálózat Azure virtuális hálózat neve és az Azure Délkelet-Ázsia.
- **Forrás VNet1** és **Azure virtuális hálózat** site-to-site VPN-kapcsolat révén csatlakoznak. Ehhez hasonlóan **forrás VNet2** és **Azure virtuális hálózat** site-to-site VPN-kapcsolat is össze.
- **Forrás VNet1** és **forrás VNet2** ebben a példában S2S VPN-kapcsolaton keresztül is csatlakoznak. Mivel a két virtuális hálózat ugyanabban a régióban, virtuális hálózatok közötti társviszony is használható S2S VPN helyett.
- **Helyreállítási VNet1** és **helyreállítási VNet2** nem csatlakoznak a bármely más virtuális hálózattal.
- Csökkentse a helyreállítási időre vonatkozó célkitűzés (RTO), a VPN-átjárók konfigurálása **helyreállítási VNet1** és **helyreállítási VNet2** feladatátvétel előtt.

![A feladatátvétel előtt elkülönített Azure-ról Azure kapcsolat alkalmazás](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

Ha meghibásodik egy katasztrófa utáni helyzetben, amely csak egy alkalmazás (az ebben a példában a forrás VNet2 részeként) befolyásolja a vállalat módon állíthatja helyre az érintett alkalmazás:
- VPN-kapcsolatok közötti **forrás VNet1** és **forrás VNet2**, és között **forrás VNet2** és **Azure virtuális hálózat** le vannak választva.
- VPN-kapcsolatok között jönnek létre **forrás VNet1** és **helyreállítási VNet2**, és között **helyreállítási VNet2** és **Azure virtuális hálózat**.
- A virtuális gépek **forrás VNet2** való feladatátvétel **helyreállítási VNet2**.

![Alkalmazás elkülönített Azure-ról Azure kapcsolat a feladatátvétel után](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)

A fenti elkülönített feladatátvételi példa bővíthetők ki további alkalmazásokat is, és a hálózati kapcsolat. A javaslat, hogy hasonló jellegű kapcsolat modell, amennyire csak lehetséges, ha a forrásból a célként megadott feladatátvétele.

### <a name="further-considerations"></a>További szempontok

VPN-átjárók használatára, nyilvános IP-címek és az átjáró ugrások kapcsolatokat. Ha nem szeretné, nyilvános IP-címet használja, és/vagy további ugrásai elkerülése érdekében szeretne, használhatja az Azure [virtuális hálózatok közötti társviszony](../virtual-network/virtual-network-peering-overview.md) virtuális hálózatok közötti társviszonyt [Azure-régióban támogatott](../virtual-network/virtual-network-manage-peering.md#cross-region).

## <a name="on-premises-to-azure-connectivity"></a>A – helyszíni – Azure-kapcsolat

A második esetben javasolt **vállalati B** , amely rendelkezik az Azure és a helyszínen futó többi futó alkalmazás infrastruktúra része. Üzleti folytonosság és a megfelelőségi okokból **vállalati B** úgy dönt, hogy az Azure Site Recovery használatával az Azure-ban futó alkalmazások védelmére.

Itt látható, hogy a hálózati architektúra néz ki a feladatátvétel előtt:
- Alkalmazás virtuális gépek az Azure Kelet-Ázsia, kész üzletifolyamat-cím terület 10.1.0.0/16 Azure virtuális hálózatban üzemelnek. Ez a virtuális hálózat neve **forrása VNet**.
- Alkalmazás számítási feladatainak elosztja a három alhálózatot – 10.1.1.0/24, 10.1.2.0/24, 10.1.3.0/24, illetve nevű **alhálózati 1**, **alhálózati 2**, **alhálózati 3**.
- Az Azure Délkelet-Ázsia a célrégióban és egy helyreállítási virtuális hálózatra, amelyhez a forrás címe címteret és az alhálózati beállításai utánozza. Ez a virtuális hálózat neve **helyreállítási virtuális hálózat**.
- Virtuális gépek Azure Kelet-Ázsia helyszíni adatközpont ExpressRoute- vagy Site-to-Site VPN keresztül csatlakoznak.
- Helyreállítási időre vonatkozó célkitűzés (RTO) csökkentése érdekében a B kiosztja az átjárókat, a helyreállítási virtuális hálózat az Azure Délkelet-Ázsia a feladatátvétel előtt.
- **Vállalati B** replikált elemek cél IP-címet rendel/ellenőrzi. Ebben a példában a cél IP-címet pedig ugyanaz, mint a forrás IP-cím az egyes virtuális Gépekhez

![A feladatátvétel előtt az – helyszíni – Azure-kapcsolat](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="full-region-failover"></a>Teljes többrégiós feladatátvétel

Egy regionális kimaradás esetén **vállalati B** helyreállíthatja a teljes telepítési gyorsan és egyszerűen az Azure Site Recovery hatékony [helyreállítási tervek](site-recovery-create-recovery-plans.md). Kellene már be van állítva a célként megadott IP-cím az egyes virtuális Gépekhez a feladatátvétel előtt **vállalati B** is feladatátadását, és a helyreállítási virtuális hálózat és a helyszíni adatközpont közötti kapcsolat létrehozásának automatizálása, ahogyan az alábbi ábrán látható.

Az eredeti kapcsolatot Azure Kelet-Ázsia és a helyszíni adatközpont közötti Azure Délkelet-Ázsia és a helyszíni adatközpont közötti kapcsolat kialakítása előtt le kell választva. A helyszíni útválasztási is átkonfigurálása, hogy a célrégióban mutasson, és az átjárók a feladatátvétel után.

![A feladatátvételt követően a – helyszíni – Azure-kapcsolat](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

### <a name="subnet-failover"></a>Alhálózat feladatátvétel

Ellentétben az Azure – Azure forgatókönyvhöz ismertetett **a vállalat**, egy alhálózat-szintű feladatátvételi ez nem lehetséges ebben az esetben a **vállalati B**. Ennek az oka, hogy a forrás- és a helyreállítási virtuális hálózat a címtér megegyezik, és az eredeti adatforrás, a helyszíni kapcsolat aktív.

Rugalmas alkalmazások elérése érdekében javasoljuk, hogy mindegyik alkalmazás saját dedikált Azure virtuális hálózatban részeként. Alkalmazások majd feladatátvétele elkülönítve, és a szükséges a helyszíni adatforrás-kapcsolatok átirányíthatók a célrégióban a fent leírtak szerint.

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [helyreállítási tervek](site-recovery-create-recovery-plans.md).
