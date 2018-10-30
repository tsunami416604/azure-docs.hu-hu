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
ms.openlocfilehash: 97ac405db3de4fa2c6f1173f813eafd41a5361ad
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50209445"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Az Azure Machine Learning szolgáltatással modellek üzembe helyezése

Az Azure Machine Learning szolgáltatás a betanított modell telepíthet több módszert is biztosít. Ebből a dokumentumból megtudhatja, hogyan helyezi üzembe a modellt webszolgáltatásként, amely az Azure-felhőben, vagy az IoT edge-eszközök.

A következő számítási célnak modellek helyezhető üzembe:

- [Az Azure Container Instances (aci) Szolgáltatásban](#aci): gyors üzembe helyezés. Jó fejlesztési vagy tesztelési célokra.
- [Az Azure Kubernetes Service (AKS)](#aks): jó nagy méretű éles környezetekben üzemelő példányok. Automatikus skálázást és gyors válaszidők biztosít.
- [Az Azure IoT Edge](#iotedge): IoT-eszközök a modellek üzembe helyezése. Következtetési történik az eszközön.
- [A mező-programmable gate array (FPGA)](#fpga): valós idejű következtetési ultraalacsony késése.

Ez a dokumentum többi része ezen lehetőségek részletesen ismerteti.

## <a id="aci"></a>Az Azure Container Instances szolgáltatásban

A modellek telepítéséről, egy REST API-végpont, ha egy vagy több az alábbi feltételek használata Azure Container Instances szolgáltatásban teljesül:
- Gyors üzembe helyezése és a modell érvényesítése kell. ACI üzembe helyezés befejeződött, kevesebb mint 5 perc alatt.
- A találatokat helyezi üzembe a modellt fejlesztési vagy tesztelési környezetben. ACI-előfizetésenként 20 tárolócsoportok üzembe teszi lehetővé. További információkért lásd: a [kvóták és régiók rendelkezésre állása az Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) dokumentumot.

További információkért lásd: a [modell üzembe helyezése az Azure Container Instances](how-to-deploy-to-aci.md) dokumentumot.

## <a id="aks"></a>Az Azure Kubernetes Service

A nagy léptékű termelési forgatókönyvekhez használja az Azure Kubernetes Service (AKS). Használjon egy meglévő AKS-fürtöt, vagy hozzon létre egy újat az Azure Machine Learning SDK-t, a parancssori felület vagy az Azure portal használatával.

AKS-fürt létrehozása az egy folyamatot, amikor a munkaterülethez. Újból felhasználhatja a fürt több telepítéshez. Ha törli a fürtöt, majd kell létrehoznia egy új fürtöt, üzembe kell helyeznie legközelebb.

Az Azure Kubernetes Service az alábbi képességeket biztosítja:

* Automatikus skálázás
* Naplózás
* A modelladatok gyűjtésének
* A webes szolgáltatások gyors válaszidők

AKS-fürt létrehozása nagyjából 20 percet vesz igénybe.

További információkért lásd: a [modell üzembe helyezése az Azure Kubernetes Service](how-to-deploy-to-aks.md) dokumentumot.

## <a id="iotedge"></a>Az Azure IoT Edge

Az IoT-eszközök azt az gyorsabban szükséges pontozáshoz elvégzendő műveleteken pontozó a felhőbe történő adatküldés helyett az eszközön. Az Azure IoT Edge segítségével a modell a peremhálózati eszközökön is üzemeltethet. A modell üzembe helyezése IoT Edge-ben Ha egy vagy több, a következő funkciók van szüksége:
- Felhőalapú kapcsolat nélkül is helyileg, a feladatok prioritása kezeléséhez
- A létrehozott adatok a felhőben gyorsan lekéréshez túl nagy
- Engedélyezze a keresztül intelligencia a és a helyi eszközök közel valós idejű feldolgozás
- Adatvédelemmel kapcsolatos követelmények adatok befogadásához 

További információkért lásd: a [üzembe helyezés az Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning) dokumentumot.

Az IoT Edge szolgáltatás további információkért lásd: a [Azure IoT Edge dokumentációja](https://docs.microsoft.com/azure/iot-edge/).


## <a id="fpga"></a>A mező-programmable gate arrays (FPGA)

Hardver gyorsított modellek segítségével Project Brainwave ultramagas közel valós idejű következtetési kérelmek eléréséhez lehetővé teszik. Project Brainwave gyorsítja Neurális hálózatok (DNN) mező-programmable gate arrays az Azure-felhőben üzembe helyezett. Leggyakrabban használt dnn-eket egyaránt elérhetők a tanulással featurizers, vagy saját adatai testre szabható a súlyok betanított.

További információkért lásd: a [üzembe helyezés az egy FPGA](how-to-deploy-fpga-web-service.md) dokumentumot.

## <a name="next-steps"></a>További lépések

Modell üzembe helyezése egy adott számítási célnak vonatkozó további információkért lásd az alábbi dokumentumokat:

* [Modell üzembe helyezése az Azure Container Instances szolgáltatásban](how-to-deploy-to-aci.md)
* [Modell üzembe helyezése az Azure Kubernetes Service szolgáltatásban](how-to-deploy-to-aks.md)
* [Modell üzembe helyezése az Azure IoT Edge-ben](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)
* [Modell üzembe helyezése FPGA](how-to-deploy-fpga-web-service.md)
