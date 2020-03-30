---
title: Jupyter-jegyzetfüzet bemutatása diavetítésként az Azure Notebookelőzetes verzióban
description: Ismerje meg, hogyan állíthatja be a cellákat a diavetítés módhoz egy Jupyter notebookban, majd mutassa be a diavetítést a RISE kiterjesztéssel.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 05dd3d9c5580e208ecf6f9e6d762476b0b493a6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647118"
---
# <a name="run-a-notebook-slideshow-in-azure-notebooks-preview"></a>Jegyzetfüzet-diavetítés futtatása az Azure Notebookelőzetes verzióban

Az Azure Notebooks előre konfigurálva van a Jupyter/IPython Diavetítés bővítmény (RISE) használatával, amely lehetővé teszi, hogy a jegyzetfüzetet közvetlenül diavetítésként mutassa be. A diavetítésben a cellák általában egyenként jelennek meg olyan betűmérettel, amely alkalmas a nagy képernyőkön való megjelenítésre, és a kódot továbbra is futtathatja ahelyett, hogy külön bemutatószámítógépre váltana.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Az alábbi képen a szabványos jegyzetfüzetnézet látható, amelyben együtt láthatja a Markdown és a kódcellákat:

![Jegyzetfüzet normál nézetben](media/slideshow/slideshow-notebook-view.png)

Amikor elindítja a diavetítés, az első cella kinagyítja, hogy töltse ki a böngészőt, ahol az **X** a bal felső kilép a diavetítés, **?** a bal alsó sarokban megjelenik a billentyűparancsok, és a jobb alsó sarokban lévő nyilak a diák között navigálnak:

![Jegyzetfüzet diavetítési módban](media/slideshow/slideshow-slide-view.png)

A jegyzetfüzet diavetítéshez való előkészítése két elsődleges tevékenységet foglal magában:

1. Mivel a Markdown-cellák nagy betűkészletekkel jelennek meg, előfordulhat, hogy egyes tartalmak nem láthatók a diavetítésben. Így általában korlátozza a szöveg mennyiségét egy adott cellában; a négy-hat sokkal rendelkező fejléc általában a legjobban működik. Ha több szöveget tartalmaz, ossza fel az adatokat több cellára.

2. A diavetítés egyes celláinak viselkedését a diavetítéscella eszköztárával konfigurálhatja. A cellatípusok határozzák meg a navigációs gombok viselkedését.

## <a name="the-anatomy-of-a-slideshow"></a>A diavetítés anatómiája

Ha veszel egy véletlenszerű notebook, és használja a diavetítés, akkor általában úgy találja, hogy az összes cella összekeveredett, és sok a tartalom rejtve le az alján a böngésző ablakban. A hatékony bemutatóhoz a Diavetítéscella eszköztár segítségével minden cellához diavetítés-típust kell rendelnie:

1. A **Nézet** menüben válassza a Celltoolbar Slideshow ( **Cellaeszköztár** > **diavetítése) parancsot:**

    ![A cellavetítés eszköztárának bekapcsolása](media/slideshow/slideshow-view-cell-toolbar.png)

1. A **Diatípus** legördülő lista a Jegyzetfüzet minden cellájának jobb felső részén megjelenik:

    ![Cellavetítés eszköztár](media/slideshow/slideshow-cell-toolbar.png)

1. Minden cellához válasszon öt típust:

    ![Celladiavetítés típusai](media/slideshow/slideshow-cell-slide-types.png)

    | Dia típusa | Viselkedés |
    | --- | --- |
    | - (nincs beállítva) | Cella jelenik meg az előző cella, ami gyakran nem a kívánt hatást a diavetítés. |
    | Csúszik | A cella egy elsődleges dia, amely a navigációs vezérlő bal és jobb nyilaival navigálható. |
    | Aldia | A cella "alatt" van egy elsődleges dia, amely a navigációs vezérlő lefelé mutató nyilával navigál. A felfelé mutató nyíl visszatér az elsődleges diához. Az aldiák olyan másodlagos anyagokhoz használatosak, amelyeket a bemutató fő útvonalára ugorhat, de szükség esetén könnyen elérhetővé válnak. |
    | Töredék | A cellatartalom az előző vagy aldia környezetében jelenik meg a lefelé mutató navigációs nyíl használatakor (a mutató a felfelé mutató nyíl használatakor egy töredék törlődik). A kódcellával rendelkező töredékeket használhatunk, hogy a kód megjelenjen a dián belül, vagy több töredékkel is beállíthatja, hogy a szöveglistajelek egyenként jelenjenek meg (lásd a következő szakaszban található példát). Mivel a töredékek az aktuális diára épülnek, a felesleges töredékek nem lesznek láthatók a böngészőablak alján. |
    | Kihagyás | A cella nem jelenik meg a diavetítésben. |
    | Megjegyzések | A cella hangszóróként tartalmaz jegyzeteket, amelyek nem jelennek meg a diavetítésben. |

