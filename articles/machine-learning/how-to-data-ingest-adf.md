---
title: Adatok betöltése az Azure Data Factory segítségével
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan hozhat létre adatbetöltési folyamatot az Azure Data Factory segítségével.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78274787"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Adatok betöltése az Azure Data Factory segítségével

Ebben a cikkben megtudhatja, hogyan hozhat létre egy adatbetöltési folyamat ot az Azure Data Factory (ADF) használatával. Ez a folyamat az Azure Machine Learning használatával használható adatok betöltésére szolgál. Az Azure Data Factory lehetővé teszi, hogy könnyen kibontsa, átalakítsa és töltse be (ETL) adatokat. Miután az adatok at átalakították és betöltötték a tárolóba, a gépi tanulási modellek betanításához használható.

Az egyszerű adatátalakítás natív ADF-tevékenységekkel és eszközökkel , például [adatáramlással](https://docs.microsoft.com/azure/data-factory/control-flow-execute-data-flow-activity)kezelhető. Ha bonyolultabb forgatókönyvekről van szó, az adatok néhány egyéni kóddal is feldolgozhatók. Például Python vagy R kód.

Az Azure Data Factory számos gyakori módszerrel alakíthatja át az adatokat a betöltés során. Minden technika előnye és hátránya, hogy meghatározza, ha ez egy jó illeszkedés egy adott használati eset:

| Módszer | Előnyök | Hátrányok |
| ----- | ----- | ----- |
| ADF + Azure-függvények | Alacsony késleltetésű, kiszolgáló nélküli számítás</br>Állapotalapú funkciók</br>Újrafelhasználható funkciók | Csak rövid ideig futó feldolgozáshoz |
| ADF + egyéni összetevő | Nagyléptékű párhuzamos számítástechnika</br>Alkalmas nehéz algoritmusokhoz | Kód tördelése végrehajtható fájlba</br>A függőségek és az IO kezelésének összetettsége |
| ADF + Azure Databricks notebook | Apache Spark</br>Natív Python-környezet | Költséges lehet</br>A fürtök létrehozása kezdetben időt vesz igénybe, és késleltetést ad hozzá

## <a name="adf-with-azure-functions"></a>ADF Azure-funkciókkal

![adf-funkció](media/how-to-data-ingest-adf/adf-function.png)

Az Azure Functions lehetővé teszi, hogy kis kóddarabokat (függvényeket) futtasson anélkül, hogy aggódnia kellene az alkalmazás-infrastruktúra miatt. Ebben a beállításban az adatok feldolgozása egyéni Python-kód egy Azure-függvénybe csomagolva történik. 

A függvény meghívása az [ADF Azure-függvény tevékenység.](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity) Ez a megközelítés egy jó lehetőség a könnyű adatátalakítások. 

* Profik:
    * Az adatok feldolgozása kiszolgáló nélküli számításon történik, viszonylag alacsony késleltetéssel
    * Az ADF-folyamat meghívhat egy [tartós Azure-függvényt,](/azure/azure-functions/durable/durable-functions-overview) amely kifinomult adatátalakítási folyamatot valósíthat meg 
    * Az adatátalakítás részleteit az Azure Függvény absztrakttá teszi, amely más helyekről újra felhasználható és meghívható
* Hátránya:
    * Az Azure-függvényeket az ADF-fel való használat előtt létre kell hozni.
    * Az Azure Functions csak rövid ideig futó adatfeldolgozáshoz jó

## <a name="adf-with-custom-component-activity"></a>ADF egyéni összetevő-tevékenységgel

![adf-customcomponent](media/how-to-data-ingest-adf/adf-customcomponent.png)

Ebben a beállításban az adatok feldolgozása egyéni Python-kód egy végrehajtható fájlba csomagolva történik. A rendszer [ADF egyéni összetevő tevékenységgel](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity)hívja meg. Ez a megközelítés jobban illeszkedik a nagy méretű adatokhoz, mint az előző technika.

* Profik:
    * Az adatok feldolgozása az [Azure Batch-készleten](https://docs.microsoft.com/azure/batch/batch-technical-overview) történik, amely nagyméretű párhuzamos és nagy teljesítményű számítástechnikát biztosít
    * Nehéz algoritmusok futtatására és jelentős mennyiségű adat feldolgozására használható
* Hátránya:
    * Az ADF-fel való használat előtt létre kell hozni az Azure Batch-készletet
    * A Python-kód végrehajtható fájlba történő csomagolásával kapcsolatos mérnöki munka. A függőségek és a bemeneti/kimeneti paraméterek kezelésének összetettsége

## <a name="adf-with-azure-databricks-python-notebook"></a>ADF az Azure Databricks Python-jegyzetfüzettel

![adf-databricks](media/how-to-data-ingest-adf/adf-databricks.png)

[Az Azure Databricks](https://azure.microsoft.com/services/databricks/) egy Apache Spark-alapú elemzési platform a Microsoft felhőjében.

Ebben a technikában az adatátalakítást egy [Python-jegyzetfüzet](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)hajtja végre, amely egy Azure Databricks-fürtön fut. Valószínűleg ez a leggyakoribb megközelítés, amely kihasználja az Azure Databricks-szolgáltatás teljes erejét. Úgy tervezték, elosztott adatfeldolgozás i skála.

* Profik:
    * Az adatok a leghatékonyabb adatfeldolgozó Azure-szolgáltatáson átalakításra kerülnek, amelyről az Apache Spark-környezet nyújt biztonsági másolatot
    * A Python natív támogatása adatelemzési keretrendszerekkel és kódtárakkal együtt, beleértve a TensorFlow-t, a PyTorch-ot és a scikit-learn-t
    * Nincs szükség a Python-kód függvényekbe vagy végrehajtható modulokba való burkolására. A kód úgy működik, ahogy van.
* Hátránya:
    * Az Azure Databricks-infrastruktúrát az ADF-fel való használat előtt létre kell hozni
    * Az Azure Databricks konfigurációjától függően költséges lehet
    * A számítási fürtök "hideg" módból való felpörgetése némi időt vesz igénybe, amely nagy késleltetést hoz a megoldásba 
    

## <a name="consuming-data-in-azure-machine-learning-pipelines"></a>Adatok fogyasztása az Azure Machine Learning-folyamatokban

![aml-adatkészlet](media/how-to-data-ingest-adf/aml-dataset.png)

Az átalakított adatok az ADF-folyamat menti az adattároló (például az Azure Blob). Az Azure Machine Learning [adattárak](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores) és adatkészletek használatával érheti el ezeket az [adatokat.](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)

Minden alkalommal, amikor az ADF-folyamat fut, az adatok mentése egy másik helyre a tárolóban. A hely átadása az Azure Machine Learning, az ADF-folyamat meghívja az Azure Machine Learning-folyamat. Az ML-folyamat hívásakor az adatok helye és a futtatási azonosító paraméterekként lesz elküldve. A rendszermegtalálható klisésszefolyamat ezután létrehozhat egy adattárolót/adatkészletet az adathely használatával. 

> [!TIP]
> Az adatkészletek támogatják a [verzióverziózást,](https://docs.microsoft.com/azure/machine-learning/how-to-version-track-datasets)így a gépi tanulási folyamat regisztrálhatja az adatkészlet új verzióját, amely az ADF-folyamat legfrissebb adataira mutat.

Miután az adatok elérhetőegy adattárban vagy adatkészleten keresztül, használhatja a betanítása egy gépi tanulási modell. Előfordulhat, hogy a betanítási folyamat ugyanannak a hibajelző folyamatnak a része, amelyet az ADF-ből hívnak meg. Vagy lehet, hogy egy külön folyamat, mint a kísérletezés egy Jupyter notebook.

Mivel az adatkészletek támogatják a verzióverziózást, és a folyamatról való futtatásúj verziót hoz létre, könnyen érthető, hogy az adatok melyik verzióját használták a modell betanításához.

## <a name="next-steps"></a>További lépések

* [Databricks-jegyzetfüzet futtatása az Azure Data Factoryban](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)
* [Adatok elérése az Azure-tárolási szolgáltatásokban](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)
* [Modellek betanítása adatkészletekkel az Azure Machine Learningben](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets)
* [DevOps adatbetöltési folyamathoz](https://docs.microsoft.com/azure/machine-learning/how-to-cicd-data-ingestion)

