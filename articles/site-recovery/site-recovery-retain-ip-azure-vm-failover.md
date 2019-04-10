---
title: Az Azure Site Recovery az Azure virtuális gép feladatátvételi IP-cím megőrzése |} A Microsoft Docs
description: Ismerteti, hogyan lehet IP-cím megőrzése, ha katasztrófa utáni helyreállítás egy másodlagos régióba az Azure Site Recovery az Azure virtuális gépek feladatátvétele
ms.service: site-recovery
ms.date: 4/9/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 618d60417aa6b582eaef94bf75dcf16c74750f83
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357881"
---
# <a name="retain-ip-addresses-during-failover"></a>IP-cím megőrzése feladatátvétel során

[Az Azure Site Recovery](site-recovery-overview.md) lehetővé teszi vészhelyreállítás az Azure virtuális gépek a virtuális gépek replikálása másik Azure-régióba, kimaradás esetén feladatátvétele és az elsődleges régióba meghiúsul, ha a következők vissza a normál.

A feladatátvétel során előfordulhat, hogy szeretné megőrizni az IP-címzési a célrégióban a forrás régiójára azonos:

- Alapértelmezés szerint a vész-helyreállítási engedélyezése az Azure-beli virtuális gépek esetén a Site Recovery létrehoz célerőforrások adatforrás-erőforrás beállítások alapján. Az Azure virtuális gépek statikus IP-címmel konfigurálva a Site Recovery megpróbálja kiépítése a cél virtuális Gépen, azonos IP-címét, ha nem használja. Hogyan kezeli a Site Recovery címzés, a teljes leírását [ebből a cikkből](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).
- Egyszerű alkalmazások esetében az alapértelmezett konfiguráció is elegendő. Összetettebb alkalmazások esetében szükség lehet annak biztosításához, hogy kapcsolat megfelelően működik-e a feladatátvételt követően további erőforrás kiépítéséhez.


Ez a cikk példákat nyújt összetettebb példaforgatókönyvek az IP-cím megőrzése. A példák a következők:

- Feladatátvételi rendelkező Azure-ban futó összes erőforrás
- Feladatátvétel a vállalat egy hibrid telepítésben, és a helyszínen futó erőforrások és az Azure-ban

## <a name="resources-in-azure-full-failover"></a>Az Azure-erőforrások: teljes feladatátvétel

Az Azure-ban futó összes alkalmazás a vállalat rendelkezik.

### <a name="before-failover"></a>A feladatátvétel előtt

Ez az architektúra a feladatátvétel előtt.

- A vállalat rendelkezik azonos hálózatok és alhálózatok a forrás- és a cél Azure-régióban.
- Helyreállítási időre vonatkozó célkitűzés (RTO) csökkentése érdekében a vállalat replikacsomópontokon használ az SQL Server Always On, tartományvezérlők stb. A replika csomópontok vannak egy másik virtuális hálózat a célrégióban, hogy a forrás- és régiók közötti VPN-helyek közötti kapcsolatot építhetnek ki. Ez nem lehetséges, ha a forrás és cél használja az ugyanazon IP-címtér.  
- A feladatátvétel előtt a hálózati architektúra a következőképpen történik:
    - Elsődleges régió állapota Azure Kelet-Ázsia
        - Kelet-Ázsia rendelkezik egy virtuális hálózathoz (**forrása VNet**) a cím terület 10.1.0.0/16.
        - Kelet-Ázsia számítási feladatok elosztja a három alhálózatot a virtuális hálózat rendelkezik:
            - **1. alhálózata**: 10.1.1.0/24
            - **2. alhálózata**: 10.1.2.0/24,
            - **Alhálózat 3**: 10.1.3.0/24
    - Másodlagos (cél) régióban Azure Délkelet-Ázsia
        - Délkelet-Ázsia rendelkezik egy helyreállítási virtuális hálózat (**helyreállítási virtuális hálózat**) azonos **forrása VNet**.
        - Délkelet-Ázsia rendelkezik egy további virtuális hálózat (**Azure virtuális hálózat**) a cím terület 10.2.0.0/16.
        - **Az Azure virtuális hálózat** tartalmaz egy alhálózat (**alhálózat 4**) a cím terület 10.2.4.0/24.
        - Az SQL Server Always On replikacsomópontokon tartományvezérlő stb. találhatók **alhálózat 4**.
    - **A forrás virtuális hálózat** és **Azure virtuális hálózat** site-to-site VPN-kapcsolattal csatlakoznak.
    - **A helyreállítási virtuális hálózat** nem kapcsolódik semmilyen más virtuális hálózattal.
    - **A vállalati** replikált elemek cél IP-címek kiosztása/ellenőrzi. A cél IP-cím megegyezik a forrás IP-cím az egyes virtuális Gépekhez.

