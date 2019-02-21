---
title: '2. oktatóanyag: Kredit kockázati modelleket taníthat be'
titleSuffix: Azure Machine Learning Studio
description: Hitelkockázat értékelésére szolgáló prediktív elemzési megoldás létrehozása az Azure Machine Learning Studio bemutató részletes oktatóanyag. Ez az oktatóanyag egy háromrészes oktatóanyag-sorozat második részére. Azt mutatja be, a modellek betanítása és kiértékelése.
keywords: hitelkockázat, prediktív elemzési megoldás,kockázatértékelés
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 45407e183c70fe67e6bd59e3fd86a50a31844c47
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56453479"
---
# <a name="tutorial-2-train-credit-risk-models---azure-machine-learning-studio"></a>2. oktatóanyag: Kockázati kredit - modellek Azure Machine Learning Studióban

Ebben az oktatóanyagban a folyamat egy prediktív elemzési megoldás fejlesztése tekintjük igénybe vehet. A Machine Learning Studio egy egyszerű modellt fejleszt.  A modell Azure Machine Learning-webszolgáltatásként majd rendszerbe.  Ez a modell üzembe helyezett végezhet előrejelzéseket új adatokkal. Ez az oktatóanyag **második része a háromrészes oktatóanyag-sorozat**.

Tegyük fel, hogy előrejelzést kell készíteni egy személy hitelkockázatáról az általa kitöltött hitelkérelemben megadott adatok alapján.  

A hitelkockázat értékelése összetett probléma, de ebben az oktatóanyagban leegyszerűsíti azt egy kicsit. Ez a példa használatával a Microsoft Azure Machine Learning Studio prediktív elemzési megoldások létrehozásának módját fogja használni. Az Azure Machine Learning Studio és a egy Machine Learning webszolgáltatás ehhez a megoldáshoz fogja használni.  

A háromrészes oktatóanyag megkezdése nyilvánosan elérhető hitelkockázati adatokkal.  Fejlesztés és betanítunk egy prediktív modellt.  Végül, a modell üzembe webszolgáltatásként.

A [az oktatóanyag részében](tutorial-part1-credit-risk.md), a Machine Learning Studio-munkaterület létrehozása, adatokat töltött fel, és létrehozott egy kísérletet.

Az oktatóanyag ezen részében meg:
 
> [!div class="checklist"]
> * Több modell betanítása
> * Pontszám a modellek és kiértékelése


A [az oktatóanyag harmadik része](tutorial-part3-credit-risk-deploy.md), a modellt webszolgáltatásként fogja központilag telepíteni.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="prerequisites"></a>Előfeltételek

Teljes [az oktatóanyag részében](tutorial-part1-credit-risk.md).

## <a name="train"></a>Több modell betanítása

A machine learning-modellek létrehozása az Azure Machine Learning Studio használatának előnyei egyike egyetlen kísérletben egyszerre próbálja ki a modell egynél több típusa és az eredmények összehasonlítása. Kísérletezés az ilyen típusú segítséget a legjobb megoldás a probléma.

A kísérletben ebben az oktatóanyagban fejlesztést, két különböző modelleket hozhat létre, és ezután hasonlítsa össze a pontozási eredményeinek dönthet arról, hogy az elkészült kísérletnek a következőképpen a használni kívánt algoritmus.  

Nincsenek dönthet a különböző modellek. Elérhető modellek megtekintéséhez bontsa ki a **Machine Learning** a modulpaletta csomópontja majd **modell inicializálása** és a csomópontok alatta. Ez a kísérlet célja, hogy ki kell választania a [Kétosztályos támogató Vektorgép] [ two-class-support-vector-machine] (SVM) és a [két osztályú súlyozott döntési fa] [ two-class-boosted-decision-tree] modulok.

> [!TIP]
> Annak eldöntése, mely Machine Learning algoritmus ajánlott megoldani kívánt probléma megfelelő segítséget szeretne kérni, lásd: [algoritmusok kiválasztása a Microsoft Azure Machine Learning Studio](algorithm-choice.md).
> 
> 

Mindkét fogja hozzáadni a [két osztályú súlyozott döntési fa] [ two-class-boosted-decision-tree] modul és [Kétosztályos támogató Vektorgép] [ two-class-support-vector-machine] modul a kísérletben .

### <a name="two-class-boosted-decision-tree"></a>Kétosztályos gyorsított döntési fa

Először állítsa be a gyorsított döntési fa modell.

1. Keresse meg a [két osztályú súlyozott döntési fa] [ two-class-boosted-decision-tree] modulban a modulpaletta, és húzza a vászonra.

