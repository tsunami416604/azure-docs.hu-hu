---
title: Adatkészletek elérése Python-ügyféltárral – Csapatadatelemzési folyamat
description: Telepítse és használja a Python-ügyfélkönyvtárat az Azure Machine Learning-adatok biztonságos eléréséhez és kezeléséhez egy helyi Python-környezetből.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720979"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Hozzáférés az adathalmazokhoz Python segítségével, az Azure Machine Learning Python ügyfélkönyvtárat használva
A Microsoft Azure Machine Learning Python-ügyfélkódtár előzetes verziója biztonságos hozzáférést biztosít az Azure Machine Learning-adatkészletekhez egy helyi Python-környezetből, és lehetővé teszi az adatkészletek létrehozását és kezelését egy munkaterületen.

Ez a témakör a következőket ismerteti:

* a Machine Learning Python-ügyfélkönyvtár telepítése
* adatkészletek elérése és feltöltése, beleértve az Azure Machine Learning-adatkészletek elérésére vonatkozó engedély betöltésére vonatkozó utasításokat a helyi Python-környezetből
* köztes adatkészletek elérése kísérletekből
* a Python-ügyfélkódtár használatával felsorolják az adatkészleteket, hozzáférnek a metaadatokhoz, beolvassák egy adatkészlet tartalmát, új adatkészleteket hoznak létre, és frissíthetik a meglévő adatkészleteket

## <a name="prerequisites"></a><a name="prerequisites"></a>Előfeltételek
A Python-ügyfélkódtár tesztelése a következő környezetekben történt:

* Windows, Mac és Linux
* Python 2.7, 3.3 és 3.4

A következő csomagoktól függ:

* Kérelmek
* python-dateutil
* Pandák

