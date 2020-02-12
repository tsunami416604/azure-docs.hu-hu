---
title: 'Példa: Kickstart-kísérletek'
titleSuffix: ML Studio (classic) - Azure
description: Megtudhatja, hogyan használhatók például a Machine learning-kísérletek új kísérletek létrehozására Azure AI Gallery és Azure Machine Learning Studio (klasszikus) használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/05/2018
ms.openlocfilehash: dfbca6514328897afe0c0eda5ce7d5a6d2ff92a7
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152839"
---
# <a name="create-azure-machine-learning-studio-classic-experiments-from-working-examples-in-azure-ai-gallery"></a>Hozzon létre Azure Machine Learning Studio (klasszikus) kísérleteket a munkaAzure AI Galleryban működő példákból

Ebből a cikkből megtudhatja, hogy teljesen új Machine Learning-kísérletek létrehozása helyett hogyan kezdhet hozzá a munkához az [Azure AI-katalógus](https://gallery.azure.ai/) példakísérleteivel. A példák segítségével felépítheti saját Machine Learning-megoldását.

A katalógus például a Microsoft Azure Machine Learning Studio (klasszikus) csapat által végzett kísérleteket, valamint a Machine Learning Közösség által megosztott példákat is tartalmaz. Továbbá kérdéseket tehet fel, illetve megjegyzéseket is fűzhet a kísérletekhez.

A katalógus használatának megismeréséhez tekintse meg az [Adatelemzés kezdőknek](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) sorozat következő 3 perces videóját: [Copy other people's work to do data science](data-science-for-beginners-the-5-questions-data-science-answers.md) (Mások munkájának felhasználása adatelemzéshez).



## <a name="find-an-experiment-to-copy-in-azure-ai-gallery"></a>Másolni kívánt kísérlet keresése az Azure AI-katalógusban
Az elérhető kísérletek megtekintéséhez a [Gallery](https://gallery.azure.ai/) megnyitása után kattintson az **Experiments** (Kísérletek) fülre az oldal tetején.

### <a name="find-the-newest-or-most-popular-experiments"></a>A legújabb vagy a legnépszerűbb kísérletek megkeresése
Ezen az oldalon megtekintheti a **Recently added** (Újonnan hozzáadott) kísérleteket, illetve lefelé görgetve a **What's popular** (Népszerű) kísérleteket vagy a legújabb **Popular Microsoft experiments** (Népszerű Microsoft kísérletek) kísérletek között böngészhet.

### <a name="look-for-an-experiment-that-meets-specific-requirements"></a>Adott követelményeknek megfelelő kísérletek keresése
Az összes kísérlet tallózásához:

1. Kattintson az **Összes tallózása** fülre az oldal tetején.
2. A bal oldalon, a **Categories** (Kategóriák) szakasz **Refine by** (Pontosítás) területén válassza a **Experiment** (Kísérlet) fület a Katalógusban található összes kísérlet megtekintéséhez.
3. A követelményeknek megfelelő kísérletek keresése különféle módokon történhet:
   * **Válasszon ki szűrőket a bal oldalon.** Például a PCA-alapú anomáliaészlelő algoritmusokat használó kísérletek böngészéséhez a **Categories** (Kategóriák) területen kattintson az **Experiment** (Kísérlet) elemre. Ezután az **Algorithms Used** (Használt algoritmusok) területen kattintson a **Show All** (Összes megjelenítése) elemre, és a párbeszédpanelen válassza a **PCA-Based Anomaly Detection** (PCA-alapú anomáliaészlelés) lehetőséget. Lehetséges, hogy le kell görgetnie, hogy ez láthatóvá váljon.<br></br>
     ![Szűrők kiválasztása](./media/sample-experiments/choose-an-algorithm.png)
   * **Használja a keresőmezőt.** Ha például a Microsoft által közzétett, kétosztályos támogató vektorgép-algoritmust használó, számjegyfelismeréssel kapcsolatos kísérleteket szeretne keresni, a keresőmezőbe írja be a „digit recognition” (számjegyfelismerés) kifejezést. Ezt követően válassza ki az **Experiment** (Kísérlet), a **Microsoft content only** (Kizárólag Microsoft tartalom) és a **Two-Class Support Vector Machine** (Kétosztályos támogató vektorgép) szűrőt:<br></br>
     ![A keresőmező használata](./media/sample-experiments/search-for-experiments.png)
4. Kattintson a kísérletre, ha többet szeretne megtudni róla.
5. A kísérlet futtatásához és/vagy módosításához kattintson a **Megnyitás a Studióban** fülre a kísérlet oldalán. <br></br>

    ![Példakísérlet](./media/sample-experiments/example-experiment.png)

## <a name="create-a-new-experiment-using-an-example-as-a-template"></a>Új kísérlet létrehozása példa sablonként való használatával
Létrehozhat egy új kísérletet Machine Learning Studio (klasszikus) is, például sablonként egy katalógus használatával.

1. Jelentkezzen be a [Studióba](https://studio.azureml.net) a Microsoft-fiók hitelesítő adatait használva, és kattintson az **Új** fülre egy kísérlet létrehozásához.
2. Tallózzon a példák között, és kattintson rá az egyikre.

Új kísérlet jön létre a Machine Learning Studio (klasszikus) munkaterületen, a példaként szolgáló kísérlettel sablonként.

## <a name="next-steps"></a>Következő lépések
* [Adatok importálása különböző forrásokból](import-data.md)
* [Gyors üzembe helyezési oktatóanyag az R nyelvhez a Machine Learning eszközben](r-quickstart.md)
* [Machine Learning webszolgáltatás üzembe helyezése](deploy-a-machine-learning-web-service.md)
