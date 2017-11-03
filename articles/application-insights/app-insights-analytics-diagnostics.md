---
title: "A webes alkalmazás teljesítmény változásainak Azure Application Insights diagnosztika intelligens |} Microsoft Docs"
description: "Automatikus diagnosztizálására igényeiben jelentkező vagy a webes alkalmazás teljesítmény telemetriai lépéseit."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/16/2017
ms.author: mbullwin
ms.openlocfilehash: e0c8d712f03da0c5e47ea422b051c0b8734c6b21
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="diagnose-sudden-changes-in-your-app-telemetry"></a>Az alkalmazás telemetriai hirtelen változásai diagnosztizálása

*A funkció jelenleg előzetes verzió.*

A webalkalmazás teljesítmény- vagy egyetlen kattintással használat hirtelen változásai diagnosztizálása! Az intelligens diagnosztika szolgáltatás esetén érhető el, amikor egy idő diagramot a [Analytics](app-insights-analytics.md) a [Application Insights](app-insights-overview.md). Bárhol egy szokatlan módosult az a tendencia, például egy csúcs vagy DIP-t, az eredmények intelligens diagnosztika azonosítja a dimenziók és a kapcsolódó értékek, amelyek megmagyarázhatják, a módosítás egy mintát. Ennek segítségével gyorsan diagnosztizálhatja a problémát. 

Ebben a példában intelligens diagnosztikai észlelt egy mintáját a módosítás tulajdonságait, és kiemeli a rendelkező és anélküli minta közötti különbség:

![Példa analytics diagnosztika eredménye](./media/app-insights-analytics-diagnostics/analytics-result.png)
 

## <a name="diagnose-data-changes"></a>Adatok változásait diagnosztizálása

1.  Az Analytics-lekérdezés futtatható, és megjelenítheti idő diagramként. 
2.  Ha van ilyen, kattintson a bármely kiemelt csúcs pont.
 
    ![csúcsidőszak pont](./media/app-insights-analytics-diagnostics/peak.png)

    Diagnosztika a minta felderítéséhez néhány másodpercet vesz igénybe.

3. A diagnosztikai eredményeket lapon láthatók a mintát, amely előfordulhat, hogy az adatok folytonosságában ismertetik.

    ![eredménye](./media/app-insights-analytics-diagnostics/result.png)
 
    A szöveg a dimenzió értékeit, és a shift kivizsgált tartalmazza. Ebben a példában van társítva egy adott kérés és egy adott böngésző verziószáma.

    Figyelje meg a diagramot, a szűrő IGAZ és hamis értéket is a két összetevőt. A hamis összetevő egy változatlan trend jeleníti meg. Ez azt jelenti nincs változás a telemetriai adatok eredmények között, ha a dimenziók diagnosztika azonosított problematikus kombinációja zárja azt. Ezzel szemben a belül kombinációt eredményben jelentős változás vizsgálat kiemelt területen belül. Ez azt jelenti, hogy diagnosztika megtalálható-e tulajdonságok kombinációja, amely változásait mutatja be.

4.  Ha a minta összetett, szeretné-e rámutat **az összes megjelenítése** a dimenziók megjelenítéséhez.

    ![összes megjelenítése](./media/app-insights-analytics-diagnostics/show-all.png)
 
5.  Diagnosztika megtalálja nincs jelentős mintát értesítést küldeni, ha a nem eredménye lap jelenik meg. Ezen a ponton módosíthatja a lekérdezést. Például sikerült szűkítse az időtartományt, és a dobozolás Analytics a lekérdezésben, egy további elemzés céljából, és esetleg jobb eredményeket.

Volt képes a Tudásbázis, hogy rendelkezik-e egy adott oldal, a webhely probléma egy adott böngésző felvértezve, mostantól, lépjen a probléma lapot, és vizsgálja meg a legutóbbi módosítások.

## <a name="try-the-demo"></a>Próbálja ki a demót

[Ide kattintva megtekintheti a bemutató](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA3VSTY%2FTQAy991dYPXWlLf0QIO2KIiGWA3duiMPsxEnMzhe2p6WIH48nVUsuGylRNPOe3%2FOzN5vFZgPfRhL4VZHPIGM%2BCdgHdESgpMjOKx0RnsgNKYuSF%2BjRaWUE7xKMGIoBgTpMSv2Z0jBxOWc1QBWEPjM4EMUCP2uc0A3x8E5HKMi%2BEQNC7oHRbIgKdJWdUk5vmr9PvdkArildit%2Fcrk0lBDjnyhBzk%2FKVxdTy0QhNY6RhDPYqdlCy9XMV96NjBZc68IH8y6Tzuf01iZxeIZ%2FI5DqMOYmaQQRXNUdz6qGb5WOdSKEXnOozHtEFK%2Bh0qnq5YQzGF9DcoinoqbcigkO0NOZRNGOZaaBkMuat5xznFOtULKhG%2BdrGlVDhy%2B8SMlsETV8dD6gTd0YrbsBrFq6U1v%2Filv4C%2FsJpRJuwUrQTZ0P7eIDOHLeD1X67e7%2Fe7dbbB9htH%2Ffbu4vQDfvhFez%2B8a1h%2F1f3VSy%2BJ4Ol1oN8X4qN0qMZWv44HJanzKFLeJIltKcRpcbomP7gbHNkdV2Xe1uqO3g%2BwzOl1c3PvbmMlC7KjKlry2GX0w4s%2FgFoo5%2BhBAMAAA%3D%3D&timespan=PT24H) a mintaadatokat.

## <a name="how-it-works"></a>Működés

Intelligens diagnosztika egy speciális felügyeletlen gépi tanulási algoritmus alapján használja a [DiffPatterns](app-insights-analytics-reference.md) műveletet. A változását elmagyarázhatja jelölt mintázatokat keresi. Elemzi az egyes jelölt a metrika a hatását, és a minta jeleníti meg, hogy legjobban megfelel a módosítást.

## <a name="no-diagnostic-points"></a>Olyan diagnosztikai pontot?

Intelligens diagnosztika csak akkor működik, amikor a következő feltételek teljesülnek:

 * Intelligens diagnosztika beállítás kapcsolni. Keresse meg a beállítások ikonra Analytics.
 * Az intelligens diagnosztikai beállításai a beállítást. 
 * Időtengelye: a diagram az x tengely típusúnak kell lennie `datetime`.
 * Sor-vagy terület: diagnosztika csak akkor működik, az ilyen típusú diagram. Használjon `| render timechart` vagy `| render areachart` a lekérdezést; végén, vagy válassza ki a legördülő lista választó sor vagy területdiagram.
 * Folytonosságában: Szerepelnie kell jelentős kihagyást az adatokat.
 * Elegendő pontok elemzéséhez.
 * Legfeljebb egy összefoglalója záradék a lekérdezésben.
 * Nincs projekt záradék, amely egy név-definíciót, mielőtt a summarize záradékot tartalmaz.

 
 ## <a name="related-articles"></a>Kapcsolódó cikkek

 * [Elemzés oktatóanyag](app-insights-analytics-tour.md)
 * [Észlelési intelligens](app-insights-proactive-diagnostics.md) automatikusan riasztást küld, a teljesítménnyel kapcsolatos problémákat.