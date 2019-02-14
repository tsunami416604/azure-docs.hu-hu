---
title: A Machine Learning Studio-kísérletek létrehozása a példákból
titleSuffix: Azure Machine Learning Studio
description: Ismerje meg, hogyan használhatja a machine learning-példakísérleteket új kísérletek létrehozására az Azure AI-katalógusban, és az Azure Machine Learning Studióban.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/05/2018
ms.openlocfilehash: e62e5a1df2b5ad3099d2ef7e5dc33b0d11683988
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245932"
---
# <a name="create-azure-machine-learning-studio-experiments-from-working-examples-in-azure-ai-gallery"></a>Az Azure Machine Learning Studio-kísérletek létrehozása munkapéldákból az Azure AI-katalógusban

Ebből a cikkből megtudhatja, hogy teljesen új Machine Learning-kísérletek létrehozása helyett hogyan kezdhet hozzá a munkához az [Azure AI-katalógus](https://gallery.azure.ai/) példakísérleteivel. A példák segítségével felépítheti saját Machine Learning-megoldását.

A katalógusban szereplő példakísérleteket a Microsoft Azure Machine Learning csapata készítette, illetve a Machine Learning közösség osztotta meg. Továbbá kérdéseket tehet fel, illetve megjegyzéseket is fűzhet a kísérletekhez.

A katalógus használatának megismeréséhez tekintse meg az [Adatelemzés kezdőknek](data-science-for-beginners-the-5-questions-data-science-answers.md) sorozat következő 3 perces videóját: [Copy other people's work to do data science](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) (Mások munkájának felhasználása adatelemzéshez).



## <a name="find-an-experiment-to-copy-in-azure-ai-gallery"></a>Másolni kívánt kísérlet keresése az Azure AI-katalógusban
Az elérhető kísérletek megtekintéséhez a [Gallery](https://gallery.azure.ai/) megnyitása után kattintson az **Experiments** (Kísérletek) fülre az oldal tetején.

### <a name="find-the-newest-or-most-popular-experiments"></a>A legújabb vagy a legnépszerűbb kísérletek megkeresése
Ezen az oldalon megtekintheti a **Recently added** (Újonnan hozzáadott) kísérleteket, illetve lefelé görgetve a **What's popular** (Népszerű) kísérleteket vagy a legújabb **Popular Microsoft experiments** (Népszerű Microsoft kísérletek) kísérletek között böngészhet.

### <a name="look-for-an-experiment-that-meets-specific-requirements"></a>Adott követelményeknek megfelelő kísérletek keresése
Az összes kísérlet tallózásához:

1. Kattintson az **Összes tallózása** fülre az oldal tetején.
2. A bal oldalon, a **Categories** (Kategóriák) szakasz **Refine by** (Pontosítás) területén válassza a **Experiment** (Kísérlet) fület a Katalógusban található összes kísérlet megtekintéséhez.
3. A követelményeknek megfelelő kísérletek keresése különféle módokon történhet:
   * **Válasszon ki szűrőket a bal oldalon.** Ha például tallózással kísérleteket használó a PCA alapú anomáliaészlelő: A **kategóriák** kattintson **kísérlet**. Ezután az **Algorithms Used** (Használt algoritmusok) területen kattintson a **Show All** (Összes megjelenítése) elemre, és a párbeszédpanelen válassza a **PCA-Based Anomaly Detection** (PCA-alapú anomáliaészlelés) lehetőséget. Lehetséges, hogy le kell görgetnie, hogy ez láthatóvá váljon.<br></br>
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
