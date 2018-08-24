---
title: Ismerkedés az Azure-költségek költségelemzés |} A Microsoft Docs
description: Ez a cikk segít a költségek elemzése az vizsgálata és elemzése az Azure-szervezeti költségeit.
services: billing
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 08/16/2018
ms.topic: conceptual
ms.service: billing
manager: dougeby
ms.custom: ''
ms.openlocfilehash: db06956e01ed38ce1f8ee0ce92526be16733fd73
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818124"
---
# <a name="explore-and-analyze-costs-with-cost-analysis"></a>Vizsgálata és elemzése a költségeket a költségelemzés

Mielőtt megfelelően szabályozhatja, és optimalizálhatja Azure-költségeit, kell tudni, ahol költségek adja meg a szervezeten belül. Emellett hasznos tudja, hogy mennyi pénzt a szolgáltatások költség, és milyen környezetek és a rendszerek támogatásához. A költségek teljes spektruma elérhető betekintést fontos pontosan a szervezeti költségkeret mintákat, amelyek segítségével költség mechanizmusai, például a költségvetés kényszerítése ismertetése.

Ez a cikk segítségével költségelemzés vizsgálata és elemzése a szervezeti költségeket. Megtekintheti a összesített költségeit a szervezet megértéséhez, hogy hol vannak feladatokkal kapcsolatos költségek, és azonosíthatja a költési trendeket. Megtekintheti a összesített költségek időbeli alakulása megbecsülni havonta, negyedévente vagy évente akár költségtrendek költségvetési ellen. Költségvetési pénzügyi korlátok formátumhoz vagy napi vagy havi költségek elkülöníteni a költségkeret szabálytalanságok megtekintése segít. És tölthet le az aktuális jelentés adatokat további elemzés céljából, vagy egy külső rendszer használhatják.

## <a name="requirements"></a>Követelmények

Költségelemzés érhető el nagyvállalati szerződéssel (EA)-ügyfelekre. A költségadatok megtekintéséhez a következő hatókörök legalább egy olvasási hozzáféréssel kell rendelkeznie.

- Az Azure nagyvállalati szerződés számlázási fiók (regisztráció)
- Az Azure nagyvállalati szerződéses előfizetésében
- Az Azure nagyvállalati szerződéssel rendelkező előfizetés-erőforráscsoport

## <a name="review-costs-in-cost-analysis"></a>Költségelemzés a költségek áttekintése

Tekintse át a költségelemzés költségeket, nyissa meg az Azure Portalon, majd nyissa meg **Költségkezelés + számlázás** &gt; **számlázási fiókok** &gt; válassza ki a nagyvállalati szerződés számlázási fiók &gt; Költségkezelés, válassza a **költségelemzés**.

A kezdeti költségek elemzése nézet tartalmazza a következő területeken:

**Teljes** – az aktuális hónaphoz teljes költségeit mutatja.

**Költségvetés** – megjeleníti a tervezett költségkeret a kijelölt hatókör, ha elérhető.

