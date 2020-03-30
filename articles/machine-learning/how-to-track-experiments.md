---
title: Ml-kísérletek naplózása & mérőszámok
titleSuffix: Azure Machine Learning
description: Figyelje az Azure ML-kísérleteket, és figyelje a futtatási metrikákat a modell létrehozási folyamatának javítása érdekében. Adja hozzá a naplózást a betanítási parancsfájlhoz, és tekintse meg a futtatás naplózott eredményeit.  Használja a run.log, run.start_logging vagy ScriptRunConfig fájlt.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/12/2020
ms.custom: seodec18
ms.openlocfilehash: 0c77e9d0aa4f44f33b1345a6021fc0378459ee85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79296965"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>Az Azure ML-kísérletek futásának és metrikáinak figyelése
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

A kísérletek nyomon követésével és a futtatási mérőszámok figyelésével javíthatja a modell létrehozási folyamatát. Ebben a cikkben megtudhatja, hogyan adhat hozzá naplózási kódot a betanítási parancsfájlhoz, küldhet be egy kísérletfuttatást, figyelheti a futót, és hogyan vizsgálhatja meg az eredményeket az Azure Machine Learningben.

> [!NOTE]
> Az Azure Machine Learning is naplózhatja az adatokat más forrásokból a képzés során, például az automatikus gépi tanulási fut, vagy a Docker-tároló, amely futtatja a betanítási feladat. Ezek a naplók nincsenek dokumentálva. Ha problémákat tapasztal, és kapcsolatba lép a Microsoft támogatási szolgálatával, előfordulhat, hogy hibaelhárítás kor használhatják ezeket a naplókat.

> [!TIP]
> A jelen dokumentumban szereplő információk elsősorban adatszakértők és fejlesztők számára készült, akik a modell betanítási folyamatát szeretnék figyelni. Ha ön az Azure Machine Learning erőforrás-használatának és eseményeinek figyelése iránt érdeklődő rendszergazda, például kvóták, befejezett képzési futtatások vagy befejezett modelltelepítések, olvassa el [az Azure Machine Learning figyelése.](monitor-azure-machine-learning.md)

## <a name="available-metrics-to-track"></a>Nyomon követhető mérőszámok

A következő metrikák adhatók hozzá egy futtatáshoz egy kísérlet betanítása közben. Ha részletesebb listát szeretne látni arról, hogy mi követhető nyomon futás közben, olvassa el az [Osztály futtatása hivatkozási dokumentációt.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)

|Típus| Python függvény | Megjegyzések|
|----|:----|:----|
|Skaláris értékek |Funkció:<br>`run.log(name, value, description='')`<br><br>Példa:<br>run.log("pontosság", 0,95) |Naplózjon egy numerikus vagy karakterláncértéket a megadott névvel rendelkező futtatáshoz. Ha egy metrikát naplóz egy futtatásra, akkor a mérőszám a kísérlet ben lévő futtatási rekordban tárolódik.  Egy futtatáson belül többször is naplózhatja ugyanazt a metrikát, és az eredmény az adott mutató vektorának számít.|
|Listák|Funkció:<br>`run.log_list(name, value, description='')`<br><br>Példa:<br>run.log_list("pontosság", [0,6, 0,7, 0,87]) | Naplózza az értékek listáját a megadott névvel rendelkező futtatásra.|
|Sor|Funkció:<br>`run.log_row(name, description=None, **kwargs)`<br>Példa:<br>run.log_row("Y x felett", x=1, y=0,4) | A *log_row* használatával a kwargs-ban leírt több oszlopot tartalmazó mutatót hoz létre. Minden elnevezett paraméter létrehoz egy megadott értékű oszlopot.  *log_row* meg lehet hívni egyszer, hogy jelentkezzen be egy tetszőleges törzs, vagy többször egy hurok, hogy létrehoz egy teljes táblát.|
|Tábla|Funkció:<br>`run.log_table(name, value, description='')`<br><br>Példa:<br>run.log_table("Y over X", {"x":[1, 2, 3], "y":[0,6, 0,7, 0,89]}) | Szótárobjektum naplózása a megadott névvel rendelkező futtatáshoz. |
|Képek|Funkció:<br>`run.log_image(name, path=None, plot=None)`<br><br>Példa:<br>`run.log_image("ROC", plot=plt)` | Jelentkezzen be egy lemezképet a futtatási rekordba. A log_image segítségével naplózjon egy képfájlt vagy egy matplotlib-tel a futtatáshoz.  Ezek a képek láthatók és összehasonlíthatók lesznek a futtatási rekordban.|
|Futtatás címkézése|Funkció:<br>`run.tag(key, value=None)`<br><br>Példa:<br>run.tag("kiválasztva", "igen") | Címkézze fel a futtatást egy karakterlánc-kulccsal és a választható karakterláncértékkel.|
|Fájl vagy könyvtár feltöltése|Funkció:<br>`run.upload_file(name, path_or_stream)`<br> <br> Példa:<br>run.upload_file("best_model.pkl", "./model.pkl") | Töltsön fel egy fájlt a futtatási rekordba. Automatikusan rögzíti a fájlt a megadott kimeneti könyvtárban, amely alapértelmezés szerint "./outputs" a legtöbb futtatási típusesetében.  Csak akkor használja upload_file, ha további fájlokat kell feltölteni, vagy nincs megadva kimeneti könyvtár. Javasoljuk, `outputs` hogy adja hozzá a nevet, hogy a kimeneti könyvtárba kerüljön. A futtatási rekordhoz társított összes fájlt felsorolhatja a`run.get_file_names()`|

