---
title: Adatok az Azure Machine Learningben
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogy az Azure Machine Learning hogyan csatlakozik biztonságosan az adatokhoz, és hogyan használja fel ezeket az adatokat gépi tanulási feladatokhoz.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 03/20/2020
ms.openlocfilehash: 982c9c9eadec4403c8116430e1e25092de99f1d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128493"
---
# <a name="data-access-in-azure-machine-learning"></a>Adathozzáférés az Azure Machine Learningben

Az Azure Machine Learning megkönnyíti az adatokhoz való csatlakozást a felhőben.  Absztrakciós réteget biztosít az alapul szolgáló tárolási szolgáltatás felett, így biztonságosan elérheti és dolgozhat az adatokkal anélkül, hogy a tárolótípusra jellemző kódot kellene írnia. Az Azure Machine Learning a következő adatfunkciókat is biztosítja:

*    Az adatvonal verziószámozása és nyomon követése
*    Adatfeliratozás 
*    Adateltérések monitorozása
*    Interoperabilitás a Pandákkal és a Spark dataframe-ekkel

## <a name="data-workflow"></a>Adatmunkafolyamat

Ha készen áll a felhőalapú tárolási megoldásban lévő adatok használatára, a következő adatkézbesítési munkafolyamatot javasoljuk. Ez a munkafolyamat feltételezi, hogy rendelkezik egy [Azure-tárfiókkal](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) és adatokkal egy felhőalapú tárolási szolgáltatásban az Azure-ban. 

