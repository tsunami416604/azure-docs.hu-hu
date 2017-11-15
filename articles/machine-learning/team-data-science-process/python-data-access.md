---
title: "A Machine Learning Python ügyféloldali kódtár adatkészletek eléréséhez |} Microsoft Docs"
description: "Telepítheti és használhatja a Python ügyféloldali kódtár férhessen hozzá és felügyelhesse Azure Machine Learning adatok biztonságos helyen a helyi Python-környezetben."
services: machine-learning
documentationcenter: python
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 9ab42272-c30c-4b7e-8e66-d64eafef22d0
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: huvalo;bradsev
ms.openlocfilehash: 3cffb90baadef570bbb7e4e2d96741cc0420b0a2
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2017
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Hozzáférés az adathalmazokhoz Python segítségével, az Azure Machine Learning Python ügyfélkönyvtárat használva
A Microsoft Azure Machine Learning Python ügyféloldali kódtár preview engedélyezheti a helyi Python-környezetben az Azure Machine Learning adatkészletekhez a biztonságos hozzáférést, és lehetővé teszi, hogy a létrehozását és kezelését egy munkaterület adathalmazok.

Ez a témakör a kapcsolatos utasításokat tartalmazza:

* Telepítse a Machine Learning Python ügyféloldali kódtár 
* hozzáférés, és töltse fel az adatkészleteket, beleértve az beszerzése a helyi Python környezetből Azure Machine Learning adatkészletek hozzáférési útmutatást
* köztes adatkészletek elérje kísérletek
* használjon, Python adatkészletek számbavétele, metaadatok elérheti, olvassa el a dataset tartalmát, hozzon létre új adatkészletek és frissítheti a meglévő adatkészletek

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="prerequisites"></a>Előfeltételek
A Python ügyféloldali kódtára a tesztek alapján a következő környezetekben:

* Windows, Mac és Linux
* Python 2.7, 3.3 és 3.4

A következő csomag rendelkezik egy függőséget:

* kérés
* Python-dateutil
* pandas

