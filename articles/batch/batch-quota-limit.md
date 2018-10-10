---
title: Azure Batch szolgáltatás kvótái és korlátozásai |} A Microsoft Docs
description: Alapértelmezett Azure Batch-kvóták, korlátozások és megkötések ismertetése, és növeli a hogyan kvótát
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 25a8150a2fcf7cdd4e3c82478c0b3db3dad870b4
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48887564"
---
# <a name="batch-service-quotas-and-limits"></a>A Bach szolgáltatás kvótái és korlátozásai

Mivel az más Azure-szolgáltatásokkal, korlátozva van az egyes erőforrásokat a Batch szolgáltatáshoz társított. Ezeket a korlátokat számos az előfizetés vagy a fiók szintjén az Azure által alkalmazott alapértelmezett kvóta. Ez a cikk ismerteti azokat az alapértelmezett beállításokat, és hogyan kérheti a kvóta növeli.

Kvóta ne feledje, tervezési és a Batch számítási feladatok vertikális. Például ha a készlet nem éri el a számítási csomópontok megadott célszámát, lehet, hogy elérte magkvótájának határértékét a Batch-fiók.

Több Batch számítási feladatot is futtathat egyetlen Batch-fiókon, de el is oszthatja a számítási feladatokat ugyanazon előfizetéshez, de különböző Azure-régiókhoz tartozó Batch-fiókok között.

