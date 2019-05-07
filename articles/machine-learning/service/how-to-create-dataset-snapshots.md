---
title: Összehasonlítás & reprodukálása adatok pillanatképekkel idővel
titleSuffix: Azure Machine Learning service
description: Hasonlítsa össze az adatokat idővel és megismételhetőség adatkészlet pillanatképekkel biztosítása
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.date: 05/02/2019
ms.openlocfilehash: 51d0dcfc543834e9a8725d11fa82b566a5132a6b
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204994"
---
# <a name="compare-data-and-ensure-reproducibility-with-snapshots-preview"></a>Hasonlítsa össze az adatokat, és ellenőrizze, megismételhetőség pillanatképek (előzetes verzió)

Ebben a cikkben megtudhatja, hogyan hozhat létre és kezelheti a pillanatképeket a [Azure Machine Learning adatkészletek](how-to-create-register-datasets.md) (adatkészletek), így rögzítése vagy adatok időbeli összehasonlítása. Az adatkészletek egyszerűbbé eléréséhez, és az adatok a felhőben, a különböző forgatókönyvekben.

**Adatkészlet pillanatképek** egy profilt (az összefoglaló statisztikák) az adatok tárolása a létrehozás időpontjában. Ha szeretné, is tárolhatja az adatokat a megismételhetőség pillanatképe.

