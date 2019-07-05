---
title: Modell értelmezhetősége
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan annak magyarázata, hogy miért a modell lehetővé teszi az Azure Machine Learning SDK használatával készíthet előrejelzéseket. Használat során a tanuláshoz és következtetésekhez tudni, hogy a modell előrejelzéseket tesz.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 06/21/2019
ms.openlocfilehash: cba46a277dfce93d0080d8f04a26fd135407de15
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536738"
---
# <a name="model-interpretability-with-azure-machine-learning-service"></a>Modell-e az Azure Machine Learning szolgáltatással

Ebből a cikkből megismerheti, hogyan annak magyarázata, hogy miért a modell készült-e az előrejelzés az Azure Machine Learning Python SDK különböző e csomagokkal zavartalanul.

Az osztályok és módszerek használata az SDK-ban, kaphat:
+ A szolgáltatás nyers és a visszafejtett funkciók fontossági értékek
+ E nagy mennyiségű, való életből vett adatkészletek során tanuláshoz és következtetésekhez.
+ Interaktív Vizualizációk, melyek a segítségére lehetnek a data- és magyarázatok minták felderítését a képzési időpontban

A fejlesztési ciklus képzési fázisban modell tervezők és értékelők segítségével a modell e kimeneti feltételezéseket ellenőrzése és az érintettekkel való megbízhatósági kapcsolat létrehozása.  Hibakeresési is felhasználják az elemzéseket a modellbe, eszközmodell viselkedésének érvényesítése megegyezik a célokat, és az eltérés kereséséhez.

A machine learninghez **funkciók** is képes előre jelezni egy cél adatpont adatmezőket. Például a hitelkockázatot, kor, a fiók és a fiók életkor datová Pole használhatók. Ebben az esetben életkor, a fiók és a fiók élettartama a **funkciók**. A szolgáltatás fontosság kiderül, hogy hogyan minden adatmező érintett-e a modell-előrejelzéseket. Például kora erősen használhatók az előrejelzéshez fiók és a korszűrő nem befolyásolják az előrejelzési pontosság jelentősen közben. Ez a folyamat lehetővé teszi, hogy az adatszakértők, az eredményül kapott előrejelzéseket ismertetik, hogy az érdekelt felek, hogy mely adatpontok közel a modell legfontosabb.

Ezek az eszközök akkor is azt ismerteti, machine learning-modellek **globálisan az összes adat**, vagy **helyileg, egy adott adatpontot** a legmodernebb technológiák használatával egy könnyen használható és méretezhető módon.

