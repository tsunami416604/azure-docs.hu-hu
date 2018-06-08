---
title: Az Azure Machine Learning modell adatok gyűjtemény API-referencia |} Microsoft Docs
description: Az Azure Machine Learning modell adatok gyűjtemény API-hivatkozás.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: ff80130ebb9c4095d4a20202cdfabd9aaf1b1992
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34832006"
---
# <a name="azure-machine-learning-model-data-collection-api-reference"></a>Az Azure Machine Learning modell adatok gyűjtemény API-referencia

Modell az adatgyűjtés lehetővé teszi, hogy archiválja a modell a be- és a machine learning-webszolgáltatás által. Tekintse meg a [modell adatok gyűjtése – útmutató útmutató](how-to-use-model-data-collection.md) annak megértése, hogyan telepítendő `azureml.datacollector` a Windows és Linux rendszerű számítógépen.

Az API-referencia útmutatóban a megközelítésnek modell a be- és előrejelzéseket gyűjtésének a machine learning-webszolgáltatás használjuk.

## <a name="enable-model-data-collection-in-azure-ml-workbench-environment"></a>Modell az adatgyűjtést az Azure ML munkaterület környezetben

 Keresse meg conda\_dependencies.yml a projekt aml_config mappában lévő fájlt, és rendelkezik a conda\_dependencies.yml fájl tartalmazza a pip szakaszban azureml.datacollector modul alább látható módon. Megjegyzés: Ez csak a teljes conda alszakasz\_dependencies.yml fájlt:

    dependencies:
      - python=3.5.2
      - pip:
        - azureml.datacollector==0.1.0a13

>[!NOTE] 
>Jelenleg Azure ML munkaterület az adatokat gyűjtő moduljának is használhatja, ha a docker módban. Helyi módú nem feltétlenül alkalmas minden környezetben.




## <a name="enable-model-data-collection-in-the-scoring-file"></a>A pontozási fájlban modell adatgyűjtés engedélyezése

Használatos operationalization pontozási fájlban az adatokat gyűjtő moduljának és ModelDataCollector osztály importálása:

    from azureml.datacollector import ModelDataCollector


## <a name="model-data-collector-instantiation"></a>Modell adatokat gyűjtő által okozott
Egy új példányát egy ModelDataCollector hozható létre:

    dc = ModelDataCollector(model_name, identifier='default', feature_names=None, model_management_account_id='unknown', webservice_name='unknown', model_id='unknown', model_version='unknown')

A Részletek területen osztály és a paraméter:

### <a name="class"></a>Osztály
| Name (Név) | Leírás |
|--------------------|--------------------|
| ModelDataCollector | Egy osztály azureml.datacollector névtérben. Ez az osztály példánya használandó modell adatok gyűjtéséért felelős ügyfélfeladatot. A pontozási egyetlen fájl tartalmazhat több ModelDataCollectors. Adatgyűjtés a pontozási fájlban egy különálló helyen, hogy az összegyűjtött adatokat a séma konzisztensek maradnak minden példányt kell használni (Ez azt jelenti, hogy a be- és előrejelzés)|


### <a name="parameters"></a>Paraméterek

| Name (Név) | Típus | Leírás |
|-------------|------------|-------------------------|
| model_name | karakterlánc | a modell, mely adatokat gyűjtenek neve |
| Azonosítója | karakterlánc | a hely a kódot, amely ezeket az adatokat, azaz azonosítja "RawInput" vagy "Előrejelző" |
| feature_names | karakterlánc-listával | a csv-fejléc, amikor váló szolgáltatás nevek listája |
| model_management_account_id | karakterlánc | a modell-felügyeleti fiókja, ez a modell tároló azonosítója. Ez a telepítéskor automatikusan amikor modellek vannak operationalized AML keresztül |
| webservice_name | karakterlánc | a neve, amelyhez ez a modell jelenleg telepítve van a webszolgáltatás. Ez a telepítéskor automatikusan amikor modellek vannak operationalized AML keresztül |
| model_id | karakterlánc | A modellből az adott környezetben a modell felügyeleti fiók egyedi azonosítója. Ez a telepítéskor automatikusan amikor modellek vannak operationalized AML keresztül |
| model_version | karakterlánc | Ez a modell egy modell felügyeleti fiók a környezetében verziószámát. Ez a telepítéskor automatikusan amikor modellek vannak operationalized AML keresztül |



 

## <a name="collecting-the-model-data"></a>A modell adatok gyűjtése

A modell adatai, a fentiekben létrehozott ModelDataCollector példányának használatával hozhatja létre.

    dc.collect(input_data, user_correlation_id="")

A Részletek területen metódus és a paraméter:

### <a name="method"></a>Módszer
| Name (Név) | Leírás |
|--------------------|--------------------|
| gyűjtése | A modell beviteli vagy előrejelzés adatok gyűjtéséért felelős ügyfélfeladatot|


### <a name="parameters"></a>Paraméterek

| Name (Név) | Típus | Leírás |
|-------------|------------|-------------------------|
| input_data | többféle | az adatok összegyűjtésére (a típusok listája, numpy.array, pandas jelenleg elfogad. DataFrame, pyspark.sql.DataFrame). A dataframe típusú fejléc létezik, amelynek a neve, ha ezt az információt szerepel az adatok cél (anélkül, hogy kifejezetten kéri a ModelDataCollector konstruktorban neve) |
| user_correlation_id | karakterlánc | egy nem kötelező korrelációs azonosítója, amely az előrejelzés összefüggéseket a felhasználó által megadható |

