---
title: Az Azure Stackben kvótatípusok |} A Microsoft Docs
description: Tekintse át a különböző kvótatípusok elérhető szolgáltatásokat és erőforrásokat az Azure Stackben.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/08/2018
ms.author: sethm
ms.reviewer: xiaofmao
ms.openlocfilehash: ec84c87e047d6426d24ef339d1247c6cb53954dd
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2018
ms.locfileid: "51299419"
---
# <a name="quota-types-in-azure-stack"></a>Az Azure Stackben kvótatípusok

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

[Kvóták](azure-stack-plan-offer-quota-overview.md#plans) erőforrásokat, amelyeket a felhasználói előfizetés is üzembe helyezése és felhasználása a vonatkozó korlátok beállításához. Például kvóták lehetővé teheti a felhasználó legfeljebb öt virtuális gépek létrehozásához. Az egyes erőforrások a saját kvótatípusokat is rendelkezhet.

## <a name="compute-quota-types"></a>COMPUTE kvótatípusok 
| **Típus** | **Alapértelmezett érték** | **Leírás** |
| --- | --- | --- |
| Virtuális gépek maximális száma | 50 | Egy előfizetést hozhat létre ezen a helyen lévő virtuális gépek maximális számát. |
| Virtuálisgép-magok maximális száma | 100 | Egy előfizetést hozhat létre ezen a helyen lévő magok maximális számának (például egy a3-as virtuális gép rendelkezik négy magot). |
| Maximális száma a rendelkezésre állási csoportok | 10 | Ezen a helyen létrehozott rendelkezésre állási csoportok maximális száma. |
| Maximális száma virtuálisgép-méretezési csoportok | 100 | Ezen a helyen létrehozott virtuális gép méretezési csoportok maximális száma. |

## <a name="storage-quota-types"></a>Tárolási kvóta típusa 
| **Elem** | **Alapértelmezett érték** | **Leírás** |
| --- | --- | --- |
| Maximális kapacitás (GB) |2048 |Ezen a helyen az előfizetés által felhasználható teljes tárolási kapacitást. |
| Tárfiókok teljes száma |20 |Egy előfizetést hozhat létre ezen a helyen storage-fiókok maximális számát. |

> [!NOTE]  
> Mielőtt a tárhelykvótát van érvényben akár két óráig is eltarthat.


## <a name="network-quota-types"></a>Hálózati kvótatípusok
| **Elem** | **Alapértelmezett érték** | **Leírás** |
| --- | --- | --- |
| Maximális nyilvános IP-címek |50 |Egy előfizetést hozhat létre ezen a helyen nyilvános IP-címek maximális számát. |
| Virtuális hálózatok maximális száma |50 |Ezen a helyen előfizetést hozhat létre virtuális hálózatok maximális száma. |
| Max. virtuális hálózati átjárók |1 |Virtuális hálózati átjárók (VPN-átjárók), amely egy előfizetést hozhat létre ezen a helyen maximális számát. |
| Hálózati kapcsolatok maximális száma |2 |A maximális hálózati kapcsolatok száma (point-to-point vagy site-to-site), amely egy előfizetést hozhat létre ezen a helyen az összes virtuális hálózati átjáró között. |
| Maximális terheléselosztók |50 |Egy előfizetést hozhat létre ezen a helyen terheléselosztók maximális számát. |
| Hálózati adapterek maximális száma |100 |A hálózati adapterek számára olyan előfizetést hozhat létre ezen a helyen maximális számát. |
| Maximális hálózati biztonsági csoportok |50 |Egy előfizetést hozhat létre ezen a helyen hálózati biztonsági csoportok maximális száma. |

## <a name="view-an-existing-quota"></a>Egy meglévő kvóta megtekintése
1. Keresse meg az alapértelmezett irányítópult, a felügyeleti portálon, a **erőforrás-szolgáltatók** csempére.
2. Válassza ki a szolgáltatást, hogy meg szeretné tekinteni, például a kvótához **számítási** vagy **tárolási**.
3. Válassza ki **kvóták**, majd válassza ki a megtekinteni kívánt kvótát.


## <a name="edit-a-quota"></a>Kvóta szerkesztése  
Dönthet úgy, hogy az eredeti konfiguráció helyett a kvóta szerkesztése [egy kiegészítő csomag használatával](create-add-on-plan.md). Ha szerkeszti a kvótát, az új konfigurációt automatikusan globálisan érvényes a kvóta használó összes csomag és az összes meglévő előfizetéseket, amelyek ezeket a csomagokat. A kvóta szerkesztése eltér attól, hogy ha használ egy kiegészítő csomagot adjon meg egy módosított kvóta, amely egy felhasználó úgy dönt, hogy az előfizetés. 

### <a name="to-edit-a-quota"></a>Kvóta szerkesztése  
1. Keresse meg az alapértelmezett irányítópult, a felügyeleti portálon, a **erőforrás-szolgáltatók** csempére.
2. Válassza ki a szolgáltatást, amely a módosítandó, például a kvótához **számítási**, **hálózati**, vagy **tárolási**.
3. Majd **kvóták**, majd válassza ki a módosítani kívánt kvótát.
4. Az a **kvóták beállítása** panelen módosítsa értékeit, és válassza **mentése**. 

Az új értékekkel kvóta globálisan vonatkoznak, a módosított kvóta használó kívül minden csomagra, és minden meglévő előfizetésekhez, amelyek ezeket a csomagokat. 



## <a name="next-steps"></a>További lépések

- [Ismerje meg, további információk csomagok, ajánlatok és kvóták.](azure-stack-plan-offer-quota-overview.md)
- [Kvóták létrehozása a csomag létrehozásakor.](azure-stack-create-plan.md)
