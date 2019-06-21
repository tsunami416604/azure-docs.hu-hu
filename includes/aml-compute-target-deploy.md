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
ms.openlocfilehash: 59e5adb5d1ee35b983c9552a2076a373561c0b44
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67168261"
---
| Számítási célt | Használat | GPU-támogatással | FPGA-támogatás | Leírás |
| ----- | ----- | ----- | ----- | ----- |
| [Local&nbsp;web&nbsp;service](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Tesztelés és hibakeresés | Talán | &nbsp; | Megfelelő választás a korlátozott tesztelés és hibaelhárítás. A hardveres gyorsítás attól függ, a helyi rendszer kódtárak használatával.
| [Az Azure Kubernetes Service (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Valós idejű következtetésekhez |  [igen](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)  | [igen](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Megfelelő választás a nagy méretű éles környezetekben üzemelő példányok. Automatikus skálázást és gyors válaszidők biztosít.  Az AKS csak a lehetőség érhető el a vizuális felhasználói felületet. |
| [Az Azure Container Instances (aci Szolgáltatásban)](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Tesztelési vagy fejlesztési | &nbsp;  | &nbsp; | Lépték esetén a CPU-alapú igénylő számítási feladatok számára hasznos < 48 GB RAM |
| [Az Azure Machine Learning Compute](../articles/machine-learning/service/how-to-run-batch-predictions.md) | (Előzetes verzió) A batch&nbsp;következtetésekhez | igen | &nbsp;  | Futtassa a kötegelt pontozási a kiszolgáló nélküli számítási. A normál és alacsony prioritású virtuális gépeket támogatja. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | (Előzetes verzió) IoT&nbsp;modul |  &nbsp; | &nbsp; | Üzembe helyezés, és gépi Tanulási modelleket szolgálnak az IoT-eszközökön. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | via IoT Edge |  &nbsp; | igen | Üzembe helyezés, és gépi Tanulási modelleket szolgálnak az IoT-eszközökön. |