Javasoljuk, hogy egy Python-disztribúció, például [anaconda](http://continuum.io/downloads#all) vagy [canopy,](https://store.enthought.com/downloads/)amely a Python, IPython és a fent felsorolt három csomag telepítve. Bár az IPython nem feltétlenül szükséges, nagyszerű környezet az adatok interaktív manipulálására és megjelenítésére.

### <a name="how-to-install-the-azure-machine-learning-python-client-library"></a><a name="installation"></a>Az Azure Machine Learning Python-ügyfélkönyvtár telepítése
Telepítse az Azure Machine Learning Python-ügyfélkönyvtárat a témakörben ismertetett feladatok elvégzéséhez. Ez a könyvtár a [Python csomagindexből](https://pypi.python.org/pypi/azureml)érhető el. A Python-környezetben történő telepítéshez futtassa a következő parancsot a helyi Python-környezetből:

    pip install azureml

Másik lehetőségként letöltheti és telepítheti a Forrásokból a [GitHubon.](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python)

    python setup.py install

Ha git telepítve van a gépre, a pip segítségével közvetlenül a git-tárházból telepítheti:

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


## <a name="use-code-snippets-to-access-datasets"></a><a name="datasetAccess"></a>Kódrészletek használata adatkészletek eléréséhez
A Python-ügyfélkódtár programozott hozzáférést biztosít a meglévő adatkészletekhez a futtatott kísérletekből.

Az Azure Machine Learning Studio (klasszikus) webes felületről olyan kódrészleteket hozhat létre, amelyek tartalmazzák az adatkészletek letöltéséhez és deszerializálásához szükséges összes információt pandaDataFrame-objektumként a helyi gépen.

### <a name="security-for-data-access"></a><a name="security"></a>Az adatokhoz való hozzáférés biztonsága
Az Azure Machine Learning Studio (klasszikus) által a Python-ügyfélkódtárhoz biztosított kódrészletek tartalmazzák a munkaterület-azonosítót és az engedélyezési jogkivonatot. Ezek teljes hozzáférést biztosítanak a munkaterülethez, és védeni kell őket, például egy jelszót.

Biztonsági okokból a kódrészlet funkció csak azok számára érhető el, akik **szerepkör-készlete tulajdonosként** van beállítva a munkaterülethez. A szerepkör az Azure Machine Learning Studio (klasszikus) környezetben jelenik meg a **FELHASZNÁLÓK** lap **Beállítások (Settings)** területén.

![Biztonság][security]

Ha a szerepkör nincs **tulajdonosként**beállítva, kérheti, hogy tulajdonosként újra meghívják, vagy megkérheti a munkaterület tulajdonosát, hogy adja meg a kódrészletet.

Az engedélyezési jogkivonat beszerzéséhez az alábbi lehetőségek közül választhat:

* Kérjen egy tokent egy tulajdonostól. A tulajdonosok az azure Machine Learning Studio (klasszikus) munkaterületének Beállítások lapján érhetik el engedélyezési tokenjaikat. Válassza a **beállítások a** bal oldali ablaktáblában, és kattintson **a ZÁROLÁSI TOKENEK** az elsődleges és másodlagos jogkivonatok megtekintéséhez. Bár az elsődleges vagy a másodlagos engedélyezési jogkivonatok használhatók a kódkódrészletben, ajánlott, hogy a tulajdonosok csak a másodlagos engedélyezési jogkivonatokat osszák meg.

   ![Engedélyezési jogkivonatok](./media/python-data-access/ml-python-access-settings-tokens.png)

* Kérje meg, hogy léptessék elő tulajdonosi szerepkörré: a munkaterület jelenlegi tulajdonosának először el kell távolítania a munkaterületről, majd újra meg kell hívnia, hogy tulajdonosként legyen.

Miután a fejlesztők beszerezték a munkaterület-azonosítót és az engedélyezési jogkivonatot, a kódkódrészlet használatával hozzáférhetnek a munkaterülethez, függetlenül a szerepkörüktől.

Az engedélyezési tokenek kezelése a **BEÁLLÍTÁSOK**csoport **AUTHORIZATION TOKENS** lapján történik. Újragenerálhatja őket, de ez az eljárás visszavonja az előző jogkivonathoz való hozzáférést.

### <a name="access-datasets-from-a-local-python-application"></a><a name="accessingDatasets"></a>Adatkészletek elérése helyi Python-alkalmazásból
1. A Machine Learning Studio (klasszikus) alkalmazásban kattintson a bal oldali navigációs sáv **DATASETS** gombjára.
2. Válassza ki az elérni kívánt adatkészletet. Az adatkészletek közül bármelyiket kiválaszthatja a **SAJÁT ADATKÉSZLETEK** listából vagy a **MINTÁK** listából.
3. Az alsó eszköztáron kattintson az **Adatelérési kód létrehozása gombra.** Ha az adatok a Python-ügyfélkódtárral nem kompatibilis formátumban vannak, ez a gomb le van tiltva.
   
    ![Adathalmazok][datasets]
4. Jelölje ki a megjelenő ablakban a kódrészletet, és másolja a vágólapra.
   
    ![Adatelérési kód létrehozása gomb][dataset-access-code]
5. Illessze be a kódot a helyi Python-alkalmazás jegyzetfüzetébe.
   
    ![Kód beillesztése a jegyzetfüzetbe][ipython-dataset]

## <a name="access-intermediate-datasets-from-machine-learning-experiments"></a><a name="accessingIntermediateDatasets"></a>Köztes adatkészletek elérése Machine Learning-kísérletekből
Miután egy kísérlet fut a Machine Learning Studio (klasszikus), a modulok kimeneti csomópontjaiból is elérhető a köztes adatkészletek. A köztes adatkészletek olyan adatok, amelyeket a modelleszköz futtatásakor hoztak létre és használtak köztes lépésekhez.

A köztes adatkészletek mindaddig elérhetők, amíg az adatformátum kompatibilis a Python ügyfélkódtárral.

A következő formátumok támogatottak (ezeknek a `azureml.DataTypeIds` formátumoknak az állandói az osztályban vannak):

* PlainText
* Generikus CSV
* Generikus
* GenericCSVNoFejléc
* GenericTSVnofejléc

A formátumot úgy határozhatja meg, hogy egy modul kimeneti csomópontjára viszi az egérmutatót. A csomópont nevével együtt jelenik meg egy elemleírásban.

Egyes modulok, például a [Split][split] modul, kimeneti `Dataset`nevű formátumban, amely nem támogatja a Python ügyfélkönyvtár.

![Adatkészlet formátuma][dataset-format]

A kimenet támogatott formátumba való bekerüléséhez konverziós modult kell használnia, például [konvertálás CSV-vé.][convert-to-csv]

![GenericCSV formátum][csv-format]

A következő lépések egy példát mutatnak be, amely létrehoz egy kísérletet, futtatja, és hozzáfér a köztes adatkészlethez.

1. Hozzon létre egy új kísérletet.
2. Felnőtt **népszámlálási jövedelem bináris besorolási adatkészletmodul beszúrása.**
3. [Split][split] modul beszúrása, és a bemenet csatlakoztatása az adatkészlet modul kimenetéhez.
4. Helyezzen be egy [Konvertált CSV-modulra,][convert-to-csv] és csatlakoztassa a bemenetet a [Split][split] modul egyik kimenetéhez.
5. Mentse a kísérletet, futtassa, és várja meg, amíg a feladat befejeződik.
6. Kattintson a kimeneti csomópontra a [Konvertálás CSV-vé][convert-to-csv] modulon.
7. Amikor megjelenik a helyi menü, válassza **az Adatelérési kód generálása lehetőséget.**
   
    ![Helyi menü][experiment]
8. Jelölje ki a kódrészletet, és másolja a vágólapra a megjelenő ablakból.
   
    ![Hozzáférési kód létrehozása a helyi menüből][intermediate-dataset-access-code]
9. Illessze be a kódot a jegyzetfüzetbe.
   
    ![Kód beillesztése a jegyzetfüzetbe][ipython-intermediate-dataset]
10. Az adatokat a matplotlib segítségével jelenítheti meg. Ez a koroszlop hisztogramjában jelenik meg:
    
    ![Hisztogram][ipython-histogram]

## <a name="use-the-machine-learning-python-client-library-to-access-read-create-and-manage-datasets"></a><a name="clientApis"></a>A Machine Learning Python-ügyfélkódtár használatával adatkészletek elérése, olvasása, létrehozása és kezelése
### <a name="workspace"></a>Munkaterület
A munkaterület a Python-ügyfélkódtár belépési pontja. Adja `Workspace` meg az osztálynak a munkaterület-azonosítót és az engedélyezési jogkivonatot egy példány létrehozásához:

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>Adatkészletek számbavétele
Egy adott munkaterület összes adatkészlete számbavétele:

    for ds in ws.datasets:
        print(ds.name)

Csak a felhasználó által létrehozott adatkészletek számbavétele:

    for ds in ws.user_datasets:
        print(ds.name)

Csak a példaadatkészletek számbavétele:

    for ds in ws.example_datasets:
        print(ds.name)

Az adatkészletek név szerint érhetők el (ami nem érinti a kis- és nagybetűket):

    ds = ws.datasets['my dataset name']

Vagy elérheti index:

    ds = ws.datasets[0]


### <a name="metadata"></a>Metaadatok
Az adatkészletek a tartalom mellett metaadatokkal is rendelkeznek. (A köztes adatkészletek kivételt képeznek ez alól a szabály alól, és nem rendelkeznek metaadatokkal.)

A felhasználó a létrehozás időpontjában rendel hozzá néhány metaadat-értéket:

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

Mások az Azure ML által hozzárendelt értékek:

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Az `SourceDataset` elérhető metaadatokról az osztályban további információk állnak rendelkezésre.

### <a name="read-contents"></a>Tartalom olvasása
A Machine Learning Studio (klasszikus) által biztosított kódrészletek automatikusan letöltik és deszerializálják az adatkészletet egy pandadataframe objektumra. Ez a `to_dataframe` következő módszerrel történik:

    frame = ds.to_dataframe()

Ha inkább letölti a nyers adatokat, és saját maga hajtja végre a deszerializálást, ez egy lehetőség. Jelenleg ez az egyetlen lehetőség az olyan formátumokhoz, mint az "ARFF", amelyet a Python ügyfélkódtár nem tud deszerializni.

A tartalom szövegként való olvasása:

    text_data = ds.read_as_text()

A tartalom binárisként való olvasása:

    binary_data = ds.read_as_binary()

Azt is csak nyit egy patak a tartalmát:

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>Új adatkészlet létrehozása
A Python-ügyfélkódtár lehetővé teszi, hogy adatkészleteket töltsön fel a Python-programból. Ezek az adatkészletek ezután a munkaterületen használhatók.

Ha adatait pandas DataFrame-ben használja, használja a következő kódot:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Ha az adatok már szerializáltak, a következőket használhatja:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

A Python ügyfélkönyvtár képes szerializálni a pandák DataFrame a következő `azureml.DataTypeIds` formátumok (állandók ezek az osztály):

* PlainText
* Generikus CSV
* Generikus
* GenericCSVNoFejléc
* GenericTSVnofejléc

### <a name="update-an-existing-dataset"></a>Meglévő adatkészlet frissítése
Ha egy meglévő adatkészletnek megfelelő nevű új adatkészletet próbál feltölteni, ütközési hibát kell kapnia.

Meglévő adatkészlet frissítéséhez először a meglévő adatkészletre kell hivatkoznia:

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Ezután `update_from_dataframe` szerializálhatja és lecserélheti az azure-beli adatkészlet tartalmát:

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Ha az adatokat más formátumba szeretné szerializálni, adjon meg egy értéket a nem kötelező `data_type_id` paraméterhez.

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Új leírást is beállíthat a `description` paraméter értékének megadásával.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

Új nevet tetszés szerint a `name` paraméter értékének megadásával is beállíthat. Mostantól csak az új névvel fogja beolvasni az adatkészletet. A következő kód frissíti az adatokat, a nevet és a leírást.

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

A `data_type_id` `name` és `description` a paraméterek nem kötelezőek, és alapértelmezés szerint a korábbi értékükhöz tartoznak. A `dataframe` paraméter mindig szükséges.

Ha az adatok már szerializáltak, használja `update_from_raw_data` a helyett. `update_from_dataframe` Ha csak át `raw_data` helyett `dataframe`, ez szerkezet -ban egy hasonló út.

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