1. Keresse meg a [tanítási modell] [ train-model] modult, húzza a vászonra, és csatlakoztassa a kimenete a [két osztályú súlyozott döntési fa] [ two-class-boosted-decision-tree] modul bal oldali bemeneti portjával a [tanítási modell] [ train-model] modul.
   
   A [két osztályú súlyozott döntési fa] [ two-class-boosted-decision-tree] modul inicializálja az általános modell és [Train Model] [ train-model] használ a betanítási adatok taníthat be a modell. 

1. Csatlakozás a bal oldalon, a bal oldali kimeneti [R-szkript végrehajtása] [ execute-r-script] modul jobb oldali bemeneti portjával a [tanítási modell] [ train-model] modul (Ez az oktatóanyag Ön [bal oldalán származó adatokon használt](#train) a felosztási adatok modul tanítási).
   
   > [!TIP]
   > nem kell a két bemenet és a egy kimeneteire a [R-szkript végrehajtása] [ execute-r-script] ehhez a kísérlethez, így hagyhatja őket nem csatolt modul. 
   > 
   > 

Ez a része a kísérletet most alábbihoz hasonlóan jelenik meg:  

![A modell tanítása](./media/tutorial-part2-credit-risk-train/experiment-with-train-model.png)

Most meg kell adnia a [tanítási modell] [ train-model] modult, amelyet a modell előre jelezni a hitelkockázat-érték.

1. Válassza ki a [tanítási modell] [ train-model] modul. Az a **tulajdonságok** ablaktáblán kattintson a **Oszlopválasztás indítása**.

1. Az a **csak egy oszlop kiválasztása** párbeszédpanelen írja be a "kockázati jóváírás" a keresőmező alatt a **elérhető oszlopok**, válassza ki a "Kredit kockázati" alatt, és kattintson a jobbra mutató nyíl gombra (**>**) "Hitelkockázat" áthelyezése **kijelölt oszlopok**. 

    ![A hitelkockázat kiszámításához a tanítási modell modulhoz oszlop kiválasztása](./media/tutorial-part2-credit-risk-train/train-model-select-column.png)

1. Kattintson a **OK** pipa jelre.

### <a name="two-class-support-vector-machine"></a>Kétosztályos tartóvektor-gép

Ezután a SVM modell beállítása.  

Első, némi magyarázatát SVM. Gyorsított döntési fák algoritmus jól működnek a funkciók bármilyen típusú. Azonban a SVM modul lineáris besorolás állít elő, mert a létrehozott modellnek a legjobb sikerült ha összes numerikus szolgáltatás ugyanolyan skála. Az azonos méretezési csoport összes numerikus szolgáltatás konvertálásához használja "Tanh" átalakítás (az a [normalizálása adatok] [ normalize-data] modul). A számok alakítja a [0,1] tartományon. A SVM modul konvertálja karakterlánc funkciók kategorikus szolgáltatásokat, majd bináris 0/1 szolgáltatásait, így nem kell manuálisan átalakítja a karakterlánc-funkciókat. Ezenkívül nem kívánja a hitelkockázat oszlop (oszlop 21) átalakítása – numerikus, de az értéke azt a modell tanítása előre, ezért meg kell felügyeli azt.  

Állítsa be a SVM modell, tegye a következőket:

1. Keresse meg a [Kétosztályos támogató Vektorgép] [ two-class-support-vector-machine] modulban a modulpaletta, és húzza a vászonra.

1. Kattintson a jobb gombbal a [tanítási modell] [ train-model] modult, válassza **másolási**, majd kattintson jobb gombbal a vásznon, majd válassza **beillesztési**. A másolata a [tanítási modell] [ train-model] modul rendelkezik a azonos kijelölés az eredetivel.

1. Csatlakozzon a kimenetét a [Kétosztályos támogató Vektorgép] [ two-class-support-vector-machine] modul bal oldali bemeneti portját a második [tanítási modell] [ train-model] modul.

1. Keresse meg a [normalizálása adatok] [ normalize-data] modul, és húzza a vászonra.

1. Csatlakozás a bal oldalon, a bal oldali kimeneti [R-szkript végrehajtása] [ execute-r-script] modul (figyelje meg, hogy a modul kimeneti portjára csatlakoztathatók más modul egynél több) Ez a modul bemenetével.

1. A bal oldali kimeneti portjára, csatlakozzon a [normalizálása adatok] [ normalize-data] modul jobb oldali bemeneti portját a második [tanítási modell] [ train-model] modul.

Ez a kísérlet részének kell kinéznie ehhez hasonló:  

![A második modell tanítása](./media/tutorial-part2-credit-risk-train/svm-model-added.png)

Mostantól konfigurálhatja az [normalizálása adatok] [ normalize-data] modul:

1. Kattintással jelölje ki a [normalizálása adatok] [ normalize-data] modul. Az a **tulajdonságok** ablaktáblán válassza **Tanh** számára a **átalakítási metódus** paraméter.

1. Kattintson a **Oszlopválasztás indítása**, válassza ki a "Nem szerepelnek oszlopok a" for **megkezdése a**, jelölje be **Belefoglalás** az első legördülő menüben válassza **oszloptípus** a második legördülő menüből, és válasszon **numerikus** a harmadik legördülő listában. Azt határozza meg, hogy minden a numerikus oszlopok (és csak numerikus) átalakításából származnak.

1. Ez a sor jobb oldalán kattintson a plusz jelre (+) – Ez létrehoz egy legördülő menük sorát. Válassza ki **kizárása** az első legördülő menüben válassza **oszlopnevek** a második legördülő listában, és adja meg a "Kockázati jóváírás" a szövegmezőben. Azt határozza meg, hogy a hitelkockázat oszlop figyelmen kívül lehet hagyni (kell ehhez, mivel ez az oszlop numerikus, és így lenne lesz átalakítva, ha nem zárja ki).

1. Kattintson a **OK** pipa jelre.  

    ![Adatok optimalizálása modul oszlopok kijelölése](./media/tutorial-part2-credit-risk-train/normalize-data-select-column.png)


A [normalizálása adatok] [ normalize-data] modul most már van beállítva a hitelkockázat oszlop kivételével az összes numerikus oszlopokhoz Tanh átalakítás végrehajtásához.  

## <a name="score-and-evaluate-the-models"></a>Pontszám a modellek és kiértékelése

a tesztelési adatok, amelyek volt elválasztva ki a [Split Data] [ split] modult a betanított modellek pontozása. Ezután összehasonlíthatja a eredményeinek megtekintéséhez a jobb eredmények jön létre, amely a két modell.  

### <a name="add-the-score-model-modules"></a>A pontszám modell modulok hozzáadása

1. Keresse meg a [Score Model] [ score-model] modul, és húzza a vászonra.

1. Csatlakozás a [tanítási modell] [ train-model] modul, amely csatlakozik a [két osztályú súlyozott döntési fa] [ two-class-boosted-decision-tree] modul bal oldali bemeneti portjával a [Score Model] [ score-model] modul.

1. Csatlakozás a jobb oldalon [R-szkript végrehajtása] [ execute-r-script] (a tesztelési adatok) modul jobb oldali bemeneti portjával a [Score Model] [ score-model] modul.

    ![Csatlakoztatott score Model-modul](./media/tutorial-part2-credit-risk-train/score-model-connected.png)

   
   A [Score Model] [ score-model] modul mostantól a kredit információkat a tesztelési adatokból venni, futtassa a modellel, és hasonlítsa össze az előrejelzés a modell állít elő, a tényleges kredit kockázati oszloppal a adatok ellenőrzése.

1. Másolja és illessze be a [Score Model] [ score-model] modul egy másolat létrehozásához.

1. Csatlakozás a kimenet a SVM modell (azt jelenti, a kimeneti portjára, a [tanítási modell] [ train-model] modul, amely csatlakozik a [Kétosztályos támogató Vektorgép] [ two-class-support-vector-machine] modul) a második bemeneti portjával [Score Model] [ score-model] modul.

1. SVM modell ugyanazt az átalakítást a tesztadatok, tegye a betanítási adatok ugyanúgy kell. Ezért másolja és illessze be a [normalizálása adatok] [ normalize-data] hozzon létre egy második példányt, és csatlakoztathatja azt a jogot a modul [R-szkript végrehajtása] [ execute-r-script] modul.

1. Csatlakoztassa a második a bal oldali kimeneti [normalizálása adatok] [ normalize-data] modul jobb oldali bemeneti portját a második [Score Model] [ score-model] modul.

    ![Mindkét csatlakoztatott Score Model modulok](./media/tutorial-part2-credit-risk-train/both-score-models-added.png)


### <a name="add-the-evaluate-model-module"></a>A modell kiértékelése modul hozzáadása

A két pontozási eredményeinek kiértékeléséhez, és hasonlítsa össze azokat, használjon egy [Evaluate Model] [ evaluate-model] modul.  

1. Keresse meg a [Evaluate Model] [ evaluate-model] modul, és húzza a vászonra.

1. A kimeneti portjára, csatlakozzon a [Score Model] [ score-model] modul bal oldali bemeneti portjával, a gyorsított döntési fa modell társított a [Evaluate Model] [ evaluate-model] modul.

1. Csatlakozzon a többi [Score Model] [ score-model] modul jobb oldali bemeneti porthoz.  

    ![Modell értékelése csatlakoztatott](./media/tutorial-part2-credit-risk-train/evaluate-model-added.png)


### <a name="run-the-experiment-and-check-the-results"></a>A kísérlet futtatásához és az eredmények ellenőrzése

A kísérlet futtatásához kattintson a **futtatása** gombot a vászon alatti. Ennek futtatása eltarthat néhány percig. Az egyes modulok tartalomfogyasztás mutató mutatja, hogy fut-e, és ezután egy zöld pipa jeleníti meg, miután befejezte a modult. Minden modul be van jelölve, amikor a kísérlet befejezését követően.

A kísérlet kell kinéznie ehhez hasonló:  

![Mindkét modellt kiértékelése](./media/tutorial-part2-credit-risk-train/final-experiment.png)


Az eredmények ellenőrzéséhez kattintson a kimeneti portjára, a [Evaluate Model] [ evaluate-model] modul, és válassza ki **Visualize**.  

A [Evaluate Model] [ evaluate-model] modul görbék és mérőszámok, amelyek lehetővé teszik, hogy a két pontozott modell eredményeinek összehasonlítására két eredményez. Megtekintheti az eredményeket, fogadó operátor jellemző (ROC) görbék, pontosság/visszaírási görbék, vagy át. További adatok jelennek meg a keveredési mátrix a terület alatt a görbe (AUC) és egyéb mérőszámok összesített értékeket tartalmaz. Módosítsa a küszöbértéket a csúszkát balra vagy jobbra, és tekintse meg, milyen hatással van a metrikák készletét.  

A diagram jobb, kattintson a **Pontozását adatkészlet** vagy **Pontozását összehasonlítására adatkészlet** jelölje ki a társított görbe és megjelenítheti a kapcsolódó metrikákat az alábbi. A bal oldali bemeneti portjával "Adatkészlet Pontozását" megfelel a görbék a jelmagyarázatban, a [Evaluate Model] [ evaluate-model] modul – ebben az esetben ez az a gyorsított döntési fa modell. "Adatkészlet összehasonlítására pontozását" felel meg a jobb oldali bemeneti portját – ebben az esetben az SVM modellt. Ha ezek a címkék egyikét kattint, a modell a görbe ki van jelölve, és a megfelelő metrikák jelennek meg, az alábbi ábrán látható módon.  

![A modellek ROC görbévé](./media/tutorial-part2-credit-risk-train/roc-curves.png)

Ezek az értékek megvizsgálásával eldöntheti, melyik modellben a legközelebbi a keresett eredményeket biztosít. Lépjen vissza, és a kísérlet iterálása a különböző modellek paraméter értékeinek módosításával. 

Az adatelemzési és az eredmények értelmezésében és a modellek teljesítményének hangolása art ebben az oktatóanyagban hatókörén kívül esik. További segítségre van szüksége akkor előfordulhat, hogy olvassa el a következő cikkeket:
- [Hogyan modellek teljesítményének kiértékelése az Azure Machine Learning Studióban](evaluate-model-performance.md)
- [Válassza ki az Azure Machine Learning Studióban az algoritmusok optimalizálása paraméterekkel](algorithm-parameters-optimize.md)
- [Az Azure Machine Learning Studióban modell eredményeinek értelmezése](interpret-model-results.md)

> [!TIP]
> Minden alkalommal futtassa a kísérletet, ismétlés egy rekordot a futtatási előzmények maradjanak. Ezeket az ismétlések megtekintheti, és bármelyiket, kattintva térjen **FUTTATÁSI előzmények megtekintése** a vászon alatti. Is **előzetes futtatása** a a **tulajdonságok** panelre való visszatéréshez a közvetlenül megelőző egy iteráció meg van nyitva.
> 
> Meghatározhat egy példányát is futtathatja a kísérletet ismétlések kattintva **SAVE AS** a vászon alatti. 
> Használja a kísérlet **összegzése** és **leírás** tárolja a kísérlet ismétléseinek a már kipróbált tulajdonságokat.
> 
> További információkért lásd: [kísérlet ismétléseinek kezelése az Azure Machine Learning Studio](manage-experiment-iterations.md).  
> 
> 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban elvégezte ezeket a lépéseket: 
 
> [!div class="checklist"]
> * Kísérlet létrehozása
> * Több modell betanítása
> * Pontszám a modellek és kiértékelése

Most már készen áll, ezeket az adatokat a modellek üzembe helyezése.

> [!div class="nextstepaction"]
> [3. oktatóanyag – Modellek üzembe helyezése](tutorial-part3-credit-risk-deploy.md)


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/