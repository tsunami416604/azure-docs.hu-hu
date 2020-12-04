---
title: Szószedet jelentés az adatairól a hatáskörébe-elemzések használatával
description: Ez a útmutató ismerteti, hogyan tekinthetők meg és használhatók a hatáskörébe tartozó elemzések az adatairól.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: f61d99a61cb50886d70489b586d948bfa751e196
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576774"
---
# <a name="glossary-insights-on-your-data-in-azure-purview"></a>Az Azure hatáskörébe tartozó adataival kapcsolatos Szószedet

Ez a útmutató ismerteti, hogyan lehet hozzáférni, megtekinteni és szűrni a hatáskörébe tartozó Szószedeti jelentéseket az adataihoz.

Ebben a útmutatóban megismerheti a következőket:

> [!div class="checklist"]
> - Keresse meg a hatáskörébe tartozó fiókját
> - A madár szem nézetének beolvasása az adatairól

## <a name="prerequisites"></a>Előfeltételek

A hatáskörébe tartozó ismeretek megismerése előtt győződjön meg arról, hogy végrehajtotta a következő lépéseket:

- Állítsa be az Azure-erőforrásokat, és töltse fel a fiókot adatokkal

- A forrás típusának beállítása és a vizsgálat befejezése

- Szószedet beállítása és eszközök csatolása a Szószedet feltételeihez

További információ: [adatforrások kezelése az Azure hatáskörébe (előzetes verzió)](manage-data-sources.md).

## <a name="use-purview-glossary-insights"></a>A hatáskörébe tartozó Szószedet-felismerések használata

Az Azure hatáskörébe tartozik a Szószedet feltétele, és csatolható az eszközökhöz. Később megtekintheti a Szószedet-eloszlást a Szószedetbeli áttekintésekben. Ez azt jelzi, hogy a Szószedet állapota az eszközökhöz csatolt feltételek szerint van. Emellett az állapot és a szerepkörök eloszlása alapján is megadja a feltételeket a felhasználók száma szerint.

**A Szószedet-áttekintések megtekintéséhez:**

1. Nyissa meg a Azure Portal **Azure hatáskörébe** tartozó [példány képernyőjét](https://aka.ms/purviewportal) , és válassza ki a hatáskörébe tartozó fiókot.

1. Az **Áttekintés** lap első **lépések** szakaszában válassza a hatáskörébe tartozó fiók **indítása** csempét.

   :::image type="content" source="./media/glossary-insights/portal-access.png" alt-text="A hatáskörébe tartozó Azure Portal elindítása":::

1. A hatáskörébe tartozó **kezdőlapon** válassza az eredmények **megtekintése** csempét az információhoz való hozzáféréshez **Insights** :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: .

   :::image type="content" source="./media/glossary-insights/view-insights.png" alt-text="Tekintse meg az Azure Portal":::

1. Az **Áttekintés** :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: területen válassza a **Szószedet** lehetőséget a hatáskörébe tartozó **Szószedet** -információk jelentés megjelenítéséhez.

A **Glosszárium** -információk oldal a következő területeket jeleníti meg:
1. **Magas szintű KPI** -k a Szószedet feltételeit és a katalógus felhasználóinak megjelenítéséhez

2. A **legfontosabb Szószedet-használati feltételek és az eszközök száma** az 5. számú szószedetet mutatja a hozzájuk csatolt eszközökkel. Minden egyéb eszköz a gráf "other" kategóriájában szerepel.

3. A **Szószedet feltételek szerinti állapota szerint** a szószedetben szereplő kifejezések állapota például a "draft", a "jóváhagyva", a "riasztás" és a "lejárt". 

1. Mutasson rá, vagy kattintson a gráf szeletére egy állapottal, és jegyezze fel az adott állapotú kifejezések számát.

1. **A szerepköröknek a felhasználók száma alapján történő eloszlása** a szerepkörben lévő felhasználók száma szerint osztja meg a szerepkörök eloszlását.

   :::image type="content" source="./media/glossary-insights/glossary-insights1.png" alt-text="Szószedet-áttekintések megtekintése":::

## <a name="next-steps"></a>További lépések

További információ az Azure-beli felügyeleti jelentésekről az [Asset](./asset-insights.md) -elemzések segítségével