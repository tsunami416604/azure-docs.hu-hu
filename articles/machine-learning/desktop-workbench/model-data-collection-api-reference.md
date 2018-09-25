---
title: Az Azure Machine Learning modell adatok gyűjtési API-referencia |} A Microsoft Docs
description: Azure Machine Learning modell adatok gyűjtési API-referencia
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ROBOTS: NOINDEX
ms.openlocfilehash: c047555c30481f34b607197f71483197bc64620c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961467"
---
# <a name="azure-machine-learning-model-data-collection-api-reference"></a>Az Azure Machine Learning modell adatok gyűjtési API-referencia

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 

A modelladatok gyűjtésének lehetővé teszi modellbemenetek és előrejelzések Machine learning-webszolgáltatás archiválhatja. Tekintse meg a [modell adatok gyűjtése a gyakorlati útmutató](how-to-use-model-data-collection.md) való telepítése `azureml.datacollector` a Windows és Linux rendszerű gépen.

Az API-referencia útmutatóban a lépésekre bontott eljárással modellbemenetek és előrejelzések gyűjtésének a Machine learning-webszolgáltatást használunk.

## <a name="enable-model-data-collection-in-azure-ml-workbench-environment"></a>Az Azure ML Workbench környezetben a modelladatok gyűjtésének engedélyezése

 Keressen a conda\_dependencies.yml fájlt a projektben a aml_config mappában, és rendelkezik a conda\_dependencies.yml fájl tartalmazza a azureml.datacollector modul a pip szakaszában alább látható módon. Vegye figyelembe, hogy ez csak egy teljes conda alszakasz\_dependencies.yml fájlt:

    dependencies:
      - python=3.5.2
      - pip:
        - azureml.datacollector==0.1.0a13

>[!NOTE] 
>Jelenleg az Azure Machine Learning Workbench a adatokat gyűjtő moduljának is használhatja, ha a docker módban. Helyi módú nem feltétlenül alkalmas összes környezetet.




## <a name="enable-model-data-collection-in-the-scoring-file"></a>A pontozófájlt a modelladatok gyűjtésének engedélyezése

Az operacionalizálás használt pontozási fájlban a ModelDataCollector osztály és a adatokat gyűjtő moduljának importálása:

    from azureml.datacollector import ModelDataCollector


## <a name="model-data-collector-instantiation"></a>Modell data collector példányosítás
Hozza létre egy új példányát a modelldatacollector osztályt:

    dc = ModelDataCollector(model_name, identifier='default', feature_names=None, model_management_account_id='unknown', webservice_name='unknown', model_id='unknown', model_version='unknown')

Az osztály és a paraméter részletek:

### <a name="class"></a>Osztály
| Name (Név) | Leírás |
|--------------------|--------------------|
| ModelDataCollector | Egy osztály azureml.datacollector névtérben. Ez az osztály egy példányát a modelladatok gyűjtése használható. Egyetlen pontozófájl több ModelDataCollectors is tartalmazhat. Adatgyűjtés a pontozófájl egy különálló helyen, hogy a séma az összegyűjtött adatok konzisztensek maradnak minden példányt kell használni (azt jelenti, bemeneti és előrejelzési)|


### <a name="parameters"></a>Paraméterek

| Name (Név) | Típus | Leírás |
|-------------|------------|-------------------------|
| model_name | sztring | mely adatokat gyűjtenek a modell neve |
| azonosító | sztring | Ezeket az adatokat, azaz beazonosító kód helyét "RawInput" vagy "Előrejelzési" |
| feature_names | karakterláncok | a szolgáltatás nevét, amelyek a csv-fejléc, ha a megadott listája |
| model_management_account_id | sztring | Ez a modell tárolódnak a modellkezelési fiók azonosítója. Automatikusan ennek kitöltése során a modellek üzembe AML keresztül helyezte azt |
| webservice_name | sztring | a neve, amelyhez ez a modell jelenleg telepítve van a webszolgáltatás. Automatikusan ennek kitöltése során a modellek üzembe AML keresztül helyezte azt |
| model_id | sztring | Ez a modell egy modellkezelési fiók környezetében egyedi azonosítója. automatikusan ennek kitöltése során a modellek üzembe AML keresztül helyezte azt |
| model_version | sztring | Ez a modell egy modellkezelési fiók környezetében verziószáma. Automatikusan ennek kitöltése során a modellek üzembe AML keresztül helyezte azt |



 

## <a name="collecting-the-model-data"></a>A modelladatok gyűjtése

A modell egy példányát a fent létrehozott ModelDataCollector adatokat gyűjthet.

    dc.collect(input_data, user_correlation_id="")

Módszer és a paraméter részletek:

### <a name="method"></a>Módszer
| Name (Név) | Leírás |
|--------------------|--------------------|
| gyűjtése | Használja a modell bemenet vagy előrejelzési összegyűjtése|


### <a name="parameters"></a>Paraméterek

| Name (Név) | Típus | Leírás |
|-------------|------------|-------------------------|
| input_data | többféle típus | az összegyűjtendő adatok (a típusainak listája, numpy.array, pandas jelenleg elfogad. Adathalmaz, pyspark.sql.DataFrame). Adathalmaz esetében, ha fejléc már létezik, a szolgáltatások neveit, ezeket az információkat tartalmazza az adatok cél (anélkül, hogy explicit módon továbbíthat funkciónevek a ModelDataCollector konstruktor) |
| user_correlation_id | sztring | egy választható korrelációs azonosítója, amelyet a felhasználó korrelációját, ha az előrejelzési adható meg |

