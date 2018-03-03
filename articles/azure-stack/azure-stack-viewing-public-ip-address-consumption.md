---
title: "Tekintse meg nyilvános IP-cím fogyasztás Azure verem |} Microsoft Docs"
description: "A rendszergazdák megtekinthetik a nyilvános IP-címek fogyasztás régióban"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 0f77be49-eafe-4886-8c58-a17061e8120f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 50bf01d6de6105d3041c6bb88e803f3d110f751d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="view-public-ip-address-consumption-in-azure-stack"></a>Nyilvános IP-cím fogyasztás Azure verem megtekintése

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Felhő rendszergazdaként tekintheti meg:
 - Nyilvános IP-címek a bérlők számára lefoglalt száma.
 - Nyilvános IP-címek, amelyek továbbra is elérhető a foglalási száma.
 - A százalékos nyilvános IP-címek, amelyek az adott helyre került kiosztásra.

A **nyilvános IP-címet a készletbe használati** csempe nyilvános IP-címkészletek között használt nyilvános IP-címek számát jeleníti meg. IP-címek a a csempe az infrastruktúra-szolgáltatási virtuális gép bérlői használatát mutatja a példányok, a háló infrastruktúra-szolgáltatásokat és a bérlők által explicit módon létrehozott nyilvános IP-cím erőforrás.

A csempe célja, hogy Azure verem bizonyos értelemben az ezen a helyen használt nyilvános IP-címek számának. A szám segítségével a rendszergazda határozza meg, hogy azok kevés ezen az erőforráson.

A **nyilvános IP-címek** menüpont alatt **bérlői erőforrások** csak ezen nyilvános IP-címek listája, amelyek lettek *explicit módon a bérlők által létrehozott*. A menüpont megtalálhatja a **erőforrás-szolgáltató**, **hálózati** ablaktáblán. Száma **használható** nyilvános IP-címek a a **nyilvános IP-címet a készletbe használati** csempe nem mindig azonos a (nagyobb) a száma a **nyilvános IP-címek** acsempe **A bérlői erőforrások**.

## <a name="view-the-public-ip-address-usage-information"></a>A nyilvános IP-címek használati adatait megtekintése
Nyilvános IP-címek, amelyek a régió felhasznált teljes számának megtekintése:

1. Válassza ki a verem Azure felügyeleti portálon **további szolgáltatások**a **felügyeleti erőforrások**, jelölje be **erőforrás-szolgáltató**.
2. A listájáról **erőforrás-szolgáltató**, jelölje be **hálózati**.
3. A **hálózati** ablaktáblán megjelennek azok a **nyilvános IP-címet a készletbe használati** csempére a **áttekintése** szakasz.

![Hálózati erőforrás-szolgáltató ablaktábla](media/azure-stack-viewing-public-ip-address-consumption/image01.png)

A **használható** szám számát jelenti, a nyilvános IP-címek hozzárendelve a nyilvános IP-címkészletet. A **szabad** szám azt jelzi, a nyilvános IP-címek számának nyilvános IP-cím a címkészlet, amely nincs hozzárendelve, és továbbra is elérhetők. A **(%) értékét** számot jelöli a számát, használt vagy címek hozzárendelve a nyilvános IP-címkészletet azon a helyen a nyilvános IP-címek száma százalékában.

## <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>A nyilvános IP-címet, amelyet bérlői előfizetések megtekintése
Válassza ki **nyilvános IP-címek** alatt **bérlői erőforrások**. Tekintse át a bérlői előfizetések egy adott régióban explicit módon létrehozott nyilvános IP-címek listáját.

![Bérlő nyilvános IP-címek](media/azure-stack-viewing-public-ip-address-consumption/image02.png)

Bizonyára észrevette, hogy, hogy néhány nyilvános IP-címeket dinamikusan lefoglalt szerepelnek a listán. Azonban egy címet még nem hozzájuk tartozó még. A cím erőforrás létrejött, a hálózati erőforrás-szolgáltató, de még nem található a hálózati vezérlő.

A hálózati vezérlő nem egy címet rendel az erőforrás csak illesztőfelület, a hálózati kártya (NIC), a terheléselosztó vagy a virtuális hálózati átjáró is kötődik. Ha a nyilvános IP-cím illesztőfelület van kötve, a hálózati vezérlő IP-címet foglal le. A cím jelenik meg a **cím** mező.

## <a name="view-the-public-ip-address-information-summary-table"></a>A nyilvános IP-cím információk összefoglaló táblázata megtekintése
Más esetekben nyilvános IP-címek vannak társítva, amelyek meghatározzák, hogy a cím jelenik meg egy lista vagy egy másik.

| **Nyilvános IP-cím hozzárendelés eset** | **Megjelenik a használatának összegzése** | **A bérlő nyilvános IP-címeket tartalmazó listájában jelenik meg** |
| --- | --- | --- |
| Még nem rendelt (átmeneti) egy hálózati adapter vagy terheléselosztó dinamikus nyilvános IP-cím |Nem |Igen |
| Dinamikus nyilvános IP-cím egy hálózati adapter vagy terheléselosztó rendelt. |Igen |Igen |
| Statikus nyilvános IP-címet hozzárendelni a bérlő hálózati adapter vagy terheléselosztó. |Igen |Igen |
| Statikus nyilvános IP-címet hozzárendelni a háló infrastruktúra-szolgáltatási végpont. |Igen |Nem |
| Nyilvános IP-cím implicit módon létrehozott IaaS Virtuálisgép-példányok és használt kimenő forgalmat kezelő NAT a virtuális hálózaton. Amikor a bérlő hoz létre egy Virtuálisgép-példány, hogy a virtuális gépek képes információkat küldeni Internet ezek jönnek létre a háttérben. |Igen |Nem |

## <a name="next-steps"></a>További lépések
[A verem az Azure Storage-fiókok kezelése](azure-stack-manage-storage-accounts.md)