---
title: Oktatóanyag – létrehozása és futtatása a Jupyter notebook az Azure-ban
description: Hogyan lehet egy futtatása Jupyter notebook létrehozása Azure-jegyzetfüzetekben, azt mutatja be, az adatelemzés lineáris regressziós folyamatán.
ms.topic: tutorial
ms.date: 01/11/2019
ms.openlocfilehash: 2c151cb0de2855856e92d9de07ad7dabfda2f55b
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277418"
---
# <a name="tutorial-create-and-run-a-jupyter-notebook-with-python"></a>Oktatóanyag: létrehozása és futtatása a Jupyter notebook Python használatával

Ez az oktatóanyag végigvezeti a folyamat azt mutatja be, lineáris regresszió egyszerű befejezése Jupyter notebook létrehozása Azure jegyzetfüzeteinek előnyeit. Ebben az oktatóanyagban képviselőinkkel, ismerkedjen meg a Jupyter notebook felhasználói felületén, például a különböző cellák létrehozásához, cellák fut, és diavetítésként a notebook bemutatása.

Az elkészült jegyzetfüzet a [GitHub-Azure Notebooks mintákon](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps)érhető el. Ebben az oktatóanyagban egy új projektet és a egy üres notebook azonban kezdődik, így a tapasztalhatnak, lépésről lépésre hozza létre.

## <a name="create-the-project"></a>A projekt létrehozása

