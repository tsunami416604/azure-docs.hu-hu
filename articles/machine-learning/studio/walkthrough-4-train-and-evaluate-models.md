---
title: '4. lépés: Betanítása és kiértékelése a prediktív elemzési modellek |} A Microsoft Docs'
description: 'A fejlesztés egy prediktív megoldás bemutatója 4. lépés: Train, pontszám, és az Azure Machine Learning Studio több modell kiértékelése.'
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.custom: (previous ms.author hshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: d905f6b3-9201-4117-b769-5f9ed5ee1cac
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: 069e2f4d2cb6ae1e9890610bbace5298c62eee75
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2018
ms.locfileid: "51821021"
---
# <a name="walkthrough-step-4-train-and-evaluate-the-predictive-analytic-models"></a>Az útmutató 4. lépése: A prediktív elemzési modellek betanítása és kiértékelése
Ez a témakör ismerteti a forgatókönyv negyedik lépésében [az Azure Machine Learning prediktív elemzési megoldás fejlesztése](walkthrough-develop-predictive-solution.md)

1. [Machine Learning-munkaterület létrehozása](walkthrough-1-create-ml-workspace.md)
2. [Meglévő adatok feltöltése](walkthrough-2-upload-data.md)
3. [Új kísérlet létrehozása](walkthrough-3-create-new-experiment.md)
4. **A modellek betanítása és kiértékelése**
5. [A webszolgáltatás üzembe helyezése](walkthrough-5-publish-web-service.md)
6. [Hozzáférés a webszolgáltatáshoz](walkthrough-6-access-web-service.md)

- - -
A machine learning-modellek létrehozása az Azure Machine Learning Studio használatának előnyei egyike egyetlen kísérletben egyszerre próbálja ki a modell egynél több típusa és az eredmények összehasonlítása. Kísérletezés az ilyen típusú segítséget a legjobb megoldás a probléma.

A kísérletben, ez az útmutató olyan fejlesztést hogy modellek két különböző típusú hozhat létre, és hasonlítsa össze a pontozási eredményeinek dönthet arról, hogy azt szeretné használni az elkészült kísérletnek a következőképpen algoritmus.  

Nincsenek azt is választhat a különböző modellek. Elérhető modellek megtekintéséhez bontsa ki a **Machine Learning** a modulpaletta csomópontja majd **modell inicializálása** és a csomópontok alatta. Ez a kísérlet célja, hogy ki kell választania a [Kétosztályos támogató Vektorgép] [ two-class-support-vector-machine] (SVM) és a [két osztályú súlyozott döntési fa] [ two-class-boosted-decision-tree] modulok.    

> [!TIP]
> Annak eldöntése, mely Machine Learning algoritmus ajánlott megoldani kívánt probléma megfelelő segítséget szeretne kérni, lásd: [algoritmusok kiválasztása a Microsoft Azure Machine Learning](algorithm-choice.md).
> 
> 

## <a name="train-the-models"></a>A modellek betanítása

Mindkét hozzáadjuk a [két osztályú súlyozott döntési fa] [ two-class-boosted-decision-tree] modul és [Kétosztályos támogató Vektorgép] [ two-class-support-vector-machine] modul a kísérletben.

### <a name="two-class-boosted-decision-tree"></a>Kétosztályos gyorsított döntési fa

Először állítsa be a gyorsított döntési fa modell.

1. Keresse meg a [két osztályú súlyozott döntési fa] [ two-class-boosted-decision-tree] modulban a modulpaletta, és húzza a vászonra.

2. Keresse meg a [tanítási modell] [ train-model] modult, húzza a vászonra, és csatlakoztassa a kimenete a [két osztályú súlyozott döntési fa] [ two-class-boosted-decision-tree] modul bal oldali bemeneti portjával a [tanítási modell] [ train-model] modul.
   
   A [két osztályú súlyozott döntési fa] [ two-class-boosted-decision-tree] modul inicializálja az általános modell és [Train Model] [ train-model] használ a betanítási adatok taníthat be a modell. 

3. Csatlakozás a bal oldalon, a bal oldali kimeneti [R-szkript végrehajtása] [ execute-r-script] modul jobb oldali bemeneti portjával a [tanítási modell] [ train-model] (döntöttünk a modul [3. lépés](walkthrough-3-create-new-experiment.md) jelen kalauz használatához a származó bal oldalán található a felosztási adatok modul tanítási adatokat).
   
   > [!TIP]
   > Két bemenet és a egy kimeneteire nem szükséges a [R-szkript végrehajtása] [ execute-r-script] ehhez a kísérlethez, így azt is hagyhatja őket nem csatolt modul. 
   > 
   > 

Ez a része a kísérletet most alábbihoz hasonlóan jelenik meg:  

![A modell tanítása][1]

Most tudnia a [tanítási modell] [ train-model] modul szeretnénk, hogy a modell előre jelezni a hitelkockázat-érték.

1. Válassza ki a [tanítási modell] [ train-model] modul. Az a **tulajdonságok** ablaktáblán kattintson a **Oszlopválasztás indítása**.

2. Az a **csak egy oszlop kiválasztása** párbeszédpanelen írja be a "kockázati jóváírás" a keresőmező alatt a **elérhető oszlopok**, válassza ki a "Kredit kockázati" alatt, és kattintson a jobbra mutató nyíl gombra (**>**) "Hitelkockázat" áthelyezése **kijelölt oszlopok**. 

    ![A hitelkockázat kiszámításához a tanítási modell modulhoz oszlop kiválasztása][0]

3. Kattintson a **OK** pipa jelre.

### <a name="two-class-support-vector-machine"></a>Kétosztályos tartóvektor-gép

A következő hogy állítsa be a SVM modell.  

Első, némi magyarázatát SVM. Gyorsított döntési fák algoritmus jól működnek a funkciók bármilyen típusú. Azonban a SVM modul lineáris besorolás állít elő, mert a létrehozott modellnek a legjobb sikerült ha összes numerikus szolgáltatás ugyanolyan skála. Az azonos méretezési csoport összes numerikus szolgáltatás konvertálásához "Tanh" átalakítás használjuk (az a [normalizálása adatok] [ normalize-data] modul). A számok alakítja a [0,1] tartományon. A SVM modul konvertálja karakterlánc funkciók kategorikus szolgáltatásokat, majd bináris 0/1 szolgáltatásait, így nem szükséges manuálisan átalakítja a karakterlánc-funkciókat. Emellett a hitelkockázat oszlop (oszlop 21) átalakítása nem szeretnénk – numerikus, de az értéke azt a modell tanítása előre, ezért ellenőriznünk kell a felügyeli azt.  

Állítsa be a SVM modell, tegye a következőket:

1. Keresse meg a [Kétosztályos támogató Vektorgép] [ two-class-support-vector-machine] modulban a modulpaletta, és húzza a vászonra.

2. Kattintson a jobb gombbal a [tanítási modell] [ train-model] modult, válassza **másolási**, majd kattintson jobb gombbal a vásznon, majd válassza **beillesztési**. A másolata a [tanítási modell] [ train-model] modul rendelkezik a azonos kijelölés az eredetivel.

3. Csatlakozzon a kimenetét a [Kétosztályos támogató Vektorgép] [ two-class-support-vector-machine] modul bal oldali bemeneti portját a második [tanítási modell] [ train-model] modul.

4. Keresse meg a [normalizálása adatok] [ normalize-data] modul, és húzza a vászonra.

5. Csatlakozás a bal oldalon, a bal oldali kimeneti [R-szkript végrehajtása] [ execute-r-script] modul (figyelje meg, hogy a modul kimeneti portjára csatlakoztathatók más modul egynél több) Ez a modul bemenetével.

6. A bal oldali kimeneti portjára, csatlakozzon a [normalizálása adatok] [ normalize-data] modul jobb oldali bemeneti portját a második [tanítási modell] [ train-model] modul.

Ez a kísérlet részének kell kinéznie ehhez hasonló:  

![A második modell tanítása][2]  

Mostantól konfigurálhatja az [normalizálása adatok] [ normalize-data] modul:

1. Kattintással jelölje ki a [normalizálása adatok] [ normalize-data] modul. Az a **tulajdonságok** ablaktáblán válassza **Tanh** számára a **átalakítási metódus** paraméter.

2. Kattintson a **Oszlopválasztás indítása**, válassza ki a "Nem szerepelnek oszlopok a" for **megkezdése a**, jelölje be **Belefoglalás** az első legördülő menüben válassza **oszloptípus** a második legördülő menüből, és válasszon **numerikus** a harmadik legördülő listában. Azt határozza meg, hogy minden a numerikus oszlopok (és csak numerikus) átalakításából származnak.

3. Ez a sor jobb oldalán kattintson a plusz jelre (+) – Ez létrehoz egy legördülő menük sorát. Válassza ki **kizárása** az első legördülő menüben válassza **oszlopnevek** a második legördülő listában, és adja meg a "Kockázati jóváírás" a szövegmezőben. Azt határozza meg, hogy a hitelkockázat oszlop figyelmen kívül lehet hagyni (szükséges ehhez, mivel ez az oszlop numerikus, és így lenne lesz átalakítva, ha nem Elzárkózunk).

4. Kattintson a **OK** pipa jelre.  

    ![Adatok optimalizálása modul oszlopok kijelölése][5]

A [normalizálása adatok] [ normalize-data] modul most már van beállítva a hitelkockázat oszlop kivételével az összes numerikus oszlopokhoz Tanh átalakítás végrehajtásához.  

## <a name="score-and-evaluate-the-models"></a>Pontszám a modellek és kiértékelése

A tesztelési volt elválasztva ki adatokat használunk a [Split Data] [ split] modult a betanított modellek pontozása. Ezután összehasonlíthatja azt a eredményeinek megtekintéséhez a jobb eredmények jön létre, amely a két modell.  

### <a name="add-the-score-model-modules"></a>A pontszám modell modulok hozzáadása

1. Keresse meg a [Score Model] [ score-model] modul, és húzza a vászonra.

2. Csatlakozás a [tanítási modell] [ train-model] modul, amely csatlakozik a [két osztályú súlyozott döntési fa] [ two-class-boosted-decision-tree] modul bal oldali bemeneti portjával a [Score Model] [ score-model] modul.

3. Csatlakozás a jobb oldalon [R-szkript végrehajtása] [ execute-r-script] (a tesztelési adatok) modul jobb oldali bemeneti portjával a [Score Model] [ score-model] modul.

    ![Csatlakoztatott score Model-modul][6]
   
   A [Score Model] [ score-model] modul mostantól a kredit információkat a tesztelési adatokból venni, futtassa a modellel, és hasonlítsa össze az előrejelzés a modell állít elő, a tényleges kredit kockázati oszloppal a adatok ellenőrzése.

4. Másolja és illessze be a [Score Model] [ score-model] modul egy másolat létrehozásához.

5. Csatlakozás a kimenet a SVM modell (azt jelenti, a kimeneti portjára, a [tanítási modell] [ train-model] modul, amely csatlakozik a [Kétosztályos támogató Vektorgép] [ two-class-support-vector-machine] modul) a második bemeneti portjával [Score Model] [ score-model] modul.

6. SVM modell rendelkezünk ugyanazt az átalakítást, a Tesztadatok tennie, mint a betanítási adatok. Ezért másolja és illessze be a [normalizálása adatok] [ normalize-data] hozzon létre egy második példányt, és csatlakoztathatja azt a jogot a modul [R-szkript végrehajtása] [ execute-r-script] modul.

7. Csatlakoztassa a második a bal oldali kimeneti [normalizálása adatok] [ normalize-data] modul jobb oldali bemeneti portját a második [Score Model] [ score-model] modul.

    ![Mindkét csatlakoztatott Score Model modulok][7]

### <a name="add-the-evaluate-model-module"></a>A modell kiértékelése modul hozzáadása

A két pontozási eredményeinek kiértékeléséhez, és hasonlítsa össze azokat, használjuk egy [Evaluate Model] [ evaluate-model] modul.  

1. Keresse meg a [Evaluate Model] [ evaluate-model] modul, és húzza a vászonra.

2. A kimeneti portjára, csatlakozzon a [Score Model] [ score-model] modul bal oldali bemeneti portjával, a gyorsított döntési fa modell társított a [Evaluate Model] [ evaluate-model] modul.

3. Csatlakozzon a többi [Score Model] [ score-model] modul jobb oldali bemeneti porthoz.  

    ![Modell értékelése csatlakoztatott][8]

### <a name="run-the-experiment-and-check-the-results"></a>A kísérlet futtatásához és az eredmények ellenőrzése

A kísérlet futtatásához kattintson a **futtatása** gombot a vászon alatti. Ennek futtatása eltarthat néhány percig. Az egyes modulok tartalomfogyasztás mutató mutatja, hogy fut-e, és ezután egy zöld pipa jeleníti meg, miután befejezte a modult. Minden modul be van jelölve, amikor a kísérlet befejezését követően.

A kísérlet kell kinéznie ehhez hasonló:  

![Mindkét modellt kiértékelése][3]

Az eredmények ellenőrzéséhez kattintson a kimeneti portjára, a [Evaluate Model] [ evaluate-model] modul, és válassza ki **Visualize**.  

A [Evaluate Model] [ evaluate-model] modul görbék és mérőszámok, amelyek lehetővé teszik, hogy a két pontozott modell eredményeinek összehasonlítására két eredményez. Megtekintheti az eredményeket, fogadó operátor jellemző (ROC) görbék, pontosság/visszaírási görbék, vagy át. További adatok jelennek meg a keveredési mátrix a terület alatt a görbe (AUC) és egyéb mérőszámok összesített értékeket tartalmaz. Módosítsa a küszöbértéket a csúszkát balra vagy jobbra, és tekintse meg, milyen hatással van a metrikák készletét.  

A diagram jobb, kattintson a **Pontozását adatkészlet** vagy **Pontozását összehasonlítására adatkészlet** jelölje ki a társított görbe és megjelenítheti a kapcsolódó metrikákat az alábbi. A bal oldali bemeneti portjával "Adatkészlet Pontozását" megfelel a görbék a jelmagyarázatban, a [Evaluate Model] [ evaluate-model] modul – ebben az esetben ez az a gyorsított döntési fa modell. "Adatkészlet összehasonlítására pontozását" felel meg a jobb oldali bemeneti portját – ebben az esetben az SVM modellt. Ha ezek a címkék egyikét kattint, a modell a görbe ki van jelölve, és a megfelelő metrikák jelennek meg, az alábbi ábrán látható módon.  

![A modellek ROC görbévé][4]

Ezek az értékek megvizsgálásával eldöntheti, melyik modellben a legközelebbi a keresett eredményeket biztosít. Lépjen vissza, és a kísérlet iterálása a különböző modellek paraméter értékeinek módosításával. 

Az adatelemzési és az eredmények értelmezésében és a modellek teljesítményének hangolása art Ez az útmutató hatókörén kívül esik. További segítségre van szüksége akkor előfordulhat, hogy olvassa el a következő cikkeket:
- [Az Azure Machine Learning modellek teljesítményének kiértékelése](evaluate-model-performance.md)
- [Az Azure Machine Learning termékben használt algoritmusok optimalizálására paraméterek kiválasztása](algorithm-parameters-optimize.md)
- [Az Azure Machine Learning modell eredményeinek értelmezése](interpret-model-results.md)

> [!TIP]
> Minden alkalommal futtassa a kísérletet, ismétlés egy rekordot a futtatási előzmények maradjanak. Ezeket az ismétlések megtekintheti, és bármelyiket, kattintva térjen **FUTTATÁSI előzmények megtekintése** a vászon alatti. Is **előzetes futtatása** a a **tulajdonságok** panelre való visszatéréshez a közvetlenül megelőző egy iteráció meg van nyitva.
> 
> Meghatározhat egy példányát is futtathatja a kísérletet ismétlések kattintva **SAVE AS** a vászon alatti. 
> Használja a kísérlet **összegzése** és **leírás** tárolja a kísérlet ismétléseinek a már kipróbált tulajdonságokat.
> 
> További részletekért lásd: [kísérlet ismétléseinek kezelése az Azure Machine Learning Studio](manage-experiment-iterations.md).  
> 
> 

- - -
**A következő: [a webszolgáltatás üzembe helyezése](walkthrough-5-publish-web-service.md)**

[0]: ./media/walkthrough-4-train-and-evaluate-models/train-model-select-column.png
[1]: ./media/walkthrough-4-train-and-evaluate-models/experiment-with-train-model.png
[2]: ./media/walkthrough-4-train-and-evaluate-models/svm-model-added.png
[3]: ./media/walkthrough-4-train-and-evaluate-models/final-experiment.png
[4]: ./media/walkthrough-4-train-and-evaluate-models/roc-curves.png
[5]: ./media/walkthrough-4-train-and-evaluate-models/normalize-data-select-column.png
[6]: ./media/walkthrough-4-train-and-evaluate-models/score-model-connected.png
[7]: ./media/walkthrough-4-train-and-evaluate-models/both-score-models-added.png
[8]: ./media/walkthrough-4-train-and-evaluate-models/evaluate-model-added.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
