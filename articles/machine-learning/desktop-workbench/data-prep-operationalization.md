---
title: Részletes útmutató az Azure Machine Learning adatok előkészített Operationalization használatával |} Microsoft Docs
description: Ez a dokumentum nyújt részleteket korábban végrehajtó tervezett adatforrások és az előkészített csomagokat
services: machine-learning
author: hughz
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/13/2018
ms.openlocfilehash: 0849747fe6d66d55d11c131b51b07d8f689774e1
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="data-preparation-operationalization"></a>Adatok előkészítése Operationalization 

## <a name="operationalization-scenario"></a>Operationalization forgatókönyv

Az alábbi táblázat a különböző adatok előkészítése Operationalization forgatókönyvek, amelyek között a felhasználó tudta.

### <a name="develop-your-model-based-on-training-data"></a>A betanítási adatokon alapuló modellezése

Tegyük fel, hogy szeretné létrehozni és telepíteni a valós idejű pontozási API-szolgáltatás. Az első lépés, hogy a modellezése pontozó néhány betanítási adatok alapján. Adatok Prep importálja a betanítási adatok mintát új adatforrásként. Az adatok készen áll a testreszabásra, miután a DataPrep csomag előkészítési lépéseket tartalmazza. Az AzureML kísérletezhet fiókot használ, a felhasználó többször is a címkék az egyes bemeneti generálásához. a betanítási adatok a gépi tanulási modellt.

A felhasználó szolgáltatások adatokat kell frissíteni, mint a DataPrep csomaghoz új módon az előkészítés, és mentse azokat a lépéseket adja vissza. A új szolgáltatások létrehozása és a gépi tanulási modellek tökéletesítse iterációs folyamat továbbra is fennáll, addig, amíg a modell pontosan pontszámaihoz teszt adataikat. 

### <a name="deploy-your-model-to-the-azure-model-management-service"></a>A modell rendszerbe állítása a modell Azure szolgáltatás

Most már rendelkezik, amely valós idejű pontozása szeretné ügyféladatokat. Az Azure modell Management szolgáltatással, telepítheti a modell a AzureML Python parancssori szolgáltatásként. Az üzemelő szolgáltatás elérhetővé teszi a felhasználói adatok fogadására a valós idejű REST-végpont, és a betanított modell címkéi ad vissza, a megfelelő kimeneti.

Könnyű használatra a modell végpont JSON formátumú adatokat fogad el. A bemeneti az adatokat, amelyeket a ReadJSONLiteral átalakítása keresztül történő továbbítása során, és DataPrep adatforrás alakítja át a 2-dimenziós tömböt képviselő JSON karakterláncnak kell lennie. Az előkészítő adatcsomag során a kísérleti fázisban használatával létrehozott, majd az adatfolyamként továbbított JSON-adatok alapján hajtható végre. Az eredményül kapott dataframe majd átadhatók a betanított modell pontozó.

## <a name="read-json-literal-transformation"></a>Olvassa el a JSON-szövegkonstans átalakítása

### <a name="description"></a>Leírás

Operationalization célokra adatok Prep tartalmaz egy **ReadJsonLiteral** tevékenység végrehajtása, és egy Pandas generálásához átalakítási vagy Spark Dataframe. Ez a transzformáció egyedileg veszi bemenetként meglévő előkészítése csomag és JSON adatforrás. A transzformáció a DataPrep Python parancssori felületen keresztül kommunikál.

### <a name="instructions"></a>Utasítások

#### <a name="pythoncli"></a>PythonCLI

Az Azure Machine Learning munkaterület, nyissa meg a parancssori felület (Fájl > Nyissa meg a parancssorba).

Ebben a példában a TrainingPreparationSteps előkészítő adatcsomag szolgál az előkészítés, és adja hozzá a kötet szolgáltatást minden egyes bemeneti.

```
>>> import azureml.dataprep.package as azdp

>>> azdp.run_on_data.__doc__
"Generate a dataframe based on a selected dataflow in a package using in-memory data sources.
'user_config' is expected as a dictionary that maps the absolute path of a dsource file to an in-memory data source represented as a list of lists."

>>> real_time_customer_data = [[1.0, 1.5, 2.5], [2.5, 1.5, 3]]
>>> azdp.run_on_data({ "C:\\TrainingSampleData.dsource": real_time_customer_data}, "C:\\TrainingPreparationSteps.dprep")
    Height   Width   Depth  Volumne
0   1.0       1.5    2.5    3.75
1   2.5       1.5    3.0    11.25
```

`run_on_data()` a következő választható paraméterek:
 - `dataflow_idx`: Adja meg a csomagban végrehajtani kívánt adatfolyamblokk indexe
 - `secrets`: titkos tároló szótár (kulcs: secretId, érték: tárolt titkos kulcs)
 - `spark`: Adjon meg egy spark-munkamenetet a spark végrehajtás

#### <a name="input"></a>Input (Bemenet)

A 2-dimenziós tömböt adjon meg ("tömbökből álló tömb"). A Python a bemeneti listák listájának kell lennie. JSON nem tartozik egy natív dátum adattípusú, Python datetime.datetime objektumokat dátumot ISO-formátumú karakterlánccá kell konvertálni. A megadott REST-végpontok, egy JSON literál karakterlánc, amely a 2-D JSON-tömb lehet.

#### <a name="output"></a>Kimenet

Egy Pandas vagy Spark DataFrame. DataFrame típusa határozza meg a kiválasztott a futtatási konfigurációs keretrendszer (`.runconfig`). Az eredményül kapott dataframe pontozó a betanított modell bemenetként függvénynek adható át.
