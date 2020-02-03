---
title: Hozzáférés az adathalmazokhoz Python ügyféloldali kódtár – csoportos adatelemzési folyamat
description: Telepítheti és használhatja a Python ügyféloldali kódtár eléréséhez, és az Azure Machine Learning adatok biztonságos kezelésére egy helyi Python-környezetet.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 93ec5e740ac6acf9420a9d980092ed772ac1618e
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720979"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Hozzáférés az adathalmazokhoz Python segítségével, az Azure Machine Learning Python ügyfélkönyvtárat használva
Az előzetes verzióját a Microsoft Azure Machine Learning Python ügyfélkönyvtárat engedélyezheti a helyi Python-környezetben az Azure Machine Learning-adatkészletek biztonságos hozzáférést, és lehetővé teszi a létrehozását és kezelését, az adatkészletek a munkaterületen.

Ez a témakör nyújt útmutatást:

* a Machine Learning Python ügyfélkönyvtárat telepítése
* eléréséhez, és töltse fel az adatkészletek, többek között a útmutatást találhat a helyi Python-környezetből az Azure Machine Learning adatkészletek hozzáférési beszerzése
* a kísérletek köztes adatkészletek elérése
* a Python ügyféloldali kódtár használatával enumerálhatja az adatkészleteket, elérheti a metaadatokat, beolvashatja az adatkészlet tartalmát, új adatkészleteket hozhat létre, és frissítheti a meglévő adatkészleteket.

## <a name="prerequisites"></a>Előfeltételek
A Python ügyféloldali kódtár tesztelve lett a következő környezetekben alatt:

* Windows, Mac és Linux
* Python 2.7-es, 3.3 és 3.4

Ez maga a következő csomagok:

* kérelmek
* Python-dateutil
* pandas

