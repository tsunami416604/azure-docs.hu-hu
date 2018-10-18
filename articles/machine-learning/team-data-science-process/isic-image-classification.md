---
title: Számítógépes látástechnológia, és a csoportos adatelemzési folyamat (TDSP) az Azure Machine Learning csomag képbesorolás |} A Microsoft Docs
description: Számítógépes látástechnológia képek besorolása a csoportos adatelemzési folyamat (TDSP) és az Azure Machine Learning csomag használatát ismerteti.
services: machine-learning, team-data-science-process
documentationcenter: ''
author: deguhath
ms.author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.openlocfilehash: ee2e797f3838b8b6b36174d14c73e97fe9790315
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49392812"
---
# <a name="skin-cancer-image-classification-with-the-azure-machine-learning-package-for-computer-vision-and-team-data-science-process"></a>Felszín rák képek besorolása az Azure Machine Learning-csomaggal számítógépes látástechnológiai és a csoportos adatelemzési folyamat

Ez a cikk bemutatja, hogyan használható a [számítógépes látástechnológiai Azure Machine Learning-csomagja](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) munkámnak, teszteléséhez és üzembe egy *besorolási kép* modell. A példa a csoportos adatelemzési folyamat (TDSP) struktúra és a sablonok [Azure Machine Learning Workbench](https://docs.microsoft.com/azure/machine-learning/service/quickstart-installation). Ez az útmutató a teljes minta biztosít. Használja a [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) , a deep learning-keretrendszert, és képzési hajtja végre a [Data Science virtuális gép](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview) GPU gép. Üzembe helyezés az Azure Machine Learning operacionalizálás CLI használ.

Számos alkalmazásban a számítógép vision tartományban is megalkotni, mint kép osztályozási problémák. Ezek közé tartozik, hogy választ kaphat a egyszerű "egy olyan objektum szerepel a lemezkép?" modellek készítése Ha az objektum lehet egy kutya, autó vagy szállítási. Ezenkívül összetettebb kérdésekre adott válaszok például "a betegek retinal vizsgálat szerint milyen szemmel betegségek súlyossági osztályát érintő?" Az Azure Machine Learning-csomagot számítógépes látástechnológiai leegyszerűsíti a besorolási adatok képfeldolgozás és a modellezési folyamat. 

## <a name="link-to-the-github-repository"></a>A GitHub-adattárra mutató hivatkozás
Ez a cikk a minta kapcsolatos összefoglaló dokumentumot. További részletes dokumentációt találhat a [a GitHub-webhelyről](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification).

## <a name="team-data-science-process-walkthrough"></a>Csapat adatelemzési folyamat forgatókönyv

Ez az útmutató használja a [csoportos adatelemzési folyamat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) életciklusát. A forgatókönyv a következő életciklus lépéseket ismerteti.

### <a name="1-data-acquisitionhttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode01dataacquisitionandunderstanding"></a>[1. Adatgyűjtés](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/01_data_acquisition_and_understanding)
A kép hálóbesorolási feladat a nemzetközi felszín Imaging együttműködési (ISIC) adatkészlet használható. Nemzetközi DIÁKIGAZOLVÁNY az egyetemi és iparági megkönnyítése érdekében a digitális felszín imaging tanulmányozása és melanómás mortalitási csökkentheti az alkalmazás közötti partneri. A [nemzetközi DIÁKIGAZOLVÁNYA archív](https://isic-archive.com/#images) , ártalmatlan vagy rosszindulatú van jelölve, hogy több mint 13 000 példányból álló felszín valamely lemezképet tartalmaz. Töltse le a rendszerképet egy minta a nemzetközi DIÁKIGAZOLVÁNY archív szintről.

### <a name="2-modelinghttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode02modeling"></a>[2. Modellezés](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/02_modeling)
A modellezés a lépésben a következő részlépések történik.

#### <a name="dataset-creation"></a>Adatkészlet létrehozása

Hozzon létre egy adatkészlet-objektum az Azure Machine Learning-csomagot számítógépes látástechnológiai lemezképek gyökérkönyvtár azáltal, hogy a helyi lemezen. 

#### <a name="image-visualization-and-annotation"></a>Kép megjelenítési és jegyzet

Megjelenítheti a rendszerképeket az adatkészlet-objektum, és szükség esetén javítsa ki a címkék.

#### <a name="image-augmentation"></a>Kép kiegészítését.

Egy adatkészlet-objektum bővítésével ismertetett átalakítások használatával a [imgaug](https://github.com/aleju/imgaug) könyvtár.

#### <a name="dnn-model-definition"></a>A DNN model definition

Adja meg a használt modell architektúra a betanítási lépést. Az Azure Machine Learning-csomagot számítógépes látástechnológiai hat előre betanított Neurális hálózat-modellek támogatottak: AlexNet, a Resnet-18-ra, a Resnet-34, a Resnet-50, a Resnet-101 és a Resnet-152.

#### <a name="classifier-training"></a>Osztályozó által igénybe vett képzés

Az alapértelmezett vagy egyéni paraméterek a Neurális hálózatokat betanításához.

#### <a name="evaluation-and-visualization"></a>Kipróbálási és Vizualizáció

Olyan funkciókat kínálnak az a betanított modell egy független vizsgálati adatkészleten teljesítményének értékeléséhez. Az értékelési mérőszámok közé tartozik a pontosság, a pontosság, a visszaírási és a ROC-görbe.

A részlépések tartozó Jupyter Notebookot az részletesen taglalja. A notebook útmutatásokat is tartalmaz a paraméterek, például a tanulási ráta mini kötegméret és jelkiesés arány további modell teljesítményének javítása érdekében kapcsolja.

### <a name="3-deploymenthttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode03deployment"></a>[3. Üzembe helyezés](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/03_deployment)

Ebben a lépésben a modell az modellezési lépésben előállított operationalizes. Azt mutatja be, az Előfeltételek és a szükséges beállításokat. A felhasználási webszolgáltatás kifejtett is. Ebben az oktatóanyagban elsajátíthatja az Azure Machine Learning-csomaggal a számítógépes látástechnológiai mély tanulási modelleket készíthet, és az Azure-ban a modell üzembe helyezéséhez.

## <a name="next-steps"></a>További lépések
- További dokumentációját olvassa [számítógépes látástechnológiai Azure Machine Learning-csomagja](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest).
- Olvassa el a [csoportos adatelemzési folyamat](https://aka.ms/tdsp) dokumentációja a kezdéshez.


## <a name="references"></a>Referencia

* [Számítógépes látástechnológia az Azure Machine Learning-csomagja](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest)
* [Azure Machine Learning Workbench](https://docs.microsoft.com/azure/machine-learning/service/quickstart-installation)
* [Adatelemző virtuális gép](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)

