---
title: Adatbetöltés az Azure Data Factoryvel
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre adatfeldolgozási folyamatot Azure Data Factory és az egyes lehetőségek előnyeivel.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 03/01/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 73850d8022618dd6544d19564e425288aff09771
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360632"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Adatbetöltés az Azure Data Factoryvel

Ebből a cikkből megtudhatja, hogyan hozhat létre adatfeldolgozási folyamatot Azure Data Factory (ADF) használatával. Ez a folyamat a Azure Machine Learningsal való használatra szolgáló adatbevitelhez használatos. Azure Data Factory lehetővé teszi az adatok egyszerű kinyerését, átalakítását és betöltését (ETL). Az adatgyűjtés és a tárolóba való betöltés után a gépi tanulási modellek betanítására is használható.

Az egyszerű adatátalakítás a natív ADF-tevékenységekkel és olyan eszközökkel kezelhető, mint például az [adatfolyam](../data-factory/control-flow-execute-data-flow-activity.md). Bonyolultabb forgatókönyvek esetén az adatok feldolgozhatók egyéni kódokkal is. Például: Python vagy R code.

Számos gyakori módszer áll rendelkezésre a Azure Data Factory az adatfeldolgozás során történő átalakítására. Minden technikának van olyan előnye és hátránya, amely meghatározza, hogy megfelelő-e az adott használati esethez:

| Módszer | Előnyök | Hátrányok |
| ----- | ----- | ----- |
| ADF + Azure Functions | Kis késleltetés, kiszolgáló nélküli számítás</br>Állapot-nyilvántartó függvények</br>Újrafelhasználható függvények | Csak rövid ideig futó feldolgozás esetén jó |
| ADF + egyéni összetevő | Nagyméretű párhuzamos számítástechnika</br>Nagy teljesítményű algoritmusokhoz | Kód becsomagolása végrehajtható fájlba</br>A függőségek és az i/o kezelési bonyolultsága |
| ADF + Azure Databricks notebook | Apache Spark</br>Natív Python-környezet | Költséges lehet</br>A fürtök létrehozása kezdetben időt vesz igénybe, és késleltetést okoz

## <a name="adf-with-azure-functions"></a>ADF az Azure functions használatával

![Ábrán látható egy Azure Data Factory folyamat, az Azure Function és az M L folyamat futtatásával, valamint egy Azure Machine Learning folyamattal, a betanítási modellel, valamint a nyers és előkészített adattal való interakcióval.](media/how-to-data-ingest-adf/adf-function.png)

Azure Functions lehetővé teszi, hogy kis mennyiségű kódot (functions) futtasson anélkül, hogy az alkalmazás-infrastruktúrával kellene foglalkoznia. Ebben a beállításban az Azure-függvénybe becsomagolt egyéni Python-kóddal dolgozza fel az adatfeldolgozást. 

A függvényt az [ADF Azure Function tevékenység](../data-factory/control-flow-azure-function-activity.md)hívja meg. Ez a megközelítés jó választás az egyszerűsített adatátalakításokhoz. 

* Szakemberek
    * Az adatok feldolgozása kiszolgáló nélküli számításban történik, viszonylag alacsony késéssel
    * Az ADF-folyamat olyan [tartós Azure-függvényt](../azure-functions/durable/durable-functions-overview.md) hívhat meg, amely kifinomult Adatátalakítási folyamatot valósít meg 
    * Az adatok átalakításának részleteit az Azure-függvény elküldi, amely a többi helyről újra felhasználható és meghívható
* Hátránya
    * Az ADF-sel való használat előtt létre kell hozni a Azure Functions.
    * Azure Functions csak rövid ideig futó adatfeldolgozás esetén jó

## <a name="adf-with-custom-component-activity"></a>ADF egyéni összetevő-tevékenységgel

![Az ábrán egy Azure Data Factory folyamat látható, amely egy egyéni összetevővel és az M L folyamat futtatásával, valamint egy Azure Machine Learning folyamattal, valamint a betanítási modellel, valamint a nyers és az előkészített adattal való interakcióval.](media/how-to-data-ingest-adf/adf-customcomponent.png)

Ebben a beállításban az adatfeldolgozást egyéni Python-kóddal, végrehajtható fájlba csomagolja a rendszer. A rendszer az [Egyéni ADF-összetevő tevékenységével](../data-factory/transform-data-using-dotnet-custom-activity.md)hívja meg. Ez a megközelítés jobban illeszkedik az előző technikánál nagyobb méretű adatmennyiséghez.

* Szakemberek
    * Az adat [Azure batch](../batch/batch-technical-overview.md) készletre lesz feldolgozva, amely nagy léptékű párhuzamos és nagy teljesítményű számítástechnikai lehetőségeket biztosít
    * Nehéz algoritmusok futtatására és jelentős adatmennyiségek feldolgozására is használható.
