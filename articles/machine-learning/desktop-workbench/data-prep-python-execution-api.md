---
title: Részletes útmutató az Azure Machine Learning adat előkészített végrehajtási API használatával |} A Microsoft Docs
description: Ez a dokumentum nyújt részleteket végrehajtása korábban tervezett adatforrások és az adat-Előkészítések csomagok
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 9f15f949ba76240da2788c1a01e7086ba3b42fd1
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35645574"
---
# <a name="execute-data-sources-and-data-preparations-packages-from-python"></a>Hajtsa végre az adatforrások és az adat-Előkészítések csomagokat a Pythonnal

Az Azure Machine Learning-adatforrások vagy az Azure Machine Learning adat előkészített csomag belül Python használata:

1. Nyissa meg a **adatok** a projekt fülre.

2. Kattintson a jobb gombbal a megfelelő forrás.

3. Válasszon **adathozzáférési kódfájl létrehozása.**

Ez a művelet létrehoz egy rövid Python-szkriptet, amely végrehajtja a csomagot, és dataframe adja vissza.

## <a name="data-sources"></a>Adatforrások

A `azureml.dataprep.datasource` modult tartalmaz egy adott függvény végrehajtásához egy adatforrást, és vissza dataframe: `load_datasource(path, secrets=None, spark=None)`.
- `path` az adatforrás (.dsource fájl) elérési útja van.
- `secrets` van egy nem kötelező szótár kulcsok, titkos kulcsok leképező.
- `spark` van egy nem kötelező bool, amely megadja, hogy a Spark dataframe vagy Pandas dataframe adnak vissza. Alapértelmezés szerint az Azure Machine Learning Workbench meghatározza, hogy milyen típusú dataframe futásidőben környezet alapján adja vissza.

## <a name="data-preparations-packages"></a>Adat-Előkészítések csomagok

A `azureml.dataprep.package` modul három függvényeket tartalmaz, amelyek végrehajtása az adatfolyam egy adat-Előkészítések csomagból.

### <a name="execution-functions"></a>Végrehajtási funkciók

- `submit(package_path, dataflow_idx=0, secrets=None, spark=None)` elküldi a megadott folyamat végrehajtásához, de nem ad vissza egy adathalmaz.
- `run(package_path, dataflow_idx=0, secrets=None, spark=None)` a megadott folyamat fut, és adja vissza az eredményeket adathalmazaként.
- `run_on_data(user_config, package_path, dataflow_idx=0, secrets=None, spark=None)` a megadott adatfolyam egy memórián belüli adatforrás alapján fut, és visszaadja az eredményeket adathalmazaként. A `user_config` argumentum értéke egy szótár, amely hozzárendeli az abszolút elérési út egy adatforrás (.dsource fájl) egy memórián belüli adatforráshoz listáinak listáját jelöli.

### <a name="common-arguments"></a>Gyakori argumentumok

- `package_path` az útvonal az adat-Előkészítések csomaghoz (.dprep fájlt).
- `dataflow_idx` a rendszer melyik adatfolyamot a csomag végrehajtásához a nulla alapú indexét. Ha a megadott adatfolyam hivatkozik, más adatfolyam-gyűjteményre vagy az adatforrásokat, ezeket is végrehajtja.
- `secrets` van egy nem kötelező szótár kulcsok, titkos kulcsok leképező.
- `spark` van egy nem kötelező bool, amely megadja, hogy a Spark dataframe vagy Pandas dataframe adnak vissza. Alapértelmezés szerint Workbench meghatározza, hogy milyen típusú dataframe futásidőben környezet alapján adja vissza.