>[!Important]
> A pillanatképek tárolási költségekkel. Még több tárolási adatok másolatát tárolja a pillanatkép van szükség. Használat [ `dataset.delete_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#delete-snapshot-snapshot-name-) ha azok már nincs szükség.

## <a name="when-to-use-snapshots"></a>A pillanatképek használata

Nincsenek pillanatképekhez három fő használja:

+ **Érvényesítési modell**: Hasonlítsa össze az adatprofil különböző pillanatképek a betanítási futtatás, vagy a termelési adatokon.

+ **Modell megismételhetőség**: Reprodukálja az eredményeket egy pillanatkép, amely adatokat tartalmaz a betanítás során meghívásával.

+ **Nyomon követheti az adatok idővel**: Tekintse meg, hogyan alakult a az adatkészlet által [profilok összehasonlítása](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py#compare-profiles-rhs-dataset-snapshot--include-columns-none--exclude-columns-none--histogram-compare-method--histogramcomparemethod-wasserstein--0--)
  
## <a name="prerequisites"></a>Előfeltételek

Adatkészlet-pillanatképek létrehozásához, szüksége van egy regisztrált az Azure Machine Learning adatkészletet. Ha nem rendelkezik egy, [létrehozása és regisztrálása adatkészletek](how-to-create-register-datasets.md).

## <a name="create-dataset-snapshots"></a>Adatkészlet-pillanatképek létrehozásához

Egy adatkészlet pillanatkép létrehozásához használja [ `dataset.create_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?#create-snapshot-snapshot-name--compute-target-none--create-data-snapshot-false--target-datastore-none-) Azure Machine Learning SDK.

Alapértelmezés szerint a pillanatkép tárolja, a legújabb adatok profiljának (összefoglaló statisztikák) [adatkészlet-definícióban](how-to-manage-dataset-definitions.md) alkalmazza. Egy adatkészlet-definícióban bármely Adatátalakítási lépéseket, az adatok meghatározott bejegyzést tartalmaz. Ez remek módja, hogy a munka adatelőkészítéshez reprodukálható.

Szükség esetén is használható az adatok másolatát a pillanatfelvétel hozzáadásával `create_data_snapshot = True`.  Ezek az adatok megismételhetőség hasznos lehet.

Ez a példa [bűnözés mintaadatok](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) és a egy adatkészlet `dataset_crime` használatával a cikkben létrehozott ["létrehozása és regisztrálása adatkészletek"](how-to-create-register-datasets.md).

```Python
from azureml.core.workspace import Workspace
from azureml.core.dataset import Dataset
from azureml.data.dataset_snapshot import DatasetSnapshot
import datetime

# get existing workspace
workspace = Workspace.from_config()

# get existing, named dataset:
dataset = workspace.datasets['dataset_crime']

# assign name to snapshot
snapshot_name = 'snapshot_' + datetime.datetime.today().strftime('%Y%m%d%H%M%S')

# create snapshot to store profile from a remote environment
# and include copy of data
snapshot = dataset.create_snapshot(snapshot_name = snapshot_name,
                                   compute_target = remote_compute_target,
                                   create_data_snapshot = True)
```

Használjon aszinkron módon jönnek létre pillanatképek, mert a [ `wait_for_completion()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py#wait-for-completion-show-output-true--status-update-frequency-10-) metódus a folyamat megfigyeléséhez.

```Python
# monitor process every 10 seconds
snapshot.wait_for_completion(show_output=True, status_update_frequency=10)

# return snapshot just created
dataset.get_snapshot(snapshot_name)
```

Kimenet:

```
Action status: Running
Action status: Running
Action status: Completed


DatasetSnapshot(Name: snapshot_20190411165420,
Dataset Id: 66cd0830-2f72-41e5-b677-d2d953f54821,
Workspace: "your_workspace_name",
Dataset definition version: 1,
Snapshot created date: 2019-05-11 17:24:00+00:00)
```

Használat [ `dataset.delete_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#delete-snapshot-snapshot-name-) ha azok már nincs szükség.

## <a name="find-snapshots"></a>A pillanatképek keresése

Egy meglévő pillanatkép kérheti [ `get_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-snapshot-snapshot-name-).

A mentett pillanatképeket egy adott adatkészlet listájának lekéréséhez használja [ `get_all_snapshots()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-all-snapshots--).

```Python
# Get named snapshot for this dataset
dataset.get_snapshot(snapshot_name)

# Get all snapshots for this dataset
dataset.get_all_snapshots()
```

## <a name="get-data-and-profiles-from-snapshots"></a>Adatok és profilok beolvasása pillanatképekből

Minden pillanatképet hoz létre egy profilt, az adatkészlet, amely magában foglalja az összefoglaló statisztikák, és lehetővé teszi az adatok másolatát menteni.

### <a name="get-the-data-profile"></a>Az adatok profil beolvasása

Használat [ `get_profile()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-) a profilt az adatok eléréséhez.  Ez a profil segítségével betanítási és éles adatok, például összehasonlítása.

```Python
snapshot.get_profile()
```

||Típus|Min|Max|Darabszám|Hiányzó száma|Nem hiányzó száma|Hiányzó százalék|Hibák száma|Üres száma|0,1 % ki osztóérték|1 % ki osztóérték|5 %-os ki osztóérték|25 %-os ki osztóérték|50 %-os ki osztóérték|75 %-os ki osztóérték|95 %-os ki osztóérték|99 %-os ki osztóérték|99,9 %-os ki osztóérték|középérték|Szórás|Variancia|Döntés|Értékek
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
ID (Azonosító)|FieldType.INTEGER|1.04986e + 07|1.05351e + 07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e + 07|1.04992e + 07|1.04986e + 07|1.05166e + 07|1.05209e + 07|1.05259e + 07|1.05351e + 07|1.05351e + 07|1.05351e + 07|1.05195e + 07|12302.7|1.51358e + 08|-0.495701|-1.02814
Eset száma|FieldType.STRING|HZ239907|HZ278872|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Dátum|FieldType.DATE|2016-04-04 23:56:00+00:00|2016-04-15 17:00:00+00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Blokk|FieldType.STRING|004XX S KILBOURN ELENTÉS MENTÉSE|113XX S VALÓ MENTÉSE|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
IUCR|FieldType.INTEGER|810|1154|10.0|0.0|10.0|0.0|0.0|0.0|810|850|810|890|1136|1153|1154|1154|1154|1058.5|137.285|18847.2|-0.785501|-1.3543
Elsődleges típusa|FieldType.STRING|MEGTÉVESZTŐ ELJÁRÁS|LOPÁS|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Leírás|FieldType.STRING|HAMIS ELLENŐRZÉSE|500 USD KERESZTÜL|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Leírás helye|FieldType.STRING||SCHOOL, PUBLIC, BUILDING|10.0|0.0|10.0|0.0|0.0|1.0||||||||||||||
Letartóztatás|FieldType.BOOLEAN|False (Hamis)|False (Hamis)|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Hazai|FieldType.BOOLEAN|False (Hamis)|False (Hamis)|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Beat|FieldType.INTEGER|531|2433|10.0|0.0|10.0|0.0|0.0|0.0|531|531|531|614|1318.5|1911|2433|2433|2433|1371.1|692.094|478994|0.105418|-1.60684
Kerület|FieldType.INTEGER|5|24|10.0|0.0|10.0|0.0|0.0|0.0|5|5|5|6|13|19|24|24|24|13.5|6.94822|48.2778|0.0930109|-1.62325
Ward|FieldType.INTEGER|1|48|10.0|0.0|10.0|0.0|0.0|0.0|1|5|1|9|22.5|40|48|48|48|24.5|16.2635|264.5|0.173723|-1.51271
Közösségi terület|FieldType.INTEGER|4|77|10.0|0.0|10.0|0.0|0.0|0.0|4|8.5|4|24|37.5|71|77|77|77|41.2|26.6366|709.511|0.112157|-1.73379

### <a name="get-the-data-from-the-snapshot"></a>Az adatok lekérése a pillanatképből

Adatkészlet pillanatkép menti az adatokat egy példányának beszerzéséhez, hozzon létre egy pandas DataFrame-a [ `to_pandas_dataframe()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#to-pandas-dataframe--) metódust.

Ez a metódus sikertelen lesz, ha az adatok másolatát pillanatkép létrehozása során nem volt szükség.

```Python
snapshot.to_pandas_dataframe().head(3)
```

||ID (Azonosító)|Eset száma|Dátum|Letiltás|IUCR|Elsődleges típusa|Leírás|Leírás helye|Letartóztatás|Hazai|...|Ward|Közösségi terület|Az FBI kód|X koordinátáját|Y koordinátája|Év|Frissítés dátuma|Szélesség|Hosszúság|Hely
-|--|-----------|----|-----|----|------------|-----------|--------------------|------|--------|---|----|--------------|--------|------------|------------|----|----------|--------|---------|--------
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|1153|MEGTÉVESZTŐ ELJÁRÁS|PÉNZÜGYI ADATOKKAL VALÓ VISSZAÉLÉS KERESZTÜL 300 USD|EGYÉB|False (Hamis)|False (Hamis)|...|9|50|11|1183356.0|1831503.0|2016|2016-05-11 15:48:00|41.692834|-87.604319|(41.692833841, -87.60431945)
1|10516598|HZ258664|2016-04-15 17:00:00|082XX S MARSHFIELD ELENTÉS MENTÉSE|890|LOPÁS|FROM BUILDING|RESIDENCE|False (Hamis)|False (Hamis)|...|21|71|6|1166776.0|1850053.0|2016|2016-05-12 15:48:00|41.744107|-87.664494|(41.744106973, -87.664494285)
2|10519196|HZ261252|2016-04-15 10:00:00|104XX S SACRAMENTÓI ELENTÉS MENTÉSE|1154|MEGTÉVESZTŐ ELJÁRÁS|PÉNZÜGYI ADATOKKAL VALÓ VISSZAÉLÉS 300 USD MAJD A|RESIDENCE|False (Hamis)|False (Hamis)|...|19|74|11|NaN|NaN|2016|2016-05-12 15:50:00|NaN|NaN|

## <a name="next-steps"></a>További lépések

* Lásd: az SDK [áttekintése](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) tervezési minták és használati példák.

* Példa adatkészlet-pillanatképekkel, tekintse meg a [notebookok minta](https://aka.ms/dataset-tutorial).
