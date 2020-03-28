---
title: Oktatóanyag – Jupyter-jegyzetfüzet létrehozása és futtatása – Azure Notebookelőzetes verzió
description: Ismerje meg, hogyan hozhat létre és futtathat jupyter-jegyzetfüzetet az Azure Notebookelőzetes verzióban, amely bemutatja a lineáris regresszió folyamatát az adatelemzésben.
ms.topic: tutorial
ms.date: 01/11/2019
ms.openlocfilehash: 8a1c13f41ef1588b040b3540b852d83764c6ce79
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75660817"
---
# <a name="tutorial-create-and-run-a-jupyter-notebook-with-python"></a>Oktatóanyag: Jupyter-jegyzetfüzet létrehozása és futtatása Pythonnal

Ez az oktatóanyag végigvezeti az Azure Notebookok használatával egy teljes Jupyter-jegyzetfüzet létrehozásának folyamatán, amely bemutatja az egyszerű lineáris regressziót. Az oktatóanyag során megismerkedhet a Jupyter notebook felhasználói felületével, amely magában foglalja a különböző cellák létrehozását, a cellák futtatását és a jegyzetfüzet diavetítésként való bemutatását.

A kitöltött jegyzetfüzet megtalálható a [GitHubon – Az Azure-jegyzetfüzetminták.](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps) Ez az oktatóanyag azonban egy új projekttel és egy üres jegyzetfüzettel kezdődik, így lépésről lépésre megtapasztalhatja a létrehozását.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Projektjegyzetfüzet létrehozása néhány mintaadattal
> * A jegyzetfüzet felülete segítségével különböző típusú cellákat hozhat létre
> * A notebook futtatása
> * A jegyzetfüzet mentése
> * A jegyzetfüzet hibakeresése a Visual Studio-kódban

## <a name="create-the-project"></a>A projekt létrehozása

