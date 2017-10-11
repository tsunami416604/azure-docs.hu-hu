---
title: "Az Azure Batch kvótái és korlátai szolgáltatás |} Microsoft Docs"
description: "További tudnivalók az alapértelmezett Azure Batch kvóták, korlátozások és megkötések-re, arról, hogyan kérhet kvóta"
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f3f69ed8d3a985afe07e648e7512a88b25278ced
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="batch-service-quotas-and-limits"></a>A Bach szolgáltatás kvótái és korlátozásai

Mint az egyéb Azure-szolgáltatásokkal, nincsenek korlátozások bizonyos erőforrások, a Batch szolgáltatás társított. Ezek a korlátozások számos alapértelmezett kvóták alkalmazása az Azure-ban az előfizetés vagy a fiók szintjén. Ez a cikk ismerteti azokat az alapértelmezett beállításokat, és hogyan kérheti a kvótájának növeli.

Vegye figyelembe ezeket a kvótákat a Batch számítási feladatok tervezésekor és bővítésekor. Például a készlet nem eléri a számítási csomópontok a megadott cél száma, ha előfordulhat, hogy elérte a core kvótát a Batch-fiók, vagy a regionális virtuális gép magok kvóta az előfizetéséhez.

Több Batch számítási feladatot is futtathat egyetlen Batch-fiókon, de el is oszthatja a számítási feladatokat ugyanazon előfizetéshez, de különböző Azure-régiókhoz tartozó Batch-fiókok között.

