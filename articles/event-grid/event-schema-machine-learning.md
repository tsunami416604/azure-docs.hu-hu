---
title: Azure Event Grid Machine Learning esemény sémája
description: A Machine Learning-munkaterület eseményekhez megadott tulajdonságokat ismerteti Azure Event Grid
services: event-grid
author: jenns
ms.service: event-grid
ms.topic: reference
ms.date: 10/18/2019
ms.author: jenns
ms.openlocfilehash: 4051598a9abd787f6707e67a8c4dab12fc6d626a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79202144"
---
# <a name="azure-event-grid-event-schema-for-azure-machine-learning"></a>Azure Machine Learning Azure Event Gridi esemény sémája

Ez a cikk a Machine learning-munkaterület eseményeinek tulajdonságait és sémáját ismerteti. Az események sémáinak bemutatása: [Azure Event Grid Event Schema](event-schema.md).

A mintául szolgáló parancsfájlok és oktatóanyagok listáját itt tekintheti meg: [AzureML-esemény forrása](event-sources.md#azure-machine-learning).

## <a name="available-event-types"></a>Elérhető események típusai

Azure Machine Learning a következő típusú eseményeket bocsátja ki:

| Esemény típusa | Leírás |
| ---------- | ----------- |
| Microsoft. MachineLearningServices. ModelRegistered | Új modell vagy modell verziójának sikeres regisztrálásakor következik be. |
| Microsoft. MachineLearningServices. ModelDeployed | Akkor következik be, amikor a modell (ek) sikeresen telepítve lett egy végpontra. |
| Microsoft. MachineLearningServices. RunCompleted | A Futtatás sikeres befejeződése után következik be. |
| Microsoft. MachineLearningServices. DatasetDriftDetected | Akkor következik be, amikor egy adatkészlet drift figyelője észleli a driftet. |
| Microsoft. MachineLearningServices. RunStatusChanged | Akkor következik be, amikor a futtatási állapot "Failed" értékre változik. |

## <a name="the-contents-of-an-event-response"></a>Egy eseményre adott válasz tartalma

Egy esemény indításakor a Event Grid szolgáltatás adatokat küld az eseményről a végpontra való feliratkozáshoz.

Ez a szakasz egy példát mutat be, hogy az egyes események milyen módon néznek ki.

### <a name="microsoftmachinelearningservicesmodelregistered-event"></a>Microsoft. MachineLearningServices. ModelRegistered esemény

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

### <a name="microsoftmachinelearningservicesmodeldeployed-event"></a>Microsoft. MachineLearningServices. ModelDeployed esemény

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

### <a name="microsoftmachinelearningservicesruncompleted-event"></a>Microsoft. MachineLearningServices. RunCompleted esemény

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

### <a name="microsoftmachinelearningservicesdatasetdriftdetected-event"></a>Microsoft. MachineLearningServices. DatasetDriftDetected esemény

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

### <a name="microsoftmachinelearningservicesrunstatuschanged-event"></a>Microsoft. MachineLearningServices. RunStatusChanged esemény

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

Egy esemény a következő legfelső szintű adattal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| topic | Karakterlánc | Az eseményforrás teljes erőforrás-elérési útja. Ez a mező nem írható. Event Grid megadja ezt az értéket. |
| subject | Karakterlánc | Közzétevő által megadott elérési út az esemény tárgya számára. |
| eventType | Karakterlánc | Az eseményforrás egyik regisztrált eseménytípus. |
| eventTime | Karakterlánc | Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor. |
| id | Karakterlánc | Az esemény egyedi azonosítója. |
| data | object | BLOB Storage-események |
| dataVersion | Karakterlánc | Az adatobjektum séma-verziója. A közzétevő határozza meg a séma verzióját. |
| metadataVersion | Karakterlánc | Az esemény metaadatainak séma-verziója. Event Grid a legfelső szintű tulajdonságok sémáját határozza meg. Event Grid megadja ezt az értéket. |

Az adatobjektum minden eseménytípus esetében a következő tulajdonságokkal rendelkezik:

### <a name="microsoftmachinelearningservicesmodelregistered"></a>Microsoft. MachineLearningServices. ModelRegistered

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| ModelName | Karakterlánc | A regisztrált modell neve. |
| ModelVersion | Karakterlánc | A regisztrált modell verziója. |
| ModelTags | object | A regisztrált modell címkéi. |
| ModelProperties | object | A regisztrált modell tulajdonságai. |

### <a name="microsoftmachinelearningservicesmodeldeployed"></a>Microsoft. MachineLearningServices. ModelDeployed

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| ServiceName | Karakterlánc | A telepített szolgáltatás neve. |
| ServiceComputeType | Karakterlánc | A központilag telepített szolgáltatás számítási típusa (pl. ACI, ak). |
  | ModelIds | Karakterlánc | A modell-azonosítók vesszővel tagolt listája. A szolgáltatásban üzembe helyezett modellek azonosítói. |
| ServiceTags | object | A telepített szolgáltatás címkéi. |
| ServiceProperties | object | A telepített szolgáltatás tulajdonságai. |

### <a name="microsoftmachinelearningservicesruncompleted"></a>Microsoft. MachineLearningServices. RunCompleted

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| ExperimentId | Karakterlánc | Annak a kísérletnek az azonosítója, amelyhez a Futtatás tartozik. |
| ExperimentName | Karakterlánc | Annak a kísérletnek a neve, amelyhez a Futtatás tartozik. |
| RunId | Karakterlánc | A befejezett Futtatás azonosítója. |
| RunType | Karakterlánc | A befejezett Futtatás típusa. |
| RunTags | object | A befejezett futtatások címkéi. |
| RunProperties | object | A befejezett Futtatás tulajdonságai. |

### <a name="microsoftmachinelearningservicesdatasetdriftdetected"></a>Microsoft. MachineLearningServices. DatasetDriftDetected

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| DataDriftId | Karakterlánc | Az eseményt kiváltó adatdrift-figyelő azonosítója. |
| DataDriftName | Karakterlánc | Az eseményt kiváltó adatdrift-figyelő neve. |
| RunId | Karakterlánc | Az adateltolódást észlelő Futtatás azonosítója. |
| BaseDatasetId | Karakterlánc | A drift észleléséhez használt alapadatkészlet azonosítója. |
| TargetDatasetId | Karakterlánc | A drift észleléséhez használt cél adatkészlet azonosítója. |
| DriftCoefficient | double | Az eseményt kiváltó együtthatós eredmény. |
| StartTime | dátum/idő | A cél adatkészlet idősorozatának kezdési időpontja, amely a drift észlelését eredményezte.  |
| EndTime | dátum/idő | A cél adatkészlet idősorozatának befejezési időpontja, amely a drift észlelését eredményezte. |

### <a name="microsoftmachinelearningservicesrunstatuschanged"></a>Microsoft. MachineLearningServices. RunStatusChanged

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| ExperimentId | Karakterlánc | Annak a kísérletnek az azonosítója, amelyhez a Futtatás tartozik. |
| ExperimentName | Karakterlánc | Annak a kísérletnek a neve, amelyhez a Futtatás tartozik. |
| RunId | Karakterlánc | A befejezett Futtatás azonosítója. |
| RunType | Karakterlánc | A befejezett Futtatás típusa. |
| RunTags | object | A befejezett futtatások címkéi. |
| RunProperties | object | A befejezett Futtatás tulajdonságai. |
| RunStatus | Karakterlánc | A Futtatás állapota. |

## <a name="next-steps"></a>További lépések

* A Azure Event Grid bemutatása: [Mi az Event Grid?](overview.md)
* Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetési séma](subscription-creation-schema.md)
* A Azure Event Grid és a Azure Machine Learning használatának bemutatása: [Azure Machine learning események felhasználása](/azure/machine-learning/service/concept-event-grid-integration)
* A Azure Event Grid és a Azure Machine Learning használatát bemutató példát az [Event Driven Machine learning-munkafolyamatok létrehozása](/azure/machine-learning/service/how-to-use-event-grid) című témakörben talál.
