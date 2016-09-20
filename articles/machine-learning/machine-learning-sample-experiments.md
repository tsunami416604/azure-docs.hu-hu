<properties
    pageTitle="Machine Learning-mintakísérletek másolása | Microsoft Azure"
    description="Ebből a cikkből megtudhatja, hogyan használhatja a Machine Learning-mintakísérleteket új kísérletek létrehozására a Cortana Intelligence Gallery és a Microsoft Azure Machine Learning alkalmazásával."
    services="machine-learning"
    documentationCenter=""
    authors="cjgronlund"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/17/2016"
    ms.author="cgronlun;chhavib;olgali"/>

# Új Machine Learning-kísérletek létrehozása mintakísérletek másolásával
Ebből a cikkből megtudhatja, hogy teljesen új Machine Learning-kísérletek létrehozása helyett hogyan kezdhet hozzá a munkához a [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com/) mintakísérleteivel. A minták segítségével felépítheti saját Machine Learning-megoldását.

A katalógusban szereplő mintakísérleteket a Microsoft Azure Machine Learning csapata készítette, illetve a Machine Learning közösség osztotta meg. Továbbá kérdéseket tehet fel, illetve megjegyzéseket is fűzhet a kísérletekhez.

A katalógus használatának megismeréséhez tekintse meg az [Adatelemzés kezdőknek](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) sorozat következő 3 perces videóját: [Copy other people's work to do data science](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) (Mások munkájának felhasználása adatelemzéshez).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Másolni kívánt kísérlet keresése a Cortana Intelligence Galleryben

Az elérhető kísérletek megtekintéséhez a [Gallery](http://gallery.cortanaintelligence.com/) megnyitása után kattintson az **Experiments** (Kísérletek) fülre az oldal tetején.

### A legújabb vagy a legnépszerűbb kísérletek megkeresése

Ezen az oldalon megtekintheti a **Recently added** (Újonnan hozzáadott) kísérleteket, illetve lefelé görgetve a **What's popular** (Népszerű) kísérleteket vagy a legújabb **Popular Microsoft experiments** (Népszerű Microsoft kísérletek) kísérletek között böngészhet.

### Adott követelményeknek megfelelő kísérletek keresése

Az összes kísérlet tallózásához:

1. Kattintson az **Összes tallózása** fülre az oldal tetején.
2. A **Pontosítás** fül alatt válassza ki a **Kísérlet** fület a Katalógusban található összes kísérlet megtekintéséhez.
3. A követelményeknek megfelelő kísérletek keresése különféle módokon történhet:
    * **Válasszon ki szűrőket a bal oldalon.** A PCA-alapú anomáliaészlelő algoritmusokat használó kísérletek böngészéséhez válassza az **Experiment** (Kísérlet) fület a **Categories** (Kategóriák) alatt és a **PCA-Based Anomaly Detection** (PCA alapú anomáliaészlelés) lehetőséget az **Algorithms Used** (Használt algoritmusok) alatt. (Ha nem látja a keresett algoritmust, kattintson a **Show all** (Összes megjelenítése) lehetőségre a lista alján.)<br></br>
      ![](./media/machine-learning-sample-experiments/refine-the-view.png)
    *  **Használja a keresőmezőt.** Ha például a Microsoft által közzétett, kétosztályos támogató vektorgép-algoritmust használó, számjegyfelismeréssel kapcsolatos kísérleteket szeretne keresni, a keresőmezőbe írja be a „digit recognition” (számjegyfelismerés) kifejezést. Ezt követően válassza ki az **Experiment** (Kísérlet), a **Microsoft content only** (Kizárólag Microsoft tartalom) és a **Two-Class Support Vector Machine** (Kétosztályos támogató vektorgép) szűrőt:
      ![](./media/machine-learning-sample-experiments/search-for-experiments.png) 
4. Kattintson a kísérletre, ha többet szeretne megtudni róla.
5. A kísérlet futtatásához és/vagy módosításához kattintson a **Megnyitás a Studióban** fülre a kísérlet oldalán.

    > [AZURE.NOTE] Egy kísérlet megnyitásához a Machine Learning Studióban be kell jelentkeznie a Microsoft-fiók hitelesítő adatait használva. Ha még nem rendelkezik Machine Learning munkaterülettel, egy ingyenes próba munkaterület jön létre. [Az Azure Machine Learning ingyenes próbaverziójának tartalma](https://azure.microsoft.com/pricing/details/machine-learning/)

    ![](./media/machine-learning-sample-experiments/example-experiment.png) 


## Sablon használata a Machine Learning Studióban

A Galleryben található egyik sablont mintaként használva új kísérletet hozhat létre a Machine Learning Studióban.

1. Jelentkezzen be a [Studióba](https://studio.azureml.net) a Microsoft-fiók hitelesítő adatait használva, és kattintson az **Új** fülre egy új kísérlet létrehozásához.
2. Tallózzon a sablonok között, és kattintson rá az egyikre.

A mintakísérletet példaként használva új kísérlet jön létre a munkaterületén.

## Következő lépések
- [Adatok előkészítése](machine-learning-data-science-import-data.md)
- [Próbálja meg az R nyelv használatát a kísérlet során](machine-learning-r-quickstart.md)
- [Tekintse át az R nyelven íródott mintakísérleteket](machine-learning-r-csharp-web-service-examples.md)
- [Webszolgáltatási API létrehozása](machine-learning-publish-a-machine-learning-web-service.md)
- [Használatra kész alkalmazások tallózása](https://datamarket.azure.com/browse?query=machine+learning)



<!--HONumber=sep16_HO1-->


