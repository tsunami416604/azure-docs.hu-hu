---
title: Sávdiagram réteg hozzáadása a Azure Maps Power BI vizualizációhoz | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan használhatja a sávdiagramok réteget a Microsoft Azure Maps-vizualizációban Power BIhoz.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 34d2d96e28f90249ad25788f6994dac63f83b1f6
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261953"
---
# <a name="add-a-bar-chart-layer"></a>Oszlopdiagram rétegének hozzáadása

A **sávdiagram réteg** akkor hasznos, ha a következő dimenzióba helyezi az adatgyűjtést, mivel lehetővé teszi a helyadatok 3D sávokként vagy hengerként való megjelenítését a térképen. A buborék réteghez hasonlóan a diagram később is könnyedén megjelenítheti két mérőszámot a szín és a relatív magasság használatával. Ahhoz, hogy a sávok magassága meglegyenek, fel kell venni egy mértéket a **mezők** ablaktábla **méret** gyűjtőbe. Ha egy mérték nincs megadva, a **sáv alakzat** beállításától függően a rendszer nem tartalmaz magasság nélküli négyzeteket vagy köröket.

> [!div class="mx-imgBorder"]
> ![Térképi pont adatai a sávdiagram réteggel](media/power-bi-visual/bar-chart-layer-styled.png)

A felhasználók megtiltják és elforgatják a térképet az adatok különböző perspektívából való megtekintéséhez. A Térkép az alábbi módszerek egyikével állítható be vagy helyezhető el.

-   Kapcsolja be a **navigációs vezérlők** beállítást a **Formátum** ablaktábla **Térkép beállításainál** . Ezzel hozzáad egy gombot a Térkép megdöntéséhez.
-   Nyomja le a jobb egérgombot, és húzza felfelé vagy lefelé az egeret.
-   Érintse meg a térképet két ujjal, és húzza őket egymáshoz.
-   A Térkép fókuszban tartsa a **SHIFT** billentyűt, és nyomja le a **fel** vagy a **le nyílbillentyűket** .

A Térkép a következő módszerek egyikével forgatható el.

-   Kapcsolja be a **navigációs vezérlők** beállítást a **Formátum** ablaktábla **Térkép beállításainál** . Ezzel hozzáad egy gombot a Térkép elforgatásához.
-   Nyomja le a jobb egérgombot, és húzza az egeret balra vagy jobbra.
-   Érintse meg a Térkép két ujjal való használatát, és forgassa el.
-   A Térkép középpontjában tartsa a **SHIFT** billentyűt, majd nyomja meg a **bal** vagy a **jobb nyílbillentyűt** .

A következő beállítások szerepelnek a **sávdiagramok réteg** szakaszban elérhető **Format (formátum** ) ablaktáblán.

| Beállítás              | Leírás      |
|----------------------|------------------|
| Sáv alakja            | A 3D sáv alakja<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Box – a téglalap alakú mezőként megjelenített sávok.<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Henger – a sávokat hengerként jeleníti meg. |
| Magasság               | Az egyes sávok magassága Ha egy mezőt a **mezők** ablaktábla **Size (méret** ) gyűjtője adja át, a sávok ehhez a magassághoz viszonyítva lesznek méretezve. |
| Méretezési magasság nagyításkor | Meghatározza, hogy a sávok magasságának a nagyítási szinthez képest kell-e méreteznie. |
| Szélesség                | Az egyes sávok szélessége  |
| Méretezés szélessége nagyításkor  | Meghatározza, hogy a sávok szélessége a nagyítási szinthez képest legyen-e méretezhető.  |
| Kitöltési szín           | Az egyes sávok színe Ez a beállítás akkor jelenik meg, ha a **mezők** ablaktábla **Jelmagyarázat** -gyűjtője egy mezőt ad át, és a **Formátum** ablaktáblán megjelenik egy külön **adatszíni** szakasz. |
| Átláthatóság         | Az egyes sávok átlátszósága. |
| Minimális nagyítás             | A minimális nagyítási szint csempéi elérhetők. |
| Maximális nagyítás             | A maximális nagyítási szint csempe elérhető. |
| Réteg pozíciója       | Meghatározza a réteg pozícióját a többi térképi réteghez képest. |

> [!NOTE]
> Ha a sávoknál kis szélességű érték van letiltva, és a **Méretezés szélessége a nagyítási** beállításnál le van tiltva, akkor eltűnnek, ha a kinagyított szélessége nagyobb, mint a képpontnál kisebb méret. Ha azonban a **méretezési szélesség a nagyításkor** beállítás engedélyezve van, további számítások történnek, amikor a nagyítási szint megváltozik, ami hatással lehet a nagyméretű adathalmazok teljesítményére.

## <a name="next-steps"></a>Következő lépések

További környezet hozzáadása a térképhez:

> [!div class="nextstepaction"]
> [Hivatkozási réteg hozzáadása](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [Mozaikréteg hozzáadása](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Valós idejű forgalom megjelenítése](power-bi-visual-show-real-time-traffic.md)

A vizualizáció testreszabása:

> [!div class="nextstepaction"]
> [Színformázási tippek és trükkök a Power BI-hoz](https://docs.microsoft.com/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [A vizualizáció címeinek, háttereinek és jelmagyarázatainak testreszabása](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)