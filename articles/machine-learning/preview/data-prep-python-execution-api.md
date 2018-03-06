---
title: "Részletes útmutató az Azure Machine Learning adatok előkészített végrehajtási API használatával |} Microsoft Docs"
description: "Ez a dokumentum nyújt részleteket korábban végrehajtó tervezett adatforrások és az előkészített csomagokat"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 36814d238aabd12e7cc6947809c135130002eb46
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2018
---
# <a name="execute-data-sources-and-data-preparations-packages-from-python"></a>Az adatforrások és az előkészített csomagokat végre Python

Az Azure Machine Learning adatforrások vagy az Azure Machine Learning adatok előkészített csomag belül Python használata:

1. Lépjen a **adatok** a projekt fülre.

2. Kattintson a jobb gombbal a megfelelő forrás.

3. Válasszon **adatfájl hozzáférési kódot létrehozni.**

Ez a művelet létrehoz egy rövid Python-parancsfájl, amely végrehajtja a csomag és egy dataframe adja vissza.

## <a name="data-sources"></a>Adatforrások

A `azureml.dataprep.datasource` modul hajtható végre egy adatforrást, és térjen vissza a dataframe egyetlen függvényt tartalmaz: `load_datasource(path, secrets=None, spark=None)`.
- `path` az adatforrás (.dsource fájl) elérési útja van.
- `secrets` van egy választható könyvtár, amely leképezhető titkos kulcsok.
- `spark` van egy nem kötelező logikai érték, amely megadja, hogy egy Spark dataframe vagy egy Pandas dataframe vissza. Alapértelmezés szerint Azure Machine Learning-munkaterület meghatározza, hogy milyen típusú dataframe környezet alapján futásidőben adja vissza.

## <a name="data-preparations-packages"></a>Adatok előkészített csomagokat

A `azureml.dataprep.package` modul három olyan függvényeket tartalmaz, az adatfolyam végrehajtani egy előkészített adatok csomagból.

### <a name="execution-functions"></a>Végrehajtási funkciók

- `submit(package_path, dataflow_idx=0, secrets=None, spark=None)` elküldi az adott adatfolyam végrehajtásra, de nem ad vissza egy dataframe.
- `run(package_path, dataflow_idx=0, secrets=None, spark=None)` a megadott adatfolyam fut, és adja vissza az eredményeket egy dataframe.
- `run_on_data(user_config, package_path, dataflow_idx=0, secrets=None, spark=None)` a megadott adatfolyam egy memórián belüli adatforrás alapján fut, és adja vissza az eredményeket egy dataframe. A `user_config` argumentum, amely egy adatforrás (.dsource fájl) abszolút elérési útja leképezhető dictionary listáinak listáját-ként memórián belüli adatforráshoz.

### <a name="common-arguments"></a>Általános argumentumok

- `package_path` az adatok előkészített alkalmazáscsomag (.dprep fájl) elérési útja van.
- `dataflow_idx` a rendszer mely adatfolyam a csomag végrehajtása nulla alapú indexét. Ha a megadott adatfolyam adatforrásokat, illetve más adatfolyamok hivatkozik, a végre is.
- `secrets` van egy választható könyvtár, amely leképezhető titkos kulcsok.
- `spark` van egy nem kötelező logikai érték, amely megadja, hogy egy Spark dataframe vagy egy Pandas dataframe vissza. Alapértelmezés szerint munkaterület meghatározza, hogy milyen típusú dataframe környezet alapján futásidőben adja vissza.
