---
title: Az Azure Machine Learning-csomaggal számítógép stratégiai és csapata adatok tudományos folyamat (TDSP) besorolás kép |} Microsoft Docs
description: Leírja, hogy a számítógép átfogóan bemutató kép besorolási Team adatok tudományos folyamat (TDSP) és az Azure Machine Learning csomag használható.
services: machine-learning, team-data-science-process
documentationcenter: ''
author: xibingao
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: xibingao
ms.openlocfilehash: f9e88cfb7185845e96f287b39bebaaa24320f537
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300796"
---
# <a name="skin-cancer-image-classification-with-the-azure-machine-learning-package-for-computer-vision-and-team-data-science-process"></a>Felszín kapcsolatos kép besorolás és az Azure Machine Learning csomag számítógép stratégiai és csapata tudományos folyamata

Ez a cikk bemutatja, hogyan használható a [számítógép stratégiai Azure Machine Learning-csomagja](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) betanítása, tesztelését és rendszerbe egy *besorolás kép* modell. A mintánkban Team adatok tudományos folyamat (TDSP) felépítését és sablonok [Azure Machine Learning-munkaterület](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation). A forgatókönyvben a teljes minta. Használja a [Microsoft kognitív eszközkészlet](https://www.microsoft.com/en-us/cognitive-toolkit/) keretrendszer megtanulásával és betanítása átfogó történik, egy [Adattudomány virtuális gép](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview) GPU gép. Telepítés az Azure Machine Learning operationalization CLI-t használ.

A számítógép stratégiai tartomány számos alkalmazás is keretezhető, kép besorolás problémák. Ezek közé tartoznak, létrehozási modelleket, hogy egyszerű kérdéseire választ "Is szerepel a kép objektum?" Ha az objektum egy kutya, a car és a szállítási lehet. Ezenkívül tartalmazza a kérdésekre adott válaszok összetettebb, mint például "a türelmet retinal vizsgálat során szem elleni súlyosságú mely osztály érintő?" Az Azure Machine Learning csomag számítógép stratégiai leegyszerűsíti a kép besorolás az adatfeldolgozás és a modellezési folyamat. 

## <a name="link-to-the-github-repository"></a>A GitHub-tárházban csatolása
Ez a cikk a minta kapcsolatos összefoglaló dokumentumot. A szélesebb körű dokumentációjában talál a [GitHub hely](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification).

## <a name="team-data-science-process-walkthrough"></a>Team adatok tudományos folyamat forgatókönyv

Ez az útmutató használja a [Team adatok tudományos folyamat](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview) életciklusát. A forgatókönyv a következő életciklus lépéseket ismerteti.

### <a name="1-data-acquisitionhttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode01dataacquisitionandunderstanding"></a>[1. Adatok beszerzése](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/01_data_acquisition_and_understanding)
A kép hálóbesorolási feladat használható a nemzetközi felszín Imaging együttműködés (ISIC) adatkészlet. ISIC van tudomány és az iparág tanulmányozására és melanómás mortalitás csökkentése érdekében imaging digitális felszín alkalmazásához közötti partneri kapcsolat áll fenn. A [ISIC archív](https://isic-archive.com/#images) tartalma szerint jóindulatú vagy rosszindulatú több mint 13,000 felszín valamely lemezképet is tartalmaz. Töltse le a lemezképet minta ISIC fájlból.

### <a name="2-modelinghttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode02modeling"></a>[2. Modellezési](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/02_modeling)
A modellezési a lépésben a következő részlépések megy végbe.

#### <a name="dataset-creation"></a>Adatkészlet létrehozása

A helyi lemezen képek gyökérkönyvtár megadásával számítógép stratégiai az Azure Machine Learning-csomagot létrehozni egy dataset objektum. 

#### <a name="image-visualization-and-annotation"></a>Kép képi megjelenítés és Megjegyzés

Megjelenítheti az adatkészlet-objektumot a lemezképeket, és javítsa ki a címkék szükség szerint.

#### <a name="image-augmentation"></a>Kép bővítés

Az átalakítás ismertetett használatával egy dataset objektum kiegészítheti a [imgaug](https://github.com/aleju/imgaug) könyvtárban.

#### <a name="dnn-model-definition"></a>DNN modell meghatározása

A modell architektúra használt a képzés lépést ad meg. Hat előre képzett mély Neurális hálózat modellek támogatottak az Azure Machine Learning csomag számítógép stratégiai: AlexNet, Resnet-18-ra, Resnet-34, Resnet-50, Resnet-101 és Resnet-152.

#### <a name="classifier-training"></a>Osztályozó képzési

A Neurális hálózatokat és a alapértelmezett vagy egyéni paraméterek betanításához.

#### <a name="evaluation-and-visualization"></a>Kiértékelési és -megjelenítésre

Egy független tesztelési adatkészletnél a betanított modell teljesítményétől kiértékelése ellátni. A kiértékelési mérőszámok közé tartozik a pontossága, visszaírási, és: ROC-görbe.

A részlépések a megfelelő Jupyter Notebook részletesen taglalja. A notebook is hasznos útmutatást ad a paraméterek, például a tanulási rátát mini kötegméret vagy jelkiesés arány további a modell teljesítményének növelése érdekében kapcsolja.

### <a name="3-deploymenthttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode03deployment"></a>[3. központi telepítés](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/03_deployment)

Ez a lépés a modell a modellezési. lépésben létrehozott operationalizes. Az Előfeltételek és a szükséges beállításokat okozna. A felhasználási webszolgáltatás kifejtett is. Ebben az oktatóanyagban elsajátíthatja az Azure Machine Learning csomag számítógép stratégiai mély tanulási modellek létrehozásához, és azok a modellt az Azure-ban.

## <a name="next-steps"></a>További lépések
- Olvassa el, további dokumentációt [számítógép stratégiai Azure Machine Learning-csomagja](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest).
- Olvassa el a [Team adatok tudományos folyamat](https://aka.ms/tdsp) dokumentáció a kezdéshez.


## <a name="references"></a>Referencia

* [A számítógép stratégiai Azure Machine Learning-csomag](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest)
* [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation)
* [Adatok tudományos virtuális gép](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)

