---
title: 'MLOps: Kezelheti, üzembe helyezheti és figyelheti a gépi Tanulási modelleket'
titleSuffix: Azure Machine Learning service
description: 'Ismerje meg, hogyan használható az Azure Machine Learning szolgáltatás MLOps: telepíthet, kezelheti és figyelheti a modellek révén folyamatosan fejleszthető. A helyi gépen, vagy egyéb forrásokból az Azure Machine Learning szolgáltatás a betanított modellek is telepítheti.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: chris-lauren
ms.author: clauren
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 416bebc070cfcad52c6180e65f0066c46c826cbe
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65849644"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>MLOps: Kezelheti, telepítheti és figyelheti a modellek az Azure Machine Learning szolgáltatás

Ebben a cikkben megismerheti az Azure Machine Learning szolgáltatás használatával telepíthet, kezelheti és figyelheti a modellek révén folyamatosan fejleszthető. A helyi gépen, vagy egyéb forrásokból az az Azure Machine Learning betanított modellek is telepítheti. 

A következő ábra szemlélteti a teljes telepítési munkafolyamat: [![Az Azure Machine Learning telepítési munkafolyamat](media/concept-model-management-and-deployment/deployment-pipeline.png)](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

A MLOps / az üzembe helyezést megvalósító munkafolyamat a következő lépéseket tartalmazza:
1. **Regisztrálja a modellt** az Azure Machine Learning szolgáltatás munkaterületen üzemeltetett beállításjegyzékben
1. **Használat** a modell a felhőben, egy IoT-eszközön, vagy a Power BI-jal analytics webszolgáltatásban.
1. **Adatainak figyelésére és gyűjtésére**
1. **Frissítés** központi telepítést, használjon új lemezképet.

Az egyes lépések egymástól függetlenül vagy, egyetlen paranccsal hajtható végre. Ezenkívül létrehozhat egy **CI/CD a munkafolyamat** alábbi képen szemléltetett módon.

[!["Az azure Machine Learning folyamatos integráció/folyamatos készregyártás (CI/CD) ciklus:](media/concept-model-management-and-deployment/model-ci-cd.png)](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GX]

## <a name="step-1-register-model"></a>1. lépés: Modell regisztrálása

Modell regisztrálását segítségével tárolja és verzió a modellek az Azure-felhőben, a munkaterületén. A modell beállításjegyzék megkönnyíti a betanított modellek rendszerezéséhez és nyomon követésére.

> [!TIP]
> Az Azure Machine Learning szolgáltatás kívül betanított modellek is rögzítheti.
 
A regisztrált modellek nevét és verzióját azonosítja. Minden alkalommal, amikor egy modell regisztrálni a neve megegyezik egy meglévő, a beállításjegyzék növeli a verziót. Használható kereséskor a modellek regisztrálása során is megadható további metaadat-címkéket. Az Azure Machine Learning szolgáltatásban minden olyan modell, amely lehet betölteni a Python 3.5.2-es verzióját használja, vagy magasabb támogatja.

Egy aktív központi telepítés használt modellek nem törölhető.

További információkért lásd: a register-modell szakasz, [modellek üzembe helyezése](how-to-deploy-and-where.md#registermodel).

Regisztrálás a modell pickle formátumban tárolja egy példa: [oktatóanyag: Egy rendszerkép osztályozási modell betanításához](tutorial-deploy-models-with-aml.md).

## <a name="step-2-use-the-model"></a>2. lépés: A modell használata

Gépi tanulási modelleket webszolgáltatásként, az IoT Edge-eszközökön, vagy szolgáltatások, mint a Power bi-ban az elemzéshez használható.

### <a name="web-service"></a>Webszolgáltatás

Használhatja a modellek **webszolgáltatások** számítási célok a következők:

* Azure-tárolópéldány
* Azure Kubernetes Service

A modellt webszolgáltatásként üzembe helyezéséhez meg kell adnia a következőket:

* A modell vagy ensemble modellek.
* A modell használatához szükséges függőségeket. Például egy parancsfájlt, amely elfogadja a kérelmeket, és hívja meg a modellt, a conda-függőségeket stb.
* Központi telepítés konfigurálása, amely azt ismerteti, hogyan és hol a modell rendszerbe.

További információkért lásd: [modellek üzembe helyezése](how-to-deploy-and-where.md).

### <a name="iot-edge-devices"></a>IoT Edge-eszközök

Az IoT-eszközök segítségével is használhatja a modellek **Azure IoT Edge-modulok**. IoT Edge-modulok hardvereszközök, amely lehetővé teszi a következtetésekhez, vagy a modell pontozása, az eszközön telepített.

További információkért lásd: [modellek üzembe helyezése](how-to-deploy-and-where.md).

### <a name="analytics"></a>Elemzés

A Microsoft Power BI támogatja a machine learning-modellek data Analytics. További információkért lásd: [Azure Machine Learning-integráció a Power bi-ban (előzetes verzió)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="step-3-monitor-models-and-collect-data"></a>3. lépés: A figyelő modelleket és adatokat gyűjthet

Figyelési lehetővé teszi, hogy jobban megismerhesse, milyen adatokat küld a rendszer a modell és az előrejelzések, amely azt adja vissza.

Ezek az információk segítségével megismerheti, hogyan a modellt használják. A bemeneti adatokat gyűjti össze a modell betanítási jövőbeli verzióinak hasznos lehet.

További információkért lásd: [a modelladatok gyűjtésének engedélyezése](how-to-enable-data-collection.md).

## <a name="step-4-update-the-deployment"></a>4. lépés: Az üzemelő példány frissítése

Központi telepítések explicit módon kell frissíteni. További információkért lásd: szakaszában frissítése [modellek üzembe helyezése](how-to-deploy-and-where.md#update).

## <a name="next-steps"></a>További lépések

Tudjon meg többet [hol és hogyan telepítheti a modellek](how-to-deploy-and-where.md) az Azure Machine Learning szolgáltatással. A központi telepítés egy példa: [oktatóanyag: Egy kép besorolási modell az Azure Container Instancesben üzembe](tutorial-deploy-models-with-aml.md).

Ismerje meg, hogyan hozhat létre [folyamatos integrációs és üzembe helyezési rendelkező Azure Machine Learning modellek](/azure/devops/pipelines/targets/azure-machine-learning). 

Ismerje meg, hogyan hozhat létre ügyfél alkalmazások és szolgáltatások, amelyek [webszolgáltatásként üzembe helyezett modell felhasználása](how-to-consume-web-service.md).
