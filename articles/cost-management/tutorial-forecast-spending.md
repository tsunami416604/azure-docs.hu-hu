---
title: "Felmérheti a kiadásokat az Azure költség Management szolgáltatással |} Microsoft Docs"
description: "Felmérheti a korábbi használati használ, és az adatok költségeik kiadásokat."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 10/11/2017
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: e0b4bc196786ca4dfa3d57f3e89dd36ddc32acbc
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2017
---
# <a name="forecast-future-spending"></a>Felmérheti a jövőbeli kiadásokat

Azure költség-kezelést úgy Cloudyn segít előrejelzési jövőbeli kiadásokat korábbi használati használ, és ebből az adatokat. Cloudyn jelentések segítségével minden költség leképezése adatainak megtekintéséhez. Ebben az oktatóanyagban szereplő példák végigvezetik Önt a jelentések használata költség leképezések áttekintése. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Felmérheti a jövőbeli kiadásokat

## <a name="forecast-future-spending"></a>Felmérheti a jövőbeli kiadásokat

Cloudyn költség leképezése jelentéseket segítségével felmérheti a kiadásokat használati mutatói alapján a időbeli tartalmazza. Az elsődleges célja segítségével győződjön meg arról, hogy a energiaköltségi trendjeinek nem haladja meg a szervezet elvárásainak. A jelentések használata az aktuális hónap tervezett költség és éves tervezett költség. Mindkét megjelenítése tervezett jövőbeli kiadásokat az használatának az elmúlt 30 napban használati viszonylag konzisztens marad.

Az aktuális hónap tervezett költség jelentésben a szolgáltatások költségeit. A tervezett költség megjelenítése a és az előző hónap elején található költségek használ. Kattintson a portál felső jelentések menü **költség** > **leképezési és a költségekre** > **aktuális hónap tervezett költség**. Az alábbi képen látható.

![aktuális hónap becsült költség](./media/tutorial-forecast-spending/project-month01.png)

A példában láthatja, melyik szolgáltatás a legtöbb töltött. Az Azure költségek voltak alacsonyabb, mint AWS költségeket. Ha szeretné-e a részletek költség leképezése Azure virtuális gépeken, a a **szűrő** listáról válassza ki **Azure/VM**.

![Azure virtuális gép az aktuális hónap becsült költség](./media/tutorial-forecast-spending/project-month02.png)

Ugyanezen lépések alapvető előző havi költség leképezések kíváncsiak vagyunk más szolgáltatások közül.

Az éves tervezett költség jelentés a szolgáltatások a extrapolált költségét jeleníti meg a következő 12 hónapon keresztül.

Kattintson a portál felső jelentések menü **költség** > **leképezési és a költségekre** > **éves tervezett költség**. Az alábbi képen látható.

![éves tervezett költség-jelentés](./media/tutorial-forecast-spending/project-annual01.png)

A példában láthatja, melyik szolgáltatás a legtöbb töltött. A havi példához hasonlóan az Azure költségek voltak alacsonyabb, mint AWS költségeket. Ha szeretné-e a részletek költség leképezése Azure virtuális gépeken, a a **szűrő** listáról válassza ki **Azure/VM**.

![a virtuális gépek éves tervezett költség](./media/tutorial-forecast-spending/project-annual02.png)

A fenti kép Azure virtuális gépek tervezett éves költségét $28,374.

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Felmérheti a jövőbeli kiadásokat


További Cloudyn dokumentációját előzetes készül első lépések Cloudyn, és annak szolgáltatásait használja.

> [!div class="nextstepaction"]
> [Cloudyn dokumentáció](https://support.cloudyn.com/hc/)
