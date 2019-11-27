---
title: Jelenleg a Jupyter notebook az Azure-ban diavetítésként
description: Hogyan cellák diavetítés mód konfigurálása a Jupyter notebook majd jelentenek a diavetítés a elő kiterjesztéssel.
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: d180eaf571fa57191e3b0856020b02f05d05e344
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277596"
---
# <a name="run-a-notebook-slideshow"></a>A notebook diavetítés futtatása

Az Azure-jegyzetfüzeteket a Jupyter/IPython diavetítés bővítmény (EGYRE), amely lehetővé teszi egy jegyzetfüzetet diavetítésként közvetlenül jelen előre konfigurálva. Egy diavetítés cellák általában megjelenített egyszerre csak egy használatával betűtípus méretének megfelelő jelentő nagy képernyők, és továbbra is futtathatja a kódot az átváltás egy külön bemutató számítógép helyett.

Az alábbi képen látható, a standard notebook nézet, amely látható Markdown és a kód minden egy helyen:

![A notebook standard nézetben](media/slideshow/slideshow-notebook-view.png)

A diavetítés indításakor az első cella kibővül a böngésző kitöltéséhez, ahol az **X** a bal felső sarokban kilép a diavetítésből **?** a bal alsó jeleníti meg a billentyűparancsok, és a jobb alsó sarkában lévő nyilakra sorrendjének megváltoztatása:

![A notebook diavetítés módban](media/slideshow/slideshow-slide-view.png)

Diavetítés jegyzetfüzet előkészítése magában foglalja a két elsődleges tevékenység:

1. Markdown cellák a nagy betűk jelennek meg, mert néhány tartalmat nem lehet a diavetítés látható. Így általában korlátozza a szöveg bármilyen adott cella; négy-hat sorok fejlécet általában működik a legjobban. Ha még több szöveg, felosztása több cellákba ezt az információt.

2. Konfigurálja az egyes cellákban viselkedését a diavetítés diavetítés cella eszköztárának. Cella típusa határozza meg, a navigációs gombok viselkedését.

## <a name="the-anatomy-of-a-slideshow"></a>Diavetítés leírása

Ha igénybe vehet egy véletlenszerű jegyzetfüzetet, és használhatja azt egy diavetítés, általában találja, hogy minden a cellák vannak Táncoló együtt, és nagy része a tartalmat el van rejtve a böngészőablak alján. Ahhoz, hogy egy hatékony bemutató, ezt követően, hozzá kell rendelni egy diavetítés típus minden cella diavetítés cella eszköztárának:

1. A **nézet** menüben válassza a **cella eszköztár** > **Slideshow**elemet:

    ![A cella diavetítés eszköztár bekapcsolása](media/slideshow/slideshow-view-cell-toolbar.png)

1. A Jegyzetfüzet minden cellájának jobb felső sarkában megjelenik egy **dia típusa** legördülő menü:

    ![Cella diavetítés eszköztár](media/slideshow/slideshow-cell-toolbar.png)

1. Válassza minden cella öt típus egyikére:

    ![Cella diavetítés típusok](media/slideshow/slideshow-cell-slide-types.png)

    | Húzza az ujját típusa | Viselkedés |
    | --- | --- |
    | -(nincs beállítva) | Az előző cella, amely gyakran nem diavetítésként effektusokat cella jelenik meg. |
    | Dia | Cella nem elsődleges dia nyit meg, a jobb és bal oldali navigációs ovládacího prvku nyilak használatával. |
    | Alárendelt dia | Cella nem "az átlagosnál" elsődleges dia nyit meg, a lefelé mutató nyílra a navigációs vezérlőelem használatával. A felfelé mutató nyílra az elsődleges diára adja vissza. Diák alárendelt másodlagos anyagot találhat az, hogy a fő elérési úton a bemutató kihagyása sikerült, de szükség esetén azonnal elérhető szolgálnak. |
    | Fragment | A navigációs nyílra (egy kódrészletet a rendszer eltávolítja a felfelé mutató nyílra használatakor) használatakor cella tartalmát az előző vagy a részleges dia keretében jelenik meg. Használata egy kódrészletet egy kódcellát, hogy a dián jelenik meg a kódot, vagy több töredékkel használhatja, hogy a szöveg jelenik meg a felsorolás egyenként (lásd a példát a következő szakaszban). Töredékek száma a jelenlegi dián hozhat létre, mert töredékek nem lesz látható a böngészőablak alján. |
    | Kihagyás | Cella nem látható a diavetítést. |
    | Megjegyzések | A cella, előadói jegyzeteket, amelyek nem láthatók a diavetítés tartalmazza. |

