---
title: Mik azok FPGA-kban és Project Brainwave?
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan modellek és az FPGA-kban Neurális hálózatokkal felgyorsítása az Azure-ban. Ez a cikk bemutatja, mező-programmable gate arrays (FPGA) és hogyan az Azure Machine Learning szolgáltatáshoz nyújt a valós idejű mesterséges intelligenciát (AI) Ha egy Azure FPGA helyezheti üzembe a modellt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tedway
author: tedway
ms.reviewer: jmartens
ms.date: 1/10/2019
ms.custom: seodec18
ms.openlocfilehash: 716de6d491be753c5c32c0a2774404140010f72c
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56823314"
---
# <a name="what-are-fpgas-and-project-brainwave"></a>Mik azok FPGA-kban és Project Brainwave?

Ez a cikk bemutatja a mező-programmable gate arrays (FPGA), és hogyan az Azure Machine Learning szolgáltatáshoz nyújt a valós idejű mesterséges intelligenciát (AI) Ha egy Azure FPGA helyezheti üzembe a modellt.

FPGA-kban programozható logikai blokkok tömbjét tartalmazza, és amellyel újrakonfigurálható hierarchiájának. A kapcsolatok engedélyezi ezeket az adatblokkokat után gyártási különféle módon kell konfigurálni. Más lapkák képest, FPGA-kban programozhatóság és teljesítményt biztosítanak.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGA-kban vs. A Processzor, grafikus Processzor és ASIC

A következő ábra és táblázat bemutatják, hogyan FPGA-kban más feldolgozók összehasonlítása.

![Az Azure Machine Learning szolgáltatás FPGA-összehasonlítást diagram](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

|Processzor||Leírás|
|---|:-------:|------|
|Alkalmazás-specifikus integrált Kapcsolatcsoportok|Az ASICs|Egyéni kapcsolatok, például a Google TensorFlow processzor egységek (TPU), adja meg a lehető legnagyobb hatékonysággal. A változó igényeknek megfelelő, nem állítható át.|
|A mező-programmable gate arrays|FPGA-k|FPGA-kban, például az Azure-ban elérhető közeli ASICs teljesítményt nyújtanak. Szerepelnek rugalmas és újrakonfigurálható új a logikát alkalmazzák, az idő múlásával.|
|Grafikai feldolgozóegységek|A GPU-k|AI-számítások népszerű választást. A GPU-k olyan funkciókat biztosítanak a párhuzamos feldolgozást, így gyorsabb processzorokat, mint a képrenderelés.|
|Központi egység|CPU-k|Általános célú processzor, a teljesítmény, amelyek nem grafikus és Videofeldolgozás ideális.|

## <a name="project-brainwave-on-azure"></a>Project Brainwave az Azure-ban

[A Project Brainwave](https://www.microsoft.com/en-us/research/project/project-brainwave/) egy hardver architektúra a Microsofttól. Intel FPGA-eszközök, amely adatkutatóknak és fejlesztőknek használatával felgyorsíthatja a valós idejű mesterséges Intelligencia számítások alapul. Ez az architektúra FPGA-kompatibilis teljesítményt, rugalmasságot és méretezhetőséget biztosít, és az Azure-ban érhető el.

FPGA-kban közel valós idejű következtetési kérelmek eléréséhez lehetővé teszik. Nem szükségesek a aszinkron kérelmet (kötegelés). Kötegelés késés, okozhat, mert több adatot kell feldolgozni. Neurális feldolgozóegység Project Brainwave megvalósítása nem igénylik a kötegelés; ezért a késés lehet sokszor alacsonyabb Processzor és GPU processzorok képest.

### <a name="reconfigurable-power"></a>Újrakonfigurálható power
A különböző típusú gépi tanulási modelleket FPGA-kban újra létrehozhatja. Ezt a rugalmasságot megkönnyíti az alkalmazások, a legtöbb optimális numerikus pontosság és a használt memória modell alapján gyorsabb. Mivel FPGA-kban újrakonfigurálható, akkor is és legyen naprakész a gyorsan változó az AI-algoritmusokat követelményeinek.

### <a name="whats-supported-on-azure"></a>Az Azure-ban támogatott műveletek
A Microsoft Azure-ban a világ legnagyobb felhőalapú befektetés FPGA-kban. Az Azure skálázási infrastruktúrájában Project Brainwave futtathatja.

Project Brainwave még ma, a következőket támogatja:
+ Kép besorolási és felismerés forgatókönyvek
+ TensorFlow-telepítés
+ Dnn-eket: ResNet 50, ResNet 152, VGG-16, SSD-VGG és DenseNet-121
+ Az Intel FPGA hardver 

Ez az architektúra FPGA-kompatibilis hardveres, betanított Neurális hálózatokkal futtassa gyorsan és kisebb késést biztosítanak. Project Brainwave párhuzamosítható előre betanított Neurális hálózatok (DNN) FPGA-kban horizontális felskálázási a szolgáltatás között. A dnn-eket is lehet előre betanított, mint egy részletes featurizer tanulási, vagy a frissített súlyok finomította átvitele.

### <a name="scenarios-and-applications"></a>Forgatókönyvek és alkalmazási

Project Brainwave integrálva van az Azure Machine Learning. A Microsoft használ FPGA-kban DNN kipróbáláshoz, valamint a Bing keresési rangsorolás és a szoftver meghatározott hálózatkezelési (SDN) gyorsítás a késés, csökkentheti az processzorok felszabadításával más feladatok elvégzéséhez.

A következő helyzetekben használjon FPGA Project Brainwave architektúra:
+ [Automatikus optikai hálózatvizsgáló rendszer](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [Föld cover-leképezés](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="deploy-to-fpgas-on-azure"></a>Üzembe helyezése FPGA-kban az Azure-ban

Kép felismerés szolgáltatás létrehozása az Azure-ban, támogatott dnn-eket használhatja egy featurizer, Azure FPGA-kban az üzembe helyezéshez:

1. Használja a [Azure Machine Learning SDK Pythonhoz készült](https://aka.ms/aml-sdk) definiált szolgáltatás létrehozásához. Egy szolgáltatás definíciója egy olyan fájl, TensorFlow alapján grafikonok (bemeneti, featurizer és osztályozó) folyamatot leíró. A telepítési parancs automatikusan tömöríti a definíció- és grafikonok ZIP-fájlban, és az Azure Blob storage a zip-fájl feltöltését. A DNN Project Brainwave futtathatók a FPGA már telepítve van.

1. Regisztrálja a modellt a SDK-val rendelkező a ZIP-fájlt az Azure Blob storage-ban.

1. Telepítse a szolgáltatást a a regisztrált modell az SDK-val.

A kezdéshez DNN betanított modellek üzembe helyezése FPGA-kban az Azure-felhőben, lásd: [modell üzembe helyezése FPGA-webszolgáltatásként](how-to-deploy-fpga-web-service.md).


## <a name="next-steps"></a>További lépések

Tekintse meg ezeket a videókat és blogok:

+ [Nagy kapacitású hardver: Gépi tanulás nagy mennyiségű Azure + FPGA fölött: Build 2018-as (videó)](https://www.youtube.com/watch?v=BMgQAHIx2eY)

+ [A Microsoft FPGA-alapú konfigurálható felhő (videó)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Project Brainwave valós idejű mesterséges: Projekt kezdőlapja](https://www.microsoft.com/research/project/project-brainwave/)
