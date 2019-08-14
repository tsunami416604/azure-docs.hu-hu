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
ms.date: 05/30/2019
ms.openlocfilehash: c5a6f957cd3f799f9d7eff1df22df2200779ac30
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946042"
---
| Számítási célt | Használat | GPU-támogatás | FPGA-támogatás | Leírás |
| ----- | ----- | ----- | ----- | ----- |
| [Helyi&nbsp;webszolgáltatás&nbsp;](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Tesztelés/hibakeresés | talán | &nbsp; | Korlátozott teszteléshez és hibaelhárításhoz. A hardveres gyorsítás a helyi rendszer könyvtárainak használatával függ.
| [Notebook VM&nbsp;webszolgáltatás&nbsp;](../articles/machine-learning/service/how-to-deploy-and-where.md#notebookvm) | Tesztelés/hibakeresés | talán | &nbsp; | Korlátozott teszteléshez és hibaelhárításhoz. 
| [Az Azure Kubernetes Service (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Valós idejű következtetés |  [igen](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)  | [igen](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Megfelelő választás a nagy méretű éles környezetekben üzemelő példányok. Gyors válaszidőt és automatikus skálázást biztosít a telepített szolgáltatás számára. A fürt automatikus skálázása nem támogatott a Azure Machine Learning SDK-n keresztül. Az AK-fürt csomópontjainak módosításához használja a Azure Portalban az AK-fürt felhasználói felületét. Az egyazon lehetőség a vizualizációs felületen csak az AK-ban érhető el. |
| [Az Azure Container Instances (aci Szolgáltatásban)](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Tesztelés vagy fejlesztés | &nbsp;  | &nbsp; | Alacsony méretekben, CPU-alapú számítási feladatokhoz, amelyekhez < 48 GB RAM szükséges |
| [Az Azure Machine Learning Compute](../articles/machine-learning/service/how-to-run-batch-predictions.md) | Előnézet Batch&nbsp;-következtetés | igen | &nbsp;  | Kötegelt pontozás futtatása kiszolgáló nélküli számítási feladatokhoz. A a normál és az alacsony prioritású virtuális gépeket támogatja. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | Előnézet IoT&nbsp;modul |  &nbsp; | &nbsp; | & Üzembe helyezése IoT-eszközökön ML-modelleket szolgál ki. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | via IoT Edge |  &nbsp; | igen | & Üzembe helyezése IoT-eszközökön ML-modelleket szolgál ki. |
