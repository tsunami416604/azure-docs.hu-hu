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
ms.openlocfilehash: d7af89409cb908f98f86288a0d673ab287e3aaaa
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="discover-how-customers-are-using-your-application-with-the-application-insights-funnels"></a>Hogyan használják az ügyfelek a az alkalmazás és az Application Insights tölcsérek felderítése

Ismertetése felhasználói élményének paraméter az üzleti kiemelt fontossággal bír. Ha az alkalmazás több szakaszt tartalmaz, akkor tudja, hogy a legtöbb ügyfél a teljes folyamatot végrehajtásába, vagy ha azok egy bizonyos ponton a folyamat befejezése esetén. A webalkalmazás lépések egy sorozatát keresztül előmenetel "tölcsér" néven ismert. Az Application Insights tölcsérek segítségével betekintést nyerhet a felhasználók és a figyelő részletes átváltása. 

## <a name="get-started-with-the-funnels-blade"></a>Ismerkedjen meg a tölcsérek panel
A legegyszerűbben úgy tölcsérek megismerése lépésről-lépésre, ha például. Az alábbi ábrák bemutatják, egy e-kereskedelemhez kapcsolódó üzleti lépéseket tulajdonosai megtudhatja, hogy az ügyfelek hogyan működjön együtt a webes alkalmazás időt vesz igénybe.  

### <a name="create-your-funnel"></a>A tölcsér létrehozása
A tölcsér létrehozása előtt kell adja meg a felvenni kívánt kérdést. Például érdemes tudni, hogy hány ügyfél megtekintése a kezdőlapján kattintson a hirdetmény. Ebben a példában a Fabrikam Fiber vállalati tulajdonosai szeretnék tudni, hogy az ügyfelek, akik az elmúlt hónapban a kosár elemek hozzáadása után a vásárlás hány százalékát.

Az alábbiakban azokat a lépéseket a tölcsér létrehozása.

1. Az új gombra a tölcsérek panelen.
1. Válassza ki az időtartományt, az "Előző hónap" a **időtartomány** legördülő listán. 
1. Válassza ki a **termékoldalára** eseményt a **1. lépés** legördülő listából. 
1. Válassza ki a **kosár hozzáadása** eseményt a **2. lépés** legördülő listából.
1. Válassza ki a **kattintson a vételi** eseményt a **3. lépés** legördülő listából.
1. A tölcsér név hozzáadása, és kattintson a **mentése**.

Az alábbi ábra azt mutatja be, az adatokat a tölcsérek panel állít elő. A Fabrikam innen tulajdonosok tekintheti meg, hogy az elmúlt hét során az ügyfelek, akik egy elemet fel a kosár 22.7 %-át a beszerzési befejeződött. Is láthatják, hogy 1 %-át az ügyfeleket hirdetmény kattintott, miután befejezte a vásárlást kijelentkezteti a termék oldalát, valamint a 20 %-át az ügyfelek előtt.


![Adatok tölcsérek panelről](./media/app-insights-understand-usage-patterns/funnel1.png)

## <a name="next-steps"></a>Következő lépések
  * [Használat – áttekintés](app-insights-usage-overview.md)
  * [Felhasználók, a munkamenetek és az események](app-insights-usage-segmentation.md)
  * [Megőrzés](app-insights-usage-retention.md)
  * [Munkafüzetek](app-insights-usage-workbooks.md)
  * [Felhasználói környezet hozzáadása](app-insights-usage-send-user-context.md)
