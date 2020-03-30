---
title: Egy ml-folyamat futtatása egy logikai alkalmazásból
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan indíthatja el egy ml-folyamat futtatását az Azure Logic Apps használatával.
services: machine-learning
author: sanpil
ms.author: sanpil
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 6bb976b8b310fb3eb4d0247a8d745599f688d7b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122856"
---
# <a name="trigger-a-run-of-a-machine-learning-pipeline-from-a-logic-app"></a>Machine Learning-folyamat futtatásának aktiválása logikai alkalmazásból

Indítsa el az Azure Machine Learning-folyamat futtatását, amikor új adatok jelennek meg. Például érdemes lehet a folyamat ot egy új modell betanításához, amikor új adatok jelennek meg a blob storage-fiókban. Állítsa be az eseményindítót az [Azure Logic Apps](../logic-apps/logic-apps-overview.md)alkalmazásokkal.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Machine Learning-munkaterület. További információ: [Create an Azure Machine Learning workspace](how-to-manage-workspace.md).

* A REST-végpont egy közzétett Machine Learning-folyamat. [Hozza létre és tegye közzé a folyamatot.](how-to-create-your-first-pipeline.md) Ezután keresse meg a közzétett folyamat folyamatának REST-végpontját a folyamatazonosító használatával:
    
     ```
    # You can find the pipeline ID in Azure Machine Learning studio
    
    published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
    published_pipeline.endpoint 
    ```
* [Azure blob storage](../storage/blobs/storage-blobs-overview.md) az adatok tárolásához.
* A munkaterület egy [adattár,](how-to-access-data.md) amely tartalmazza a blob storage-fiók részleteit.

## <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása

Most hozzon létre egy Azure Logic App-példányt. [Azure Logic App](../logic-apps/logic-apps-overview.md) Ha szeretné, [használjon integrációs szolgáltatási környezetet (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) és [állítson be egy ügyfél által felügyelt kulcsot](../logic-apps/customer-managed-keys-integration-service-environment.md) a logikai alkalmazás általi használatra.

A logikai alkalmazás kiépítése után az alábbi lépésekkel konfigurálhatja a folyamat eseményindítóját:

1. [Hozzon létre egy rendszer által hozzárendelt felügyelt identitást,](../logic-apps/create-managed-service-identity.md) hogy az alkalmazás hozzáférést biztosítson az Azure Machine Learning-munkaterülethez.

1. Nyissa meg a Logic App Designer nézetet, és válassza ki az Üres logikai alkalmazás sablont. 
    > [!div class="mx-imgBorder"]
    > ![Üres sablon](media/how-to-trigger-published-pipeline/blank-template.png)

1. A Tervezőben keressen rá a **blobra.** Válassza ki a **Blob hozzáadásakor vagy módosításakor (csak tulajdonságok)** eseményindító, és adja hozzá ezt az eseményindítót a logikai alkalmazáshoz.
    > [!div class="mx-imgBorder"]
    > ![Trigger hozzáadása](media/how-to-trigger-published-pipeline/add-trigger.png)

1. Töltse ki a blobtár-fiók kapcsolatadatait, amelyet figyelni szeretne a blob-kiegészítések vagy -módosítások miatt. Válassza ki a figyelni kívánt tárolót. 
 
    Válassza ki az **Intervallum** és **gyakoriság** lehetőséget az Ön számára megfelelő frissítések lekérdezéséhez.  

    > [!NOTE]
    > Ez az eseményindító figyeli a kijelölt tárolót, de nem figyeli az almappákat.

1. Adjon hozzá egy HTTP-műveletet, amely új vagy módosított blob észlelésekor fog futni. Válassza a **+ Új lépés**lehetőséget, majd keresse meg és jelölje ki a HTTP műveletet.

  > [!div class="mx-imgBorder"]
  > ![HTTP-művelet keresése](media/how-to-trigger-published-pipeline/search-http.png)

  A művelet konfigurálásához használja az alábbi beállításokat:

  | Beállítás | Érték | 
  |---|---|
  | HTTP-művelet | POST |
  | URI |a végpontot a közzétett folyamathoz, amelyet [előfeltételként](#prerequisites) talált |
  | Hitelesítési módszer | Felügyelt identitás |

1. Állítsa be az ütemezést, hogy beállítsa a [DataPath PipelineParameters](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) lehet, hogy:

    ```json
    "DataPathAssignments": { 
         "input_datapath": { 
                            "DataStoreName": "<datastore-name>", 
                            "RelativePath": "@triggerBody()?['Name']" 
    } 
    }, 
    "ExperimentName": "MyRestPipeline", 
    "ParameterAssignments": { 
    "input_string": "sample_string3" 
    },
    ```

    `DataStoreName` A munkaterülethez hozzáadott feltételt [használja.](#prerequisites)
     
    > [!div class="mx-imgBorder"]
    > ![HTTP-beállítások](media/how-to-trigger-published-pipeline/http-settings.png)

1. Válassza a **Mentés** lehetőséget, és az ütemezés készen áll.