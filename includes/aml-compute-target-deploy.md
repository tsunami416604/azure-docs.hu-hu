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
ms.openlocfilehash: 722893fca90e5a5d0958d1de1698b625af4a5e21
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497004"
---
| Számítási cél | Alkalmazási cél | GPU-támogatás | FPGA-támogatás | Leírás |
| ----- | ----- | ----- | ----- | ----- |
| [Helyi&nbsp;web&nbsp;szolgáltatás](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Tesztelés/hibakeresés | &nbsp; | &nbsp; | Korlátozott teszteléshez és hibaelhárításhoz használható. A hardveres gyorsítás a helyi rendszer könyvtárainak használatából függ.
| [Azure Machine Learning számítási példány&nbsp;web&nbsp;Service](../articles/machine-learning/service/how-to-deploy-and-where.md#notebookvm) | Tesztelés/hibakeresés | &nbsp; | &nbsp; | Korlátozott teszteléshez és hibaelhárításhoz használható.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Valós idejű következtetés |  [Igen](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md) (webszolgáltatás üzembe helyezése) | [Igen](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Nagy léptékű üzembe helyezések esetén használható. Gyors válaszidőt és automatikus skálázást biztosít a telepített szolgáltatás számára. A fürt automatikus skálázása nem támogatott a Azure Machine Learning SDK-n keresztül. Az AK-fürt csomópontjainak módosításához használja a Azure Portalban az AK-fürt felhasználói felületét. A tervező számára az egyazon lehetőség áll rendelkezésre. |
| [Azure Container Instances](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Tesztelés vagy fejlesztés | &nbsp;  | &nbsp; | Alacsony léptékű CPU-alapú számítási feladatokhoz használható, amelyek kevesebb mint 48 GB RAM memóriát igényelnek. |
| [Számítási fürtök Azure Machine Learning](../articles/machine-learning/service/how-to-run-batch-predictions.md) | Előnézet Batch&nbsp;következtetés | [Igen](../articles/machine-learning/service/how-to-run-batch-predictions.md) (gépi tanulási folyamat) | &nbsp;  | Kötegelt pontozás futtatása kiszolgáló nélküli számítási feladatokhoz. A a normál és az alacsony prioritású virtuális gépeket támogatja. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | Előnézet IoT&nbsp;modul |  &nbsp; | &nbsp; | ML modellek üzembe helyezése és kiszolgálása a IoT-eszközökön. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | IoT Edge keresztül |  &nbsp; | Igen | ML modellek üzembe helyezése és kiszolgálása a IoT-eszközökön. |

> [!NOTE]
> Bár a számítási célok, például a helyi, Azure Machine Learning számítási példányok, és a Azure Machine Learning számítási fürtök támogatják a GPU-t a betanításhoz és kísérletezéshez, a GPU for bevezetést, __Ha a szolgáltatást webszolgáltatásként telepítik__ , csak az Azure-ban támogatott Kubernetes szolgáltatás.
>
> __Ha a Machine learning-folyamattal való pontozást__ csak Azure Machine learning számításokban támogatja, GPU-t használ a következtetéshez.