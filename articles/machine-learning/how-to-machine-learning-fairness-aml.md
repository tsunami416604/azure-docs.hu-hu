---
title: A Python (előzetes verzió) ML-modelljeinek felmérése
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan állapíthatja meg a modelljeinek méltányos Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mesameki
author: mesameki
ms.reviewer: luquinta
ms.date: 07/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: a0f06c2db0a5bf196ae610efadd85ef1df8defe8
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691309"
---
# <a name="use-azure-machine-learning-with-the-fairlearn-open-source-package-to-assess-the-fairness-of-ml-models-preview"></a>Azure Machine Learning használata a Fairlearn nyílt forráskódú csomaggal, amellyel mérhető az ML-modellek tisztasága (előzetes verzió)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben a útmutatóban megtudhatja, hogyan használhatja a [Fairlearn](https://fairlearn.github.io/) nyílt forráskódú Python-csomagot Azure Machine learning a következő feladatok elvégzéséhez:

* Mérje fel a modell előrejelzésének méltányos mértékét. A gépi tanulással kapcsolatos méltányosság megismeréséhez tekintse meg a [méltányosság a gépi tanulással című cikket](concept-fairness-ml.md).
* Töltse fel, sorolja fel és töltse le a Azure Machine Learning studióhoz tartozó méltányos felmérési elemzéseket.
* Tekintse meg a tisztesség értékelésének irányítópultját Azure Machine Learning Studióban, hogy együttműködjön a modell (ek) méltányos elemzésével.

>[!NOTE]
> A méltányosság felmérése nem pusztán technikai gyakorlat. **Ez a csomag segít felmérni a gépi tanulási modellek igazságos értékelését, de csak a modell végrehajtásához és a döntések meghozatalához lehet döntéseket hozni.**  Habár ez a csomag segíti a mennyiségi mérőszámok azonosítását a méltányosság kiértékeléséhez, a gépi tanulási modellek fejlesztőinek is minőségi elemzést kell végezniük a saját modelljeik korrekt kiértékeléséhez.

## <a name="azure-machine-learning-fairness-sdk"></a>Azure Machine Learning méltányos SDK 

