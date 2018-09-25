---
title: Mi az FPGA és Project Brainwave? – Az azure Machine Learning
description: Ismerje meg, hogyan modellek és az FPGA-kban Neurális hálózatokkal felgyorsítása az Azure-ban. Ez a cikk mutatja be, hogy a mező-programmable gate arrays (FPGA) és hogyan Azure Machine Learning nyújt a valós idejű mesterséges intelligenciát (AI) Ha egy Azure FPGA helyezheti üzembe a modellt.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: tedway
author: tedway
ms.reviewer: jmartens
ms.date: 9/24/2018
ms.openlocfilehash: fa6ff90c3a573285ec8d6cc6f2e97bf3c75a1e8e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993280"
---
# <a name="what-is-fpga-and-project-brainwave"></a>Mi az FPGA- és Project Brainwave?

Ez a cikk mutatja be, hogy a mező-programmable gate arrays (FPGA) és hogyan Azure Machine Learning nyújt a valós idejű mesterséges intelligenciát (AI) Ha egy Azure FPGA helyezheti üzembe a modellt.

FPGA-kban programozható logikai blokkok tömbjét tartalmazza, és amellyel újrakonfigurálható hierarchiájának. A kapcsolatok engedélyezi ezeket az adatblokkokat a különböző módokon utáni gyártásban kell konfigurálni. FPGA-kban programozhatóság és más lapkák-teljesítményt nyújtanak.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGA-kban vs. A Processzor, grafikus Processzor és ASIC

![Az Azure Machine Learning FPGA összehasonlítása](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

|Processzor||Leírás|
|---|:-------:|------|
|Alkalmazás-specifikus integrált Kapcsolatcsoportok|Az ASICs|Egyéni kapcsolatok, például a Google TensorFlow processzor egységek (TPU), adja meg a lehető legnagyobb hatékonysággal, de rugalmatlan.|
|A mező-programmable gate arrays|FPGA-kban|FPGA-kban, például az Azure-ban elérhető közeli ASICs teljesítményt nyújtanak, de rugalmas és újrakonfigurálható időbeli alakulása új logikai megvalósításához.|
|Grafikai feldolgozóegységek|A GPU-k|Mesterséges Intelligencia számításokat, így gyorsabb processzorokat, mint a képrenderelés, párhuzamos feldolgozást képességeket kínál népszerű választást.|
|Központi egység|CPU-k|Általános célú processzorok, amelynek a teljesítmény nem grafikus és Videofeldolgozás ideális.|

## <a name="project-brainwave-on-azure"></a>Project Brainwave az Azure-ban

[A Project Brainwave](https://www.microsoft.com/research/project/project-brainwave/) Microsoft gazdaságos hardver architektúra, alapú Intel FPGA-eszközön, az adatelemzők és fejlesztők használatával felgyorsíthatja a valós idejű mesterséges Intelligencia számításokat.  Ez az architektúra FPGA-kompatibilis kínál **teljesítmény**, **rugalmasságot**, és **méretezési** és az Azure-ban érhető el.

FPGA-kban közel valós idejű következtetési kérelmek eléréséhez lehetővé teszik. Kötegelés azt jelenti, nagy mennyiségű adatot gyűjt, és át a processzor-javíthatja a hardver kihasználását. Kötegelés okozhat, késés, mert több adatot kell feldolgozni, de javíthatja a teljesítményt. Neurális feldolgozóegység Project Brainwave megvalósítása nem igénylik a kötegelés; ezért a késés lehet sokszor alacsonyabb Processzor és GPU képest.

### <a name="reconfigurable-power"></a>Újrakonfigurálható power
FPGA-kban újra kell konfigurálni a különböző típusú gépi tanulási modelleket. Ezt a rugalmasságot megkönnyíti az alkalmazások, a legtöbb optimális numerikus pontosság és a használt memória modell alapján gyorsabb.

Új machine learning-módszerekkel rendszeresen fejlesztenek, és a Project Brainwave hardverdizájn is gyorsan fejlődik. Mivel az FPGA-kban újrakonfigurálható, lehetőség a naprakész a gyorsan változó AI-algoritmusokat követelményeinek.

### <a name="whats-supported-on-azure"></a>Az Azure-ban támogatott műveletek
A Microsoft Azure-ban a világ legnagyobb felhőalapú befektetés FPGA-kban. Az Azure skálázási infrastruktúrájában Project Brainwave futtathatja.

Ez az architektúra FPGA-kompatibilis hardveres, betanított Neurális hálózatokkal futtassa gyorsan és kisebb késést biztosítanak. Project Brainwave párhuzamosítható előre betanított Neurális hálózatok (DNN) FPGA-kban horizontális felskálázási a szolgáltatás között. A dnn-eket is lehet előre betanított, mint egy részletes featurizer tanulási, vagy a frissített súlyok finomította átvitele.

Project Brainwave még ma, a következőket támogatja:
+ Kép besorolási és felismerés forgatókönyvek
+ TensorFlow-telepítés
+ Dnn-eket: ResNet 50, ResNet 152, VGG-16, SSD-VGG és DenseNet-121
+ Az Intel FPGA hardver 

## <a name="scenarios-and-applications"></a>Forgatókönyvek és alkalmazási

Project Brainwave integrálva van az Azure Machine Learning. A Microsoft használ FPGA-kban DNN kipróbáláshoz, valamint a Bing keresési rangsorolás és a szoftver meghatározott hálózatkezelési (SDN) gyorsítás közben processzorok felszabadításával más feladatok elvégzéséhez a késés csökkentése érdekében.

A következő helyzetekben használjon FPGA Project Brainwave architektúra:
+ [Automatikus optikai hálózatvizsgáló rendszer](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [Föld cover-leképezés](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="deploy-to-fpgas-on-azure"></a>Üzembe helyezése FPGA-kban az Azure-ban

Íme a munkafolyamat az Azure-ban támogatott dnn-eket, egy featurizer Azure FPGA-kban központi telepítésével kapcsolatos kép felismerés szolgáltatás létrehozására szolgáló:

1. A szolgáltatás-definíciót, amely egy fájl létrehozásához használja az Azure Machine Learning SDK Pythonhoz készült TensorFlow grafikonok (bemeneti, featurizer és osztályozó) folyamat leírása alapján. A telepítési parancs automatikusan a definíció- és grafikonok tömörítése ZIP-fájlban, és a zip-fájl feltöltése az Azure Blob storage.  A DNN Project Brainwave futtathatók a FPGA már telepítve van.

1. Regisztrálja a modellt, az SDK-val rendelkező a ZIP-fájlt az Azure Blob storage-ban.

1. A regisztrált modell az SDK szolgáltatás üzembe helyezése.

Deploying kezdheti betanított DNN modellek FPGA-kban az Azure-felhőbe is ebben a cikkben **"[modell üzembe helyezése FPGA-webszolgáltatásként](how-to-deploy-fpga-web-service.md)"**.


## <a name="next-steps"></a>További lépések

Tekintse meg ezeket a videókat és blogok:

+ [Nagy kapacitású hardver: gépi tanulás nagy mennyiségű épülő Azure + FPGA: Build 2018-as (videó)](https://www.youtube.com/watch?v=BMgQAHIx2eY)

+ [A Microsoft FPGA-alapú konfigurálható felhő (videó)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Project Brainwave valós idejű mesterséges: Projekt kezdőlapja](https://www.microsoft.com/research/project/project-brainwave/)
