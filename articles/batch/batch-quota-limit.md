---
title: Szolgáltatási kvóták és korlátozások – Azure Batch | Microsoft Docs
description: Ismerje meg az alapértelmezett Azure Batch kvótákat, korlátozásokat és korlátozásokat, valamint a kvóta növelésének módját
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79248228"
---
# <a name="batch-service-quotas-and-limits"></a>A Bach szolgáltatás kvótái és korlátozásai

A többi Azure-szolgáltatáshoz hasonlóan a Batch szolgáltatáshoz társított bizonyos erőforrások is korlátozottak. Ezen korlátok közül sok az Azure által az előfizetés vagy a fiók szintjén alkalmazott alapértelmezett kvóták. Ez a cikk ezeket az alapértelmezéseket ismerteti, valamint azt, hogy miként lehet a kvóta növelését kérni.

Tartsa szem előtt ezeket a kvótákat a Batch-munkaterhelések tervezése és skálázása során. Ha például a készlet nem éri el a megadott számítási csomópontok számát, akkor előfordulhat, hogy elérte a Batch-fiókhoz tartozó fő kvóta korlátját.

Több Batch számítási feladatot is futtathat egyetlen Batch-fiókon, de el is oszthatja a számítási feladatokat ugyanazon előfizetéshez, de különböző Azure-régiókhoz tartozó Batch-fiókok között.

