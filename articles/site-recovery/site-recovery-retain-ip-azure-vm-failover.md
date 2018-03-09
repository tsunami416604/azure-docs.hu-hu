---
title: "Egy másik Azure-régió, az Azure virtuális gépek feladatátvétele IP-címek megtartására |} Microsoft Docs"
description: "Ismerteti, hogyan szeretné megőrizni az IP-címek az Azure Site Recovery Azure az Azure-bA feladatátvételi forgatókönyvek"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: 15f87ba87d90cee765f52d3188796bc1ff7b8a35
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="ip-address-retention-for-azure-virtual-machine-failover"></a>Azure virtuális gép feladatátvételi IP-cím megőrzési

Az Azure Site Recovery lehetővé teszi, hogy vészhelyreállítás Azure virtuális gépekhez. Egy Azure-régióban való feladatátadással egy másik, az ügyfelek gyakran az az IP-konfigurációjának megőrzési beállítani. Hely helyreállítása után alapértelmezés szerint utánozza forrás virtuális hálózati és alhálózati struktúra történő létrehozásakor a cél régió ezeket az erőforrásokat. Azure virtuális gépekhez konfigurált statikus magánhálózati IP-címekkel rendelkező a Site Recovery is teszi a lehető legkedvezőbb megpróbálja kiépíteni az azonos magánhálózati IP-címe a cél virtuális gép, ha az adott IP nem már blokkolja egy Azure-erőforrás vagy a replikált virtuális gépek.

Egyszerű alkalmazások a fenti alapértelmezett konfigurálás az, hogy. Összetettebb vállalati alkalmazásokat az ügyfelek kell annak biztosítása érdekében a feladatátvételt követően csatlakozni az infrastruktúra egyéb összetevői a további hálózati erőforrások biztosításához. Ez a cikk ismerteti a hibás hálózati követelményei átadott Azure virtuális gépekhez egy régióban másik megtartja a virtuális gép IP-címeket.

## <a name="azure-to-azure-connectivity"></a>Azure-Azure-kapcsolat

Az első esetben az adatraktárakban **a vállalat** , amely rendelkezik az összes, az Azure-ban futó alkalmazás infrastruktúra. Üzleti folytonosság és megfelelőségi okokból **a vállalat** úgy dönt, hogy az Azure Site Recovery segítségével az alkalmazások védelmét.

A követelmény IP adatmegőrzési (ilyen alkalmazás kötések mint) megadott, a vállalat rendelkezik az azonos virtuális hálózati és alhálózati struktúra cél régió. További csökkentése érdekében a helyreállítási idő célkitűzése (RTO) **a vállalat** replikacsomópontokon SQL Always-ON, a tartományvezérlők, a stb és a replika csomópontok kerülnek, a cél régió, egy másik virtuális hálózatot használja. Lehetővé teszi egy másik címtartományt használja a replikacsomópontokon **a vállalat** forrása és célja régiók közötti VPN-webhelyek kapcsolatot létesíteni, amelyek egyébként nem lenne lehetséges, ha ugyanaz a címtér használatos mindkét végén .

Ez a hálózati architektúra néz feladatátvétel előtt:
- Alkalmazás virtuális gépeket üzemeltető az Azure Kelet-Ázsia, a cím terület 10.1.0.0/16 Azure virtuális hálózat használatával. Ez a virtuális hálózat neve **forrás VNet**.
- Alkalmazások és szolgáltatások el vannak osztva három alhálózatokban – 10.1.0.0/24, 10.1.1.0/24, 10.1.2.0/24, illetve nevű **alhálózati 1**, **alhálózati 2**, **alhálózati 3**.
- Azure Délkelet-Ázsia a cél régió, és egy helyreállítási virtuális hálózathoz, amely a forrás és az alhálózati beállításában utánozza rendelkezik. Ez a virtuális hálózat neve **helyreállítási VNet**.
- Például a mindig bekapcsolva, tartományvezérlő, stb. szükséges replikacsomópontokon cím terület 20.1.0.0/16 alhálózati 4 belüli cím 20.1.0.0/24 a virtuális hálózat kerülnek. A virtuális hálózat neve **Azure VNet** , és az Azure Délkelet-Ázsia.
- **A forrás virtuális hálózat** és **Azure VNet** VPN hely-hely kapcsolatot keresztül csatlakoznak.
- **A helyreállítási virtuális hálózat** nem csatlakozik más virtuális hálózathoz.
- **A vállalati** replikált elemek cél IP-címet rendel/ellenőrzi. Ebben a példában a cél IP-címet megegyezik a forrás IP-cím, az egyes virtuális gépek.

