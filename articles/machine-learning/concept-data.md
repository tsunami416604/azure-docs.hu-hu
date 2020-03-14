---
title: Azure Machine Learningban lévő adatkészletek
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan kommunikálhat a Azure Machine Learning az adataival, és hogyan hasznosítható a gépi tanulási kísérletek során.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 12/09/2019
ms.openlocfilehash: a2af1e87ce7b17183ae09fb02b2652a04f585e84
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270263"
---
# <a name="data-access-in-azure-machine-learning"></a>Adatelérés Azure Machine Learning

Ebben a cikkben megismerheti Azure Machine Learning adatkezelési és-integrációs megoldásait a gépi tanulási feladatokhoz. Ez a cikk feltételezi, hogy már létrehozott egy [Azure Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) és egy [Azure Storage-szolgáltatást](https://docs.microsoft.com/azure/storage/common/storage-introduction).

Ha készen áll a tárolóban lévő adatfelhasználásra, javasoljuk, hogy

1. Hozzon létre egy Azure Machine Learning adattárt.
2. Ebből az adattárból hozzon létre egy Azure Machine Learning adatkészletet. 
3. Használja ezt az adatkészletet a Machine learning-kísérletben a következők bármelyikével: 
    1. Csatlakoztatás a kísérlet számítási céljához a modell betanításához

        **VAGY** 

    1. Közvetlenül a Azure Machine Learning-megoldások, például az automatizált gépi tanulás (automatizált ML) kísérlet futtatása, a gépi tanulási folyamatok és a [Azure Machine learning Designer](concept-designer.md)használatában.
4. Hozzon létre adatkészlet-figyelőket a modell kimeneti adatkészletéhez az adateltolódás észleléséhez. 
5. Ha az adateltolódás észlelhető, frissítse a bemeneti adatkészletet, és ennek megfelelően módosítsa a modellt.

Az alábbi ábra az ajánlott adatelérési munkafolyamat vizuális bemutatóját mutatja be.

![Adatkoncepció – diagram](./media/concept-data/data-concept-diagram.svg)

## <a name="access-data-in-storage"></a>A tárterületen tárolt adathozzáférés

A Storage-fiókban tárolt adatai eléréséhez Azure Machine Learning adattárolókat és adatkészleteket is biztosít. Az adattárolók megválaszolják a kérdést: Hogyan lehet biztonságosan csatlakozni az Azure Storage-ban tárolt adataihoz? Az adattárolók egy absztrakt réteget biztosítanak a tárolási szolgáltatáshoz. Ez a biztonsági és könnyű hozzáférést biztosít a tárolóhoz, mivel a kapcsolódási adatokat az adattár tárolja, és nem teszi közzé a szkriptekben. 

Az adatkészletek megválaszolják a kérdést: Hogyan szerezhetek be konkrét adatfájlokat az adattárba? Az adatkészletek az alapul szolgáló tárolóban lévő adott fájlra vagy fájlokra mutatnak, amelyeket használni szeretne a gépi tanulási kísérlethez. Az adattárolók és adatkészletek együttesen biztonságos, méretezhető és reprodukálható adattovábbítási munkafolyamatot biztosítanak a gépi tanulási feladatokhoz.

### <a name="datastores"></a>Lemezét

Az Azure Machine Learning adattár az Azure Storage-szolgáltatások tárolási absztrakciója. [Regisztráljon, és hozzon létre egy adattárt](how-to-access-data.md) az Azure Storage-fiókhoz való egyszerű kapcsolódáshoz, és a mögöttes Azure Storage-szolgáltatásokban tárolt adatokat.

Támogatott Azure Storage-szolgáltatások, amelyek adattárként regisztrálhatók:
+ Azure Blob-tároló
+ Azure-fájlmegosztás
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Azure SQL Database
+ Azure Database for PostgreSQL
+ Databricks fájlrendszer
+ Azure Database for MySQL

### <a name="datasets"></a>Adathalmazok

[Hozzon létre egy Azure Machine learning adatkészletet](how-to-create-register-datasets.md) az adattárolókban lévő adatokkal való kommunikációhoz, és a gépi tanulási feladatokhoz csomagolja ki az adatokat egy fogyasztható objektumba. Regisztrálja az adatkészletet a munkaterületen, hogy az adatfeldolgozási bonyolultság nélkül ossza meg és használja fel a különböző kísérletek között.

Az adatkészletek a helyi fájlokból, a nyilvános URL-címekből, az [Azure Open-adatkészletből](#open)vagy az adattárolókban található fájlokból is létrehozhatók. Ha adatkészletet szeretne létrehozni egy memóriából származó pandák dataframe, írja az adatokat egy helyi fájlba, például egy CSV-fájlba, és hozza létre az adatkészletet a fájlból. Az adathalmazok nem másolják az adatokat, de a tárolási szolgáltatásban lévő adatokra mutató hivatkozásokat tartalmaznak, így nem merül fel további tárolási költségek. 

Az alábbi ábrán látható, hogy ha nem rendelkezik Azure Storage szolgáltatással, létrehozhat egy adatkészletet közvetlenül a helyi fájlokból, a nyilvános URL-címekből vagy egy Azure Open-adatkészletből. Ezzel összekapcsolja az adatkészletet az alapértelmezett adattárral, amelyet a rendszer automatikusan hozott létre a kísérlet [Azure Machine learning munkaterületen](concept-workspace.md).

![Adatkoncepció – diagram](./media/concept-data/dataset-workflow.svg)

Az adatkészletek további funkciói a következő dokumentációban találhatók:

+ [Az adatkészlet verziószáma és nyomon követése](how-to-version-track-datasets.md) .
+ [Figyelje az adathalmazt](how-to-monitor-datasets.md) , hogy segítsen az adateltolódás észlelésében.
+  A két adathalmazra vonatkozó dokumentációt a következő témakörben találja:
    + A [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) táblázatos formátumban jeleníti meg az adatokat a megadott fájl vagy fájlok listájának elemzésével. Ez lehetővé teszi, hogy a további manipuláció és tisztítás érdekében egy Panda vagy Spark DataFrame megvalósuljon az adatgyűjtés. A TabularDatasets létrehozásához használható fájlok teljes listáját a [TabularDatasetFactory osztályban](https://aka.ms/tabulardataset-api-reference)tekintheti meg.

    + A [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) egy vagy több fájlt hivatkozik az adattárolókban vagy a nyilvános URL-címeken. Ezzel a módszerrel letöltheti vagy csatlakoztathatja a számítási célra választott fájlokat FileDataset objektumként.

## <a name="work-with-your-data"></a>Az adataival dolgozhat

Az adatkészletekkel számos gépi tanulási feladatot hajthat végre Azure Machine Learning funkciókkal való zökkenőmentes integráció révén. 

+ A [gépi tanulási modellek betanítása](how-to-train-with-datasets.md).
+ Adatkészletek felhasználása a-ben 
     + [automatizált ML-kísérletek](how-to-use-automated-ml-for-ml-models.md)
     + a [tervező](tutorial-designer-automobile-price-train-score.md#import-data) 
+ Az adatkészletek a [gépi tanulási folyamatokban](how-to-create-your-first-pipeline.md)a Batch-következtetéssel való pontozáshoz érhetők el.
+ [Adatcímkéző projekt](#label)létrehozása.
+ Adatkészlet-figyelő beállítása az [adateltolódás](#drift) észleléséhez.

<a name="open"></a>

## <a name="azure-open-datasets"></a>Azure Open Datasets

Az [Azure Open-adatkészletek](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets) olyan beszerzett nyilvános adatkészletek, amelyekkel pontosabb modelleket adhat hozzá a gépi tanulási megoldásokhoz. A nyílt adatkészletek a felhőben vannak Microsoft Azure és integrálva vannak a Azure Machine Learningba. Az adatkészleteket API-kon keresztül is elérheti, és más termékekben, például Power BI és Azure Data Factory is használhatja.

Az Azure Open-adatkészletek az időjárási, a népszámlálási, az ünnepnapok, a közbiztonság és a gépi tanulási modellek betanítását, valamint a prediktív megoldások bővítését segítő nyilvános tartományi adatokat tartalmazzák. Az Azure Open-adatkészleteken is megoszthatja nyilvános adatkészleteit.

<a name="label"></a>

## <a name="data-labeling"></a>Adatfelirat

A nagyméretű adatmennyiségek címkézése gyakran fejfájást eredményezett a gépi tanulási projektekben. A számítógépes látási összetevőkkel, például a képbesorolással vagy az objektum-észleléssel rendelkezők általában több ezer képet és a hozzájuk tartozó címkéket igényelnek.

Azure Machine Learning központi helyet biztosít a címkézési projektek létrehozásához, kezeléséhez és figyeléséhez. A projektek címkézése segít az adatok, címkék és csoporttagok koordinálásában, így hatékonyabban kezelheti a címkézési feladatokat. A jelenleg támogatott feladatok a képbesorolás, a többcímkés vagy a többosztályos, valamint a kötött mezőkkel rendelkező objektumok azonosítása.

+ Hozzon létre egy [adatcímkéző projektet](how-to-create-labeling-projects.md), és exportálja az adatkészletet a gépi tanulási kísérletekben való használatra.

<a name="drift"></a>

## <a name="data-drift"></a>Adateltolódás

A gépi tanulás kontextusában az adateltolódás a modellben a teljesítmény romlását eredményező bemeneti adatok változása. Ez az egyik legfontosabb ok, hogy a modell pontossága az idő múlásával csökken, így az adateltolódás monitorozása segít a modell teljesítményével kapcsolatos problémák észlelésében.
Az adatkészlet létrehozása című cikkből megtudhatja, hogyan azonosíthatja és [figyelheti](how-to-monitor-datasets.md) az adateltolódást az adatkészletekben lévő új adatokat.

## <a name="next-steps"></a>További lépések 

+ Hozzon létre egy adatkészletet Azure Machine Learning Studióban vagy a Python SDK-val, [kövesse az alábbi lépéseket.](how-to-create-register-datasets.md)
+ A [minta-jegyzetfüzetekkel](https://aka.ms/dataset-tutorial)kipróbálhatja az adatkészlet tanítási példáit.
+ Az adateltolódással kapcsolatos példákért tekintse meg ezt az [adateltolódási oktatóanyagot](https://aka.ms/datadrift-notebook).
