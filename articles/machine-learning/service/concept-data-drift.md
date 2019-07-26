---
title: Adateltolódás-figyelés (előzetes verzió)
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan figyelheti Azure Machine Learning szolgáltatás az adateltolódást.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cody-dkdc
ms.author: copeters
ms.date: 06/20/2019
ms.openlocfilehash: 13f73718fabd711e9c71a56ac4537b2ebef8a411
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371079"
---
# <a name="what-is-data-drift-monitoring-preview"></a>Mi az az adateltolódás-figyelés (előzetes verzió)?

Az adateltolódás az adateloszlás változása. A gépi tanulás kontextusában a betanított gépi tanulási modellek a drift miatt csökkenhetnek az előrejelzési teljesítménytől. A betanítási adat és az előrejelzések készítéséhez használt adat közötti eltolódás segíthet a teljesítménnyel kapcsolatos problémák észlelésében.

A gépi tanulási modellek csak olyan jól működnek, mint a betanításhoz használt adattípusok. A modellek éles környezetben történő üzembe helyezése a teljesítmény monitorozása nélkül nem észlelhető és hátrányos következményekkel járhat. Az adateltolódások figyelésével észlelhetők és módosíthatók az adateltolódások. 

## <a name="when-to-monitor-for-data-drift"></a>Mikor kell figyelni az adateltolódást?

A figyelésre használható metrikák a következők:

+ A drift nagysága (a drift együttható)
+ Drift oka (a szolgáltatás drift-hozzájárulása)
+ Távolsági metrikák (Wasserstein, energia stb.)

Ezzel a figyeléssel a rendszer riasztást vagy műveleteket állíthat be a drift észlelésekor, és az adattudós megvizsgálhatja a probléma kiváltó okát. 

Ha úgy gondolja, hogy a központilag telepített modell bemeneti adatai változhatnak, érdemes megfontolnia az adateltolódás-észlelés használatát.

## <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>Az adateltolódások figyelése Azure Machine Learning szolgáltatásban

**Azure Machine learning szolgáltatás**használatakor az adateltolódást adathalmazok vagy központi telepítések figyelik. Az adateltolódás figyeléséhez egy alapkonfigurációt – általában a modell betanítási adatkészletét – kell megadni. Egy második adatkészlet – általában az üzembe helyezésből összegyűjtött bemeneti adatok modellezése az alapadatkészlet alapján történik. Mind az adathalmazok, mind az adateltolódás [-](how-to-explore-prepare-data.md#explore-with-summary-statistics) figyelési szolgáltatás bemenete. A Machine learning-modellek a két adathalmaz közötti különbségek észlelésére vannak kiképezve. A modell teljesítményét a rendszer a drift együtthatóra konvertálja, amely a két adathalmaz közötti eltolódás mértékét méri. A [modell értelmezése](machine-learning-interpretability-explainability.md) során a rendszer kiszámítja a drift együtthatóhoz hozzájáruló funkciókat. Az adatkészlet-profilban az egyes szolgáltatásokra vonatkozó statisztikai adatokat követjük nyomon. 

## <a name="data-drift-metric-output"></a>Adateltolódás metrikájának kimenete

A drift mérőszámok több módon is megtekinthetők:

* Használja a `RunDetails` [Jupyter widgetet](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).
* A `get_metrics()` függvényt bármely `datadriftRun` objektumon használhatja.
* A modellben lévő Azure Portal metrikáinak megtekintése

A következő metrikákat menti a rendszer minden egyes futtatási iterációban egy adatdrift feladat esetén:

|Metrika|Leírás|
--|--|
wasserstein_distance|Egy dimenziós numerikus eloszláshoz megadott statisztikai távolság.|
energy_distance|Egy dimenziós numerikus eloszláshoz megadott statisztikai távolság.|
datadrift_coefficient|Hivatalosan Matthews korrelációs együttható, egy-1 és 1 közötti valós szám. A drift kontextusban 0 azt jelzi, hogy nincs eltolódás, és az 1 a maximális eltolódást jelzi.|
datadrift_contribution|A funkciók fontossága a drifthez hozzájáruló szolgáltatások szempontjából.|

## <a name="next-steps"></a>További lépések

Tekintse át a példákat, és Ismerje meg, hogyan figyelheti az adateltolódást:

+ [Megtudhatja, hogyan figyelheti az Azure Kubernetes Service (ak) használatával üzembe helyezett modellek adateltolódását](how-to-monitor-data-drift.md)
+ [Jupyter notebook minták](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/data-drift/) kipróbálása