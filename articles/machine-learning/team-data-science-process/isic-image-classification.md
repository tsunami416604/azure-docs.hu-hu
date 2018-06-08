---
title: A számítógép stratégiai (AMLPCV) és a Team adatok tudományos folyamat (TDSP) lemezkép az Azure Machine Learning (AML) csomag besorolás |} Microsoft Docs
description: Leírja, hogy a kép besorolás TDSP (csapat az tudományos folyamata) és AMLPCV használható
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
ms.openlocfilehash: a3dcfd8a9292d31c7342b8d50ec58c0da53318d3
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837218"
---
# <a name="skin-cancer-image-classification-with-azure-machine-learning-aml-package-for-computer-vision-amlpcv-and-team-data-science-process-tdsp"></a>Felszín kapcsolatos kép besorolás Azure Machine Learning (AML) csomag, a számítógép stratégiai (AMLPCV) és a Team adatok tudományos folyamat (TDSP)

## <a name="introduction"></a>Bevezetés

Ez a cikk bemutatja, hogyan használható a [Azure Machine Learning csomag a számítógép stratégiai (AMLPCV)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) betanítása, tesztelését és rendszerbe egy **besorolás kép** modell. A mintánkban TDSP felépítését és sablonok [Azure Machine Learning-munkaterület](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation). A teljes minta a forgatókönyv valósul meg. Használja [CNTK](https://www.microsoft.com/en-us/cognitive-toolkit/) keretrendszer megtanulásával és betanítása átfogó történik, egy [adatok tudományos VM](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview) GPU gép. Telepítés használ az Azure ML Operationalization parancssori felület.

A számítógép stratégiai tartomány számos alkalmazás is keretezhető, kép besorolás problémák. Ezek közé tartoznak, létrehozási modelleket, hogy válaszoljon a kérdésekre, mint "az objektum szerepel a kép?" (objektum lehet *kutya*, *car*, vagy *szolgáltatástól*) és az összetettebb kérdésekre, mint "a türelmet retinal vizsgálat során szem elleni súlyosságú mely osztály érintő?" AMLPCV leegyszerűsíti a kép besorolás az adatfeldolgozás és -modellezési folyamat. 

## <a name="link-to-github-repository"></a>GitHub-tárházban csatolása
Azt adja meg az összefoglaló dokumentációját Itt a minta. Szélesebb körű dokumentációjában található meg a [GitHub hely](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification).

## <a name="team-data-science-process-tdsp-walkthrough-with-amlpcv"></a>Team adatok tudományos folyamat (TDSP) forgatókönyv AMLPCV

Ez az útmutató használja a [Team adatok tudományos folyamat (TDSP)](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview) életciklusát.

A forgatókönyv a következő életciklus lépéseit tartalmazza:

### <a name="1-data-acquisionhttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode01dataacquisitionandunderstanding"></a>[1. Adatok acquision](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/01_data_acquisition_and_understanding)
A kép hálóbesorolási feladat ISIC dataset használható. ISIC (a nemzetközi felszín Imaging együttműködés) egy partership közötti tudomány és az iparág digitális felszín imaging tanulmányozására és melanómás mortalitás csökkentése érdekében alkalmazásának elősegítése érdekében. A [ISIC archív](https://isic-archive.com/#images) címkével jóindulatú vagy rosszindulatú több mint 13,000 felszín valamely lemezképet is tartalmaz. A lemezképek mintát ISIC archívumból letöltődnek.

### <a name="2-modelinghttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode02modeling"></a>[2. Modellezési](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/02_modeling)
Modellezési a lépésben a következő részlépések megy végbe. 

<b>2.1 adatkészlet létrehozása</b><br>

AMLPCV egy Dataset objektum létrehozásához adja meg a helyi lemezen képek gyökérkönyvtár. 

<b>2.2 a képi megjelenítés és jegyzet kép</b><br>

Jelenítheti meg az adatkészlet-objektumot a lemezképeket, és szükség esetén javítsa ki a címkék némelyike.

<b>2.3 kép bővítés</b><br>

Egy adatkészlet-objektum beolvasását az ismertetett átalakításokat kiegészítheti a [imgaug](https://github.com/aleju/imgaug) könyvtárban.

<b>2.4 DNN modell meghatározása</b><br>

Adja meg a modell architektúra, a képzési lépésben használt. Hat különböző / betanítása mély Neurális hálózat modellek támogatottak AMLPCV: AlexNet, Resnet-18-ra, Resnet-34, és Resnet-50, Resnet-101 és Resnet-152.

<b>2.5 osztályozó képzési</b><br>

A Neurális hálózatokat és a alapértelmezett vagy egyéni paraméterek betanításához.

<b>2.6 értékelési és -megjelenítésre</b><br>

A be egy független tesztelési adatkészletnél a betanított modell teljesítményétől kiértékelése funkciókat biztosítja. A kiértékelési mérőszámok közé tartozik a pontossága és visszaírási, és: ROC-görbe.

E részlépések a megfelelő Jupyter Notebook a részletesen. Azt is biztosítja a irányelvek bekapcsolása esetén a paraméterek, például sebesség, a mini köteg mérete és a jelkiesés arány további a modell teljesítményének javítása céljából.

### <a name="3-deploymenthttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode03deployment"></a>[3. központi telepítés](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/03_deployment)

Ez a lépés a modell a modellezési. lépésben létrehozott operationalizes. A operationalization Előfeltételek és a telepítő okozna. Végezetül a felhasználási webszolgáltatás esetén is, tekintse meg. Az oktatóanyag bemutatja az AMLPCV mély tanulási modellek létrehozásához, és azok a modellt az Azure-ban.

## <a name="next-steps"></a>További lépések
Olvassa el a további dokumentációt a [Azure Machine Learning csomag a számítógép stratégiai (AMLPCV)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) és [Team adatok tudományos folyamat (TDSP)](https://aka.ms/tdsp) a kezdéshez.


## <a name="references"></a>Referencia

* [Azure Machine Learning csomag a számítógép stratégiai (AMLPCV)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest)
* [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation)
* [Virtuális gép Adattudomány](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)