* Hátránya
    * Azure Batch készletet az ADF-sel való használat előtt létre kell hozni
    * A Python-kód egy végrehajtható fájlba való becsomagolásával kapcsolatos mérnöki tevékenység. A függőségek és a bemeneti/kimeneti paraméterek összetettsége

## <a name="adf-with-azure-databricks-python-notebook"></a>ADF Azure Databricks Python notebooktal

![A diagram egy Azure Data Factory folyamatot mutat be, amely a Azure Databricks Python és az M L folyamat futtatásával, valamint egy Azure Machine Learning folyamattal, a betanítási modellel, valamint a nyers és az előkészített adattal való interakcióval foglalkozik.](media/how-to-data-ingest-adf/adf-databricks.png)

A [Azure Databricks](https://azure.microsoft.com/services/databricks/) egy Apache Spark-alapú elemzési platform a Microsoft Cloud-ban.

Ebben a technikában az adatátalakítást egy Azure Databricks-fürtön futó [Python-jegyzetfüzet](../data-factory/transform-data-using-databricks-notebook.md)hajtja végre. Ez valószínűleg a leggyakoribb megközelítés, amely kihasználja egy Azure Databricks szolgáltatás teljes erejét. A szolgáltatás nagy léptékű elosztott adatfeldolgozásra lett tervezve.

* Szakemberek
    * Az adatfeldolgozás a leghatékonyabban használt Azure-szolgáltatásra épül, amelyet Apache Spark környezet készít biztonsági mentésre
    * A Python natív támogatása az adatelemzési keretrendszerek és kódtárak mellett, beleértve a TensorFlow, a PyTorch és a scikit – Learn
    * Nincs szükség a Python-kód functions vagy végrehajtható modulokba való becsomagolására. A kód a következőképpen működik.
* Hátránya
    * Az ADF-sel való használat előtt létre kell hozni Azure Databricks-infrastruktúrát
    * Költséges lehet a Azure Databricks konfigurációjától függően
    * A számítási fürtök "hideg" módból való elforgatása hosszabb időt vesz igénybe, amely nagy késleltetést biztosít a megoldásnak 
    

## <a name="consuming-data-in-azure-machine-learning-pipelines"></a>Azure Machine Learning-folyamatok adatfeldolgozása

![A diagram egy Azure Data Factory folyamatot és egy Azure Machine Learning folyamatot mutat be, valamint azt, hogy hogyan működnek együtt a nyers és az előkészített adatfeldolgozási folyamat. Az Data Factory folyamat az adatokat az előkészített adatadatbázisba küldi, amely egy adattárat, amely a Machine Learning munkaterületen található adatkészleteket táplálja.](media/how-to-data-ingest-adf/aml-dataset.png)

Az ADF-folyamatból származó átalakított adatok adattárolóba (például Azure Blobba) lesznek mentve. [A Azure Machine learning](./how-to-access-data.md#create-and-register-datastores) adattárolók és [adatkészletek](./how-to-create-register-datasets.md)használatával érheti el ezeket az adatokat.

Minden alkalommal, amikor az ADF-folyamat fut, a rendszer az adattárolást egy másik helyre menti. Ha át szeretné adni a helyet a Azure Machine Learningnak, az ADF-folyamat meghívja a Azure Machine Learning folyamatot. A ML-folyamat meghívásakor az adatok helye és a futtatási azonosító paraméterként lesz elküldve. A ML-folyamat Ezután létrehoz egy adattárt/adatkészletet az adathelyen. 

> [!TIP]
> Az adatkészletek [támogatják a verziószámozást](./how-to-version-track-datasets.md), így a ml-folyamat képes regisztrálni az adatkészlet új verzióját, amely az ADF-folyamat legutóbbi adatait mutat.

Ha az adat egy adattáron vagy adatkészleten keresztül érhető el, egy ML-modell betanítására is használható. Előfordulhat, hogy a betanítási folyamat az ADF-ből meghívott azonos ML folyamat része. Vagy lehet különálló folyamat, például kísérletezés egy Jupyter jegyzetfüzetben.

Mivel az adatkészletek támogatják a verziószámozást, és a folyamat minden futtatása új verziót hoz létre, könnyen megtudhatja, hogy az adatok melyik verzióját használták a modell betanításához.

## <a name="next-steps"></a>Következő lépések

* [Databricks-jegyzetfüzet futtatása Azure Data Factory](../data-factory/transform-data-using-databricks-notebook.md)
* [Az Azure Storage-szolgáltatásokban tárolt adathozzáférés](./how-to-access-data.md#create-and-register-datastores)
* [Modellek betanítása adatkészletekkel Azure Machine Learning](./how-to-train-with-datasets.md)
* [A DevOps használata adatfeldolgozási folyamathoz](./how-to-cicd-data-ingestion.md)