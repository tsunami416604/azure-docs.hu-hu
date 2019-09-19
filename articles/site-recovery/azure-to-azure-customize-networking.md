---
title: A feladatátvételi virtuális gép hálózati konfigurációinak testreszabása | Microsoft Docs
description: Áttekintést nyújt a feladatátvételi virtuális gép hálózati konfigurációinak testreszabásáról az Azure-beli virtuális gépek Azure Site Recovery használatával történő replikálásával.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/07/2019
ms.author: rajanaki
ms.openlocfilehash: 1905d6afb023b1bed15f1359fed8477d815acb45
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087707"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>A cél Azure-beli virtuális gép hálózati konfigurációjának testreszabása

Ez a cikk útmutatást nyújt a hálózati konfigurációk testreszabásához a cél Azure-beli virtuális gépen, ha az Azure-beli virtuális gépeket az egyik régióból a másikba, [Azure site Recovery](site-recovery-overview.md)használatával replikálja és helyreállítja.

## <a name="before-you-start"></a>Előkészületek

[Ebből a forgatókönyvből](azure-to-azure-architecture.md)megtudhatja, hogyan biztosít a site Recovery vész-helyreállítást.

## <a name="supported-networking-resources"></a>Támogatott hálózati erőforrások

Az Azure-beli virtuális gépek replikálásakor a feladatátvételi virtuális gép következő fő erőforrás-konfigurációi adhatók meg.

- [Belső terheléselosztó](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#what-is-standard-load-balancer)
- [Nyilvános IP-cím](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- [Hálózati biztonsági csoport](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) mind az alhálózathoz, mind a hálózati adapterhez

 > [!IMPORTANT]
  > Ezek a beállítások csak a feladatátvételi műveletben támogatottak, és nem a feladatátvételi teszthez.

## <a name="pre-requisites"></a>Előfeltételek

- Győződjön meg arról, hogy előre tervezi a helyreállítási oldal konfigurációit.
- A hálózati erőforrásokat előre kell létrehoznia. Adja meg bemenetként, hogy Azure Site Recovery szolgáltatás tiszteletben tartsák ezeket a beállításokat, és győződjön meg arról, hogy a feladatátvételi virtuális gép megfelel ezeknek a beállításoknak.

## <a name="steps-to-customize-failover-networking-configurations"></a>A feladatátvételi hálózati konfigurációk testreszabásának lépései

1. Navigáljon a **replikált elemekhez**. 
2. Kattintson a kívánt Azure-beli virtuális gépre.
3. Kattintson a **számítás és a hálózat**, majd a **Szerkesztés**elemre. Figyelje meg, hogy a hálózati adapter konfigurációs beállításai tartalmazzák a forráshoz tartozó megfelelő erőforrásokat. 

     ![testreszabása](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. A konfigurálni kívánt hálózati adapter közelében kattintson a **Szerkesztés** elemre. A megnyíló következő panelen válassza ki a célhelyen a megfelelő előre létrehozott erőforrásokat.

    ![NIC – részletezés](media/azure-to-azure-customize-networking/nic-drilldown.png) 

5. Kattintson az **OK** gombra.

Site Recovery most megtartja ezeket a beállításokat, és gondoskodik arról, hogy a feladatátvételi virtuális gép a megfelelő hálózati adapteren keresztül kapcsolódjon a kiválasztott erőforráshoz.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="unable-to-view-or-select-a-resource"></a>Nem lehet megtekinteni vagy kiválasztani egy erőforrást

Ha nem tud kijelölni vagy megtekinteni egy hálózati erőforrást, tekintse át a következő ellenőrzéseket & feltételeket:

- A hálózati erőforráshoz tartozó célként megadott mező csak akkor engedélyezett, ha a forrás virtuális gép megfelelő bemenettel rendelkezik. Ez a vész-helyreállítási forgatókönyvre alapul, és a forrás pontos vagy méretezett verzióját szeretné használni.
- A kérdéses hálózati erőforrások mindegyike esetében egyes szűrők a legördülő menüben vannak alkalmazva annak biztosításához, hogy a feladatátvevő virtuális gép magához tudja kapcsolni a kiválasztott erőforrást, és hogy a feladatátvétel megbízhatósága megmaradjon. Ezek a szűrők ugyanazon hálózati feltételeken alapulnak, amelyeket a forrás virtuális gép konfigurálásakor ellenőriztek volna.

Belső terheléselosztó érvényessége:

1. Az előfizetésnek és az LB-nek és a célként megadott virtuális gépnek azonosnak kell lennie.
2. A belső Load Balancerhoz és a célként megadott VMshould tartozó virtuális hálózatnak azonosnak kell lennie.
3. A célként megadott virtuális gép nyilvános IP-címe és a belső terheléselosztó SKU-jának azonosnak kell lennie.
4. Ha a célként megadott virtuális gép egy rendelkezésre állási zónában van beállítva, akkor ellenőrizze, hogy a terheléselosztó zóna redundáns vagy bármely rendelkezésre állási zóna része-e. (Az alapszintű SKU-terheléselosztó nem támogatja a zónákat, és ebben az esetben nem jelennek meg a legördülő listából.)
5. Győződjön meg arról, hogy a belső terheléselosztó rendelkezik egy előre létrehozott háttér-készlettel és előtér-konfigurációval.


Nyilvános IP-cím:
    
1. A nyilvános IP-cím és a célként megadott virtuális gép előfizetésének és régiójának azonosnak kell lennie.
2. A célként megadott virtuális gép nyilvános IP-címe és a belső terheléselosztó SKU-jának azonosnak kell lennie.

Hálózati biztonsági csoport:
1. A hálózati biztonsági csoport és a célként megadott virtuális gép előfizetésének és régiójának azonosnak kell lennie.


> [!WARNING]
> Ha a célként megadott virtuális gép egy rendelkezésre állási csoporthoz van társítva, akkor társítania kell ugyanahhoz az SKU-hoz tartozó nyilvános IP-/belső terheléselosztó-t, mint a többi virtuális gép nyilvános IP-címéhez/belső terheléselosztó a rendelkezésre állási csoporton belül. Ennek elmulasztása feladatátvételi hibát eredményezhet.
