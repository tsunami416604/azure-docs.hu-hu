---
title: Adattárolók az adatok elérése / blobok képzéshez
titleSuffix: Azure Machine Learning service
description: Adattárolók használata közben való hozzáféréséhez adatok blobtárolóban képzés az Azure Machine Learning szolgáltatással
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: 410867ae034309db0bb013ae22f90e8489aa463e
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56886111"
---
# <a name="access-data-from-your-datastores"></a>Az adattárolók érheti el adatait
Ebből a cikkből megtudhatja, elérheti és kezelheti az Azure Machine Learning-munkafolyamatokban adattárainak keresztül az adatok különböző módjait.

Ebben az útmutatóban példát a következő feladatokhoz: 
* Hozzon létre, és a egy adattár eléréséhez
* [Töltse fel és adattárainak adatok letöltése](#upload-and-download-data)
* [Hozzáférés adattárolója képzéshez](#access-datastores-for-training)

<a name="access"></a>

## <a name="create-and-access-a-datastore"></a>Hozzon létre, és a egy adattár eléréséhez
Adattárolók használatához szüksége lesz egy [munkaterület](concept-azure-machine-learning-architecture.md#workspace). Első lépésként vagy [új munkaterület létrehozása](quickstart-create-workspace-with-python.md) vagy egy meglévő beolvasása:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

### <a name="use-the-default-datastore"></a>Használja az alapértelmezett adattárhoz
Nincs szükség létrehozása vagy konfigurálása egy storage-fiókot.  Az egyes munkaterületeken használatának azonnali megkezdéséhez alapértelmezett adattárolót tartalmaz.

A munkaterület alapértelmezett adattárolója lekérése:
```Python
ds = ws.get_default_datastore()
```

### <a name="register-a-datastore"></a>Egy adattár regisztrálása
Ha rendelkezik meglévő Azure Storage, regisztrálhatja adattárolót, a munkaterületen.  A rendszer a register-módszerek a [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) osztályt, és rendelkezik az űrlap register_azure_ *. Az alábbi példák bemutatják, hogy egy Azure Blob-tároló és az Azure-fájlmegosztás regisztrálásához adattárolót.

#### <a name="azure-blob-container-datastore"></a>Az Azure Blob-tároló adattár
Egy Azure Blob-tároló adattár regisztrálásához használja [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-:)

```Python
ds = Datastore.register_azure_blob_container(workspace=ws, 
                                             datastore_name='your datastore name', 
                                             container_name='your azure blob container name',
                                             account_name='your storage account name', 
                                             account_key='your storage account key',
                                             create_if_not_exists=True)
```

#### <a name="azure-file-share-datastore"></a>Azure-beli fájlmegosztás adattárhoz
Egy Azure-fájlmegosztás adattárolója regisztrálásához használja [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)

```Python
ds = Datastore.register_azure_file_share(workspace=ws, 
                                         datastore_name='your datastore name', 
                                         container_name='your file share name',
                                         account_name='your storage account name', 
                                         account_key='your storage account key',
                                         create_if_not_exists=True)
```

### <a name="get-an-existing-datastore"></a>Egy meglévő adattárhoz beolvasása
A [ `get()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) név alapján egy már regisztrált adattárolója metódus lekérdezéseket:

```Python
ds = Datastore.get(ws, datastore_name='your datastore name')
```

A munkaterület összes állomásához is használhatja:

```Python
datastores = ws.datastores
for name, ds in datastores.items():
    print(name, ds.datastore_type)
```

Az egyszerűség kedvéért [ `set_default_datastore()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) állítja be a munkaterület az alapértelmezett adattárhoz bármelyik adattárolója választja:

```Python
ws.set_default_datastore('your datastore name')
```

## <a name="upload-and-download-data"></a>Adatok le- és feltöltése
### <a name="upload"></a>Feltöltés
Töltse fel az adattárhoz, a Python SDK-val vagy a könyvtár, vagy az egyes fájlok.

Egy könyvtár tölthet fel egy adattár `ds`:

```Python
ds.upload(src_dir='your source directory',
          target_path='your target path',
          overwrite=True,
          show_progress=True)
```
`target_path` a helyét adja meg a feltöltendő fájlmegosztást (vagy blob-tároló). A rendszer alapértelmezés szerint `None`, ebben az esetben az adatfeltöltés lekérdezi a legfelső szintű. `overwrite=True` felülírja a meglévő adatokat `target_path`.

Vagy töltse fel az egyes fájlok listáját az adattárhoz az adattár keresztül `upload_files()` metódust.

### <a name="download"></a>Letöltés
Ehhez hasonlóan adatokat letölteni a adattárolót a helyi fájlrendszerbe.

```Python
ds.download(target_path='your target path',
            prefix='your prefix',
            show_progress=True)
```
`target_path` az a hely, az adatok letöltése a helyi könyvtárban. Adja meg a mappa elérési útját a letöltéséhez fájlmegosztást (vagy blob-tárolóba), adja meg, hogy az elérési út `prefix`. Ha `prefix` van `None`, a fájlmegosztást (vagy a blob-tároló) teljes tartalmát fogja letöltése.

## <a name="access-datastores-for-training"></a>Hozzáférés adattárainak képzéshez
Egy adattár (például a képzés és az érvényesítési adatok) futtat egy távoli számítási célnak keresztül a Python SDK használatával egy betanítás során érheti el a [ `DataReference` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) osztály.

Ahhoz, hogy az adattár elérhető a távoli számítási két támogatott módja van:
* **Csatlakoztatási**  

    * [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), a csatlakozási mód megadásával, az adattárral lekérdezi csatlakoztatott, a távoli számítási.

    ```Python
    import azureml.data
    from azureml.data import DataReference

    ds.as_mount()
    ```

* **Letöltés és feltöltése**  
    * [`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), letölti a adatokat a megadott helyen `path_on_compute` a távoli számítási az adattárban.

    * [`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), feltölti az adatokat az adattár gyökérkönyvtárába a megadott helyen `path_on_compute`

    ```Python
    ds.as_download(path_on_compute='your path on compute')
    ds.as_upload(path_on_compute='yourfilename')
    ```   

Egy adott mappa vagy fájl az adattár a hivatkozik, használja az adattárhoz [ `path()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-) függvény.

```Python
#download the contents of the `./bar` directory from the datastore 
ds.path('./bar').as_download()
```
Bármely `ds` vagy `ds.path` objektumra mutat egy környezeti változó neve, a formátum `"$AZUREML_DATAREFERENCE_XXXX"` amelynek értéke a csatlakoztatási/letöltési út távoli számítási jelöli. Az adattároló elérési út távoli számítási nem lehet ugyanaz, mint a szkript végrehajtási útvonalát.

Szeretné elérni az adattárhoz betanítás során, át azt a tanítási szkriptet keresztül parancssori argumentumként `script_params` származó a [ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) osztály:

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': ds.as_mount()
}

est = Estimator(source_directory='your code directory',
                script_params=script_params,
                compute_target=compute_target,
                entry_script='train.py')
```
`as_mount()` egy adattár, alapértelmezett módja van, így közvetlenül is helyettesíthető `ds` , a `'--data_dir'` argumentum.

Vagy adattárainak listáját adja át a Estimator konstruktor `inputs` csatlakoztatni vagy és- tárolókról a datastore(s) másolása paramétert

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```

Az előző példakód:

* Letölti az adattár összes tartalom `ds1` , mielőtt a tanítási szkriptet a távoli számítási `train.py` futtatása

* A mappa letölti `'./foo'` az adattárhoz `ds2` , mielőtt a távoli számítási `train.py` futtatása

* Feltölti a fájlt `'./bar.pkl'` , akár az adattárhoz távoli számítási `d3` a szkript futtatása után

## <a name="next-steps"></a>További lépések

* [A modell tanítása](how-to-train-ml-models.md)
* [Modell üzembe helyezése](how-to-deploy-and-where.md)

