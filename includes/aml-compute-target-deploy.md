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
ms.openlocfilehash: 944b96e7726f2b2becd5960a17a89c00d00c878a
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2020
ms.locfileid: "91841950"
---
A modell üzemeltetéséhez használt számítási cél a telepített végpont költségeit és rendelkezésre állását is befolyásolja. A táblázat segítségével kiválaszthatja a megfelelő számítási célt.

| Számítási cél | Alkalmazási cél | GPU-támogatás | FPGA-támogatás | Leírás |
| ----- | ----- | ----- | ----- | ----- |
| [Helyi &nbsp; webszolgáltatás &nbsp;](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | Tesztelés/hibakeresés | &nbsp; | &nbsp; | Korlátozott teszteléshez és hibaelhárításhoz használható. A hardveres gyorsítás a helyi rendszer könyvtárainak használatából függ.
| [Azure Machine Learning számítási példány &nbsp; &nbsp; webszolgáltatása](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | Tesztelés/hibakeresés | &nbsp; | &nbsp; | Korlátozott teszteléshez és hibaelhárításhoz használható.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-azure-kubernetes-service.md) | Valós idejű következtetés |  [Igen](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (webszolgáltatás üzembe helyezése) | [Igen](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Nagy léptékű üzembe helyezések esetén használható. Gyors válaszidőt és automatikus skálázást biztosít a telepített szolgáltatás számára. A fürt automatikus skálázása nem támogatott a Azure Machine Learning SDK-n keresztül. Az AK-fürt csomópontjainak módosításához használja a Azure Portalban az AK-fürt felhasználói felületét. A tervező számára az egyazon lehetőség áll rendelkezésre. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-azure-container-instance.md) | Tesztelés vagy fejlesztés | &nbsp;  | &nbsp; | Alacsony léptékű CPU-alapú számítási feladatokhoz használható, amelyek kevesebb mint 48 GB RAM memóriát igényelnek. |
| [Az Azure Machine Learning számítási fürtjei](../articles/machine-learning/how-to-use-parallel-run-step.md) | Batch- &nbsp; következtetés | [Igen](../articles/machine-learning/how-to-use-parallel-run-step.md) (gépi tanulási folyamat) | &nbsp;  | Kötegelt pontozás futtatása kiszolgáló nélküli számítási feladatokhoz. A a normál és az alacsony prioritású virtuális gépeket támogatja. |
| [Azure Functions](../articles/machine-learning/how-to-deploy-functions.md) | Előnézet Valós idejű következtetés | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | Előnézet IoT &nbsp; modul |  &nbsp; | &nbsp; | Gépi tanulási modellek üzembe helyezése és kiszolgálása a IoT-eszközökön. |
| [Azure Data Box Edge](../articles/databox-online/azure-stack-edge-overview.md)   | IoT Edge használatával |  &nbsp; | Igen | Gépi tanulási modellek üzembe helyezése és kiszolgálása a IoT-eszközökön. |

> [!NOTE]
> Bár a számítási célok, például a helyi, Azure Machine Learning számítások, és a Azure Machine Learning számítási fürtök támogatják a GPU-t a betanításhoz és kísérletezéshez, GPU-t használ, _Ha webszolgáltatásként való üzembe helyezés esetén_ csak az AK-ban támogatott.
>
> _Ha a Machine learning-folyamattal való pontozást_ csak Azure Machine learning számításokban támogatja, GPU-t használ a következtetéshez.

> [!NOTE]
> * A Container instances csak az 1 GB-nál kisebb méretű kisméretű modellek esetében alkalmas.
> * Használjon egycsomópontos AK-fürtöket nagyobb modellek fejlesztéséhez és teszteléséhez.