---
title: Az Ön számára fontos Azure Advisor-javaslatok megtekintése
description: Azure Advisor javaslatok megtekintése és szűrése a zaj csökkentése érdekében.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 10d7b16864f8e449dc51e870c5ff9f20d8c0dc87
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75422374"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>Az Ön számára fontos Azure Advisor-javaslatok megtekintése

Azure Advisor ajánlásokat nyújt az Azure-beli üzembe helyezések optimalizálásához. Az Advisoron belül néhány olyan szolgáltatáshoz férhet hozzá, amelyek segítségével leszűkítheti javaslatait, hogy csak az Ön számára legyen szó.

## <a name="configure-subscriptions-and-resource-groups"></a>Előfizetések és erőforráscsoportok konfigurálása

Az Advisor lehetővé teszi az Ön és a szervezete számára fontos előfizetések és erőforráscsoportok kiválasztását. Csak a kiválasztott előfizetésekre és erőforrás-csoportokra vonatkozó javaslatokat láthat. Alapértelmezés szerint az összes ki van választva. A konfigurációs beállítások az előfizetés vagy az erőforráscsoport esetében érvényesek, így ugyanazok a beállítások érvényesek az adott előfizetéshez vagy erőforráscsoporthoz hozzáférő mindenkire. A konfigurációs beállítások a Azure Portalban vagy programozott módon módosíthatók.

A Azure Portal módosításához tegye a következőket:

1. Nyissa meg [Azure Advisor](https://aka.ms/azureadvisordashboard) a Azure Portalban.

1. Válassza a **konfiguráció** lehetőséget a menüből.

   ![Advisor konfigurációs menü](./media/view-recommendations/configuration.png)

1. Az Advisor-javaslatok fogadásához jelölje be a **Belefoglalás** oszlopban található bármelyik előfizetéshez vagy erőforráscsoporthoz tartozó jelölőnégyzetet. Ha a mező le van tiltva, lehet, hogy nincs engedélye arra, hogy konfigurációs módosítást hajtson végre az adott előfizetésen vagy az erőforrás-csoportban. További információ a [Azure Advisor engedélyeiről](permissions.md).

1. A módosítás után kattintson a lenti **alkalmazás** gombra.

## <a name="filtering-your-view-in-the-azure-portal"></a>A nézet szűrése a Azure Portalban

A konfigurációs beállítások addig maradnak aktívak, amíg meg nem változnak Ha korlátozni szeretné a javaslatok megtekintését egyetlen megtekintésre, az Advisor panel felső részén található legördülő menüket használhatja. Az áttekintés, a magas rendelkezésre állás, a biztonság, a teljesítmény, a Cost és az összes javaslat panel közül kiválaszthatja a megtekinteni kívánt előfizetéseket, erőforrás-típusokat és ajánlási állapotot.

   ![Advisor-szűrés menü](./media/view-recommendations/filtering.png)

## <a name="dismissing-and-postponing-recommendations"></a>Elutasított és Elhalasztott javaslatok

Azure Advisor lehetővé teszi, hogy egyetlen erőforráson utasítsa el vagy elhalasztsa az ajánlásokat. Ha elveti a javaslatot, nem jelenik meg újra, hacsak manuálisan nem aktiválja. A javaslatok elhalasztása azonban lehetővé teszi, hogy megadhatja azt az időtartamot, amely után az ajánlás automatikusan aktiválódik. Az elhalasztás a Azure Portalon vagy programozott módon végezhető el.

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>Egyetlen javaslat elhalasztása a Azure Portal 

1. Nyissa meg [Azure Advisor](https://aka.ms/azureadvisordashboard) a Azure Portalban.
1. A javaslatok megtekintéséhez válasszon ki egy ajánlási kategóriát
1. Válasszon ki egy javaslatot a javaslatok listájáról
1. Válassza az elhalasztás vagy az Elvetés lehetőséget az elhalasztani vagy elhagyni kívánt javaslathoz

     ![Advisor-szűrés menü](./media/view-recommendations/postpone-dismiss.png)

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>Több javaslat elhalasztása vagy elvetése a Azure Portal

1. Nyissa meg [Azure Advisor](https://aka.ms/azureadvisordashboard) a Azure Portalban.
1. A javaslatok megtekintéséhez válasszon egy ajánlási kategóriát.
1. Válasszon ki egy javaslatot a javaslatok listájáról.
1. Jelölje be a jelölőnégyzetet a sor bal oldalán az összes olyan erőforrásnál, amelyet el szeretne halasztani, vagy utasítsa el a javaslatot.
1. Válassza az **elhalasztás** vagy az **Elvetés** lehetőséget a tábla bal felső részén.

     ![Advisor-szűrés menü](./media/view-recommendations/postpone-dismiss-multiple.png)

> [!NOTE]
> A javaslatok elvetéséhez vagy elhalasztásához jogosultsággal kell rendelkeznie. További információ a Azure Advisor engedélyeiről.

> [!NOTE]
> Ha a kiválasztási mezők le vannak tiltva, a javaslatok továbbra is betölthetők. Várjon, amíg a rendszer betölti az összes javaslatot, mielőtt elkezdené az elhalasztását vagy elvetését.

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>Elhalasztott vagy elutasított javaslat újraaktiválása

Aktiválhat egy elhalasztott vagy elutasított javaslatot. Ezt a műveletet a Azure Portal vagy programozott módon végezheti el. Az Azure Portalon:

1. Nyissa meg [Azure Advisor](https://aka.ms/azureadvisordashboard) a Azure Portalban.

1. Módosítsa a szűrőt az Áttekintés panelen az **Elhalasztáshoz**. Az Advisor ezután megjeleníti az elhalasztott vagy elhagyott javaslatokat.

    ![Advisor-szűrés menü](./media/view-recommendations/activate-postponed.png)

1. Válasszon ki egy kategóriát az **elhalasztott** és **elutasított** javaslatok megtekintéséhez.

1. Válasszon ki egy javaslatot a javaslatok listájáról. Ez megnyit egy javaslatot az **elhalasztott & elhagyott** lapról, hogy megjelenjenek azok az erőforrások, amelyekre ez az ajánlás el lett halasztott vagy el lett vetve.

1. Kattintson az **aktiválás** gombra a sor végén. Ha rákattintott, a javaslat aktív lesz az adott erőforrás esetében, ezért el lett távolítva ebből a táblából. A javaslat már látható az **aktív** lapon.
 
     ![Advisor-szűrés menü](./media/view-recommendations/activate-postponed-2.png)

## <a name="next-steps"></a>További lépések

Ez a cikk azt ismerteti, hogyan tekintheti meg a Azure Advisorban lévő ajánlásokat. Az Advisor szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következőt: 

- [Mi az Azure Advisor?](advisor-overview.md)
- [Első lépések Advisortal](advisor-get-started.md)
- [Engedélyek Azure Advisor](permissions.md)



