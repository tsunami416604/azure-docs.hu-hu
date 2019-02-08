---
title: Prediktív megoldás a hitelkockázat - Azure Machine Learning Studióban |} A Microsoft Docs
description: Részletes útmutató, amely azt ismerteti, hogyan hozható létre a hitelkockázat értékelésére szolgáló prediktív elemzési megoldás az Azure Machine Learning Studio eszközben.
keywords: hitelkockázat, prediktív elemzési megoldás,kockázatértékelés
services: machine-learning
documentationcenter: ''
author: garyericson
ms.custom: seodec18
ms.author: garye
ms.assetid: 43300854-a14e-4cd2-9bb1-c55c779e0e93
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/23/2017
ms.openlocfilehash: 67f893f0e201b6800e6c1a4bc1f656003f6daee5
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55879411"
---
# <a name="walkthrough-develop-predictive-solution-for-credit-risk-assessment-in-azure-machine-learning-studio"></a>Forgatókönyv: A hitelkockázat értékelése az Azure Machine Learning Studio prediktív megoldás fejlesztése

Ebben az útmutatóban a prediktív elemzési megoldások Machine Learning Studióban való fejlesztési folyamatát tekintjük át részleteiben. Egy egyszerű modellt fejlesztünk ki a Machine Learning Studióban, majd üzembe helyezzük azt Azure Machine Learning-webszolgáltatásként, ahol a modell új adatokkal végezhet előrejelzéseket. 

Az útmutatóban leírtak abból indulnak ki, hogy legalább egyszer használta már a Machine Learning Studiót, és hogy valamennyire tisztában van a gépi tanulás fogalmaival. Az útmutató azonban nem feltételezi, hogy a fent említett területeken szakértő lenne.

Ha még soha nem használta az **Azure Machine Learning Studiót**, érdemes lehet [az első adatelemzési kísérlet az Azure Machine Learning Studióban történő létrehozását](create-experiment.md) ismertető oktatóanyaggal kezdenie. Az oktatóanyag végigvezeti a Machine Learning Studio használatának megkezdésén. Bemutatja az alapokat, azt, hogy hogyan húzhat be modulokat a kísérletbe és kapcsolhatja össze azokat, és hogyan futtathatja a kísérletet és tekintheti meg az eredményeket.
 
Ha csak most ismerkedik a gépi tanulás területével általánosságban, akkor az alábbi videósorozat hasznos lehet az Ön számára. A videósorozat címe [Adatelemzés kezdőknek](data-science-for-beginners-the-5-questions-data-science-answers.md), és remek bevezetőt kínál hétköznapi nyelven és elterjedt fogalmak használatával a gépi tanulás témakörébe.


[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
 

## <a name="the-problem"></a>A probléma

Tegyük fel, hogy előrejelzést kell készíteni egy személy hitelkockázatáról az általa kitöltött hitelkérelemben megadott adatok alapján.  

A hitelkockázat-értékelés összetett probléma, de az útmutató kedvéért leegyszerűsítjük egy kicsit. Ezután ezt példaként használjuk annak bemutatására, hogyan hozható létre prediktív elemzési megoldás a Microsoft Azure Machine Learning segítségével. Ehhez az Azure Machine Learning Studiót és egy Machine Learning-webszolgáltatást használunk majd.  

## <a name="the-solution"></a>A megoldás

Ebben a részletes útmutatóban nyilvánosan elérhető hitelkockázati adatokkal fogunk dolgozni, amelyek alapján kifejlesztünk és betanítunk egy prediktív modellt. Ezután üzembe helyezzük a modellt webszolgáltatásként, hogy mások is használhassák hitelkockázat-értékeléshez.

A hitelkockázat-értékelési megoldás létrehozásához az alábbi lépéseket fogjuk követni:  

1. [Machine Learning-munkaterület létrehozása](walkthrough-1-create-ml-workspace.md)
2. [Meglévő adatok feltöltése](walkthrough-2-upload-data.md)
3. [Kísérlet létrehozása](walkthrough-3-create-new-experiment.md)
4. [A modellek betanítása és kiértékelése](walkthrough-4-train-and-evaluate-models.md)
5. [A webszolgáltatás üzembe helyezése](walkthrough-5-publish-web-service.md)
6. [Hozzáférés a webszolgáltatáshoz](walkthrough-6-access-web-service.md)

> [!TIP] 
> Az ebben az útmutatóban kifejlesztett kísérlet működő példányát megtalálja az [Azure AI-katalógusban](https://gallery.cortanaintelligence.com). Lépjen **[a hitelkockázat előrejelzésével kapcsolatos útmutatóra](https://gallery.cortanaintelligence.com/Experiment/Walkthrough-Credit-risk-prediction-1)**, és kattintson az **Open in Studio** (Megnyitás a Studióban) lehetőségre a kísérlet egy példányának a Machine Learning Studio-munkaterületre való letöltéséhez.
> 
> Ez a forgatókönyv mintakísérlet egyszerűsített verzióján alapul [bináris osztályozás: Hitelkockázat-becslés](https://go.microsoft.com/fwlink/?LinkID=525270)is elérhető, a [katalógus](http://gallery.cortanaintelligence.com/).
