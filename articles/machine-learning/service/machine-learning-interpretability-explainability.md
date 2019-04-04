---
title: Modell-e
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan használható az Azure Machine Learning e SDK annak magyarázata, hogy miért a modell előrejelzéseket tesz. Használat során betanítási vagy következtetési tudni, hogy a modell előrejelzéseket tesz.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 03/27/2019
ms.openlocfilehash: 1cd5f48e8e0e74dfa04465993246e5d68840a783
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58919725"
---
# <a name="azure-machine-learning-interpretability-sdk"></a>Az Azure Machine Learning-e SDK

Ismerje meg, hogyan annak magyarázata, hogy miért a modell érkezik-e az előrejelzés teszi. Az Azure Machine Learning e SDK a következőket teheti a modellt, amely a következő okokból fontos ismertetik:

* Ügyfelek és az érdekelt felekkel szeretné tudni, hogy **nyugodtan megbízhatják az előrejelzéseket Ha a modell lehetővé teszi a**.
* Értenie az adatokhoz, mint szeretné megismerni **lekérdezése a modell a háttérismeretek**. Szükség lehet tájékozott döntést az eszközök **a modell fejlesztéséhez**.
* Vállalati, ismernie kell **viselkedését a modell a különböző disztribúciók bemeneti** és **hogyan fogja a modell viselkednek meghatározott bevitel elemzése közben**.

Machine learning-e fontos gépi tanulási fejlesztési ciklus két fázisban történik: **képzési** idő és **következtetési** idő:

* Során **képzési**: Modell tervezők és értékelők szükséges az érdekelt felek hozhat létre megbízhatósági modell kimenete ismertetik e eszközöket. E eszközök lehetővé teszi, hogy a modell debug:

    * Egyezik annak viselkedését a célok és célkitűzések?
    * Van az alábbiak torzítatlan azt?

* Során **következtetési**: Előrejelzés kell lennie a személyeknek a modellt használó explainable. Például, miért volt a modell a törlesztés kölcsön megtagadása, vagy előre jelezni, hogy egy befektetési portfólió sorozatéhoz nagyobb eséllyel?

Az Azure Machine Learning e SDK technológiák magában foglalja a Microsoft által kifejlesztett és bevált külső gyártótól származó kódtárakat (például Alakzatadatok és SÁRGÁSZÖLD). Közös API-t biztosít a beépített kódtárak között, és az Azure Machine Learning services szolgáltatásainak használatával. 

Ez az SDK használatával, machine learning-modellek is ismertetik **globálisan az összes adat**, vagy **helyileg, egy adott adatpontot** a legmodernebb technológiák használatával egy könnyen használható és méretezhető módon.

## <a name="how-does-it-work"></a>Hogyan működik?

Az Azure Machine Learning-e a modell globális működését és a egy adott előrejelzési megértéséhez alkalmazhatók. Az előbbi neve globális magyarázat, és ez utóbbi nevezzük helyi magyarázata.

Az Azure Machine Learning-e módszerekkel is csoportosíthatók az módszer e modell független vagy adott modell alapján. Egyes metódusok a céloznia modellek bizonyos típusú. Ha például Alakzatadatok a fa ismertető csak érvényes fa-alapú modell. Egyes metódusok kezeli a modell egy fekete mezőt, például mimic ismertető vagy Alakzatadatok a kernel ismertető. E SDK az Azure Machine Learning modul ezen különböző megközelítések adatkészletek adatmodell-típusokat, és a használati esetek alapján.

Az Azure Machine Learning-e meg, hogyan modell lehetővé teszi az előrejelzési információkat készletét adja vissza. Az adatokat például elemeket tartalmazza:

* Globális vagy helyi relatív funkció fontosság
* Globális vagy helyi szolgáltatás és az előrejelzési kapcsolat
* Interaktív vizualizációkat:

    * Előrejelzések
    * A szolgáltatás és az előrejelzési kapcsolatok
    * Relatív funkció fontossági értékek helyileg és globálisan

## <a name="architecture"></a>Architektúra

Az Azure Machine Learning e SDK szerkezete két Python-csomagokat be:

* [azureml.EXPLAIN.Model](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py) – a fő csomagot, amely tartalmazza a Microsoft által támogatott funkciók.
* `azureml.contrib.explain.model` – Előzetes verzió és kísérleti funkciók kipróbálható.

    > [!IMPORTANT]
    > Contrib is teljes mértékben nem támogatottak. A kísérleti funkciók érett lenni, mivel azokat a fő csomag fokozatosan kerül.

### <a name="explainers"></a>Explainers

Az Azure Machine Learning e SDK explainers két készletnyi mutatja be: Közvetlen Explainers és Meta Explainers.

__A közvetlen explainers__ integrált kódtárak származnak. Az SDK összes explainers burkolja, így egy közös API-t és a kimeneti formátum szolgáltatnak. Az SDK-ban elérhető közvetlen explainers listáját a következők:

> [!TIP]
> Ha járatos több közvetlenül ezek explainers, közvetlenül hívhatók meg azokat a közös API-t és a kimeneti formátum használata helyett.

* **Fatartomány ismertető**: Az Alakzatadatok a fa ismertető, amelynek fő témája a polinom time gyors Alakzatadatok érték költségbecslési algoritmus adott fák és együttesek fák.
* **Részletes ismertető**: Alapján a magyarázat az Alakzatadatok, részletes ismertető "egy nagy sebességű approximációs algoritmus, deep learning-modellek az Alakzatadatok értékek, amelyek az Alakzatadatok NIPS könyvben leírt DeepLIFT épül, amely kapcsolatot is. TensorFlow-modellek és a TensorFlow-háttérrendszer használatával Keras-modellek támogatottak (nincs PyTorch előzetes támogatása) ".
* **Kernel Explainer**: Alakzatadatok a Kernel ismertető kifejezetten súlyozott helyi lineáris regresszió segítségével bármely modellhez tartozó Alakzatadatok értékeket használja.
* **Ismertető utánzására**: Mimic ismertető az elgondolásra épül, globális helyettes modellek alapul. Egy globális helyettes modell egy belsőleg interpretable modell, amely a lehető legpontosabban méretkorlátjai az előrejelzés egy fekete mezőt modell tanítása. Adattudós tudja értelmezni a helyettes modell következtetéseket a fekete dobozhoz modellel kapcsolatos.
* **SÁRGÁSZÖLD ismertető**: SÁRGÁSZÖLD alapozva SÁRGÁSZÖLD ismertető a állapota-a-a legújabb helyi interpretable modell-agnosztikus magyarázatokat (SÁRGÁSZÖLD) algoritmust használja helyi helyettes modellek létrehozásának eljárásait. SÁRGÁSZÖLD ellentétben a globális helyettes modellek összpontosít az egyes előrejelzéseket elmagyarázni helyi helyettes modellek betanítása.
* **HAN szöveg ismertető**: HAN szöveg ismertető szöveges adatok egy adott fekete dobozhoz szöveg modell modell magyarázatok lekérése egy hierarchikus figyelmet hálózatot használ. Az egy adott oktatói modell előre jelzett kimenetek HAN helyettes modell betanításához azt. Globálisan a szöveg forrásgyűjteményébe között képzést, miután egy fine-tune lépést, az adott dokumentum hozzáadtuk a magyarázatokat pontosságának javítása érdekében. HAN egy kétirányú RNN két figyelmet rétegeket, használja a mondat és a word figyelmet. A DNN tanított az oktatói modell, és a egy adott dokumentum finomította, hogy bontsa ki a word importances a figyelmet rétegek. HAN pontosabb, mint SÁRGÁSZÖLD vagy Alakzatadatok szöveges adatok esetében, de költségesebb, a használati idő, valamint képzési is található. Azonban hajtottunk végre a betanítási idő fejlesztései, így a felhasználó kesztyűtartó szóbeágyazásokból, a hálózati inicializálása arra, bár ez továbbra is lassú. A betanítási idő jelentősen javítható, ha egy távoli Azure-GPU virtuális gépen futó HAN. HAN végrehajtásának leírt "Hierarchikus figyelmet hálózatok dokumentum besorolási (Yang et al., 2016)" ([https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf)).