![Az Azure-erőforrások teljes feladatátvétel előtt](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Feladatátvétel után

Forrás regionális kimaradás során, a vállalat átveheti a célrégióban az összes erőforrását.

- A cél IP-címek már a feladatátvétel előtt helyen, a vállalat feladatátadását és közötti feladatátvétel után automatikusan kapcsolatokat hozhat létre **helyreállítási virtuális hálózat** és **Azure virtuális hálózat**. Ezt szemlélteti az alábbi ábrán...
- A két virtuális hálózatok közötti kapcsolatok alkalmazás követelményeitől függően (**helyreállítási virtuális hálózat** és **Azure virtuális hálózat**) a célzott régió lehet létrehozott előtt, közben (mint egy köztes lépés) és a feladatátvétel után.
  - A vállalat [helyreállítási tervek](site-recovery-create-recovery-plans.md) , adja meg, amikor kapcsolatokat kell létrehozni.
  - A site-to-site VPN vagy a virtuális hálózatok közötti társviszony-létesítés virtuális hálózatok közötti kapcsolódhatnak.
      - Virtuális hálózatok közötti társviszony nem használ VPN-átjárót, és más korlátozásokkal rendelkezik.
      - Virtuális hálózatok közötti társviszony-létesítés [díjszabás](https://azure.microsoft.com/pricing/details/virtual-network) kiszámítása máshogy történik, mint a virtuális hálózatok közötti VPN Gateway [díjszabás](https://azure.microsoft.com/pricing/details/vpn-gateway). A folyamatban lévő feladatátvételi teszteket azt általában javasoljuk, hogy ugyanazt a kapcsolódási módszert használandó adatforrás hálózatok, beleértve a kapcsolat típusa események kiszámíthatatlan hálózati minimalizálása érdekében.

    ![Az Azure teljes feladatátvételi erőforrások](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)



## <a name="resources-in-azure-isolated-app-failover"></a>Az Azure-erőforrások: izolált app feladatátvétel

Szüksége lehet az alkalmazás szintjén átadja a feladatokat. Például a feladatátvételt egy adott alkalmazás vagy alkalmazás szinten található dedikált alhálózatán.

- Ebben a forgatókönyvben bár akkor is megőrizheti az IP-címkezelést, nem tanácsos általában mert növeli a kapcsolat inkonzisztenciák esélyét. Alhálózati kapcsolat az azonos Azure virtuális hálózaton belüli többi alhálózathoz is elveszíti.
- Egy nagyobb alhálózat-szintű alkalmazás feladatátvételi módja másik céloldali IP-címek használata a feladatátvételhez (ha szükséges a forrás virtuális alhálózatok), vagy minden alkalmazás saját dedikált virtuális hálózatban a forrásrégióban elkülönítésére. Az utóbbi megközelítés a forrásrégióban hálózatok közötti kapcsolatot, és ugyanazon viselkedésének emulációjához, amikor a rendszer átadja a feladatokat a célrégióban.  

Ebben a példában A vállalati helyek alkalmazásokat a forrásrégióban található dedikált virtuális hálózatokhoz, és ezeket a virtuális hálózatok közötti kapcsolatot létesít. Ezzel a kialakítással azokat is elkülönített alkalmazás feladatátvételt hajt végre, és megőrizni a forrás magánhálózati IP-címek a célhálózat.

### <a name="before-failover"></a>A feladatátvétel előtt

A feladatátvétel előtt az architektúra a következőképpen történik:

- Az elsődleges régióban Azure Kelet-Ázsia üzemeltetett alkalmazás virtuális gépek:
    - **Az App1** virtuális hálózatban található virtuális gépek **forrása VNet 1**: 10.1.0.0/16.
    - **App2** virtuális hálózatban található virtuális gépek **forrása VNet 2**: 10.2.0.0/16.
    - **A forrás virtuális hálózatok közötti 1** két alhálózattal rendelkezik.
    - **A forrás virtuális hálózat 2** két alhálózattal rendelkezik.
- (Cél) másodlagos régió Azure Délkelet-Ázsia – Délkelet-Ázsia van egy helyreállítási virtuális hálózatok (**helyreállítási VNet 1** és **helyreállítási virtuális hálózat 2**), amelyek megegyeznek a **forrása VNet 1** és **Vnet2 forrás**.
        - **Helyreállítási VNet 1** és **helyreállítási virtuális hálózat 2** alhálózatai megfelelő két alhálózattal rendelkező **forrása VNet 1** és **forrása VNet 2** – Délkelet-Ázsia rendelkezik egy További virtuális hálózatok (**Azure virtuális hálózat**) cím terület 10.3.0.0/16 együtt.
        - **Az Azure virtuális hálózat** tartalmaz egy alhálózat (**alhálózat 4**) a cím terület 10.3.4.0/24.
        -Az SQL Server Always On csomópont replika tartományvezérlő stb. találhatók **alhálózat 4**.
- Van egy helyek közötti VPN-kapcsolatok száma: 
    - **A forrás virtuális hálózat 1** és **Azure Vnethez**
    - **A forrás virtuális hálózat 2** és **Azure Vnethez**
    - **A forrás virtuális hálózatok közötti 1** és **forrása VNet 2** kapcsolódnak VPN site-to-site
- **A helyreállítási virtuális hálózat 1** és **helyreállítási virtuális hálózat 2** nem bármely más virtuális hálózatokhoz csatlakoznak.
- **A vállalati** beállítja a VPN-átjárók a **helyreállítási VNet 1** és **helyreállítási virtuális hálózat 2**, RTO csökkentése érdekében.  
- **Helyreállítási VNet1** és **helyreállítási VNet2** nem csatlakoznak a bármely más virtuális hálózattal.
- Csökkentse a helyreállítási időre vonatkozó célkitűzés (RTO), a VPN-átjárók konfigurálása **helyreállítási VNet1** és **helyreállítási VNet2** feladatátvétel előtt.

    ![Alkalmazás a feladatátvétel előtt az Azure-erőforrások](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

### <a name="after-failover"></a>Feladatátvétel után

Egy kimaradás vagy egyetlen alkalmazást érintő probléma esetén (a ** ebben a példában a forrás virtuális hálózat 2), a vállalat módon állíthatja helyre az érintett alkalmazás:


- Válassza le a VPN-kapcsolatok közötti **forrás VNet1** és **forrás VNet2**, és között **forrás VNet2** és **Azure virtuális hálózat** .
- Közötti VPN-kapcsolatok létesítéséhez **forrás VNet1** és **helyreállítási VNet2**, és a köztes **helyreállítási VNet2** és **Azure virtuális hálózat**.
- A virtuális gépek feladatainak átadása **VNet2 forrás** való **helyreállítási VNet2**.

![Az Azure-alkalmazás feladatátvételi erőforrások](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)


- Ebben a példában is bővíthetők ki további alkalmazások és a hálózati kapcsolatok. A javaslat, hogy hasonló jellegű kapcsolat modell, amennyire csak lehetséges, ha a forrásból a célként megadott feladatátvétele.
- VPN-átjárók a nyilvános IP-címek és az átjáró ugrások kapcsolatokat használja. Ha nem szeretné használni a nyilvános IP-címeket vagy el szeretné kerülni az extra ugrások, használhat [Azure virtuális hálózatok közötti társviszony-létesítés](../virtual-network/virtual-network-peering-overview.md) virtuális hálózatok közötti társviszonyt [Azure-régióban támogatott](../virtual-network/virtual-network-manage-peering.md#cross-region).

## <a name="hybrid-resources-full-failover"></a>A hibrid erőforrások: teljes feladatátvétel

Ebben a forgatókönyvben **vállalati B** egy hibrid üzleti fut, az Azure és a helyszínen futó többi futó alkalmazás-infrastruktúra része. 

### <a name="before-failover"></a>A feladatátvétel előtt

Itt látható, hogy a hálózati architektúra néz ki a feladatátvétel előtt.

- Alkalmazás virtuális gépeken üzemelnek Azure Kelet-Ázsia.
- Kelet-Ázsia rendelkezik egy virtuális hálózathoz (**forrása VNet**) a cím terület 10.1.0.0/16.
  - Kelet-Ázsia rendelkezik elosztja a három alhálózatot, a számítási feladatok **forrása VNet**:
    - **1. alhálózata**: 10.1.1.0/24
    - **2. alhálózata**: 10.1.2.0/24,
    - **Alhálózat 3**: az Azure virtuális hálózat, a cím terület 10.1.0.0/16 10.1.3.0/24utilizing. Ez a virtuális hálózat neve **forrás virtuális hálózat**
      - A másodlagos (cél) régióban Azure Délkelet-Ázsia:
  - Délkelet-Ázsia rendelkezik egy helyreállítási virtuális hálózat (**helyreállítási virtuális hálózat**) azonos **forrása VNet**.
- Kelet-Ázsiában lévő virtuális gép csatlakozik egy helyszíni adatközpont az Azure expressroute-on vagy a hely-hely – VPN.
- RTO csökkentése érdekében a B kiosztja az átjárókat, a helyreállítási virtuális hálózat az Azure Délkelet-Ázsia a feladatátvétel előtt.
- Vállalati B rendel/ellenőrzi a cél IP-címek replikált virtuális gépek számára. A cél IP-cím megegyezik a forrás IP-címet minden virtuális géphez.


![A feladatátvétel előtt az – helyszíni – Azure-kapcsolat](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Feladatátvétel után


Forrás regionális kimaradás során, a B átveheti a célrégióban az összes erőforrását.

- A cél IP-címek már a feladatátvétel előtt a helyükön a, B vállalat feladatátadását és közötti feladatátvétel után automatikusan kapcsolatokat hozhat létre **helyreállítási virtuális hálózat** és **Azure virtuális hálózat**.
- A két virtuális hálózatok közötti kapcsolatok alkalmazás követelményeitől függően (**helyreállítási virtuális hálózat** és **Azure virtuális hálózat**) a célzott régió lehet létrehozott előtt, közben (mint egy köztes lépés) és a feladatátvétel után. A vállalat [helyreállítási tervek](site-recovery-create-recovery-plans.md) , adja meg, amikor kapcsolatokat kell létrehozni.
- Az eredeti kapcsolatot Azure Kelet-Ázsia és a helyszíni adatközpont közötti Azure Délkelet-Ázsia és a helyszíni adatközpont közötti kapcsolat kialakítása előtt le kell választva.
- A helyszíni útválasztási átkonfigurálása, hogy a célrégióban mutasson, és az átjárók a feladatátvétel után.

![A feladatátvételt követően a – helyszíni – Azure-kapcsolat](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

## <a name="hybrid-resources-isolated-app-failover"></a>A hibrid erőforrások: izolált app feladatátvétel

Vállalati B nem átadja a feladatokat az alhálózat szintjén elkülönített alkalmazásokat. Ez azért, mert a forrás- és a helyreállítási virtuális hálózat a címtér megegyezik, és ha az eredeti adatforrás, a helyszíni kapcsolat aktív.

 - Alkalmazás rugalmasság vállalati B kell helyezni, minden alkalmazás saját dedikált Azure-beli virtuális.
 - Minden alkalmazás egy külön virtuális hálózatban a B is elkülönített alkalmazások feladatátvételét és adatforrás-kapcsolatok átirányítása a célrégióban.

## <a name="next-steps"></a>További lépések

Ismerje meg [helyreállítási tervek](site-recovery-create-recovery-plans.md).