Ha azt tervezi, éles számítási feladatok futtatása a Batchben, szükség lehet egy vagy több az alapértelmezettnél eggyel a kvóták növelése érdekében. Ha szeretné a kvóta növeléséhez, akkor megnyithatja az online [támogatási kérést](#increase-a-quota) díjmentesen.

> [!NOTE]
> Kvóta kreditjét, nem kapacitás garantálja. Ha nagyméretű kapacitásigények, lépjen kapcsolatba az Azure-támogatás.
> 
> 

## <a name="resource-quotas"></a>Erőforráskvóták
[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]


### <a name="cores-quotas-in-user-subscription-mode"></a>Magkvóták felhasználói előfizetési módban

Ha beállítása készletlefoglalási móddal létrehozott Batch-fiók **felhasználói előfizetés**, eltérő kvóták érvényesek. Ebben a módban a Batch virtuális gépei és egyéb erőforrások jönnek létre közvetlenül az előfizetésben egy készlet létrehozásakor. Az Azure Batch magkvóták nem hozza létre ezt a módot a alkalmazni. Ehelyett a kvótákat, az előfizetéséhez tartozó területi számítási magot, és más erőforrások lépnek érvénybe. További információ a kvóta [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../azure-subscription-service-limits.md).

## <a name="other-limits"></a>Egyéb korlátok

| **Erőforrás** | **Felső korlát** |
| --- | --- |
| [Egyidejű feladatok](batch-parallel-node-tasks.md) számítási csomópontok |csomópont-magok száma 4 x |
| [Alkalmazások](batch-application-packages.md) Batch-fiókonként |20 |
| Alkalmazáscsomagok alkalmazásonként |40 |
| A tevékenység maximális élettartama | 7 nap<sup>1</sup> |
| Számítási csomópontok [készletben a csomópontok közötti kommunikáció engedélyezve](batch-mpi.md) | 100 |
| Dedikált számítási csomópontok [egyéni Virtuálisgép-rendszerkép használatával létrehozott készlet](batch-custom-images.md) | 2500 |
| Alacsony prioritású számítási csomópontok [egyéni Virtuálisgép-rendszerkép használatával létrehozott készlet](batch-custom-images.md) | 1000 |

<sup>1</sup> maximális élettartama egy feladatot, amikor megjelenik a feladat befejezését követően a rendszer 7 nap. A befejezett tevékenységek korlátlan ideig megőrződnek; a maximális élettartamon belül nem befejezett tevékenységek adatai nem elérhetőek.

## <a name="view-batch-quotas"></a>Batch-kvóták megtekintése

A Batch fiókra vonatkozó kvóták az megtekintése a [az Azure portal][portal].

1. Válassza ki **Batch-fiókok** a portálon, majd válassza ki az Önt érdeklő Batch-fiókhoz.
1. Válassza ki **kvóták** a Batch-fiók menüjében.
1. A Batch-fiók telepítve a kvóták megtekintése
   
    ![Batch-fiókra vonatkozó kvóták][account_quotas]



## <a name="increase-a-quota"></a>A kvóta növeléséhez

Kövesse az alábbi lépéseket egy kvótát a Batch-fiók vagy az előfizetés használatával növelheti a [az Azure portal][portal]. Kvótanövelést típusát a Batch-fiók készletlefoglalási módjától függ.

### <a name="increase-a-batch-cores-quota"></a>Batch-magkvóta növeléséhez 

1. Válassza ki a **súgó + támogatás** csempét az irányítópulton, vagy a kérdőjel (**?**) a portál jobb felső sarkában.
1. Válassza ki **új támogatási kérelem** > **alapjai**.
1. A **alapjai**:
   
    a. **Probléma típusa** > **kvóta**
   
    b. Válassza ki előfizetését.
   
    c. **Kvóta típusa** > **Batch**
   
    d. **Támogatási csomag** > **kvóta támogatás – benne foglalt**
   
    Kattintson a **Tovább** gombra.
1. A **probléma**:
   
    a. Válassza ki a **súlyossági** a következők szerint a [üzletmenetre gyakorolt hatás][support_sev].
   
    b. A **részletek**, adja meg minden egyes módosítani kívánt kvótát, a Batch-fiók nevét és az új határértéket.
   
    Kattintson a **Tovább** gombra.
1. A **kapcsolattartási adatok**:
   
    a. Válassza ki a **elsődleges kapcsolattartási módszert**.
   
    b. Győződjön meg arról, és adja meg a szükséges kapcsolattartási adatokat.
   
    Kattintson a**Create** (Létrehozás) gombra a támogatási kérelem elküldéséhez.

Miután a támogatási kérelem elküldése, az Azure-támogatás felveszi Önnel a kapcsolatot. Vegye figyelembe, hogy a kérelem befejezése legfeljebb 2 munkanapon is igénybe vehet.

## <a name="related-quotas-for-vm-pools"></a>Virtuálisgép-készletek a kapcsolódó kvóták

A virtuális gép konfigurációjában, automatikusan üzembe helyezett Azure-beli virtuális hálózathoz a Batch-készletek foglalása további Azure hálózati erőforrásaival. A következő erőforrások a virtuális hálózat minden egyes 50 készletcsomópontokat van szükség:

* 1 [hálózati biztonsági csoport](../virtual-network/security-overview.md#network-security-groups)
* 1 [nyilvános IP-cím](../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* 1 [terheléselosztó](../load-balancer/load-balancer-overview.md)

Ezeket az erőforrásokat az előfizetést, amely tartalmazza a virtuális hálózat, a Batch-készlet létrehozásakor megadott vannak lefoglalva. Ezeket az erőforrásokat az előfizetés-re van korlátozva [erőforráskvóták](../azure-subscription-service-limits.md). Ha azt tervezi, hogy a nagy méretű készletet üzemelő példányok virtuális hálózatban, ellenőrizze az előfizetéshez tartozó kvóták ezekhez az erőforrásokhoz. Szükség esetén kérheti növelését az Azure Portalon kiválasztásával **súgó + támogatás**.


## <a name="related-topics"></a>Kapcsolódó témakörök
* [Hozzon létre egy Azure Batch-fiókot az Azure portal használatával](batch-account-create-portal.md)
* [Az Azure Batch funkcióinak áttekintése](batch-api-basics.md)
* [Az Azure-előfizetések és -szolgáltatások korlátozásai, kvótái és megkötései](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
