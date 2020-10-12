---
title: Jupyter-jegyzetfüzet megjelenítése diavetítésként Azure Notebooks előzetes verzióban
description: Megtudhatja, hogyan konfigurálhat cellákat Diavetítés módban egy Jupyter-jegyzetfüzetben, majd a diavetítést a RISE bővítmény használatával.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 2fe337361436ecfc8eabf2855ad633b891db69d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85834046"
---
# <a name="run-a-notebook-slideshow-in-azure-notebooks-preview"></a>Jegyzetfüzet-Diavetítés futtatása Azure Notebooks előzetes verzióban

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Azure Notebooks előre konfigurálva van a Jupyter/IPython Slideshow bővítménnyel (RISE), amely lehetővé teszi, hogy egy jegyzetfüzetet közvetlenül diavetítésként nyújtson be. A diavetítésben a cellák általában egy olyan betűmérettel jelennek meg, amely a nagyméretű képernyőkön való megjelenítéshez megfelelő, és a kódot továbbra is futtathatja, és nem kell külön bemutató számítógépre váltania.

Az alábbi képen a szabványos jegyzetfüzet nézet látható, amelyben a Markdown és a kód cellái együttesen láthatók:

![Normál nézetben található jegyzetfüzet](media/slideshow/slideshow-notebook-view.png)

A diavetítés indításakor az első cella kibővül a böngésző kitöltéséhez, ahol az **X** a bal felső sarokban kilép a diavetítésből **?** a bal alsó részen a billentyűparancsok és a jobb alsó sarokban lévő nyilak láthatók a diák között:

![Diavetítés módban lévő jegyzetfüzet](media/slideshow/slideshow-slide-view.png)

A jegyzetfüzetek diavetítésre való előkészítése két elsődleges tevékenységet foglal magában:

1. Mivel a Markdown-cellák nagy betűkészletekkel jelennek meg, előfordulhat, hogy egyes tartalmak nem láthatók a diavetítésben. Így általában az adott cellában lévő szöveg mennyiségét korlátozza. a négy – hat soros fejléc általában a legjobban működik. Ha több szöveggel rendelkezik, Ossza szét ezt az információt több cellába.

2. A diavetítési cella eszköztárának használatával konfigurálja az egyes cellák viselkedését. A cellák típusai határozzák meg a navigációs gombok viselkedését.

## <a name="the-anatomy-of-a-slideshow"></a>A diavetítés anatómiája

Ha véletlenszerű jegyzetfüzetet használ, és egy diavetítéshez használja, általában úgy látja, hogy az összes cella össze van keverve, és a tartalom nagy része rejtve marad a böngészőablak alján. Ahhoz, hogy egy hatékony bemutatót lehessen készíteni, minden cellához hozzá kell rendelnie egy slideshow-típust a diavetítés cella eszköztár használatával:

1. A **nézet** menüben válassza a **cella eszköztár**-  >  **Diavetítés**elemet:

    ![A cella-diavetítés eszköztárának bekapcsolása](media/slideshow/slideshow-view-cell-toolbar.png)

1. A Jegyzetfüzet minden cellájának jobb felső sarkában megjelenik egy **dia típusa** legördülő menü:

    ![Cella-diavetítés eszköztár](media/slideshow/slideshow-cell-toolbar.png)

1. Mindegyik cella esetében válasszon ki egy öt típust:

    ![Cella-diabemutató típusai](media/slideshow/slideshow-cell-slide-types.png)

    | Dia típusa | Működés |
    | --- | --- |
    | -(nincs beállítva) | A cella az előző cellával jelenik meg, ami gyakran nem a kívánt hatás egy diavetítésben. |
    | Dia | A cella egy elsődleges dia, amely a navigációs vezérlőelem bal és jobb nyilát használva navigál. |
    | Sub-slide | A cella "alul" egy elsődleges dia, a navigációs vezérlőelem lefelé mutató nyilát használva navigált. A felfelé mutató nyíl az elsődleges diához tér vissza. Az aldiákat olyan másodlagos anyagokhoz használják, amelyeket kihagyhat a bemutató fő elérési útjában, de szükség esetén könnyen elérhető. |
    | Töredék | A cella tartalma az előző dia vagy az aldia kontextusában jelenik meg a lefelé mutató navigációs nyíl használatakor (a felugró nyíl használatakor a rendszer eltávolítja a töredéket). A kódrészletek segítségével a kód megjelenhet egy dián belül, vagy több töredéket is használhat a szöveges felsorolásjelek megjelenítéséhez (lásd a példát a következő szakaszban). Mivel a töredékek az aktuális dián vannak kiépítve, a felesleges töredékek nem lesznek láthatók a böngészőablak alján. |
    | Kihagyás | A cella nem jelenik meg a diavetítésben. |
    | Jegyzetek | A cella olyan hangjegyzeteket tartalmaz, amelyek nem láthatók a diavetítésben. |

