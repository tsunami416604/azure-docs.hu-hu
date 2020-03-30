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
ms.openlocfilehash: ba1e0eee5de65364d8e5e762195f7de98384c2a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76122583"
---
| Számítási cél | Alkalmazási cél | GPU-támogatás | FPGA támogatás | Leírás |
| ----- | ----- | ----- | ----- | ----- |
| [Helyi&nbsp;&nbsp;webszolgáltatás](../articles/machine-learning/how-to-deploy-and-where.md#local) | Tesztelés/hibakeresés | &nbsp; | &nbsp; | Korlátozott teszteléshez és hibaelhárításhoz használható. A hardveres gyorsítás a könyvtárak helyi rendszerben való használatától függ.
| [Azure Machine Learning&nbsp;számítási&nbsp;példány webes szolgáltatása](../articles/machine-learning/how-to-deploy-and-where.md#notebookvm) | Tesztelés/hibakeresés | &nbsp; | &nbsp; | Korlátozott teszteléshez és hibaelhárításhoz használható.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-and-where.md#aks) | Valós idejű következtetés |  [Igen](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (webszolgáltatás telepítése) | [Igen](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Nagy méretű éles környezetekben használható. Gyors válaszidőt és az üzembe helyezett szolgáltatás automatikus skálázását biztosítja. Fürt automatikus skálázás nem támogatott az Azure Machine Learning SDK.Cluster autoscaling isn't supported through the Azure Machine Learning SDK. Az AKS-fürt csomópontjainak módosításához használja az AKS-fürt felhasználói felületét az Azure Portalon. Az AKS az egyetlen lehetőség a tervező számára. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-and-where.md#aci) | Tesztelés vagy fejlesztés | &nbsp;  | &nbsp; | Olyan alacsony méretű CPU-alapú számítási feladatokhoz használható, amelyek 48 GB-nál kevesebb RAM-ot igényelnek. |
| [Azure Machine Learning számítási fürtök](../articles/machine-learning/how-to-use-parallel-run-step.md) | (Előzetes verzió) Kötegkövetkeztetés&nbsp; | [Igen](../articles/machine-learning/how-to-use-parallel-run-step.md) (gépi tanulási folyamat) | &nbsp;  | Kötegelt pontozás futtatása kiszolgáló nélküli számítási rendszeren. Támogatja a normál és alacsony prioritású virtuális gépek. |
| [Azure Functions](../articles/machine-learning/how-to-deploy-functions.md) | (Előzetes verzió) Valós idejű következtetés | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | (Előzetes verzió) IoT&nbsp;modul |  &nbsp; | &nbsp; | Ml-modellek üzembe helyezése és kiszolgálása IoT-eszközökön. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | IoT edge-en keresztül |  &nbsp; | Igen | Ml-modellek üzembe helyezése és kiszolgálása IoT-eszközökön. |

> [!NOTE]
> Bár a számítási célok, például a helyi, az Azure Machine Learning számítási példány és az Azure Machine Learning számítási fürtök támogatják a GPU-t a betanításhoz és a kísérletezéshez, a GPU-t a __következtetéshez, ha webszolgáltatásként üzembe helyezve__ csak az Azure Kubernetes-szolgáltatás támogatja.
>
> A GPU-t a következtetés, __ha egy gépi tanulási folyamat__ csak az Azure Machine Learning Compute támogatott.