Ha szeretné futtatni a termelési számítási feladatokhoz kötegben, szükség lehet egy vagy több, a kvóták fenti az alapértelmezett növelése érdekében. Ha azt szeretné, a kvóta emelése, az online megnyithatja [ügyfél-támogatási kérelem](#increase-a-quota) díjmentesen.

> [!NOTE]
> A kvóta legfeljebb, nem kapacitás garancia. Ha nagyméretű lemezkapacitási igényekről, forduljon az Azure támogatási szolgálatához.
> 
> 

## <a name="resource-quotas"></a>Erőforráskvóták
[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="quotas-in-user-subscription-mode"></a>Kvótákat felhasználói előfizetési módban

Tárolókészlet foglalási módban a Batch-fiókhoz **felhasználói előfizetési**, a Batch-virtuális gépek és más erőforrások, például a storage-fiókok, közvetlenül az előfizetésében jönnek létre, amikor egy alkalmazáskészlet jön létre. Az Azure Batch magok kvóta nem vonatkozik az ebben a módban létrehozott fiók. Ehelyett az előfizetéshez tartozó területi kvóták számítási mag, és más erőforrások alkalmazza. További információ a ezek mely százalékértékénél kéri [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](../azure-subscription-service-limits.md).

Egy olyan fiók felhasználói előfizetési módban létrehozott erőforrás-használat tervezésekor vegye figyelembe a következő kötegelt erőforrások (számítási magok) mellett minden 40 Linux virtuális gépet, vagy szükségesek 20 Windows virtuális gépek:

| Erőforrás | Kvóta | Szolgáltató |
| --- | ---| --- |
| Egy tárfiók | Tárfiókok | Microsoft.Storage |
| Egy nyilvános IP-cím | Nyilvános IP-címek | Microsoft.Network | 
| Egy virtuális hálózat | Virtuális hálózatok | Microsoft.Network | 
| Egy hálózati biztonsági csoport | Network Security Groups (Hálózati biztonsági csoportok) | Microsoft.Network | 
| Egy virtuálisgép-méretezési csoport | Virtual Machine Scale Sets | Microsoft.Compute | 
| Egy terheléselosztó | Terheléselosztók | Microsoft.Network | 

Regionális szinten, vagy a virtuális gép termékcsalád magok kvóta a Virtuálisgép-méretet, a Batch-készlet vagy készletek szükséges megfelelően kell beállítani:

| Kvóta | Szolgáltató |
| --- | ---- |
| Teljes regionális magok | Microsoft.Compute |
| … Családbiztonsági magok | Microsoft.Compute |



## <a name="other-limits"></a>Egyéb korlátozások
| **Erőforrás** | **Felső korlát** |
| --- | --- |
| [Egyidejű feladatok](batch-parallel-node-tasks.md) egyes számítási csomópontjain |csomópont magok száma 4 x |
| [Alkalmazások](batch-application-packages.md) / Batch-fiókhoz. |20 |
| Alkalmazáscsomagok alkalmazásonként |40 |
| Csomag mérete (minden) |KB. 195GB<sup>1</sup> |
| Maximális kezdő tevékenység mérete | 32768 karakterek<sup>2</sup> |

<sup>1</sup> blob maximális blokkméretének azure tárolási kapacitása<br />
<sup>2</sup> erőforrás fájlok és a környezeti változók

## <a name="view-batch-quotas"></a>Kötegelt kvóták megtekintése
A kötegelt fiók kvótákat megtekintése a [Azure-portálon][portal].

1. Válassza ki **Batch-fiókok** a portálon, majd válassza ki a Batch-fiók kíváncsiak vagyunk.
2. Válassza ki **tulajdonságok** a Batch-fiók menü panelen.
3. A Tulajdonságok panelen megjeleníti a **kvóták** jelenleg hozzárendelve a Batch-fiók
   
    ![Batch-fiók kvóták][account_quotas]

Batch-fiók felhasználói előfizetési módban létrehozott tekintse meg a kapcsolódó előfizetés kvóták az Azure portálon.

1. Válassza ki **előfizetések**, és válassza ki az előfizetést, a Batch-fiókot használ.

2. Az a **előfizetés** panelen válassza **használati + kvóták**.



## <a name="increase-a-quota"></a>A kvóta növeléséhez
Kövesse ezeket a lépéseket, kérje a kvóta növeléséhez a Batch-fiók, illetve az előfizetés használata a [Azure-portálon][portal]. Kvótájának növelését típusa attól függ, hogy a tárolókészlet foglalási mód a Batch-fiókhoz.

### <a name="increase-a-batch-cores-quota"></a>A kötegelt magok kvóta növelése 

Ha a Batch-fiókhoz készült **a Batch szolgáltatás** mód, kövesse ezeket a lépéseket, kérje a kötegelt magok kvóta növelése:

1. Válassza ki a **súgó + támogatás** csempét a portál irányítópultján, vagy a kérdőjel (**?**) a portál jobb felső sarkában.
2. Válassza ki **új támogatja a kérelem** > **alapjai**.
3. Az a **alapjai** panel:
   
    a. **Típusú** > **kvóta**
   
    b. Válassza ki előfizetését.
   
    c. **Kvóta típusa** > **kötegelt**
   
    d. **Támogatás megléte** > **kvóta támogatásához -**
   
    Kattintson a **Tovább** gombra.
4. Az a **probléma** panel:
   
    a. Válassza ki a **súlyossági** megfelelően a [üzletmenetre gyakorolt hatás][support_sev].
   
    b. A **részletek**, adja meg minden egyes módosítani kívánt kvótát, a Batch-fiók nevét és az új korlát.
   
    Kattintson a **Tovább** gombra.
5. Az a **elérhetőségi adatai** panel:
   
    a. Válassza ki a **elsődleges kapcsolattartási módszert**.
   
    b. Győződjön meg arról, és írja be a szükséges kapcsolattartási adatait.
   
    Kattintson a**Create** (Létrehozás) gombra a támogatási kérelem elküldéséhez.

Ha a támogatási kérelmet küldött, az Azure támogatási kapcsolatba lép Önnel. Ne feledje, hogy a kérelem befejezése telhet legfeljebb 2 munkanapos határidejűek.

### <a name="increase-a-subscription-cores-quota"></a>Egy mag előfizetési kvóta növeléséhez

Ha a Batch-fiókhoz készült **felhasználói előfizetési** módot, és át kell további területi vagy virtuális gép termékcsalád mag, a kvóta kérelem növelje az előfizetésben. Útmutató: [erőforrás-kezelő core kvóta növelése kérelmek](../azure-supportability/resource-manager-core-quotas-request.md).



## <a name="related-topics"></a>Kapcsolódó témakörök
* [Az Azure portál használata az Azure Batch-fiók létrehozása](batch-account-create-portal.md)
* [Azure Batch funkcióinak áttekintése](batch-api-basics.md)
* [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG
