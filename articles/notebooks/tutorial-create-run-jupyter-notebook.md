---
title: Oktatóanyag – létrehozása és futtatása a Jupyter notebook az Azure-ban
description: Hogyan lehet egy futtatása Jupyter notebook létrehozása Azure-jegyzetfüzetekben, azt mutatja be, az adatelemzés lineáris regressziós folyamatán.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 65bbb5fe-9939-4e8e-8f5b-c197d4be142a
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: kraigb
ms.openlocfilehash: d5ccf3e9f35a8d35387962278577333ff92ff02b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59258532"
---
# <a name="tutorial-create-and-run-a-jupyter-notebook-with-python"></a>Oktatóanyag: létrehozása és futtatása a Jupyter notebook Python használatával

Ez az oktatóanyag végigvezeti a folyamat azt mutatja be, lineáris regresszió egyszerű befejezése Jupyter notebook létrehozása Azure jegyzetfüzeteinek előnyeit. Ebben az oktatóanyagban képviselőinkkel, ismerkedjen meg a Jupyter notebook felhasználói felületén, például a különböző cellák létrehozásához, cellák fut, és diavetítésként a notebook bemutatása.

A befejezett notebook találhatók [GitHub - jegyzetfüzetek minták Azure](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps). Ebben az oktatóanyagban egy új projektet és a egy üres notebook azonban kezdődik, így a tapasztalhatnak, lépésről lépésre hozza létre.

## <a name="create-the-project"></a>A projekt létrehozása

1. Lépjen a [Azure notebookok](https://notebooks.azure.com) , és jelentkezzen be. (További információkért lásd: [rövid útmutató – jelentkezzen be Azure-jegyzetfüzetek](quickstart-sign-in-azure-notebooks.md)).

1. Válassza ki a nyilvános profiloldalán **saját projektek** az oldal tetején:

    ![A böngésző ablakának felső részén saját projektek hivatkozás](media/quickstarts/my-projects-link.png)

1. A a **saját projektek** lapon jelölje be **+ új projekt** (billentyűparancs: n); a gomb csak megjelenik **+** Ha a böngészőablak keskeny:

    ![Új projekt parancsot a saját projektek lapon](media/quickstarts/new-project-command.png)

1. Az a **új projekt létrehozása** előugró ablak, amely akkor jelenik meg, adja meg, vagy állítsa be a következő adatokat, majd válassza ki **létrehozás**:

    - **Projektnév**: Lineáris regresszió példa – Cricket utánzó rendszerhangokat
    - **A projekt Azonosítóját**: lineáris regressziós – példa
    - **Nyilvános projekt**: (törölve)
    - **Hozzon létre egy README.md**: (törölve)

1. Néhány pillanat múlva Azure notebookok nyit meg az új projekt.

## <a name="create-the-data-file"></a>Az adatfájl létrehozása

A lineáris regressziós modellt hoz létre a jegyzetfüzet adatok megrajzolja a nevű projekt fájlból *cricket_chirps.csv*. Ez a fájl másolásával, vagy hozhat létre [GitHub - jegyzetfüzetek minták Azure](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps), vagy közvetlenül az adatok megadásával. A következő szakaszok mindkét módszert ismertetik.

### <a name="upload-the-data-file"></a>Az adatfájl feltöltése

1. Az Azure-jegyzetfüzetekben projekt irányítópultján, válassza ki a **feltöltése** > **URL-CÍMRŐL**
1. Az előugró ablakban adja meg a következő URL-cím **fájl URL-cím** és *cricket_chirps.csv* a **Fájlnév**, majd **kész**.

    ```url
    https://raw.githubusercontent.com/Microsoft/AzureNotebooks/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps/cricket_chirps.csv
    ```

1. A *cricket_chirps.csv* fájlt meg kell jelennie a projekt fájllista:

    ![Az újonnan létrehozott CSV-fájlt a projekt fájlok listájának megjelenítése](media/tutorial/csv-file-in-project.png)

### <a name="create-a-file-from-scratch"></a>Sablon nélkül hozzon létre egy fájlt

1. Az Azure-jegyzetfüzetekben projekt irányítópultján, válassza ki a **+ új** > **üres fájl**
1. Egy mezőt a projekt fájl listájában jelenik meg. Adjon meg *cricket_chirps.csv* nyomja le az Enter billentyűt.
1. Kattintson a jobb gombbal *cricket_chirps.csv* válassza **fájl szerkesztése**.
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

1. Válassza ki **fájl mentése** mentse a fájlt, és térjen vissza a projekt irányítópultján.

## <a name="install-project-level-packages"></a>Projekt szolgáltatásiszint-csomagok telepítése

A notebook belül mindig parancsokat használhatja például a `!pip install` egy kódcellába szükséges csomagok telepítéséhez. Azonban az ilyen parancsok futtatása minden alkalommal, amikor a notebook kód cellák futtatja, és hosszabb időt is igénybe vehet. Ebből kifolyólag inkább telepíthet csomagokat a projekthez szinten az egy `requirements.txt` fájlt.

1. Az ismertetett folyamat használata [hozzon létre egy fájlt a teljesen új](#create-a-file-from-scratch) nevű fájl létrehozása `requirements.txt` a következő tartalommal:

    ```text
    matplotlib==3.0.0
    numpy==1.15.0
    pandas==0.23.4
    scikit-learn==0.20.0
    ```

    Emellett feltölthet egy `requirements.txt` fájlt a helyi számítógépről igény szerint leírt [az adatfájl feltöltése](#upload-the-data-file).

1. Válassza ki a projekt irányítópultján **Projektbeállítások**.
1. A megjelenő előugró ablakban válassza ki a **környezet** lapfülre, majd válassza ki **+ Hozzáadás**.
1. Az első legördülő vezérlő (művelet) alatt **környezet beállítási lépéseket**, válassza a **Requirements.txt**.
1. Válassza ki a második legördülő vezérlőben (fájlnév) *requirements.txt* (a létrehozott fájlt).
1. A harmadik legördülő vezérlőt (a Python-verzió) válassza **Python 3.6-os verzió**.
1. Kattintson a **Mentés** gombra.

![A projekt beállítások környezet lapon requirements.txt fájl megadása](media/tutorial/tutorial-requirements-txt.png)

A telepítő lépés helyen a projekt futtatása minden olyan jegyzetfüzet fog futni egy környezetben amelyen telepítve vannak-e az azokat a csomagokat.

## <a name="create-and-run-a-notebook"></a>Notebook létrehozása és futtatása

Készen áll az adatfájlban és a projekt környezet beállítása hogy mostantól létrehozhat és nyissa meg a notebook.

1. Válassza ki a projekt irányítópultján **+ új** > **Notebook**.
1. Az előugró ablakban adja meg a *lineáris regressziós példa – Cricket Chirps.ipynb* a **elem neve**, válassza a **Python 3.6-os** a nyelvet, majd válassza **új**.
1. Miután az Új jegyzetfüzet a fájllista megjelenik, válassza ki, hogy a notebook. Egy új böngészőlapon nyílik meg automatikusan.
1. Mert rendelkezik egy *requirements.txt* fájl a környezet beállításaiban látja az üzenetet, "Várakozás a tároló befejezéséhez a előkészítésére." Választhat **OK** zárja be az üzenetet, és folytatni a munkát a notebook; kód cellák, azonban nem tudja futtatni, amíg a környezet teljes van beállítva.
1. A Notebookban egy üres kódcellába Jupyter kapcsolaton alapértelmezett nyílik meg.

    [![Kezdeti nézete egy új jegyzetfüzetet az Azure-jegyzetfüzetekben](media/tutorial/tutorial-new-notebook.png)](media/tutorial/tutorial-new-notebook.png#lightbox)

## <a name="tour-the-notebook-interface"></a>A notebook felület bemutatása

A notebook fut, a kód Markdown cellák, futtassa a cellákat és kezelheti a notebookot az is hozzáadhat. Először azonban érdemes Ismerkedjen meg a kapcsolat néhány percet vesz igénybe. A teljes dokumentációt, válassza a **súgó** > **súgója Notebook** parancs.

Az ablak tetején jelenik meg a következő elemek:

(A) a neve, a jegyzetfüzet, amelyre kattintva szerkesztheti.
(B) a tartalmazó projekt és a projekt irányítópultján, amely új lapok megnyitása a böngészőben nyissa meg a gombokat.
(C) A menü-parancsokkal a notebook való munkához.
(D) eszköztár parancsikonjait gyakori műveletekhez.
(E) a cellákat tartalmazó szerkesztési vászonra.
(F) jelzi, hogy a notebook megbízható (alapértelmezett érték a **nem megbízható**).
(G) a kernel a jegyzetfüzet futtatásához egy tevékenységet jelző együtt használja.

[![A Jupyter felület területeit elsődleges felhasználói felület](media/tutorial/tutorial-notebook-ui.png)](media/tutorial/tutorial-notebook-ui.png#lightbox)

Jupyter biztosít beépített Ismerkedjen meg az elsődleges felhasználói felületi elemeket. A bemutató első lépésként válassza ki a **súgó** > **felhasználói felületet bemutató** parancs, és kattintson az előugró ablakok keresztül.

A csoportok menü parancsok a következők:

| Menü | Leírás |
| --- | --- |
| Fájl | A notebook fájlt, beleértve azokat a parancsokat hozhat létre, és másolja a jegyzetfüzet, kezelésére szolgáló parancsok egy nyomtatási kép megjelenítése, és töltse le a notebookot a számos különböző formátumban. |
| Szerkesztés | Kivágás, másolja, és illessze be a cellák, keresse meg és cserélje le az értékeket, tipikus parancsokat cella mellékletek kezelése, és helyezze be a lemezképek.  |
| Nézet | A Jupyter felhasználói felület más részein láthatóságot parancsokat. |
| Beszúrás | Új cella felett vagy alatt az aktuális cella beszúrása parancsokat. Ezek a parancsok gyakran egy jegyzetfüzetet létrehozásakor használ. |
| Cella | A különböző **futtatása** parancsok futtatása egy vagy több cella különböző kombinációkban. A **cella típusa** parancsok között egy cella típusának módosítása **kód**, **Markdown**, és **nyers NBConvert** (egyszerű szöveg). A **aktuális kimenetek** és **összes kimenetének** parancsok vezérelheti, hogyan kimeneti futtatási kódból látható, és törölje az összes kimeneti parancsot tartalmazza. |
| Kernel | Hogyan kód futtatják a kernel mentén kezelését végző parancsokról **módosítása kernel** nyelv vagy a jegyzetfüzet futtatásához használt Python-verzió módosításához. |
| Adatok | Fájlok feltöltését és letöltését a projekt vagy a munkamenet parancsokat. Lásd: [soubory projektu adatok használata](work-with-project-data-files.md) |
| widgetek | Kezelését végző parancsokról [Jupyter Widgetek](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Basics.html), amely további funkciókat biztosítsanak a képi megjelenítés, a leképezési és küldik az ábrázolást.|
| Súgó | Parancsok, nyújthat segítséget és a Jupyter felület dokumentációját. |

Legtöbb parancs, az eszköztáron rendelkezik egyenértékű menüparancsai. Kivételt jelent **Enter/Szerkesztés NÖVEKMÉNYE Diavetítés**, amely a következő cikkben [megosztás és a jelen notebookok](present-jupyter-notebooks-slideshow.md).

Ezek a parancsok számos módon feltölti a notebookot az alábbi szakaszok a használhatja.

## <a name="create-a-markdown-cell"></a>Hozzon létre egy Markdown-cella

1. Kattintson a notebook vásznon látható első üres cellába. Alapértelmezés szerint a cella nem egy **kód** típusa, ami azt jelenti, hogy a feladata, hogy a kiválasztott kernel futtatható kódját tartalmazza (Python, R, vagy F#). A jelenlegi típus: írja be a legördülő menüből az eszköztáron látható:

    ![Cella típusa eszköztár legördülő menü](media/tutorial/tutorial-cell-type-drop-down.png)

1. Módosítsa a cella típusát **Markdown** az eszköztár legördülő menü használatával; az azt is megteheti, használhatja a **cella** > **cella típusa**  >   **Markdown** parancs:

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

1. Jelennek meg a Markdown HTML böngésző, válassza a **futtatása** parancsot az eszköztáron, vagy használja a **cella** > **futtatása cellák** parancsot. A Markdown kódot a formázás és hivatkozások most őket egy böngészőben a várt módon jelennek meg.

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

1. Adja meg az alábbi parancsokat a kódcellába, amely után a használt megjelent **futtatása** a korábbi Markdown cellára. Ha egy új cella nem jelenik meg, hozzon létre egyet a **beszúrása** > **cella beszúrása alábbi** vagy használja a **+** gombra az eszköztáron.

    ```bash
    !pip install numpy
    !pip install matplotlib
    !pip install pandas
    !pip install sklearn
    ```

1. Mielőtt futtatná a cellára, hozzon létre egy új cellára a **+** gombra az eszköztáron, beállíthatja azt a Markdown és a következő magyarázatot:

    ```markdown
    Note that when you run a code block that contains install commands, and also those with `import` statements, it make take the notebooks a little time to complete the task. To the left of the code block you see `In [*]` to indicate that execution is happening. The Notebook's kernel on the upper right also shows a filled-in circle to indicate "busy."
    ```

1. Válassza ki a **cella** > **összes futtatása** parancs összes cellát a jegyzetfüzet futtatásához. Figyelje meg, hogy a Markdown cellák HTML formátumban jelennek meg, és a parancs futtatása a kernel, és figyelje meg a kernel kijelző, a markdown-szöveg leírtak szerint:

    ![A notebook kernel foglalt mutatója](media/tutorial/tutorial-kernel-busy.png)

1. Még tart egy kis időt az összes a `pip install` parancsok futtatásához, és mivel ezeket a csomagokat a projekthez környezetben már telepítve (és mivel azok már is megtalálható az Azure-jegyzetfüzetek alapértelmezés szerint), láthatja, hogy olvassa el a "követelmény számos üzenetek már elégedett az eredménnyel." Az összes, a kimeneti vizuálisan zavaró, ezért válassza, amelyek értékesíteni (egyetlen kattintással használatával), majd az is lehet a **cella** > **cella kimenetek** > **váltógomb**elrejtése a kimenetet. Is használhatja a **egyértelmű** teljesen eltávolítja a kimenetet, hogy ugyanazon almenü parancsot.

    A **váltógomb** parancs elrejti a cellából csak a legutóbbi kimeneti; Ha a cella ismét futtatja, a kimeneti ismét megjelenik.

1. Mivel a csomagok telepítése a projekt környezetében, tegye megjegyzésbe a `! pip install` parancsok használatával `#`; ezzel a módszerrel a jegyzetfüzet oktatási anyagok is maradnak, de nem minden időt vesz igénybe, és nem állít elő a szükségtelen kimenetet. Ebben az esetben a notebookot a megjegyzésekkel parancsok tartja azt is jelzi a notebook függőségek.

    ```bash
    # !pip install numpy
    # !pip install matplotlib
    # !pip install pandas
    # !pip install sklearn
    ```

## <a name="create-the-remaining-cells"></a>A fennmaradó cellák létrehozása

A többi a notebook feltöltéséhez, kell létrehoznia egy sorozatát Markdown és a kódot. Az alább felsorolt egyes cellák először hozzon létre az új cellára, majd állítsa be a típusát, majd illessze be a tartalom.

Bár minden cella létrehozását követően a jegyzetfüzet futtatásához várhat, érdemes futtatni minden cella létrehozása közben. Nem minden cellák megjelenítése kimeneti; Ha nem látja az esetleges hibákat, azt feltételezik, a cella általában futott.

Minden egyes kódcella attól függ, a kódot, amely az előző cella fut, és nem a cellák valamelyike futtassa így tesz, ha újabb cellák eredményezhet a hibák. Ha azt tapasztalja, hogy elfelejtette a futtatásához egy cellát, próbáljon a **cella** > **futtatása az összes fenti** az aktuális cella futtatása előtt.

Ha nem várt eredmények (Ez valószínűleg fogja!), ellenőrizze, hogy minden cella értéke "Code" vagy "Markdown" szükség szerint. Például "Érvénytelen szintaxis" hiba általában történik, ha megadta a Markdown kódot cellába.

1. Markdown-cella:

    ```markdown
    ## Import packages and prepare the dataset

    In this example we're using numpy, pandas, and matplotlib. Data is in the file cricket_chirps.csv. Because this file is in the same project as this present Notebook, we can just load it using a relative pathname.
    ```

1. Kódcella; Futtatás jelenít meg a tábla tartalmának kimenetként. A kimenet tilthatja le a megjegyzéseket a `print` utasítást.

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

    The regressor's `fit` method here creates the line, which algebraically is of the form `y = x*b1 + b0`, where b1 is the coefficient or slope of the line (which you can get to through `regressor.coef_`), and b0 is the intercept of the line at x=0 (which you can get to through `regressor.intercept`).
    ```

1. Kódcella; futtatásakor a cella jeleníti meg a kimeneti `LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None,normalize=False)`.

    ```python
    from sklearn.linear_model import LinearRegression

    regressor = LinearRegression()    # This object is the regressor, that does the regression
    regressor.fit(X_train, y_train)   # Provide training data so the machine can learn to predict using a learned model.
    ```

1. Markdown-cella:

    ```markdown
    ## Predict the results

    With the regressor in hand, we can predict the test set results using its `predict` method. That method takes a vector of independent variables for which you want predictions.

    Because the regressor is fit to the data by virtue of `coef_` and `intercept_` and `coef_`, a prediction is the result of `coef_ * x + intercept_`. (Indeed, `predict(0)` returns `intercept_` and `predict(1)` returns `intercept_ + coef_`.)

    In the code, the `y_test` matrix (from when we split the set) contains the real observations. `y_pred` assigned here contains the predictions for the same `X_test` inputs. It's not expected that the test or training points exactly fit the regression; the regression is trying to find the model that we can use to make predictions with new observations of the independent variables.
    ```

1. Kódcella; futtatja, a cella jelenít meg eredményt, például `[79.49588055 75.98873911 77.87719989 80.03544077 75.17939878]`.

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

1. Törölje a kernel munkamenet az adatokat és az összes kimeneti cella kiválasztásával **Kernel** > **Újraindítás & egyértelmű kimeneti**. Ez a parancs mindig egy jó egy-egy jegyzetfüzetet csak, győződjön meg arról, hogy még nem létrehozott kód cellák között furcsa függőségek befejezése után futtassa.

1. Futtassa újra a notebook használatával **cella** > **összes futtatása**. Figyelje meg, hogy a kernel kijelző kitölti a kód futtatása közben.

    Ha bármilyen kódot, amely hosszabb ideig fut, vagy más módon elakad, állítsa le a kernel használatával a **Kernel** > **megszakítási** parancsot.

1. Görgessen végig a notebook vizsgálja meg az eredményeket. (Ha újra a diagram nem jelenik meg, indítsa újra a cella.)

## <a name="save-halt-and-close-the-notebook"></a>Mentse, leállíthatja, és bezárja a notebookot

A időszakban szerkeszt egy jegyzetfüzetet, mentheti a jelenlegi állapotában a **fájl** > **mentése és ellenőrzőpont** parancsot vagy a Mentés gombra az eszköztáron. "Ellenőrzőpont", amely a munkamenet során bármikor visszatérhet pillanatképet hoz létre. Ellenőrzőpontok lehetővé teszik, hogy a kísérleti módosítások sorozata, és ezek a módosítások nem működnek, ha csak térhet vissza egy ellenőrzőpontot a a **fájl** > **ellenőrzőpont visszaállítása** parancsot. Egy alternatív módszer az, hogy további cellákat és tegye megjegyzésbe, amelyet szeretne futtatni; kód létrehozása mindkét esetben működik.

Is használhatja a **fájl** > **készítsen róla egy másolatot** parancsot, és győződjön meg a notebook aktuális állapotát egy példányát a projektben egy új fájlba. A másolatot automatikusan megnyílik egy új böngészőlapon.

Ha elkészült, a jegyzetfüzet, használja a **fájl** > **Bezárás és halt** parancsot, amely bezárja a notebookot, és a kernel futott, hogy leállítja. Az Azure notebookok majd a böngészőlapon automatikusan bezáródik.

## <a name="debug-notebooks-using-visual-studio-code"></a>Hibakeresés a Visual Studio Code notebookok

A kód cellák a jegyzetfüzet nem a várt módon működni, ha lehet kódhibák vagy más hibák. Azonban más, az `print` utasításokat egy tipikus Jupyter-környezet nem biztosít semmilyen hibakeresési létesítményekben változók, értéket jeleníti meg.

Szerencsére a letöltheti a notebook *.ipynb* fájlt, majd nyissa meg a Visual Studio Code a Python-bővítmény használatával. A bővítmény közvetlenül importálja egy jegyzetfüzetet egyetlen fájlként, megőrizve a Markdown cellák megjegyzéseket. A notebook importálása után a Visual Studio Code hibakereső segítségével végighaladhat a kódot, állítson be töréspontokat, vizsgálja meg az állapot és így tovább. Után korrigálás kell a kódot, majd exportálja a *.ipynb* fájlt a Visual Studio Code-ból, és töltse fel újra az Azure-jegyzetfüzetek üzembe.

További információkért lásd: [Jupyter notebook Debug](https://code.visualstudio.com/docs/python/jupyter-support#debug-a-jupyter-notebook) a Visual Studio Code dokumentáció.

Is [Visual Studio Code - Jupyter támogatási](https://code.visualstudio.com/docs/python/jupyter-support) Jupyter notebookokhoz Visual Studio Code, további funkciók.

## <a name="next-steps"></a>További lépések

- [Ismerkedés a mintafüzetek](azure-notebooks-samples.md)

Útmutatók:

- [Hozzon létre, és klónozza a projektek](create-clone-jupyter-notebooks.md)
- [Konfigurálhatja és kezelheti a projektek](configure-manage-azure-notebooks-projects.md)
- [Egy jegyzetfüzetet a csomagok telepítése](install-packages-jupyter-notebook.md)
- [Diavetítés bemutatásához](present-jupyter-notebooks-slideshow.md)
- [Adatfájlok használata](work-with-project-data-files.md)
- [Adatok erőforrások eléréséhez](access-data-resources-jupyter-notebooks.md)
- [Az Azure Machine Learning-szolgáltatások használata](use-machine-learning-services-jupyter-notebooks.md)