A Azure Machine Learning méltányos SDK, amely `azureml-contrib-fairness` a Azure Machine Learningon belül integrálja a nyílt forráskódú Python-csomagot, a [Fairlearn](http://fairlearn.github.io)-t. Ha többet szeretne megtudni a Fairlearn Azure Machine Learningon belüli integrálásáról, tekintse meg ezeket a [minta jegyzetfüzeteket](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness). A Fairlearn kapcsolatos további információkért tekintse meg a [példa útmutató](https://fairlearn.github.io/auto_examples/) és a [minta jegyzetfüzetek](https://github.com/fairlearn/fairlearn/tree/master/notebooks)című részt. 

A és a csomagok telepítéséhez használja az alábbi parancsokat `azureml-contrib-fairness` `fairlearn` :
```bash
pip install azureml-contrib-fairness
pip install fairlearn==0.4.6
```



## <a name="upload-fairness-insights-for-a-single-model"></a>Egyetlen modell méltányos bepillantást tölthet fel

Az alábbi példa azt mutatja be, hogyan használható a méltányos csomag a modellnek a Azure Machine Learningba való feltöltéséhez, és a Azure Machine Learning Studióban tekintse meg a tisztesség értékelésének irányítópultját.

1. Egy minta modell betanítása Jupyter-jegyzetfüzetbe. 

    Az adatkészlet esetében a jól ismert felnőtt népszámlálás adatkészletet használjuk, amelyet a használatával töltünk be `shap` (a kényelem érdekében). Ebben a példában ezt az adatkészletet a hitelnyújtási döntési problémaként kezeljük, és a címke azt jelzi, hogy az egyes hitelek korábban nem fizettek-e vissza. Az adatküldés során a rendszer betanítja a prediktív betanítást, hogy a korábban láthatatlan személyek fizessenek-e kölcsönt vagy sem. Feltételezi, hogy a modell előrejelzései alapján döntheti el, hogy kell-e egy adott személyt felkínálni.

    ```python
    from sklearn.model_selection import train_test_split
    from fairlearn.widget import FairlearnDashboard
    from sklearn.linear_model import LogisticRegression
    from sklearn.preprocessing import LabelEncoder, StandardScaler
    import pandas as pd
    import shap

    # Load the census dataset
    X_raw, Y = shap.datasets.adult()
    X_raw["Race"].value_counts().to_dict()
    

    # (Optional) Separate the "sex" and "race" sensitive features out and drop them from the main data prior to training your model
    A = X_raw[['Sex','Race']]
    X = X_raw.drop(labels=['Sex', 'Race'],axis = 1)
    X = pd.get_dummies(X)
    
    sc = StandardScaler()
    X_scaled = sc.fit_transform(X)
    X_scaled = pd.DataFrame(X_scaled, columns=X.columns)

    # Perform some standard data preprocessing steps to convert the data into a format suitable for the ML algorithms
    le = LabelEncoder()
    Y = le.fit_transform(Y)

    # Split data into train and test
    from sklearn.model_selection import train_test_split
    from sklearn.model_selection import train_test_split
    X_train, X_test, Y_train, Y_test, A_train, A_test = train_test_split(X_scaled, 
                                                        Y, 
                                                        A,
                                                        test_size = 0.2,
                                                        random_state=0,
                                                        stratify=Y)

    # Work around indexing issue
    X_train = X_train.reset_index(drop=True)
    A_train = A_train.reset_index(drop=True)
    X_test = X_test.reset_index(drop=True)
    A_test = A_test.reset_index(drop=True)

    # Improve labels
    A_test.Sex.loc[(A_test['Sex'] == 0)] = 'female'
    A_test.Sex.loc[(A_test['Sex'] == 1)] = 'male'


    A_test.Race.loc[(A_test['Race'] == 0)] = 'Amer-Indian-Eskimo'
    A_test.Race.loc[(A_test['Race'] == 1)] = 'Asian-Pac-Islander'
    A_test.Race.loc[(A_test['Race'] == 2)] = 'Black'
    A_test.Race.loc[(A_test['Race'] == 3)] = 'Other'
    A_test.Race.loc[(A_test['Race'] == 4)] = 'White'


    # Train a classification model
    lr_predictor = LogisticRegression(solver='liblinear', fit_intercept=True)
    lr_predictor.fit(X_train, Y_train)

    # (Optional) View this model in Fairlearn's fairness dashboard, and see the disparities which appear:
    from fairlearn.widget import FairlearnDashboard
    FairlearnDashboard(sensitive_features=A_test, 
                       sensitive_feature_names=['Sex', 'Race'],
                       y_true=Y_test,
                       y_pred={"lr_model": lr_predictor.predict(X_test)})
    ```

2. Jelentkezzen be Azure Machine Learningba, és regisztrálja a modellt.
   
    A méltányosság irányítópultja integrálható a regisztrált vagy nem regisztrált modellekkel. Regisztrálja Azure Machine Learning modelljét a következő lépésekkel:
    ```python
    from azureml.core import Workspace, Experiment, Model
    import joblib
    import os
    
    ws = Workspace.from_config()
    ws.get_details()

    os.makedirs('models', exist_ok=True)
    
    # Function to register models into Azure Machine Learning
    def register_model(name, model):
        print("Registering ", name)
        model_path = "models/{0}.pkl".format(name)
        joblib.dump(value=model, filename=model_path)
        registered_model = Model.register(model_path=model_path,
                                        model_name=name,
                                        workspace=ws)
        print("Registered ", registered_model.id)
        return registered_model.id

    # Call the register_model function 
    lr_reg_id = register_model("fairness_linear_regression", lr_predictor)
    ```

3. Kiszámítja a méltányosság mérőszámait.

    Irányítópult-szótár létrehozása a Fairlearn `metrics` csomagjának használatával. A `_create_group_metric_set` metódushoz hasonló argumentumok tartoznak az irányítópult-konstruktorhoz, azzal a különbséggel, hogy az érzékeny funkciók szótárként lesznek átadva (annak érdekében, hogy a nevek elérhetők legyenek). A metódus meghívásakor meg kell adni az előrejelzés típusát (ebben az esetben a bináris besorolást is).

    ```python
    #  Create a dictionary of model(s) you want to assess for fairness 
    sf = { 'Race': A_test.Race, 'Sex': A_test.Sex}
    ys_pred = { lr_reg_id:lr_predictor.predict(X_test) }
    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Töltse fel az előre számított méltányosság mérőszámait.
    
    Most importálja `azureml.contrib.fairness` a csomagot a feltöltés végrehajtásához:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Hozzon létre egy kísérletet, majd futtasson, majd töltse fel az irányítópultot:
    ```python
    exp = Experiment(ws, "Test_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness insights of Logistic Regression Classifier"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```
5. A Azure Machine Learning Studióban található tisztesség-irányítópult megtekintése

    Ha elvégezte az előző lépéseket (a kinyert tisztasági bepillantást Azure Machine Learning), megtekintheti a tisztességes irányítópultot [Azure Machine learning Studióban](https://ml.azure.com). Ez az irányítópult a Fairlearn-ben megadott vizualizációs irányítópult, amely lehetővé teszi, hogy elemezze a különbségeket a bizalmas szolgáltatás alcsoportjai között (pl.: férfi és nő).
    Kövesse az alábbi elérési utakat a vizualizációs irányítópult eléréséhez Azure Machine Learning Studióban:

    * **Kísérletek panel (előzetes verzió)**
    1. Válassza a **kísérletek** lehetőséget a bal oldali ablaktáblán a Azure Machine learning futtatott kísérletek listájának megjelenítéséhez.
    1. Válasszon ki egy adott kísérletet a kísérlet összes futtatásának megtekintéséhez.
    1. Válasszon egy futtatást, majd a **tisztesség** fület a magyarázat vizualizáció irányítópultra.


    [![Méltányosság irányítópultja](./media/how-to-machine-learning-fairness-aml/dashboard.png)](./media/how-to-machine-learning-fairness-aml/dashboard.png#lightbox)
    
    * **Modellek panel**
    1. Ha az előző lépések követésével regisztrálta az eredeti modellt, akkor a bal oldali ablaktáblán kiválaszthatja a **modelleket** .
    1. Válasszon ki egy modellt, majd a **tisztesség** lapon tekintse meg a magyarázó vizualizáció irányítópultját.

    Ha többet szeretne megtudni a vizualizációs irányítópultról és arról, hogy mit tartalmaz, tekintse meg a Fairlearn [felhasználói útmutatóját](https://fairlearn.github.io/user_guide/assessment.html#fairlearn-dashboard).

## <a name="upload-fairness-insights-for-multiple-models"></a>Több modell méltányos bepillantást tölthet fel

Ha több modell összehasonlítását szeretné megtekinteni, és látni szeretné, hogy a tisztesség értékelése hogyan különbözik, több modellt is átadhat a vizualizációs irányítópulthoz, és megkeresheti a teljesítmény-méltányos kompromisszumot.

1. A modellek betanítása:
    
    A korábbi logisztikai regressziós modellen kívül egy második besorolást is létrehozunk egy támogatási vektoros számítógép-kalkulátor alapján, és fel kell tölteni a tisztességes irányítópult-szótárt a Fairlearn `metrics` csomagjának használatával. Vegye figyelembe, hogy itt kihagyjuk az betöltési és előfeldolgozási lépéseket, és rögtön a modell betanítási szakaszába kerülnek.


    ```python
    # Train your first classification model
    from sklearn.linear_model import LogisticRegression
    lr_predictor = LogisticRegression(solver='liblinear', fit_intercept=True)
    lr_predictor.fit(X_train, Y_train)

    # Train your second classification model
    from sklearn import svm
    svm_predictor = svm.SVC()
    svm_predictor.fit(X_train, Y_train)
    ```

2. A modellek regisztrálása

    Ezután regisztráljon mindkét modellt Azure Machine Learningon belül. A későbbi metódusokban való használat érdekében az eredményeket egy olyan szótárban tárolja, amely a `id` regisztrált modell (egy formátumú karakterlánc) leképezi a `name:version` prediktív szolgáltatást:

    ```python
    model_dict = {}

    lr_reg_id = register_model("fairness_linear_regression", lr_predictor)
    model_dict[lr_reg_id] = lr_predictor

    svm_reg_id = register_model("fairness_svm", svm_predictor)
    model_dict[svm_reg_id] = svm_predictor
    ```

3. A Fairlearn irányítópult helyi betöltése

    A méltányos elemzések Azure Machine Learningba való feltöltése előtt megvizsgálhatja ezeket az előrejelzéseket egy helyileg meghívott Fairlearn-irányítópulton. 



    ```python
    #  Generate models' predictions and load the fairness dashboard locally 
    ys_pred = {}
    for n, p in model_dict.items():
        ys_pred[n] = p.predict(X_test)

    from fairlearn.widget import FairlearnDashboard

    FairlearnDashboard(sensitive_features=A_test, 
                    sensitive_feature_names=['Sex', 'Race'],
                    y_true=Y_test.tolist(),
                    y_pred=ys_pred)
    ```

3. Kiszámítja a méltányosság mérőszámait.

    Irányítópult-szótár létrehozása a Fairlearn `metrics` csomagjának használatával.

    ```python
    sf = { 'Race': A_test.Race, 'Sex': A_test.Sex }

    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Töltse fel az előre számított méltányosság mérőszámait.
    
    Most importálja `azureml.contrib.fairness` a csomagot a feltöltés végrehajtásához:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Hozzon létre egy kísérletet, majd futtasson, majd töltse fel az irányítópultot:
    ```python
    exp = Experiment(ws, "Compare_Two_Models_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness Assessment of Logistic Regression and SVM Classifiers"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```


    Az előző szakaszhoz hasonlóan a Azure Machine Learning Studióban a fentiekben ismertetett elérési utak **Experiments** egyikét **Models**is követheti a vizualizációs irányítópult eléréséhez, és a két modellt a méltányosság és a teljesítmény szempontjából hasonlíthatja össze.


## <a name="upload-unmitigated-and-mitigated-fairness-insights"></a>Nem enyhített és enyhített tisztasági adatok feltöltése

Használhatja a Fairlearn [enyhítő algoritmusait](https://fairlearn.github.io/user_guide/mitigation.html), összehasonlíthatja a létrehozott, kinyert modell (eke) t az eredeti, nem enyhített modellel, és megtekintheti a teljesítmény/méltányosság elleni kompromisszumokat az összehasonlított modellek között.

Ha szeretné megtekinteni egy példát, amely bemutatja a [Grid Search](https://fairlearn.github.io/user_guide/mitigation.html#grid-search) -enyhítő algoritmus használatát (amely a különböző tisztességgel és teljesítményű adatforgalommal rendelkező, csökkenthető modellek gyűjteményét hozza létre), tekintse meg ezt a [minta jegyzetfüzetet](https://github.com/Azure/MachineLearningNotebooks/blob/master/contrib/fairness/fairlearn-azureml-mitigation.ipynb). 

Ha több modellt tölt fel egy adott futtatásban, az a méltányosság és a teljesítmény tekintetében lehetővé teszi a modellek összehasonlítását. További rákattinthat a modell összehasonlító diagramján megjelenő modellekre is, hogy megtekintse az adott modell részletes, korrekt megállapításait.


[![Modell összehasonlító Fairlearn irányítópultja](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png)](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png#lightbox)
    

## <a name="next-steps"></a>További lépések

[További információ a modell tisztaságáról](concept-fairness-ml.md)

[Tekintse meg Azure Machine Learning a méltányos minta notebookok](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)
