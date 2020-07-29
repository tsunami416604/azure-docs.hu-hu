---
title: Biztonságos adathozzáférés a felhőben
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan kapcsolódhat biztonságosan a Azure Machine Learning adataihoz, és hogyan használhatók az adatkészletek és az adattárolók a ML-feladatokhoz. Az adattárolók tárolhatnak egy Azure-Blob adatait, Azure Data Lake 1. generációs & 2, SQL db, Databricks,...
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 04/24/2020
ms.custom: tracking-python
ms.openlocfilehash: 15cf4aa6adda26991e76ec8a5e7378766fe2a21f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84552647"
---
# <a name="secure-data-access-in-azure-machine-learning"></a>Adatelérés védelme Azure Machine Learning

A Azure Machine Learning megkönnyíti a felhőben tárolt adataihoz való kapcsolódást.  Absztrakt réteget biztosít a mögöttes tárolási szolgáltatáshoz, így biztonságosan férhet hozzá és dolgozhat az adataival anélkül, hogy kódot kellene írnia a tárolási típusra. A Azure Machine Learning a következő adatkezelési képességeket is biztosítja:

*    Az adatvonal verziószámozása és nyomon követése
*    Adatfelirat 
*    Adateltérések monitorozása
*    Együttműködés a pandák és a Spark DataFrames

## <a name="data-workflow"></a>Adatmunkafolyamat

Ha készen áll a felhőalapú tárolási megoldásban tárolt adatfelhasználásra, javasoljuk, hogy a következő adattovábbítási munkafolyamatot ajánljuk. Ez a munkafolyamat feltételezi, hogy rendelkezik [Azure Storage-fiókkal](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) és-adattal az Azure felhőalapú tárolási szolgáltatásában. 

