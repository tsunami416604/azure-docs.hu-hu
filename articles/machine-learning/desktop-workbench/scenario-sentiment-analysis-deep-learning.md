---
title: Hangulatelemzés mély tanulás használatával az Azure Machine Learning |} A Microsoft Docs
description: Hogyan hangulatelemzés mély tanulás használatával az Azure Machine Learning Workbench használatával végezheti el.
services: machine-learning
documentationcenter: ''
author: miprasad
manager: kristin.tolle
editor: miprasad
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2018
ms.author: miprasad
ROBOTS: NOINDEX
ms.openlocfilehash: e2fbb0b7b0dede198be0e57ffcd2b58a7da7fce7
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46947771"
---
# <a name="sentiment-analysis-using-deep-learning-with-azure-machine-learning"></a>Hangulatelemzés mély tanulás használatával az Azure Machine Learning

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



Hangulatelemzés a jól ismert feladat természetes nyelvi feldolgozás tartozik. Adja meg a szövegek készletét, célja, hogy határozza meg, hogy a szöveg a róluk szóló véleményeket. Ez a megoldás célja, Deep Learning használandó movie felülvizsgálatok hangulatának előrejelzésére.

A megoldás nem található: https://github.com/Azure/MachineLearningSamples-SentimentAnalysis

## <a name="link-to-the-gallery-github-repository"></a>A katalógus GitHub-adattárra mutató hivatkozás

Erre a hivatkozásra a nyilvános GitHub-adattárhoz:

[https://github.com/Azure/MachineLearningSamples-SentimentAnalysis](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis)

## <a name="use-case-overview"></a>Használati eset – áttekintés

Az adatok robbanásszerűen és a mobil eszközök elterjedésével létrehozott nagy mennyiségű lehetőségek az ügyfelek számára, hogy azok érzéseinkre és szokások express kapcsolatos mindent bármikor. A vélemény vagy a "vélemények" gyakran akkor jön létre, az űrlap felülvizsgálatokról azoknak a közösségi csatornákon keresztül, csevegés, közös, kedvelések, Twitter-üzeneteket, és így tovább. Lehet, hogy a vélemény felbecsülhetetlen értékű termékei és szolgáltatásai fejlesztéséhez, döntéseket hozhat, és jobban elősegítik azok márkái vállalkozások számára.

Hangulatelemzés érték lekéréséhez vállalkozások lehetővé teszi hatalmas tárolók közösségi teljes strukturálatlan adatmennyiséget a gyakorlatban hasznosítható elemzéseket adatbányászatot kell rendelkeznie. Ebben a példában kifejlesztett deep learning-modelleket az AMLWorkbench használatával movie felülvizsgálatok vélemények elemzése

## <a name="prerequisites"></a>Előfeltételek

* Egy [Azure-fiók](https://azure.microsoft.com/free/) (az ingyenes próbaverziók érhető el).

* Egy telepített példánya [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) következő a [rövid telepítési útmutatójában](quickstart-installation.md) telepítse a programot, és hozzon létre egy munkaterületet.

* Az operacionalizálás célszerű Ha helyben telepített és futtatott Docker-motor. Ha nem, a fürt lehetőséget is használhatja. Azonban egy Azure Container Service (ACS) futó költséges lehet.

* Ez a megoldás feltételezi, hogy futtat az Azure Machine Learning Workbench Windows 10-es a helyileg telepített Docker-motor. A MacOS-gépeken az utasítás a nagymértékben megegyezik.

## <a name="data-description"></a>Adatok leírása

Ehhez a mintához használt adatkészlet aktuális kialakított egy kisméretű adatkészlet, és a található a [Adatmappa](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/tree/master/data).

Az első oszlopa tartalmazza a film felülvizsgálatok és második oszlopa tartalmazza a róluk szóló véleményeket (0 – negatív és 1 – pozitív). Az adatkészlet csupán bemutatási célokra van használatban, de általában robusztus véleménypontszámának lekéréséhez szükség van egy nagy méretű adathalmazt. Például a [IMDB Movie felülvizsgálatok vélemények osztályozási problémához](https://keras.io/datasets/#datasets ) származó 25 000 filmek felülvizsgálatok IMDB, vélemények által címkézett, (pozitív vagy negatív) az adatkészlet áll. A laborgyakorlat szándéka az, hogy bemutatják, hogyan hajthat végre hangulatelemzés mély tanulást használó az AMLWorkbench.

## <a name="scenario-structure"></a>A forgatókönyv-struktúra

A gyökérmappa-szerkezetében a következőképpen van rendezve:

1. Hangulatelemzés AMLWorkbench használatával kapcsolatos összes kód van a gyökérmappában
2. adatok: tartalmazza az adatkészletet a megoldásban használt
3. Docs: tartalmazza az összes laborgyakorlat

Gyakorlati Laborgyakorlatok sorrendje számára, a megoldás a következőképpen történik:

| Rendelés| Fájlnév | Kapcsolódó fájlok |
|--|-----------|------|
| 1 | [`SentimentAnalysisDataPreparation.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisDataPreparation.md) | 'data/sampleReviews.txt' |
| 2 | [`SentimentAnalysisModelingKeras.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisModelingKeras.md) | 'SentimentExtraction.py' |
| 3 | [`SentimentAnalysisOperationalization.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisOperationalization.md) | "Operaionalization" |

## <a name="conclusion"></a>Összegzés

Végül Ez a megoldás bemutatja, Deep Learning-hangulatelemzés az Azure Machine Learning Workbench-végrehajtására. Azt is üzembe helyezheti HDF5 modellek használata.
