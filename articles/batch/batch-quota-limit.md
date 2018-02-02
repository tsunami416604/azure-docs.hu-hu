---
title: "Az Azure Batch kvótái és korlátai szolgáltatás |} Microsoft Docs"
description: "További tudnivalók az alapértelmezett Azure Batch kvóták, korlátozások és megkötések-re, arról, hogyan kérhet kvóta"
services: batch
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: 
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 06445845ef8f93a06cb4b52e07108965cc3b7f8a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="batch-service-quotas-and-limits"></a>A Bach szolgáltatás kvótái és korlátozásai

Mint az egyéb Azure-szolgáltatásokkal, nincsenek korlátozások bizonyos erőforrások, a Batch szolgáltatás társított. Ezek a korlátozások számos alapértelmezett kvóták alkalmazása az Azure-ban az előfizetés vagy a fiók szintjén. Ez a cikk ismerteti azokat az alapértelmezett beállításokat, és hogyan kérheti a kvótájának növeli.

Ezek a kvóták tartsa szem előtt, közben tervezési és a Batch számítási feladatait, felskálázott. Például ha a készlet nem éri el a számítási csomópontok megadott cél száma, előfordulhat, hogy elérte a core kvótát a Batch-fiókhoz.

Több Batch számítási feladatot is futtathat egyetlen Batch-fiókon, de el is oszthatja a számítási feladatokat ugyanazon előfizetéshez, de különböző Azure-régiókhoz tartozó Batch-fiókok között.

Ha szeretné futtatni a termelési számítási feladatokhoz kötegben, szükség lehet egy vagy több, a kvóták fenti az alapértelmezett növelése érdekében. Ha azt szeretné, a kvóta emelése, az online megnyithatja [ügyfél-támogatási kérelem](#increase-a-quota) díjmentesen.

> [!NOTE]
> A kvóta legfeljebb, nem kapacitás garancia. Ha nagyméretű lemezkapacitási igényekről, forduljon az Azure támogatási szolgálatához.
> 
> 

## <a name="resource-quotas"></a>Erőforráskvóták
[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

### <a name="quotas-in-user-subscription-mode"></a>Kvótákat felhasználói előfizetési módban

Ha egy korábbi verzióját a kötegelt API Batch-fiók létrehozása az alkalmazáskészlet foglalási módban használt **felhasználói előfizetési**, kvóták alkalmazása eltérő. Ebben a módban, amely már nem ajánlott, kötegelt virtuális gépek és egyéb erőforrások jönnek létre közvetlenül az előfizetés készlet létrehozásakor. Az Azure Batch magok kvóta nem vonatkozik az ebben a módban létrehozott fiók. Ehelyett az előfizetéshez tartozó területi kvóták számítási mag, és más erőforrások alkalmazza. További információ a ezek mely százalékértékénél kéri [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](../azure-subscription-service-limits.md).

## <a name="other-limits"></a>Egyéb korlátozások
| **Erőforrás** | **Felső korlát** |
| --- | --- |
| [Egyidejű feladatok](batch-parallel-node-tasks.md) egyes számítási csomópontjain |csomópont magok száma 4 x |
| [Alkalmazások](batch-application-packages.md) / Batch-fiókhoz. |20 |
| Alkalmazáscsomagok alkalmazásonként |40 |
| Csomag mérete (minden) |Approx. 195GB<sup>1</sup> |
| Maximális kezdő tevékenység mérete | 32768 karakterek<sup>2</sup> |
| Maximális feladatütemezés élettartama | 7 nap<sup>3</sup> |

<sup>1</sup> blob maximális blokkméretének azure tárolási kapacitása<br />
<sup>2</sup> erőforrás fájlok és a környezeti változók<br />
<sup>3</sup> maximális élettartamát egy feladatot, amikor felveszik a feladat befejezését követően a rendszer 7 nap. A befejezett tevékenységek korlátlan ideig megőrződnek; a maximális élettartamon belül nem befejezett tevékenységek adatai nem elérhetőek.


## <a name="view-batch-quotas"></a>Kötegelt kvóták megtekintése
A kötegelt fiók kvótákat megtekintése a [Azure-portálon][portal].

1. Válassza ki **Batch-fiókok** a portálon, majd válassza ki a Batch-fiók kíváncsiak vagyunk.
2. Válassza ki **kvóták** a Batch-fiók menüben.
3. A Batch-fiókhoz az aktuálisan érvényes kvóták megtekintése
   
    ![Batch-fiók kvóták][account_quotas]



## <a name="increase-a-quota"></a>A kvóta növeléséhez
Kövesse ezeket a lépéseket, kérje a kvóta növeléséhez a Batch-fiók, illetve az előfizetés használata a [Azure-portálon][portal]. Kvótájának növelését típusa attól függ, hogy a tárolókészlet foglalási mód a Batch-fiókhoz.

### <a name="increase-a-batch-cores-quota"></a>A kötegelt magok kvóta növelése 

1. Válassza ki a **súgó + támogatás** csempét a portál irányítópultján, vagy a kérdőjel (**?**) a portál jobb felső sarkában.
2. Válassza ki **új támogatja a kérelem** > **alapjai**.
3. A **alapjai**:
   
    a. **Típusú** > **kvóta**
   
    b. Válassza ki előfizetését.
   
    c. **Kvóta típusa** > **kötegelt**
   
    d. **Támogatás megléte** > **kvóta támogatásához -**
   
    Kattintson a **Tovább** gombra.
4. A **probléma**:
   
    a. Válassza ki a **súlyossági** megfelelően a [üzletmenetre gyakorolt hatás][support_sev].
   
    b. A **részletek**, adja meg minden egyes módosítani kívánt kvótát, a Batch-fiók nevét és az új korlát.
   
    Kattintson a **Tovább** gombra.
5. A **elérhetőségi adatai**:
   
    a. Válassza ki a **elsődleges kapcsolattartási módszert**.
   
    b. Győződjön meg arról, és írja be a szükséges kapcsolattartási adatait.
   
    Kattintson a**Create** (Létrehozás) gombra a támogatási kérelem elküldéséhez.

Ha a támogatási kérelmet küldött, az Azure támogatási kapcsolatba lép Önnel. Ne feledje, hogy a kérelem befejezése telhet legfeljebb 2 munkanapos határidejűek.


## <a name="related-topics"></a>Kapcsolódó témakörök
* [Az Azure portál használata az Azure Batch-fiók létrehozása](batch-account-create-portal.md)
* [Azure Batch funkcióinak áttekintése](batch-api-basics.md)
* [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
