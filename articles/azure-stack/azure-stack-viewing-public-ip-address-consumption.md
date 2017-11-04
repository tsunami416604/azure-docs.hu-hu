---
title: "Tekintse meg nyilvános IP-cím fogyasztás Azure verem |} Microsoft Docs"
description: "A rendszergazdák megtekinthetik a nyilvános IP-címek fogyasztás régióban"
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: darmour
editor: 
ms.assetid: 0f77be49-eafe-4886-8c58-a17061e8120f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: scottnap
ms.openlocfilehash: 7651565eebf6272f307a4ce4790ca19b41bfa826
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="view-public-ip-address-consumption-in-azure-stack"></a>Nyilvános IP-cím fogyasztás Azure verem megtekintése

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Felhő rendszergazdaként Megtekintheti a nyilvános IP-címek a bérlők számára, a nyilvános IP-címet, amely kiosztható továbbra is elérhetők, és a nyilvános IP-címek, amelyek az adott helyre került kiosztásra százalékos rendelt.

A **nyilvános IP-címet a készletbe használati** csempe kellett volna használni az összes nyilvános IP-címkészletet a hálón között nyilvános IP-címek teljes számát jeleníti meg, hogy IaaS Virtuálisgép-példányok, bérlő hálóinfrastruktúrában használja-e szolgáltatások, vagy explicit módon bérlők által létrehozott nyilvános IP-cím erőforrás.

Ez a csempe célja segítségével Azure verem rendszergazdák egyfajta kellett volna használni az ezen a helyen nyilvános IP-címek teljes száma. Ez segít meghatározni, hogy azok kevés erőforrás rendszergazdák.

A a **erőforrás-szolgáltató**, **hálózati** panelen a **nyilvános IP-címek** menüpont alatt **bérlői erőforrások** csak azokat a nyilvános listája IP-címek, amelyek lettek *explicit módon a bérlők által létrehozott*. Így a száma **használható** nyilvános IP-címek a a **nyilvános IP-címet a készletbe használati** csempe nem mindig azonos a (nagyobb) a száma a **nyilvános IP-címek** csempe a **bérlői erőforrások**.

## <a name="view-the-public-ip-address-usage-information"></a>A nyilvános IP-címek használati adatait megtekintése
Nyilvános IP-címek, amelyek a régió felhasznált teljes számának megtekintése:

1. A verem Azure felügyeleti portálon kattintson **további szolgáltatások**a **felügyeleti erőforrások**, kattintson a **erőforrás-szolgáltató**.
2. A listájáról **erőforrás-szolgáltató**, jelölje be **hálózati**.
3. A **hálózati** csempe megjeleníti a **nyilvános IP-címet a készletbe használati** csempére a **áttekintése** szakasz.

![Hálózati erőforrás-szolgáltató panel](media/azure-stack-viewing-public-ip-address-consumption/image01.png)

A következőket kell figyelembe venni, hogy a **használható** számát jelenti. a nyilvános IP-címek száma az összes nyilvános IP-címkészletet azon a helyen rendelt. A **szabad** szám azt jelzi, a nyilvános IP-címek számának minden nyilvános IP-cím a címkészlet, amely nincs hozzárendelve, és továbbra is elérhetők. A **(%) értékét** számot jelöli a számát, használt vagy címek hozzárendelve a nyilvános IP-címeket az összes nyilvános IP-címkészletet azon a helyen lévő száma százalékában.

## <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>A nyilvános IP-címet, amelyet bérlői előfizetések megtekintése
Nyilvános IP-címek egy adott régióban bérlői előfizetések explicit módon létrehozott listájának megtekintéséhez kattintson **nyilvános IP-címek** alatt **bérlői erőforrások**.

![Bérlő nyilvános IP-címek](media/azure-stack-viewing-public-ip-address-consumption/image02.png)

Bizonyára észrevette, hogy, hogy néhány nyilvános IP-címeket dinamikusan lefoglalt szerepelnek a listán, de nincs társítva még címe. Ennek oka az, a cím erőforrás még jött létre a hálózati erőforrás-szolgáltató, de nem a hálózati vezérlő.

A hálózati vezérlő nem címet hozzárendelni az erőforráshoz való csak illesztőfelület, a hálózati kártya (NIC), a terheléselosztó vagy a virtuális hálózati átjáró ténylegesen kötött. Ha a nyilvános IP-cím egy adapterhez van kötve, a hálózati vezérlő osztja ki az IP-címet, és megjelenik a **cím** mező.

## <a name="view-the-public-ip-address-information-summary-table"></a>A nyilvános IP-cím információk összefoglaló táblázata megtekintése
Számos nyilvános IP-címek vannak társítva, amelyek meghatározzák, hogy a cím jelenik meg egy lista vagy egy másik különbözik azoktól.

| **Nyilvános IP-cím hozzárendelés eset** | **Megjelenik a használatának összegzése** | **A bérlő nyilvános IP-címeket tartalmazó listájában jelenik meg** |
| --- | --- | --- |
| Még nem rendelt (átmeneti) egy hálózati adapter vagy terheléselosztó dinamikus nyilvános IP-cím |Nem |Igen |
| Dinamikus nyilvános IP-cím egy hálózati adapter vagy terheléselosztó rendelt. |Igen |Igen |
| Statikus nyilvános IP-címet hozzárendelni a bérlő hálózati adapter vagy terheléselosztó. |Igen |Igen |
| Statikus nyilvános IP-címet hozzárendelni a háló infrastruktúra-szolgáltatási végpont. |Igen |Nem |
| Nyilvános IP-cím implicit módon létrehozott IaaS Virtuálisgép-példányok és használt kimenő forgalmat kezelő NAT a virtuális hálózaton. Amikor a bérlő hoz létre egy Virtuálisgép-példány, hogy a virtuális gépek képes információkat küldeni Internet ezek jönnek létre a háttérben. |Igen |Nem |

## <a name="next-steps"></a>Következő lépések
[A verem az Azure Storage-fiókok kezelése](azure-stack-manage-storage-accounts.md)