1. Nyissa meg az [Azure-jegyzetfüzeteket,](https://notebooks.azure.com) és jelentkezzen be. (További részletek: [Rövid útmutató – Bejelentkezés az Azure Notebooks ba).](quickstart-sign-in-azure-notebooks.md)

1. A nyilvános profil lapon válassza a **Saját projektek** lehetőséget a lap tetején:

    ![A Saját projektek hivatkozás a böngészőablak tetején](media/quickstarts/my-projects-link.png)

1. A **Saját projektek** lapon válassza a **+ Új projekt** (billentyűparancs: n); a gomb csak **+** akkor jelenhet meg, ha a böngészőablak keskeny:

    ![Új projekt parancs a Saját projektek lapon](media/quickstarts/new-project-command.png)

1. A megjelenő **Új projekt** létrehozása előugró ablakban adja meg vagy állítsa be a következő részleteket, majd válassza a **Létrehozás**lehetőséget:

   - **Projekt neve**: Lineáris regressziós példa - Cricket Chirps
   - **Projektazonosító:** lineáris regressziós példa
   - **Nyilvános projekt**: (törölve)
   - **README.md létrehozása**: (törölve)

1. Néhány pillanat múlva az Azure Notebooks navigál az új projekthez.

## <a name="create-the-data-file"></a>Az adatfájl létrehozása

A jegyzetfüzetben létrehozott lineáris regressziós modell a projekt *egy cricket_chirps.csv*nevű fájljából rajzol adatokat. Ezt a fájlt a [GitHub – Azure Notebook minták](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps)másolásával vagy az adatok közvetlen megadásával hozhatja létre. A következő szakaszok mindkét megközelítést ismertetik.

### <a name="upload-the-data-file"></a>Az adatfájl feltöltése

1. Az Azure-jegyzetfüzetek projektirányítópultján válassza a **Feltöltés** > **URL-címről** lehetőséget.
1. Az előugró ablakban írja be a következő URL-címet a **Fájl URL-címében** és *cricket_chirps.csv* **fájlnév**mezőbe, majd válassza a **Kész gombot.**

    ```url
    https://raw.githubusercontent.com/Microsoft/AzureNotebooks/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps/cricket_chirps.csv
    ```

1. A *cricket_chirps.csv* fájl nak most meg kell jelennie a projekt fájllistájában:

    ![Újonnan létrehozott CSV-fájl jelenik meg a projektfájl listában](media/tutorial/csv-file-in-project.png)

### <a name="create-a-file-from-scratch"></a>Teljesen új fájl létrehozása

1. Az Azure Notebooks projektirányítópultján válassza a **+ Új** > **üres fájl lehetőséget**
1. Egy mező jelenik meg a projekt fájllistájában. Írja be *cricket_chirps.csv,* és nyomja le az Enter billentyűt.
1. Kattintson a jobb gombbal *a cricket_chirps.csv* fájlra, és válassza **a Fájl szerkesztése parancsot.**
1. A megjelenő szerkesztőben adja meg a következő adatokat:

    ```csv
    Chirps/Minute,Temperature
    20,88.6
    16,71.6
    19.8,93.3
    18.4,84.3
    17.1,80.6
    15.5,75.2
    14.7,69.7
    17.1,82
    15.4,69.4
    16.2,83.3
    15,79.6
    17.2,82.6
    16,80.6
    17,83.5
    14.4,76.3
    ```

1. A **Fájl mentése gombra** a fájl mentéséhez és a projekt irányítópultjára való visszatéréshez válassza a Fájl mentése lehetőséget.

## <a name="install-project-level-packages"></a>Projektszintű csomagok telepítése

A jegyzetfüzeten belül mindig használhatja `!pip install` a szükséges csomagok telepítéséhez hasonló parancsokat. Az ilyen parancsok azonban minden alkalommal futnak, amikor a jegyzetfüzet kódcelláit futtatja, és jelentős időt vehet igénybe. Ezért ehelyett a csomagokat projektszinten telepítheti egy `requirements.txt` fájl használatával.

1. A [Teljesen új fájl létrehozása](#create-a-file-from-scratch) című részben leírt `requirements.txt` eljárással a következő tartalommal elnevezett fájlt hozhat létre:

    ```text
    matplotlib==3.0.0
    numpy==1.15.0
    pandas==0.23.4
    scikit-learn==0.20.0
    ```

    Az `requirements.txt` [adatfájl feltöltése](#upload-the-data-file)című részében leírtak szerint a helyi számítógépről is feltölthet egy fájlt.

1. A projekt irányítópultján válassza a **Projektbeállítások lehetőséget.**
1. A megjelenő előugró ablakban válassza a **Környezet** lapot, majd a **+Hozzáadás gombot.**
1. A **Környezet beállítása lépések**csoport első legördülő vezérlőjében (a műveletben) válassza a **Requirements.txt**fájl lehetőséget.
1. A második legördülő vezérlőben (a fájl neve) válassza a *requirements.txt* (a létrehozott fájl) lehetőséget.
1. A harmadik legördülő listában (a Python-verzióban) válassza a **Python 3.6-os verzióját.**
1. Kattintson a **Mentés** gombra.

![A Project Beállítások környezet lap egy requirements.txt fájlt ad meg](media/tutorial/tutorial-requirements-txt.png)

Ezzel a beállítási lépéssel a projektben futtatott jegyzetfüzetek olyan környezetben futnak, ahol a csomagok telepítve vannak.

## <a name="create-and-run-a-notebook"></a>Notebook létrehozása és futtatása

Ha az adatfájl készen áll, és a projektkörnyezet be van állítva, most már létrehozhatja és megnyithatja a jegyzetfüzetet.

1. A projekt irányítópultján válassza a **+ Új** > **jegyzetfüzet**lehetőséget.
1. Az előugró ablakban írja be a *Lineáris regressziós példa - Cricket Chirps.ipynb parancsot* **az Elemnév**mezőbe, válassza a **Python 3.6** lehetőséget a nyelvhez, majd válassza az **Új**lehetőséget.
1. Miután az új jegyzetfüzet megjelenik a fájllistában, jelölje ki a jegyzetfüzet elindításához. Automatikusan megnyílik egy új böngészőlap.
1. Mivel a környezetbeállításaiban van egy *requirements.txt* fájl, a következő üzenet jelenik meg: "Várakozás a tároló előkészítése befejezésére.". Az **OK gombra** választva bezárhatja az üzenetet, és folytathatja a munkát a jegyzetfüzetben; kódcellákat azonban nem futtathat, amíg a környezet teljesen be nem van állítva.
1. A jegyzetfüzet a Jupyter-csatolóban nyílik meg, alapértelmezettként egyetlen üres kódcellával.

    [![Új jegyzetfüzet kezdeti nézete az Azure-jegyzetfüzetekben](media/tutorial/tutorial-new-notebook.png)](media/tutorial/tutorial-new-notebook.png#lightbox)

## <a name="tour-the-notebook-interface"></a>A jegyzetfüzet felületének bejárása

A jegyzetfüzet futásával hozzáadhat kódot és Markdown-cellákat, futtathatja ezeket a cellákat, és kezelheti a jegyzetfüzet működését. Először azonban érdemes néhány percet igénybe venni, hogy megismerkedjen a felülettel. A teljes dokumentációhoz válassza a **Súgójegyzetfüzet** > **súgója** menüparancsot.

Az ablak tetején a következő elemek láthatók:

(A) A jegyzetfüzet neve, amelyet kattintással szerkeszthet.
(B) Gombok a tartalmazó projektre és a projektek irányítópultjára való navigáláshoz, amelyek új lapokat nyitnak meg a böngészőben.
(C) A notebookkal való munka parancsaival ellátott menü.
(D) egy eszköztár, amely a közös műveletek parancsikonjait tartalmazza.
(E) a cellákat tartalmazó vágóvászon.
(F) annak jelzése, hogy a jegyzetfüzet megbízható-e (az alapértelmezett **érték nem megbízható).**
(G) a notebook futtatásához használt kernel egy tevékenységjelzővel együtt.

[![A Jupyter-felület elsődleges felhasználói felülete](media/tutorial/tutorial-notebook-ui.png)](media/tutorial/tutorial-notebook-ui.png#lightbox)

A Jupyter beépített bemutatót biztosít az elsődleges felhasználói felület elemeiről. Indítsa el a bemutatót a **Súgó** > **felhasználói felülete bemutató** parancs kiválasztásával, majd a felugró ablakokra kattintva.

A menüparancsok csoportjai a következők:

| Menü | Leírás |
| --- | --- |
| Fájl | A jegyzetfüzetfájl kezelésére szolgáló parancsok, beleértve a jegyzetfüzetek létrehozására és másolására, a nyomtatási kép megjelenítésére, valamint a jegyzetfüzet különböző formátumokban történő letöltésére szolgáló parancsokat. |
| Szerkesztés | Cellák kivágására, másolására és beillesztésére, értékek keresésére és cseréjére, cellamellékletek kezelésére és képek beszúrására szolgáló tipikus parancsok.  |
| Nézet | Parancsok a Jupyter felhasználói felület különböző részei láthatóságának szabályozására. |
| Beszúrás | Parancsok: új cella beszúrása az aktuális cella fölé vagy alá. Ezeket a parancsokat gyakran használja jegyzetfüzet létrehozásakor. |
| Cella | A különböző **Futtatás** parancsok egy vagy több cellát futtatnak különböző kombinációkban. A **Cellatípus** parancsok megváltoztatják a cella típusát a **Kód**, **Markdown**és **Raw NBConvert** (egyszerű szöveg) között. Az **Aktuális kimenetek** és az Összes kimenet parancs **szabályozza** a futtatási kód kimenetének megjelenítését, és tartalmaz egy parancsot az összes kimenet törléséhez. |
| Kernel | A kód kernelben való futtatásának módját kezelő parancsok, valamint a **Kernel módosítása** a jegyzetfüzet futtatásához használt nyelv vagy Python-verzió módosításához. |
| Adatok | Fájlok feltöltésére és letöltésére a projektből vagy munkamenetből. Lásd: [Projektadatfájlok munkája](work-with-project-data-files.md) |
| Vezérlők | A [Jupyter widgetek](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Basics.html)kezelésére szolgáló parancsok, amelyek további vizualizációs, leképezési és ábrázolási lehetőségeket biztosítanak.|
| Súgó | A Jupyter-felülethez segítséget és dokumentációt biztosító parancsok. |

Az eszköztár legtöbb parancsa egyenértékű menüparancsokkal rendelkezik. Az egyik kivétel az **Enter /Edit RISE Diavetítés**, amely a Jegyzetfüzetek megosztása és bemutatása című kiállításon kerül megvitatásra. [Share and present notebooks](present-jupyter-notebooks-slideshow.md)

A jegyzetfüzet feltöltésekor számos ilyen parancsot használhat.

## <a name="create-a-markdown-cell"></a>Markdown cella létrehozása

1. Kattintson a jegyzetfüzetvásznon látható első üres cellára. Alapértelmezés szerint a cella **kódtípus,** ami azt jelenti, hogy a kijelölt kernel (Python, R vagy F#) futtatható kódját tartalmazza. Az aktuális típus az eszköztár típuslegördülő menüjében látható:

    ![Cellatípus eszköztár legördülő menü](media/tutorial/tutorial-cell-type-drop-down.png)

1. Módosítsa a cellatípust **Markdown** úrra az eszköztár legördülő menüjével; felváltva használja a **CellCell** > **Type** > **Markdown** menüparancsot:

    ![Cellatípus menü parancs](media/tutorial/tutorial-cell-type-menu.png)

1. Kattintson a cellába a szerkesztés megkezdéséhez, majd írja be a következő Markdown értéket:

    ```markdown
    # Example Linear Regression

    This notebook contains a walkthrough of a simple linear regression. The data, obtained from [college.cengage.com](https://college.cengage.com/mathematics/brase/understandable_statistics/7e/students/datasets/slr/frames/frame.html), relates the rate of cricket chirps to temperature from *The Song of Insects*, by Dr. G. W. Pierce, Harvard College Press.

    In this example we're using the count of chirps per minute as the independent varible to then predict the dependent variable, temperature. In short, we're using a little data science to make ourselves a cricket thermometer. (You could also reverse the data and use temperature to predict the number of chirps, but it's more fun to use crickets as the thermometer itself!)

    The methods shown in this example follow what's presented in the Udemy course, [Machine Learning A to Z](https://www.udemy.com/machinelearning/learn/v4/).

    A lovely aspect of Notebooks is that you can use Markdown cells to explain what the code is doing rather than code comments. There are several benefits to doing so:

    - Markdown allows for richer text formatting, like *italics*, **bold**, `inline code`, hyperlinks, and headers.
    - Markdown cells automatically word wrap whereas code cells do not. Code comments typically use explicit line breaks for formatting, but that's not necessary in Markdown.
    - Using Markdown cells makes it easier to run the Notebook as a slide show.
    - Markdown cells help you remove lengthy comments from the code, making the code easier to scan.

    When you run a code cell, Jupyter executes the code; when you run a Markdown cell, Jupyter renders all the formatting into text that's suitable for presentation.
    ```

1. Ha a korrektúrát HTML formátumba szeretné alakítani a böngészőben, jelölje ki a **Futtatás** parancsot az eszköztáron, vagy használja a**Cellafuttatási** **Cell** > cellák parancsot. A formázáshoz és hivatkozásokhoz való Markdown-kód mostantól úgy jelenik meg, ahogy azt a böngészőben elvárná.

1. A jegyzetfüzet utolsó cellájának futtatásakor a Jupyter automatikusan létrehoz egy új cellát a futtatott cella alatt. Tegyen több Markdown-t ebbe a cellába az ebben a szakaszban található lépések megismétlésével a következő Markdown-nal:

    ```markdown
    ## Install packages using pip or conda

    Because the code in your notebook likely uses some Python packages, you need to make sure the Notebook environment contains those packages. You can do this directly within the notebook in a code block that contains the appropriate pip or conda commands prefixed by `!`:

    \```
    !pip install <pkg name>

    !conda install <pkg name> -y
    \```
    ```

1. A Markdown ismételt szerkesztéséhez kattintson duplán a renderelt cellára. Ha a módosítások végrehajtása után ismét meg szeretné jelenni a HTML függvényt, futtassa a cellát.

## <a name="create-a-code-cell-with-commands"></a>Kódcella létrehozása parancsokkal

Ahogy az előző Markdown cella kifejtette, közvetlenül a jegyzetfüzetbe is felvehet parancsokat. A parancsok segítségével csomagokat telepíthet, futtathatja a curl-t vagy a wget-et az adatok lekéréséhez, vagy bármi máshoz. A Jupyter notebookok hatékonyan futnak egy Linux virtuális gépen belül, így a teljes Linux parancs be van állítva a munkahoz.

1. Írja be az alábbi parancsokat a kódcellába, amely a **Futtatás** az előző Markdown cellában való használat után jelent meg. Ha nem lát új cellát, hozzon létre egyet a **+** **Cella beszúrása** > **alatt,** vagy használja az eszköztár gombját.

    ```bash
    !pip install numpy
    !pip install matplotlib
    !pip install pandas
    !pip install sklearn
    ```

1. A cella futtatása előtt hozzon létre egy új cellát az **+** eszköztár gombjával, állítsa markdown-ra, és írja be a következő magyarázatot:

    ```markdown
    Note that when you run a code block that contains install commands, and also those with `import` statements, it make take the notebooks a little time to complete the task. To the left of the code block you see `In [*]` to indicate that execution is happening. The Notebook's kernel on the upper right also shows a filled-in circle to indicate "busy."
    ```

1. A **jegyzetfüzet** > összes cellájának futtatásához jelölje ki a**CellA futtatása az összes** et. Figyelje meg, hogy a Markdown cellák HTML-ként jelennek meg, és a parancs a kernelben fut, és tartsa be a kernel jelzőjét a Markdown-ban leírtak szerint:

    ![Foglaltságjelző a jegyzetfüzet kerneléhez](media/tutorial/tutorial-kernel-busy.png)

1. Az összes `pip install` parancs futtatásához is kis időt vesz igénybe, és mivel már telepítette ezeket a csomagokat a projektkörnyezetben (és mivel alapértelmezés szerint az Azure Notebookok ban is szerepelnek), számos olyan üzenet jelenik meg, amelyek en a "Követelmény már teljesült". Az összes kimenet vizuálisan zavaró lehet, ezért jelölje ki a cellát (egyetlen kattintással), majd használja a > **Cellcella kimenetek** > **váltást** a kimenet elrejtéséhez. **Cell** Ugyanebben az almenüben a **Törlés** paranccsal is teljesen eltávolíthatja a kimenetet.

    A **Váltó** parancs csak a cella legutóbbi kimenetét rejti el; ha újra futtatja a cellát, a kimenet újra megjelenik.

1. Mivel a csomagok a projektkörnyezetben vannak `! pip install` telepítve, `#`fűzzön megjegyzéseket a parancsokhoz a ; ily módon maradhatnak a notebook, mint oktatási anyag, de nem fog semmilyen időt futtatni, és nem termel felesleges kimenetet. Ebben az esetben a megjegyzéssel kapcsolatos parancsok jegyzetfüzetben tartása a jegyzetfüzet függőségeit is jelzi.

    ```bash
    # !pip install numpy
    # !pip install matplotlib
    # !pip install pandas
    # !pip install sklearn
    ```

## <a name="create-the-remaining-cells"></a>A fennmaradó cellák létrehozása

A jegyzetfüzet többi részének feltöltéséhez ezután hozzon létre egy sor Markdown- és kódcellát. Az alább felsorolt minden cellához először hozza létre az új cellát, majd állítsa be a típust, majd illessze be a tartalmat.

Bár az egyes cellák létrehozása után megvárhatja a jegyzetfüzet futtatását, érdemes minden cellát létrehozáskor futtatni. Nem minden cella mutat kimenetet; Ha nem lát semmilyen hibát, tegyük fel, hogy a cella normálisan futott.

Minden kódcella az előző cellákban futtatott kódtól függ, és ha elhanyagolja az egyik cella futtatását, a későbbi cellák hibákat okozhatnak. Ha úgy találja, hogy elfelejtett futtatni egy cellát, próbálja meg használni a **Cell** > **Run All Above programot,** mielőtt futtatja az aktuális cellát.

Ha nem várt eredményeket lát (ami valószínűleg így lesz!), ellenőrizze, hogy minden cella "Kód" vagy "Markdown" értékre van-e beállítva, ha szükséges. Például egy "Érvénytelen szintaxis" hiba általában akkor fordul elő, ha beírta a Markdown t a Kódcellába.

1. Markdown cella:

    ```markdown
    ## Import packages and prepare the dataset

    In this example we're using numpy, pandas, and matplotlib. Data is in the file cricket_chirps.csv. Because this file is in the same project as this present Notebook, we can just load it using a relative pathname.
    ```

1. Kódcella; futtatáskor a tábla tartalmát kimenetként jeleníti meg. A kimenetet letilthatja a `print` kimutatás megjegyzésével.

    ```python
    import numpy as np
    import pandas as pd

    dataset = pd.read_csv('cricket_chirps.csv')
    print(dataset)
    X = dataset.iloc[:, :-1].values  # Matrix of independent variables -- remove the last column in this data set
    y = dataset.iloc[:, 1].values    # Matrix of dependent variables -- just the last column (1 == 2nd column)
    ```

    > [!Note]
    > A kódból figyelmeztetések jelenhetnek meg a "numpy.dtype size changed" címmel kapcsolatban; a figyelmeztetéseket biztonságosan figyelmen kívül lehet hagyni.

1. Markdown cella:

    ```markdown
    Next, split the dataset into a Training set (2/3rds) and Test set (1/3rd). We don't need to do any feature scaling because there is only one column of independent variables, and packages typically do scaling for you.
    ```

1. Kódcella; futtatáskor ez a cella nem rendelkezik kimenettel.

    ```python
    from sklearn.model_selection import train_test_split

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 1/3, random_state = 0)
    ```

1. Markdown cella:

    ```markdown
    ## Fit the data to the training set

    "Fitting" the data to a training set means making the line that describes the relationship between the independent and the dependent variables. With a simple data set like we're using here, you can visualize the line on a simple x-y plot: the x-axis is the independent variable (chirp count in this example), and the y-axis is the independent variable (temperature). Fitting the data means plotting all the points in the training set, then drawing the best-fit line through that data.

    With two independent variables you can imagine a three-dimensional plot with a line fitted to the data. At three or more independent variables, however, it's no longer easy to visualize the fit, but you get the idea. In the end, it's all just mathematics, which a computer can handle easily without having to form a mental picture!

    The regressor's `fit` method here creates the line, which algebraically is of the form `y = x*b1 + b0`, where b1 is the coefficient or slope of the line (which you can get to through `regressor.coef_`), and b0 is the intercept of the line at x=0 (which you can get to through `regressor.intercept_`).
    ```

1. Kódcella; futtatáskor ez a cella `LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None,normalize=False)`a kimenetet mutatja, .

    ```python
    from sklearn.linear_model import LinearRegression

    regressor = LinearRegression()    # This object is the regressor, that does the regression
    regressor.fit(X_train, y_train)   # Provide training data so the machine can learn to predict using a learned model.
    ```

1. Markdown cella:

    ```markdown
    ## Predict the results

    With the regressor in hand, we can predict the test set results using its `predict` method. That method takes a vector of independent variables for which you want predictions.

    Because the regressor is fit to the data by virtue of `coef_` and `intercept_`, a prediction is the result of `coef_ * x + intercept_`. (Indeed, `predict(0)` returns `intercept_` and `predict(1)` returns `intercept_ + coef_`.)

    In the code, the `y_test` matrix (from when we split the set) contains the real observations. `y_pred` assigned here contains the predictions for the same `X_test` inputs. It's not expected that the test or training points exactly fit the regression; the regression is trying to find the model that we can use to make predictions with new observations of the independent variables.
    ```

1. Kódcella; futtatáskor ez a cella a következőhoz hasonló `[79.49588055 75.98873911 77.87719989 80.03544077 75.17939878]`eredményeket jelenít meg: .

    ```python
    y_pred = regressor.predict(X_test)
    print(y_pred)
    ```

1. Markdown cella:

    ```markdown
    It's interesting to think that all the "predictions" we use in daily life, like weather forecasts, are just regression models of some sort working with various data sets. Those models are much more complicated than what's shown here, but the idea is the same.

    Knowing how predictions work help us understand that the actual observations we would collect in the moment will always be somewhat off from the predictions: the predictions fit exactly to the model, whereas the observations typically won't.

    Of course, such systems feed new observations back into the dataset to continually improve the model, meaning that predictions should get more accurate over time.

    The challenge is determining what data to actually use. For example, with weather, how far back in time do you go? How have weather patterns been changing decade by decade? In any case, something like weather predictions will be doing things hour by hour, day by day, for things like temperature, precipitation, winds, cloud cover, etc. Radar and other observations are of course fed into the model and the predictions are reduced to mathematics.
    ```

1. Markdown cella:

    ```markdown
    ## Visualize the results

    The following code generates a plot: green dots are training data, red dots are test data, blue dots are predictions. Gray line is the regression itself. You see that all the blue dots are exactly on the line, as they should be, because the predictions exactly fit the model (the line).
    ```

1. Kódcella; futtatáskor ez a cella grafikus nyomtatást eredményez. Ha nem látja a nyomtatást az első alkalommal (és ehelyett a "640x480-as ábra 1 fejjel) című témakört találja), futtassa újra a cellát.

    ```python
    import matplotlib.pyplot as plt

    plt.scatter(X_train, y_train, color = 'green')
    plt.scatter(X_test, y_test, color = 'red')   
    plt.scatter(X_test, y_pred, color = 'blue')  # The predicted temperatures of the same X_test input.
    plt.plot(X_train, regressor.predict(X_train), color = 'gray')
    plt.title('Temperature based on chirp count')
    plt.xlabel('Chirps/minute')
    plt.ylabel('Temperature')
    plt.show()
    ```

    ![Telekkimenet a matplotlib kódból](media/tutorial/tutorial-plot-output.png)

1. Markdown cella:

    ```markdown
    ## Closing comments

    At the end of the day, when you create a model, you use training data. Then you start feeding test data (real observations) to see how well the model actually works. You may find that the model is a little inaccurate over time, in which case you retrain the model with some new data. Retraining the model means you're creating a new fit line that's used for predictions.

    This regression can be used to examine the variability of the relationship between temperature and chirp count. Of course, if the model proves too inaccurate (that is, the predictions aren't very good), then it suggests that we might need to introduce more independent variables like humidity, time of year, latitude, amount of moonlight, and so on. If you have such data, you can do separate lines regressions for each independent variable, and then do multiple regressions with combinations of independent variables. 

    Again, once you are working with more than one or two independent variables, it's much easier to use machine learning to crunch the numbers than to try to visualize it graphically.
    ```

## <a name="clear-outputs-and-rerun-all-cells"></a>Kimenetek törlése és az összes cella újrafuttatása

Miután követte az előző szakaszban a teljes jegyzetfüzet feltöltésére vonatkozó lépéseket, létrehozott egy futó kódot a lineáris regresszióról szóló teljes oktatóanyag kontextusában. Ez a közvetlen kombinációja a kód és a szöveg az egyik nagy előnye a notebook!

Próbálja meg újrafuttatni a teljes jegyzetfüzetet most:

1. Törölje a rendszermag munkamenet-adatait és az összes cellakimenetet a **Kernel** > **újraindítása & kimenet törlése parancsra**kattintva. Ez a parancs mindig jó, ha befejezte a jegyzetfüzetet, csak azért, hogy megbizonyosodjon arról, hogy nem hozott létre furcsa függőségeket a kódcellák között.

1. Futtassa újra a jegyzetfüzetet a **Cell** > **Run All**használatával. Figyelje meg, hogy a kód futása közben a kerneljelző ki van töltve.

    Ha olyan kódunk van, amely túl sokáig fut, vagy más módon elakad, a **Kernel** > Interrupt paranccsal leállíthatja a rendszermagot.**Interrupt**

1. Görgessen végig a jegyzetfüzeten az eredmények vizsgálatához. (Ha ismét a telek nem jelenik meg, futtassa újra a cellát.)

## <a name="save-halt-and-close-the-notebook"></a>Jegyzetfüzet mentése, leállítása és bezárása

A jegyzetfüzetek szerkesztése alatt a fájlok aktuális állapotát a **Fájlmentés** > és az**Ellenőrzőpont** paranccsal vagy az eszköztár Mentés gombjával mentheti. Az "ellenőrzőpont" létrehoz egy pillanatképet, amelyhez a munkamenet során bármikor visszaállíthatja. Az ellenőrzőpontok lehetővé teszik egy sor kísérleti módosítás, és ha ezek a módosítások nem működnek, akkor csak visszaegy ellenőrzőpont segítségével a **Fájl** > **visszaad -hoz Ellenőrzőpont** követel. Egy másik megközelítés, hogy hozzon létre további cellákat, és megjegyzést ki minden olyan kódot, amit nem akar futtatni; mindkét irányban működik.

A **Fájl** > **másolása** paranccsal bármikor másolatot készíthet a jegyzetfüzet aktuális állapotáról a projekt új fájljába. Ez a másolat automatikusan megnyílik egy új böngészőlapon.

Ha végzett egy jegyzetfüzettel, használja a > **Fájlbezárás és leállítás** parancsot, amely bezárja a jegyzetfüzetet, és leállítja az azt futtató kernelt. **File** Az Azure Notebooks ezután automatikusan bezárja a böngészőlapot.

## <a name="debug-notebooks-using-visual-studio-code"></a>Jegyzetfüzetek hibakeresése a Visual Studio-kód használatával

Ha a jegyzetfüzet kódcellái nem a várt módon viselkednek, akkor kódhibák vagy egyéb hibák lehetnek. Azonban a változók értékének megjelenítésére vonatkozó utasítások on kívül `print` egy tipikus Jupyter-környezet nem kínál hibakeresési lehetőségeket.

Szerencsére letöltheti a notebook *.ipynb* fájlját, majd megnyithatja a Visual Studio Code programban a Python kiterjesztéssel. A bővítmény közvetlenül importálja a jegyzetfüzetet egyetlen kódfájlként, megőrizve a Markdown cellákat a megjegyzésekben. Miután importálta a jegyzetfüzetet, a Visual Studio kódhiba-keresőjével végigléphet a kódon, töréspontokat állíthat be, megvizsgálhatja az állapotot stb. Miután kijavításazni a kódot, majd exportálja az *.ipynb* fájlt a Visual Studio-kódból, és töltse fel újra az Azure Notebooks ba.

További információt a Visual Studio-kód dokumentációjában, a [Jupyter-jegyzetfüzet hibakeresése](https://code.visualstudio.com/docs/python/jupyter-support#debug-a-jupyter-notebook) című témakörben talál.

A [Visual Studio Code – Jupyter támogatás](https://code.visualstudio.com/docs/python/jupyter-support) című témakörben a Visual Studio Code for Jupyter notebookok további szolgáltatásait is megtekintheti.

## <a name="next-steps"></a>További lépések

- [Mintajegyzetfüzetek felfedezése](azure-notebooks-samples.md)

Útmutató cikkek:

- [Projektek létrehozása és klónozása](create-clone-jupyter-notebooks.md)
- [Projektek konfigurálása és kezelése](configure-manage-azure-notebooks-projects.md)
- [Csomagok telepítése jegyzetfüzetből](install-packages-jupyter-notebook.md)
- [Diavetítés bemutatása](present-jupyter-notebooks-slideshow.md)
- [Adatfájlok használata](work-with-project-data-files.md)
- [Adatforrások elérése](access-data-resources-jupyter-notebooks.md)
- [Az Azure Machine Learning használata](use-machine-learning-services-jupyter-notebooks.md)
