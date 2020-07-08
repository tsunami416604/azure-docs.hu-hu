---
title: Azure Machine Learning Event Grid forrásként
description: A Machine Learning-munkaterület eseményekhez megadott tulajdonságokat ismerteti Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: f77a76d6acb67c739e0adf186d23e9b16ff7e2ee
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82928873"
---
# <a name="azure-machine-learning-as-an-event-grid-source"></a>Azure Machine Learning Event Grid forrásként

Ez a cikk a Machine learning-munkaterület eseményeinek tulajdonságait és sémáját ismerteti. Az események sémáinak bemutatása: [Azure Event Grid Event Schema](event-schema.md).

## <a name="event-grid-event-schema"></a>Event Grid-eseményséma

### <a name="available-event-types"></a>Elérhető események típusai

Azure Machine Learning a következő típusú eseményeket bocsátja ki:

| Eseménytípus | Description |
| ---------- | ----------- |
| Microsoft. MachineLearningServices. ModelRegistered | Új modell vagy modell verziójának sikeres regisztrálásakor következik be. |
| Microsoft. MachineLearningServices. ModelDeployed | Akkor következik be, amikor a modell (ek) sikeresen telepítve lett egy végpontra. |
| Microsoft. MachineLearningServices. RunCompleted | A Futtatás sikeres befejeződése után következik be. |
| Microsoft. MachineLearningServices. DatasetDriftDetected | Akkor következik be, amikor egy adatkészlet drift figyelője észleli a driftet. |
| Microsoft. MachineLearningServices. RunStatusChanged | Akkor következik be, amikor a futtatási állapot "Failed" értékre változik. |

### <a name="the-contents-of-an-event-response"></a>Egy eseményre adott válasz tartalma

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

### <a name="event-properties"></a>Esemény tulajdonságai

Egy esemény a következő legfelső szintű adattal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| témakör | sztring | Az eseményforrás teljes erőforrás-elérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| tulajdonos | sztring | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| eventType | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| eventTime | sztring | Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor. |
| id | sztring | Az esemény egyedi azonosítója. |
| adatok | objektum | BLOB Storage-események |
| dataVersion | sztring | Az adatobjektum sémaverziója. A sémaverziót a közzétevő határozza meg. |
| metadataVersion | sztring | Az esemény metaadatok sémaverziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Az értéket az Event Grid adja meg. |

Az adatobjektum minden eseménytípus esetében a következő tulajdonságokkal rendelkezik:

### <a name="microsoftmachinelearningservicesmodelregistered"></a>Microsoft. MachineLearningServices. ModelRegistered

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| Modellnév | sztring | A regisztrált modell neve. |
| ModelVersion | sztring | A regisztrált modell verziója. |
| ModelTags | objektum | A regisztrált modell címkéi. |
| ModelProperties | objektum | A regisztrált modell tulajdonságai. |

### <a name="microsoftmachinelearningservicesmodeldeployed"></a>Microsoft. MachineLearningServices. ModelDeployed

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| ServiceName | sztring | A telepített szolgáltatás neve. |
| ServiceComputeType | sztring | A központilag telepített szolgáltatás számítási típusa (pl. ACI, ak). |
  | ModelIds | sztring | A modell-azonosítók vesszővel tagolt listája. A szolgáltatásban üzembe helyezett modellek azonosítói. |
| ServiceTags | objektum | A telepített szolgáltatás címkéi. |
| ServiceProperties | objektum | A telepített szolgáltatás tulajdonságai. |

### <a name="microsoftmachinelearningservicesruncompleted"></a>Microsoft. MachineLearningServices. RunCompleted

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| ExperimentId | sztring | Annak a kísérletnek az azonosítója, amelyhez a Futtatás tartozik. |
| ExperimentName | sztring | Annak a kísérletnek a neve, amelyhez a Futtatás tartozik. |
| RunId | sztring | A befejezett Futtatás azonosítója. |
| RunType | sztring | A befejezett Futtatás típusa. |
| RunTags | objektum | A befejezett futtatások címkéi. |
| RunProperties | objektum | A befejezett Futtatás tulajdonságai. |

### <a name="microsoftmachinelearningservicesdatasetdriftdetected"></a>Microsoft. MachineLearningServices. DatasetDriftDetected

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| DataDriftId | sztring | Az eseményt kiváltó adatdrift-figyelő azonosítója. |
| DataDriftName | sztring | Az eseményt kiváltó adatdrift-figyelő neve. |
| RunId | sztring | Az adateltolódást észlelő Futtatás azonosítója. |
| BaseDatasetId | sztring | A drift észleléséhez használt alapadatkészlet azonosítója. |
| TargetDatasetId | sztring | A drift észleléséhez használt cél adatkészlet azonosítója. |
| DriftCoefficient | double | Az eseményt kiváltó együtthatós eredmény. |
| StartTime | dátum/idő | A cél adatkészlet idősorozatának kezdési időpontja, amely a drift észlelését eredményezte.  |
| EndTime | dátum/idő | A cél adatkészlet idősorozatának befejezési időpontja, amely a drift észlelését eredményezte. |

### <a name="microsoftmachinelearningservicesrunstatuschanged"></a>Microsoft. MachineLearningServices. RunStatusChanged

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| ExperimentId | sztring | Annak a kísérletnek az azonosítója, amelyhez a Futtatás tartozik. |
| ExperimentName | sztring | Annak a kísérletnek a neve, amelyhez a Futtatás tartozik. |
| RunId | sztring | A befejezett Futtatás azonosítója. |
| RunType | sztring | A befejezett Futtatás típusa. |
| RunTags | objektum | A befejezett futtatások címkéi. |
| RunProperties | objektum | A befejezett Futtatás tulajdonságai. |
| RunStatus | sztring | A Futtatás állapota. |

## <a name="tutorials-and-how-tos"></a>Oktatóanyagok és útmutatók
| Cím | Leírás |
| ----- | ----- |
| [Azure Machine Learning események felhasználása](../machine-learning/concept-event-grid-integration.md) | A Azure Machine Learning és a Event Grid integrálásának áttekintése. |

## <a name="next-steps"></a>További lépések

* A Azure Event Grid bemutatása: [Mi az Event Grid?](overview.md)
* Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetési séma](subscription-creation-schema.md)
* A Azure Event Grid és a Azure Machine Learning használatának bemutatása: [Azure Machine learning események felhasználása](/azure/machine-learning/concept-event-grid-integration)
* A Azure Event Grid és a Azure Machine Learning használatát bemutató példát az [Event Driven Machine learning-munkafolyamatok létrehozása](/azure/machine-learning/how-to-use-event-grid) című témakörben talál.
