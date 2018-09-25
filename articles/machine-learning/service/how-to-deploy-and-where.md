---
title: Az üzembe helyezés modellek Azure Machine Learning szolgáltatással |} A Microsoft Docs
description: Ismerje meg a különböző lehetőségeket a modellek üzembe helyezhető éles környezetben az Azure Machine Learning szolgáltatás használatával.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/29/2018
ms.openlocfilehash: 606e8aed42bce69d5b3210b4e97f8cbfeaaf104c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961008"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Az Azure Machine Learning szolgáltatással modellek üzembe helyezése

Az Azure Machine Learning szolgáltatás a betanított modell telepíthet több módszert is biztosít. Ebből a dokumentumból megtudhatja, hogyan helyezi üzembe a modellt webszolgáltatásként, amely az Azure-felhőben, vagy az IoT edge-eszközök.

A következő számítási célnak modellek helyezhető üzembe:

- Az Azure Container Instances (aci Szolgáltatásban)
- Azure Kubernetes Service (AKS)
- Azure IoT Edge
- A mező-programmable gate array (FPGA)

Ez a dokumentum többi része ezen lehetőségek részletesen ismerteti.

## <a name="azure-container-instances"></a>Azure Container Instances

A modellek telepítéséről, egy REST API-végpont, ha egy vagy több az alábbi feltételek használata Azure Container Instances szolgáltatásban teljesül:
- A gyors üzembe helyezés pontozása és a modell érvényesítése érdekében keres. ACI üzembe helyezés általában kevesebb mint 5 perc alatt befejeződött.
- A találatokat helyezi üzembe a modellt fejlesztési vagy tesztelési környezetben. ACI-előfizetésenként 20 tárolócsoportok üzembe teszi lehetővé. További információkért lásd: a [kvóták és régiók rendelkezésre állása az Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) dokumentumot.

További információkért lásd: a [modell üzembe helyezése az Azure Container Instances](how-to-deploy-to-aci.md) dokumentumot.

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Nagy léptékű termelési forgatókönyvekhez helyezheti üzembe a modellt az Azure Kubernetes Service (AKS). Használjon egy meglévő AKS-fürtöt, vagy hozzon létre egy újat az Azure Machine Learning SDK-t, a parancssori felület vagy az Azure portal használatával.

Fürt létrehozása AKS áll egy folyamat, amikor a munkaterülethez. Létrehozása után újból felhasználhatja a fürt több telepítéshez. Ha törli a fürtöt vagy az azt tartalmazó erőforráscsoportot, majd kell létrehoznia egy új fürtöt, üzembe kell helyeznie legközelebb.

Az aks üzembe helyezéséhez biztosít, automatikus méretezést, naplózás, a modelladatok gyűjtésének és gyors válaszidők, a webszolgáltatások. 

AKS-fürt létrehozása nagyjából 20 percet vesz igénybe.

További információkért lásd: a [modell üzembe helyezése az Azure Kubernetes Service](how-to-deploy-to-aks.md) dokumentumot.

## <a name="azure-iot-edge"></a>Azure IoT Edge

Az IoT-eszközök azt az gyorsabban szükséges pontozáshoz elvégzendő műveleteken helyett a felhőbe történő adatküldés és a egy felhőben üzemeltetett modellt adatokat adja vissza a vár az eszközön. Az Azure IoT Edge segítségével a modell a peremhálózati eszközökön is üzemeltethet. A modell üzembe helyezése IoT Edge-ben Ha egy vagy több, a következő funkciók van szüksége:
- Felhőalapú kapcsolat nélkül is helyileg, a feladatok prioritása kezeléséhez
- A létrehozott adatok a felhőben gyorsan lekéréshez túl nagy
- Engedélyezze a keresztül intelligencia a és a helyi eszközök közel valós idejű feldolgozás
- Adatvédelemmel kapcsolatos követelmények adatok befogadásához 

További információkért lásd: a [üzembe helyezés az Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning) dokumentumot.

Az IoT Edge szolgáltatás további információkért lásd: a [Azure IoT Edge dokumentációja](https://docs.microsoft.com/azure/iot-edge/).


## <a name="field-programmable-gate-arrays-fpga"></a>A mező-programmable gate arrays (FPGA)

Hardver gyorsított modellek segítségével Project Brainwave ultramagas közel valós idejű következtetési kérelmek eléréséhez lehetővé teszik. Project Brainwave gyorsítja Neurális hálózatok (DNN) mező-programmable gate arrays az Azure-felhőben üzembe helyezett. Leggyakrabban használt dnn-eket egyaránt elérhetők a tanulással featurizers, vagy saját adatai testre szabható a súlyok betanított.

További információkért lásd: a [üzembe helyezés az egy FPGA](how-to-deploy-fpga-web-service.md) dokumentumot.

## <a name="next-steps"></a>További lépések

Modell üzembe helyezése egy adott számítási célnak vonatkozó további információkért lásd az alábbi dokumentumokat:

* [Modell üzembe helyezése az Azure Container Instances szolgáltatásban](how-to-deploy-to-aci.md)
* [Modell üzembe helyezése az Azure Kubernetes Service szolgáltatásban](how-to-deploy-to-aks.md)
* [Modell üzembe helyezése az Azure IoT Edge-ben](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)
* [Modell üzembe helyezése FPGA](how-to-deploy-fpga-web-service.md)
