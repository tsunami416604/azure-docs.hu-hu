---
title: Modellek üzembe helyezésének módja és helye
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan és hol helyezheti üzembe a Azure Machine Learning modelleket, beleértve a Azure Container Instances, az Azure Kubernetes Service, a Azure IoT Edge és a Field-programozható Gate-tömböket.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, tracking-python
zone_pivot_groups: aml-control-methods
ms.openlocfilehash: 42205f87e2025fa7f4492cb76aeb44a1fbf46eb6
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542783"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Modellek üzembe helyezése az Azure Machine Learninggel
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Megtudhatja, hogyan helyezheti üzembe a gépi tanulási modellt webszolgáltatásként az Azure-felhőben, vagy Azure IoT Edge eszközöket.

A munkafolyamat a modell telepítésének helyétől függetlenül hasonló:

1. A modell regisztrálása.
1. Következtetési konfiguráció előkészítése
1. Beléptetési parancsfájl előkészítése (kivéve, ha [kód nélküli telepítést](how-to-deploy-no-code-deployment.md)használ)
1. A modell üzembe helyezése a számítási célon.
1. Tesztelje az üzembe helyezett modellt, más néven webszolgáltatást.

Az üzembe helyezési munkafolyamatban részt vevő fogalmakkal kapcsolatos további információkért lásd: [modellek kezelése, üzembe helyezése és figyelése Azure Machine Learningokkal](concept-model-management-and-deployment.md).


 
::: zone pivot="cli"
[!INCLUDE [CLI quickstart](../../includes/machine-learning-how-to-deploy-and-where-cli.md)]
::: zone-end

::: zone pivot="py-sdk"
[!INCLUDE [SDK quickstart](../../includes/machine-learning-how-to-deploy-and-where-sdk.md)]
::: zone-end


### <a name="understanding-service-state"></a>A szolgáltatás állapotának ismertetése

A modell telepítése során előfordulhat, hogy a szolgáltatás állapota megváltozik a teljes üzembe helyezése során.

Az alábbi táblázat a különböző szolgáltatás-állapotokat ismerteti:

| Webszolgáltatás állapota | Leírás | Végső állapot?
| ----- | ----- | ----- |
| Transitioning | A szolgáltatás üzembe helyezési folyamatban van. | Nem |
| Nem kifogástalan | A szolgáltatás telepítve van, de jelenleg nem érhető el.  | Nem |
| Unschedulable | A szolgáltatás jelenleg nem telepíthető az erőforrások hiánya miatt. | Nem |
| Sikertelen | Hiba vagy összeomlás miatt nem sikerült telepíteni a szolgáltatást. | Igen |
| Kifogástalan | A szolgáltatás kifogástalan állapotban van, és a végpont elérhető. | Igen |


### <a name="batch-inference"></a><a id="azuremlcompute"></a>Batch-következtetés
A Azure Machine Learning számítási célokat Azure Machine Learning hozza létre és kezeli. A kötegelt előrejelzésekhez Azure Machine Learning folyamatokból is felhasználhatók.

A Batch-következtetések Azure Machine Learning számítási feladatokkal való áttekintését lásd: [a Batch-előrejelzések futtatása](tutorial-pipeline-batch-scoring-classification.md).

### <a name="iot-edge-inference"></a><a id="iotedge"></a>IoT Edge következtetés
A peremhálózati üzembe helyezésének támogatása előzetes verzióban érhető el. További információ: [Azure Machine learning telepítése IoT Edge modulként](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).


## <a name="next-steps"></a>További lépések

* [Sikertelen üzembe helyezés hibáinak megoldása](how-to-troubleshoot-deployment.md)
* [Üzembe helyezés az Azure Kubernetes Service-ben](how-to-deploy-azure-kubernetes-service.md)
* [Ügyfélalkalmazások létrehozása webszolgáltatások felhasználásához](how-to-consume-web-service.md)
* [Webszolgáltatás frissítése](how-to-deploy-update-web-service.md)
* [Modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md)
* [Webszolgáltatás biztonságossá tétele a TLS használatával Azure Machine Learning](how-to-secure-web-service.md)
* [A Azure Machine Learning modellek monitorozása a Application Insights](how-to-enable-app-insights.md)
* [Adatok gyűjtése a termelési modellekhez](how-to-enable-data-collection.md)
* [Esemény-riasztások és eseményindítók létrehozása a modell üzembe helyezéséhez](how-to-use-event-grid.md)