1. Kezdetben hasznos, ha minden cellához a Dia lehetőséget **választja.** Ezután futtathatja a diavetítést, és elvégezheti a megfelelő módosításokat.

### <a name="example-fragment-cells-for-bullet-items"></a>Példa: felsorolásjelek töredékcellái

Ha azt szeretné, hogy a dián lévő felsorolásjelek egyenként jelenjenek meg, helyezze a diafejlécet egy **Dia** típusú Markdown cellába, majd helyezze az egyes felsorolásjeleket egy külön **Tagrészlet** típusú Markdown cellába:

![Példa több Markdown-cella létrehozására listajelelemekhez](media/slideshow/slideshow-fragments.png)

Mivel a diavetítés függőlegesebb térközöket eredményez, mint amikor az összes listajel ugyanabban a cellában van, előfordulhat, hogy nem tud annyi felsorolásjelet használni.

## <a name="run-the-slideshow"></a>Diavetítés futtatása

1. Ha bármilyen Markdown-cellát szerkesztettél, mindenképpen futtassd őket a HTML-kód megjelenítéséhez, különben *markdownként* jelennek meg a diavetítésben.

1. Miután beállította az egyes **mobilcellák diatípusát,** jelölje ki azt a cellát, amellyel a diavetítést el szeretné indítani, majd válassza az **Enter/Exit RISE Diavetítés** gombot a fő eszköztáron:

    ![A RISE diavetítésének be- és kilépésgombja a fő eszköztáron](media/slideshow/slideshow-start.png)

1. A diák és a töredékek közötti navigáláshoz használja a navigációs vezérlő bal és jobb oldali nyilát. A vezérlőben lévő szöveg a *slide.sub-slide*-t jelölő számot jeleníti meg.

    ![Diavetítés navigációs vezérlője](media/slideshow/slideshow-navigation-control.png)

1. A diák és az aldiák, valamint a töredékek közötti navigáláshoz használja a felfelé és lefelé mutató nyilakat, ha ez engedélyezve van:

    ![Diavetítésnavigációs vezérlők aldiákhoz](media/slideshow/slideshow-navigation-control-subslide.png)

1. Egy kódcellában a lejátszás gombbal futtassa a kódot; kimenet jelenik meg a dián:

    ![Lejátszás gomb kódcella futtatásához](media/slideshow/slideshow-run-code-cell.png)

    ![A kódcella kimenete megjelenik a diavetítésben](media/slideshow/slideshow-run-code-cell-output.png)

    > [!Tip]
    > A cellakimenet a diavetítés cellájának részének számít. Ha egy cellát jegyzetfüzetben vagy diavetítés nézetben futtat, a kimenet a másik nézetben is megjelenik. A kimenet törléséhez használja a **Cellcurrent** > Outputs**Clear** **parancsot** > (az aktuális cellához) vagy az Összes **kimenet** > **törlése parancsot** > **(az** összes cellához).

1. Ha végzett a diavetítéssel, az **X** segítségével térjen vissza a jegyzetfüzetnézetbe.

## <a name="next-steps"></a>További lépések

- [Útmutató: Projektek konfigurálása és kezelése](configure-manage-azure-notebooks-projects.md)
- [Útmutató: Csomagok telepítése jegyzetfüzetből](install-packages-jupyter-notebook.md)
- [Útmutató: Adatfájlok kalvaló megoldása](work-with-project-data-files.md)
- [Útmutató: Adaterőforrások elérése](access-data-resources-jupyter-notebooks.md)
