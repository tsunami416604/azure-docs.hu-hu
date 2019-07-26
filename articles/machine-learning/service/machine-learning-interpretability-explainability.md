---
title: Modell értelmezhetősége
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogy a modell miért teszi a jóslatokat az Azure Machine Learning SDK használatával. A képzés során felhasználható, hogy megtudja, hogyan teszi lehetővé a modell előrejelzéseit.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 06/21/2019
ms.openlocfilehash: 1e742c278b9356c7501964541802e0c96dc74b09
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358650"
---
# <a name="model-interpretability-with-azure-machine-learning-service"></a>A modell értelmezése Azure Machine Learning szolgáltatással

Ebből a cikkből megtudhatja, hogy a modell miért hozta létre az előrejelzéseket a Azure Machine Learning Python SDK különböző értelmező csomagjaival.

Az SDK osztályok és metódusok használatával a következőket érheti el:
+ A szolgáltatás fontossági értékei mind a nyers, mind a mérnöki funkciók esetében
+ Valós idejű adatkészletek értelmezése nagy léptékben, a képzés és a következtetések során.
+ Interaktív vizualizációk, amelyek segítséget nyújtanak az adatmintázatok felderítésében, valamint a betanítás időpontjában.

A fejlesztési ciklus betanítási fázisában a modell-tervezők és-értékelők a modell értelmező kimenetét használhatják a hipotézisek ellenőrzéséhez és az érdekelt felekkel való bizalom kiépítéséhez.  Emellett a modellen alapuló elemzéseket is használják a hibakereséshez, a modell viselkedésének érvényesítéséhez, és a torzítás ellenőrzéséhez.

A gépi tanulásban a **funkciók** a célként megadott adatpontok előrejelzésére szolgáló adatmezők. Például a hitelkockázat előrejelzéséhez az életkor, a fiók mérete és a fiók kora adatmezőket lehet használni. Ebben az esetben a kor, a fiók mérete és a fiók kora **funkciók**. A szolgáltatás fontossága azt mutatja be, hogy az egyes adatmezők hogyan érintik a modell előrejelzéseit. Előfordulhat például, hogy az életkor nagy mértékben használatban van az előrejelzésben, míg a fiók mérete és kora nem befolyásolja az előrejelzés pontosságát. Ez a folyamat lehetővé teszi, hogy az adatszakértők elmagyarázzák az eredményül kapott előrejelzéseket, így az érintettek a modellben a legfontosabb adatpontok betekintést nyerhetnek.

Ezeknek az eszközöknek a használatával a gépi tanulási modelleket globálisan az **összes adattal**megmagyarázhatja, vagy **egy adott adatponton helyileg** , a legkorszerűbb technológiák használatával könnyen használható és méretezhető módon.

