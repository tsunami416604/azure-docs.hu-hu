---
title: Fejleszthet és telepíthet egy kép hasonlóság modell Azure Machine Learning-csomagot használ a Computer Vision.
description: Ismerje meg, hogyan hozhat létre, betanítását, tesztelése és üzembe helyezése egy számítógépes látástechnológiai kép hasonlóság modellel az Azure Machine Learning csomagot használ a Computer Vision.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 05/20/2018
ROBOTS: NOINDEX
ms.openlocfilehash: b1b4e3e19adb38a69e16aaa98300972df3bb71a8
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093649"
---
# <a name="build-and-deploy-image-similarity-models-with-azure-machine-learning"></a>Létrehozása és üzembe helyezése a rendszerkép hasonlóság modellek Azure Machine Learning segítségével

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]


Ebből a cikkből megtudhatja, hogyan betanításához, tesztelése és egy kép hasonlóság modell üzembe helyezése Azure Machine Learning-csomag a számítógép Vision (AMLPCV) használatával. Ezt a csomagot és annak részletes műszaki útmutatóját áttekintését lásd: https://aka.ms/aml-packages/vision.

A számítógép vision tartomány problémák nagy számú kép hasonlóság segítségével megoldhatók. Ezeket a problémákat, amelyek például egyéb kérdésre modellek létrehozásához a következők:
+ _Az ezen az ábrán egy objektum? Például "kutya", "autó", "szállítási" és így tovább_
+ _Milyen osztályba tartozó szemmel betegségek súlyosság szerint a betegek retinal vizsgálat van érintő?_

Összeállításakor, és ez AMLPCV a modell üzembe helyezésével, végigvesszük az alábbi lépéseket:
1. Adatkészlet létrehozása
2. Kép megjelenítési és jegyzet
3. Kép kiegészítését.
4. Részletes Neurális hálózat (DNN) model definition
5. Osztályozó által igénybe vett képzés
6. Kipróbálási és Vizualizáció
7. Webszolgáltatás üzembe
8. Webes szolgáltatás terheléses tesztelés

[CNTK](https://www.microsoft.com/en-us/cognitive-toolkit/) használja, a deep learning-keretrendszerek képzési helyileg kell elvégezni a GPU-alapú gépek például a ([Deep learning-adatelemzési virtuális gép](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)), és üzembe helyezés az Azure Machine Learning Operacionalizálás CLI használja.

## <a name="prerequisites"></a>Előfeltételek

1. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

1. A következő fiókok és az alkalmazás kell hozni és telepítve:
   - Egy Azure Machine Learning kísérletezési fiókra 
   - Az Azure Machine Learning Modellkezelés-fiók
   - Egy telepített Azure Machine Learning Workbenchre

   Ha három vannak létrehozott vagy még nincs telepítve, kövesse a [Azure Machine Learning gyors üzembe helyezés és a Workbench telepítési](../desktop-workbench/quickstart-installation.md) cikk. 

1. Az Azure Machine Learning csomag számítógépes Látástechnológiai telepítve kell lennie. Ismerje meg, ez a csomag telepítése amint https://aka.ms/aml-packages/vision.

## <a name="sample-data-and-notebook"></a>Mintaadatok és notebook

### <a name="get-the-jupyter-notebook"></a>A Jupyter notebook beszerzése

Letöltés a notebookot a minta futtatásához az itt leírtak szerint saját magának.

> [!div class="nextstepaction"]
> [A Jupyter notebook beszerzése](https://aka.ms/aml-packages/vision/notebooks/object_detection)

### <a name="load-the-sample-data"></a>A mintaadatok betöltése

Az alábbi példában egy adatkészletet, amely 63 asztali rendszerképek. Minden egyes képe címkével négy különböző osztályokhoz (bowl, cup, evőeszközök, lemezt) valamelyikéhez. Az ebben a példában képek számát azért kis, hogy ez a minta gyorsan hajthatók végre. A gyakorlatban meg kell adni legalább 100 képenként osztály. Az összes rendszerkép a következő helyen találhatók *".. /sample_data/imgs_recycling / "*, az alkönyvtárak"bowl"nevű,"cup","evőeszközök"és"lemez".

![Az Azure Machine Learning-adatkészlet](media/how-to-build-deploy-image-classification-models/recycling_examples.jpg)

## <a name="next-steps"></a>További lépések

További információ az Azure Machine Learning-csomagot a Computer Vision ezekben a cikkekben:

+ Ismerje meg, hogyan [Ez a modell pontosságának javítása](how-to-improve-accuracy-for-computer-vision-models.md).

+ Olvassa el a [csomag áttekintése](https://aka.ms/aml-packages/vision).

+ Fedezze fel a [referenciadokumentációt](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision) a csomag számára.

+ Ismerje meg [egyéb Python-csomagokat az Azure Machine Learning](reference-python-package-overview.md).
