---
title: Az Azure Advisor-javaslatok megtekintése, amelyek fontosak az Ön számára
description: Tekintse meg és szűrje az Azure Advisor-javaslatokat a zaj csökkentése érdekében.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 10d7b16864f8e449dc51e870c5ff9f20d8c0dc87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422374"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>Az Azure Advisor-javaslatok megtekintése, amelyek fontosak az Ön számára

Az Azure Advisor javaslatokat nyújt az Azure-telepítések optimalizálásához. Az Advisoron belül hozzáférhet néhány olyan funkcióhoz, amelyek segítenek leszűkíteni a javaslatokat csak azokra, amelyek fontosak önnek.

## <a name="configure-subscriptions-and-resource-groups"></a>Előfizetések és erőforráscsoportok konfigurálása

Az Advisor lehetővé teszi az Ön és szervezete számára fontos előfizetések és erőforráscsoportok kiválasztását. Csak a kiválasztott előfizetésekhez és erőforráscsoportokhoz vonatkozó javaslatok jelennek meg. Alapértelmezés szerint az összes ki van jelölve. A konfigurációs beállítások az előfizetésre vagy az erőforráscsoportra vonatkoznak, így ugyanazok a beállítások vonatkoznak mindenkire, aki hozzáfér az adott előfizetéshez vagy erőforráscsoporthoz. A konfigurációs beállítások módosíthatók az Azure Portalon vagy programozott módon.

Módosítások végrehajtása az Azure Portalon:

1. Nyissa meg az [Azure Advisort](https://aka.ms/azureadvisordashboard) az Azure Portalon.

1. Válassza a menü **Konfiguráció** parancsát.

   ![Tanácsadó konfigurációs menüje](./media/view-recommendations/configuration.png)

1. Jelölje be a **Belefoglalás** oszlopban az esetleges előfizetések vagy erőforráscsoportok számára az Advisor-javaslatok fogadásához. Ha a mező le van tiltva, előfordulhat, hogy nincs engedélye az adott előfizetés vagy erőforráscsoport konfigurációs módosítására. További információ [az Azure Advisor engedélyeiről.](permissions.md)

1. A módosítás után kattintson az **Alkalmaz** gombra alul.

## <a name="filtering-your-view-in-the-azure-portal"></a>A nézet szűrése az Azure Portalon

A konfigurációs beállítások a módosításig aktívak maradnak. Ha korlátozni szeretné az egyetlen megtekintésre vonatkozó javaslatok nézetét, használhatja az Advisor panel tetején található legördülő listaokat. Az Áttekintés, a Magas rendelkezésre állás, a Biztonság, a Teljesítmény, a Költség és az Összes javaslat panelen kiválaszthatja a megtekinteni kívánt előfizetések, erőforrástípusok és javaslati állapotot.

   ![Tanácsadó szűrési menüje](./media/view-recommendations/filtering.png)

## <a name="dismissing-and-postponing-recommendations"></a>Ajánlások elvetése és elállása

Az Azure Advisor lehetővé teszi, hogy egyetlen erőforrásra vonatkozó javaslatokat utasítsa el vagy halassza el. Ha elutasít egy javaslatot, csak akkor látja azt újra, ha manuálisan aktiválja azt. A javaslat elhancesztálása azonban lehetővé teszi, hogy megadja azt az időtartamot, amely után a javaslat automatikusan újra aktiválódik. A postolás elvégezhető az Azure Portalon vagy programozott módon.

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>Egyetlen javaslat elhalasztása az Azure Portalon 

1. Nyissa meg az [Azure Advisort](https://aka.ms/azureadvisordashboard) az Azure Portalon.
1. Válasszon egy ajánlási kategóriát a javaslatok megtekintéséhez
1. Válasszon egy ajánlást az ajánlások listájából
1. Válassza az Elhalasztás vagy a Elvetés lehetőséget az elhalasztani vagy elutasítani kívánt javaslathoz.

     ![Tanácsadó szűrési menüje](./media/view-recommendations/postpone-dismiss.png)

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>Több javaslat elhalasztása vagy elvetése az Azure Portalon

1. Nyissa meg az [Azure Advisort](https://aka.ms/azureadvisordashboard) az Azure Portalon.
1. Válasszon ki egy javaslatkategóriát a javaslatok megtekintéséhez.
1. Válasszon egy javaslatot a javaslatok listájából.
1. Jelölje be a sor bal oldalán lévő jelölőnégyzetet az összes elhalasztani kívánt erőforráshoz, vagy utasítsa el a javaslatot.
1. Válassza a **Halasztás** vagy a **Elvetés** lehetőséget a táblázat bal felső részén.

     ![Tanácsadó szűrési menüje](./media/view-recommendations/postpone-dismiss-multiple.png)

> [!NOTE]
> Közreműködői vagy tulajdonosi engedélyre van szüksége egy javaslat elvetéséhez vagy elhalasztásához. További információ az Azure Advisor engedélyeiről.

> [!NOTE]
> Ha a kijelölőmezők le vannak tiltva, előfordulhat, hogy a javaslatok továbbra is betöltődnek. Várjon, amíg az összes javaslat betöltődik, mielőtt megpróbálna elhalasztani vagy elutasítani.

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>Elhalasztott vagy elutasított javaslat újraaktiválása

Aktiválhat egy elhalasztott vagy elutasított javaslatot. Ez a művelet az Azure Portalon vagy programozott módon végezhető el. Az Azure Portalon:

1. Nyissa meg az [Azure Advisort](https://aka.ms/azureadvisordashboard) az Azure Portalon.

1. Módosítsa az Áttekintés panel szűrőjét **Elhalasztott**ra. Az Advisor ezután megjeleníti az elhalasztott vagy elutasított ajánlásokat.

    ![Tanácsadó szűrési menüje](./media/view-recommendations/activate-postponed.png)

1. Jelöljön ki egy **kategóriát az Elhalasztott** és elutasított javaslatok **megtekintéséhez.**

1. Válasszon egy javaslatot a javaslatok listájából. Ezzel megnyitom a javaslatokat, ha az **Elhalasztott & elvetése** lapon már be van jelölve, hogy megjelenítse azokat az erőforrásokat, amelyek esetében ez a javaslat ellett halasztva vagy elvetve.

1. Kattintson az **Aktiválás gombra** a sor végén. Miután rákattintott, a javaslat aktív az adott erőforrás, és így eltávolították ebből a táblából. A javaslat most már látható az **Aktív** lapon.
 
     ![Tanácsadó szűrési menüje](./media/view-recommendations/activate-postponed-2.png)

## <a name="next-steps"></a>További lépések

Ebből a cikkből megtudhatja, hogyan tekintheti meg az Azure Advisorban fontos javaslatokat. Ha többet szeretne megtudni az Advisorról, olvassa el az: 

- [Mi az Azure Advisor?](advisor-overview.md)
- [Első lépések az Advisorral](advisor-get-started.md)
- [Engedélyek az Azure Advisorban](permissions.md)



