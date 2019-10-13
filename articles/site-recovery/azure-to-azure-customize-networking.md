---
title: Feladatátvevő virtuális gép hálózati konfigurációinak testreszabása | Microsoft Docs
description: Áttekintést nyújt a feladatátvételi virtuális gép hálózati konfigurációinak testreszabásáról az Azure-beli virtuális gépek Azure Site Recovery használatával történő replikálásával.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/07/2019
ms.author: rajanaki
ms.openlocfilehash: 8038f7c909cfeaf15039afa7335dd6b0460a2622
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72293462"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>A cél Azure-beli virtuális gép hálózati konfigurációjának testreszabása

Ez a cikk útmutatást nyújt a hálózati konfigurációk testreszabásához a cél Azure-beli virtuális gépen (VM), ha az Azure-beli virtuális gépeket az egyik régióból a másikba, [Azure site Recovery](site-recovery-overview.md)használatával replikálja és helyreállítja.

## <a name="before-you-start"></a>Előkészületek

[Ebből a forgatókönyvből](azure-to-azure-architecture.md)megtudhatja, hogyan biztosít a site Recovery vész-helyreállítást.

## <a name="supported-networking-resources"></a>Támogatott hálózati erőforrások

Az Azure-beli virtuális gépek replikálásakor a következő fő erőforrás-konfigurációkat adhatja meg a feladatátvételi virtuális géphez:

- [Belső terheléselosztó](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#what-is-standard-load-balancer)
- [Nyilvános IP-cím](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- [Hálózati biztonsági csoport](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) mind az alhálózathoz, mind a hálózati adapterhez

 > [!IMPORTANT]
  > Ezek a beállítások csak a feladatátvételi művelet esetében támogatottak, nem a feladatátvételi teszthez.

## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy előre tervezi a helyreállítási oldal konfigurációit.
- Előre hozza létre a hálózati erőforrásokat. Adja meg bemenetként, hogy Azure Site Recovery szolgáltatás tiszteletben tartsák ezeket a beállításokat, és győződjön meg arról, hogy a feladatátvételi virtuális gép megfelel ezeknek a beállításoknak.

## <a name="customize-failover-networking-configurations"></a>Feladatátvételi hálózati konfigurációk testreszabása

1. Nyissa meg a **replikált elemeket**. 
2. Válassza ki a kívánt Azure-beli virtuális gépet.
3. Válassza a **számítás és hálózat** lehetőséget, majd válassza a **Szerkesztés**lehetőséget. Figyelje meg, hogy a hálózati adapter konfigurációs beállításai tartalmazzák a forráshoz tartozó megfelelő erőforrásokat. 

     ![A feladatátvételi hálózati konfigurációk testreszabása](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Válassza a **Szerkesztés** lehetőséget a konfigurálni kívánt hálózati adapter közelében. A megnyíló következő panelen válassza ki a cél megfelelő előre létrehozott erőforrásait.

    ![A NIC-konfiguráció szerkesztése](media/azure-to-azure-customize-networking/nic-drilldown.png) 

5. Kattintson az **OK** gombra.

Site Recovery most megtartja ezeket a beállításokat, és gondoskodik arról, hogy a feladatátvételi virtuális gép a megfelelő hálózati adapteren keresztül kapcsolódjon a kiválasztott erőforráshoz.

## <a name="troubleshooting"></a>Hibakeresés

### <a name="unable-to-view-or-select-a-resource"></a>Nem lehet megtekinteni vagy kiválasztani egy erőforrást

Ha nem tudja kiválasztani vagy megtekinteni a hálózati erőforrásokat, folytassa a következő ellenőrzésekkel és feltételekkel:

- A hálózati erőforráshoz tartozó célként megadott mező csak akkor van engedélyezve, ha a forrás virtuális gép megfelelő bemenettel rendelkezik. Ez a vész-helyreállítási forgatókönyvre alapul, és a forrás pontos vagy kicsinyített verzióját szeretné használni.
- Minden hálózati erőforrás esetében egyes szűrők a legördülő listában vannak alkalmazva annak biztosítására, hogy a feladatátvételi virtuális gép magához tudja kapcsolni a kiválasztott erőforrást, és hogy a feladatátvétel megbízhatósága megmaradjon. Ezek a szűrők ugyanazon hálózati feltételeken alapulnak, amelyeket a forrás virtuális gép konfigurálásakor ellenőriztek volna.

Belső terheléselosztó érvényessége:

- A terheléselosztó és a célként megadott virtuális gép előfizetésének és régiójának azonosnak kell lennie.
- A belső terheléselosztó és a célként megadott virtuális gép által hozzárendelt virtuális hálózatnak azonosnak kell lennie.
- A célként megadott virtuális gép nyilvános IP-címe és a belső terheléselosztó SKU-jának azonosnak kell lennie.
- Ha a célként megadott virtuális gép egy rendelkezésre állási zónában van beállítva, akkor ellenőrizze, hogy a terheléselosztó zóna redundáns vagy bármely rendelkezésre állási zóna része-e. (Az alapszintű SKU Load Balancer nem támogatja a zónákat, és ebben az esetben nem jelennek meg a legördülő listában.)
- Győződjön meg arról, hogy a belső terheléselosztó előre létrehozott háttér-készlettel és előtér-konfigurációval rendelkezik.


Nyilvános IP-cím:
    
- A nyilvános IP-cím és a célként megadott virtuális gép előfizetésének és régiójának azonosnak kell lennie.
- A célként megadott virtuális gép nyilvános IP-címe és a belső terheléselosztó SKU-jának azonosnak kell lennie.

Hálózati biztonsági csoport:
- A hálózati biztonsági csoport és a célként megadott virtuális gép előfizetésének és régiójának azonosnak kell lennie.


> [!WARNING]
> Ha a célként megadott virtuális gép egy rendelkezésre állási csoporttal van társítva, akkor a másik virtuális gép nyilvános IP-címéhez és belső Load Balancerhez kell társítania ugyanazt a SKU-t, mint a rendelkezésre állási csoporton belül. Ha nem, akkor lehet, hogy nem sikerül a feladatátvétel.
