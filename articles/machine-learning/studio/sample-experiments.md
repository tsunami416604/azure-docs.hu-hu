---
title: Kickstart kísérletek példákból
titleSuffix: ML Studio (classic) - Azure
description: Ismerje meg, hogyan használhatja a példa gépi tanulási kísérleteket új kísérletek létrehozásához az Azure AI Gallery és az Azure Machine Learning Studio (klasszikus) használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/05/2018
ms.openlocfilehash: 1a913e714565ffb99237357e331ef5cf0189c40f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204221"
---
# <a name="create-azure-machine-learning-studio-classic-experiments-from-working-examples-in-azure-ai-gallery"></a>Azure Machine Learning Studio (klasszikus) kísérletek létrehozása munkapéldákból az Azure AI-galériában

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Ebből a cikkből megtudhatja, hogy teljesen új Machine Learning-kísérletek létrehozása helyett hogyan kezdhet hozzá a munkához az [Azure AI-katalógus](https://gallery.azure.ai/) példakísérleteivel. A példák segítségével felépítheti saját Machine Learning-megoldását.

A katalógus ban a Microsoft Azure Machine Learning Studio (klasszikus) csapata által végzett példakísérleteket, valamint a Machine Learning-közösség által megosztott példákat tartalmaz. Továbbá kérdéseket tehet fel, illetve megjegyzéseket is fűzhet a kísérletekhez.

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
   * **Válasszon ki szűrőket a bal oldalon.** Például a PCA-alapú anomáliaészlelő algoritmusokat használó kísérletek böngészéséhez a **Categories** (Kategóriák) területen kattintson az **Experiment** (Kísérlet) elemre. Ezután az **Algorithms Used** (Használt algoritmusok) területen kattintson a **Show All** (Összes megjelenítése) elemre, és a párbeszédpanelen válassza a **PCA-Based Anomaly Detection** (PCA-alapú anomáliaészlelés) lehetőséget. Lehetséges, hogy le kell görgetnie, hogy ez láthatóvá váljon.<br></br>
     ![Szűrők kiválasztása](./media/sample-experiments/choose-an-algorithm.png)
   * **Használja a keresőmezőt.** Ha például a Microsoft által közzétett, kétosztályos támogató vektorgép-algoritmust használó, számjegyfelismeréssel kapcsolatos kísérleteket szeretne keresni, a keresőmezőbe írja be a „digit recognition” (számjegyfelismerés) kifejezést. Ezután jelölje ki a **Kísérlet**, **csak Microsoft-tartalom**és **a Kétosztályos támogatási vektorgép szűrőket:**<br></br>
     ![A keresőmező használata](./media/sample-experiments/search-for-experiments.png)
4. Kattintson a kísérletre, ha többet szeretne megtudni róla.
5. A kísérlet futtatásához és/vagy módosításához kattintson a **Megnyitás a Studióban** fülre a kísérlet oldalán. <br></br>

    ![Példakísérlet](./media/sample-experiments/example-experiment.png)

## <a name="create-a-new-experiment-using-an-example-as-a-template"></a>Új kísérlet létrehozása példa sablonként való használatával
Új kísérletet is létrehozhat a Machine Learning Studio (klasszikus) egy galéria példa sablonként használatával.

1. Jelentkezzen be a [Studióba](https://studio.azureml.net) a Microsoft-fiók hitelesítő adatait használva, és kattintson az **Új** fülre egy kísérlet létrehozásához.
2. Tallózzon a példák között, és kattintson rá az egyikre.

Egy új kísérlet jön létre a Machine Learning Studio (klasszikus) munkaterületen a példa kísérlet sablonként.

## <a name="next-steps"></a>További lépések
* [Adatok importálása különböző forrásokból](import-data.md)
* [Gyors üzembe helyezési oktatóanyag az R nyelvhez a Machine Learning eszközben](r-quickstart.md)
* [Machine Learning webszolgáltatás üzembe helyezése](deploy-a-machine-learning-web-service.md)
