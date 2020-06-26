---
title: fájlbefoglalás
description: fájlbefoglalás
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 06/25/2020
ms.openlocfilehash: 82234719320f1ac707147c7c8393c0464956dd99
ms.sourcegitcommit: dfa5f7f7d2881a37572160a70bac8ed1e03990ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2020
ms.locfileid: "85378616"
---
| Számítási cél | Alkalmazási cél | GPU-támogatás | FPGA-támogatás | Description |
| ----- | ----- | ----- | ----- | ----- |
| [Helyi &nbsp; webszolgáltatás &nbsp;](../articles/machine-learning/how-to-deploy-and-where.md#local) | Tesztelés/hibakeresés | &nbsp; | &nbsp; | Korlátozott teszteléshez és hibaelhárításhoz használható. A hardveres gyorsítás a helyi rendszer könyvtárainak használatából függ.
| [Azure Machine Learning számítási példány &nbsp; &nbsp; webszolgáltatása](../articles/machine-learning/how-to-deploy-and-where.md#notebookvm) | Tesztelés/hibakeresés | &nbsp; | &nbsp; | Korlátozott teszteléshez és hibaelhárításhoz használható.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-and-where.md#aks) | Valós idejű következtetés |  [Igen](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (webszolgáltatás üzembe helyezése) | [Igen](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Nagy léptékű üzembe helyezések esetén használható. Gyors válaszidőt és automatikus skálázást biztosít a telepített szolgáltatás számára. A fürt automatikus skálázása nem támogatott a Azure Machine Learning SDK-n keresztül. Az AK-fürt csomópontjainak módosításához használja a Azure Portalban az AK-fürt felhasználói felületét. A tervező számára az egyazon lehetőség áll rendelkezésre. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-and-where.md#aci) | Tesztelés vagy fejlesztés | &nbsp;  | &nbsp; | Alacsony léptékű CPU-alapú számítási feladatokhoz használható, amelyek kevesebb mint 48 GB RAM memóriát igényelnek. |
| [Számítási fürtök Azure Machine Learning](../articles/machine-learning/how-to-use-parallel-run-step.md) | Batch- &nbsp; következtetés | [Igen](../articles/machine-learning/how-to-use-parallel-run-step.md) (gépi tanulási folyamat) | &nbsp;  | Kötegelt pontozás futtatása kiszolgáló nélküli számítási feladatokhoz. A a normál és az alacsony prioritású virtuális gépeket támogatja. |
| [Azure Functions](../articles/machine-learning/how-to-deploy-functions.md) | Előnézet Valós idejű következtetés | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | Előnézet IoT &nbsp; modul |  &nbsp; | &nbsp; | ML modellek üzembe helyezése és kiszolgálása a IoT-eszközökön. |
| [Azure Data Box Edge](../articles/databox-online/azure-stack-edge-overview.md)   | IoT Edge keresztül |  &nbsp; | Yes | ML modellek üzembe helyezése és kiszolgálása a IoT-eszközökön. |

> [!NOTE]
> Bár a számítási célok, például a helyi, Azure Machine Learning számítási példányok, és a Azure Machine Learning számítási fürtök támogatják a GPU-t a betanításhoz és kísérletezéshez, a GPU for bevezetést, __Ha a szolgáltatást webszolgáltatásként telepítik__ , csak az Azure Kubernetes szolgáltatásban támogatott.
>
> __Ha a Machine learning-folyamattal való pontozást__ csak Azure Machine learning számításokban támogatja, GPU-t használ a következtetéshez.