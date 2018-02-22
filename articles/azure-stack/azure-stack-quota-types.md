---
title: "Kvóta típusa Azure verem |} Microsoft Docs"
description: "Tekintse át a szolgáltatásokat és erőforrásokat a Azure verem használható különböző kvóta típusok."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 8/23/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: da4105fa88848b14e5d5d0289859cfd0f85c8fee
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="quota-types-in-azure-stack"></a>Azure verem kvóta típusa

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

[Kvóták](azure-stack-plan-offer-quota-overview.md#plans) erőforrásokat, amelyek felhasználói előfizetés kiépítése, vagy felhasználhatják határait. Például a kvóta lehetővé teheti a felhasználó legfeljebb öt virtuális gépek létrehozásához. Az egyes erőforrások saját kvótatípusokat is rendelkezhet.

## <a name="compute-quota-types"></a>Számítási kvóta típusa
| **Típus** | **Alapértelmezett érték** | **Leírás** |
| --- | --- | --- |
| Virtuális gépek maximális száma | 20 | Előfizetés is ezen a helyen létrehozott virtuális gépek maximális száma. |
| Virtuális gép magok maximális száma | 50 | Előfizetés hozhat létre ezen a helyen magok maximális számának (például egy VM A3 csomag négy magok rendelkezik). |
| Rendelkezésre állási maximális száma | 10 | Ezen a helyen létrehozott rendelkezésre állási csoportok maximális számát. |
| Virtuálisgép-méretezési maximális száma | 20 | Ezen a helyen létrehozott virtuálisgép-méretezési csoportok maximális számát. |

> [!NOTE]
> Számítási kvóták nem érvényesek a technical Preview-ban.
> 
> 

## <a name="storage-quota-types"></a>Tárolási kvóta típusok
| **Elem** | **Alapértelmezett érték** | **Leírás** |
| --- | --- | --- |
| Maximális kapacitás (GB) |500 |Ezen a helyen az előfizetés által felhasználható teljes tárolási kapacitás. |
| Storage-fiókok száma összesen |20 |Előfizetés hozhat létre ezen a helyen storage-fiókok maximális száma. |

## <a name="network-quota-types"></a>Kvóta hálózattípusok
| **Elem** | **Alapértelmezett érték** | **Leírás** |
| --- | --- | --- |
| Maximális nyilvános IP-címek |50 |Nyilvános IP-cím, egy előfizetést hozhat létre ezen a helyen maximális száma. |
| Virtuális hálózatok maximális száma |50 |Előfizetés is ezen a helyen létrehozott virtuális hálózatok maximális száma. |
| Virtuális hálózati átjárók maximális száma |1 |A virtuális hálózati átjáró (VPN-átjárók), egy előfizetést hozhat létre ezen a helyen maximális száma. |
| Hálózati kapcsolatok maximális száma |2 |A hálózati kapcsolatok (point-to-point vagy pont-pont), amely egy előfizetést hozhat létre ezen a helyen az összes virtuális hálózati átjárók közötti maximális száma. |
| Maximális terheléselosztók |50 |A maximális száma a terheléselosztókat, amelyekben egy előfizetést hozhat létre ezen a helyen. |
| Hálózati adapterek maximális száma |100 |Amely egy előfizetést hozhat létre ezen a helyen hálózati adapterek maximális száma. |
| A maximális hálózati biztonsági csoportok |50 |Hálózati biztonsági csoportok, egy előfizetést hozhat létre ezen a helyen maximális száma. |

## <a name="view-an-existing-quota"></a>Egy meglévő kvóta megtekintése
1. Kattintson a **további szolgáltatások** > **erőforrás-szolgáltató**.
2. Válassza ki a szolgáltatást, amely a megtekinteni kívánt kvótát.
3. Kattintson a **kvóták**, és válassza ki a megtekinteni kívánt kvótát.

## <a name="next-steps"></a>További lépések
[Ismerje meg a csomagok, ajánlatokat, és tájékozódhat a kvóták.](azure-stack-plan-offer-quota-overview.md)

[Kvóták létrehozása terv létrehozása közben.](azure-stack-create-plan.md)
