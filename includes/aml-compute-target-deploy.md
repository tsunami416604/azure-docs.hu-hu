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
ms.openlocfilehash: e402989c6bdbbc90ae10047fcf19f4f2d74328a6
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014771"
---
| Számítási célt | Használat | GPU-támogatás | FPGA-támogatás | Leírás |
| ----- | ----- | ----- | ----- | ----- |
| [Helyi&nbsp;webszolgáltatás&nbsp;](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Tesztelés/hibakeresés | &nbsp; | &nbsp; | Korlátozott teszteléshez és hibaelhárításhoz. A hardveres gyorsítás a helyi rendszer könyvtárainak használatával függ.
| [Notebook VM&nbsp;webszolgáltatás&nbsp;](../articles/machine-learning/service/how-to-deploy-and-where.md#notebookvm) | Tesztelés/hibakeresés | &nbsp; | &nbsp; | Korlátozott teszteléshez és hibaelhárításhoz. 
| [Az Azure Kubernetes Service (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Valós idejű következtetés |  [igen](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)  | [igen](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Megfelelő választás a nagy méretű éles környezetekben üzemelő példányok. Gyors válaszidőt és automatikus skálázást biztosít a telepített szolgáltatás számára. A fürt automatikus skálázása nem támogatott a Azure Machine Learning SDK-n keresztül. Az AK-fürt csomópontjainak módosításához használja a Azure Portalban az AK-fürt felhasználói felületét. Az egyazon lehetőség a vizualizációs felületen csak az AK-ban érhető el. |
| [Az Azure Container Instances (aci Szolgáltatásban)](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Tesztelés vagy fejlesztés | &nbsp;  | &nbsp; | Alacsony méretekben, CPU-alapú számítási feladatokhoz, amelyekhez < 48 GB RAM szükséges |
| [Az Azure Machine Learning Compute](../articles/machine-learning/service/how-to-run-batch-predictions.md) | Előnézet Batch&nbsp;-következtetés | &nbsp; | &nbsp;  | Kötegelt pontozás futtatása kiszolgáló nélküli számítási feladatokhoz. A a normál és az alacsony prioritású virtuális gépeket támogatja. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | Előnézet IoT&nbsp;modul |  &nbsp; | &nbsp; | & Üzembe helyezése IoT-eszközökön ML-modelleket szolgál ki. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | via IoT Edge |  &nbsp; | igen | & Üzembe helyezése IoT-eszközökön ML-modelleket szolgál ki. |

> [!NOTE]
> A számítási célok, például a helyi, a jegyzetfüzet-alapú virtuális gépek és a Azure Machine Learning számítási támogatás a GPU-t a betanításhoz és kísérletezéshez, a GPU for következtetés használata csak az Azure Kubernetes szolgáltatásban támogatott.