__Meta explainers__ automatikusan válassza ki a megfelelő közvetlen ismertető és az ajánlott magyarázata adatok alapján az adott modell és az adatkészletek létrehozása. A metaadatok explainers összes könyvtár (Alakzatadatok, SÁRGÁSZÖLD, GA2M, előnézet, stb.) a Microsoft integrált vagy fejlesztett használhatja. Az SDK-ban elérhető meta explainers a következők:

* **Táblázatos ismertető**: A táblázatos adatkészleteket használja.
* **Szöveg ismertető**: A szöveg adatkészleteket használja.
* **Kép ismertető** a lemezkép-adatkészleteket használja.

Emellett a meta lehetőségre kattint, a közvetlen explainers, meta explainers felett az alapul szolgáló kódtárak további funkciók fejlesztése és növelheti a sebességet és méretezhetőséget biztosít a közvetlen explainers keresztül.

Jelenleg `TabularExplainer` alkalmaz az alábbi logika, a közvetlen Explainers meghívásához:

1. Ha egy fa-alapú modell, a alkalmazni `TreeExplainer`, más
2. Ha egy DNN-modell, a alkalmazni `DeepExplainer`, más
3. Fekete beépített modellként kezelje azt, és a alkalmazni `KernelExplainer`

A beépített intelligencia `TabularExplainer` kifinomultabb fog válni, további kódtárak pedig az SDK integrálva vannak, és hogy ismertetése és az egyes ismertető hátrányai.

`TabularExplainer` a közvetlen Explainers keresztül is rendelkezik végzett jelentős funkciót és a teljesítmény fejlesztései:

* **Az inicializálás adatkészlet összefoglaló**. Olyan esetekben, ahol magyarázata sebességétől legfontosabb hogy összesíteni az inicializálási adatkészlet és létrehozása egy kis készletét reprezentatív mintát, amely felgyorsítja a globális és a helyi magyarázata.
* **Az értékelés adatkészlet mintavételi**. Ha a felhasználó átadja az értékelés minták nagy készletét, de nem ténylegesen szükséges összes ki kell értékelni őket, a mintavételi paraméterrel állítható az igaz értékre a globális magyarázat felgyorsítása érdekében.
* **KNN gyors magyarázata**. Abban az esetben, ahol magyarázata kell lennie egy egyetlen pontozási/előrejelzéssel töltött gyorsaságának KNN módszer is használható. Globális ismertetése során az inicializálási minták és a megfelelő legfelső-k szolgáltatások megmaradnak. A magyarázat, minden egyes értékelési minta létrehozásához, a KNN metódus a legtöbb hasonló mintát a inicializálási minták a kereséséhez használt, és a legtöbb hasonló mintát top-k funkciók vissza, a kiértékelési minta a top-k funkciókat.

Az alábbi ábrán látható a két készletnyi közvetlen és meta explainers közötti kapcsolatot.

