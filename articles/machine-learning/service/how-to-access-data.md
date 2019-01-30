---
title: Hozzáférés az adatokhoz a adattárainak képzéshez
titleSuffix: Azure Machine Learning service
description: Az Azure Machine Learning szolgáltatás betanítás során adatokat tároló elérése érdekében adattárainak használata
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: a4960f3e29011948ec30fbc24222d8a6dccf6b8a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55252115"
---
# <a name="access-data-during-training-from-your-datastores"></a>Az adattárolók a betanítás során érheti el adatait
Egy adattár segítségével elérheti és kezelheti az Azure Machine Learning-munkafolyamatok adatait.

Az Azure Machine Learning szolgáltatásban, az adattárral absztrakciós felett [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction). Az adattároló hivatkozhat, vagy egy [Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) tároló vagy [Azure-fájlmegosztás](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) az alapul szolgáló tárolóként. 

## <a name="create-a-datastore"></a>Egy adattár létrehozása
Adattárolók használatához szüksége lesz egy [munkaterület](concept-azure-machine-learning-architecture.md#workspace). Első lépésként vagy [új munkaterület létrehozása](quickstart-create-workspace-with-python.md) vagy egy meglévő beolvasása:

```Python
import azureml.core
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="use-the-default-datastore"></a>Használja az alapértelmezett adattárhoz
Nincs szükség létrehozása vagy konfigurálása egy storage-fiókot.  Az egyes munkaterületeken használatának azonnali megkezdéséhez alapértelmezett adattárolót tartalmaz.

A munkaterület alapértelmezett adattárolója lekérése:
```Python
ds = ws.get_default_datastore()
```

### <a name="register-a-datastore"></a>Egy adattár regisztrálása
Ha rendelkezik meglévő Azure Storage, regisztrálhatja adattárolót, a munkaterületen. Egy adattár, regisztrálhat az Azure Blob-tároló vagy az Azure-fájlmegosztás. A rendszer a register-módszerek a `Datastore` osztályt, és hogy az űrlap `register_azure_*`.

#### <a name="azure-blob-container-datastore"></a>Az Azure Blob-tároló adattár
Egy Azure Blob-tároló adattár regisztrálása:

```Python
ds = Datastore.register_azure_blob_container(workspace=ws, 
                                             datastore_name='your datastore name', 
                                             container_name='your azure blob container name',
                                             account_name='your storage account name', 
                                             account_key='your storage account key',
                                             create_if_not_exists=True)
```

#### <a name="azure-file-share-datastore"></a>Azure-beli fájlmegosztás adattárhoz
Egy Azure-fájlmegosztás adattárolója regisztrálása:

```Python
ds = Datastore.register_azure_file_share(workspace=ws, 
                                         datastore_name='your datastore name', 
                                         container_name='your file share name',
                                         account_name='your storage account name', 
                                         account_key='your storage account key',
                                         create_if_not_exists=True)
```

### <a name="get-an-existing-datastore"></a>Egy meglévő adattárhoz beolvasása
Lekérdezni a regisztrált adattár neve:
```Python
ds = Datastore.get(ws, datastore_name='your datastore name')
```

A munkaterület összes állomásához is használhatja:
```Python
datastores = ws.datastores()
for name, ds in datastores.items(),
    print(name, ds.datastore_type)
```

Az egyszerűség kedvéért állítja be a regisztrált adattárainak egyikét a munkaterület az alapértelmezett adattárhoz:
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
Egy adattár során-képzés (például a képzés és az érvényesítési adatok) futtat egy távoli számítási célnak a Python SDK-n keresztül érheti el. 

Ahhoz, hogy az adattár elérhető a távoli számítási két támogatott módja van:
* **Csatlakoztatási**  
`ds.as_mount()`: a csatlakozási mód megadásával az adattárral fogja csatlakozik az Ön számára távoli számítási. 
* **Letöltés és feltöltése**  
    * `ds.as_download(path_on_compute='your path on compute')` adatokat tölt le az adattár a megadott helyen távoli számítási `path_on_compute`.
    * `ds.as_upload(path_on_compute='yourfilename'` feltölti az adatokat az adattárral.  Tegyük fel, hogy a tanítási szkriptet hoz létre egy `foo.pkl` távoli számítási fájlt az aktuális munkakönyvtár. Ez a fájl feltöltése a rendelkező adattárolóját `ds.as_upload(path_on_compute='./foo.pkl')` Miután a parancsfájl létrehozza a fájlt. A fájlt az adattár gyökérkönyvtárában van feltöltve.
    
Egy adott mappa vagy fájl az adattár a hivatkozik, használja az adattárhoz **`path`** függvény. Töltse le a tartalmát, például a `./bar` könyvtárat abból az adattárhoz, a számítási célnak, használati `ds.path('./bar').as_download()`.

Bármely `ds` vagy `ds.path` objektumra mutat egy környezeti változó neve, a formátum `"$AZUREML_DATAREFERENCE_XXXX"` amelynek értéke a csatlakoztatási/letöltési út távoli számítási jelöli. Az adattároló elérési út távoli számítási nem lehet ugyanaz, mint a szkript végrehajtási útvonalát.

Szeretné elérni az adattárhoz betanítás során, át azt a tanítási szkriptet keresztül parancssori argumentumként `script_params`:

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
`as_mount()` egy adattár, alapértelmezett módja van, így is közvetlenül csak helyettesíthető `ds` , a `'--data_dir'` argumentum.

Vagy adattárainak listáját adja át a Estimator konstruktor `inputs` csatlakoztatni vagy és- tárolókról a datastore(s) másolása paramétert:

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```
A fenti kódot fogja végrehajtani:
* az adattár összes a tartalom letöltéséhez `ds1` , mielőtt a tanítási szkriptet a távoli számítási `train.py` futtatása
* Töltse le a mappa `'./foo'` az adattárhoz `ds2` , mielőtt a távoli számítási `train.py` futtatása
* a fájl feltöltése `'./bar.pkl'` , akár az adattárhoz távoli számítási `d3` a szkript futtatása után

## <a name="next-steps"></a>További lépések
* [A modell tanítása](how-to-train-ml-models.md)
