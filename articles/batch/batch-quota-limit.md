---
title: Szolgáltatási kvóták és korlátozások - Azure Batch | Microsoft dokumentumok
description: Ismerje meg az azure batch-kvótákat, korlátokat és korlátozásokat, valamint a kvótaemelések igénylésének módját
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: c8a78acfa1f3e7332cee337ae1a82ee365fe356a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248228"
---
# <a name="batch-service-quotas-and-limits"></a>A Bach szolgáltatás kvótái és korlátozásai

Más Azure-szolgáltatásokhoz is csakúgy, mint más Azure-szolgáltatások, a Batch szolgáltatáshoz társított bizonyos erőforrások korlátozottak. Ezek közül a korlátok közül sok az Azure által az előfizetés vagy a fiók szintjén alkalmazott alapértelmezett kvóták. Ez a cikk ismerteti ezeket az alapértelmezett értékeket, és hogyan kérhet kvótanövelést.

Tartsa szem előtt ezeket a kvótákat a Batch-számítási feladatok tervezése és felskálázása kor. Ha például a készlet nem éri el a megadott számítási csomópontok célszámát, előfordulhat, hogy elérte a Batch-fiók alapkvóta-korlátját.

Több Batch számítási feladatot is futtathat egyetlen Batch-fiókon, de el is oszthatja a számítási feladatokat ugyanazon előfizetéshez, de különböző Azure-régiókhoz tartozó Batch-fiókok között.

