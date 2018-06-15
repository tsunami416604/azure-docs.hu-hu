---
title: Kísérletek létrehozása Machine Learning-példákból – Azure | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan használhatja a Machine Learning-példakísérleteket új kísérletek létrehozására az Azure AI-katalógus és az Azure Machine Learning alkalmazásával.
keywords: machine learning példák, mintakísérlet, machine learning minta, AI példák
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 81e6c1d8-682c-4db3-bfd5-d7bfb1150ff3
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/05/2018
ms.openlocfilehash: 11cc4889690cb44cb621ed53f3e3614afcc57abf
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838007"
---
# <a name="create-machine-learning-experiments-from-working-examples-in-azure-ai-gallery"></a>Machine Learning-kísérletek létrehozása munkapéldákból az Azure AI-katalógusban

Ebből a cikkből megtudhatja, hogy teljesen új Machine Learning-kísérletek létrehozása helyett hogyan kezdhet hozzá a munkához az [Azure AI-katalógus](https://gallery.cortanaintelligence.com/) példakísérleteivel. A példák segítségével felépítheti saját Machine Learning-megoldását.

A katalógusban szereplő példakísérleteket a Microsoft Azure Machine Learning csapata készítette, illetve a Machine Learning közösség osztotta meg. Továbbá kérdéseket tehet fel, illetve megjegyzéseket is fűzhet a kísérletekhez.

A katalógus használatának megismeréséhez tekintse meg az [Adatelemzés kezdőknek](data-science-for-beginners-the-5-questions-data-science-answers.md) sorozat következő 3 perces videóját: [Copy other people's work to do data science](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) (Mások munkájának felhasználása adatelemzéshez).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="find-an-experiment-to-copy-in-azure-ai-gallery"></a>Másolni kívánt kísérlet keresése az Azure AI-katalógusban
Az elérhető kísérletek megtekintéséhez a [Gallery](https://gallery.cortanaintelligence.com/) megnyitása után kattintson az **Experiments** (Kísérletek) fülre az oldal tetején.

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

    > [!NOTE]
    > Amikor először nyit meg egy kísérletet a Machine Learning Studióban, ingyen kipróbálhatja, vagy Azure-előfizetést vásárolhat. [Információk a Machine Learning Studio ingyenes próbaverziójáról és fizetős szolgáltatásáról](https://azure.microsoft.com/pricing/details/machine-learning/)
    >
    >

## <a name="create-a-new-experiment-using-an-example-as-a-template"></a>Új kísérlet létrehozása példa sablonként való használatával
A katalógusban található egyik példát sablonként használva új kísérletet hozhat létre a Machine Learning Studióban.

1. Jelentkezzen be a [Studióba](https://studio.azureml.net) a Microsoft-fiók hitelesítő adatait használva, és kattintson az **Új** fülre egy kísérlet létrehozásához.
2. Tallózzon a példák között, és kattintson rá az egyikre.

A mintakísérletet példaként használva új kísérlet jön létre a Machine Learning munkaterületén.

## <a name="next-steps"></a>További lépések
* [Adatok importálása különböző forrásokból](import-data.md)
* [Gyors üzembe helyezési oktatóanyag az R nyelvhez a Machine Learning eszközben](r-quickstart.md)
* [Machine Learning webszolgáltatás üzembe helyezése](publish-a-machine-learning-web-service.md)
