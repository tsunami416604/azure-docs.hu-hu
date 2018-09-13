---
title: A Machine Learning Python ügyfélkönyvtárat adatkészletek elérése |} A Microsoft Docs
description: Telepítheti és használhatja a Python ügyféloldali kódtár eléréséhez, és az Azure Machine Learning adatok biztonságos kezelésére egy helyi Python-környezetet.
services: machine-learning
documentationcenter: python
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 9ab42272-c30c-4b7e-8e66-d64eafef22d0
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 496ffdcb5cc2e7b208a53dc8c1d8ba4c0818945f
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44719970"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Hozzáférés az adathalmazokhoz Python segítségével, az Azure Machine Learning Python ügyfélkönyvtárat használva
Az előzetes verzióját a Microsoft Azure Machine Learning Python ügyfélkönyvtárat engedélyezheti a helyi Python-környezetben az Azure Machine Learning-adatkészletek biztonságos hozzáférést, és lehetővé teszi a létrehozását és kezelését, az adatkészletek a munkaterületen.

Ez a témakör nyújt útmutatást:

* a Machine Learning Python ügyfélkönyvtárat telepítése 
* eléréséhez, és töltse fel az adatkészletek, többek között a útmutatást találhat a helyi Python-környezetből az Azure Machine Learning adatkészletek hozzáférési beszerzése
* a kísérletek köztes adatkészletek elérése
* a Python ügyféloldali kódtár használatával adatkészletek számbavétele, hozzáfér, olvassa el a tartalmát egy adatkészletet, új adatkészleteket hoz létre és frissítheti a meglévő adatkészletek

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="prerequisites"></a>Előfeltételek
A Python ügyféloldali kódtár tesztelve lett a következő környezetekben alatt:

* Windows, Mac és Linux rendszereken
* Python 2.7-es, 3.3 és 3.4

Ez maga a következő csomagok:

* kérelem
* Python-dateutil
* pandas