![Feladatátvétel előtt Azure-Azure kapcsolat](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover.png)

### <a name="full-region-failover"></a>Teljes terület feladatátvétel

A regionális kimaradás esetén **a vállalat** helyre tudja állítani a teljes telepítés gyorsan és egyszerűen Azure Site Recovery hatékony [helyreállítási tervek](site-recovery-create-recovery-plans.md). Ha már be van állítva a cél IP-címet az egyes virtuális gépek a feladatátvétel előtt **a vállalat** kezelheti feladatátvételi és helyreállítási virtuális hálózat és az Azure Vnet közötti kapcsolat felépítése automatizálhatja, ahogy az az alábbi ábra.

![Teljes terület feladatátvételi Azure-Azure kapcsolat](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover.png)

Attól függően, hogy az alkalmazás követelményeinek, a két Vnetek cél régió közötti kapcsolatok lehet meghatározott előtt, közben (mint egy köztes lépés) vagy a feladatátvétel után. Használjon [helyreállítási tervek](site-recovery-create-recovery-plans.md) parancsfájlok hozzáadása, és adja meg a feladatátvételi sorrendjét.

A választott Vnetben társviszony-létesítés vagy telephelyek közötti VPN használatával helyreállítási VNet és Azure VNet közötti kapcsolatot létesíteni a vállalat is rendelkezik. A virtuális hálózatok közötti társviszony nem használ VPN-átjárót, és más korlátozásokkal rendelkezik. Emellett a [virtuális hálózatok közötti társviszony díjszabásának](https://azure.microsoft.com/pricing/details/virtual-network) kiszámítása máshogy történik, mint a [virtuális hálózatok közötti VPN-átjáró](https://azure.microsoft.com/pricing/details/vpn-gateway) esetén. A feladatátvétel tanácsos általában utánozzák adatforrás kapcsolatát, beleértve a kapcsolat típusát, azzal kapcsolatban, hogy a hálózati módosítások előre nem látható incidensek minimalizálása érdekében.

### <a name="isolated-application-failover"></a>Elkülönített alkalmazás feladatátvételi

Bizonyos körülmények között felhasználók módosítania kell az alkalmazás-infrastruktúra feladatátvételi részeit. Ilyen például egy adott alkalmazás vagy a réteghez, amelynek van egy dedikált alhálózaton belül fájlban található átkapcsol. Míg egy alhálózat feladatátvételi IP-megőrzést lehetséges, tanácsos nem az esetek többségében, mivel jelentősen növeli a kapcsolat inkonzisztenciát. Alhálózati kapcsolat más alhálózatokra belül az azonos Azure virtuális hálózat is elvesznek.

A jobb fiókot alhálózati szintű application feladatátvétel követelményei a módja másik cél IP-címek a feladatátvételre használni (Ha a kapcsolatra szükség a forrás virtuális hálózaton lévő más alhálózatokra), vagy minden alkalmazás saját dedikált virtuális elkülönítése a forrás hálózati. Az utóbbi módszert használja a forrás közötti hálózati kapcsolatot létrehozni, és emulálni azonos, ha a cél régióban feladatátvétele.

A tervezővel egyes alkalmazások a rugalmasságot, javasoljuk a saját dedikált virtuális hálózatban egy alkalmazás tartalmazhat, és szükség szerint a virtuális hálózatok közötti kapcsolatot létrehozni. Ez lehetővé teszi elkülönített alkalmazás feladatátvételi megtartja az eredeti magánhálózati IP-címét.

Ezután a előtti-feladatátvételi konfiguráció a következőképpen néz ki:
- Alkalmazás virtuális gépeket üzemeltető az Azure Kelet-Ázsia, az első alkalmazás címe terület 10.1.0.0/16 és 15.1.0.0/16 a második alkalmazáshoz egy Azure virtuális hálózat használatával. A virtuális hálózatok nevesített **forrás VNet1** és **forrás VNet2** az első és második alkalmazáshoz, illetve.
- Minden egyes virtuális hálózat további felosztása két alhálózat.
- Az Azure Délkelet-Ázsia a cél régió és helyreállítási virtuális hálózatok a helyreállítási VNet1 és helyreállítási VNet2.
- Például a mindig bekapcsolva, tartományvezérlő, stb. szükséges replikacsomópontokon kerülnek cím terület 20.1.0.0/16 belüli virtuális hálózat **alhálózati 4** a cím 20.1.0.0/24. A virtuális hálózati Azure VNet neve és az Azure Délkelet-Ázsia.
- **Forrás-VNet1** és **Azure VNet** VPN hely-hely kapcsolatot keresztül csatlakoznak. Hasonlóképpen **forrás VNet2** és **Azure VNet** keresztül VPN hely-hely kapcsolatot is csatlakoznak.
- **Forrás-VNet1** és **forrás VNet2** ebben a példában S2S VPN-en keresztül is csatlakoznak. Mivel a két Vnetek ugyanabban a régióban, Vnetben társviszony-létesítést is használható S2S VPN-je helyett.
- **Helyreállítási VNet1** és **helyreállítási VNet2** nem csatlakoznak bármely más virtuális hálózattal.
- Csökkentse a helyreállítási idő célkitűzése (RTO), a VPN-átjárók konfigurálása a **helyreállítási VNet1** és **helyreállítási VNet2** feladatátvétel után már.

![Elkülönített Azure-Azure kapcsolat alkalmazás feladatátvétel előtt](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover.png)

Esetén a katasztrófa helyzet, amely befolyásolja a csak egy alkalmazás (az ebben a példában az adatforrás VNet2 fájlban található) a vállalat állíthatja helyre az érintett alkalmazás az alábbiak szerint:
- VPN-kapcsolatok között **forrás VNet1** és **forrás VNet2**, és közötti **forrás VNet2** és **Azure VNet** megszakad.
- VPN-kapcsolatot létesít közötti **forrás VNet1** és **helyreállítási VNet2**, és közötti **helyreállítási VNet2** és **Azure VNet**.
- A virtuális gépek **forrás VNet2** keresztül nem sikerült **helyreállítási VNet2**.

![Elkülönített Azure-Azure kapcsolat alkalmazás feladatátvételt követően](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover.png)

A fenti elkülönített feladatátvételi példa bővíthető további alkalmazásokat is, és a hálózati kapcsolat. Az ajánljuk, hogy a hasonló jellegű kapcsolat modell, amennyire csak lehetséges, kövesse az feladatátadás a cél-bejegyzéseket.

### <a name="further-considerations"></a>További szempontok

VPN-átjárók használata a nyilvános IP-címek és az átjáró ugrások kapcsolatok létrehozásához. Ha nem szeretné, hogy a nyilvános IP-címet használ, és/vagy el szeretné kerülni az extra ugrások, használhatja globális Vnetben társviszony-létesítés virtuális hálózatok egyenrangú Azure-régiók között.

Ez a funkció jelenleg nyilvános előzetes verziójában, és további támogatási régiók bővíteni – közvetlen VM-VM kapcsolat nélkül bármely nyilvános internet beavatkozás vagy bármely extra ugrások engedélyezése.

További információkért tekintse meg a [társviszony-létesítési dokumentáció](../virtual-network/tutorial-connect-virtual-networks-portal.md#register) és [árképzési](https://azure.microsoft.com/pricing/details/virtual-network/).

## <a name="on-premises-to-azure-connectivity"></a>A helyszíni-az-Azure-kapcsolat

A második esetben az adatraktárakban **B vállalat** , amely rendelkezik az alkalmazás-infrastruktúra fut az Azure és a helyszínen futtatott hátralévő részét. Üzleti folytonosság és megfelelőségi okokból **B vállalat** úgy dönt, hogy az Azure Site Recovery segítségével az Azure-ban futó alkalmazások védelmét.

Ez a hálózati architektúra néz feladatátvétel előtt:
- Alkalmazás virtuális gépeket üzemeltető az Azure Kelet-Ázsia, a cím terület 10.1.0.0/16 Azure virtuális hálózat használatával. Ez a virtuális hálózat neve **forrás VNet**.
- Alkalmazások és szolgáltatások el vannak osztva három alhálózatokban – 10.1.0.0/24, 10.1.1.0/24, 10.1.2.0/24, illetve nevű **alhálózati 1**, **alhálózati 2**, **alhálózati 3**.
- Azure Délkelet-Ázsia a cél régió, és egy helyreállítási virtuális hálózathoz, amely a forrás és az alhálózati beállításában utánozza rendelkezik. Ez a virtuális hálózat neve **helyreállítási VNet**.
- Virtuális gépek Azure Kelet-Ázsia ExpressRoute vagy telephelyek közötti VPN használatával a helyszíni adatközpontjához csatlakoznak.
- Helyreállítási idő célkitűzése (RTO) csökkentése érdekében a B átjárókat a helyreállítási virtuális hálózatot az Azure Délkelet-Ázsiában, feladatátvétel után már látja el.
- **B vállalat** replikált elemek cél IP-címet rendel/ellenőrzi. Ebben a példában a cél IP-cím ugyanaz, mint a forrás IP-cím, az egyes virtuális gépek

![A helyszíni-az-Azure-kapcsolat feladatátvétel előtt](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover.png)

### <a name="full-region-failover"></a>Teljes terület feladatátvétel

A regionális kimaradás esetén **B vállalat** helyre tudja állítani a teljes telepítés gyorsan és egyszerűen Azure Site Recovery hatékony [helyreállítási tervek](site-recovery-create-recovery-plans.md). Ha már be van állítva a cél IP-címet az egyes virtuális gépek a feladatátvétel előtt **B vállalat** kezelheti feladatátvételi és automatizálhatja a kapcsolat felépítése helyreállítási virtuális hálózat és a helyszíni adatközpont között, ahogy az az alábbi ábra.

Az eredeti kapcsolatot az Azure Kelet-Ázsia és a helyszíni adatközpontját a Azure Délkelet-Ázsia és a helyszíni adatközpontban közötti kapcsolat létrehozása előtt kell leválasztja. A helyi útválasztási is konfigurálni, hogy a cél régió mutasson, és átjárók feladatátvétel utáni.

![A feladatátvételt követően a helyi-az-Azure-kapcsolat](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover.png)

### <a name="subnet-failover"></a>Alhálózati feladatátvétel

Az Azure-Azure forgatókönyv ismertetett eltérően **a vállalat**, alhálózati szintű feladatátvevő nincs lehetőség ebben az esetben a **B vállalat**. Ennek az oka, hogy a forrás- és a helyreállítási virtuális hálózatok címterület azonos, és az eredeti forrás kapcsolatra a helyszíni active.

Eléréséhez, az alkalmazás rugalmas, javasoljuk, hogy minden alkalmazáshoz fájlban található-e a saját dedikált Azure virtuális hálózatban. Alkalmazások majd feladatátvételre elkülönítve, és a szükséges a helyszíni adatforrás-kapcsolatok a cél régió átirányítható, fent leírt módon.

## <a name="next-steps"></a>További lépések
- További információ [helyreállítási tervek](site-recovery-create-recovery-plans.md).
