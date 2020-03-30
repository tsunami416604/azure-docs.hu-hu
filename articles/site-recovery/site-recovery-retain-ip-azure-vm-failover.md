---
title: AZ Azure VM-feladatátvétel után is megőrzi az IP-címeket az Azure Site Recovery szolgáltatással
description: Ez a témakör azt ismerteti, hogy miként őrizze meg az IP-címeket, amikor az Azure Site Recovery szolgáltatással egy másodlagos régióba történő vészhelyreállításhoz az Azure Site Recovery szolgáltatással rendelkező, az Azure Site Rektének átadhatja a vészhelyreállítást.
ms.service: site-recovery
ms.date: 4/9/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 650fb7f0877a98ef53ed3868550f9c084ecb5885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257562"
---
# <a name="retain-ip-addresses-during-failover"></a>IP-címek megőrzése feladatátvétel során

[Az Azure Site Recovery](site-recovery-overview.md) lehetővé teszi az Azure-beli virtuális gépek vészutáni helyreállítást azáltal, hogy a virtuális gépeket egy másik Azure-régióba replikálja, ha kimaradás történik, és nem felel meg az elsődleges régiónak, amikor a dolgok visszatérnek a normális kerékvágásba.

A feladatátvétel során érdemes lehet az IP-címzet a célrégióban azonos a forrásrégióval:

- Alapértelmezés szerint, ha engedélyezi a vészhelyreállítást az Azure virtuális gépek, site recovery létrehoz célerőforrások at forráserőforrás-beállítások alapján. A statikus IP-címekkel konfigurált Azure-beli virtuális gépek esetén a Site Recovery megpróbálja ugyanazt az IP-címet létesíteni a célvirtuális géphez, ha nincs használatban. A Site Recovery által a címzés kezelésének teljes magyarázatáért [tekintse meg ezt a cikket.](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms)
- Egyszerű alkalmazások esetén az alapértelmezett konfiguráció elegendő. Összetettebb alkalmazások esetén előfordulhat, hogy további erőforrást kell kiépítenie annak érdekében, hogy a kapcsolat a feladatátvétel után a várt módon működjön.


Ez a cikk néhány példát tartalmaz az IP-címek összetettebb példaesetekben való megőrzésére. A példák a következők:

- Feladatátvétel olyan vállalatnál, amely az Azure-ban futó összes erőforrással együtt
- Feladatátvétel hibrid telepítéssel rendelkező vállalatnál, valamint a helyszínen és az Azure-ban futó erőforrásoknál

## <a name="resources-in-azure-full-failover"></a>Erőforrások az Azure-ban: teljes feladatátvétel

"A" vállalat nak minden alkalmazása fut az Azure-ban.

### <a name="before-failover"></a>Feladatátvétel előtt

Itt az architektúra a feladatátvétel előtt.

- "A" vállalat azonos hálózatokkal és alhálózatokkal rendelkezik a forrás- és a célAzure-régiókban.
- A helyreállítási idő (RTO) csökkentése érdekében a vállalat az SQL Server Always On, tartományvezérlők stb. Ezek a replikacsomópontok a célrégióban egy másik virtuális hálózatban találhatók, így a forrás és a célrégiók között vpn-helyek közötti kapcsolatot létesíthetnek. Ez nem lehetséges, ha ugyanazt az IP-címterületet használja a forrásban és a célban.  
- A feladatátvétel előtt a hálózati architektúra a következő:
    - Az elsődleges régió az Azure Kelet-Ázsia
        - Kelet-Ázsia rendelkezik egy virtuális hálózattal (**Forrás virtuális hálózat**) 10.1.0.0/16 címterülettel.
        - Kelet-Ázsia számítási feladatok három alhálózat ra van felosztva a virtuális hálózatban:
            - **1. alhálózat:** 10.1.1.0/24
            - **Alhálózat 2:** 10.1.2.0/24
            - **Alhálózat 3:** 10.1.3.0/24
    - Másodlagos (cél) régió az Azure Délkelet-Ázsia
        - Délkelet-Ázsiában van egy helyreállítási virtuális hálózat (**Recovery VNet**) azonos **forrás VNet**.
        - Délkelet-Ázsia rendelkezik egy további virtuális hálózat (**Azure Virtuális hálózat**) címterület 10.2.0.0/16.
        - **Az Azure VNet** tartalmaz egy alhálózatot (**4. alhálózat**) 10.2.4.0/24 címtérrel.
        - Az SQL Server Mindig bekapcsolt replikacsomópontjai, **Subnet 4**a tartományvezérlő stb.
    - **A forrásvirtuális hálózat** és az **Azure virtuális hálózat** vpn-helyek közötti kapcsolattal van összekapcsolva.
    - **A helyreállítási virtuális hálózat** nem csatlakozik más virtuális hálózathoz.
    - **"A" vállalat** hozzárendeli/ellenőrzi a replikált elemek cél IP-címeit. A cél IP ugyanaz, mint a forrás IP minden virtuális gép.

