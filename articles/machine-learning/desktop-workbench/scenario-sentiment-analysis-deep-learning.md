---
title: Részletes tanulás használatával az Azure Machine Learning véleményeket elemzése |} Microsoft Docs
description: Hogyan mély tanulás használatával az Azure ML munkaterület véleményeket elemzést.
services: machine-learning
documentationcenter: ''
author: miprasad
manager: kristin.tolle
editor: miprasad
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2018
ms.author: miprasad
ms.openlocfilehash: b4f16ba296e5481e70bd3172eae7b89af67638d3
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="sentiment-analysis-using-deep-learning-with-azure-machine-learning"></a>Részletes tanulás használatával az Azure Machine Learning véleményeket elemzés

Véleményeket elemzésre abban a tartományban, a természetes nyelvű feldolgozásának jól ismert feladat. Megadott szövegek készlete, célja annak meghatározásához, hogy a szöveg a céggel kapcsolatos véleményeket. Ez a megoldás célja mély Learning használatához előrejelzésére movie értékelést a céggel kapcsolatos véleményeket.

A megoldás itt található: https://github.com/Azure/MachineLearningSamples-SentimentAnalysis

## <a name="link-to-the-gallery-github-repository"></a>A gyűjtemény GitHub-tárházban csatolása

A nyilvános GitHub-tárház kattintson erre a hivatkozásra:

[https://github.com/Azure/MachineLearningSamples-SentimentAnalysis](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis)

## <a name="use-case-overview"></a>Használja az eset áttekintése

A felbontást az adatok és a mobil eszközök elterjedése lehetőségek az ügyfelek számára a otthon és szokások express kapcsolatos elemzésekből, bármikor rengeteg hozott létre. A véleményét vagy a "véleményeket" gyakran jönnek létre értékelést formájában közösségi csatornákon keresztül, csevegés, megosztások, kedveli, a Twitter-üzeneteket, stb. Lehet, hogy a vállalkozások számára szeretné termékei és szolgáltatásai fejlesztéséhez, több tájékozott döntést hozni, és jobban lépteti elő a márka hasznos információt a céggel kapcsolatos véleményeket.

Érték beszerzése véleményeket elemzés, vállalatok számára a gyakorlatban használható elemzések strukturálatlan közösségi adatok túlnyomó tárolóinak enyém lehessen kell rendelkeznie. Ez a példa azt fejlesztése mély tanulási modellek a céggel kapcsolatos véleményeket elemzése movie felülvizsgálat AMLWorkbench használatával

## <a name="prerequisites"></a>Előfeltételek

* Egy [Azure-fiók](https://azure.microsoft.com/free/) (az ingyenes próbaverzió érhetők el).

* Egy telepített példánya [Azure Machine Learning-munkaterület](../service/overview-what-is-azure-ml.md) következő a [– első lépések a telepítési útmutató](../service/quickstart-installation.md) telepíteni a programot, és hozzon létre egy munkaterület.

* Operationalization célszerű, ha van telepítve, és helyileg futó Docker-motorhoz. Ha nem, a fürt kapcsolót. Azonban az Azure tároló szolgáltatás (ACS) futtató költséges lehet.

* A megoldás feltételezi, hogy futtatja Azure Machine Learning-munkaterület Windows 10 helyi telepítése Docker-motorhoz. A egy macOS található utasítás nagyjából azonos.

## <a name="data-description"></a>Adatok leírása

Ez a minta használatos a dataset kis kézzel létrehozott adatkészlet és a található a [Adatmappa](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/tree/master/data).

Az első oszlopban szerepel movie értékelést, és a második oszlopban szerepelnek a céggel kapcsolatos véleményeket (0 – negatív és 1 – pozitív). A dataset csupán bemutatási célokra használja, de általában robusztus véleményeket pontszámok beszerzéséhez kell nagy adatbázisból. Például a [IMDB Movie ellenőrzi, hogy véleményeket osztályozási problémához](https://keras.io/datasets/#datasets ) áll Keras 25 000 filmek felülvizsgálat IMDB (pozitív vagy negatív) által véleményeket feliratú a DataSet adatkészlet. A labor nem kívánja bemutatják a mély tanulási használata AMLWorkbench véleményeket elemzést.

## <a name="scenario-structure"></a>A forgatókönyv struktúra

A mappastruktúra az alábbiak szerint van rendezve:

1. A gyökérmappában van minden, a céggel kapcsolatos véleményeket elemzés AMLWorkbench használatával kapcsolatos kódot
2. adatok: a megoldásban használt adatkészlet tartalmaz
3. dokumentumok: a gyakorlati labs tartalmazza

A megoldás elvégzésére Hands-on Labs sorrendjét a következőképpen történik:

| Sorrend| Fájlnév | Kapcsolódó fájlokat |
|--|-----------|------|
| 1 | [`SentimentAnalysisDataPreparation.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisDataPreparation.md) | 'data/sampleReviews.txt' |
| 2 | [`SentimentAnalysisModelingKeras.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisModelingKeras.md) | 'SentimentExtraction.py' |
| 3 | [`SentimentAnalysisOperationalization.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisOperationalization.md) | "Operaionalization" |

## <a name="conclusion"></a>Összegzés

Végül Ez a megoldás bemutatja, mély tanulás használatával az Azure Machine Learning-munkaterület a céggel kapcsolatos véleményeket elemzést. Emellett azok HDF5 modellek segítségével.