Ha azt tervezi, hogy éles számítási feladatok futtatásához batch, előfordulhat, hogy növelnie kell egy vagy több a kvóták felett az alapértelmezett. Ha növelni szeretné a kvótát, ingyenesen megnyithat egy online [ügyfélszolgálati kérelmet.](#increase-a-quota)

## <a name="resource-quotas"></a>Erőforráskvóták

A kvóta hitelkeret, nem pedig kapacitásgarancia. Ha nagy kapacitásra van szüksége, forduljon az Azure ügyfélszolgálatához.

Azt is vegye figyelembe, hogy a kvóták nem garantált értékek. A kvóták a Batch szolgáltatás módosításaitól vagy a kvótaérték módosítására irányuló felhasználói kéréstől függően változhatnak.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

### <a name="cores-quotas-in-user-subscription-mode"></a>Magkvóták felhasználói előfizetési módban

Ha létrehozott egy Batch-fiókot, amelynek készletfoglalási módja **felhasználói előfizetésre**van állítva, a kvóták eltérően lesznek alkalmazva. Ebben a módban a batch virtuális gépek és más erőforrások közvetlenül az előfizetésben jönnek létre, amikor egy készlet jön létre. Az Azure Batch magok kvóták nem vonatkoznak az ebben a módban létrehozott fiókra. Ehelyett a kvótákat a regionális számítási magok és egyéb erőforrások az előfizetésben. Ezekről a kvótákról az [Azure-előfizetési és szolgáltatáskorlátokban, kvótákban és korlátozásokban olvashat bővebben.](../azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="pool-size-limits"></a>A készlet méretére vonatkozó korlátozások

A készletméret-korlátokat a Batch szolgáltatás állítja be. Az [erőforráskvótákkal](#resource-quotas)ellentétben ezek az értékek nem módosíthatók. Csak a csomópontok közötti kommunikációval és egyéni rendszerképekkel rendelkező készletek a szabványos kvótától eltérő korlátozásokkal rendelkeznek.

| **Erőforrás** | **Felső korlát** |
| --- | --- |
| **Csomópontok közötti [kommunikációt engedélyező készlet](batch-mpi.md) ben lévő csomópontok**  ||
| Kötegelt szolgáltatáskészlet foglalási módja | 100 |
| Kötegelt előfizetési készlet foglalási módja | 80 |
| ** [Felügyelt lemezkép-erőforrással létrehozott számítási](batch-custom-images.md)csomópontok**<sup>1</sup> ||
| Dedikált csomópontok | 2000 |
| Alacsony prioritású csomópontok | 1000 |

<sup>1</sup> Olyan készletek esetén, amelyek nem engedélyezve vannak a csomópontok közötti kommunikációban.

## <a name="other-limits"></a>Egyéb korlátok

A Batch szolgáltatás által beállított további korlátozások. Az [erőforráskvótákkal](#resource-quotas)ellentétben ezek az értékek nem módosíthatók.

| **Erőforrás** | **Felső korlát** |
| --- | --- |
| [Egyidejű feladatok](batch-parallel-node-tasks.md) számítási csomópontonként | 4 x csomópontmagok száma |
| [Alkalmazások](batch-application-packages.md) kötegelt fiókonként | 20 |
| Alkalmazáscsomagok alkalmazásonként | 40 |
| Alkalmazáscsomagok készletenként | 10 |
| A feladat maximális élettartama | 180 nap<sup>1</sup> |
| [Csatlakoztatások](virtual-file-mount.md) számítási csomópontonként | 10 |

<sup>1</sup> A feladat maximális élettartama a feladathoz való hozzátöltéstől a feladat befejezéséig 180 nap. A befejezett feladatok hét napig fennállnak; a maximális élettartamon belül el nem végzett feladatok adatai nem érhetők el.

## <a name="view-batch-quotas"></a>Kötegkvóták megtekintése

Tekintse meg batch-fiókkvótáit az [Azure Portalon.][portal]

1. Válassza a **Kötegfiókok lehetőséget** a portálon, majd válassza ki az önt érdeklő Batch-fiókot.
1. Válassza **a Kvóták** lehetőséget a Batch-fiók menüjében.
1. A Köteg fiókra jelenleg alkalmazott kvóták megtekintése

    ![Kötegelt fiókkvóták][account_quotas]

## <a name="increase-a-quota"></a>Kvóta növelése

Az alábbi lépésekkel kvótanövelést kérhet a Batch-fiókjához vagy az Előfizetéséhez az [Azure Portalon][portal]keresztül. A kvótanövekedés típusa a Batch-fiók készletfoglalási módjától függ. Kvótanövelés kéréséhez meg kell adnia azt a virtuális gépsorozatot, amelyhez növelni szeretné a kvótát. A kvótanövelés alkalmazásakor a rendszer a virtuális gépek összes sorozatára alkalmazza.

### <a name="increase-cores-quota-in-batch"></a>Magok kvótájának növelése kötegben 

1. Válassza a **Súgó + támogatás** csempét a portál irányítópultján, vagy a kérdőjelet (**?**) a portál jobb felső sarkában.
1. Válassza **az Új támogatási kérelem** > **alapjai lehetőséget.**
1. Az **alapok:**
   
    a. **Problématípus** > **szolgáltatás és előfizetési korlátok (kvóták)**
   
    b. Válassza ki előfizetését.
   
    c. **Kvótatípus** > **Batch**
      
    Válassza a **Tovább lehetőséget.**
    
1. A **Details** (Részletek) lapon:
      
    a. A **Részletek megadása**területen adja meg a helyet, a kvótatípusát és a Köteg fiókot.
    
    ![Kötegkvóta növelése][quota_increase]

    A kvótatípusok a következők:

    * **Kötegenkénti számla**  
        Egyetlen batch-fiókra vonatkozó értékek, beleértve a dedikált és alacsony prioritású magokat, valamint a feladatok és készletek számát.
        
    * **Régiónként**  
        Egy régió összes Batch-fiókjára vonatkozó értékek, amelyek régiónként régiónként, régiónként tartalmazzák a kötegelt fiókok számát.

    Alacsony prioritású kvóta egyetlen érték az összes virtuálisgép-sorozatban. Ha korlátozott ski-kre van szüksége, válassza ki **az alacsony prioritású magokat,** és a virtuálisgép-családokat is be kell vonnia.

    b. Válassza ki a **súlyossági** szintet az [üzleti hatásnak megfelelően.][support_sev]

    Válassza a **Tovább lehetőséget.**

1. A **Elérhetőségi adatok:**
   
    a. Válassza ki **az Előnyben részesített névjegymódszert**.
   
    b. Ellenőrizze és adja meg a szükséges kapcsolattartási adatokat.
   
    A támogatási kérelem elküldéséhez válassza a **Létrehozás** gombot.

Miután elküldte támogatási kérelmét, az Azure-támogatás felveszi Önnel a kapcsolatot. A kvótakérelmek néhány percen vagy legfeljebb két munkanapon belül teljesíthetők.

## <a name="related-quotas-for-vm-pools"></a>A virtuálisgép-készletek kapcsolódó kvótái

Az Azure virtuális hálózatban üzembe helyezett virtuális gép konfigurációjában lévő kötegkészletek automatikusan lefoglalnak további Azure-hálózati erőforrásokat. A következő erőforrások szükségesek a virtuális hálózat minden 50 készletcsomópontja számára:

* Egy [hálózati biztonsági csoport](../virtual-network/security-overview.md#network-security-groups)
* Egy [nyilvános IP-cím](../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* Egy [terheléselosztó](../load-balancer/load-balancer-overview.md)

Ezek az erőforrások a batch készlet létrehozásakor biztosított virtuális hálózatot tartalmazó előfizetésben vannak lefoglalva. Ezekre az erőforrásokra az előfizetésben meghatározott [erőforráskvóták](../azure-resource-manager/management/azure-subscription-service-limits.md) vonatkoznak. Ha azt tervezi, nagy készlet központi telepítések egy virtuális hálózatban, ellenőrizze az előfizetés kvóták at ezeket az erőforrásokat. Szükség esetén kérje az Azure Portal növelését a **Súgó + támogatás**lehetőség kiválasztásával.


## <a name="related-topics"></a>Kapcsolódó témakörök
* [Azure Batch-fiókok létrehozása az Azure Portalon](batch-account-create-portal.md)
* [Az Azure Batch szolgáltatás áttekintése](batch-api-basics.md)
* [Azure-előfizetési és szolgáltatáskorlátok, kvóták és korlátozások](../azure-resource-manager/management/azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: https://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
[quota_increase]: ./media/batch-quota-limit/quota-increase.png