Ha éles számítási feladatokat kíván futtatni a Batch szolgáltatásban, előfordulhat, hogy az alapértelmezettnél nagyobb mennyiségű kvótát kell megadnia. Ha kvótát szeretne felvenni, akkor díjmentesen nyithat meg egy online [ügyfélszolgálati kérést](#increase-a-quota) .

## <a name="resource-quotas"></a>Erőforráskvóták

Kvóta kreditjét, nem kapacitás garantálja. Ha nagyméretű kapacitásra van szüksége, vegye fel a kapcsolatot az Azure ügyfélszolgálatával.

Azt is vegye figyelembe, hogy a kvóták nem garantált értékek. A kvóták a Batch szolgáltatás módosításaitól vagy a kvóta értékének módosítására vonatkozó felhasználói kéréstől függően változhatnak.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

### <a name="cores-quotas-in-user-subscription-mode"></a>Magok kvótái felhasználói előfizetési módban

Ha olyan batch-fiókot hozott létre, amelyben a készlet-kiosztási mód **felhasználói előfizetésre**van beállítva, a kvótákat a rendszer eltérően alkalmazza. Ebben a módban a Batch virtuális gépek és egyéb erőforrások közvetlenül az előfizetésben jönnek létre a készlet létrehozásakor. Az Azure Batch magok kvótái nem érvényesek az ebben a módban létrehozott fiókra. Ehelyett a rendszer a regionális számítási magokra és egyéb erőforrásokra vonatkozó előfizetésében lévő kvótákat alkalmazza. További információ ezekről a kontingensekről az [Azure-előfizetésekben és a szolgáltatási korlátokban, a kvótákban és a megkötésekben](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="pool-size-limits"></a>Készlet méretének korlátai

A készlet méretének korlátait a Batch szolgáltatás állítja be. Az [erőforrás-kvótákkal](#resource-quotas)ellentétben ezek az értékek nem módosíthatók. Csak a csomópontok közötti kommunikációt és az egyéni lemezképeket tartalmazó készletek rendelkeznek a normál kvótától eltérő korlátozásokkal.

| **Erőforrás** | **Felső korlát** |
| --- | --- |
| **A [csomópontok közötti kommunikációt engedélyező készlet](batch-mpi.md) számítási csomópontjai**  ||
| Batch szolgáltatás-készlet kiosztási módja | 100 |
| Batch-előfizetési készlet lefoglalási módja | 80 |
| **[Felügyelt rendszerkép 1-es erőforrásával létrehozott készlet](batch-custom-images.md)számítási csomópontjai**<sup></sup> ||
| Dedikált csomópontok | 2000 |
| Alacsony prioritású csomópontok | 1000 |

<sup>1</sup> a nem csomópontok közötti kommunikációt engedélyező készletek esetén.

## <a name="other-limits"></a>Egyéb korlátok

A Batch szolgáltatás által beállított további korlátok. Az [erőforrás-kvótákkal](#resource-quotas)ellentétben ezek az értékek nem módosíthatók.

| **Erőforrás** | **Felső korlát** |
| --- | --- |
| [Egyidejű feladatok](batch-parallel-node-tasks.md) /számítási csomópontok | 4 x csomópont-magok száma |
| [Alkalmazások](batch-application-packages.md) batch-fiókkal | 20 |
| Alkalmazás-csomagok eszközönként | 40 |
| Alkalmazáscsomag/készlet | 10 |
| Feladat maximális élettartama | 180 nap<sup>1</sup> |
| [Csatlakoztatások](virtual-file-mount.md) /számítási csomópontok | 10 |

<sup>1</sup> a feladat maximális élettartama, amikor a feladat bekerül a feladatba, 180 nap. A Befejezett feladatok hét napig fennmaradnak; a maximális élettartamon belül nem befejezett feladatok adatai nem érhetők el.

## <a name="view-batch-quotas"></a>Batch-kvóták megtekintése

A Batch-fiók kvótáinak megtekintése a [Azure Portalban][portal].

1. Válassza a **Batch-fiókok** lehetőséget a portálon, majd válassza ki azt a Batch-fiókot, amelyre kíváncsi.
1. Válassza a **kvóták** lehetőséget a Batch-fiók menüjében.
1. A Batch-fiókra jelenleg alkalmazott kvóták megtekintése

    ![Batch-fiókok kvótái][account_quotas]

## <a name="increase-a-quota"></a>Kvóta emelése

Kövesse az alábbi lépéseket a kvóta növeléséhez a Batch-fiókjához vagy az előfizetéshez a [Azure Portal][portal]használatával. A kvóta megnövekedésének típusa a Batch-fiók készlet-kiosztási módjától függ. A kvóta növeléséhez meg kell adnia azt a virtuálisgép-sorozatot, amelynek a kvótáját emelni szeretné. A kvóta növelésének alkalmazása esetén a rendszer a virtuális gépek minden sorozatára alkalmazza.

### <a name="increase-cores-quota-in-batch"></a>Magok kvótájának bővítése a Batchben 

1. Válassza a **Súgó + támogatás** csempét a portál irányítópultján, vagy a portál jobb felső sarkában látható kérdőjelet ( **?** ).
1. Válassza az **új támogatási kérelem** > **alapismeretek**lehetőséget.
1. Az **alapjaiban**:
   
    a. **Probléma típusa** > **szolgáltatás és előfizetés korlátai (kvóták)**
   
    b. Válassza ki előfizetését.
   
    c. **Kvóta típusa** > **Batch**
      
    Kattintson a **Tovább** gombra.
    
1. A **Details** (Részletek) lapon:
      
    a. A **részletek**megadása területen adja meg a helyet, a kvóta típusát és a Batch-fiókot.
    
    ![Batch-kvóta növekedése][quota_increase]

    A kvóta típusai a következők:

    * **/Batch-fiók**  
        Egyetlen batch-fiókhoz tartozó értékek, beleértve a dedikált és alacsony prioritású magokat, valamint a feladatok és készletek számát.
        
    * **Régiónként**  
        Egy adott régióban lévő összes batch-fiókra vonatkozó értékek, valamint a Batch-fiókok száma régiónként/előfizetéssel.

    Az alacsony prioritású kvóta egyetlen érték az összes virtuálisgép-sorozaton belül. Ha korlátozott SKU-ra van szüksége, ki kell választania az **alacsony prioritású magok** elemet, és fel kell vennie a lekéréses virtuálisgép-családokat.

    b. Válasszon ki egy **súlyosságot** az [üzleti hatásnak][support_sev]megfelelően.

    Kattintson a **Tovább** gombra.

1. A **kapcsolattartási adatok**:
   
    a. Válasszon ki egy **előnyben részesített kapcsolattartási módszert**.
   
    b. Ellenőrizze, és adja meg a szükséges kapcsolattartási adatokat.
   
    Válassza a **Létrehozás** lehetőséget a támogatási kérelem elküldéséhez.

A támogatási kérelem elküldését követően az Azure-támogatás kapcsolatba lép Önnel. A kvóta-kérelmek néhány percen belül, vagy akár két munkanapon belül is elvégezhető.

## <a name="related-quotas-for-vm-pools"></a>Virtuálisgép-készletek kapcsolódó kvótái

Az Azure-beli virtuális hálózatban üzembe helyezett virtuálisgép-konfigurációban található batch-készletek automatikusan további Azure hálózati erőforrásokat foglalnak magukban. A következő erőforrásokra van szükség egy virtuális hálózat 50-es készlet-csomópontjaihoz:

* Egy [hálózati biztonsági csoport](../virtual-network/security-overview.md#network-security-groups)
* Egy [nyilvános IP-cím](../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* Egy [Load Balancer](../load-balancer/load-balancer-overview.md)

Ezek az erőforrások a Batch-készlet létrehozásakor megadott virtuális hálózatot tartalmazó előfizetésben vannak lefoglalva. Ezekre az erőforrásokra az előfizetésben meghatározott [erőforráskvóták](../azure-resource-manager/management/azure-subscription-service-limits.md) vonatkoznak. Ha nagyméretű készlet-központi telepítéseket tervez egy virtuális hálózaton, ellenőrizze az előfizetéshez tartozó kvótákat ezekhez az erőforrásokhoz. Ha szükséges, a **Súgó és támogatás**lehetőség kiválasztásával növelje a Azure Portal növekedését.


## <a name="related-topics"></a>Kapcsolódó témakörök
* [Azure Batch fiók létrehozása a Azure Portal használatával](batch-account-create-portal.md)
* [A Azure Batch funkcióinak áttekintése](batch-api-basics.md)
* [Az Azure-előfizetések és -szolgáltatások korlátozásai, kvótái és megkötései](../azure-resource-manager/management/azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: https://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
[quota_increase]: ./media/batch-quota-limit/quota-increase.png
