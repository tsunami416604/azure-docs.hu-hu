---
title: Adattárolók érheti el adatait az Azure Machine Learning segítségével
description: Betanítás során adatokat tároló elérése érdekében adattárainak használata
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 615ab592c040eedf7d31e3a3036f558ea6c09740
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990807"
---
# <a name="how-to-access-data-during-training"></a>Adatbázisadatok elérése során képzés
Az Azure Machine Learning-szolgáltatások, adattárolót absztrakciós felett [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction). Az adattároló hivatkozhat, vagy egy [Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) tároló vagy [Azure-fájlmegosztás](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) az alapul szolgáló tárolóként. Adattárolók könnyű elérését és a tárhely kezelése során az Azure Machine Learning-munkafolyamatokat a Python SDK-t vagy a CLI-n keresztül teszi lehetővé.

## <a name="create-a-datastore"></a>Egy adattár létrehozása
Adattárolók használatához először egy [munkaterület](concept-azure-machine-learning-architecture.md#workspace). Hozzon létre egy új munkaterületet, vagy egy meglévő lekéréséhez:

```Python
import azureml.core
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="use-the-default-datastore"></a>Használja az alapértelmezett adattárhoz
Az egyes munkaterületeken alapértelmezett adattárolót megkezdheti azonnal, így helyet takaríthat meg a munkát, létrehozását és konfigurálását a saját tárfiókok rendelkezik.

A munkaterület alapértelmezett adattárolója lekérése:
```Python
ds = ws.get_default_datastore()
```

### <a name="register-a-datastore"></a>Egy adattár regisztrálása
Ha már rendelkezik meglévő Azure Storage, regisztrálhatja adattárolót, a munkaterületen. Az Azure Machine Learning egy Azure Blob-tároló vagy az Azure-fájlmegosztás regisztrálása, egy adattár biztosítja. A rendszer a register-módszerek a `Datastore` osztályt, és hogy az űrlap `register_azure_*`.

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
    print(name, ds.datastore_type)"
```

Kényelmi célokat szolgál Ha a regisztrált adattárainak egyikét állítja be a munkaterület az alapértelmezett adattárhoz szeretne megteheti a következő:
```Python
ws.set_default_datastore('your datastore name')
```

## <a name="upload-and-download-data"></a>Adatok le- és feltöltése
### <a name="upload"></a>Feltöltés
Az adattárhoz, a Python SDK-val vagy egy könyvtárat vagy csak egyes fájlokat tölthet fel.

Egy könyvtár tölthet fel egy adattár `ds`:
```Python
ds.upload(src_dir='your source directory',
          target_path='your target path',
          overwrite=True,
          show_progress=True)
```
`target_path` a helyét adja meg a feltöltendő fájlmegosztást (vagy blob-tároló). A rendszer alapértelmezés szerint `None`, ebben az esetben az adatfeltöltés lekérdezi a legfelső szintű. `overwrite=True` felülírja a meglévő adatokat `target_path`.

Azt is megteheti feltöltheti az egyes fájlok listáját az adattárhoz az adattár keresztül `upload_files()` metódust.

### <a name="download"></a>Letöltés
Ehhez hasonlóan letölthető adatokat egy adattár a helyi fájlrendszerbe.

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
    * `ds.as_download(path_on_compute='your path on compute')`: a letöltési mód, az adatok fog első letöltve az az adattár a megadott helyen távoli számítási `path_on_compute`.
    * A képzési adattárolót futtatható a ellentétben által előállított adatok is feltöltheti. Például, ha a tanítási szkriptet hoz létre egy `foo.pkl` fájlt az aktuális munkakönyvtárban távoli számítási, megadhatja a hozzá tartozó első feltöltött az adattárhoz, a szkript futtatása után: `ds.as_upload(path_on_compute='./foo.pkl')`. Ez a fájl feltölésének az adattár gyökérkönyvtárában.
    
Ha egy adott mappa vagy fájl az adattár a hivatkozni kívánt, használhatja az adattárhoz **`path`** függvény. Például, ha az adattár rendelkezik a relatív elérési út egy könyvtár `./bar`, és csak szeretné letölteni a mappa tartalmának a számítási célnak, a következőképpen teheti: `ds.path('./bar').as_download()`

Bármely `ds` vagy `ds.path` objektumra mutat egy környezeti változó neve, a formátum `"$AZUREML_DATAREFERENCE_XXXX"` amelynek értéke a csatlakoztatási/letöltési út távoli számítási jelöli. Az adattároló elérési út távoli számítási nem lehet ugyanaz, mint a szkript végrehajtási útvonalát.

Szeretné elérni az adattárhoz betanítás során, akkor is át azt a tanítási szkriptet keresztül parancssori argumentumként `script_params`:

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

Azt is megteheti, az adattárolók listáját adhasson a `inputs` csatlakoztatni, vagy másolja és- tárolókról a datastore(s) Estimator konstruktorának paramétert:

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