[![MTanulási e architektúra achine](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)

### <a name="models-supported"></a>Támogatott modellek

Nagy méretű adatkészletek a Pythonban képzett modellekre `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData`, vagy `scipy.sparse.csr_matrix` a Machine Learning e SDK által támogatott formátumban.

A magyarázat functions fogadja el a modellek és a folyamatok bemenetként. A modell áll rendelkezésre, ha a modell meg kell valósítania az előrejelző függvényben `predict` vagy `predict_proba` , amely megerősíti, a Scikit egyezmény. Egy folyamat (a csővezeték-parancsfájl nevét) áll rendelkezésre, ha a magyarázat függvény azt feltételezi, hogy a futó folyamat parancsfájl előrejelzést.

### <a name="local-and-remote-compute-target"></a>Helyi és távoli számítási célnak

A Machine Learning e SDK használata mindkét helyi és távoli számítási célokhoz tervezték. 

* Ha a Futtatás **helyileg**, az SDK nem tud kapcsolatba lépni bármely Azure-szolgáltatást.

* Ha a Futtatás **távolról**, a Futtatás kapcsolatos információkat az Azure Machine Learning futtassa-előzmények szolgáltatások be van jelentkezve. Ezeket az adatokat a rendszer naplózza, ha jelentéseket és vizualizációkat a magyarázat érhetők el azonnal az Azure Machine Learning munkaterület portál felhasználói elemzés céljából.

## <a name="train-and-explain-locally"></a>Betanítása és helyileg magyarázata

1. A helyi Jupyter notebook modellje betanításához. 

    ``` python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    # Split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data, breast_cancer_data.target, test_size=0.2, random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. Hívja meg a ismertető. Az ismertető objektum hárítható el, adja át a modellt, és a betanítási adatok. Igény szerint lehetőségeket adhat át. Besorolás használata esetén adja át a kimeneti osztály nevét.

    Az alábbi példa bemutatja, hogyan hozzon létre egy ismertető objektum [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py), [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py), és `LimeExplainer` helyileg. `TabularExplainer` hívja a három explainers alatti egyik (`TreeExplainer`, `DeepExplainer`, vagy `KernelExplainer`), és automatikusan a legmegfelelőbbhöz az használati esetekhez jelöl. Ugyanakkor a három alapul szolgáló explainers mindegyike közvetlenül hívja.

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    or
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
    explainer = MimicExplainer(model, x_train, LGBMExplainableModel, features=breast_cancer_data.feature_names, classes=classes)
    or
    from azureml.contrib.explain.model.lime.lime_explainer import LIMEExplainer
    explainer = LIMEExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    ```

3. A globális szolgáltatás fontossági értékek beolvasása.

    ```python
    # You can use the training data or the test data here. 
    global_explanation = explainer.explain_global(x_train)
    # Sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))
    ```

4. A szolgáltatás helyi fontossági értékek: használja a következő függvényhívások annak magyarázata, egy egyéni példány vagy példányok csoportjára is.

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0,:])
    or
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4,:])
    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

## <a name="train-and-explain-remotely"></a>Betanítása és távolról magyarázata

Az Azure Machine Learning szolgáltatás támogatja a különböző számítási célnak betaníthatja, míg a jelen szakaszban ismertetett példa bemutatja, hogyan ehhez AMLCompute használatával.

1. Helyi Jupyter notebook (például run_explainer.py) hozzon létre egy tanítási szkriptet.

    ``` python  
    run = Run.get_context()
    client = ExplanationClient.from_run(run)
    
    breast_cancer_data = load_breast_cancer()
    X_train, X_test, y_train, y_test = train_test_split(breast_cancer_data.data, breast_cancer_data.target, test_size = 0.2, random_state = 0)
    data = {
        "train":{"X": X_train, "y": y_train},        
        "test":{"X": X_test, "y": y_test}
    }
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(data['train']['X'], data['train']['y'])
    joblib.dump(value = clf, filename = 'model.pkl')
    # explain predictions on your local machine    
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(data["test"]["X"])
    # explain local data points (individual instances)
    local_explanation = explainer.explain_local(data["test"]["X"][0,:])
    # upload global and local explanation objects to Run History
    upload_model_explanation(run, local_explanation, top_k=2, comment='local explanation: top 2 features')
    # Uploading global model explanation data for storage or visualization in webUX
    # The explanation can then be downloaded on any compute
    # Multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # Or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. A betanítási Futtatás elküldéséhez kövesse a lépéseket a [állítsa be a modell betanítása és számítási célnak](how-to-set-up-training-targets.md#amlcompute) cikk. A lépések segítségével hozzon létre egy Azure Machine Learning COMPUTE számítási célt, és ezután küldenie a betanítási Futtatás.

3. Töltse le a helyi Jupyter notebook a magyarázatot. 

    ``` python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    # Get model explanation data
    client = ExplanationClient.from_run(run)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Or you can use the saved run.id to retrive the feature importance values
    client = ExplanationClient.from_run_id(ws, experiment_name, run.id)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="next-steps"></a>További lépések

A Jupyter notebooks, amelyek bemutatják a fenti utasítások gyűjteménye, olvassa el a [Azure Machine Learning-e mintafüzetek](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).