Az értelmező osztályok több SDK-csomagon keresztül érhetők el. Ismerje meg, hogyan [TELEPÍTHET SDK-csomagokat Azure Machine Learninghoz](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [`azureml.explain.model`](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py)a fő csomag, amely a Microsoft által támogatott funkciókat tartalmazza.

* `azureml.contrib.explain.model`, előzetes verzió, és kísérleti funkciók, amelyeket kipróbálhat.

* `azureml.train.automl.automlexplainer`csomag az automatizált gépi tanulási modellek értelmezéséhez.

> [!IMPORTANT]
> A `contrib` névtérben lévő tartalom nem teljes mértékben támogatott. Mivel a kísérleti funkciók megérettek, a rendszer fokozatosan a fő névtérbe helyezi át őket.

## <a name="how-to-interpret-your-model"></a>A modell értelmezése

Az értelmező osztályok és módszerek alkalmazásával megismerheti a modell globális viselkedését vagy az adott előrejelzéseket. Az előző neve globális magyarázat, és az utóbbi neve helyi magyarázat.

A metódusok kategorizálva is megadhatók aszerint, hogy a módszer a modell agnosztikus vagy modell-specifikus. Egyes metódusok bizonyos típusú modelleket céloznak meg. Például a SHAP 's Tree magyarázóer csak a faalapú modellekre vonatkozik. Egyes módszerek fekete dobozként kezelik a modellt, például a magyarázatot vagy a SHAP kernel-magyarázatát. A `explain` csomag az adatkészletek, a modellek típusai és a használati esetek alapján kihasználja ezeket a különböző módszereket.

A kimenet azon információk halmaza, amelyekkel az adott modell előrejelzését végezheti el, például:
* Globális/helyi relatív funkció fontossága

* Globális/helyi szolgáltatás és előrejelzési kapcsolat

### <a name="explainers"></a>Magyarázatok

Az elmagyarázók két csoportja létezik: A közvetlen magyarázatok és meta-magyarázatok az SDK-ban.

A __közvetlen magyarázatok__ az integrált könyvtárakból származnak. Az SDK becsomagolja az összes magyarázatot, így azok közös API-és kimeneti formátumot tesznek elérhetővé. Ha Ön sokkal kényelmesebben használja ezeket a magyarázatokat, akkor a közös API-k és a kimeneti formátum használata helyett közvetlenül meghívhatja őket. Az alábbi lista az SDK-ban elérhető közvetlen magyarázatokat tartalmazza:

* **Alakítási fa magyarázata**: A SHAP 's Tree deformálója, amely a fák és a fák különböző részeire jellemző, a többhelyes idő gyors FORMÁLÁSára szolgáló algoritmusra koncentrál.
* **Mélyreható magyarázat**: A SHAPer magyarázata alapján a Deep deformáló "egy nagy sebességű közelítési algoritmus az értékek alakításához a Deep learning-modellekben, amely egy, a SHAP-beli DeepLIFT-ben leírt módon létesített kapcsolatban. A TensorFlow modellek és kerasz modellek támogatottak a TensorFlow-háttér használatával (a PyTorch előzetes támogatása is elérhető).
* **SHAP kernel-magyarázat**: A SHAP kernel-magyarázata egy speciálisan súlyozott, helyi lineáris regressziót használ az egyes modellek FORMÁLÁSi értékeinek becsléséhez.
* Elmagyarázó: A magyarázó elmagyarázó a globális helyettesítő modellek ötlete alapján történik. A globális helyettesítő modell egy belsőleg értelmezhető modell, amely úgy van kiképezve, hogy a lehető legpontosabban közelítse meg a fekete doboz modelljeinek előrejelzéseit. Az adattudós értelmezheti a helyettesítő modellt a fekete Box-modellel kapcsolatos következtetések összeállításához. A következő értelmezhető modellek egyike használható helyettesítő modellként: LightGBM (LinearExplainableModel), lineáris regresszió (LinearExplainableModel), sztochasztikus gradiens deillat megmagyarázható modell (SGDExplainableModel) és döntési fa (DecisionTreeExplainableModel).


* A **permutáció funkció fontossági magyarázata**: A permutáció funkció fontossága egy olyan módszer, amely a [Breiman véletlenszerű erdőkkel](https://www.stat.berkeley.edu/%7Ebreiman/randomforest2001.pdf) kapcsolatos tanulmányai által ihletett besorolási és regressziós modellek magyarázatára szolgál (lásd: 10. szakasz). Magas szinten a működésének módja az, hogy a teljes adatkészlet esetében véletlenszerűen végzi el az adatok egy funkciójának a kiszámítását, és kiszámítja, hogy mekkora a kamat teljesítményének mérőszáma. Minél nagyobb a változás, annál fontosabb a funkció.

* **Lime-magyarázat** (`contrib`): A lime-alapú, LIME-elmagyarázó a helyi helyettesítő modellek létrehozásához használja a modern helyi, értelmezhető modell-(LIME-) algoritmust. A globális helyettesítő modellektől eltérően a LIME a helyi helyettesítő modellek betanítására összpontosít az egyes előrejelzések elmagyarázása érdekében.
* **Han Text-magyarázat** (`contrib`): A HAN Text demagyarázó egy hierarchikus hanghálózatot használ a modell magyarázatának beszerzéséhez egy adott fekete Box Text-modell szöveges adatainak használatával. Betanítjuk a HAN helyettes modelljét egy adott tanári modell előre jelzett kimenetén. Miután globálisan betanított a Text corpusba, a magyarázatok pontosságának javítása érdekében egy adott dokumentumhoz is kibővítettük a megfelelő lépéseket. A HAN kétirányú RNN használ két figyelmet a mondatok és a szó figyelemmel. Ha a DNN betanítják a tanári modellre, és egy adott dokumentumra finomítják őket, kinyerheti a szó fontosságát a figyelmet tartalmazó rétegekből. A HAN-t úgy találtuk, hogy pontosabbak legyenek, mint az olyan MÉSZek, mint a szöveges adatmennyiség, de költségesebb a képzési idő szempontjából is. Azonban javítottuk a betanítási időt azáltal, hogy a felhasználó számára lehetőséget biztosít a hálózat és a kesztyűs Word-beágyazások inicializálására, bár ez még mindig lassú. A betanítási idő jelentősen növelhető a HAN távoli Azure GPU virtuális gépen való futtatásával. A HAN megvalósítását "hierarchikus figyelmet igénylő hálózatok a dokumentumok besorolásához (Yang et al., 2016)" ([https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf)) című cikk írja le.


A __meta__ -magyarázatok automatikusan kiválasztják a megfelelő közvetlen magyarázatot, és az adott modell és adatkészletek alapján létrehozzák a legjobb magyarázati információkat. A meta-magyarázatok kihasználják az általunk integrált vagy fejlesztett kódtárakat (SHAP, LIME, utánozza stb.). Az SDK-ban a következő meta-magyarázatok érhetők el:

* **Táblázatos magyarázat**: Táblázatos adatkészletekhez használatos.
* **Szöveg magyarázata**: Szöveges adatkészletekhez használatos.
* **Képmagyarázat**: Képkészletekhez használatos.

A közvetlen magyarázatok meta kiválasztásán felül a meta-magyarázatok további funkciókat fejlesztenek a mögöttes könyvtárakon, és javítják a közvetlen magyarázatok sebességét és méretezhetőségét.

Jelenleg `TabularExplainer` a következő logikát alkalmazza a Direct SHAP-magyarázatok meghívásához:

1. Ha ez egy faalapú modell, alkalmazza a SHAP `TreeExplainer`, különben
2. Ha ez egy DNN modell, alkalmazza a SHAP `DeepExplainer`, különben
3. Kezelés fekete dobozos modellként és a SHAP alkalmazása`KernelExplainer`

A beépített `TabularExplainer` intelligencia egyre kifinomultabb lesz, mivel egyre több kódtár van integrálva az SDK-ba, és megismerheti az egyes magyarázatok előnyeit és hátrányait.

`TabularExplainer`jelentős funkciókkal és teljesítménnyel kapcsolatos fejlesztéseket hajtott végre a közvetlen magyarázatok során:

* **Az inicializálási adatkészlet**összefoglalása. Azokban az esetekben, amikor a magyarázat sebessége a legfontosabb, összefoglaljuk az inicializálási adatkészletet, és létrehozunk egy kis reprezentatív mintát, amely felgyorsítja a globális és a helyi magyarázatot is.
* **A**kiértékelési adatkészlet mintavételezése. Ha a felhasználó a kiértékelési minták nagy készletét adja vissza, de valójában nem szükséges mindegyiket kiértékelni, a mintavételi paraméter értéke TRUE (igaz) lehet, hogy felgyorsítsa a globális magyarázatot.

A következő ábra a közvetlen és a meta-magyarázatok aktuális szerkezetét mutatja be.

[![Machine Learning-értelmező architektúra](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>Támogatott modellek

`numpy.array`A Python `pandas.DataFrame`,, vagy`scipy.sparse.csr_matrix`Format adatkészleteken betanított modelleket az SDK értelmező csomagjatámogatja.`explain` `iml.datatypes.DenseData`

A magyarázó függvények bemenetként is elfogadják a modelleket és a folyamatokat. Ha meg van adni modell, a modellnek meg kell valósítania `predict` az `predict_proba` előrejelzési függvényt, vagy a Scikit egyezménynek megfelelően kell megfelelnie. Ha a folyamat (a folyamat parancsfájljának neve) van megadva, a magyarázat függvény azt feltételezi, hogy a futó folyamat parancsfájlja egy előrejelzést ad vissza. Támogatjuk a PyTorch, a TensorFlow és a kerasz Deep learning-keretrendszereken keresztül betanított modelleket.

### <a name="local-and-remote-compute-target"></a>Helyi és távoli számítási cél

A `explain` csomag úgy van kialakítva, hogy a helyi és távoli számítási célokkal is működjön. Ha helyileg fut, az SDK-függvények nem fognak kapcsolatba lépni az Azure-szolgáltatásokkal. A magyarázatot távolról is futtathatja Azure Machine Learning számítási feladatait, és naplózhatja a magyarázat adatait Azure Machine Learning futtatási előzmények szolgáltatásba. Az információk naplózása után a magyarázatokból származó jelentések és vizualizációk azonnal elérhetők Azure Machine Learning-munkaterület portálon felhasználói elemzés céljából.

## <a name="interpretability-in-training"></a>A képzés értelmezése

### <a name="train-and-explain-locally"></a>Helyi betanítás és magyarázat

1. A modell betanítása egy helyi Jupyter jegyzetfüzetbe.

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    
    # split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data,            
                                                        breast_cancer_data.target,  
                                                        test_size=0.2,
                                                        random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. A magyarázat meghívása: A magyarázó objektumok inicializálásához át kell adnia a modelljét és a betanítási adatait az elmagyarázó konstruktorának. Igény szerint átadhatja a szolgáltatások nevét és a kimeneti osztály nevét (ha ez a besorolás), amelyet a rendszer a magyarázatok és a vizualizációk további tájékoztatása érdekében használ. Az alábbiakban bemutatjuk, hogyan hozható létre egy magyarázó objektum a [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py), a [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py)és a [PFIExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.permutation.permutation_importance.pfiexplainer?view=azure-ml-py) helyileg. `TabularExplainer`a a három SHAP magyarázat egyikét hívja meg (`TreeExplainer`, `DeepExplainer`, vagy `KernelExplainer`), és automatikusan kiválasztja a legmegfelelőbbet a használati esethez. A három mögöttes magyarázatot azonban közvetlenül is meghívhatja.

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    vagy

    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
    from azureml.explain.model.mimic.models.linear_model import LinearExplainableModel
    from azureml.explain.model.mimic.models.linear_model import SGDExplainableModel
    from azureml.explain.model.mimic.models.tree_model import DecisionTreeExplainableModel

    # "features" and "classes" fields are optional
    # augment_data is optional and if true, oversamples the initialization examples to improve surrogate model accuracy to fit original model.  Useful for high-dimensional data where the number of rows is less than the number of columns. 
    # max_num_of_augmentations is optional and defines max number of times we can increase the input data size.
    # LGBMExplainableModel can be replaced with LinearExplainableModel, SGDExplainableModel, or DecisionTreeExplainableModel
    explainer = MimicExplainer(model, 
                               x_train, 
                               LGBMExplainableModel, 
                               augment_data=True, 
                               max_num_of_augmentations=10, 
                               features=breast_cancer_data.feature_names, 
                               classes=classes)
    ```
   vagy

    ```python
    from azureml.explain.model.permutation.permutation_importance import PFIExplainer 
    
    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model, 
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

3. A globális funkciók fontossági értékeinek beolvasása.

    ```python
    # you can use the training data or the test data here
    global_explanation = explainer.explain_global(x_train)
    
    # if you used the PFIExplainer in the previous step, use the next line of code instead
    # global_explanation = explainer.explain_global(x_train, true_labels=y_test)

    # sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))

    # alternatively, you can print out a dictionary that holds the top K feature names and values
    global_explanation.get_feature_importance_dict()
    ```

4. A helyi funkció fontossági értékeinek beolvasása: az alábbi függvény hívásával ismertesse az egyes példányokat vagy a példányok egy csoportját. Vegye figyelembe, hogy a PFIExplainer nem támogatja a helyi magyarázatokat.

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

    vagy

    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

### <a name="train-and-explain-remotely"></a>Betanítás és magyarázat távolról

Habár a Azure Machine Learning szolgáltatás által támogatott különféle számítási célok betanítására van lehetőség, az ebben a szakaszban ismertetett példa azt szemlélteti, hogyan végezheti el ezt egy Azure Machine Learning számítási cél használatával.

1. Hozzon létre egy betanítási szkriptet egy helyi Jupyter-jegyzetfüzetben (például run_explainer.).

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run

    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # write code to get and split your data into train and test sets here
    # write code to train your model here 

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                x_train, 
                                features=feature_names, 
                                classes=classes)

    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    
    # uploading global model explanation data for storage or visualization in webUX
    # the explanation can then be downloaded on any compute
    # multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. A számítási [célok beállítása a modell](how-to-set-up-training-targets.md#amlcompute) betanításához című témakör útmutatását követve megtudhatja, hogyan állíthat be Azure Machine learning számítási célt, és hogyan küldhet betanítási kísérleteket.

3. Töltse le a magyarázatot a helyi Jupyter notebookon.

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    
    client = ExplanationClient.from_run(run)
    
    # get model explanation data
    explanation = client.download_model_explanation()
    # or only get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="visualizations"></a>Vizualizációk

A vizualizációs irányítópult használatával megismerheti és értelmezheti a modelljét:

### <a name="global-visualizations"></a>Globális vizualizációk

A következő mintaterületek globális képet nyújtanak a betanított modellről, valamint annak előrejelzéseit és magyarázatait.

|Diagram|Leírás|
|----|-----------|
|Adatelemzés| Az adatkészlet áttekintése az előrejelzési értékekkel együtt.|
|Globális fontosság|Megjeleníti a legfontosabb szolgáltatásokat globálisan (konfigurálható K). Ez a diagram hasznos lehet az alapul szolgáló modell globális viselkedésének megismeréséhez.|
|Magyarázat feltárása|Azt mutatja be, hogyan felelős a szolgáltatás a modell előrejelzési értékeinek (vagy az előrejelzési értékek valószínűségének) megváltoztatásához. |
|Összegzés| Az az összes adatpontnál az aláírt helyi szolgáltatás fontossági értékeit használja az egyes szolgáltatások által az előrejelzési értékre gyakorolt hatás eloszlásának megjelenítéséhez.|

[![Vizualizációs irányítópult – globális](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Helyi vizualizációk

Az előző mintaterületeken bármikor rákattinthat az egyes adatpontokra, hogy betöltse a helyi funkció fontossági területét az adott adatponthoz.

|Diagram|Leírás|
|----|-----------|
|Helyi fontosság|Megjeleníti a legfontosabb szolgáltatásokat globálisan (konfigurálható K). Ez a diagram hasznos lehet az alapul szolgáló modell helyi viselkedésének megismerésére egy adott adatponton.|
|Perturbáció feltárása|Lehetővé teszi a kijelölt adatponthoz tartozó szolgáltatások értékének módosítását, és megfigyelheti, hogy ezek a változások milyen hatással lesznek az előrejelzési értékre.|
|Egyéni feltételes várakozás (ICE)| Lehetővé teszi egy szolgáltatás értékének a minimális értékről a maximális értékre való módosítását, amely azt mutatja, hogy az adatpontok előrejelzése hogyan változik meg egy szolgáltatás változásakor.|

[![Vizualizációs irányítópult helyi funkciójának fontossága](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)


[![Vizualizációs irányítópult funkció perturbáció](./media/machine-learning-interpretability-explainability/perturbation.gif)](./media/machine-learning-interpretability-explainability/perturbation.gif#lightbox)


[![Vizualizációs irányítópult jég ábrázolása](./media/machine-learning-interpretability-explainability/ice-plot.png)](./media/machine-learning-interpretability-explainability/ice-plot.png#lightbox)

Vegye figyelembe, hogy a Jupyter-kernel elindítása előtt engedélyeznie kell a vizualizációs irányítópult widget-bővítményeit.

* Jupyter-notebookok

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.explain.model.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.explain.model.visualize
    ```



* Jupyter Labs

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```
A vizualizációs irányítópult betöltéséhez használja a következő kódot:

```python
from azureml.contrib.explain.model.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

## <a name="raw-feature-transformations"></a>Nyers funkciók átalakítása

Lehetőség van arra is, hogy átadja a funkció átalakítási folyamatát a magyarázatnak, hogy a nyers funkciókra vonatkozó magyarázatokat kapjon az átalakítás előtt (a megtervezett funkciók helyett). Ha kihagyja ezt a lehetőséget, a magyarázat a megfogalmazott funkciókra vonatkozó magyarázatokat tartalmaz.

A támogatott átalakítások formátuma ugyanaz, mint az [sklearn-pandák](https://github.com/scikit-learn-contrib/sklearn-pandas)című rész. Általánosságban elmondható, hogy az átalakítások csak akkor támogatottak, ha egyetlen oszlopon működnek, és így egyértelműen egy-sok. 

A nyers funkciókat az a `sklearn.compose.ColumnTransformer` vagy a felszerelt transzformátor-rekordok listájának használatával tudjuk megmagyarázni. Az alábbi cella a `sklearn.compose.ColumnTransformer`következőt használja:. 

```python
from sklearn.compose import ColumnTransformer

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])

# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

Ha szeretné futtatni a példát a beszerelt transzformátor-rekordok listájával, használja a következő kódot: 
```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(
    strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(
    handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# append model to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                      ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=transformations)
```

## <a name="interpretability-at-inferencing-time"></a>Értelmezhető a következtetések idején

A magyarázat az eredeti modellel együtt helyezhető üzembe, és pontozással is használható a helyi magyarázó információk megadásához. A könnyebb súlyú pontozási magyarázatokat is kínáljuk, hogy az értelmezési idő nagyobb teljesítményű legyen. A könnyebb súlyozású pontozási elmagyarázó üzembe helyezésének folyamata hasonló a modellek üzembe helyezéséhez, és a következő lépéseket tartalmazza:




1. Hozzon létre egy magyarázat objektumot (például a TabularExplainer használatával):

   ```python
   from azureml.contrib.explain.model.tabular_explainer import TabularExplainer

   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Pontozási magyarázat létrehozása a magyarázat objektum használatával:

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Egy pontozási bemutató modellt használó rendszerkép konfigurálása és regisztrálása.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. Választható A pontozási magyarázat beolvasása a felhőből és az indoklások tesztelése

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. A rendszerkép üzembe helyezése számítási célra:

   1. Hozzon létre egy pontozási fájlt (ehhez a lépéshez kövesse a [modellek üzembe helyezése a Azure Machine learning szolgáltatással](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where) című témakör lépéseit az eredeti előrejelzési modell regisztrálásához)

        ```python
        %%writefile score.py
        import json
        import numpy as np
        import pandas as pd
        import os
        import pickle
        from sklearn.externals import joblib
        from sklearn.linear_model import LogisticRegression
        from azureml.core.model import Model

        def init():

            global original_model
            global scoring_model

            # retrieve the path to the model file using the model name
            # assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('my_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

        def run(raw_data):
            # get predictions and explanations for each data point
            data = pd.read_json(raw_data)
            # make prediction
            predictions = original_model.predict(data)
            # retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # you can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
        ```

   1. Adja meg a központi telepítési konfigurációt (ez a konfiguráció a modell követelményeitől függ. Az alábbi példa olyan konfigurációt határoz meg, amely egy CPU-mag és 1 GB memóriát használ.

        ```python
        from azureml.core.webservice import AciWebservice

        aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                       memory_gb=1,
                                                       tags={"data": "NAME_OF_THE_DATASET",
                                                             "method" : "local_explanation"},
                                                       description='Get local explanations for NAME_OF_THE_PROBLEM')
        ```

   1. Környezeti függőségekkel rendelkező fájl létrehozása

        ```python
        from azureml.core.conda_dependencies import CondaDependencies

        # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

        azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-explain-model', 'azureml-core', 'azureml-telemetry', 'azureml-explain-model']
 

        # specify CondaDependencies obj
        myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                         pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                         pin_sdk_version=False)


        with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

        with open("myenv.yml","r") as f:
            print(f.read())
        ```

   1. Hozzon létre egy egyéni Docker, amelynek a g + + telepítve van

        ```python
        %%writefile dockerfile
        RUN apt-get update && apt-get install -y g++
        ```

   1. A létrehozott rendszerkép üzembe helyezése (becsült idő: 5 perc)

        ```python
        from azureml.core.webservice import Webservice
        from azureml.core.image import ContainerImage

        # use the custom scoring, docker, and conda files we created above
        image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                        docker_file="dockerfile",
                                                        runtime="python",
                                                        conda_file="myenv.yml")

        # use configs and models generated above
        service = Webservice.deploy_from_model(workspace=ws,
                                            name='model-scoring-service',
                                            deployment_config=aciconfig,
                                            models=[scoring_explainer_model, original_model],
                                            image_config=image_config)

        service.wait_for_deployment(show_output=True)
        ```

1. Az üzemelő példány tesztelése

    ```python
    import requests

    # create data to test service with
    examples = x_list[:4]
    input_data = examples.to_json()

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. Karbantartás: Az üzembe helyezett webszolgáltatáshoz törölheti `service.delete()`.




## <a name="next-steps"></a>További lépések

A fenti utasításokat bemutató Jupyter jegyzetfüzetek gyűjteményének megjelenítéséhez tekintse meg a [Azure Machine learning értelmező minta jegyzetfüzeteket](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
