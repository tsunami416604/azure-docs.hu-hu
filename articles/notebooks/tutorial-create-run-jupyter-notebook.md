---
title: Oktatóanyag – Jupyter-jegyzetfüzet létrehozása és futtatása – Azure Notebooks előzetes verzió
description: Megtudhatja, hogyan hozhat létre és futtathat Jupyter jegyzetfüzeteket Azure Notebooks előzetes verzióban, amely bemutatja az adatelemzési folyamat lineáris regressziós folyamatát.
ms.topic: tutorial
ms.date: 01/11/2019
ms.custom: tracking-python
ms.openlocfilehash: fd7fc324894595a991074c6d83ffc0f9fa3cff75
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85834131"
---
# <a name="tutorial-create-and-run-a-jupyter-notebook-with-python"></a>Oktatóanyag: Jupyter-jegyzetfüzet létrehozása és futtatása Python-val

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Ez az oktatóanyag végigvezeti a Azure Notebooks használatának folyamatán, amely egy teljes Jupyter jegyzetfüzetet hoz létre, amely egyszerű lineáris regressziót mutat be. Az oktatóanyag során megismerheti a Jupyter notebook felhasználói felületét, amely magában foglalja a különböző cellák létrehozását, a cellák futtatását és a jegyzetfüzet megjelenítését diavetítésként.

Az elkészült jegyzetfüzet a [GitHub-Azure Notebooks mintákon](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps)érhető el. Ez az oktatóanyag azonban egy új projekttel és egy üres jegyzetfüzettel kezdődik, így a lépésről lépésre megtekintheti a folyamatot.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Project-jegyzetfüzet létrehozása néhány mintaadatok
> * A notebook felületének használata különféle típusú cellák létrehozásához
> * A notebook futtatása
> * A jegyzetfüzet mentése
> * A jegyzetfüzet hibakeresése a Visual Studio Code-ban

## <a name="create-the-project"></a>A projekt létrehozása