Javasoljuk a Python elosztási például [Anaconda](http://continuum.io/downloads#all) vagy [lombkoronaszint](https://store.enthought.com/downloads/), amelyek származnak, a Python, az IPython, és a fent felsorolt három csomagokat telepíti. Bár IPython nem szigorúan kötelezőek, kezelésére és az adatok interaktív megjelenítése egy kiváló környezetet fontos.

### <a name="installation"></a>Az Azure Machine Learning Python ügyfélkönyvtárat telepítése
Az Azure Machine Learning Python ügyfélkönyvtárat is telepíteni kell a témakörben ismertetett feladatok végrehajtásához. Érhető el a [Python-Csomagindexet](https://pypi.python.org/pypi/azureml). Telepítheti a Python-környezetében, futtassa a következő parancsot a helyi Python-környezetben:

    pip install azureml

Másik lehetőségként töltse le és telepítse a forrásokból származó [github](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

    python setup.py install

Ha a git telepítve van a gépén, pip használatával közvetlenül a git-adattárból telepítése:

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


## <a name="datasetAccess"></a>Studio-kódtöredékek használata adatkészletek eléréséhez
A Python ügyféloldali kódtár programozott hozzáférést biztosít a meglévő adatkészletek az adott időpontig futtatott kísérletek.

A Studio webes felületén a kódrészletek, amelyek tartalmazzák a szükséges információkat, töltse le és adatkészletek deszerializálni pandas DataFrame objektumok a hely gépén, is létrehozhat.

### <a name="security"></a>Adatok hozzáférés biztonsága
A kódtöredékek Studio által biztosított, a Python ügyféloldali kódtár segítségével tartalmazza a munkaterület azonosítójára és engedélyezési jogkivonat. Ezek biztosítson teljes hozzáférést a munkaterületét, és védeni kell, például a jelszó.

Biztonsági okokból kód kódrészlet funkciója csak azok állítja be a szerepkörrel rendelkező felhasználók számára elérhető **tulajdonos** a munkaterület számára. A szerepkör megjelenik az Azure Machine Learning Studióban az **felhasználók** lap **beállítások**.

![Biztonság][security]

Ha nincs beállítva az Ön szerepköréhez **tulajdonosa**, vagy lehet újból meghívni tulajdonosként, vagy kérje meg a munkaterület tulajdonosa tudja biztosítani a kódtöredék irányuló kérelem is.

Az engedélyezési jogkivonat beszerzéséhez a következők egyikét teheti:

* Kérje meg a jogkivonat-tulajdonosától. Tulajdonosok saját hitelesítési jogkivonatok hozzáférhet a beállítások lapról a Studio-munkaterület. Válassza ki **beállítások** a bal oldali panelen, majd kattintson a **hitelesítési JOGKIVONATOK** megtekintéséhez az elsődleges és másodlagos jogkivonatokat.  Bár az elsődleges vagy másodlagos hitelesítési jogkivonatok kódrészlet is használható, javasoljuk, hogy a tulajdonosok csak megosztani a másodlagos hitelesítési jogkivonatokat.

![Hitelesítési jogkivonatok](./media/python-data-access/ml-python-access-settings-tokens.png)

* Kérje meg a tulajdonosi szerepkörhöz, hogy támogatni.  Ehhez a munkaterülethez jelenlegi tulajdonosának kell először távolítsa el, a munkaterülethez, majd újból meghívni, azt tulajdonosai.

Miután fejlesztők beszerezte a munkaterület-azonosítót és engedélyezési jogkivonat, azok érhetik el a munkaterületet, függetlenül azok szerepét a kódrészlet használatával.

A hitelesítési tokenek felügyelt a **hitelesítési JOGKIVONATOK** lap **beállítások**. Létrehozhatja őket, de ezt az eljárást az előző jogkivonat való hozzáférés visszavonása.

### <a name="accessingDatasets"></a>Hozzáférés az adathalmazokhoz Python-alkalmazás helyi
1. A Machine Learning Studióban, kattintson a **ADATKÉSZLETEK** a bal oldali navigációs sávban.
2. Válassza ki az elérni kívánt adatkészlethez. Kiválaszthatja az adatkészletekhez a **saját ADATKÉSZLETEK** lista vagy a **minták** listája.
3. Kattintson az alsó eszköztáron **adat-hozzáférési kód előállítása**. Ha az adatok formátuma nem kompatibilis a Python ügyféloldali kódtár, ez a gomb le van tiltva.
   
    ![Adathalmazok][datasets]
4. Válassza ki a kódtöredék a ablakban jelenik meg, és másolja a vágólapra.
   
    ![Hozzáférési kód][dataset-access-code]
5. Illessze be a kódot a notebookot a helyi Python-alkalmazás.
   
    ![Notebook][ipython-dataset]

## <a name="accessingIntermediateDatasets"></a>A Machine Learning-kísérletek köztes adatkészletek elérése
Kísérlet a Machine Learning Studio futtatása után is lehet a köztes adatkészletek elérése a modulok kimeneti csomópontjából. Az adatkészletek köztes olyan adatok, amelyek által létrehozott és használt köztes lépések egy modell eszköz futtatásakor.

Az adatkészletek köztes mindaddig, amíg az adatok formátuma kompatibilis a Python ügyféloldali kódtár érhető el.

A következő formátumok támogatottak (Ezek állandók szerepelnek a `azureml.DataTypeIds` osztály):

* Egyszerű szöveg
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

Megadhatja, hogy a formátum az egérmutatót egy modul kimeneti csomópontot. A csomópont nevét, elemleírásokban együtt jelenik meg.

Egyes modulok, például a [Split] [ split] modul kimeneti nevű formátumra `Dataset`, amely nem támogatja a Python ügyféloldali kódtár.

![Adatkészlet-formátum][dataset-format]

Például használja egy átalakítás modult kell [átalakítás fürt megosztott Kötetévé][convert-to-csv]beolvasásához a kimenetet egy támogatott formátumra.

![GenericCSV formátuma][csv-format]

A következő lépések bemutatják egy példa, amely létrehoz egy kísérletet, futtatja és a köztes adatkészlethez hozzáfér.

1. Új kísérlet létrehozásához.
2. Helyezze be egy **bináris osztályozási felnőtt népszámlálási jövedelem adatkészlet** modul.
3. Helyezze be egy [Split] [ split] modult, és csatlakoztassa a bemeneti adatkészlet modul kimenetével.
4. Helyezze be a [átalakítás fürt megosztott Kötetévé] [ convert-to-csv] modul, és csatlakozzon az egyik a bemeneti a [Split] [ split] modul kimenete.
5. Mentse a kísérletet, futtassa, és várjon, amíg a futása befejeződik.
6. Kattintson a kimeneti csomópont a a [átalakítás fürt megosztott Kötetévé] [ convert-to-csv] modul.
7. Amikor a helyi menü megjelenik, válassza ki a **adat-hozzáférési kód előállítása**.
   
    ![Helyi menü][experiment]
8. Válassza ki a kódrészletet, és a megjelenő ablakban másolja a vágólapra.
   
    ![Hozzáférési kód][intermediate-dataset-access-code]
9. Illessze be a kódot a notebookot.
   
    ![Notebook][ipython-intermediate-dataset]
10. Az adatok matplotlib használatával jelenítheti meg. Ez az életkor oszlop hisztogramja jeleníti meg:
    
    ![Hisztogram][ipython-histogram]

## <a name="clientApis"></a>A Machine Learning Python ügyféloldali kódtár használatával eléréséhez, olvassa el, hozzon létre és adatkészletek kezelése
### <a name="workspace"></a>Munkaterület
A munkaterület a Python ügyféloldali kódtár a belépési pont. Adja meg a `Workspace` osztály a munkaterület azonosítójára és engedélyezési jogkivonat-példányt létrehozni:

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>Az adatkészletek számbavétele
Egy adott munkaterület összes adatkészlete enumerálása:

    for ds in ws.datasets:
        print(ds.name)

Csak a felhasználó által létrehozott adatkészletek enumerálása:

    for ds in ws.user_datasets:
        print(ds.name)

Csupán a példa adatkészletek enumerálása:

    for ds in ws.example_datasets:
        print(ds.name)

Adatkészlet neve (amely a kis-és nagybetűket) érhető el:

    ds = ws.datasets['my dataset name']

És az index által érhessék el:

    ds = ws.datasets[0]


### <a name="metadata"></a>Metaadatok
Az adathalmazban metaadatok, a tartalom mellett. (Az adatkészletek köztes-e szabály alól, és nem rendelkezik az összes metaadat.)

Néhány metaadatértékeket a felhasználó által hozzárendelt létrehozáskor:

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

Mások az Azure gépi tanulás által hozzárendelt értékek a következők:

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Tekintse meg a `SourceDataset` további információk a rendelkezésre álló metaadatok osztály.

### <a name="read-contents"></a>Tartalom olvasása
A Machine Learning Studio által biztosított automatikusan kódtöredékek töltse le, és az adatkészlet egy pandas DataFrame objektum deszerializálása. Ez a lépés a `to_dataframe` módszer:

    frame = ds.to_dataframe()

Ha inkább a nyers adatok letöltése, és hajtsa végre a deszerializálás, saját magának, lehetőség, amely. Jelenleg ez a lehetőség csak formátumokhoz, például a "ARFF", amelyek deszerializálása nem lehetséges, a Python ügyféloldali kódtárral.

A tartalom olvasása szövegként:

    text_data = ds.read_as_text()

A bináris típusúként, olvassa el a tartalmat:

    binary_data = ds.read_as_binary()

Nyissa meg a stream tartalmát is:

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>Hozzon létre egy új adatkészlet
A Python ügyféloldali kódtár lehetővé teszi, hogy a Python program származó adatkészletek feltöltése. Ezek az adatkészletek majd érhetők el használatra a munkaterületén.

Ha az adatok egy pandas DataFrame rendelkezik, használja a következő kódot:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Ha az adatok már szerializált, akkor használhatja:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

A Python ügyféloldali kódtár tudja szerializálni az alábbi formátumok pandas DataFrame (Ezek állandók szerepelnek a `azureml.DataTypeIds` osztály):

* Egyszerű szöveg
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>Egy meglévő adatkészlet frissítése
Töltse fel egy új adatkészletet, amelynek neve megegyezik egy meglévő adatkészlet meg, ha egy ütköző hiba szerezheti be.

Egy meglévő adatkészlet frissítéséhez először kell beolvasni a meglévő adathalmazt hivatkozást:

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Ezután `update_from_dataframe` szerializálható, és cserélje ki annak tartalmát az Azure-beli adatkészlet:

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Ha meg szeretné szerializálni az adatokat egy másik formátumba, adjon meg egy értéket, a választható `data_type_id` paraméter.

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Igény szerint beállíthatja az új leírást adjon meg egy értéket a `description` paraméter.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

Igény szerint beállíthatja az új nevet adjon meg egy értéket a `name` paraméter. Mostantól fogja beolvasni a az adatkészlet csak az új név használatával. Az alábbi kód frissíti az adatokat, nevét és leírását.

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

A `data_type_id`, `name` és `description` paraméterek megadása nem kötelező, és alapértelmezés szerint a korábbi értéküket. A `dataframe` paraméter mindig szükség.

Ha az adatok már szerializált, `update_from_raw_data` helyett `update_from_dataframe`. Ha csak át kell adnia a `raw_data` helyett `dataframe`, hasonló módon működik.

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