1. Hozzon létre egy [Azure Machine Learning-adattáratot,](#datastores) amely kapcsolati adatokat tárol az Azure-tárhelyhez.

2. Ebből az adattárból hozzon létre egy [Azure Machine Learning-adatkészletet,](#datasets) amely egy adott fájl(ok)ra mutat az alapul szolgáló tárolóban. 

3. Ahhoz, hogy ezt az adatkészletet a gépi tanulási kísérletben használja,
    1. Csatlakoztassa a kísérlet számítási céljához a modellbetanításhoz.

        **Vagy** 

    1. Használja ki közvetlenül az Azure Machine Learning-megoldások, mint például az automatizált gépi tanulási (automated ML) kísérletfut, gépi tanulási folyamatok, vagy az [Azure Machine Learning tervezője.](concept-designer.md)

4. Hozzon létre [adatkészlet-figyelők](#data-drift) a modell kimeneti adatkészlet az adatok eltolódásának észleléséhez. 

5. Adateltolódás észlelése esetén frissítse a bemeneti adatkészletet, és ennek megfelelően tanítsa be újra a modellt.

Az alábbi ábra az ajánlott munkafolyamat vizuális bemutatását tartalmazza.

![Adat-koncepció-diagram](./media/concept-data/data-concept-diagram.svg)

## <a name="datastores"></a>Adattárolók

Az Azure Machine Learning-adattárak biztonságosan megőrzik a kapcsolatadatait az Azure-tárhelyen, így nem kell azokat a parancsfájlokban kódolnia. [Regisztráljon és hozzon létre egy adattaboltot,](how-to-access-data.md) amely könnyen csatlakozhat a tárfiókhoz, és hozzáférhet az alapul szolgáló Azure-tárolási szolgáltatásban lévő adatokhoz. 

Támogatott felhőalapú tárolási szolgáltatások az Azure-ban, amelyek adattárként regisztrálhatók:

+ Azure Blob-tároló
+ Azure-fájlmegosztás
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Azure SQL Database
+ Azure Database for PostgreSQL
+ Databricks fájlrendszer
+ Azure Database for MySQL

## <a name="datasets"></a>Adathalmazok

Az Azure Machine Learning-adatkészletek olyan hivatkozások, amelyek a tárolási szolgáltatás ban lévő adatokra mutatnak. Ezek nem másolatok az adatokról, így nem merül fel további tárolási költség. A tárolóban tárolt adatok kal való interakcióhoz [hozzon létre egy adatkészletet,](how-to-create-register-datasets.md) amely gépi tanulási feladatokhoz használ az adatokat egy fogyóeszköz-objektumba csomagolja. Regisztrálja az adatkészletet a munkaterületre, hogy megossza és újra felhasználja a különböző kísérletekben adatbetöltési bonyolultság nélkül.

Az adatkészletek helyi fájlokból, nyilvános URL-címekből, [Azure Open Datasets-ből](https://azure.microsoft.com/services/open-datasets/)vagy Az Adattárakon keresztül azure storage-szolgáltatásokból hozhatók létre. Ha egy memóriapanda dataframe-ből szeretne adatkészletet létrehozni, írja az adatokat egy helyi fájlba, például egy parkettába, és hozza létre az adatkészletet abból a fájlból.  

Két féle adatkészletet támogatunk: 
+ A [táblázatos adatkészlet](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) táblázatos formátumú adatokat jelöl a megadott fájl vagy fájllista elemzésével. A tabularDataset betölthetegy Pandába vagy Spark DataFrame-be további manipuláció és tisztítás céljából. A TabularDatasets létrehozásához használható adatformátumok teljes listáját a [TabularDatasetFactory osztályban](https://aka.ms/tabulardataset-api-reference)található.

+ A [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) egy vagy több fájlra hivatkozik az adattárakban vagy nyilvános URL-címekben. A FileDatasets által hivatkozott [fájlokat letöltheti vagy csatlakoztathatja](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target) a számítási célhoz.

További adatkészletek képességei a következő dokumentációban találhatók:

+ [Verzió- és nyomon követhető](how-to-version-track-datasets.md) adatkészlet-leszármazási vonal.
+ [Az adatkészlet figyelése](how-to-monitor-datasets.md) az adateltolódás észlelésének elősegítésére.    

## <a name="work-with-your-data"></a>Az adatok kalkulát

Az adatkészletek segítségével számos gépi tanulási feladatot elvégezhet az Azure Machine Learning-funkciókkal való zökkenőmentes integrációval. 

+ [Adatcímkézési projekt](#label)létrehozása .
+ Gépi tanulási modellek betanítása:
     + [automatizált ML kísérletek](how-to-use-automated-ml-for-ml-models.md)
     + a [tervező](tutorial-designer-automobile-price-train-score.md#import-data)
     + [Notebook](how-to-train-with-datasets.md)
     + [Azure Machine Learning-folyamatok](how-to-create-your-first-pipeline.md)
+ A [gépi tanulási](how-to-create-your-first-pipeline.md) [folyamatokban kötegkövetkeztetéssel](how-to-use-parallel-run-step.md) rendelkező adatkészletek elérése.
+ Adatkészlet-figyelő beállítása [az adateltolódás](#drift) észlelésére.

<a name="label"></a>

## <a name="data-labeling"></a>Adatfeliratozás

Nagy mennyiségű adat címkézése gyakran fejfájást okozott a gépi tanulási projektekben. Azok, akik a számítógép látás komponens, mint például a képbesorolás vagy objektum észlelése, általában több ezer kép és a megfelelő címkéket.

Az Azure Machine Learning központi helyet biztosít a címkézési projektek létrehozásához, kezeléséhez és figyeléséhez. A címkézési projektek segítenek az adatok, címkék és csapattagok koordinálásában, így hatékonyabban kezelheti a címkézési feladatokat. A jelenleg támogatott feladatok a képbesorolás, akár többcímkés, akár többosztályos, valamint az objektumazonosítás kötött mezők használatával.

Hozzon létre egy [adatcímkézési projektet](how-to-create-labeling-projects.md), és adja ki az adatkészletet gépi tanulási kísérletekhez.

<a name="drift"></a>

## <a name="data-drift"></a>Adateltolódás

A gépi tanulás környezetében az adateltolódás a modell bemeneti adatainak változása, amely a modell teljesítményének csökkenéséhez vezet. Ez az egyik legfontosabb oka annak, hogy a modell pontossága idővel romlik, így az adatok eltolódásának figyelése segít a modell teljesítményproblémáinak észlelésében.

Az [adatkészletfigyelő létrehozása](how-to-monitor-datasets.md) című cikkben további információ arról, hogyan észlelheti és riasztást készíthet az adatkészlet új adatainak eltolódására.

## <a name="next-steps"></a>További lépések 

+ Hozzon létre egy adatkészletet az Azure Machine Learning stúdióban vagy a Python [SDK-val az alábbi lépésekkel.](how-to-create-register-datasets.md)
+ Próbálja ki az adatkészletek betanítási [példáit mintajegyzetfüzeteinkkel.](https://aka.ms/dataset-tutorial)
+ Az adateltolódáspéldáit lásd az [adateltolódásról szóló oktatóanyagban.](https://aka.ms/datadrift-notebook)