1. Hozzon létre egy [Azure Machine learning adattárt](#datastores) a kapcsolódási adatok Azure Storage-ba való tárolásához.

2. Ebből az adattárból hozzon létre egy [Azure Machine learning adatkészletet](#datasets) , amely egy adott fájl (ok) ra mutat a mögöttes tárolóban. 

3. Ha ezt az adatkészletet a Machine learning-kísérletben szeretné használni, a következő lehetőségek közül választhat:
    1. Csatlakoztassa a kísérlet számítási céljához a modell betanításához.

        **VAGY** 

    1. Használja közvetlenül a Azure Machine Learning-megoldásokban, például az automatikus gépi tanulás (automatizált ML) kísérleteket, a gépi tanulási folyamatokat vagy a [Azure Machine learning designert](concept-designer.md).

4. Hozzon létre [adatkészlet-figyelőket](#data-drift) a modell kimeneti adatkészletéhez az adateltolódás észleléséhez. 

5. Ha az adateltolódás észlelhető, frissítse a bemeneti adatkészletet, és ennek megfelelően módosítsa a modellt.

Az alábbi ábra a javasolt munkafolyamat vizuális bemutatását mutatja be.

![Adatkoncepció – diagram](./media/concept-data/data-concept-diagram.svg)

## <a name="datastores"></a>Lemezét

Azure Machine Learning adattárolók biztonságosan megőrzik a kapcsolódási adatokat az Azure Storage-ban, így nem kell azt a parancsfájlokba beírni. [Regisztráljon, és hozzon létre egy](how-to-access-data.md) adattárolót, amellyel könnyedén csatlakozhat a Storage-fiókjához, és elérheti a mögöttes Azure Storage szolgáltatásban tárolt adatokat. 

Az Azure-ban támogatott felhőalapú tárolási szolgáltatások, amelyek adattárként regisztrálhatók:

+ Azure Blob-tároló
+ Azure-fájlmegosztás
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Azure SQL Database
+ Azure Database for PostgreSQL
+ Databricks fájlrendszer
+ Azure Database for MySQL

## <a name="datasets"></a>Adathalmazok

Azure Machine Learning adatkészletek olyan hivatkozások, amelyek a tárolási szolgáltatásban lévő adatokra mutatnak. Nem tartoznak az adataihoz, ezért nem merül fel extra tárolási költségek. Ha a tárolóban lévő adatokkal szeretne kommunikálni, [hozzon létre egy adatkészletet](how-to-create-register-datasets.md) , amely az adatokat a gépi tanulási feladatokhoz tartozó, fogyóeszközök szerinti objektummá csomagolja. Regisztrálja az adatkészletet a munkaterületen, hogy az adatfeldolgozási bonyolultság nélkül ossza meg és használja fel a különböző kísérletek között.

Az adatkészletek helyi fájlokból, nyilvános URL-címekből, [Azure Open-adatkészletből](https://azure.microsoft.com/services/open-datasets/)vagy Azure Storage-szolgáltatásokból hozhatók létre adattárolók használatával. Ha adatkészletet szeretne létrehozni egy memóriából származó pandák dataframe, írja be az adatokat egy helyi fájlba, például egy parkettával, és hozza létre az adatkészletet a fájlból.  

Két típusú adatkészletet támogatunk: 
+ A [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) táblázatos formátumban jeleníti meg az adatokat a megadott fájl vagy fájlok listájának elemzésével. A TabularDataset egy Panda vagy Spark DataFrame is betöltheti további manipuláció és tisztítás céljából. A TabularDatasets létrehozásához használható adatformátumok teljes listáját a [TabularDatasetFactory osztályban](https://aka.ms/tabulardataset-api-reference)tekintheti meg.

+ A [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) egy vagy több fájlt hivatkozik az adattárolókban vagy a nyilvános URL-címeken. A FileDatasets által hivatkozott fájlokat a számítási célra is [letöltheti vagy csatlakoztathatja](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) .

Az adatkészletek további funkciói a következő dokumentációban találhatók:

+ [Az adatkészlet verziószáma és nyomon követése](how-to-version-track-datasets.md) .
+ [Figyelje az adathalmazt](how-to-monitor-datasets.md) , hogy segítsen az adateltolódás észlelésében.    

## <a name="work-with-your-data"></a>Az adataival dolgozhat

Az adatkészletekkel számos gépi tanulási feladatot hajthat végre Azure Machine Learning funkciókkal való zökkenőmentes integráció révén. 

+ [Adatcímkéző projekt](#label)létrehozása.
+ A gépi tanulási modellek betanítása:
     + [automatizált ML-kísérletek](how-to-use-automated-ml-for-ml-models.md)
     + a [tervező](tutorial-designer-automobile-price-train-score.md#import-data)
     + [notebookok](how-to-train-with-datasets.md)
     + [Azure Machine Learning folyamatok](how-to-create-your-first-pipeline.md)
+ Az adatkészletek a [gépi tanulási folyamatokban](how-to-create-your-first-pipeline.md)a [Batch-következtetéssel](how-to-use-parallel-run-step.md) való pontozáshoz érhetők el.
+ Adatkészlet-figyelő beállítása az [adateltolódás](#drift) észleléséhez.

<a name="label"></a>

## <a name="data-labeling"></a>Adatfelirat

A nagyméretű adatmennyiségek címkézése gyakran fejfájást eredményezett a gépi tanulási projektekben. A számítógépes látási összetevőkkel, például a képbesorolással vagy az objektum-észleléssel rendelkezők általában több ezer képet és a hozzájuk tartozó címkéket igényelnek.

Azure Machine Learning központi helyet biztosít a címkézési projektek létrehozásához, kezeléséhez és figyeléséhez. A projektek címkézése segít az adatok, címkék és csoporttagok koordinálásában, így hatékonyabban kezelheti a címkézési feladatokat. A jelenleg támogatott feladatok a képbesorolás, a többcímkés vagy a többosztályos, valamint a kötött mezőkkel rendelkező objektumok azonosítása.

Hozzon létre egy [adatcímkéző projektet](how-to-create-labeling-projects.md), és exportálja az adatkészletet a gépi tanulási kísérletekben való használatra.

<a name="drift"></a>

## <a name="data-drift"></a>Adateltolódás

A gépi tanulás kontextusában az adateltolódás a modellben a teljesítmény romlását eredményező bemeneti adatok változása. Ez az egyik legfontosabb ok, hogy a modell pontossága az idő múlásával csökken, így az adateltolódás monitorozása segít a modell teljesítményével kapcsolatos problémák észlelésében.

Az adatkészlet létrehozása című cikkből megtudhatja, hogyan azonosíthatja és [figyelheti](how-to-monitor-datasets.md) az adateltolódást az adatkészletekben lévő új adatokat.

## <a name="next-steps"></a>További lépések 

+ Hozzon létre egy adatkészletet a Azure Machine Learning Studióban vagy a Python SDK-val az [alábbi lépések segítségével.](how-to-create-register-datasets.md)
+ A [minta-jegyzetfüzetekkel](https://aka.ms/dataset-tutorial)kipróbálhatja az adatkészlet tanítási példáit.
+ Az adateltolódással kapcsolatos példákért tekintse meg ezt az [adateltolódási oktatóanyagot](https://aka.ms/datadrift-notebook).
