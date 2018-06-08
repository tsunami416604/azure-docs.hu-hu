---
title: Hogyan az Azure Machine Learning modell válik egy webszolgáltatás-bővítmény |} Microsoft Docs
description: A beállítás esetén milyen az Azure Machine Learning modell történik a fejlesztői kísérletezhet az operationalized webszolgáltatásnak áttekintése.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 25e0c025-f8b0-44ab-beaf-d0f2d485eb91
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 7b9aec2815f836b3b220de37fe6428c54d39c3e5
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835572"
---
# <a name="how-a-machine-learning-model-progresses-from-an-experiment-to-an-operationalized-web-service"></a>Hogyan gépi tanulási modell megfelelően egy kísérlet egy operationalized webszolgáltatás
Az Azure Machine Learning Studio biztosít egy interaktív vászonra, amely lehetővé teszi a fejlesztés, futtassa, tesztelése és többször egy ***kísérletezhet*** képviselő prediktív elemzési modellek. Nincsenek modullistából, amelyek számos:

* A bemeneti adatok kísérletbe
* Az adatok kezelését
* A gépi tanulási algoritmusok használata modell betanításához
* A modell pontozása
* Eredmények értékelése
* Kimeneti végső értékek

Ha elégedett a kísérletet, telepítheti azt egy ***klasszikus Azure Machine Learning webszolgáltatás*** vagy egy ***új Azure Machine Learning webszolgáltatás*** , hogy a felhasználók új adatok elküldi és hátsó eredményeket.

A cikkben amelyben tudatjuk a felhasználókkal beállítás esetén hogyan a gépi tanulási modell történik a fejlesztői kísérletezhet az operationalized webszolgáltatásnak áttekintését.