1. Lépjen [Azure Notebooks](https://notebooks.azure.com) , és jelentkezzen be. (Részletekért lásd: rövid útmutató [– bejelentkezés Azure Notebooksre](quickstart-sign-in-azure-notebooks.md)).

1. A nyilvános profil oldalon válassza a **saját projektek** lehetőséget az oldal tetején:

    ![A böngésző ablakának felső részén saját projektek hivatkozás](media/quickstarts/my-projects-link.png)

1. A **saját projektek** lapon válassza az **+ új projekt** elemet (billentyűparancs: n); a gomb csak akkor szerepelhet **+** , ha a böngészőablak keskeny:

    ![Új projekt parancsot a saját projektek lapon](media/quickstarts/new-project-command.png)

1. A megjelenő **új projekt létrehozása** előugró ablakban írja be vagy adja meg a következő adatokat, majd válassza a **Létrehozás**lehetőséget:

    - **Projekt neve**: lineáris regressziós példa – krikett-csiripelő
    - **Projekt azonosítója**: lineáris – regresszió – példa
    - **Nyilvános projekt**: (törölve)
    - **Hozzon létre egy readme.MD**: (törölve)

1. Néhány pillanat múlva Azure notebookok nyit meg az új projekt.

## <a name="create-the-data-file"></a>Az adatfájl létrehozása

A jegyzetfüzetben létrehozott lineáris regressziós modell a projekt egy *cricket_chirps. csv*nevű fájljának adatait hívja meg. Ezt a fájlt a [GitHub-Azure Notebooks mintákból](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps)is létrehozhatja, vagy közvetlenül is beírhatja az adatokat. A következő szakaszok mindkét módszert ismertetik.

### <a name="upload-the-data-file"></a>Az adatfájl feltöltése

1. A Azure Notebooks a projekt irányítópultján válassza a **feltöltés** > **URL-** címről lehetőséget.
1. Az előugró ablakban adja meg a következő URL-címet a fájl **URL-címében** , és *cricket_chirps. csv* fájlt a **fájlnév**mezőben, majd válassza a **kész**lehetőséget.

    ```url
    https://raw.githubusercontent.com/Microsoft/AzureNotebooks/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps/cricket_chirps.csv
    ```

1. A *cricket_chirps. csv* fájlnak ekkor meg kell jelennie a projekt fájllista:

    ![Az újonnan létrehozott CSV-fájlt a projekt fájlok listájának megjelenítése](media/tutorial/csv-file-in-project.png)

### <a name="create-a-file-from-scratch"></a>Sablon nélkül hozzon létre egy fájlt

1. Azure Notebooks a projekt irányítópultján válassza az **+ új** > **üres fájl** elemet.
1. Egy mezőt a projekt fájl listájában jelenik meg. Adja meg *cricket_chirps. csv* fájlt, és nyomja le az ENTER billentyűt.
1. Kattintson a jobb gombbal a *cricket_chirps. csv* fájlra, majd válassza a **fájl szerkesztése**lehetőséget.
1. A szerkesztőben megjelenő adja meg a következő adatokat:

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

## <a name="install-project-level-packages"></a>Projekt szolgáltatásiszint-csomagok telepítése

A notebookon belül mindig használhat olyan parancsokat, mint a kód cellában `!pip install` a szükséges csomagok telepítéséhez. Azonban az ilyen parancsok futtatása minden alkalommal, amikor a notebook kód cellák futtatja, és hosszabb időt is igénybe vehet. Emiatt a csomagokat a projekt szintjén telepítheti egy `requirements.txt` fájl használatával.

1. A következő témakörben ismertetett eljárást követve hozzon létre [egy fájlt `requirements.txt`](#create-a-file-from-scratch) nevű fájl létrehozásával:

    ```text
    matplotlib==3.0.0
    numpy==1.15.0
    pandas==0.23.4
    scikit-learn==0.20.0
    ```

    Ha szeretné, az [adatfájl feltöltése](#upload-the-data-file)című cikkben leírtak szerint `requirements.txt` fájlt is feltölthet a helyi számítógépről.

1. A projekt irányítópultján válassza a **projekt beállításai**lehetőséget.
1. A megjelenő előugró ablakban válassza a **környezet** lapot, majd válassza a **+ Hozzáadás**lehetőséget.
1. Az első legördülő vezérlőben (a műveletben) a **környezet beállítása lépésnél**válassza a **követelmények. txt**elemet.
1. A második legördülő vezérlőben (a fájl neve) válassza a *követelmények. txt* (a létrehozott fájl) elemet.
1. A harmadik legördülő vezérlőben (a Python verziója) válassza a **python 3,6**-es verzióját.
1. Kattintson a **Mentés** gombra.

![A projekt beállítások környezet lapon requirements.txt fájl megadása](media/tutorial/tutorial-requirements-txt.png)

A telepítő lépés helyen a projekt futtatása minden olyan jegyzetfüzet fog futni egy környezetben amelyen telepítve vannak-e az azokat a csomagokat.

## <a name="create-and-run-a-notebook"></a>Notebook létrehozása és futtatása

Készen áll az adatfájlban és a projekt környezet beállítása hogy mostantól létrehozhat és nyissa meg a notebook.

1. A projekt irányítópultján válassza az **+ új** > **Jegyzetfüzet**elemet.
1. Az előugró ablakban adja meg a *lineáris regressziós példát – Cricket csiripel. ipynb* az **elem neveként**válassza a **Python 3,6** lehetőséget a nyelvhez, majd válassza az **új**lehetőséget.
1. Miután az Új jegyzetfüzet a fájllista megjelenik, válassza ki, hogy a notebook. Egy új böngészőlapon nyílik meg automatikusan.
1. Mivel a környezeti beállításokban szerepel a *követelmények. txt* fájl, a "Várakozás a tároló előkészítésének befejezésére" üzenet jelenik meg. Az **OK** gombra kattintva lezárhatja az üzenetet, és folytathatja a munkát a jegyzetfüzetben; a kód cellái azonban nem futtathatók, amíg a környezet teljesen be nem fejeződik.
1. A Notebookban egy üres kódcellába Jupyter kapcsolaton alapértelmezett nyílik meg.

    [![Azure Notebooks új jegyzetfüzetének kezdeti nézete](media/tutorial/tutorial-new-notebook.png)](media/tutorial/tutorial-new-notebook.png#lightbox)

## <a name="tour-the-notebook-interface"></a>A notebook felület bemutatása

A notebook fut, a kód Markdown cellák, futtassa a cellákat és kezelheti a notebookot az is hozzáadhat. Először azonban érdemes Ismerkedjen meg a kapcsolat néhány percet vesz igénybe. A teljes dokumentációhoz válassza a **súgó** > **notebook Súgó** menü parancsát.

Az ablak tetején jelenik meg a következő elemek:

(A) a neve, a jegyzetfüzet, amelyre kattintva szerkesztheti.
(B) a tartalmazó projekt és a projekt irányítópultján, amely új lapok megnyitása a böngészőben nyissa meg a gombokat.
(C) A menü-parancsokkal a notebook való munkához.
(D) eszköztár parancsikonjait gyakori műveletekhez.
(E) a cellákat tartalmazó szerkesztési vászonra.
(F) jelzi, hogy a jegyzetfüzet megbízható-e (az alapértelmezett érték **nem megbízható**).
(G) a kernel a jegyzetfüzet futtatásához egy tevékenységet jelző együtt használja.

[a Jupyter felület elsődleges felhasználói felületi területeinek ![](media/tutorial/tutorial-notebook-ui.png)](media/tutorial/tutorial-notebook-ui.png#lightbox)

Jupyter biztosít beépített Ismerkedjen meg az elsődleges felhasználói felületi elemeket. Indítsa el a bemutatót a **súgó** > **felhasználói felületi bemutató** parancs kiválasztásával, és kattintson az előugró ablakok elemre.

A csoportok menü parancsok a következők:

| Menü | Leírás |
| --- | --- |
| Fájl | A notebook fájlt, beleértve azokat a parancsokat hozhat létre, és másolja a jegyzetfüzet, kezelésére szolgáló parancsok egy nyomtatási kép megjelenítése, és töltse le a notebookot a számos különböző formátumban. |
| Szerkesztés | Kivágás, másolja, és illessze be a cellák, keresse meg és cserélje le az értékeket, tipikus parancsokat cella mellékletek kezelése, és helyezze be a lemezképek.  |
| Nézet | A Jupyter felhasználói felület más részein láthatóságot parancsokat. |
| Beszúrás | Új cella felett vagy alatt az aktuális cella beszúrása parancsokat. Ezek a parancsok gyakran egy jegyzetfüzetet létrehozásakor használ. |
| Cella | A különböző **futtatási** parancsok egy vagy több cellát futtatnak a különböző kombinációkban. A **cella típusa** parancsok a **kód**, a **Markdown**és a **nyers NBConvert** (egyszerű szöveg) közötti cella típusát változtatják meg. Az **aktuális kimenetek** és az **összes kimenet** parancs vezérli, hogy a rendszer hogyan jelenítse meg a futtatási kód kimenetét, és tartalmaz egy parancsot az összes kimenet törléséhez. |
| Kernel | A kód a rendszermagban történő futtatását kezelő parancsok, valamint a **kernel módosítása** a jegyzetfüzet futtatásához használt nyelv vagy Python-verzió megváltoztatásához. |
| Adatok | Fájlok feltöltését és letöltését a projekt vagy a munkamenet parancsokat. Lásd: [Project adatfájlok használata](work-with-project-data-files.md) |
| Vezérlők | A [Jupyter widgetek](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Basics.html)kezelésére szolgáló parancsok, amelyek további képességeket biztosítanak a vizualizációk, a leképezések és a nyomtatás számára.|
| Súgó | Parancsok, nyújthat segítséget és a Jupyter felület dokumentációját. |

Legtöbb parancs, az eszköztáron rendelkezik egyenértékű menüparancsai. Az egyik kivétel a [megjelenő, illetve a megjelenő jegyzetfüzetekben megjelenő megjelenő](present-jupyter-notebooks-slideshow.md)vagy **szerkesztési felemelkedési diavetítés**.

Ezek a parancsok számos módon feltölti a notebookot az alábbi szakaszok a használhatja.

## <a name="create-a-markdown-cell"></a>Hozzon létre egy Markdown-cella

1. Kattintson a notebook vásznon látható első üres cellába. Alapértelmezés szerint a cella egy **kód** típusú, ami azt jelenti, hogy a kiválasztott kernelhez (Python, R vagy F#) futtatható kódot tartalmaz. A jelenlegi típus: írja be a legördülő menüből az eszköztáron látható:

    ![Cella típusa eszköztár legördülő menü](media/tutorial/tutorial-cell-type-drop-down.png)

1. Módosítsa a cella típusát a **Markdown** az eszköztár legördülő menüjének használatával. másik lehetőségként használja a **cella** > **cellájának típusát** > **Markdown** :

    ![Parancs cella típusa](media/tutorial/tutorial-cell-type-menu.png)

1. A cella szerkesztésének megkezdéséhez kattintson, majd adja meg a következő markdown írható:

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

1. Ha a Markdown HTML formátumban szeretné megjeleníteni a böngészőben, válassza a **Futtatás** parancsot az eszköztáron, vagy használja a **cella** > **cellák futtatása** parancsot. A Markdown kódot a formázás és hivatkozások most őket egy böngészőben a várt módon jelennek meg.

1. A jegyzetfüzet futtatásakor az előző cella Jupyter automatikusan létrehozza az alábbi futtatta egy új cellára. A cella ebben a szakaszban a következő Markdown-lépéseinek megismétlésével további Markdown üzembe:

    ```markdown
    ## Install packages using pip or conda

    Because the code in your notebook likely uses some Python packages, you need to make sure the Notebook environment contains those packages. You can do this directly within the notebook in a code block that contains the appropriate pip or conda commands prefixed by `!`:

    \```
    !pip install <pkg name>

    !conda install <pkg name> -y
    \```
    ```

1. A Markdown újra szerkesztéséhez kattintson duplán a megjelenített cellában. HTML megjelenítése a módosítások elvégzése után ismét, futtassa a cella.

## <a name="create-a-code-cell-with-commands"></a>Hozzon létre egy kódcellát parancsokkal

Az előző cella Markdown mértékét, közvetlenül a notebookot a parancsok is megadhat. Parancsok segítségével csomagokat telepíteni, és futtassa a curl vagy wget beolvasni az adatokat, vagy bármi más. Jupyter notebookok hatékonyan futtatása Linux rendszerű virtuális gépen, így a teljes Linux parancs, állítsa a kell.

1. Adja meg az alábbi parancsokat a kód cellájában, amely az előző Markdown-cella **futtatása** után szerepelt. Ha nem lát új cellát, hozzon létre egyet az **insert** > INSERT **cellával** , vagy használja a **+** gombot az eszköztáron.

    ```bash
    !pip install numpy
    !pip install matplotlib
    !pip install pandas
    !pip install sklearn
    ```

1. A cella futtatása előtt hozzon létre egy új cellát az eszköztáron a **+** gombbal, állítsa be a Markdown értékre, és adja meg a következő magyarázatot:

    ```markdown
    Note that when you run a code block that contains install commands, and also those with `import` statements, it make take the notebooks a little time to complete the task. To the left of the code block you see `In [*]` to indicate that execution is happening. The Notebook's kernel on the upper right also shows a filled-in circle to indicate "busy."
    ```

1. Válassza ki a **cellát** > **futtassa az összes** parancsot a jegyzetfüzet összes cellájának futtatásához. Figyelje meg, hogy a Markdown cellák HTML formátumban jelennek meg, és a parancs futtatása a kernel, és figyelje meg a kernel kijelző, a markdown-szöveg leírtak szerint:

    ![A notebook kernel foglalt mutatója](media/tutorial/tutorial-kernel-busy.png)

1. Az összes `pip install`-parancs futtatására is kis időbe telik, és mivel már telepítette ezeket a csomagokat a Project Environment-ban (és mivel alapértelmezés szerint a Azure Notebooks is tartalmazza őket), a "követelmény már" üzenet jelenik meg. elégedett. " Az összes kimenet vizuálisan zavaró lehet, ezért **válassza az értékesítés** (egyetlen kattintással) lehetőséget, majd a cella > **cella kimenetek** > a **váltással** elrejtheti a kimenetet. Ha a kimenetet teljes mértékben el szeretné távolítani, használhatja a **Clear** parancsot is ugyanezen az almenün.

    A **pecek** parancs csak a cella legutóbbi kimenetét rejti el. Ha újra futtatja a cellát, a kimenet újra megjelenik.

1. Mivel a csomagok telepítve vannak a Project-környezetben, a `#`használatával tegye megjegyzésbe a `! pip install` parancsokat; így a jegyzetfüzetben megmaradhatnak oktatási anyagokként, de nem fog időt futtatni, és nem fog felesleges kimenetet létrehozni. Ebben az esetben a notebookot a megjegyzésekkel parancsok tartja azt is jelzi a notebook függőségek.

    ```bash
    # !pip install numpy
    # !pip install matplotlib
    # !pip install pandas
    # !pip install sklearn
    ```

## <a name="create-the-remaining-cells"></a>A fennmaradó cellák létrehozása

A többi a notebook feltöltéséhez, kell létrehoznia egy sorozatát Markdown és a kódot. Az alább felsorolt egyes cellák először hozzon létre az új cellára, majd állítsa be a típusát, majd illessze be a tartalom.

Bár minden cella létrehozását követően a jegyzetfüzet futtatásához várhat, érdemes futtatni minden cella létrehozása közben. Nem minden cellák megjelenítése kimeneti; Ha nem látja az esetleges hibákat, azt feltételezik, a cella általában futott.

Minden egyes kódcella attól függ, a kódot, amely az előző cella fut, és nem a cellák valamelyike futtassa így tesz, ha újabb cellák eredményezhet a hibák. Ha azt tapasztalja, hogy elfelejtette egy cella futtatását, próbálja meg a **cellát** > **futtatni** az aktuális cella futtatása előtt.

Ha nem várt eredmények (Ez valószínűleg fogja!), ellenőrizze, hogy minden cella értéke "Code" vagy "Markdown" szükség szerint. Például "Érvénytelen szintaxis" hiba általában történik, ha megadta a Markdown kódot cellába.

1. Markdown-cella:

    ```markdown
    ## Import packages and prepare the dataset

    In this example we're using numpy, pandas, and matplotlib. Data is in the file cricket_chirps.csv. Because this file is in the same project as this present Notebook, we can just load it using a relative pathname.
    ```

1. Kódcella; Futtatás jelenít meg a tábla tartalmának kimenetként. A kimenetet letilthatja a `print` utasítás megjegyzésével.

    ```python
    import numpy as np
    import pandas as pd

    dataset = pd.read_csv('cricket_chirps.csv')
    print(dataset)
    X = dataset.iloc[:, :-1].values  # Matrix of independent variables -- remove the last column in this data set
    y = dataset.iloc[:, 1].values    # Matrix of dependent variables -- just the last column (1 == 2nd column)
    ```

    > [!Note]
    > Ez a kód "numpy.dtype mérete-módosult;" kapcsolatos figyelmeztetések jelenhetnek meg a figyelmeztetések biztonságosan figyelmen kívül hagyhatja.

1. Markdown-cella:

    ```markdown
    Next, split the dataset into a Training set (2/3rds) and Test set (1/3rd). We don't need to do any feature scaling because there is only one column of independent variables, and packages typically do scaling for you.
    ```

1. Kódcella; Ha futtatja, ez a cella nem rendelkezik kimeneti.

    ```python
    from sklearn.model_selection import train_test_split

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 1/3, random_state = 0)
    ```

1. Markdown-cella:

    ```markdown
    ## Fit the data to the training set

    "Fitting" the data to a training set means making the line that describes the relationship between the independent and the dependent variables. With a simple data set like we're using here, you can visualize the line on a simple x-y plot: the x-axis is the independent variable (chirp count in this example), and the y-axis is the independent variable (temperature). Fitting the data means plotting all the points in the training set, then drawing the best-fit line through that data.

    With two independent variables you can imagine a three-dimensional plot with a line fitted to the data. At three or more independent variables, however, it's no longer easy to visualize the fit, but you get the idea. In the end, it's all just mathematics, which a computer can handle easily without having to form a mental picture!

    The regressor's `fit` method here creates the line, which algebraically is of the form `y = x*b1 + b0`, where b1 is the coefficient or slope of the line (which you can get to through `regressor.coef_`), and b0 is the intercept of the line at x=0 (which you can get to through `regressor.intercept_`).
    ```

1. Kód cellája; a futtatáskor a cella a kimenetet jeleníti meg `LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None,normalize=False)`.

    ```python
    from sklearn.linear_model import LinearRegression

    regressor = LinearRegression()    # This object is the regressor, that does the regression
    regressor.fit(X_train, y_train)   # Provide training data so the machine can learn to predict using a learned model.
    ```

1. Markdown-cella:

    ```markdown
    ## Predict the results

    With the regressor in hand, we can predict the test set results using its `predict` method. That method takes a vector of independent variables for which you want predictions.

    Because the regressor is fit to the data by virtue of `coef_` and `intercept_`, a prediction is the result of `coef_ * x + intercept_`. (Indeed, `predict(0)` returns `intercept_` and `predict(1)` returns `intercept_ + coef_`.)

    In the code, the `y_test` matrix (from when we split the set) contains the real observations. `y_pred` assigned here contains the predictions for the same `X_test` inputs. It's not expected that the test or training points exactly fit the regression; the regression is trying to find the model that we can use to make predictions with new observations of the independent variables.
    ```

1. Kód cellája; a futtatáskor ez a cella a `[79.49588055 75.98873911 77.87719989 80.03544077 75.17939878]`eredményeit jeleníti meg.

    ```python
    y_pred = regressor.predict(X_test)
    print(y_pred)
    ```

1. Markdown-cella:

    ```markdown
    It's interesting to think that all the "predictions" we use in daily life, like weather forecasts, are just regression models of some sort working with various data sets. Those models are much more complicated than what's shown here, but the idea is the same.

    Knowing how predictions work help us understand that the actual observations we would collect in the moment will always be somewhat off from the predictions: the predictions fit exactly to the model, whereas the observations typically won't.

    Of course, such systems feed new observations back into the dataset to continually improve the model, meaning that predictions should get more accurate over time.

    The challenge is determining what data to actually use. For example, with weather, how far back in time do you go? How have weather patterns been changing decade by decade? In any case, something like weather predictions will be doing things hour by hour, day by day, for things like temperature, precipitation, winds, cloud cover, etc. Radar and other observations are of course fed into the model and the predictions are reduced to mathematics.
    ```

1. Markdown-cella:

    ```markdown
    ## Visualize the results

    The following code generates a plot: green dots are training data, red dots are test data, blue dots are predictions. Gray line is the regression itself. You see that all the blue dots are exactly on the line, as they should be, because the predictions exactly fit the model (the line).
    ```

1. Kódcella; futtatásakor a cella grafikus rajzot hoz létre. Ha nem a diagram az első indításakor, (, és látni: ". ábra méretűnek 640 x 480 és 1 tengelyek"), futtassa újra a cellát.

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

    ![Diagram kimenete a matplotlib kódot](media/tutorial/tutorial-plot-output.png)

1. Markdown-cella:

    ```markdown
    ## Closing comments

    At the end of the day, when you create a model, you use training data. Then you start feeding test data (real observations) to see how well the model actually works. You may find that the model is a little inaccurate over time, in which case you retrain the model with some new data. Retraining the model means you're creating a new fit line that's used for predictions.

    This regression can be used to examine the variability of the relationship between temperature and chirp count. Of course, if the model proves too inaccurate (that is, the predictions aren't very good), then it suggests that we might need to introduce more independent variables like humidity, time of year, latitude, amount of moonlight, and so on. If you have such data, you can do separate lines regressions for each independent variable, and then do multiple regressions with combinations of independent variables. 

    Again, once you are working with more than one or two independent variables, it's much easier to use machine learning to crunch the numbers than to try to visualize it graphically.
    ```

## <a name="clear-outputs-and-rerun-all-cells"></a>Kimenetek törölje, majd futtassa újra az összes cellát

Mindkét egy darab kódot futtató lineáris regressziós modell futtatása egy teljes oktatóanyag keretében létrehozott után a teljes notebook feltöltéséhez az előző szakasz lépéseit követve a következőket. Ez a kód és a szöveges közvetlen kombinációja egyike a jegyzetfüzet egyik nagy előnye!

Próbálja ki a teljes notebook most újrafuttatása:

1. A rendszermag > **újraindítása & a kimenet törlése**lehetőség kiválasztásával törölje **a kernel összes** munkamenet-és összes kimenetét. Ez a parancs mindig egy jó egy-egy jegyzetfüzetet csak, győződjön meg arról, hogy még nem létrehozott kód cellák között furcsa függőségek befejezése után futtassa.

1. Futtassa újra a jegyzetfüzetet **a > ** **futtatása**paranccsal. Figyelje meg, hogy a kernel kijelző kitölti a kód futtatása közben.

    Ha olyan kódot használ, amely túl hosszú vagy egyéb módon leáll **, a kernel > ** **megszakítás** paranccsal állíthatja le a kernelt.

1. Görgessen végig a notebook vizsgálja meg az eredményeket. (Ha újra a diagram nem jelenik meg, indítsa újra a cella.)

## <a name="save-halt-and-close-the-notebook"></a>Mentse, leállíthatja, és bezárja a notebookot

Amikor egy jegyzetfüzetet szerkeszt, a jelenlegi állapotát mentheti a **fájl** > **Save and Checkpoint** paranccsal, vagy a Mentés gombra az eszköztáron. "Ellenőrzőpont", amely a munkamenet során bármikor visszatérhet pillanatképet hoz létre. Az ellenőrzőpontok lehetővé teszik a kísérleti módosítások sorozatának elvégzését, és ha ezek a módosítások nem működnek, egyszerűen visszaállíthat egy ellenőrzőpontot a **fájl** > az **ellenőrzőpont** parancs használatával. Egy alternatív módszer az, hogy további cellákat és tegye megjegyzésbe, amelyet szeretne futtatni; kód létrehozása mindkét esetben működik.

A **fájlt** > is használhatja, ha a jegyzetfüzet aktuális állapotát egy új fájlba szeretné készíteni a projektben, a **másolási** parancs használatával bármikor készíthet másolatot. A másolatot automatikusan megnyílik egy új böngészőlapon.

Ha elkészült egy jegyzetfüzettel, használja **a > ** **Bezárás és** Leállítás parancsot, amely lezárja a jegyzetfüzetet, és leállítja a futtatott kernelt. Az Azure notebookok majd a böngészőlapon automatikusan bezáródik.

## <a name="debug-notebooks-using-visual-studio-code"></a>Hibakeresés a Visual Studio Code notebookok

A kód cellák a jegyzetfüzet nem a várt módon működni, ha lehet kódhibák vagy más hibák. Azonban a változók értékének megjelenítéséhez a `print` utasítás használata helyett a tipikus Jupyter-környezet nem biztosít hibakeresési szolgáltatásokat.

Szerencsére letöltheti a notebook *. ipynb* -fájlját, majd a Python bővítménnyel megnyithatja a Visual Studio Code-ban. A bővítmény közvetlenül importál egy jegyzetfüzetet egyetlen kódlapként, megőrizve a Markdown-cellákat a megjegyzésekben. Miután importálta a jegyzetfüzetet, használhatja a Visual Studio Code debuggert a kód beléptetéséhez, a töréspontok beállításához, az állapot vizsgálatához és így tovább. A kód javítása után exportálja a *. ipynb* fájlt a Visual Studio Code-ból, és töltse fel újra Azure Notebooksba.

További információ: Jupyter- [Jegyzetfüzet hibakeresése](https://code.visualstudio.com/docs/python/jupyter-support#debug-a-jupyter-notebook) a Visual Studio Code dokumentációjában.

Lásd még: a [Visual Studio Code-Jupyter támogatása](https://code.visualstudio.com/docs/python/jupyter-support) a Visual Studio Code további szolgáltatásaihoz a Jupyter notebookokhoz.

## <a name="next-steps"></a>Következő lépések

- [Minta-jegyzetfüzetek megismerése](azure-notebooks-samples.md)

Útmutatók:

- [Projektek létrehozása és klónozása](create-clone-jupyter-notebooks.md)
- [Projektek konfigurálása és kezelése](configure-manage-azure-notebooks-projects.md)
- [Csomagok telepítése jegyzetfüzetből](install-packages-jupyter-notebook.md)
- [Bemutató megjelenítése](present-jupyter-notebooks-slideshow.md)
- [Adatfájlok használata](work-with-project-data-files.md)
- [Hozzáférés az adaterőforrásokhoz](access-data-resources-jupyter-notebooks.md)
- [Azure Machine Learning használata](use-machine-learning-services-jupyter-notebooks.md)