> [!NOTE]
> A skalárok, listák, sorok és táblák metrikái nak típusa lehet: úszó, egész vagy karakterlánc.

## <a name="choose-a-logging-option"></a>Naplózási beállítás kiválasztása

Ha nyomon szeretné követni vagy figyelni szeretné a kísérletet, a futtatás elküldésekor kódot kell hozzáadnia a naplózás megkezdéséhez. A futtatási beküldés ta- és elküldésének elindításának módjai a következők:
* __Run.start_logging__ – Naplózási függvények hozzáadása a betanítási parancsfájlhoz, és interaktív naplózási munkamenet indítása a megadott kísérletben. **start_logging** létrehoz egy interaktív futtatást olyan esetekben való használatra, mint például a jegyzetfüzetek. A munkamenet során naplózott mérőszámok hozzáadódnak a kísérlet futtatási rekordjához.
* __ScriptRunConfig__ - Naplózási függvények hozzáadása a betanítási parancsfájlhoz, és töltse be a teljes parancsfájlmappát a futtatással.  **A ScriptRunConfig** a parancsfájlfuttatások konfigurációinak beállítására szolgáló osztály. Ezzel a beállítással hozzáadhat figyelési kódot, amelyértesítést kap a befejezésről, vagy vizuális widgetet kaphat a figyeléshez.

## <a name="set-up-the-workspace"></a>A munkaterület beállítása
A naplózás hozzáadása és a kísérlet elküldése előtt be kell állítania a munkaterületet.

1. Töltse be a munkaterületet. A munkaterület konfigurációjának beállításáról a [munkaterület konfigurációs fájljában olvashat bővebben.](how-to-configure-environment.md#workspace)

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=load_ws)]


## <a name="option-1-use-start_logging"></a>1. lehetőség: Start_logging használata

**start_logging** létrehoz egy interaktív futtatást olyan esetekben való használatra, mint például a jegyzetfüzetek. A munkamenet során naplózott mérőszámok hozzáadódnak a kísérlet futtatási rekordjához.

A következő példa egy egyszerű sklearn Ridge modellhelyibe vonatok be egy helyi Jupyter notebookban. Ha többet szeretne tudni a rról, hogy miként küldheti el a kísérleteket különböző környezetekben, olvassa [el a Számítási célok beállítása modelloktatáshoz az Azure Machine Learning használatával című témakört.](https://docs.microsoft.com/azure/machine-learning/how-to-set-up-training-targets)

### <a name="load-the-data"></a>Az adatok betöltése

Ez a példa a cukorbetegség adatkészlet, egy jól ismert kis adatkészlet, amely scikit-learn. Ez a cella betölti az adatkészletet, és véletlenszerű betanítási és tesztelési készletekre osztja fel.

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=load_data)]