![Erőforrások az Azure-ban a teljes feladatátvétel előtt](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Feladatátvétel után

Ha egy forrás regionális kimaradás történik, az "A" vállalat átveheti az összes erőforrását a célrégióba.

- A feladatátvétel előtt már érvényben lévő cél IP-címekkel az A vállalat koordinálhatja a feladatátvételt, és automatikusan kapcsolatokat hozhat létre **a helyreállítási virtuális hálózat** és az Azure virtuális **hálózat**közötti feladatátvétel után. Ezt az alábbi ábra szemlélteti.
- Az alkalmazás követelményeitől függően a két virtuális hálózat (**helyreállítási virtuális hálózat** és az Azure virtuális **hálózat**) közötti kapcsolatok a célrégióban a feladatátvétel előtt, alatt (köztes lépésként) vagy a feladatátvétel után létesíthetők.
  - A vállalat [helyreállítási tervek](site-recovery-create-recovery-plans.md) segítségével megadhatja, hogy mikor jön létre a kapcsolat.
  - Virtuális hálózatok és a helyek közötti VPN használatával csatlakozhatnak a virtuális hálózatok között.
      - A virtuális hálózatok társviszony-létesítése nem használ VPN-átjárót, és különböző korlátozásokkal rendelkezik.
      - A virtuális hálózat társviszony-létesítési [díjszabása](https://azure.microsoft.com/pricing/details/virtual-network) a VNet-vnet VPN-átjáró [díjszabásátitól](https://azure.microsoft.com/pricing/details/vpn-gateway)eltérően történik. A feladatátvételek esetén általában azt javasoljuk, hogy ugyanazt a kapcsolódási módszert használja, mint a forráshálózatok, beleértve a kapcsolat típusát is, az előre nem látható hálózati incidensek minimalizálása érdekében.

    ![Erőforrások az Azure teljes feladatátvételében](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)



## <a name="resources-in-azure-isolated-app-failover"></a>Erőforrások az Azure-ban: elkülönített alkalmazásfeladat-átvétel

Előfordulhat, hogy az alkalmazás szintjén kell átkerülnie a feladatátvételt. Például egy dedikált alhálózatban található adott alkalmazás vagy alkalmazásszint feladatátvétele.

- Ebben a forgatókönyvben, bár megtarthatja az IP-címzés, általában nem tanácsos, mivel növeli a kapcsolatinkonzisztenciák esélyét. Az ugyanazon Azure-virtuális hálózaton belüli más alhálózatokal való alhálózati kapcsolat is elvesz.
- Az alhálózati szintű alkalmazás feladatátvételének jobb módja, ha különböző cél IP-címeket használ a feladatátvételhez (ha a forrás virtuális hálózatának más alhálózataival való kapcsolatra van szüksége), vagy elkülöníti az egyes alkalmazásokat a saját dedikált virtuális hálózatában a forrásrégióban. Az utóbbi megközelítéssel kapcsolatot létesíthet a forrásrégióban lévő hálózatok között, és ugyanazt a viselkedést emulálhatja, amikor a célrégióba veszi át a feladatátvételt.  

Ebben a példában az A vállalat a forrásrégióban helyezi el az alkalmazásokat a dedikált virtuális hálózatokban, és kapcsolatot hoz létre a virtuális hálózatok között. Ezzel a kialakítással elvégezhetnek elszigetelt alkalmazásfeladat-átvételt, és megtarthatják a forrás magán IP-címeit a célhálózatban.

### <a name="before-failover"></a>Feladatátvétel előtt

A feladatátvétel előtt az architektúra a következő:

- Az alkalmazás virtuális gépei az elsődleges Azure East Asia régióban találhatók:
    - **Alkalmazás1** Virtuális gépek találhatók virtuális hálózat **vnet 1:** 10.1.0.0/16.
    - **Alkalmazás2** Virtuális gépek találhatók virtuális hálózat **virtuális hálózat 2:** 10.2.0.0/16.
    - **A forrás virtuális hálózat1** két alhálózatot rendelkezik.
    - **A forrás virtuális hálózat2** két alhálózatot rendelkezik.
- Másodlagos (cél) régió az Azure Délkelet-Ázsia - Délkelet-Ázsia rendelkezik egy helyreállítási virtuális hálózatok (**Recovery VNet 1** és **Recovery VNet 2**), amelyek megegyeznek a Forrás **VNet 1** és forrás **VNet 2.**
        - **A helyreállítási virtuális hálózat 1** és a helyreállítási virtuális hálózat **2-ben** két alhálózat található, amelyek megfelelnek a Forrás virtuális **hálózat 1** és a Forrás virtuális **hálózat 2** – Délkelet-Ázsia egy további virtuális hálózattal (**Azure VNet**) a 10.3.0.0/16 címterülettel.
        - **Az Azure VNet** tartalmaz egy alhálózatot (**4. alhálózat**) 10.3.4.0/24 címtérrel.
        - Az SQL Server Mindig bekapcsolt replikacsomópontjai, a tartományvezérlő stb. **Subnet 4**
- Számos hely közötti VPN-kapcsolat létezik: 
    - **Forrás virtuális hálózat 1** és **az Azure Virtuális hálózat**
    - **Forrás virtuális hálózat 2** és **az Azure Virtuális hálózat**
    - **A Forrás virtuális hálózat 1** és a Forrás virtuális hálózat **2** a VPN-helyekhez
- **A helyreállítási virtuális hálózat 1** és a helyreállítási virtuális hálózat **2** nem csatlakozik más virtuális hálózatokhoz.
- **Az A vállalat** konfigurálja a VPN-átjárókat a **Helyreállítási virtuális hálózaton 1** és a Helyreállítási virtuális hálózat **2,** az RTO csökkentése érdekében.  
- **A helyreállítási virtuális hálózat1** és **a helyreállítási virtuális hálózat2** nem csatlakozik más virtuális hálózathoz.
- A helyreállítási idő (RTO) csökkentése érdekében a VPN-átjárók a **helyreállítási vnet1** és **a helyreállítási vnet2** feladatátvétel előtt konfigurálva vannak.

    ![Erőforrások az Azure-ban az alkalmazásfeladat-átvétel előtt](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

### <a name="after-failover"></a>Feladatátvétel után

Egy alkalmazásra (a példában található **Source VNet 2-ben) érintő kimaradás vagy probléma esetén az "A" vállalat az alábbiak szerint állíthatja helyre az érintett alkalmazást:


- Vpn-kapcsolatok leválasztása **a Forrás virtuális hálózat1** és **a Forrás virtuálishálózat2,** valamint **a Forrás virtuális hálózat2** és **az Azure Virtuális hálózat** között.
- VPN-kapcsolatok létrehozása **a Forrás virtuális hálózat1** és **a helyreállítási virtuális hálózat2**, valamint **a helyreállítási virtuális hálózat2** és **az Azure Virtuális hálózat**között.
- A virtuális gépek a **forrás virtuális hálózat2 a** helyreállítási **vNet2.**

![Erőforrások az Azure app feladatátvételben](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)


- Ez a példa kibontható további alkalmazásokkal és hálózati kapcsolatokkal. A javaslat az, hogy kövesse a hasonló típusú kapcsolat modell, amennyire csak lehetséges, ha a forrástól a célig.
- A VPN-átjárók nyilvános IP-címeket és átjáró-ugrásokat használnak a kapcsolatok létrehozásához. Ha nem szeretne nyilvános IP-címeket használni, vagy el szeretné kerülni a további ugrásokat, az [Azure vNet-társviszony-létesítésével](../virtual-network/virtual-network-peering-overview.md) a [támogatott Azure-régiók](../virtual-network/virtual-network-manage-peering.md#cross-region)ban lévő virtuális hálózatok társviszonyt.

## <a name="hybrid-resources-full-failover"></a>Hibrid erőforrások: teljes feladatátvétel

Ebben a forgatókönyvben **a B vállalat** egy hibrid vállalkozást futtat, az alkalmazás-infrastruktúra egy része az Azure-on fut, a fennmaradó pedig a helyszínen fut. 

### <a name="before-failover"></a>Feladatátvétel előtt

Így néz ki a hálózati architektúra a feladatátvétel előtt.

- Az alkalmazás virtuális gépei az Azure East Asia-ban vannak üzemeltetve.
- Kelet-Ázsia rendelkezik egy virtuális hálózattal (**Forrás virtuális hálózat**) 10.1.0.0/16 címterülettel.
  - Kelet-Ázsia számítási feladatai három alhálózatra vannak felosztva a **Forrás virtuális hálózatban:**
    - **1. alhálózat:** 10.1.1.0/24
    - **Alhálózat 2:** 10.1.2.0/24
    - **3. alhálózat:** 10.1.3.0/24, egy 10.1.0.0/16 címtérrel rendelkező Azure virtuális hálózat használatával. Ennek a virtuális hálózatnak a neve **Forrás virtuális hálózat**
      - A másodlagos (cél) régió az Azure Délkelet-Ázsia:
  - Délkelet-Ázsiában van egy helyreállítási virtuális hálózat (**Recovery VNet**) azonos **forrás VNet**.
- A kelet-ázsiai virtuális gépek egy helyszíni adatközponthoz kapcsolódnak az Azure ExpressRoute vagy a helyek közötti VPN segítségével.
- Az RTO csökkentése érdekében a B vállalat a feladatátvétel előtt átjárókat ír elő az Azure Délkelet-Ázsiában lévő helyreállítási virtuális hálózaton.
- "B" vállalat hozzárendeli/ellenőrzi a replikált virtuális gépek cél IP-címeit. A cél IP-cím megegyezik az egyes virtuális gépek forrás IP-címével.


![Helyszíni Azure-kapcsolat feladatátvétel előtt](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Feladatátvétel után


Ha egy forrás regionális kimaradás történik, a B vállalat átveheti az összes erőforrását a célrégióba.

- A cél IP-címek már a feladatátvétel előtt, a B vállalat koordinálhatja feladatátvétel és a **helyreállítási virtuális hálózat** és az Azure virtuális hálózat közötti feladatátvétel után automatikusan kapcsolatokat létesíthet. **Azure VNet**
- Az alkalmazás követelményeitől függően a két virtuális hálózat (**helyreállítási virtuális hálózat** és az Azure virtuális **hálózat**) közötti kapcsolatok a célrégióban a feladatátvétel előtt, alatt (köztes lépésként) vagy a feladatátvétel után létesíthetők. A vállalat [helyreállítási tervek](site-recovery-create-recovery-plans.md) segítségével megadhatja, hogy mikor jön létre a kapcsolat.
- Az Azure East Asia és a helyszíni adatközpont közötti eredeti kapcsolatot le kell választani, mielőtt létrejön a kapcsolat az Azure Délkelet-Ázsia és a helyszíni adatközpont között.
- A helyszíni útválasztás át van konfigurálva, hogy a célrégióra mutasson, és az átjárók feladatátvételutána.

![Helyszíni Azure-kapcsolat feladatátvétel után](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

## <a name="hybrid-resources-isolated-app-failover"></a>Hibrid erőforrások: elkülönített alkalmazásfeladat-átvétel

"B" vállalat nem tudja átkerülni az elkülönített alkalmazásokat az alhálózati szinten. Ennek az az oka, hogy a forrás- és helyreállítási virtuális hálózatok címterülete megegyezik, és a helyszíni kapcsolat eredeti forrása aktív.

 - Az alkalmazás rugalmasságát B vállalat kell elhelyezni minden alkalmazást a saját dedikált Azure virtuális hálózat.
 - Ha minden alkalmazás egy külön virtuális hálózatban, a B vállalat átveheti az elszigetelt alkalmazások, és a forráskapcsolatok a célrégióba.

## <a name="next-steps"></a>További lépések

További információ a [helyreállítási tervekről.](site-recovery-create-recovery-plans.md)