> [!NOTE]
> Más módon fejlesztésekor és telepítésekor a machine learning modellek is, de ez a cikk a Machine Learning Studio használatának összpontosít. További létrehozása a klasszikus prediktív webszolgáltatás r leírása, például a következő blogbejegyzésben talál [Build & telepítése prediktív Web Apps használatával Rstudióból és Azure ML](http://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx).
> 
> 

Amíg az Azure Machine Learning Studio célja segíteni fejlesztés és üzembe helyezését egy *prediktív elemzési modellek*, egy kísérlet, amely nem tartalmazza a prediktív elemzési modellek fejlesztése Studio segítségével meg lehet. Például egy kísérlet előfordulhat, hogy csak a bemeneti adatok, kezelhető és majd az eredményeket. Csakúgy, mint egy prediktív elemzési kísérletet telepítheti a nem prediktív kísérletté webszolgáltatásként, de egy egyszerűbb folyamat, mert a kísérlet nem betanítása vagy egy gépi tanulási modell pontozása. Bár ez nem az általános Studio ily módon, azt fogja foglalja bele a vitafórum, hogy egy teljes magyarázat Studio működését is felállításához.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Fejlesztéséhez és telepítéséhez egy prediktív webszolgáltatás-bővítmény
Az alábbiakban egy tipikus megoldás fejlesztése és a Machine Learning Studio használatával telepítse a következő szakaszokat:

![Üzembe helyezési folyamat](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*1. ábra – egy tipikus prediktív elemzési modellek szakaszainak*

### <a name="the-training-experiment"></a>A tanítási kísérletet
A ***tanítási kísérletet*** a webszolgáltatás a Machine Learning Studióban fejlesztés első fázisa. A tanítási kísérletet célja, hogy biztosítson egy helyen történő fejlesztéséhez, teszteléséhez, többször, és végül a machine learning-modell betanításához. Akkor is még betanítása több modellek egyidejűleg célszerű figyelni a legjobb megoldás, ám Miután befejezte a kísérletezés, választania betanítása egyetlen modell, és kiszűri a többi a kísérlet. Például egy prediktív elemzési kísérletet fejlődő, [a az Azure Machine Learning hitelkockázat értékelésére szolgáló prediktív elemzési megoldás fejlesztése](walkthrough-develop-predictive-solution.md).

### <a name="the-predictive-experiment"></a>A prediktív kísérletté
Miután a betanított modell a tanítási kísérletet a, kattintson a **webes szolgáltatások beállítása** válassza **prediktív webszolgáltatás** a Machine Learning Studióban átalakítani a tanítási kísérletet, és a folyamat kezdeményezéséhez egy ***prediktív kísérletté***. A prediktív kísérletté célja a betanított modell használatára azzal a céllal, végül Azure webszolgáltatásként váljon operationalized, új adatok pontozása céljából.

Az átalakításhoz az alábbi lépések segítségével történik meg:

* Alakítsa át egyetlen modulba képzési használt modulok készletét, és mentse a betanított modell
* Nem kapcsolódik pontozási idegen modul megszüntetéséhez
* Adja hozzá a végleges webszolgáltatás által használt bemeneti és kimeneti portok

Előfordulhat, hogy engedélyezni szeretné a prediktív kísérletté webszolgáltatásként üzembe helyezésére eléréséhez további módosításokat. Például ha azt szeretné, hogy a webszolgáltatás számára a kimeneti eredmények csak egy részét, hozzáadhat egy szűrési modult a kimeneti portra előtt.

Az átalakítási folyamat során nem távolítja el a tanítási kísérletet. Ha a folyamat befejeződik, a Studio van két lap található: egy a tanítási kísérletet, egy, a prediktív kísérleti fázisú funkciókat. Ezzel a módszerrel, módosíthatja a tanítási kísérletet építse újra a prediktív kísérletté és a webszolgáltatás telepítése előtt. Vagy mentheti a tanítási kísérletet egy másik sort helyezzék másolatát.

> [!NOTE]
> Amikor rákattint **prediktív webszolgáltatás** egy automatikus folyamat a tanítási kísérletet átalakítása egy prediktív kísérletté indítja el, és ez a legtöbb esetben is működik. Ha a tanítási kísérletet összetett (például, hogy együtt belépni képzési több elérési út), előfordulhat, hogy inkább manuálisan tegye az átalakításhoz. További információkért lásd: [a modell előkészítése az Azure Machine Learning Studióban telepítési](convert-training-experiment-to-scoring-experiment.md).
> 
> 

### <a name="the-web-service"></a>A webszolgáltatás
Ha készen, hogy a prediktív kísérletté készen áll, a szolgáltatás vagy egy klasszikus webszolgáltatás telepítése vagy egy új webszolgáltatás-alapú Azure Resource Manager. Üzembe helyezheti modelljét telepítésével, mint a egy *klasszikus Machine Learning webszolgáltatás*, kattintson a **webes szolgáltatás telepítése** válassza **webes szolgáltatás telepítése [klasszikus]**. Készítéséhez *új Machine Learning webszolgáltatás*, kattintson a **webes szolgáltatás telepítése** válassza **[Új] webes szolgáltatás telepítése**. Felhasználók mostantól adatokat küldeni a modell a webszolgáltatással REST API-t és kap vissza az eredményeket. További információ: [Az Azure Machine Learning webszolgáltatásainak használata](consume-web-services.md).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>A nem általános eset: nem prediktív webes szolgáltatás létrehozása
Ha kísérletbe nem végezzük egy prediktív elemzési modell, akkor nem kell létrehozni egy tanítási kísérletet, mind a pontozási kísérlet - csak egy kísérlet van, és telepítheti azt egy webszolgáltatás. A Machine Learning Studio észleli, hogy tartalmaz-e a kísérletet a prediktív modell által használt modulok elemzése.

Miután a kísérletről többször is, és elégedett azt:

1. Kattintson a **webes szolgáltatások beállítása** válassza **webszolgáltatás Átképezési** – a bemeneti és kimeneti csomópontokat ad hozzá automatikusan
2. Kattintson a **futtatása**
3. Kattintson a **webes szolgáltatás telepítése** válassza **webes szolgáltatás telepítése [klasszikus]** vagy **[Új] webes szolgáltatás telepítése** attól függően, hogy a környezet, amely számára telepíteni kívánja.

A webes szolgáltatás már telepítve van, és elérheti és hasonlóan egy prediktív webszolgáltatás-bővítmény kezeli azt.

## <a name="updating-your-web-service"></a>A webes szolgáltatás frissítése
Most, hogy a kísérletben webszolgáltatásként telepítése után, mi történik, ha szeretné a frissítést?

Ez attól függ, hogy frissíteni kell:

**A bemeneti vagy kimeneti módosítani szeretné, vagy módosítani, hogyan kezeli a webszolgáltatás a adatokat szeretné**

Ha nem módosítja a modellt, de csak változnak, hogyan kezeli a webkiszolgáló az adatokat, a prediktív kísérletté szerkesztése, és kattintson a **webes szolgáltatás telepítése** válassza **webes szolgáltatás telepítése [klasszikus]** vagy **[Új] webes szolgáltatás telepítése** újra. A webes szolgáltatás le van állítva, a frissített prediktív kísérletté van telepítve, és a webes szolgáltatás újraindításakor.

Példa: Tegyük fel, hogy a prediktív kísérletté a teljes sor a bemeneti adatok az előre jelzett eredményt adja vissza. Dönthet úgy, hogy szeretné-e a webszolgáltatás számára csak az eredményt. Így adhat hozzá egy **Projektoszlopok** modulban, a prediktív kísérletté, közvetlenül a kimeneti portra, az eredmény eltérő oszlopok kizárása előtt. Amikor rákattint **webes szolgáltatás telepítése** válassza ki **webes szolgáltatás telepítése [klasszikus]** vagy **[Új] webes szolgáltatás telepítése** újra, a webszolgáltatás frissül.

**A modell új adatokkal újratanítása kívánt**

Ha meg szeretné tartani a gépi tanulási a modell, de új adatokkal újratanítása azt szeretné, két lehetősége van:

1. **A modell újratanítása, miközben fut a webes szolgáltatás** -Ha a modell működik, a prediktív webes szolgáltatás futása közben, ehhez azáltal, hogy a tanítási kísérletet, hogy néhány módosításait egy ***átképezési kísérletezhet***, majd telepítheti azt egy  ***megőrzési webes* szolgáltatás**. Ennek módjáról, lásd: [Machine Learning-modellek szoftveres](retrain-models-programmatically.md).
2. **Lépjen vissza az eredeti tanítási kísérletet, és különböző betanítási adatok segítségével a modellezése** – a prediktív kísérletté csatolva van a webszolgáltatás, de ezzel a módszerrel nem közvetlenül kapcsolódik a tanítási kísérletet. Ha módosítja az eredeti tanítási kísérletet, és kattintson **webes szolgáltatások beállítása**, az létrehoz egy *új* prediktív kísérletezhet, amellyel telepítésekor létrehoz egy *új* webszolgáltatás. Csak az eredeti webszolgáltatás nem frissíti.
   
   Ha módosítania kell a tanítási kísérletet, nyissa meg, majd kattintson a **Mentés másként** másolatot. Ezzel az eredeti tanítási kísérletet, prediktív kísérletté érintetlenül és webes szolgáltatás. Létrehozhat egy új webszolgáltatás-bővítmény most a módosításokat. Miután a Ezután eldöntheti, hogy az előző webes szolgáltatás leállítása vagy működése érdekében együtt egy új webszolgáltatás telepítése után.

**Egy másik modell betanításához kívánt**

Ha szeretné módosítani az eredeti prediktív kísérletté, például a különböző gépi tanulási algoritmus kiválasztása próbált egy másik képzési metódus stb., majd kövesse a második eljárás a modell átképezési fent ismertetett: Nyissa meg a tanítási kísérletet, kattintson **Mentés másként** készítsen egy másolatot, és indítsa el az új elérési utat a modell fejlődő le , a prediktív kísérletté létrehozása, és a webszolgáltatás telepítése. Ezzel létrehoz egy új webhely, az eredetihez - szolgáltatás egymástól független eldöntheti, melyik, vagy mindkettőt, tovább futnak.

## <a name="next-steps"></a>További lépések
A folyamat fejlesztése és kísérlet a további részletekért lásd: a következő cikkeket:

* a kísérlet - átalakítás [a modell Azure Machine Learning Studio központi telepítésének előkészítése](convert-training-experiment-to-scoring-experiment.md)
* a webszolgáltatás - telepítése [az Azure Machine Learning webszolgáltatás telepítése](publish-a-machine-learning-web-service.md)
* a modell - átképezési [Machine Learning-modellek szoftveres](retrain-models-programmatically.md)

A teljes folyamat című részben talál példákat:

* [Machine learning oktatóanyag: az első kísérlet létrehozása az Azure Machine Learning Studióban](create-experiment.md)
* [Forgatókönyv: A hitelkockázat értékelésére az Azure Machine Learning a prediktív elemzési megoldás fejlesztése](walkthrough-develop-predictive-solution.md)