### <a name="add-tracking"></a>Követés hozzáadása
Adja hozzá a kísérletkövetést az Azure Machine Learning SDK használatával, és töltsön fel egy megőrzött modellt a kísérlet futtatási rekordjába. A következő kód címkéket, naplókat és egy modellfájlt tölt fel a kísérlet futtatásához.

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=create_experiment)]

A parancsfájl ```run.complete()```végződik , amely a futtatást befejezettként jelöli meg.  Ez a funkció általában interaktív jegyzetfüzet-forgatókönyvekben használatos.

## <a name="option-2-use-scriptrunconfig"></a>2. lehetőség: ScriptRunConfig használata

[**A ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) a parancsfájlfuttatások konfigurációinak beállítására szolgáló osztály. Ezzel a beállítással hozzáadhat figyelési kódot, amelyértesítést kap a befejezésről, vagy vizuális widgetet kaphat a figyeléshez.

Ez a példa kiterjeszti az alapvető sklearn Ridge modell felülről. Ez egy egyszerű paraméter söprés a modell alfa-értékeit, hogy rögzítse a metrikák és a betanított modellek fut a kísérlet alatt. A példa helyileg fut egy felhasználó által felügyelt környezetben. 

1. Képzési parancsfájl `train.py`létrehozása .

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]

2. A `train.py` parancsfájl `mylib.py` hivatkozások, amely lehetővé teszi, hogy a gerinc modellben használandó alfa-értékek listáját.

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/mylib.py)] 

3. Felhasználó által felügyelt helyi környezet konfigurálása.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=user_managed_env)]


4. Küldje ```train.py``` el a parancsfájlt a felhasználó által felügyelt környezetben való futtatáshoz. Ez a teljes parancsfájlmappa be van ```mylib.py``` küldve betanításra, beleértve a fájlt is.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)] [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]




## <a name="manage-a-run"></a>Futás kezelése

A [Betanítás indítása, figyelése és megszakítása](how-to-manage-runs.md) cikk kiemeli az Azure Machine Learning-munkafolyamatokat a kísérletek kezeléséhez.

## <a name="view-run-details"></a>Futtatás részleteinek megtekintése

### <a name="view-activequeued-runs-from-the-browser"></a>Aktív/várakozási sorba állított futtatások megtekintése a böngészőből

A modellek betanításához használt számítási célok megosztott erőforrások. Mint ilyen, előfordulhat, hogy több fut várólistára helyezett vagy aktív egy adott időpontban. Egy adott számítási cél futtatásai a böngészőből való megtekintéséhez kövesse az alábbi lépéseket:

1. Az [Azure Machine Learning-stúdióban](https://ml.azure.com/)válassza ki a munkaterületet, majd válassza a __Számítási__ a lap bal oldalán.

1. Válassza __ki a képzési fürtök__ a betanításhoz használt számítási célok listájának megjelenítéséhez. Ezután válassza ki a fürtöt.

    ![Válassza ki a betanítási fürtöt](./media/how-to-track-experiments/select-training-compute.png)

1. Válassza __a Futtatások__lehetőséget. Megjelenik a fürtöt használó futtatások listája. Egy adott futtatás részleteinek megtekintéséhez használja a __Futtatás__ oszlopban lévő hivatkozást. A kísérlet részleteinek megtekintéséhez használja a __Kísérlet__ oszlopban található hivatkozást.

    ![Futtatások kiválasztása a betanítási fürthöz](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > Egy futtatás gyermekfuttatásokat is tartalmazhat, így egy betanítási feladat több bejegyzést eredményezhet.

A futtatás befejezése után már nem jelenik meg ezen az oldalon. A befejezett __futtatásokkal__ kapcsolatos információk megtekintéséhez keresse fel a stúdió Kísérletek szakaszát, és válassza ki a kísérletet, és futtassa. További információt a [Lekérdezés futtatási mutatók](#queryrunmetrics) című szakaszban talál.

### <a name="monitor-run-with-jupyter-notebook-widget"></a>Monitor fut Jupyter notebook widget
Ha a **ScriptRunConfig** metódust használja a futtatások elküldéséhez, a futtatás előrehaladását [jupyter widgettel nézheti](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)meg. A futtatás elküldéséhez hasonlóan a vezérlő aszinkron módon működik, és 10-15 másodpercenként élő állapotfrissítést biztosít a feladat befejeződéséig.

1. Tekintse meg a Jupyter widgetet, miközben várja a futás befejezését.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Képernyőkép a Jupyter notebook widgetről](./media/how-to-track-experiments/run-details-widget.png)

   A munkaterületen ugyanarra a kijelzőre mutató hivatkozást is beszerezhet.

   ```python
   print(run.get_portal_url())
   ```

2. **[Automatikus gépi tanulási futtatások esetén]** A diagramok elérése egy korábbi futtatásból. Cserélje `<<experiment_name>>` ki a megfelelő kísérletnévre:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Jupyter notebook widget az automatizált gépi tanuláshoz](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


További részleteket a folyamat kattintson a folyamat, amelyet szeretne felfedezni a táblázatban, és a diagramok jelennek meg egy előugró ablakban az Azure Machine Learning stúdióban.

### <a name="get-log-results-upon-completion"></a>Naplóeredmények lekérése a befejezéskor

Modell betanítása és figyelése történik a háttérben, így futtathatja más feladatokat, amíg vár. További kód futtatása előtt megvárhatja, amíg a modell befejezi a betanítást. A **ScriptRunConfig**használatakor a ```run.wait_for_completion(show_output = True)``` modell betanításának befejezése korjelenítheti meg. A ```show_output``` zászló részletes kimenetet ad. 

<a id="queryrunmetrics"></a>

### <a name="query-run-metrics"></a>Lekérdezési futtatási mutatók

A betanított modell metrikákat a használatával ```run.get_metrics()```tekintheti meg. Most már lekaphatja a fenti példában bejelentkezett összes metrikát a legjobb modell meghatározásához.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-your-workspace-in-azure-machine-learning-studio"></a>A kísérlet megtekintése a munkaterületen az [Azure Machine Learning stúdióban](https://ml.azure.com)

Ha egy kísérlet futása befejeződött, tallózhat a rögzített kísérletfuttatási rekordra. Az előzményeket az [Azure Machine Learning stúdióból](https://ml.azure.com)érheti el.

Nyissa meg a Kísérletek lapot, és válassza ki a kísérletet. A rendszer a kísérlet futtatási irányítópultjára kerül, ahol megtekintheti a nyomon követett mutatókat és az egyes futtatásokhoz naplózott diagramokat. Ebben az esetben az MSE és az alfa értékeket naplóztuk.

  ![Részletek futtatása az Azure Machine Learning stúdióban](./media/how-to-track-experiments/experiment-dashboard.png)

Leáshat egy adott futtatásra a kimenetek vagy naplók megtekintéséhez, vagy letöltheti a beküldött kísérlet pillanatképét, hogy megoszthassa a kísérletmappát másokkal.

### <a name="viewing-charts-in-run-details"></a>Diagramok megtekintése futtatási részletekben

A naplózási API-k használatával különböző típusú metrikákat rögzíthet egy futtatás során, és megtekintheti őket diagramként az Azure Machine Learning stúdióban.

|Naplózott érték|Mintakód| Megtekintés a portálon|
|----|----|----|
|Numerikus értékek tömbjének naplózása| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|egyváltozós vonaldiagram|
|Egyetlen numerikus érték naplózása ugyanazzal a metrikanévvel, amelyet többször is használtak (például a for cikluson belülről)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Egyváltozós vonaldiagram|
|2 numerikus oszlopot tartalmazó sor ismételt naplózása|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Kétváltozós vonaldiagram|
|Naplótábla 2 numerikus oszloppal|`run.log_table(name='Sine Wave', value=sines)`|Kétváltozós vonaldiagram|


## <a name="example-notebooks"></a>Példa jegyzetfüzetekre
Az alábbi jegyzetfüzetek a jelen cikkben található fogalmakat mutatják be:
* [how-to-use-azureml/training/train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-kísérletek/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések

Próbálkozzon ezekkel a további lépésekkel a Pythonhoz készült Azure Machine Learning SDK használatának megismeréséhez:

* Tekintse meg a példát, hogyan regisztrálhatja a legjobb modellt, és üzembe helyezheti azt az oktatóanyagban, [betanítása egy lemezkép-besorolási modell az Azure Machine Learning.](tutorial-train-models-with-aml.md)

* Ismerje meg, hogyan [taníthat be PyTorch modelleket az Azure Machine Learning segítségével.](how-to-train-pytorch.md)
