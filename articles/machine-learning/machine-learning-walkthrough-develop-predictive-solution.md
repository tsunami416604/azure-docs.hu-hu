---
title: "Prediktív megoldás a hitelkockázat kiszámításához a Machine Learning használatával | Microsoft Docs"
description: "Részletes útmutató, amely azt ismerteti, hogyan hozható létre a hitelkockázat értékelésére szolgáló prediktív elemzési megoldás az Azure Machine Learning Studio eszközben."
keywords: "hitelkockázat, prediktív elemzési megoldás,kockázatértékelés"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 43300854-a14e-4cd2-9bb1-c55c779e0e93
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/16/2016
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 07cb3fe0d5688d5b63fe3312cad14c2274a58a09
ms.openlocfilehash: e98a64910f28da0a8a9b4a58c717c40d791ccf00


---
# <a name="walkthrough-develop-a-predictive-analytics-solution-for-credit-risk-assessment-in-azure-machine-learning"></a>Részletes útmutató: A hitelkockázat értékelésére szolgáló prediktív elemzési megoldás fejlesztése az Azure Machine Learning Studio használatával

Ebben az útmutatóban a megoldások Machine Learning Studióban való fejlesztési folyamatát tekintjük át részleteiben. Egy prediktív elemzési modellt fejlesztünk ki a Machine Learning Studióban, majd üzembe helyezzük azt Azure Machine Learning-webszolgáltatásként, ahol a modell új adatokkal végezhet előrejelzéseket. 

> [!TIP]
> Az útmutatóban leírtak abból indulnak ki, hogy legalább egyszer használta már a Machine Learning Studiót, és hogy valamennyire tisztában van a gépi tanulás fogalmaival, bár azt is feltételezik, hogy nem szakértője ezeknek.
> 
>Ha még soha nem használta az **Azure Machine Learning Studiót**, érdemes lehet [az első adatelemzési kísérlet az Azure Machine Learning Studióban történő létrehozását](machine-learning-create-experiment.md) ismertető oktatóanyaggal kezdenie. Az oktatóanyag végigvezeti először a Machine Learning Studio használatán, bemutatja az alapokat, valamint azt, hogyan húzhat be modulokat a kísérletbe, majd kapcsolhatja össze azokat, továbbá hogyan futtathatja a kísérletet, majd tekintheti meg az eredményeket.
>
>Ha most ismerkedik a gépi tanulással, az [Adatelemzés kezdőknek](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) videósorozat nagyszerű hely az induláshoz. Ez a videósorozat remek bevezetőt kínál hétköznapi nyelven és elterjedt fogalmak használatával a gépi tanulás témakörébe.
> 

## <a name="the-problem"></a>A probléma

Tegyük fel, hogy előrejelzést kell készíteni egy személy hitelkockázatáról az általa kitöltött hitelkérelemben megadott adatok alapján.  

A hitelkockázat értékelése természetesen összetett probléma, de a példa kedvéért most leegyszerűsítjük egy kicsit a paramétereket. Jó példaként szolgálhat annak a bemutatásához, hogyan hozhatók létre prediktív elemzési megoldások a Microsoft Azure Machine Learninggel, ha a Machine Learning Studiót és a Machine Learning webszolgáltatást használjuk.  

## <a name="the-solution"></a>A megoldás

Ebben a részletes útmutatóban nyilvánosan elérhető hitelkockázati adatokkal fogunk dolgozni, amelyek alapján kifejlesztünk és betanítunk egy prediktív modellt, majd a modellt üzembe helyezzük webszolgáltatásként, hogy mások is használhassák a hitelkockázatok értékeléséhez.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

A hitelkockázat-értékelési megoldás létrehozásához az alábbi lépéseket fogjuk követni:  

1. [Machine Learning-munkaterület létrehozása](machine-learning-walkthrough-1-create-ml-workspace.md)
2. [Meglévő adatok feltöltése](machine-learning-walkthrough-2-upload-data.md)
3. [Új kísérlet létrehozása](machine-learning-walkthrough-3-create-new-experiment.md)
4. [A modellek betanítása és kiértékelése](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5. [A webszolgáltatás üzembe helyezése](machine-learning-walkthrough-5-publish-web-service.md)
6. [Hozzáférés a webszolgáltatáshoz](machine-learning-walkthrough-6-access-web-service.md)

Ez az útmutató a [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com/) kínálatában szereplő [Binary Classfication: Credit risk prediction](http://go.microsoft.com/fwlink/?LinkID=525270) (Bináris osztályozás: a hitelkockázat előrejelzése) nevű mintakísérlet egyszerűsített verzióján alapul.


> [!TIP]
> Ha szeretne egy letölthető és kinyomtatható diagramot a Machine Learning Studio funkcióiról, tekintse meg [Az Azure Machine Learning Studio funkcióit bemutató diagram](machine-learning-studio-overview-diagram.md) című cikket.
> 
> 



<!--HONumber=Jan17_HO1-->


