---
title: "Machine Learning-mintakísérletek másolása | Microsoft Docs"
description: "Ebből a cikkből megtudhatja, hogyan használhatja a Machine Learning-mintakísérleteket új kísérletek létrehozására a Cortana Intelligence Gallery és a Microsoft Azure Machine Learning alkalmazásával."
services: machine-learning
documentationcenter: 
author: cjgronlund
manager: jhubbard
editor: cgronlun
ms.assetid: 81e6c1d8-682c-4db3-bfd5-d7bfb1150ff3
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/11/2016
ms.author: cgronlun;olgali
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 9c710e6f959afd8a4f4b965931ed4868d94c6d25


---
# <a name="copy-sample-experiments-to-create-new-machine-learning-experiments"></a>Új Machine Learning-kísérletek létrehozása mintakísérletek másolásával
Ebből a cikkből megtudhatja, hogy teljesen új Machine Learning-kísérletek létrehozása helyett hogyan kezdhet hozzá a munkához a [Cortana Intelligence Gallery](https://gallery.cortanaintelligence.com/) mintakísérleteivel. A minták segítségével felépítheti saját Machine Learning-megoldását.

A katalógusban szereplő mintakísérleteket a Microsoft Azure Machine Learning csapata készítette, illetve a Machine Learning közösség osztotta meg. Továbbá kérdéseket tehet fel, illetve megjegyzéseket is fűzhet a kísérletekhez.

A katalógus használatának megismeréséhez tekintse meg az [Adatelemzés kezdőknek](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) sorozat következő 3 perces videóját: [Copy other people's work to do data science](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) (Mások munkájának felhasználása adatelemzéshez).

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="find-an-experiment-to-copy-in-cortana-intelligence-gallery"></a>Másolni kívánt kísérlet keresése a Cortana Intelligence Galleryben
Az elérhető kísérletek megtekintéséhez a [Gallery](https://gallery.cortanaintelligence.com/) megnyitása után kattintson az **Experiments** (Kísérletek) fülre az oldal tetején.

### <a name="find-the-newest-or-most-popular-experiments"></a>A legújabb vagy a legnépszerűbb kísérletek megkeresése
Ezen az oldalon megtekintheti a **Recently added** (Újonnan hozzáadott) kísérleteket, illetve lefelé görgetve a **What's popular** (Népszerű) kísérleteket vagy a legújabb **Popular Microsoft experiments** (Népszerű Microsoft kísérletek) kísérletek között böngészhet.

### <a name="look-for-an-experiment-that-meets-specific-requirements"></a>Adott követelményeknek megfelelő kísérletek keresése
Az összes kísérlet tallózásához:

1. Kattintson az **Összes tallózása** fülre az oldal tetején.
2. A **Pontosítás** fül alatt válassza ki a **Kísérlet** fület a Katalógusban található összes kísérlet megtekintéséhez.
3. A követelményeknek megfelelő kísérletek keresése különféle módokon történhet:
   * **Válasszon ki szűrőket a bal oldalon.** A PCA-alapú anomáliaészlelő algoritmusokat használó kísérletek böngészéséhez válassza az **Experiment** (Kísérlet) fület a **Categories** (Kategóriák) alatt és a **PCA-Based Anomaly Detection** (PCA alapú anomáliaészlelés) lehetőséget az **Algorithms Used** (Használt algoritmusok) alatt. (Ha nem látja a keresett algoritmust, kattintson a **Show all** (Összes megjelenítése) lehetőségre a lista alján.)<br></br>
     ![Szűrők kiválasztása](./media/machine-learning-sample-experiments/refine-the-view.png)
   * **Használja a keresőmezőt.** Ha például a Microsoft által közzétett, kétosztályos támogató vektorgép-algoritmust használó, számjegyfelismeréssel kapcsolatos kísérleteket szeretne keresni, a keresőmezőbe írja be a „digit recognition” (számjegyfelismerés) kifejezést. Ezután válassza a következő szűrőket: **Kísérlet**, **Kizárólag Microsoft-tartalom** és **Kétosztályos támogató vektorgép**: ![Használja a keresőmezőt](./media/machine-learning-sample-experiments/search-for-experiments.png).
4. Kattintson a kísérletre, ha többet szeretne megtudni róla.
5. A kísérlet futtatásához és/vagy módosításához kattintson a **Megnyitás a Studióban** fülre a kísérlet oldalán.

   > [!NOTE]
   > Egy kísérlet megnyitásához a Machine Learning Studióban be kell jelentkeznie a Microsoft-fiók hitelesítő adatait használva. Ha még nem rendelkezik Machine Learning munkaterülettel, egy ingyenes próba munkaterület jön létre. [Az Azure Machine Learning ingyenes próbaverziójának tartalma](https://azure.microsoft.com/pricing/details/machine-learning/)
   >
   >

    ![Példakísérlet](./media/machine-learning-sample-experiments/example-experiment.png)

## <a name="use-a-template-in-machine-learning-studio"></a>Sablon használata a Machine Learning Studióban
A Galleryben található egyik sablont mintaként használva új kísérletet hozhat létre a Machine Learning Studióban.

1. Jelentkezzen be a [Studióba](https://studio.azureml.net) a Microsoft-fiók hitelesítő adatait használva, és kattintson az **Új** fülre egy új kísérlet létrehozásához.
2. Tallózzon a sablonok között, és kattintson rá az egyikre.

A mintakísérletet példaként használva új kísérlet jön létre a munkaterületén.

## <a name="next-steps"></a>Következő lépések
* [Adatok előkészítése](machine-learning-data-science-import-data.md)
* [Próbálja meg az R nyelv használatát a kísérlet során](machine-learning-r-quickstart.md)
* [Tekintse át az R nyelven íródott mintakísérleteket](machine-learning-r-csharp-web-service-examples.md)
* [Webszolgáltatási API létrehozása](machine-learning-publish-a-machine-learning-web-service.md)



<!--HONumber=Feb17_HO2-->


