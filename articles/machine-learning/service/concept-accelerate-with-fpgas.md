---
title: Mi az az FPGA? Az Azure Machine Learning – project Brainwave –
description: Megtudhatja, hogyan gyorsíthatja a modellek és az FPGA-kban Neurális hálózatokkal.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/31/2018
ms.openlocfilehash: 5c8efcbb5f2e9014c9edabfc2aee37c8c29ea0b4
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35644699"
---
# <a name="what-is-fpga-and-project-brainwave"></a>Mi az FPGA- és Project Brainwave?

Ez a cikk bemutatja, mező-programmable gate arrays (FPGA), és hogyan FPGA integrálva van az Azure machine learning valós idejű mesterséges Intelligencia biztosít.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGA-kban vs. A Processzor, grafikus Processzor és ASIC

FPGA-kban programozható logikai blokkok tömbjét tartalmazza, és újrakonfigurálható hierarchiájának amellyel, amelyek lehetővé teszik a blokkok után gyártási különböző módon kell konfigurálni.

FPGA-kban programozhatóság és a teljesítmény összehasonlítása más lapkák, adja meg:

![Az Azure Machine Learning FPGA összehasonlítása](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

- **Közép-India feldolgozóegység (CPU)** általános célú processzorok vannak. CPU-teljesítmény nincs grafikus és Videofeldolgozás ideális.
- **Grafikus (GPU)** és a egy népszerű AI számítások választás párhuzamos feldolgozást kínál. A párhuzamos feldolgozásra van szükség, processzorok, gyorsabb képrenderelés gpu-k eredményez.
- **Alkalmazás-specifikus integrált áramkörök (ASIC)**, például a Google TensorFlow processzor egység, testre szabott Kapcsolatcsoportok vannak. Habár ezek a modulok a legmagasabb szintű hatékonyságot biztosít, ASICs rugalmatlan.
- **FPGA-kban**, például az Azure-ban elérhető, a közeli ASIC teljesítményt nyújtanak, de később újra kell konfigurálni a rugalmasságot.

FPGA-kban, mostantól minden új Azure-kiszolgálót. A Microsoft Bing search rangsorértékét Neurális hálózat (DNN) próbaidőszakára, és szoftveralapú hálózatkezelési (SDN) gyorsítás már használja FPGA-kban. Ezek FPGA-megvalósítások késés csökkentheti az processzorok felszabadításával más feladatok elvégzéséhez.

## <a name="project-brainwave-on-azure"></a>Project Brainwave az Azure-ban

Project Brainwave egy hardver architektúra tervezett Intel FPGA eszközök alapján, és felgyorsíthatja a valós idejű mesterséges Intelligencia számítások használt. A gazdaságos FPGA-kompatibilis architektúrával egy betanított Neurális hálózat futtatható lehető leggyorsabb és kisebb késést biztosítanak. Project Brainwave párhuzamosítható előre betanított dnn-eket FPGA-kban horizontális felskálázási a szolgáltatás között.

- Teljesítmény

    FPGA-kban közel valós idejű következtetési kérelmek eléréséhez lehetővé teszik. Kötegelés azt jelenti, szétválasztja a kérést kisebb darabokra számból azok processzorra hardverkihasználás javítása érdekében. Kötegelés nem hatékony és késést okozhat. Brainwave kötegelés; nem igényel ezért a késést, 10 alkalommal alacsonyabb Processzor és GPU képest.

- Rugalmasság

    FPGA-kban újra kell konfigurálni a különböző típusú gépi tanulási modelleket. Ezt a rugalmasságot megkönnyíti az alkalmazások, a legtöbb optimális numerikus pontosság és a használt memória modell alapján gyorsabb.

    Új machine learning-módszerekkel rendszeresen fejlesztenek, és a Project Brainwave hardverdizájn is gyorsan fejlődik. Mivel az FPGA-kban újrakonfigurálható, lehetőség a naprakész a gyorsan változó AI-algoritmusokat követelményeinek.

- Méretezés

    A Microsoft Azure-ban a világ legnagyobb felhőalapú befektetés FPGA-kban. Az Azure skálázási infrastruktúrájában Brainwave futtathatja.

Előzetes verziója az Azure Machine Learning integrált Project Brainwave jelenleg érhető el. És a egy korlátozott előzetes verzió ahhoz, hogy Project Brainwave az Edge-ben, így igénybe veheti, hogy a saját vállalkozások és a létesítményekben számítási sebességű is érhető el.

Aktuális előzetes verzióban elérhető Brainwave korlátozódik, a TensorFlow-telepítésben, és a ResNet50-alapú Neurális hálózatok Intel FPGA hardver képbesorolás és felismerése. Nincsenek csomagok támogatásához további katalógus modelleket és más keretrendszerekhez.

A következő helyzetekben használjon FPGA Project Brainwave architektúra:

- Automatikus optikai hálózatvizsgáló rendszer. Lásd: [valós idejű mesterséges Intelligencia: a Microsoft bejelenti előzetes verzióját: Project Brainwave](https://blogs.microsoft.com/ai/build-2018-project-brainwave/).
- Leképezés a föld lefedik. Lásd: [FPGA-kban a Deep Learning következtetésekhez használatával hajtsa végre a föld Cover Légifelvételes lemezképek több terabájtnyi leképezési](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/).

## <a name="how-to-deploy-a-web-service-to-an-fpga"></a>Hogyan helyezhet üzembe egy webszolgáltatás-FPGA?

A magas szintű folyamata egy kép felismerés szolgáltatás létrehozása az Azure-ban ResNet50 egy featurizer, a következőképpen történik:

1. ResNet50 Brainwave már üzemel. Hozhat létre más gráfokat az tensorflow-hoz (a bemeneti dátum, besorolás, és így tovább), és adja meg a folyamat (-> Szabadkézi bevitel -> besorolása) szolgáltatás definíció json-fájlok használatával. A definíció- és grafikonok tömörítése zip-fájlban, és a zip-fájl feltöltése az Azure Blob storage.
2. Regisztrálja a modellt az Azure gépi Tanulási modell felügyeleti API-val, a zip-fájlt a Blob storage-ban.
3. A szolgáltatás üzembe helyezése az Azure gépi Tanulási modell Management API segítségével regisztrált modell.

További információ erről a folyamatról a cikkben [modell üzembe helyezése az Azure Machine Learning-FPGA webszolgáltatásként](how-to-deploy-fpga-web-service.md).


## <a name="start-using-fpga"></a>FPGA használatának megkezdése

Az Azure Machine Learning hardver gyorsított modellek lehetővé teszik a DNN betanított modellek üzembe helyezése FPGA-kban az Azure-felhőben. Első lépésként lásd:

- [Modell üzembe helyezése FPGA-webszolgáltatásként](how-to-deploy-fpga-web-service.md)
- [A gyorsított modellek Project Brainwave működteti a Microsoft Azure Machine Learning-hardver](https://github.com/azure/aml-real-time-ai).

## <a name="next-steps"></a>További lépések

[Modell üzembe helyezése FPGA-webszolgáltatásként](how-to-deploy-fpga-web-service.md)

[Ismerje meg, hogyan FPGA SDK telepítése](reference-fpga-package-overview.md)

[Nagy kapacitású hardver: gépi tanulás nagy mennyiségű épülő Azure + FPGA: Build 2018-as (videó)](https://www.youtube.com/watch?v=BMgQAHIx2eY)

[A Microsoft FPGA-alapú konfigurálható felhő (videó)](https://channel9.msdn.com/Events/Build/2017/B8063)

[A Microsoft Project Brainwave unveils a valós idejű mesterséges Intelligencia](https://www.microsoft.com/research/blog/microsoft-unveils-project-brainwave/)