Az e osztályok több SDK-csomagok érhetők el. Ismerje meg, hogyan [SDK-csomagok telepítése az Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [`azureml.explain.model`](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py), a fő csomag, amely tartalmazza a Microsoft által támogatott funkciók.

* `azureml.contrib.explain.model`, előzetes verzió, és kísérleti funkciók kipróbálható.

* `azureml.train.automl.automlexplainer` csomag automatikus gépi tanulási modelleket értelmezéséhez.

> [!IMPORTANT]
> A tartalom a `contrib` névtér nem teljes mértékben támogatott. A kísérleti funkciók érett lenni, mivel azokat a fő névtér fokozatosan kerül.

## <a name="how-to-interpret-your-model"></a>A modell értelmezése

Az e osztályok és módszerek megértése a modell globális viselkedés vagy adott előrejelzéseket alkalmazhat. Az előbbi neve globális magyarázat, és ez utóbbi nevezzük helyi magyarázata.

A módszerek is csoportosíthatók az módszer e modell független vagy adott modell alapján. Egyes metódusok a céloznia modellek bizonyos típusú. Ha például Alakzatadatok a fa ismertető csak érvényes fa-alapú modell. Egyes metódusok kezeli a modell egy fekete mezőt, például mimic ismertető vagy Alakzatadatok a kernel ismertető. A `explain` csomagot használja ezen különböző megközelítések adatkészletek adatmodell-típusokat, és a használati esetek alapján.

A kimenet a hogyan egy adott modell lehetővé teszi annak előrejelzése, mint például az adatok:
* Globális vagy helyi relatív funkció fontosság

* Globális vagy helyi szolgáltatás és az előrejelzési kapcsolat

### <a name="explainers"></a>Explainers

Nincsenek explainers két készletnyi: Közvetlen Explainers és Meta Explainers az SDK-ban.

__A közvetlen explainers__ integrált kódtárak származnak. Az SDK összes explainers burkolja, így egy közös API-t és a kimeneti formátum szolgáltatnak. Ha járatos több közvetlenül ezek explainers, közvetlenül hívhatók meg azokat a közös API-t és a kimeneti formátum használata helyett. Az SDK-ban elérhető közvetlen explainers listáját a következők:

* **SHAP Tree Explainer**: Az Alakzatadatok a fa ismertető, amelynek fő témája a polinom time gyors Alakzatadatok érték költségbecslési algoritmus adott fák és együttesek fák.
* **Részletes ismertető Alakzatadatok**: Alapján a magyarázat az Alakzatadatok, részletes ismertető "egy nagy sebességű approximációs algoritmus, deep learning-modellek az Alakzatadatok értékek, amelyek az Alakzatadatok NIPS könyvben leírt DeepLIFT épül, amely kapcsolatot is. TensorFlow-modellek és a TensorFlow-háttérrendszer használatával Keras-modellek támogatottak (nincs PyTorch előzetes támogatása) ".
* **SHAP Kernel Explainer**: Alakzatadatok a Kernel ismertető kifejezetten súlyozott helyi lineáris regresszió segítségével bármely modellhez tartozó Alakzatadatok értékeket használja.
* **Ismertető utánzására**: Mimic ismertető az elgondolásra épül, globális helyettes modellek alapul. Egy globális helyettes modell egy belsőleg interpretable modell, amely a lehető legpontosabban méretkorlátjai az előrejelzés egy fekete mezőt modell tanítása. Adattudós tudja értelmezni a helyettes modell következtetéseket a fekete dobozhoz modellel kapcsolatos. A helyettes modell, az alábbi interpretable minták egyikét használhatja: LightGBM (LinearExplainableModel), Linear Regression (LinearExplainableModel), Stochastic Gradient Descent explainable model (SGDExplainableModel), and Decision Tree (DecisionTreeExplainableModel).


* **Egy-egy alkalommal a szolgáltatás fontosságát ismertető**: Egy-egy alkalommal a funkció fontos a feltüntetni azokat a besorolási és regressziós modellek, amelyek merítsen inspirációt a technika [Breiman a véletlenszerű erdők tanulmány](https://www.stat.berkeley.edu/%7Ebreiman/randomforest2001.pdf) (lásd a szakasz 10). Magas szintű működési a véletlenszerűen újbóli felosztás adatok egy szolgáltatás a teljes adatkészlet egyszerre, és mekkora a teljesítmény fontos mérőszám értéke csökkenti kiszámítása. Minél nagyobb a módosítást, a fontosabb, hogy a szolgáltatás el.

* **SÁRGÁSZÖLD ismertető** (`contrib`): SÁRGÁSZÖLD alapozva SÁRGÁSZÖLD ismertető a állapota-a-a legújabb helyi interpretable modell-agnosztikus magyarázatokat (SÁRGÁSZÖLD) algoritmust használja helyi helyettes modellek létrehozásának eljárásait. SÁRGÁSZÖLD ellentétben a globális helyettes modellek összpontosít az egyes előrejelzéseket elmagyarázni helyi helyettes modellek betanítása.
* **HAN szöveg ismertető** (`contrib`): HAN szöveg ismertető szöveges adatok egy adott fekete dobozhoz szöveg modell modell magyarázatok lekérése egy hierarchikus figyelmet hálózatot használ. Az egy adott oktatói modell előre jelzett kimenetek HAN helyettes modell betanításához azt. Globálisan a szöveg forrásgyűjteményébe között képzést, miután egy fine-tune lépést, az adott dokumentum hozzáadtuk a magyarázatokat pontosságának javítása érdekében. HAN egy kétirányú RNN két figyelmet rétegeket, használja a mondat és a word figyelmet. A DNN tanított az oktatói modell, és a egy adott dokumentum finomította, hogy bontsa ki a word importances a figyelmet rétegek. HAN pontosabb, mint SÁRGÁSZÖLD vagy Alakzatadatok szöveges adatok esetében, de költségesebb, a használati idő, valamint képzési is található. Azonban hajtottunk végre a betanítási idő fejlesztései, így a felhasználó kesztyűtartó szóbeágyazásokból, a hálózati inicializálása arra, bár ez továbbra is lassú. A betanítási idő jelentősen javítható, ha egy távoli Azure-GPU virtuális gépen futó HAN. HAN végrehajtásának leírt "Hierarchikus figyelmet hálózatok dokumentum besorolási (Yang et al., 2016)" ([https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf)).


__Meta explainers__ automatikusan válassza ki a megfelelő közvetlen ismertető és az ajánlott magyarázata adatok alapján az adott modell és az adatkészletek létrehozása. A metaadatok explainers összes könyvtár (Alakzatadatok, SÁRGÁSZÖLD, előnézet, stb.) a Microsoft integrált vagy fejlesztett használhatja. Az SDK-ban elérhető meta explainers a következők:

* **Táblázatos ismertető**: A táblázatos adatkészleteket használja.
* **Szöveg ismertető**: A szöveg adatkészleteket használja.
* **Kép ismertető**: A lemezkép-adatkészleteket használja.

Emellett a meta lehetőségre kattint, a közvetlen explainers, meta explainers felett az alapul szolgáló kódtárak további funkciók fejlesztése és növelheti a sebességet és méretezhetőséget biztosít a közvetlen explainers keresztül.

Jelenleg `TabularExplainer` alkalmaz az alábbi logika, a közvetlen Alakzatadatok Explainers meghívásához:

1. Ha egy fa-alapú modell, a alkalmazni Alakzatadatok `TreeExplainer`, más
2. Ha egy DNN-modell, a alkalmazni Alakzatadatok `DeepExplainer`, más
3. Fekete beépített modellként kezelje azt, és a alkalmazni az Alakzatadatok `KernelExplainer`

A beépített intelligencia `TabularExplainer` kifinomultabb fog válni, további kódtárak pedig az SDK integrálva vannak, és hogy ismertetése és az egyes ismertető hátrányai.

`TabularExplainer` a közvetlen Explainers keresztül is rendelkezik végzett jelentős funkciót és a teljesítmény fejlesztései:

* **Az inicializálás adatkészlet összefoglaló**. Olyan esetekben, ahol magyarázata sebességétől legfontosabb hogy összesíteni az inicializálási adatkészlet és létrehozása egy kis készletét reprezentatív mintát, amely felgyorsítja a globális és a helyi magyarázata.
* **Az értékelés adatkészlet mintavételi**. Ha a felhasználó átadja az értékelés minták nagy készletét, de nem ténylegesen szükséges összes ki kell értékelni őket, a mintavételi paraméterrel állítható az igaz értékre a globális magyarázat felgyorsítása érdekében.

Az alábbi ábrán látható, a közvetlen aktuális szerkezetét és meta explainers.

[![Machine Learning-e architektúra](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>Támogatott modellek

Nagy méretű adatkészletek a Pythonban képzett modellekre `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData`, vagy `scipy.sparse.csr_matrix` formátumot az e által támogatott `explain` SDK csomagot.

A magyarázat functions fogadja el a modellek és a folyamatok bemenetként. A modell áll rendelkezésre, ha a modell meg kell valósítania az előrejelző függvényben `predict` vagy `predict_proba` , amely megfelel a Scikit egyezmény. Egy folyamat (a csővezeték-parancsfájl nevét) áll rendelkezésre, ha a magyarázat függvény azt feltételezi, hogy a futó folyamat parancsfájl előrejelzést. PyTorch, a tensorflow-hoz és a deep learning-keretrendszerek Keras-n keresztül betanított modellek is nyújtunk támogatást.

### <a name="local-and-remote-compute-target"></a>Helyi és távoli számítási célnak

A `explain` csomag az célja, hogy mindkét helyi és távoli számítási célnak dolgozhat. Ha helyileg futtatja, az SDK-függvények nem kapcsolatba fog lépni bármely Azure-szolgáltatást. MAGYARÁZAT távolról futtatni az Azure Machine Learning COMPUTE számítási, és a magyarázatot info jelentkezzen be az Azure Machine Learning előzmények szolgáltatások futtatása. Ezeket az adatokat a rendszer naplózza, ha jelentéseket és vizualizációkat a magyarázat azonnal elérhetők az Azure Machine Learning-munkaterület-portál felhasználói elemzés céljából.

## <a name="interpretability-in-training"></a>E képzési

### <a name="train-and-explain-locally"></a>Betanítása és helyileg magyarázata

1. A helyi Jupyter notebook modellje betanításához.

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

2. Hívja meg a ismertető: Egy ismertető objektum inicializálása, szüksége a modell és a egy betanítási adatok átadása az ismertető a konstruktor. Akkor is lehetősége van átadni a szolgáltatások neveit és a kimeneti osztály neve (Ha ez a besorolás), hogy a magyarázatokat és a Vizualizációk konkrétabb használandó. Így egy ismertető objektum használatával példányosítható [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py), [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py), és [PFIExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.permutation.permutation_importance.pfiexplainer?view=azure-ml-py) helyileg. `TabularExplainer` hívja a három Alakzatadatok explainers alatti egyik (`TreeExplainer`, `DeepExplainer`, vagy `KernelExplainer`), és automatikusan a legmegfelelőbbhöz az használati esetekhez jelöl. Ugyanakkor a három alapul szolgáló explainers mindegyike közvetlenül hívja.

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

3. A globális szolgáltatás fontossági értékek beolvasása.

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

4. A helyi szolgáltatás fontossági értékek beolvasása: a következő függvényhívások használata annak magyarázata, egy egyéni példány vagy példányok csoportjára is. Vegye figyelembe, hogy PFIExplainer nem támogatja a helyi találja.

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

### <a name="train-and-explain-remotely"></a>Betanítása és távolról magyarázata

Az Azure Machine Learning szolgáltatás támogatja a különböző számítási célnak betaníthatja, miközben ebben a szakaszban a példa bemutatja, hogyan teheti ezt a az Azure Machine Learning Compute cél.

1. Helyi Jupyter notebook (például run_explainer.py) hozzon létre egy tanítási szkriptet.

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

2. Kövesse az [állítsa be a modell betanítása és számítási célnak](how-to-set-up-training-targets.md#amlcompute) hogyan állíthatja be az Azure Machine Learning COMPUTE számítási, a számítási célnak, és küldje el a betanítási Futtatás.

3. Töltse le a helyi Jupyter notebook a magyarázatot.

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

A Vizualizáció Irányítópult segítségével megismerheti és értelmezni a modell:

### <a name="global-visualizations"></a>Globális Vizualizációk

A következő grafikon adja meg a globális nézet készítése a betanított modell annak előrejelzéseket és magyarázatok együtt.

|Diagram|Leírás|
|----|-----------|
|Az adatok feltárása| Az adatkészlet előrejelzési értékek együtt áttekintése.|
|Globális fontosság|A felső K (konfigurálható K) fontos funkciók globálisan mutatja. Ez a diagram hasznos annak megértéséhez, az alapul szolgáló modell globális viselkedését.|
|Explanation Exploration|Bemutatja, hogyan szolgáltatás felelős a módosítást a modell előrejelzéses értékek (vagy előrejelzési értékek valószínűségét). |
|Összefoglalás| Egy aláírt helyi szolgáltatás fontossági értékeket használja minden adatpont között a hatását, mindegyik szolgáltatás esetében az előrejelzési értéke eloszlása megjelenítéséhez.|

[![Visualization Dashboard Global](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Helyi Vizualizációk

A helyi szolgáltatás fontosság diagram a megadott adatponthoz tartozó betölteni az előző grafikon tetszőleges időpontban minden egyes adatpontra kattinthat.

|Diagram|Leírás|
|----|-----------|
|Helyi fontosság|A felső K (konfigurálható K) fontos funkciók globálisan mutatja. Ez a diagram hasznos az alapul szolgáló modell egy adott adatpontra helyi viselkedésének megértése.|
|Perturbation Exploration|Módosítsa az értékeket a kijelölt adatokat mutasson, és figyelje meg, milyen módosításokat előrejelzési érték hatással lesz a szolgáltatás lehetővé teszi.|
|Az egyes feltételes elvárás (Jégkrém)| Lehetővé teszi a funkció érték módosítása a legkisebb értéket meg, hogyan változik az adatpont előrejelzési, egy szolgáltatás megváltozásakor maximális értékre.|

[![Vizualizáció irányítópult helyi funkció fontosság](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)


[![Vizualizáció irányítópult funkció zavarok](./media/machine-learning-interpretability-explainability/perturbation.gif)](./media/machine-learning-interpretability-explainability/perturbation.gif#lightbox)


[![Vizualizáció irányítópult ICE jeleníti meg](./media/machine-learning-interpretability-explainability/ice-plot.png)](./media/machine-learning-interpretability-explainability/ice-plot.png#lightbox)

Vegye figyelembe a widget bővítmények a Vizualizáció irányítópult engedélyezve van, a Jupyter-kernel megkezdése előtt rendelkeznie kell.

* Jupyter-notebookok

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.explain.model.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.explain.model.visualize
    ```



* Jupyter-Laborgyakorlatok

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```
A Vizualizáció az irányítópult betöltése, a következő kód használatával:

```python
from azureml.contrib.explain.model.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

## <a name="raw-feature-transformations"></a>Nyers funkció átalakítások

Szükség esetén a ismertető magyarázatokat a nyers szolgáltatások tekintetében fogadásához az átalakítást (helyett visszafejtett funkciók) előtt a szolgáltatás átalakítási folyamat adhat át. Ha kihagyja ezt a beállítást, a ismertető biztosít magyarázatok visszafejtett szolgáltatások tekintetében.

A támogatott átalakításokat formátuma azonos az egyik leírtak szerint [sklearn-pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). Általánosságban véve a átalakításokat mindaddig, amíg azok a művelethez használandó egyoszlopos, ezért egyértelműen egy a többhöz támogatottak. 

Tudjuk magyarázni nyers funkciók vagy segítségével egy `sklearn.compose.ColumnTransformer` vagy illesztett átalakító rekordok listáját. A cella alábbi `sklearn.compose.ColumnTransformer`. 

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

Abban az esetben, ha szeretné futtatni a példában a illesztett átalakító rekordok listáját, használja a következő kódot: 
```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(handle_unknown='ignore', sparse=False)) for f in categorical]

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

## <a name="interpretability-at-inferencing-time"></a>E következtetési időpontban

Az ismertető és az eredeti modellt telepíthetők, és kiértékelés időpontja, adja meg a helyi magyarázata adatokat használható. Emellett pehelysúlyú pontozási explainers, hogy az idő több nagy teljesítményű, következtetési érinti. Üzembe helyezését egy pehelysúlyú pontozási ismertető hasonló helyezi üzembe a modellt, és a következő lépésekből áll:




1. Hozzon létre egy magyarázata objektumot (például használatával TabularExplainer):

   ```python
   from azureml.contrib.explain.model.tabular_explainer import TabularExplainer

   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Hozzon létre egy pontozó ismertető a magyarázat objektummal:

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Konfigurálja, és regisztrálja a pontozási ismertető modellt használó kép.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. [Opcionális] A pontozó ismertető lekérni a felhőből, és a magyarázatokat tesztelése

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

1. A-rendszerkép üzembe helyezése számítási célt:

   1. Hozzon létre egy pontozó fájlt (ebben a lépésben előtt kövesse a [helyezhet üzembe modelleket az Azure Machine Learning szolgáltatással](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where) regisztrálni az eredeti előrejelzési modell)

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

   1. Adja meg a telepítési konfigurációt (Ez a konfiguráció attól függ, a modell követelményeinek. Az alábbi példa meghatározza egy processzormaggal és 1 GB memóriát használó konfiguráció)

        ```python
        from azureml.core.webservice import AciWebservice

        aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                       memory_gb=1,
                                                       tags={"data": "NAME_OF_THE_DATASET",
                                                             "method" : "local_explanation"},
                                                       description='Get local explanations for NAME_OF_THE_PROBLEM')
        ```

   1. Hozzon létre egy fájlt a környezet függőségek

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

   1. Hozzon létre egy egyéni docker-fájlban g ++ telepítve

        ```python
        %%writefile dockerfile
        RUN apt-get update && apt-get install -y g++
        ```

   1. A létrehozott rendszerkép üzembe helyezése (becsült időtartam: 5 perc)

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

1. Karbantartása: Az üzembe helyezett webszolgáltatáshoz törölheti `service.delete()`.

## <a name="interpretability-in-automated-ml"></a>E automatizált ML-ben

Automatizált machine learning szolgáltatás fontosság automatikus betanított modellek értelmezéséhez csomagot tartalmaz. Ezenkívül besorolási forgatókönyveket teszi fontosság osztályszintű funkció lekéréséhez. Ehhez a viselkedéshez belüli automatikus machine learning két módszer áll rendelkezésre:

* Ensemble betanított modell fontosak a szolgáltatás engedélyezéséhez használja a [ `explain_model()` ](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlexplainer?view=azure-ml-py) függvény.

    ```python
    from azureml.train.automl.automlexplainer import explain_model

    shap_values, expected_values, overall_summary, overall_imp, \
        per_class_summary, per_class_imp = explain_model(fitted_model, X_train, X_test)
    ```

* Ahhoz, hogy minden egyes feladatfuttatáshoz képzési előtt a szolgáltatás fontosság, állítsa be a `model_explainability` paramétert `True` a a `AutoMLConfig` objektum érvényesítési adatok biztosítása mellett. Ezután a [ `retrieve_model_explanation()` ](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlexplainer?view=azure-ml-py) függvény.

    ```python
    from azureml.train.automl.automlexplainer import retrieve_model_explanation

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, \
        per_class_imp = retrieve_model_explanation(best_run)
    ```

További információkért lásd: a [útmutató](how-to-configure-auto-train.md#explain-the-model-interpretability) az automatizált machine learning e funkciók engedélyezésével.

## <a name="next-steps"></a>További lépések

A Jupyter notebooks, amelyek bemutatják a fenti utasítások gyűjteménye, olvassa el a [Azure Machine Learning-e mintafüzetek](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