1. Lépjen [Azure Notebooks](https://notebooks.azure.com) , és jelentkezzen be. (Részletekért lásd: rövid útmutató [– bejelentkezés Azure Notebooksre](quickstart-sign-in-azure-notebooks.md)).

1. A nyilvános profil oldalon válassza a **saját projektek** lehetőséget az oldal tetején:

    ![Saját projektek hivatkozás a böngészőablak tetején](media/quickstarts/my-projects-link.png)

1. A **saját projektek** lapon válassza az **+ új projekt** elemet (billentyűparancs: n); a gomb csak akkor jelenhet meg, **+** Ha a böngészőablak keskeny:

    ![Új projekt parancs a saját projektek oldalon](media/quickstarts/new-project-command.png)

1. A megjelenő **új projekt létrehozása** előugró ablakban írja be vagy adja meg a következő adatokat, majd válassza a **Létrehozás**lehetőséget:

   - **Projekt neve**: lineáris regressziós példa – krikett-csiripelő
   - **Projekt azonosítója**: lineáris – regresszió – példa
   - **Nyilvános projekt**: (törölve)
   - **Hozzon létre egy readme.MD**: (törölve)

1. Néhány pillanat elteltével Azure Notebooks navigál az új projekthez.

## <a name="create-the-data-file"></a>Az adatfájl létrehozása

A jegyzetfüzetben létrehozott lineáris regressziós modell a projekt egy *cricket_chirps.csv*nevű fájljának adatait hívja meg. Ezt a fájlt a [GitHub-Azure Notebooks mintákból](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps)is létrehozhatja, vagy közvetlenül is beírhatja az adatokat. A következő szakaszok mindkét megközelítést ismertetik.

### <a name="upload-the-data-file"></a>Az adatfájl feltöltése

1. A Azure Notebooks a projekt irányítópultján válassza a **feltöltés**  >  **URL-** címről lehetőséget.
1. Az előugró ablakban adja meg a következő URL-címet a **fájl URL-címében** , és *cricket_chirps.csv* a **fájlnév**mezőben, majd válassza a **kész**lehetőséget.

    ```url
    https://raw.githubusercontent.com/Microsoft/AzureNotebooks/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps/cricket_chirps.csv
    ```

1. A *cricket_chirps.csv* fájlnak ekkor meg kell jelennie a projekt fájllista:

    ![Az újonnan létrehozott CSV-fájl, amely a projektfájlok listájában látható](media/tutorial/csv-file-in-project.png)

### <a name="create-a-file-from-scratch"></a>Hozzon létre egy fájlt a semmiből

1. Azure Notebooks a projekt irányítópultján válassza az **+ új**  >  **üres fájl** elemet.
1. Megjelenik egy mező a projekt fájljának listájában. Írja be *cricket_chirps.csv* , majd nyomja le az ENTER billentyűt.
1. Kattintson a jobb gombbal a *cricket_chirps.csv* elemre, és válassza a **fájl szerkesztése**lehetőséget.
1. A megjelenő szerkesztőben adja meg a következő adatértékeket:

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

1. Válassza a **fájl mentése** lehetőséget a fájl mentéséhez és a projekt-irányítópultra való visszatéréshez.

## <a name="install-project-level-packages"></a>Projekt szintű csomagok telepítése

A notebookon belül mindig használhat olyan parancsokat, mint a `!pip install` kód cellában a szükséges csomagok telepítéséhez. Ezek a parancsok azonban minden alkalommal futnak, amikor futtatja a jegyzetfüzet kódjának celláit, és hosszabb időt is igénybe vehet. Emiatt a csomagokat a projekt szintjén telepítheti egy `requirements.txt` fájl használatával.

1. A [fájl létrehozása az előzményekből](#create-a-file-from-scratch) című témakörben leírt eljárást követve hozzon létre egy nevű fájlt `requirements.txt` a következő tartalommal:

    ```text
    matplotlib==3.0.0
    numpy==1.15.0
    pandas==0.23.4
    scikit-learn==0.20.0
    ```

    `requirements.txt`Ha szeretné, a helyi számítógépről is feltölthet egy fájlt, az [adatfájl feltöltése](#upload-the-data-file)című cikkben leírtak szerint.

1. A projekt irányítópultján válassza a **projekt beállításai**lehetőséget.
1. A megjelenő előugró ablakban válassza a **környezet** lapot, majd válassza a **+ Hozzáadás**lehetőséget.
1. Az első legördülő menüben (a művelet) a **környezet beállítása lépésnél**válassza a **Requirements.txt**lehetőséget.
1. A második legördülő menüben (a fájl neve) válassza a *requirements.txt* (a létrehozott fájl) lehetőséget.
1. A harmadik legördülő vezérlőben (a Python verziója) válassza a **python 3,6**-es verzióját.
1. Kattintson a **Mentés** gombra.

![A Project Settings környezet lapja requirements.txt fájlt ad meg](media/tutorial/tutorial-requirements-txt.png)

Ezzel a beállítással a projektben futtatott összes jegyzetfüzet olyan környezetben fog futni, ahol a csomagok telepítve vannak.

## <a name="create-and-run-a-notebook"></a>Notebook létrehozása és futtatása

Ha az adatfájl készen áll, és a Project Environment beállította, most már létrehozhatja és megnyithatja a jegyzetfüzetet.

1. A projekt irányítópultján válassza az **+ új**  >  **Jegyzetfüzet**elemet.
1. Az előugró ablakban adja meg a *lineáris regressziós példát – Cricket csiripel. ipynb* az **elem neveként**válassza a **Python 3,6** lehetőséget a nyelvhez, majd válassza az **új**lehetőséget.
1. Miután az új jegyzetfüzet megjelenik a fájl listán, válassza ki a jegyzetfüzet elindításához. Automatikusan megnyílik egy új böngésző lap.
1. Mivel a környezeti beállításokban *requirements.txt* fájl található, a "Várakozás a tároló előkészítésének befejezésére" üzenet jelenik meg. Az **OK** gombra kattintva lezárhatja az üzenetet, és folytathatja a munkát a jegyzetfüzetben; a kód cellái azonban nem futtathatók, amíg a környezet teljesen be nem fejeződik.
1. A jegyzetfüzet az Jupyter felületen egyetlen üres kóddal jelenik meg alapértelmezettként.

    [![Azure Notebooks új jegyzetfüzetének kezdeti nézete](media/tutorial/tutorial-new-notebook.png)](media/tutorial/tutorial-new-notebook.png#lightbox)

## <a name="tour-the-notebook-interface"></a>A notebook felületének meglátogatása

A-t futtató jegyzetfüzetekben kód-és Markdown cellákat adhat hozzá, futtathatja ezeket a cellákat, és kezelheti a jegyzetfüzet működését. Először is érdemes néhány percet igénybe venni, hogy megismerkedjen az interfészsel. A teljes dokumentációhoz kattintson a **Súgó**  >  **notebook Súgó** menüjének parancsára.

Az ablak tetején az alábbi elemek láthatók:

(A) a jegyzetfüzet neve, amelyet a gombra kattintva szerkeszthet.
(B) gombok a tartalmazó projekt és a projektek irányítópultjának megnyitásához, amely a böngészőben új lapokat nyit meg.
(C) a jegyzetfüzettel való munkavégzéshez szükséges parancsokat tartalmazó menü.
(D) a gyakori műveletekhez parancsikonokat tartalmazó eszköztár.
(E) a cellákat tartalmazó szerkesztési vászon.
(F) jelzi, hogy a jegyzetfüzet megbízható-e (az alapértelmezett érték **nem megbízható**).
(G) a jegyzetfüzet futtatásához használt kernel a tevékenység-kijelzővel együtt.

[![A Jupyter felület elsődleges felhasználói felületi területei](media/tutorial/tutorial-notebook-ui.png)](media/tutorial/tutorial-notebook-ui.png#lightbox)

A Jupyter az elsődleges felhasználói felület elemeinek beépített bemutatóját biztosítja. A bemutató elindításához válassza a **Súgó**  >  **felhasználói felületének bemutatója** parancsot, és kattintson az előugró ablakok elemre.

A menüparancsok csoportjai a következők:

| Menü | Leírás |
| --- | --- |
| Fájl | A jegyzetfüzet-fájl kezelésére szolgáló parancsok, beleértve a jegyzetfüzetek létrehozásához és másolásához, a nyomtatási előnézet megjelenítéséhez és a jegyzetfüzet különböző formátumokban való letöltéséhez szükséges parancsokat is. |
| Szerkesztés | A cellák kivágását, másolását és beillesztését, az értékek megkeresését és cseréjét, a cellák mellékleteinek kezelését és a képek beszúrását jellemző parancsok.  |
| Nézet | A Jupyter felhasználói felületének különböző részeinek láthatóságát vezérlő parancsok. |
| Beszúrás | Parancsok új cella az aktuális cella fölé vagy alá való beszúrásához. Ezeket a parancsokat gyakran használja jegyzetfüzetek létrehozásakor. |
| Cella | A különböző **futtatási** parancsok egy vagy több cellát futtatnak a különböző kombinációkban. A **cella típusa** parancsok a **kód**, a **Markdown**és a **nyers NBConvert** (egyszerű szöveg) közötti cella típusát változtatják meg. Az **aktuális kimenetek** és az **összes kimenet** parancs vezérli, hogy a rendszer hogyan jelenítse meg a futtatási kód kimenetét, és tartalmaz egy parancsot az összes kimenet törléséhez. |
| Kernel | A kód a rendszermagban történő futtatását kezelő parancsok, valamint a **kernel módosítása** a jegyzetfüzet futtatásához használt nyelv vagy Python-verzió megváltoztatásához. |
| Adatok | A fájloknak a projektből vagy munkamenetből való feltöltésére és letöltésére szolgáló parancsok. Lásd: [Project adatfájlok használata](work-with-project-data-files.md) |
| Vezérlők | A [Jupyter widgetek](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Basics.html)kezelésére szolgáló parancsok, amelyek további képességeket biztosítanak a vizualizációk, a leképezések és a nyomtatás számára.|
| Súgó | A Jupyter felület súgóját és dokumentációját biztosító parancsok. |

Az eszköztáron lévő parancsok többsége egyenértékű menüparancsokkal rendelkezik. Az egyik kivétel a [megjelenő, illetve a megjelenő jegyzetfüzetekben megjelenő megjelenő](present-jupyter-notebooks-slideshow.md)vagy **szerkesztési felemelkedési diavetítés**.

A következő szakaszokban számos parancsot használhat a jegyzetfüzet feltöltéséhez.

## <a name="create-a-markdown-cell"></a>Markdown cella létrehozása

1. Kattintson a jegyzetfüzet vásznon látható első üres cellára. Alapértelmezés szerint a cella egy **kód** típusú, ami azt jelenti, hogy a kiválasztott kernelhez (Python, R vagy F #) futtatható kódot tartalmaz. Az aktuális típus az eszköztár típus legördülő menüjében látható:

    ![Cella típusa eszköztár legördülő lista](media/tutorial/tutorial-cell-type-drop-down.png)

1. Módosítsa a cella típusát a **Markdown** az eszköztár legördülő menüjének használatával. másik lehetőségként használja a Markdown típusú **cellát**a  >  **következő**  >  **Markdown** paranccsal:

    ![Cella típusa menü parancs](media/tutorial/tutorial-cell-type-menu.png)

1. A Szerkesztés megkezdéséhez kattintson a cellára, majd adja meg a következő Markdown:

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

1. Ha a Markdown HTML-formátumban szeretné megjeleníteni a böngészőben, válassza a **Futtatás** parancsot az eszköztáron, vagy használja a **cella**  >  **futtatása** cellás parancsot. A formázáshoz és a hivatkozásokhoz tartozó Markdown-kód most úgy jelenik meg, ahogy azt a böngészőben várta.

1. Ha a jegyzetfüzetben az utolsó cellát futtatja, a Jupyter automatikusan létrehoz egy új cellát, amelyet a futtatott. Ha több Markdown a cellába, ismételje meg a jelen szakasz lépéseit a következő Markdown:

    ```markdown
    ## Install packages using pip or conda

    Because the code in your notebook likely uses some Python packages, you need to make sure the Notebook environment contains those packages. You can do this directly within the notebook in a code block that contains the appropriate pip or conda commands prefixed by `!`:

    \```
    !pip install <pkg name>

    !conda install <pkg name> -y
    \```
    ```

1. A Markdown újbóli szerkesztéséhez kattintson duplán a megjelenített cellára. Ha a módosítások végrehajtása után ismét szeretné megjeleníteni a HTML-kódot, futtassa a cellát.

## <a name="create-a-code-cell-with-commands"></a>Kód cellájának létrehozása parancsokkal

Az előző Markdown-cella magyarázata szerint közvetlenül a jegyzetfüzetbe is hozzáadhat parancsokat. A parancsokat a csomagok telepítésére, a curl vagy a wget futtatására is használhatja az adat lekéréséhez, vagy bármi más. A Jupyter notebookok hatékonyan futnak Linux rendszerű virtuális gépen, így a teljes Linux-parancs úgy van beállítva, hogy működjön együtt.

1. Adja meg az alábbi parancsokat a kód cellájában, amely az előző Markdown-cella **futtatása** után szerepelt. Ha nem lát új cellát, hozzon létre egyet az **Insert**  >  **cella beszúrása alatt** , vagy használja az **+** eszköztár gombját.

    ```bash
    !pip install numpy
    !pip install matplotlib
    !pip install pandas
    !pip install sklearn
    ```

1. A cella futtatása előtt hozzon létre egy új cellát a **+** gombbal az eszköztáron, állítsa be a Markdown, és adja meg a következő magyarázatot:

    ```markdown
    Note that when you run a code block that contains install commands, and also those with `import` statements, it make take the notebooks a little time to complete the task. To the left of the code block you see `In [*]` to indicate that execution is happening. The Notebook's kernel on the upper right also shows a filled-in circle to indicate "busy."
    ```

1. Jelölje be **az**  >  **összes parancs futtatása** parancsot a jegyzetfüzet összes cellájának futtatásához. Figyelje meg, hogy a Markdown-cellák HTML-ként jelennek meg, a parancs pedig a kernelben fut, és figyelje meg a kernel kijelzőt a Markdown-ben leírtak szerint:

    ![A notebook kernel foglalt kijelzője](media/tutorial/tutorial-kernel-busy.png)

1. Az összes parancs futtatására is kis időbe telik `pip install` , és mivel már telepítette ezeket a csomagokat a Project Environment-ban (és mivel alapértelmezés szerint a Azure Notebooks is tartalmazza őket), a "követelmény már teljesült" üzenet jelenik meg. Az összes kimenet vizuálisan zavaró lehet, ezért válassza ki ezt a cellát (egyetlen kattintással), majd a **cella**  >  **kimenetek**  >  **váltógomb** használatával elrejtheti a kimenetet. Ha a kimenetet teljes mértékben el szeretné távolítani, használhatja a **Clear** parancsot is ugyanezen az almenün.

    A **pecek** parancs csak a cella legutóbbi kimenetét rejti el. Ha újra futtatja a cellát, a kimenet újra megjelenik.

1. Mivel a csomagok a Project-környezetbe vannak telepítve, megjegyzésekkel `! pip install` láthatják el a parancsokat a használatával, így `#` a jegyzetfüzetben is megmaradhatnak, de nem fognak időt használni, és nem termelnek felesleges kimenetet. Ebben az esetben a jegyzetfüzetben található megjegyzési parancsok megtartása a jegyzetfüzet függőségeit is jelzi.

    ```bash
    # !pip install numpy
    # !pip install matplotlib
    # !pip install pandas
    # !pip install sklearn
    ```

## <a name="create-the-remaining-cells"></a>A fennmaradó cellák létrehozása

A jegyzetfüzet többi részének feltöltéséhez következő lépésként hozzon létre egy Markdown és egy kódrészletet. Az alább felsorolt minden egyes cella esetében először hozza létre az új cellát, majd állítsa be a típust, majd illessze be a tartalmat.

Bár az egyes cellák létrehozása után megvárhatja a jegyzetfüzet futtatását, érdekes, hogy az egyes cellákat a létrehozásakor futtassa. Nem minden cella mutat kimenetet; Ha nem lát hibát, tegyük fel, hogy a cella szabályosan futott.

Az egyes kódrészletek a korábbi cellákban futtatott kódból függenek, és ha az egyik cella nem fut, akkor a későbbi cellák hibákat okozhatnak. Ha azt tapasztalja, hogy elfelejtette a cella futtatását, próbálja **meg a**  >  **fenti cella futtatása** előtt használni az aktuális cella futtatása előtt.

Ha nem várt eredményeket lát (valószínűleg!), ellenőrizze, hogy az egyes cellák szükség szerint "code" vagy "Markdown" értékre vannak beállítva. Például az "érvénytelen szintaxis" hibaüzenet általában akkor fordul elő, ha a Markdown beírta a kód cellába.

1. Markdown cella:

    ```markdown
    ## Import packages and prepare the dataset

    In this example we're using numpy, pandas, and matplotlib. Data is in the file cricket_chirps.csv. Because this file is in the same project as this present Notebook, we can just load it using a relative pathname.
    ```

1. Kód cellája; a futtatáskor a táblázat tartalma kimenetként jelenik meg. A kimenetet letilthatja az `print` utasítással.

    ```python
    import numpy as np
    import pandas as pd

    dataset = pd.read_csv('cricket_chirps.csv')
    print(dataset)
    X = dataset.iloc[:, :-1].values  # Matrix of independent variables -- remove the last column in this data set
    y = dataset.iloc[:, 1].values    # Matrix of dependent variables -- just the last column (1 == 2nd column)
    ```

    > [!Note]
    > Előfordulhat, hogy a kód figyelmeztetéseit a "NumPy. dtype méret módosítva"; a figyelmeztetések nyugodtan figyelmen kívül hagyhatók.

1. Markdown cella:

    ```markdown
    Next, split the dataset into a Training set (2/3rds) and Test set (1/3rd). We don't need to do any feature scaling because there is only one column of independent variables, and packages typically do scaling for you.
    ```

1. Kód cellája; Ha fut, ennek a cellának nincs kimenete.

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

1. Kód cellája; Ha fut, ez a cella a kimenetet jeleníti meg `LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None,normalize=False)` .

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

1. Kód cellája; a futtatáskor ez a cella a hasonló eredményeket jeleníti meg `[79.49588055 75.98873911 77.87719989 80.03544077 75.17939878]` .

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

1. Kód cellája; Ha fut, ez a cella grafikus ábrát hoz létre. Ha az első alkalommal nem látja a mintaterületet (és ehelyett a "ábra mérete 640x480 1 tengellyel"), futtassa újra a cellát.

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

    ![A matplotlib-kód kimenetének ábrázolása](media/tutorial/tutorial-plot-output.png)

1. Markdown cella:

    ```markdown
    ## Closing comments

    At the end of the day, when you create a model, you use training data. Then you start feeding test data (real observations) to see how well the model actually works. You may find that the model is a little inaccurate over time, in which case you retrain the model with some new data. Retraining the model means you're creating a new fit line that's used for predictions.

    This regression can be used to examine the variability of the relationship between temperature and chirp count. Of course, if the model proves too inaccurate (that is, the predictions aren't very good), then it suggests that we might need to introduce more independent variables like humidity, time of year, latitude, amount of moonlight, and so on. If you have such data, you can do separate lines regressions for each independent variable, and then do multiple regressions with combinations of independent variables. 

    Again, once you are working with more than one or two independent variables, it's much easier to use machine learning to crunch the numbers than to try to visualize it graphically.
    ```

## <a name="clear-outputs-and-rerun-all-cells"></a>Kimenetek törlése és az összes cella újrafuttatása

Miután az előző szakaszban szereplő lépéseket követve feltöltheti a teljes jegyzetfüzetet, a lineáris regresszióra vonatkozó teljes oktatóanyag kontextusában is létrehozta a futó kód egy részét. A kód és a szöveg közvetlen kombinációja a notebookok egyik nagyszerű előnye!

Próbálja meg újból futtatni a teljes jegyzetfüzetet:

1. A **rendszermag**  >  **újraindítása & a kimenet törlése**lehetőség kiválasztásával törölheti a kernel összes munkamenet-és összes kimenetét. Ez a parancs mindig jó választás, ha elvégezte a jegyzetfüzetet, csak azért, hogy ne hozzon létre furcsa függőségeket a kód cellái között.

1. Futtassa újra a jegyzetfüzetet a **cella**  >  **futtatása**parancs használatával. Figyelje meg, hogy a rendszermag kijelzője ki van töltve, amíg a kód fut.

    Ha olyan kódot használ, amely túl hosszú vagy egyéb módon leáll, **a kernel**  >  **megszakítása** paranccsal állíthatja le a kernelt.

1. Görgessen végig a jegyzetfüzetben az eredmények vizsgálatához. (Ha ismét a mintaterület nem jelenik meg, futtassa újra ezt a cellát.)

## <a name="save-halt-and-close-the-notebook"></a>A jegyzetfüzet mentése, leállítása és lezárása

Amikor egy jegyzetfüzetet szerkeszt, az aktuális állapotát a **fájl**  >  **mentése és ellenőrzőpont** paranccsal, vagy a Mentés gombra kattintva mentheti az eszköztáron. Az "ellenőrzőpont" olyan pillanatképet hoz létre, amely bármikor visszaállítható a munkamenet során. Az ellenőrzőpontok lehetővé teszik a kísérleti módosítások sorozatának elvégzését, és ha ezek a módosítások nem működnek, egyszerűen visszaállíthat egy ellenőrzőpontot a **fájl**visszaállítása az  >  **ellenőrzőpontra** parancs használatával. Alternatív módszer a további cellák létrehozása, és a nem kívánt kód megjegyzése. Akárhogy is működik.

A **fájl**a  >  **Másolás** parancs használatával bármikor másolatot készíthet a jegyzetfüzet aktuális állapotáról egy új fájlba a projektben. A másolás automatikusan megnyílik egy új böngésző lapon.

Ha elkészült egy jegyzetfüzettel **, a**  >  **Bezárás és** leállítás parancs használatával zárja be a jegyzetfüzetet, és leállítja a futtatott kernelt. Azure Notebooks ezután automatikusan bezárja a böngésző fület.

## <a name="debug-notebooks-using-visual-studio-code"></a>Jegyzetfüzetek hibakeresése a Visual Studio Code használatával

Ha a jegyzetfüzetben lévő kód cellái nem a várt módon viselkednek, akkor lehet, hogy a kódban hibák vagy más hibák történtek. Azonban a `print` változók értékének megjelenítését nem használó utasítások alapján egy tipikus Jupyter-környezet nem biztosít hibakereső létesítményeket.

Szerencsére letöltheti a notebook *. ipynb* -fájlját, majd a Python bővítménnyel megnyithatja a Visual Studio Code-ban. A bővítmény közvetlenül importál egy jegyzetfüzetet egyetlen kódlapként, megőrizve a Markdown-cellákat a megjegyzésekben. Miután importálta a jegyzetfüzetet, használhatja a Visual Studio Code debuggert a kód beléptetéséhez, a töréspontok beállításához, az állapot vizsgálatához és így tovább. A kód javítása után exportálja a *. ipynb* fájlt a Visual Studio Code-ból, és töltse fel újra Azure Notebooksba.

További információ: Jupyter- [Jegyzetfüzet hibakeresése](https://code.visualstudio.com/docs/python/jupyter-support#debug-a-jupyter-notebook) a Visual Studio Code dokumentációjában.

Lásd még: a [Visual Studio Code-Jupyter támogatása](https://code.visualstudio.com/docs/python/jupyter-support) a Visual Studio Code további szolgáltatásaihoz a Jupyter notebookokhoz.

## <a name="next-steps"></a>További lépések

- [Minta-jegyzetfüzetek megismerése](azure-notebooks-samples.md)

Útmutató a cikkekhez:

- [Projektek létrehozása és klónozása](create-clone-jupyter-notebooks.md)
- [Projektek konfigurálása és kezelése](configure-manage-azure-notebooks-projects.md)
- [Csomagok telepítése jegyzetfüzetből](install-packages-jupyter-notebook.md)
- [Diavetítés bemutatása](present-jupyter-notebooks-slideshow.md)
- [Adatfájlok használata](work-with-project-data-files.md)
- [Adatforrások elérése](access-data-resources-jupyter-notebooks.md)
- [Az Azure Machine Learning használata](use-machine-learning-services-jupyter-notebooks.md)
