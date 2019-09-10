---
title: fájl belefoglalása
description: fájl belefoglalása
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/23/2019
ms.openlocfilehash: 29da4ab949cf38419bc7a4d0a3b5da6669441887
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70847695"
---
| Számítási célt | Alkalmazási cél | GPU-támogatás | FPGA-támogatás | Leírás |
| ----- | ----- | ----- | ----- | ----- |
| [Helyi&nbsp;webszolgáltatás&nbsp;](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Tesztelés/hibakeresés | &nbsp; | &nbsp; | Korlátozott teszteléshez és hibaelhárításhoz használható. A hardveres gyorsítás a helyi rendszer könyvtárainak használatából függ.
| [Notebook VM&nbsp;webszolgáltatás&nbsp;](../articles/machine-learning/service/how-to-deploy-and-where.md#notebookvm) | Tesztelés/hibakeresés | &nbsp; | &nbsp; | Korlátozott teszteléshez és hibaelhárításhoz használható.
| [Az Azure Kubernetes Service (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Valós idejű következtetés |  [Igen](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md) (webszolgáltatás központi telepítése) | [Igen](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Nagy léptékű üzembe helyezések esetén használható. Gyors válaszidőt és automatikus skálázást biztosít a telepített szolgáltatás számára. A fürt automatikus skálázása nem támogatott a Azure Machine Learning SDK-n keresztül. Az AK-fürt csomópontjainak módosításához használja a Azure Portalban az AK-fürt felhasználói felületét. Az egyazon lehetőség a vizualizációs felületen csak az AK-ban érhető el. |
| [Azure Container Instances](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Tesztelés vagy fejlesztés | &nbsp;  | &nbsp; | Alacsony léptékű CPU-alapú számítási feladatokhoz használható, amelyek kevesebb mint 48 GB RAM memóriát igényelnek. |
| [Az Azure Machine Learning Compute](../articles/machine-learning/service/how-to-run-batch-predictions.md) | Előnézet Batch&nbsp;-következtetés | [Igen](../articles/machine-learning/service/how-to-run-batch-predictions.md) (gépi tanulási folyamat) | &nbsp;  | Kötegelt pontozás futtatása kiszolgáló nélküli számítási feladatokhoz. A a normál és az alacsony prioritású virtuális gépeket támogatja. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | Előnézet IoT&nbsp;modul |  &nbsp; | &nbsp; | ML modellek üzembe helyezése és kiszolgálása a IoT-eszközökön. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | IoT Edge keresztül |  &nbsp; | Igen | ML modellek üzembe helyezése és kiszolgálása a IoT-eszközökön. |

> [!NOTE]
> Bár a számítási célok, például a helyi, a jegyzetfüzet-alapú virtuális gépek és a Azure Machine Learning a számítási támogatás GPU-t a betanításhoz és kísérletezéshez, GPU-t használ, __Ha webszolgáltatásként telepítik__ , és csak az Azure Kubernetes szolgáltatásban támogatottak.
>
> __Ha a Machine learning-folyamattal való pontozást__ csak Azure Machine learning számításokban támogatja, GPU-t használ a következtetéshez.