Javasoljuk, használjon, mint a rendelkező Python elosztási [Anaconda](http://continuum.io/downloads#all) vagy [lombkoronaszint](https://store.enthought.com/downloads/), amelyek a Python, IPython származnak, és a fenti három csomagot telepítve. Bár IPython nem feltétlenül szükséges, kezelésére és adatok interaktív megjelenítése egy nagyszerű környezetet is.

### <a name="installation"></a>Az Azure Machine Learning Python ügyféloldali kódtár telepítése
Az Azure Machine Learning Python ügyféloldali kódtár is telepíteni kell a témakörben ismertetett feladatok végrehajtásához. Az elérhető a [Python-Csomagindexet](https://pypi.python.org/pypi/azureml). A telepítéshez a Python-környezetben a következő parancsot a a helyi Python-környezetben:

    pip install azureml

Azt is megteheti, töltse le és telepítse a forrásból származó [github](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

    python setup.py install

Git a gépen telepítve van, a pip használatával közvetlenül telepítse a git-tárházba:

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


## <a name="datasetAccess"></a>Studio kódtöredékek adatkészletek eléréséhez használja
A Python ügyféloldali kódtár programozott hozzáférést biztosít a meglévő adatkészletek az adott időpontig futtatott kísérletek.

A Studio webes felhasználói felületen keresztüli kódrészletek, amelyek tartalmazzák a szükséges információkat, töltse le és adatkészletek deszerializálni a hely számítógépén Pandas DataFrame objektumként is létrehozhat.

### <a name="security"></a>Az adatelérési biztonsági
A Python ügyféloldali kódtár használható tartalmazza a munkaterület azonosítója és engedélyezési Studio által előírt kódrészletek token. Ezek a munkaterület teljes hozzáférést biztosítanak, és védeni kell, például a jelszót.

Biztonsági okokból a kód részlet szolgáltatás működik, csak a felhasználók számára, amelyek rendelkeznek a szerepkörük állítja be **tulajdonos** a munkaterülethez. A szerepkör az Azure Machine Learning Studióban jelenik meg a **felhasználók** lapon az **beállítások**.

![Biztonság][security]

Ha nincs beállítva az a szerepkör **tulajdonos**, vagy kérelem tulajdonos következőre, vagy kérje meg a munkaterület biztosítja, hogy a kódrészletet a tulajdonosa használhatja.

A hitelesítési jogkivonat beszerzése, tegye a következők egyikét:

* Kérje meg a jogkivonat a tulajdonosa. Tulajdonosok érhetik el a hitelesítési tokenek saját munkaterület Studio a beállítások lapon. Válassza ki **beállítások** a bal oldali ablaktáblán, majd kattintson a **engedélyezési JOGKIVONATOK** az elsődleges és másodlagos jogkivonatok megjelenítéséhez.  Bár az elsődleges vagy a másodlagos engedélyezési jogkivonatok használható a kódrészletet, azt javasoljuk, hogy a tulajdonosok csak használ-e a másodlagos engedélyezési jogkivonatokat.

![Engedélyezési jogkivonatok](./media/python-data-access/ml-python-access-settings-tokens.png)

* Kérje meg, hogy a szerepkör tulajdonosa előléptetni.  Ehhez a munkaterület aktuális tulajdonosának kell először távolítsa el azt a munkaterületet, majd újra hívni Önt, tulajdonos.

Miután a fejlesztők beszerezte a munkaterület azonosítója és engedélyezési jogkivonat, képesek hozzáférni a munkaterület használatával a kódrészletet, függetlenül azok szerepét.

A hitelesítési tokenek kezelt a **engedélyezési JOGKIVONATOK** lapon az **beállítások**. Újragenerálás őket, de ez az eljárás visszavonja az előző lexikális elem a hozzáférést.

### <a name="accessingDatasets"></a>Hozzáférés adatkészletek helyi Python-alkalmazás
1. A Machine Learning Studióban, kattintson a **ADATKÉSZLETEK** a bal oldali navigációs sávon.
2. Válassza ki a szeretne hozzáférni adathalmaz. Az adathalmaz bármelyikét kiválaszthatja a **saját ADATKÉSZLETEK** lista vagy a **minták** listája.
3. Kattintson az alsó eszköztár **adatok hozzáférési kód generálása**. Ha az adatok formátuma nem kompatibilis a Python ügyféloldali kódtár, ez a gomb le van tiltva.
   
    ![Adathalmazok][datasets]
4. Válassza ki a kódrészletet a ablakban jelenik meg, és másolja a vágólapra.
   
    ![Hozzáférési kód][dataset-access-code]
5. Illessze be a notebook a helyi Python-alkalmazás.
   
    ![Notebook][ipython-dataset]

## <a name="accessingIntermediateDatasets"></a>A Machine Learning kísérleteket a hozzáférés köztes adatkészletek
Egy kísérletben a Machine Learning Studio futtatása után is lehet a modulok kimeneti csomópontról hozzáférhetnek a köztes adathalmazokat. Köztes adatkészletek olyan adatok, amelyek a létrehozott és használt köztes lépések egy modell eszköz futtatásakor.

Mindaddig, amíg a adatok formátuma nem kompatibilis a Python ügyféloldali kódtára a köztes adatkészletek érhető el.

A következő formátum támogatott (Ezek állandók szerepelnek a `azureml.DataTypeIds` osztály):

* Egyszerű szöveg
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

A formátum azt is meghatározhatja, hogy egy modul kimeneti csomópont fölött. A csomópont neve eszközleírásként együtt jelenik meg.

Néhány a modulok, mint a [vegyes] [ split] modul nevű formátumra kimeneti `Dataset`, amely nem támogatja a Python ügyféloldali kódtár.

![A DataSet formátumban][dataset-format]

Kell használnia, mint a konverziós modul [CSV átalakítása][convert-to-csv], a kimenetnek feltölti egy támogatott formátumra.

![GenericCSV formátum][csv-format]

A következő lépések bemutatják egy példa, amely hoz létre a kísérlet, futtatja, és a köztes dataset fér hozzá.

1. Hozzon létre egy új kísérlet.
2. Helyezze be egy **bináris osztályozási felnőtt nyilvántartásba bevétel dataset** modul.
3. Helyezze be a [vegyes] [ split] modul, és csatlakoztassa a bemeneti adatkészlet modul kimenetével.
4. Helyezze be a [CSV átalakítása] [ convert-to-csv] modul, és csatlakoztassa a bemeneti egy a [vegyes] [ split] modul kimenete.
5. A kísérlet mentéséhez, majd futtassa és várja meg, amíg a futása befejeződik.
6. A kimeneti csomóponton kattintson a a [CSV átalakítása] [ convert-to-csv] modul.
7. Ha a helyi menü megjelenik, válassza ki a **adatok hozzáférési kód generálása**.
   
    ![A helyi menü][experiment]
8. Válassza ki a kódrészletet, és a megjelenő ablakban másolja a vágólapra.
   
    ![Hozzáférési kód][intermediate-dataset-access-code]
9. Illessze be a kódját a notebook.
   
    ![Notebook][ipython-intermediate-dataset]
10. Az adatok matplotlib használatával jelenítheti meg. Ez a kor oszlop hisztogram jelenik meg:
    
    ![Hisztogram][ipython-histogram]

## <a name="clientApis"></a>A Machine Learning Python ügyféloldali kódtár segítségével hozzáférni, olvasását, létrehozását és adatkészletek kezelése
### <a name="workspace"></a>Munkaterület
A munkaterületen a belépési pont, a Python ügyféloldali kódtára a rendszer. Adja meg a `Workspace` osztály a munkaterület azonosítója és engedélyezési jogkivonatot példány létrehozásához:

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>Adatkészletek számbavétele
Operációs rendszer egy adott munkaterület minden adathalmazok:

    for ds in ws.datasets:
        print(ds.name)

Operációs rendszer csak a felhasználó által létrehozott adatkészletek:

    for ds in ws.user_datasets:
        print(ds.name)

Operációs rendszer csak a például adatkészleteket:

    for ds in ws.example_datasets:
        print(ds.name)

A DataSet adatkészlet neve (kis-és nagybetűket) érhető el:

    ds = ws.datasets['my dataset name']

Vagy hozzá tud férni az index szerinti:

    ds = ws.datasets[0]


### <a name="metadata"></a>Metaadatok
Adatkészletek metaadatok mellett tartalom van. (Köztes adatkészletek Ez a szabály alól kivételt, és minden metaadatot.)

Néhány metaadatok értéket rendeli hozzá a felhasználó a létrehozás időpontjában:

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

Azure ML által hozzárendelt értékek:

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Tekintse meg a `SourceDataset` tudhat meg a rendelkezésre álló metaadatok osztály.

### <a name="read-contents"></a>Tartalmának olvasása
A Machine Learning Studio által biztosított automatikusan kódtöredékek töltse le, és az adatkészlet egy Pandas DataFrame objektum deszerializálása. Ez a lépés a `to_dataframe` módszert:

    frame = ds.to_dataframe()

Töltse le a nyers adatokat, és hajtsa végre a deszerializálás, saját kezűleg szeretné, ha ez egy lehetőséget. A jelenleg ez a lehetőség csak formátum például "ARFF", amely a Python ügyféloldali kódtár nem deszerializálható.

Tartalmának olvasása szövegként:

    text_data = ds.read_as_text()

Tartalmának olvasása bináris:

    binary_data = ds.read_as_binary()

Nyissa meg a tartalmát egy stream csak is:

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>Hozzon létre egy új adatkészlet
A Python ügyféloldali kódtár lehetővé teszi, hogy adatkészletek a Python-programból feltölthetők. Ezek az adatkészletek állnak majd a munkaterületen.

Ha az adatok egy Pandas DataFrame van, használja a következő kódot:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Ha az adatok már tartozik, akkor használhatja:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

A Python ügyféloldali kódtár képes szerializálni a Pandas DataFrame a következő formátumban (Ezek állandók szerepelnek a `azureml.DataTypeIds` osztály):

* Egyszerű szöveg
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>Egy meglévő adatkészlet frissítése
Ha megpróbálja feltölteni egy meglévő adatkészlet egyező nevű új adatkészlet, egy ütközés hiba szerezheti be.

Egy meglévő adatkészlet frissítéséhez először kell a meglévő adatkészletet hivatkozás:

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Ezután `update_from_dataframe` szerializálni, és cserélje ki annak tartalmát a DataSet adatkészlet Azure:

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Ha szeretné szerializálni az adatokat, más formátumba, adjon meg egy értéket a választható `data_type_id` paraméter.

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Meg nem kötelezően leírást adhat meg új értéket ad a `description` paraméter.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

Opcionálisan megadhatja egy új nevet értéket ad a `name` paraméter. Ettől kezdve a fogja lekérni az adatkészlet csak az új névre. Az alábbi kód frissíti az adatokat, nevét és leírását.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        name='existing dataset v2',
        description='data up to feb 2015',
    )

    print(dataset.data_type_id)                    # 'GenericCSV'
    print(dataset.name)                            # 'existing dataset v2'
    print(dataset.description)                     # 'data up to feb 2015'

    print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
    print(ws.datasets['existing dataset'].name)    # IndexError

A `data_type_id`, `name` és `description` paraméterek megadása nem kötelező, és alapértelmezés szerint az előző értéket. A `dataframe` paraméter megadása mindig kötelező.

Ha az adatok már szerializált, `update_from_raw_data` helyett `update_from_dataframe`. Ha a átadni `raw_data` helyett `dataframe`, hasonló módon működik.

<!-- Images -->
[security]:./media/python-data-access/security.png
[dataset-format]:./media/python-data-access/dataset-format.png
[csv-format]:./media/python-data-access/csv-format.png
[datasets]:./media/python-data-access/datasets.png
[dataset-access-code]:./media/python-data-access/dataset-access-code.png
[ipython-dataset]:./media/python-data-access/ipython-dataset.png
[experiment]:./media/python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