1. Először is hasznos lehet minden cella esetében kiválasztani a **diát** . Ezután futtassa a diavetítés, és győződjön meg a megfelelő módosításokat.

### <a name="example-fragment-cells-for-bullet-items"></a>Példa: töredék cellák listajele elemek

Ahhoz, hogy a felsorolásjelek egy dián legyenek láthatók, helyezze a dia fejlécét egy Markdown-cellába, majd helyezze **el az egyes** felsorolásjeleket egy különálló Markdown-cellába a **töredék** típussal:

![Példa a több Markdown cellák listajele elemek létrehozása](media/slideshow/slideshow-fragments.png)

A diavetítés további függőleges térköz, mint ha a pontok megjelenítésében a cellában töredékekre megjelenítve, mert nem lehet használható listajeles elemeket.

## <a name="run-the-slideshow"></a>Futtassa a diavetítés

1. Ha bármilyen Markdown-cellát szerkesztett, ne felejtse el futtatni őket a HTML-fájl megjelenítéséhez, ellenkező esetben a *Diavetítés Markdown jelenik* meg.

1. Miután konfigurálta az egyes cellákhoz tartozó **dia típusát** , válassza ki azt a cellát, amelyből el szeretné indítani a diavetítést, majd a fő eszköztáron válassza a **beírás/kilépés emelkedése** gombot:

    ![Adja meg és kilépési NÖVEKMÉNYE diavetítés a fő eszköztár gombja](media/slideshow/slideshow-start.png)

1. Navigálás a diák, valamint a szilánkok között, használja a jobb és bal nyíl a navigációs vezérlőelemben. A vezérlőelemben lévő szöveg a *dia. sub-slide*számot jelképező számot jeleníti meg.

    ![Diavetítés navigációs vezérlőelem](media/slideshow/slideshow-navigation-control.png)

1. Nyissa meg a diák, és alárendelt diák, valamint a szilánkok között, a fel és nyilak, ha engedélyezve van:

    ![Diavetítés a lapnavigálási vezérlők alárendelt diák](media/slideshow/slideshow-navigation-control-subslide.png)

1. A kód a cella a lejátszás gombra segítségével futtassa a kódot; kimenet a dián jelenik meg:

    ![A kódcella futtatásához lejátszás gombra.](media/slideshow/slideshow-run-code-cell.png)

    ![Kód cella kimenet jelenik meg a diavetítést](media/slideshow/slideshow-run-code-cell-output.png)

    > [!Tip]
    > Cella kimeneti diavetítésként a cella részének számít. Ha futtatta egy cella notebook vagy Diavetítés nézetben, a kimeneti nézetben jelenik meg a más is. A kimenet törléséhez használja a **cellát** > **aktuális kimeneteket** > **törölje** a parancsot (az aktuális cella esetében) vagy a **cellát** , > az **összes kimenetet** > **törölje** (az összes cella esetében).

1. Ha elkészült a diavetítéssel, az **X** használatával térjen vissza a jegyzetfüzet nézetbe.

## <a name="next-steps"></a>Következő lépések

- [Útmutató: projektek konfigurálása és kezelése](configure-manage-azure-notebooks-projects.md)
- [Útmutató: csomagok telepítése jegyzetfüzetből](install-packages-jupyter-notebook.md)
- [Útmutató: az adatfájlok használata](work-with-project-data-files.md)
- [Útmutató: az adaterőforrások elérése](access-data-resources-jupyter-notebooks.md)
