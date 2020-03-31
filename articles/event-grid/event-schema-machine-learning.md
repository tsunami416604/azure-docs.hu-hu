---
title: Azure Event Grid Machine Learning eseményséma
description: A Machine Learning Workspace-események hez az Azure Event Grid del biztosított tulajdonságok ismertetése
services: event-grid
author: jenns
ms.service: event-grid
ms.topic: reference
ms.date: 10/18/2019
ms.author: jenns
ms.openlocfilehash: 4051598a9abd787f6707e67a8c4dab12fc6d626a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79202144"
---
# <a name="azure-event-grid-event-schema-for-azure-machine-learning"></a>Azure Event Grid eseménysémája az Azure Machine Learninghez

Ez a cikk a gépi tanulási munkaterületi események tulajdonságait és sémáját tartalmazza. Az eseménysémák bemutatása az [Azure Event Grid eseménysémájában.](event-schema.md)

A mintaparancsfájlok és oktatóanyagok listáját az [AzureML eseményforrása](event-sources.md#azure-machine-learning)tartalmazza.

## <a name="available-event-types"></a>Elérhető eseménytípusok

Az Azure Machine Learning a következő eseménytípusokat bocsátja ki:

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft.MachineLearningServices.ModelRegisztrált | Új modell vagy modell verzió sikeres regisztrálása esetén merült fel. |
| Microsoft.MachineLearningServices.ModelDeployed | Ha a modell(ek) sikeresen telepítve van egy végpontra. |
| Microsoft.MachineLearningServices.Run Befejeződött | A futtatás sikeres befejezésekor előállt. |
| Microsoft.MachineLearningServices.DatasetDriftDetected | Ha az adatkészlet-eltolódásfigyelő elsodródást észlel. |
| Microsoft.MachineLearningServices.RunStatusChanged | Ha a futtatási állapot "sikertelen" lesz. |

## <a name="the-contents-of-an-event-response"></a>Az eseményre adott válasz tartalma

Esemény aktiválásakor az Event Grid szolgáltatás adatokat küld az adott eseményről az előfizetési végpontnak.

Ez a szakasz egy példát tartalmaz arra, hogy az adatok hogyan fognak kinézni az egyes eseményeknél.

### <a name="microsoftmachinelearningservicesmodelregistered-event"></a>Microsoft.MachineLearningServices.ModelRegisztrált esemény

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "models/sklearn_regression_model:20",
  "eventType": "Microsoft.MachineLearningServices.ModelRegistered",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ModelName": "sklearn_regression_model",
    "ModelVersion": 20,
    "ModelTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ModelProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesmodeldeployed-event"></a>Microsoft.MachineLearningServices.ModelDeployed esemény

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "endpoints/my-sklearn-service",
  "eventType": "Microsoft.MachineLearningServices.ModelDeployed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ServiceName": "my-sklearn-service",
    "ServiceComputeType": "ACI",
    "ModelIds": "sklearn_regression_model:1,sklearn_regression_model:2",
    "ServiceTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ServiceProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesruncompleted-event"></a>Microsoft.MachineLearningServices.RunBefejezett esemény

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunCompleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ExperimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "ExperimentName": "automl-local-regression",
    "RunId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "RunType": null,
    "RunTags": {},
    "RunProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesdatasetdriftdetected-event"></a>Microsoft.MachineLearningServices.DatasetDriftDetected esemény

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "datadrifts/{}/runs/{}",
  "eventType": "Microsoft.MachineLearningServices.DatasetDriftDetected",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "DataDriftId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef",
    "DataDriftName": "myDriftMonitor",
    "RunId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef_1571590300380",
    "BaseDatasetId": "3c56d136-0f64-4657-a0e8-5162089a88a3",
    "TargetDatasetId": "d7e74d2e-c972-4266-b5fb-6c9c182d2a74",
    "DriftCoefficient": 0.83503490684792081,
    "StartTime": "2019-07-04T00:00:00+00:00",
    "EndTime": "2019-07-05T00:00:00+00:00"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesrunstatuschanged-event"></a>Microsoft.MachineLearningServices.RunStatusChanged esemény

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunCompleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ExperimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "ExperimentName": "automl-local-regression",
    "RunId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "RunType": null,
    "RunTags": {},
    "RunProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    },
   "RunStatus": "failed"
   },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```




## <a name="event-properties"></a>Esemény tulajdonságai

Egy esemény legfelső szintű adatokat rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| témakör | sztring | Az eseményforrás teljes erőforráselérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| Tárgy | sztring | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| eventType | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| eventTime | sztring | Az esemény létrehozásának időpontja a szolgáltató UTC-ideje alapján. |
| id | sztring | Az esemény egyedi azonosítója |
| data | objektum | Blob tárolási esemény adatai. |
| dataVersion | sztring | Az adatobjektum sémaverziója. A sémaverziót a közzétevő határozza meg. |
| metadataVersion | sztring | Az esemény metaadatok sémaverziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Az értéket az Event Grid adja meg. |

Az adatobjektum az egyes eseménytípusokhoz a következő tulajdonságokkal rendelkezik:

### <a name="microsoftmachinelearningservicesmodelregistered"></a>Microsoft.MachineLearningServices.ModelRegisztrált

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| Modellnév | sztring | A regisztrált modell neve. |
| ModelVersion | sztring | A modell regisztrált verziója. |
| Modellcímkék | objektum | A regisztrált modell címkéi. |
| Modelltulajdonságai | objektum | A regisztrált modell tulajdonságai. |

### <a name="microsoftmachinelearningservicesmodeldeployed"></a>Microsoft.MachineLearningServices.ModelDeployed

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| ServiceName | sztring | Az üzembe helyezett szolgáltatás neve. |
| ServiceComputeType típus | sztring | Az üzembe helyezett szolgáltatás számítási típusa (pl. ACI, AKS). |
  | Modellazonosítók | sztring | A modellazonosítók vesszővel elválasztott listája. A szolgáltatásban üzembe helyezett modellek azonosítói. |
| ServiceTags | objektum | Az üzembe helyezett szolgáltatás címkéi. |
| Szolgáltatástulajdonságai | objektum | Az üzembe helyezett szolgáltatás tulajdonságai. |

### <a name="microsoftmachinelearningservicesruncompleted"></a>Microsoft.MachineLearningServices.Run Befejeződött

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| ExperimentId | sztring | Annak a kísérletnek az azonosítója, amelyhez a futtatás tartozik. |
| Kísérletneve | sztring | Annak a kísérletnek a neve, amelyhez a futtatás tartozik. |
| Futtatásazonosító | sztring | A futtatás befejeződött azonosítója. |
| RunType (Futtatástípusa) | sztring | A befejezett futtatás futtatási típusa. |
| RunTagok | objektum | A befejezett futtatás címkéi. |
| RunProperties (Futtatástulajdonságai) | objektum | A befejezett futtatás tulajdonságai. |

### <a name="microsoftmachinelearningservicesdatasetdriftdetected"></a>Microsoft.MachineLearningServices.DatasetDriftDetected

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| DataDriftId között | sztring | Az eseményt kiváltó adateltolódásfigyelő azonosítója. |
| DataDriftName (DataDriftName) | sztring | Az eseményt kiváltó adateltolódás-figyelő neve. |
| Futtatásazonosító | sztring | A futtatás adateltolódást észlelő azonosítója. |
| Alapadat-azonosító | sztring | A sodródás észlelésére használt alapadatkészlet azonosítója. |
| TargetDatasetId azonosító | sztring | A cél adatkészlet eltérésének észlelésére használt azonosítója. |
| DriftCoefficient | double | Az esemény kiváltó együtthatóeredménye. |
| StartTime | dátum/idő | A céladatkészlet-idősorok indítási időpontja, amely driftészlelést eredményezett.  |
| EndTime | dátum/idő | A céladatkészlet-idősorok befejezési időpontja, amely driftészlelést eredményezett. |

### <a name="microsoftmachinelearningservicesrunstatuschanged"></a>Microsoft.MachineLearningServices.RunStatusChanged

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| ExperimentId | sztring | Annak a kísérletnek az azonosítója, amelyhez a futtatás tartozik. |
| Kísérletneve | sztring | Annak a kísérletnek a neve, amelyhez a futtatás tartozik. |
| Futtatásazonosító | sztring | A futtatás befejeződött azonosítója. |
| RunType (Futtatástípusa) | sztring | A befejezett futtatás futtatási típusa. |
| RunTagok | objektum | A befejezett futtatás címkéi. |
| RunProperties (Futtatástulajdonságai) | objektum | A befejezett futtatás tulajdonságai. |
| RunStatus (Futtatásállapota) | sztring | A Futtatás állapota. |

## <a name="next-steps"></a>További lépések

* Az Azure Event Grid bemutatása a [Mi az eseményrács?](overview.md)
* Az Azure Event Grid-előfizetés ek létrehozásáról az [Event Grid-előfizetésséma](subscription-creation-schema.md) című témakörben talál további információt.
* Az Azure Event Grid Azure Machine Learning használatával kapcsolatos bemutatásaz [Azure Machine Learning-események fogyasztása](/azure/machine-learning/service/concept-event-grid-integration) című témakörben
* Az Azure Event Grid azure Machine Learning használatával kapcsolatos példa: [Eseményvezérelt gépi tanulási munkafolyamatok létrehozása](/azure/machine-learning/service/how-to-use-event-grid)
