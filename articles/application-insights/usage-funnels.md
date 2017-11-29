---
title: "Az Azure Application Insights tölcsérek"
description: "Ismerje meg, hogyan használhatja tölcsérek hogyan ügyfelek az alkalmazással való interakció felderítéséhez."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: mbullwin
ms.openlocfilehash: bbb25af888f34737f6a61cf43890ff248c4cc4de
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>Hogyan használják az ügyfelek a az alkalmazás az Application Insights tölcsérek felderítése

A felhasználói élmény megértése, a kiemelt fontossággal bír az üzleti. Ha az alkalmazás több szakaszt tartalmaz, akkor tudja, hogy a legtöbb ügyfél a teljes folyamatot végrehajtásába, vagy ha azok egy bizonyos ponton a folyamat befejezése esetén. A webalkalmazás lépések egy sorozatát keresztül előmenetel is ismert, egy *tölcsér*. Betekintést nyerhet a felhasználók Azure Application Insights tölcsérek segítségével, és lépésről lépésre átváltási figyelése. 

## <a name="create-your-funnel"></a>A tölcsér létrehozása
A tölcsér létrehozása előtt adja meg a felvenni kívánt kérdést. Például előfordulhat, hogy szeretné tudni, hogy hány felhasználó tekinti meg a kezdőlapján a felhasználói profil megtekintéséhez, és a jegy létrehozásának. Ebben a példában a Fabrikam Fiber vállalati tulajdonosai tudni szeretné, az ügyfelek, akik sikeresen felhasználói jegy létrehozása hány százalékát.

Az alábbiakban azokat a lépéseket a tölcsér létrehozása.

1. Válassza ki az Application Insights tölcsérek eszköz **új**.
1. Az a **időtartomány** legördülő menüben válassza **utolsó 90 napban**. Válassza ki vagy **a tölcsérek** vagy **tölcsérek megosztott**.
1. Az a **1. lépés** legördülő listában válassza **Index**. 
1. Az a **2. lépés** listáról válassza ki **ügyfél**.
1. Az a **3. lépés** listáról válassza ki **létrehozása**.
1. A tölcsér név hozzáadása, és válassza ki **mentése**.

Az alábbi képernyőfelvételen látható egy példa a típusú adatokat a tölcsérek eszköz hoz létre. A Fabrikam tulajdonosok láthatja, hogy az utolsó 90 napban, az ügyfeleknek a létrehozott felhasználói jegy kezdőlap látogató 54.3 százalékát. Is láthatják, hogy az ügyfelek 2,700 származási az indexhez a kezdőlap. Ez egy frissítési probléma utalhat.


![Képernyőfelvétel a tölcsérek eszköz adatokkal](./media/app-insights-understand-usage-patterns/funnel1.png)

### <a name="funnels-features"></a>Tölcsérek szolgáltatások
Az előző képernyőképet öt kiemelt területeket tartalmazza. Ezek a tölcsérek funkcióit. Az alábbi lista a képernyőfelvételen látható minden egyes megfelelő terület több ismerteti:
1. Ha az alkalmazás van mintát, látni fogja a mintavételi fejléc. Környezetben ablaktábla, amely ismerteti, hogy a mintavételi kikapcsolása kiválasztása a transzparens megnyitása. 
2. Exportálhatja a tölcsér való [Power BI](app-insights-export-power-bi.md).
3. Válassza ki azt a további részletek megtekintéséhez kattintson a jobb. 
4. Az átváltási történelmi diagram átváltási megjeleníti az elmúlt 90 napban. 
5. Ismerje meg a felhasználók jobban való hozzáférés a felhasználó-eszköz. A szűrőkkel az egyes lépéseket. 

## <a name="next-steps"></a>Következő lépések
  * [Használat – áttekintés](app-insights-usage-overview.md)
  * [Felhasználók, a munkamenetek és az események](app-insights-usage-segmentation.md)
  * [Megőrzés](app-insights-usage-retention.md)
  * [Munkafüzetek](app-insights-usage-workbooks.md)
  * [Felhasználói környezet hozzáadása](app-insights-usage-send-user-context.md)
  * [Power BI-exportálás](app-insights-export-power-bi.md)