Javasoljuk, hogy olyan Python-disztribúciót használjon, mint például a [anaconda](http://continuum.io/downloads#all) vagy a [lombkorona](https://store.enthought.com/downloads/), amely a Pythonhoz, a IPython-hoz és a fent felsorolt három csomaghoz készült. Bár IPython nem szigorúan kötelezőek, kezelésére és az adatok interaktív megjelenítése egy kiváló környezetet fontos.

### <a name="installation"></a>A Azure Machine Learning Python ügyféloldali kódtár telepítése
A jelen témakörben ismertetett feladatok végrehajtásához telepítse a Azure Machine Learning Python ügyféloldali kódtárat. Ez a könyvtár a Python- [csomag indexében](https://pypi.python.org/pypi/azureml)érhető el. Telepítheti a Python-környezetében, futtassa a következő parancsot a helyi Python-környezetben:

    pip install azureml

Azt is megteheti, hogy letölti és telepíti a [githubon](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python)található forrásokból.

    python setup.py install

Ha a git telepítve van a gépén, pip használatával közvetlenül a git-adattárból telepítése:

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


## <a name="datasetAccess"></a>Kódrészletek használata az adatkészletek eléréséhez
A Python ügyféloldali kódtár programozott hozzáférést biztosít a meglévő adatkészletek az adott időpontig futtatott kísérletek.

A Azure Machine Learning Studio (klasszikus) webes felületen létrehozhat kódrészleteket, amelyek tartalmazzák az összes szükséges információt, amelyekkel letöltheti és deszerializálhatja az adatkészleteket a helyi gépen lévő pandák DataFrame-objektumokként.

### <a name="security"></a>Adathozzáférés biztonsága
A Python ügyféloldali kódtár számára a Azure Machine Learning Studio (klasszikus) által biztosított kódrészletek tartalmazzák a munkaterület-azonosítót és az engedélyezési jogkivonatot. Ezek biztosítson teljes hozzáférést a munkaterületét, és védeni kell, például a jelszó.

Biztonsági okokból a kódrészlet funkció csak olyan felhasználók számára érhető el, akiknek a szerepkörük **tulajdonosként** van beállítva a munkaterületen. A szerepkör a **felhasználók** lapon, a **Beállítások**területen jelenik meg Azure Machine learning Studio (klasszikus).

![Biztonság][security]

Ha a szerepkör nem **tulajdonosként**van beállítva, kérheti, hogy a rendszer visszahívja tulajdonosként, vagy kérje meg a munkaterület tulajdonosát, hogy megadja a kódrészletet.

Az engedélyezési jogkivonat beszerzéséhez válasszon egyet a következő lehetőségek közül:

* Kérje meg a jogkivonat-tulajdonosától. A tulajdonosok a Azure Machine Learning Studio (klasszikus) munkaterületének beállítások lapján érhetik el az engedélyezési jogkivonatokat. A bal oldali ablaktáblában válassza a **Beállítások** lehetőséget, majd kattintson az **engedélyezési tokenek** elemre az elsődleges és másodlagos tokenek megtekintéséhez. Bár az elsődleges vagy másodlagos hitelesítési jogkivonatok kódrészlet is használható, javasoljuk, hogy a tulajdonosok csak megosztani a másodlagos hitelesítési jogkivonatokat.

   ![Hitelesítési jogkivonatok](./media/python-data-access/ml-python-access-settings-tokens.png)

* Kérje, hogy népszerűsítse a tulajdonos szerepkörét: a munkaterület aktuális tulajdonosának először el kell távolítania a munkaterületről, majd újra meg kell hívnia a tulajdonosként.

Ha a fejlesztők megszerezték a munkaterület-azonosítót és az engedélyezési jogkivonatot, akkor a szerepkörtől függetlenül hozzáférhetnek a munkaterülethez a kódrészlet használatával.

Az engedélyezési jogkivonatok kezelése az **engedélyezési JOGkivonatok** lapon, a **Beállítások**területen történik. Létrehozhatja őket, de ezt az eljárást az előző jogkivonat való hozzáférés visszavonása.

### <a name="accessingDatasets"></a>Adatkészletek elérése helyi Python-alkalmazásból
1. Machine Learning Studio (klasszikus) területen kattintson a bal oldali navigációs sávban található **adatkészletek** elemre.
2. Válassza ki az elérni kívánt adatkészlethez. Bármelyik adatkészletet kiválaszthatja a **saját ADATkészletek** listából vagy a **minták** listából.
3. Az alsó eszköztáron kattintson az **adatelérési kód előállítása**elemre. Ha az adatok formátuma nem kompatibilis a Python ügyféloldali kódtár, ez a gomb le van tiltva.
   
    ![Adatkészletek][datasets]
4. Válassza ki a kódtöredék a ablakban jelenik meg, és másolja a vágólapra.
   
    ![Létrehozás adatok hozzáférési kód gomb][dataset-access-code]
5. Illessze be a kódot a notebookot a helyi Python-alkalmazás.
   
    ![Illessze be a notebook kódot][ipython-dataset]

## <a name="accessingIntermediateDatasets"></a>A köztes adatkészletek elérése Machine Learning kísérletekből
Machine Learning Studio (klasszikus) kísérlet után a közbenső adatkészletek a modulok kimeneti csomópontjairól is elérhetők. Az adatkészletek köztes olyan adatok, amelyek által létrehozott és használt köztes lépések egy modell eszköz futtatásakor.

Az adatkészletek köztes mindaddig, amíg az adatok formátuma kompatibilis a Python ügyféloldali kódtár érhető el.

A következő formátumok támogatottak (a formátumok állandói a `azureml.DataTypeIds` osztályban találhatók):

* Egyszerű szöveg
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

Megadhatja, hogy a formátum az egérmutatót egy modul kimeneti csomópontot. A csomópont nevét, elemleírásokban együtt jelenik meg.

Néhány modul, például a [felosztott][split] modul, kimenete `Dataset`nevű formátumra, amelyet a Python ügyféloldali kódtár nem támogat.

![Adatkészlet-formátum][dataset-format]

Egy konverziós modult kell használnia, például a CSV-re történő [konvertálást][convert-to-csv], hogy a kimenet támogatott formátumú legyen.

![GenericCSV formátuma][csv-format]

A következő lépések bemutatják egy példa, amely létrehoz egy kísérletet, futtatja és a köztes adatkészlethez hozzáfér.

1. Új kísérlet létrehozásához.
2. Helyezzen be egy **felnőtt népszámlálás-bevétel bináris besorolási adatkészlet** modulját.
3. Szúrjon be egy [felosztott][split] modult, és csatlakoztassa a bemenetét az adatkészlet moduljának kimenetéhez.
4. Helyezzen be egy [Konvertálás CSV][convert-to-csv] -modulba, és csatlakoztassa a bemenetét az egyik [felosztott][split] modul kimenetéhez.
5. Mentse a kísérletet, futtassa, majd várjon, amíg a feladatok befejeződik.
6. Kattintson a kimenet csomópontra az [átalakítás CSV-][convert-to-csv] modulba.
7. Amikor megjelenik a helyi menü, válassza az **adatelérési kód létrehozása**lehetőséget.
   
    ![Helyi menü][experiment]
8. Válassza ki a kódrészletet, és a megjelenő ablakban másolja a vágólapra.
   
    ![Helyi menüből a hozzáférési kód előállítása][intermediate-dataset-access-code]
9. Illessze be a kódot a notebookot.
   
    ![Illessze be a jegyzetfüzet kódot][ipython-intermediate-dataset]
10. Az adatok matplotlib használatával jelenítheti meg. Ez az életkor oszlop hisztogramja jeleníti meg:
    
    ![Hisztogram][ipython-histogram]

## <a name="clientApis"></a>Adatkészletek elérése, olvasása, létrehozása és kezelése a Machine Learning Python ügyféloldali kódtár használatával
### <a name="workspace"></a>Munkaterület
A munkaterület a Python ügyféloldali kódtár a belépési pont. Példány létrehozásához adja meg a `Workspace` osztályt a munkaterület-AZONOSÍTÓval és az engedélyezési jogkivonattal:

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

A rendelkezésre álló metaadatokkal kapcsolatos további információkért tekintse meg a `SourceDataset` osztályt.

### <a name="read-contents"></a>Tartalom olvasása
A Machine Learning Studio (klasszikus) által megadott kódrészletek automatikusan letöltik és deszerializálják az adatkészletet egy Panda DataFrame objektumba. Ez a `to_dataframe` metódussal történik:

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

A Python ügyféloldali kódtár a következő formátumokba tudja szerializálni a pandák DataFrame (az állandók a `azureml.DataTypeIds` osztályban találhatók):

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

Ezután a `update_from_dataframe` használatával szerializálhatja és lecserélheti az adatkészlet tartalmát az Azure-ban:

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Ha más formátumba kívánja szerializálni az adattípust, akkor a választható `data_type_id` paraméter értékét is meg kell adni.

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

A `description` paraméter értékének megadásával megadhat egy új leírást.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

A `name` paraméter értékének megadásával új nevet is beállíthat. Mostantól fogja beolvasni a az adatkészlet csak az új név használatával. A következő kód frissíti az adathalmazt, a nevet és a leírást.

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

A `data_type_id`, `name` és `description` paraméterek nem kötelezőek, és alapértelmezés szerint az előző értékük. A `dataframe` paramétert mindig kötelező megadni.

Ha az adatai már szerializálva vannak, a `update_from_dataframe`helyett használja a `update_from_raw_data`. Ha `dataframe`helyett csak `raw_data`, akkor hasonló módon működik.

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