**Összesített költségeit** – megjeleníti az összes feladatokkal kapcsolatos napi kiadások, a hónap kezdetétől fogva. Ha rendelkezik [költségvetési létrehozott](billing-cost-management-budget-scenario.md#create-the-azure-budget) a számlázási fiók vagy előfizetés, gyorsan megtekintheti a költési trend, a költségvetés viszonyítva. A kurzort egy dátumot, az adott napon összesített költségeit.

**Forgáspont (fánkdiagramok)** – adja meg a dinamikus szűkítése. Ezek az alapvető tulajdonságok készletének összköltség felosztania. A diagramok megjelenítése az aktuális hónaphoz feladatokkal kapcsolatos legalább költség, a legtöbb. Kimutatásdiagramok különböző területekre kattintva bármikor módosíthatja. Alapértelmezés szerint költségek bontásban (mérőszám kategóriája) szolgáltatás, a helyet (régiót) és a gyermek hatókörben (például a számlázási fiókok regisztrációs fiókok, előfizetések tartozó erőforráscsoportok és erőforrások erőforráscsoportok alapján).

![Költségelemzés kezdeti nézete](./media/billing-cost-analysis/cost-analysis-01.png)



## <a name="customizing-cost-views"></a>Költség nézetek testreszabása

Az alapértelmezett nézet hasonló gyakori kérdések gyors válaszokat nyújt:

- Mennyire volt költségek?
- E marad a költségvetésen belül?

Vannak azonban sok esetben, ha mélyebb elemzésre van szüksége. Testreszabás elindítja az a dátum kiválasztása az oldal tetején.

Alapértelmezés szerint költségelemzés az aktuális hónaphoz adatokat jeleníti meg. Hónap, ebben a hónapban, naptári negyedévet, ebben az évben vagy egy egyéni dátumtartományt tetszőleges gyorsan váltani a legutóbbi dátum választómezőt használja. A leggyorsabb mód a legújabb Azure-számla elemzésére és a költségek egyszerűen egyeztetése kiválasztása az elmúlt hónapban. Az aktuális negyedéves és éves beállítások segítségével nyomon követheti a hosszabb távú támasztott költségeket. Vagy a minden részletre kiterjedő, vagy szélesebb körű dátumtartomány egy nap az elmúlt hét nap vagy bármely legfeljebb előtt az aktuális hónap év közül választhat.

![Dátum-választó](./media/billing-cost-analysis/date-selector.png)

Is alapértelmezés szerint cost analysis mutat be **halmozott** költségeket. Összesített költségek mellett az elmúlt napokban, napi, esedékes költségeit egyre bővülő megjelenítéséhez minden nap közé tartozik minden díjat. Ez a nézet bemutatják, hogyan vannak, szemben a megadott időtartományban költségvetést népszerű lett optimalizálva.

Emellett van a **napi** megtekintése. Költség látható minden egyes nap és a egy növekedési trendje nem jelenik meg. A napi nézet szabálytalanságok állapotúként költségek kiugrás vagy merítsük napról napra van optimalizálva. Költségvetési választja, a napi nézet is jelenít meg a napi költségvetést néz becslése. Ha a napi költségek egységesen a becsült napi költségvetés fölött, majd várható, hogy lesz túllépi a havi költségvetést. A becsült napi költségvetés az egyszerűen azt segítségével vizualizálhatja a költségvetést, alacsonyabb szinten. Ha napi költségek ingadozása, a havi költségvetés becsült napi költségvetés összehasonlítás esetén kevésbé pontosak.

![Napi megtekintése](./media/billing-cost-analysis/daily-view.png)

Is **csoportosítás** csoport kategória a teljes terület felső diagramon megjelenő adatok módosítása kiválasztásához. A csoportosítás megfigyelhessük, hogyan költségeit kategorizálva erőforrástípus szerint teszi lehetővé. Íme a legutóbbi hónap megjelenítéséhez az Azure szolgáltatási költségei nézetét.

![Csoportosított napi halmozott megtekintése](./media/billing-cost-analysis/grouped-daily-accum-view.png)

Kimutatásdiagramok a felső teljes nézet alatt találhatók. Ezek segítségével tekintse meg a nézetek különböző csoportosítási és szűrés a kategóriák. Minden csoport kategóriát választja, a teljes adatkészlet összes nézet esetén a nézet alján. Íme egy példa erőforráscsoportokhoz.

![Összes adat a jelenlegi nézet](./media/billing-cost-analysis/full-data-set.png)

### <a name="download-cost-analysis-data"></a>Töltse le a cost analysis-adatok

Ha Ön **letöltése** származó információk költségelemzés, jelenleg az Azure Portalon látható minden adat CSV-fájl jön létre. Ha szűrőket alkalmazott, vagy a csoportosítás, majd, hogy a fájl tartalmazza. A felső teljes diagram, amely nem aktívan jelenik meg a portálon alapul szolgáló adatok is megtalálható a CSV-fájlt.

## <a name="next-steps"></a>További lépések

Egyéb megtekintése [Azure-elszámolással és a cost management dokumentációja](billing-cost-management-budget-scenario.md).
