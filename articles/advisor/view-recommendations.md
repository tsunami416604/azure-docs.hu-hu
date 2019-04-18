---
title: Önt érdeklő megtekintése az Azure Advisor-javaslatok
description: Megtekintheti és szűrheti az Azure Advisor javaslatainak zaj csökkentéséhez.
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 04/03/2019
ms.author: kasparks
ms.openlocfilehash: 9f599a63fd5f52420f1b79e769d4f7bca9683b32
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59052837"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>Önt érdeklő megtekintése az Azure Advisor-javaslatok

Az Azure Advisor biztosít az Azure-környezetek optimalizálására vonatkozó javaslatok. Belül Advisor hogy bizonyos funkciók, amelyek segítenek leszűkíteni a javaslatok azokra, amelyek az Ön számára a hozzáférést.

## <a name="configure-subscriptions-and-resource-groups"></a>Az előfizetések és -erőforráscsoportok konfigurálása

Az Advisor lehetővé teszi ki az előfizetésekhez és erőforráscsoportokhoz, amely az Ön és szervezete számára. Csak láthatja az előfizetések és erőforráscsoportok kiválasztott vonatkozó javaslatokat. Alapértelmezés szerint az összes ki van jelölve. Konfigurációs beállítások alkalmazhatja az előfizetés vagy az erőforrás csoportra, így mindenki számára, amely hozzáfér ehhez a csoporthoz vagy erőforráscsoportonként alkalmazza ugyanazokat a beállításokat. Az Azure Portalon vagy programozott módon is lehet módosítani a konfigurációs beállítások.

A módosításokat az Azure Portalon:

1. Nyissa meg [az Azure Advisor](https://aka.ms/azureadvisordashboard) az Azure Portalon.

1. Válassza ki **konfigurációs** a menüből.

   ![Az Advisor-konfiguráció menü](./media/view-recommendations/configuration.png)

1. Jelölje be a **Belefoglalás** oszlop bármely előfizetések vagy erőforráscsoportok fogadásához az Advisor-javaslatok. A box le van tiltva, ha, előfordulhat, hogy nincs engedélye, hogy a konfiguráció módosítását vagy erőforráscsoportonként benne. Tudjon meg többet [engedélyek az Azure Advisor](permissions.md).

1. Kattintson a **alkalmaz** alján, a módosítás végrehajtása után.

## <a name="filtering-your-view-in-the-azure-portal"></a>Az Azure portal nézetét szűrése

Konfigurációs beállítások marad aktív, amíg nem módosult. Ha szeretné korlátozni a nézet egyetlen megtekintésre javaslatokat, használhatja a drop olyan plusz lehetőségeket, az Advisor panel felső részén adja meg. A áttekintése, magas rendelkezésre állású, biztonsági, teljesítmény, költséget és az összes ajánlás panelek kiválaszthatja az előfizetések, erőforrástípusok és, hogy meg szeretné tekinteni a javaslat állapota.

   ![Az Advisor szűrési menü](./media/view-recommendations/filtering.png)

## <a name="dismissing-and-postponing-recommendations"></a>Elvetése és ajánlások elhalasztása

Az Azure Advisor lehetővé teszi, hogy hagyja figyelmen kívül, vagy egyetlen erőforrás-ajánlások elhalasztása. Hagyja figyelmen kívül a javaslatot, ha nem látja azt újra, ha manuálisan aktiválhatja. Azonban ajánlás elhalasztása lehetővé teszi, hogy adjon meg egy időtartamot, amely után az ajánlás automatikusan aktiválódik újra. Elhalasztása elvégezhető az Azure Portalon vagy programozott módon.

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>Az Azure Portalon egyetlen ajánlás elhalasztása 

1. Nyissa meg [az Azure Advisor](https://aka.ms/azureadvisordashboard) az Azure Portalon.
1. Válassza ki egy javaslatot a javaslatok megtekintése
1. Válasszon ki egy javaslatot a javaslatok listája
1. Válassza ki az ajánlás elhalasztása, vagy hagyja figyelmen kívül elhalasztás és az Elvetés

     ![Az Advisor szűrési menü](./media/view-recommendations/postpone-dismiss.png)

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>Elhalaszthatja vagy bezárása egy több javaslatok az Azure Portalon

1. Nyissa meg [az Azure Advisor](https://aka.ms/azureadvisordashboard) az Azure Portalon.
1. Megtekintheti az ajánlások javaslatot szabályzatkategória kiválasztása.
1. Válasszon ki egy javaslatot a javaslatok listája.
1. Jelölje be a bal oldalán a sor az összes olyan erőforrásra, halassza el, vagy hagyja figyelmen kívül a javaslatot.
1. Válassza ki **elhalasztás** vagy **Elvetés** a bal felső részén a táblában.

     ![Az Advisor szűrési menü](./media/view-recommendations/postpone-dismiss-multiple.png)

> [!NOTE]
> Hagyja figyelmen kívül, vagy egy ajánlás elhalasztása közreműködői vagy tulajdonosi engedéllyel is kell rendelkeznie. További információ az Azure Advisor engedélyeket.

> [!NOTE]
> Ha a kijelölt jelölőnégyzetek le vannak tiltva, a előfordulhat, hogy még feltöltés javaslatok. Várjon, amíg elhalaszthatja vagy bezárása előtt betölteni az összes javaslatot.

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>Elhalasztott vagy elvetett javaslat újraaktiválása

Elhalasztva vagy elvetett javaslat aktiválhatja. Ez a művelet az Azure Portalon vagy programozott módon teheti meg. Az Azure Portalon:

1. Nyissa meg [az Azure Advisor](https://aka.ms/azureadvisordashboard) az Azure Portalon.

1. Módosítsa a szűrőt az Áttekintés panelen **elhalasztott**. Az Advisor majd elhalasztott vagy elvetett javaslatok jeleníti meg.

    ![Az Advisor szűrési menü](./media/view-recommendations/activate-postponed.png)

1. Válassza ki azt a kategóriát, lásd: **elhalasztott** és **Dismissed** javaslatok.

1. Válasszon ki egy javaslatot a javaslatok listája. Ekkor megnyílik a javaslatok a **Elhalasztva & elvetett** lapon bejelölve, az erőforrásokat, amelyekre ez a javaslat elhalasztották vagy elvetett megjelenítéséhez.

1. Kattintson a **aktiválás** a sor végén található. Miután kattint, az ajánlás aktív, az adott erőforráshoz, és így eltávolítása ebből a táblázatból. Az ajánlás már látható a **aktív** fülre.
 
     ![Az Advisor szűrési menü](./media/view-recommendations/activate-postponed-2.png)

## <a name="next-steps"></a>További lépések

Ez a cikk bemutatja, hogyan megtekintheti az Önt érdeklő az Azure Advisor ajánlásait. Az Advisor kapcsolatos további információkért lásd: 

- [Mi az Azure Advisor?](advisor-overview.md)
- [Ismerkedés az Advisorral](advisor-get-started.md)
- [Az Azure Advisor engedélyek](permissions.md)



