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
ms.openlocfilehash: 0396c59d9d95ab71f0af04029d87afbb6e47dc35
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2017
---
# <a name="discover-how-customers-are-using-your-application-with-the-application-insights-funnels"></a>Hogyan használják az ügyfelek a az alkalmazás és az Application Insights tölcsérek felderítése

Ismertetése felhasználói élményének paraméter az üzleti kiemelt fontossággal bír. Ha az alkalmazás több szakaszt tartalmaz, akkor tudja, hogy a legtöbb ügyfél a teljes folyamatot végrehajtásába, vagy ha azok egy bizonyos ponton a folyamat befejezése esetén. A webalkalmazás lépések egy sorozatát keresztül előmenetel "tölcsér" néven ismert. Az Application Insights tölcsérek segítségével betekintést nyerhet a felhasználók és a figyelő részletes átváltása. 

## <a name="create-your-funnel"></a>A tölcsér létrehozása
A tölcsér létrehozása előtt kell adja meg a felvenni kívánt kérdést. Például előfordulhat, hogy szeretné tudni, hogy hány felhasználó tekinti meg a kezdőlapján a felhasználói profil megtekintéséhez, és a jegy létrehozásának. Ebben a példában a Fabrikam Fiber vállalati tulajdonosai tudni szeretné, az ügyfelek, akik sikeresen felhasználói jegy létrehozása hány százalékát.

Az alábbiakban azokat a lépéseket a tölcsér létrehozása.

1. Kattintson a tölcsérek eszközt az új gombra.
1. "Az elmúlt 90 napban" időtartománya válassza ki a **időtartomány** legördülő listán. Válassza "A tölcsérek" vagy "Megosztás tölcsérek"
1. Válassza ki a **Index** eseményt a **1. lépés** legördülő listából. 
1. Válassza ki a **ügyfél** eseményt a **2. lépés** legördülő listából.
1. Válassza ki a **létrehozása** eseményt a **3. lépés** legördülő listából.
1. A tölcsér név hozzáadása, és kattintson a **mentése**.

A következő ábra azt mutatja be, az adatokat a tölcsérek eszköz hoz létre. A Fabrikam innen tulajdonosok tekintheti meg, hogy az utolsó 90 napban 54.3 % az a kezdőlap látogató ügyfeleknek hozza létre a felhasználói jegy. Láthatják, hogy az ügyfelek 2.7-k az index származik a kezdőlap, ennek oka lehet egy frissítési probléma is.


![Az adatok tölcsérek eszköz](./media/app-insights-understand-usage-patterns/funnel1.png)

### <a name="funnel-features"></a>A tölcsér szolgáltatások
1. Ha az alkalmazás van mintát, látni fogja a mintavételi fejléc. Kattintson a szalagcím mintavételi kikapcsolása utasítja környezetben ablaktábla nyílik meg. 
2. Exportálhatja a tölcsér való [Power BI](app-insights-export-power-bi.md).
3. Kattintson egy lépésben, hogy részletesebb elemzéseket nyerhessen a jobb oldalon. 
4. Múltbeli csomagkonverzió jeleníti meg az elmúlt 90 napban az átalakításhoz. 
5. A felhasználók jobban megérteni a tölcsérek nyissa meg a felhasználó-eszköz. Az egyes lépések, válogatott felhasználók szűrők rendszerében. 

## <a name="next-steps"></a>Következő lépések
  * [Használat – áttekintés](app-insights-usage-overview.md)
  * [Felhasználók, a munkamenetek és az események](app-insights-usage-segmentation.md)
  * [Megőrzés](app-insights-usage-retention.md)
  * [Munkafüzetek](app-insights-usage-workbooks.md)
  * [Felhasználói környezet hozzáadása](app-insights-usage-send-user-context.md)
  * [Power BI-exportálás](app-insights-export-power-bi.md)

