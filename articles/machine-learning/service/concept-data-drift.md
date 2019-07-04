---
title: Adatok eltéréseket monitoring (előzetes verzió)
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan figyelheti meg az Azure Machine Learning szolgáltatás az adatok eltéréseket.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cody-dkdc
ms.author: copeters
ms.date: 06/20/2019
ms.openlocfilehash: a03e3124647869e7148f271810bb523986a851c6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442387"
---
# <a name="what-is-data-drift-monitoring-preview"></a>Mi az adatok konfigurációsodródás monitorozási (előzetes verzió)?

Adatok eltéréseket változása, amely az adatok. A machine Learning a környezetben betanított gépi tanulási modelleket csökkentett teljesítményű előrejelzési teljesítményt tapasztalhat eltéréseket miatt. Betanítási adatok és előrejelzések végzett használt adatok közötti eltéréseket figyelése segíthet a teljesítménybeli problémák észleléséhez.

Machine learning-modellek csak annyira jók, ezek használt adatokat. A rendszer nem észleli és káros hatással van a teljesítmény figyelése nélkül üzemi modellek üzembe helyezéséhez vezethet. Az adatok eltéréseket figyeléssel, észleléséhez és alkalmazkodni az adatok eltéréseket. 

## <a name="when-to-monitor-for-data-drift"></a>Mikor adatok eltéréseket figyelni?

A metrikák azt figyelheti a következők:

+ Milyen mértékű eltolódása (eltéréseket együttható)
+ Eltolódás (a szolgáltatás eltéréseket hozzájárulás) okát
+ Távolságskála metrikák (Wasserstein, energia, stb.)

A helyen a monitorozás, riasztás vagy műveleteket állíthatja eltéréseket észlel, és az adatszakértő megvizsgálhatja a probléma okának. 

Ha úgy véli, hogy a bemeneti adatok az üzembe helyezett modell változhat, érdemes eltéréseket észlelési adatok használatával.

## <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>Hogyan felügyelik adatok eltéréseket az Azure Machine Learning szolgáltatás

Használatával **Azure Machine Learning szolgáltatás**, adatkészletek és a központi telepítések keresztül figyelt adatok eltéréseket. Adatok eltéréseket monitorozásához – általában a betanítási adatkészletet egy modell - alapkonfiguráció adatkészlet van megadva. Egy második adatkészlet - az alapkonfiguráció élelmiszer-általában a bemeneti adatok modellezése a központi telepítés – összeállításunkat lett tesztelve. Mindkét adatkészletek [profiled](how-to-explore-prepare-data.md#explore-with-summary-statistics) és az adatok bemeneti konfigurációsodródás figyelési szolgáltatás. Egy gépi tanulási modell tanítása észleli a különbség a két adatkészlet között. A modell teljesítményét az eltéréseket együttható, amely méri a két adatkészlet között eltéréseket mértékétől alakítja át. Használatával [model e](machine-learning-interpretability-explainability.md) számítja ki a Funkciók, az eltéréseket együttható hozzájáruló. Az adatkészlet profilban, a nyomon követett statisztikai információkat talál az egyes szolgáltatásokról. 

## <a name="data-drift-metric-output"></a>Eltolódás metrika kimeneti adatok

Több módon eltéréseket metrikákat tekinthet meg:

* A Jupyter widget használja.
* Használja a `get_metrics()` bármely függvény `datadriftRun` objektum.
* A metrikák megtekintése az adatmodell az Azure Portalon

A következő metrikák adatok eltéréseket feladat futtatási törzsének lesznek mentve:

|Metrika|Leírás|
--|--|
wasserstein_distance|Statisztikai távolság egydimenziós numerikus terjesztési definiálva.|
energy_distance|Statisztikai távolság egydimenziós numerikus terjesztési definiálva.|
datadrift_coefficient|Formálisan Matthews korrelációs együttható, -1 és 1 közötti valós szám. A eltéréseket kontextusában a 0 azt jelzi, hogy nincs eltéréseket, és 1 azt jelzi, hogy maximális eltéréseket.|
datadrift_contribution|Ez a funkció konfigurációsodródás hozzájáruló funkciók fontosságát.|

## <a name="next-steps"></a>További lépések

Példák és az adatok eltéréseket monitorozása:

+ [Adatok eltéréseket a modellek üzembe helyezett keresztül az Azure Kubernetes Service (AKS) monitorozása](how-to-monitor-data-drift.md)
+ Próbálja ki [Jupyter Notebook minták](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/data-drift/)