1. Először is hasznos lehet minden cella esetében kiválasztani a **diát** . Ezután futtathatja a diavetítést, és elvégezheti a megfelelő módosításokat.

### <a name="example-fragment-cells-for-bullet-items"></a>Példa: felsorolásjeles elemek töredék cellái

Ahhoz, hogy a felsorolásjelek egy dián legyenek láthatók, helyezze a dia fejlécét egy Markdown-cellába, majd helyezze **el az egyes** felsorolásjeleket egy különálló Markdown-cellába a **töredék** típussal:

![Példa több Markdown-cella létrehozására a felsorolásjeles elemekhez](media/slideshow/slideshow-fragments.png)

Mivel a diavetítés több függőleges térközt jelenít meg, mint amikor az összes felsorolásjel ugyanabban a cellában található, előfordulhat, hogy nem tud annyi felsorolásjelet használni.

## <a name="run-the-slideshow"></a>A Diavetítés futtatása

1. Ha bármilyen Markdown-cellát szerkesztett, ne felejtse el futtatni őket a HTML-fájl megjelenítéséhez, ellenkező esetben a *Diavetítés Markdown jelenik* meg.

1. Miután konfigurálta az egyes cellákhoz tartozó **dia típusát** , válassza ki azt a cellát, amelyből el szeretné indítani a diavetítést, majd a fő eszköztáron válassza a **beírás/kilépés emelkedése** gombot:

    ![A fő eszköztáron a bemutató indítása/bezárása gomb](media/slideshow/slideshow-start.png)

1. A diák és a töredékek közötti navigáláshoz használja a navigációs vezérlő bal és jobb nyilát. A vezérlőelemben lévő szöveg a *dia. sub-slide*számot jelképező számot jeleníti meg.

    ![Diabemutató navigációs vezérlője](media/slideshow/slideshow-navigation-control.png)

1. A diák és az allemezek, valamint a töredékek közötti váltáshoz használja a fel és a le nyilakat, ha engedélyezve vannak:

    ![Diavetítéses navigációs vezérlők az aldiákhoz](media/slideshow/slideshow-navigation-control-subslide.png)

1. A kód cellán a lejátszás gombra kattintva futtassa a kódot; a kimenet megjelenik a dián:

    ![Lejátszás gomb a kód cellájának futtatásához](media/slideshow/slideshow-run-code-cell.png)

    ![A kód cellájának kimenete megjelenik a diavetítésben](media/slideshow/slideshow-run-code-cell-output.png)

    > [!Tip]
    > A cella kimenete a bemutató cellájának része. Ha jegyzetfüzet vagy Diavetítés nézetben futtat egy cellát, a kimenet a másik nézetben is megjelenik. A kimenet törléséhez használja a **cella**  >  **aktuális kimenete**  >  **Clear** parancsot (az aktuális cellánál), vagy **Cell**az  >  **összes kimenetet**  >  **törölje** (minden cella esetében).

1. Ha elkészült a diavetítéssel, az **X** használatával térjen vissza a jegyzetfüzet nézetbe.

## <a name="next-steps"></a>További lépések

- [Útmutató: projektek konfigurálása és kezelése](configure-manage-azure-notebooks-projects.md)
- [Útmutató: csomagok telepítése jegyzetfüzetből](install-packages-jupyter-notebook.md)
- [Útmutató: az adatfájlok használata](work-with-project-data-files.md)
- [Útmutató: az adaterőforrások elérése](access-data-resources-jupyter-notebooks.md)
