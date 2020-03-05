---
title: Adatfeldolgozás Azure Data Factory
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre adatfeldolgozási folyamatot Azure Data Factory használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 03/01/2020
ms.openlocfilehash: 5b4ed40048aab815397c9726098880b2125b732c
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274787"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Adatfeldolgozás Azure Data Factory

Ebből a cikkből megtudhatja, hogyan hozhat létre adatfeldolgozási folyamatot Azure Data Factory (ADF) használatával. Ez a folyamat a Azure Machine Learningsal való használatra szolgáló adatbevitelhez használatos. Azure Data Factory lehetővé teszi az adatok egyszerű kinyerését, átalakítását és betöltését (ETL). Az adatgyűjtés és a tárolóba való betöltés után a gépi tanulási modellek betanítására is használható.

Az egyszerű adatátalakítás a natív ADF-tevékenységekkel és olyan eszközökkel kezelhető, mint például az [adatfolyam](https://docs.microsoft.com/azure/data-factory/control-flow-execute-data-flow-activity). Bonyolultabb forgatókönyvek esetén az adatok feldolgozhatók egyéni kódokkal is. Például: Python vagy R code.

Számos gyakori módszer áll rendelkezésre a Azure Data Factory az adatfeldolgozás során történő átalakítására. Minden technikának van olyan előnye és hátránya, amely meghatározza, hogy megfelelő-e az adott használati esethez:

| Módszer | Szakemberek számára | Hátrányok |
| ----- | ----- | ----- |
| ADF + Azure Functions | Kis késleltetés, kiszolgáló nélküli számítás</br>Állapot-nyilvántartó függvények</br>Újrafelhasználható függvények | Csak rövid ideig futó feldolgozás esetén jó |
| ADF + egyéni összetevő | Nagyméretű párhuzamos számítástechnika</br>Nagy teljesítményű algoritmusokhoz | Kód becsomagolása végrehajtható fájlba</br>A függőségek és az i/o kezelési bonyolultsága |
| ADF + Azure Databricks notebook | Apache Spark</br>Natív Python-környezet | Költséges lehet</br>A fürtök létrehozása kezdetben időt vesz igénybe, és késleltetést okoz

## <a name="adf-with-azure-functions"></a>ADF az Azure functions használatával

![ADF – függvény](media/how-to-data-ingest-adf/adf-function.png)

Azure Functions lehetővé teszi, hogy kis mennyiségű kódot (functions) futtasson anélkül, hogy az alkalmazás-infrastruktúrával kellene foglalkoznia. Ebben a beállításban az Azure-függvénybe becsomagolt egyéni Python-kóddal dolgozza fel az adatfeldolgozást. 

A függvényt az [ADF Azure Function tevékenység](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity)hívja meg. Ez a megközelítés jó választás az egyszerűsített adatátalakításokhoz. 

* Szakemberek
    * Az adatok feldolgozása kiszolgáló nélküli számításban történik, viszonylag alacsony késéssel
    * Az ADF-folyamat olyan [tartós Azure-függvényt](/azure/azure-functions/durable/durable-functions-overview) hívhat meg, amely kifinomult Adatátalakítási folyamatot valósít meg 
    * Az adatok átalakításának részleteit az Azure-függvény elküldi, amely a többi helyről újra felhasználható és meghívható
* Hátránya
    * Az ADF-sel való használat előtt létre kell hozni a Azure Functions.
    * Azure Functions csak rövid ideig futó adatfeldolgozás esetén jó

## <a name="adf-with-custom-component-activity"></a>ADF egyéni összetevő-tevékenységgel

![ADF – customcomponent](media/how-to-data-ingest-adf/adf-customcomponent.png)

Ebben a beállításban az adatfeldolgozást egyéni Python-kóddal, végrehajtható fájlba csomagolja a rendszer. A rendszer az [Egyéni ADF-összetevő tevékenységével](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity)hívja meg. Ez a megközelítés jobban illeszkedik az előző technikánál nagyobb méretű adatmennyiséghez.

* Szakemberek
    * Az adat [Azure batch](https://docs.microsoft.com/azure/batch/batch-technical-overview) készletre lesz feldolgozva, amely nagy léptékű párhuzamos és nagy teljesítményű számítástechnikai lehetőségeket biztosít
    * Nehéz algoritmusok futtatására és jelentős adatmennyiségek feldolgozására is használható.
* Hátránya
    * Azure Batch készletet az ADF-sel való használat előtt létre kell hozni
    * A Python-kód egy végrehajtható fájlba való becsomagolásával kapcsolatos mérnöki tevékenység. A függőségek és a bemeneti/kimeneti paraméterek összetettsége

## <a name="adf-with-azure-databricks-python-notebook"></a>ADF Azure Databricks Python notebooktal

![ADF – databricks](media/how-to-data-ingest-adf/adf-databricks.png)

A [Azure Databricks](https://azure.microsoft.com/services/databricks/) egy Apache Spark-alapú elemzési platform a Microsoft Cloud-ban.

Ebben a technikában az adatátalakítást egy Azure Databricks-fürtön futó [Python-jegyzetfüzet](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)hajtja végre. Ez valószínűleg a leggyakoribb megközelítés, amely kihasználja egy Azure Databricks szolgáltatás teljes erejét. A szolgáltatás nagy léptékű elosztott adatfeldolgozásra lett tervezve.

* Szakemberek
    * Az adatfeldolgozás a leghatékonyabban használt Azure-szolgáltatásra épül, amelyet Apache Spark környezet készít biztonsági mentésre
    * A Python natív támogatása az adatelemzési keretrendszerek és kódtárak mellett, beleértve a TensorFlow, a PyTorch és a scikit – Learn
    * Nincs szükség a Python-kód functions vagy végrehajtható modulokba való becsomagolására. A kód a következőképpen működik.
* Hátránya
    * Az ADF-sel való használat előtt létre kell hozni Azure Databricks-infrastruktúrát
    * Költséges lehet a Azure Databricks konfigurációjától függően
    * A számítási fürtök "hideg" módból való elforgatása hosszabb időt vesz igénybe, amely nagy késleltetést biztosít a megoldásnak 
    

## <a name="consuming-data-in-azure-machine-learning-pipelines"></a>Azure Machine Learning-folyamatok adatfeldolgozása

![pénzmosás – adatkészlet](media/how-to-data-ingest-adf/aml-dataset.png)

Az ADF-folyamatból származó átalakított adatok adattárolóba (például Azure Blobba) lesznek mentve. [A Azure Machine learning](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores) adattárolók és [adatkészletek](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)használatával érheti el ezeket az adatokat.

Minden alkalommal, amikor az ADF-folyamat fut, a rendszer az adattárolást egy másik helyre menti. Ha át szeretné adni a helyet a Azure Machine Learningnak, az ADF-folyamat meghívja a Azure Machine Learning folyamatot. A ML-folyamat meghívásakor az adatok helye és a futtatási azonosító paraméterként lesz elküldve. A ML-folyamat Ezután létrehoz egy adattárt/adatkészletet az adathelyen. 

> [!TIP]
> Az adatkészletek [támogatják a verziószámozást](https://docs.microsoft.com/azure/machine-learning/how-to-version-track-datasets), így a ml-folyamat képes regisztrálni az adatkészlet új verzióját, amely az ADF-folyamat legutóbbi adatait mutat.

Ha az adat egy adattáron vagy adatkészleten keresztül érhető el, egy ML-modell betanítására is használható. Előfordulhat, hogy a betanítási folyamat az ADF-ből meghívott azonos ML folyamat része. Vagy lehet különálló folyamat, például kísérletezés egy Jupyter jegyzetfüzetben.

Mivel az adatkészletek támogatják a verziószámozást, és a folyamat minden futtatása új verziót hoz létre, könnyen megtudhatja, hogy az adatok melyik verzióját használták a modell betanításához.

## <a name="next-steps"></a>Következő lépések

* [Databricks-jegyzetfüzet futtatása Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)
* [Adatok elérése az Azure Storage-szolgáltatásokban](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)
* [Modellek betanítása adatkészletekkel Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets)
* [DevOps adatfeldolgozási folyamathoz](https://docs.microsoft.com/azure/machine-learning/how-to-cicd-data-ingestion)

