---
title: '2. oktatóanyag: A hitelkockázati modellek vonatkészítése'
titleSuffix: ML Studio (classic) - Azure
description: Részletes oktatóanyag, amely bemutatja, hogyan hozhat létre prediktív elemzési megoldást a hitelkockázat-értékeléshez az Azure Machine Learning Studio (klasszikus) környezetében. Ez az oktatóanyag egy háromrészes oktatóanyag-sorozat második része. Bemutatja, hogyan kell betanítani és értékelni a modelleket.
keywords: hitelkockázat, prediktív elemzési megoldás,kockázatértékelés
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 8feca17f10bb891f0ca5577b2363f95901da4a46
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79217868"
---
# <a name="tutorial-2-train-credit-risk-models---azure-machine-learning-studio-classic"></a>2. oktatóanyag: Hitelkockázati modellek betanítása – Azure Machine Learning Studio (klasszikus)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Ebben az oktatóanyagban tekintse meg a prediktív elemzési megoldás kifejlesztésének folyamatát. Egy egyszerű modellt fejleszt a Machine Learning Studio (klasszikus) alkalmazásban.  Ezután üzembe helyezi a modellt egy Azure Machine Learning-webszolgáltatásként.  Ez az üzembe helyezett modell új adatok használatával előrejelzéseket készíthet. Ez az oktatóanyag **egy háromrészes oktatóanyag-sorozat második része.**

Tegyük fel, hogy előrejelzést kell készíteni egy személy hitelkockázatáról az általa kitöltött hitelkérelemben megadott adatok alapján.  

Hitelkockázat értékelése egy összetett probléma, de ez a bemutató egyszerűsíti azt egy kicsit. Ezt fogja használni példaként arra, hogyan hozhat létre prediktív elemzési megoldást a Microsoft Azure Machine Learning Studio (klasszikus) használatával. Ehhez a megoldáshoz az Azure Machine Learning Studio (klasszikus) és egy Machine Learning-webszolgáltatás használható.  

Ebben a három részes oktatóanyagban a nyilvánosan elérhető hitelkockázati adatokkal kezdheti.  Ezután kifejleszt és betanít egy prediktív modellt.  Végül a modellt webszolgáltatásként telepíti.

Az [oktatóanyag első részében](tutorial-part1-credit-risk.md)létrehozott egy Machine Learning Studio (klasszikus) munkaterületet, feltöltött adatokat, és létrehozott egy kísérletet.

Ebben a részben a bemutató, hogy:
 
> [!div class="checklist"]
> * Több modell betanítása
> * Pontszám és értékeli a modellek


Az [oktatóanyag harmadik részében](tutorial-part3-credit-risk-deploy.md)a modellwebszolgáltatásként fog üzembe helyezni.

## <a name="prerequisites"></a>Előfeltételek

Teljes [első része a bemutató](tutorial-part1-credit-risk.md).

## <a name="train-multiple-models"></a><a name="train"></a>Több modell betanítása

Az Azure Machine Learning Studio (klasszikus) gépi tanulási modellek létrehozásának egyik előnye, hogy egyszerre több modelltípust próbálhat ki egy kísérletben, és összehasonlíthatja az eredményeket. Ez a fajta kísérletezés segít megtalálni a legjobb megoldást a problémára.

Az oktatóanyagban fejlesztett kísérletben két különböző típusú modellt hozhat létre, majd összehasonlíthatja a pontozási eredményeket, hogy eldönthesse, melyik algoritmust szeretné használni a végső kísérletben.  

Vannak különböző modellek közül lehet választani. A rendelkezésre álló modellek megtekintéséhez bontsa ki a **Machine Learning** csomópontot a modulpalettán, majd bontsa ki a Modell és az alatta lévő csomópontok initialize.To see the models available, expand the Machine Learning node in the module palette, and then expand **Initialize Model** and the nodes under it. Ennek a kísérletnek az alkalmazásában válassza ki a [kétosztályos támogatási vektorgépet][two-class-support-vector-machine] (SVM) és a [kétosztályos súlyozott döntési fa][two-class-boosted-decision-tree] modulokat.

> [!TIP]
> Ha segítségre van szüksége annak eldöntéséhez, hogy melyik Machine Learning-algoritmus felel meg a legjobban a megoldandó problémának, olvassa [el a Microsoft Azure Machine Learning Studio (klasszikus) algoritmusainak kiválasztása](algorithm-choice.md)című témakört.
> 
> 

Ehhez a kísérlethez hozzáadja a [kétosztályos súlyozott döntési fa][two-class-boosted-decision-tree] modult és a [kétosztályos támogatási vektorgép][two-class-support-vector-machine] modult is.

### <a name="two-class-boosted-decision-tree"></a>Kétosztályos súlyozott döntési fa

Először állítsa be a kiemelt döntési fa modellt.

1. Keresse meg a [kétosztályos súlyozott döntési fa][two-class-boosted-decision-tree] modult a modulpalettán, és húzza a vászonra.

1. Keresse meg a [Train Model][train-model] modult, húzza a vászonra, majd csatlakoztassa a [kétosztályos súlyozott döntési fa][two-class-boosted-decision-tree] modul kimenetét a Train [Model][train-model] modul bal oldali bemeneti portjához.
   
   A [kétosztályos súlyozott döntési fa][two-class-boosted-decision-tree] modul inicializálja az általános modellt, és a [Betanítási modell][train-model] betanítási adatokat használ a modell betanításához. 

1. Csatlakoztassa a bal [Oldali R-parancsfájl végrehajtása][execute-r-script] modul bal oldali kimenetét a Train [Model][train-model] modul jobb bemeneti portjához (ebben az oktatóanyagban a Split Data modul [bal oldaláról érkező adatokat használta](#train) betanításhoz).
   
   > [!TIP]
   > ehhez a kísérlethez nincs szükség két bemenetre és az [R-parancsfájl végrehajtása][execute-r-script] modul egyik kimenetére, így azok nem kapcsolhatók össze. 
   > 
   > 

Ez a része a kísérlet most valahogy így néz ki:  

![Modell betanítása](./media/tutorial-part2-credit-risk-train/experiment-with-train-model.png)

Most meg kell mondania a [Train Model][train-model] modulnak, hogy azt szeretné, hogy a modell előre jelezze a hitelkockázati értéket.

1. Válassza ki a [Vonatmodell][train-model] modult. A **Tulajdonságok** ablaktáblán kattintson az **Indítás oszlopkijelölő gombra.**

1. Az **Egyetlen oszlop kiválasztása** párbeszédpanelen írja be a "hitelkockázat" szót a keresési mezőbe az Elérhető oszlopok**>** **csoportban,** válassza az alábbi "Hitelkockázat" lehetőséget, majd kattintson a jobb nyílra ( ) a "Hitelkockázat" elem nek a **Kijelölt oszlopokba**való áthelyezéséhez. 

    ![Válassza ki a Hitelkockázat oszlopot a Vonatmodell modulhoz](./media/tutorial-part2-credit-risk-train/train-model-select-column.png)

1. Kattintson az **OK** gombra.

### <a name="two-class-support-vector-machine"></a>Kétosztályos támogató vektorgép

Ezután állítsa be az SVM modellt.  

Először is, egy kis magyarázatot SVM. A kiemelt döntési fák bármilyen típusú jellemzőkkel jól működnek. Mivel azonban az SVM modul lineáris osztályozót hoz létre, az általa létrehozott modell rendelkezik a legjobb teszthibával, ha az összes numerikus jellemző azonos skálával rendelkezik. Az összes numerikus jellemző azonos skálára történő konvertálásához használjon "Tanh" transzformációt (az [Adatok normalizálása][normalize-data] modullal). Ez átalakítja a számokat a [0,1] tartományban. Az SVM modul a karakterlánc-funkciókat kategorikus funkciókká, majd bináris 0/1-es funkciókká alakítja át, így nem kell manuálisan átalakítania a karakterlánc-funkciókat. Emellett nem szeretné átalakítani a Hitelkockázat oszlopot (21. oszlop) – ez numerikus, de ez az az érték, amelyet a modell előrejelzésére képezünk be, ezért békén kell hagynia.  

Az SVM-modell beállításához tegye a következőket:

1. Keresse meg a [kétosztályos támogatásvevevevelgép][two-class-support-vector-machine] modult a modulpalettán, és húzza a vászonra.

1. Kattintson a jobb gombbal a [Modell betanítása][train-model] modulra, válassza a **Másolás parancsot,** majd kattintson a jobb gombbal a vászonra, és válassza a **Beillesztés parancsot.** A [Betanítási modell][train-model] modul példánya ugyanazt az oszlopkijelölést, mint az eredeti.

1. Csatlakoztassa a [kétosztályos támogatási vektorgép][two-class-support-vector-machine] modul kimenetét a második [Train Model][train-model] modul bal oldali bemeneti portjához.

1. Keresse meg az [Adatok normalizálása modult,][normalize-data] és húzza a vászonra.

1. Csatlakoztassa a bal Oldali [R-parancsfájl végrehajtása][execute-r-script] modul bal oldali kimenetét a modul bemenetéhez (figyelje meg, hogy a modul kimeneti portja több modulhoz is csatlakoztatható).

1. Csatlakoztassa a [Normalize Data][normalize-data] modul bal oldali kimeneti portját a második [Train Model][train-model] modul jobb bemeneti portjához.

A kísérletünknek ez a része most valahogy így fog kinézni:  

![A második modell betanítása](./media/tutorial-part2-credit-risk-train/svm-model-added.png)

Most konfigurálja az [Adatok normalizálása][normalize-data] modult:

1. Ide kattintva jelölje ki az [Adatok normalizálása modult.][normalize-data] A **Tulajdonságok** ablaktáblán válassza a **Tanh** elemet az **Átalakítási metódus** paraméterhez.

1. Kattintson **az Indítás oszlopkijelölő**gombra, válassza a "Nincs oszlop" lehetőséget a **Kezdés lehetőséget,** válassza a **Felvétel lehetőséget** az első legördülő menüben, válassza az **oszloptípust** a második legördülő menüben, és válassza a **Numeric** lehetőséget a harmadik legördülő menüben. Ez azt adja meg, hogy az összes numerikus oszlop (és csak numerikus) átalakulna.

1. Kattintson a sor jobb oldalán található pluszjelre (+) – ez legördülő sorozatot hoz létre. Válassza a **Kizárás elemet** az első legördülő menüben, jelölje ki az **oszlopneveket** a második legördülő menüben, és írja be a "Hitelkockázat" szót a szövegmezőbe. Ez azt adja meg, hogy a Hitelkockázat oszlopot figyelmen kívül kell hagyni (ezt meg kell tennie, mert ez az oszlop numerikus, és így átalakulna, ha nem zárja ki).

1. Kattintson az **OK** gombra.  

    ![Oszlopok kijelölése az Adatok normalizálása modulhoz](./media/tutorial-part2-credit-risk-train/normalize-data-select-column.png)


Az [adatok normalizálása][normalize-data] modul most úgy van beállítva, hogy a Hitelkockázat oszlop kivételével minden numerikus oszlopon tanh-átalakítást hajtson végre.  

## <a name="score-and-evaluate-the-models"></a>Pontszám és értékeli a modellek

a [Split Data][split] modul által elválasztott tesztelési adatokat használja a betanított modellek pontozásához. ezután összehasonlíthatja a két modell eredményeit, hogy lássa, melyik eredményezett jobb eredményeket.  

### <a name="add-the-score-model-modules"></a>A Score Model modulok hozzáadása

1. Keresse meg a [Score Model][score-model] modult, és húzza a vászonra.

1. Csatlakoztassa a [Train Model][train-model] modul, amely csatlakozik a [kétosztályos súlyozott döntési fa][two-class-boosted-decision-tree] modul a bal oldali bemeneti port a Score [Model][score-model] modul.

1. Csatlakoztassa a megfelelő [R-parancsfájl-végrehajtás][execute-r-script] modult (a tesztelési adatokat) a [Score Model][score-model] modul megfelelő bemeneti portjához.

    ![Score Modell modul csatlakoztatva](./media/tutorial-part2-credit-risk-train/score-model-connected.png)

   
   A [Score Model][score-model] modul most már leveheti a hiteladatokat a tesztelési adatokból, futtathatja azt a modellen keresztül, és összehasonlíthatja a modell által generált előrejelzéseket a tesztelési adatok tényleges hitelkockázati oszlopával.

1. Másolja és illessze be a [Score Model][score-model] modult egy második másolat létrehozásához.

1. Csatlakoztassa az SVM-modell kimenetét (azaz a [kétosztályos támogató vektorgép][two-class-support-vector-machine] modulhoz csatlakoztatott [Train Model][train-model] modul kimeneti portját) a második [score modell][score-model] modul bemeneti portjához.

1. Az SVM-modell esetében ugyanazt az átalakítást kell végeznie a tesztadatokon, mint a betanítási adatokon. Így másolja és illessze be a [Normalize Data][normalize-data] modult egy második példány létrehozásához, és csatlakoztassa a megfelelő [R-parancsfájl végrehajtása][execute-r-script] modulhoz.

1. Csatlakoztassa a második [Normalize Data][normalize-data] modul bal oldali kimenetét a második [Score Model][score-model] modul jobb bemeneti portjához.

    ![Mindkét score modell modul csatlakoztatva](./media/tutorial-part2-credit-risk-train/both-score-models-added.png)


### <a name="add-the-evaluate-model-module"></a>A Modell kiértékelése modul hozzáadása

A két pontozási eredmény kiértékeléséhez és összehasonlításához használja [a Modell kiértékelése][evaluate-model] modult.  

1. Keresse meg a [Modell kiértékelése][evaluate-model] modult, és húzza a vászonra.

1. Csatlakoztassa a kiemelt [döntési][score-model] fa modellhez társított Score Model modul kimeneti portját a [Modell kiértékelése][evaluate-model] modul bal oldali bemeneti portjához.

1. Csatlakoztassa a másik [score modell][score-model] modult a megfelelő bemeneti porthoz.  

    ![Csatlakoztatott modellmodul kiértékelése](./media/tutorial-part2-credit-risk-train/evaluate-model-added.png)


### <a name="run-the-experiment-and-check-the-results"></a>Futtassa a kísérletet, és ellenőrizze az eredményeket

A kísérlet futtatásához kattintson a vászon alatti **FUTTATÁS** gombra. Ennek futtatása eltarthat néhány percig. Minden modulon egy forgó jelző jelzi, hogy fut, majd egy zöld pipa jelzi, ha a modul elkészült. Ha az összes modul be van jelölve, a kísérlet futása befejeződött.

A kísérlet most valahogy így néz ki:  

![Mindkét modell értékelése](./media/tutorial-part2-credit-risk-train/final-experiment.png)


Az eredmények ellenőrzéséhez kattintson a [Modell kiértékelése][evaluate-model] modul kimeneti portjára, és válassza a **Visualize**lehetőséget.  

A [Modell kiértékelése][evaluate-model] modul ívek és metrikák pár, amely lehetővé teszi a két pontozott modell eredményeinek összehasonlítása. Az eredményeket a vevőoperátori jellemzők (ROC) görbéi, a Pontosság/visszahívás görbék vagy a Lift görbék nézetében tekintheti meg. A megjelenített további adatok közé tartozik a zűrzavaros mátrix, a görbe alatti terület (AUC) összesített értékei és egyéb mutatók. A küszöbértéket a csúszka balra vagy jobbra mozgatásával módosíthatja, és megtekintheti, hogy az milyen hatással van a mérőszámok készletére.  

A diagram jobb oldalán kattintson a **Pontozott adatkészlet** vagy **a Pontozott adatkészlet elemre** az összehasonlításhoz a társított görbe kiemeléséhez és az alábbi mutatók megjelenítéséhez. A görbék jelmagyarázatában a "Pontozott adatkészlet" a [Modell kiértékelése][evaluate-model] modul bal oldali bemeneti portjának felel meg – a mi esetünkben ez a kiemelt döntési fa modell. Az "összehasonlítandó pontozott adatkészlet" a megfelelő bemeneti portnak felel meg - a mi esetünkben az SVM-modellnek. Ha a címkék valamelyikére kattint, a modell görbéje kilesz emelve, és a megfelelő mutatók jelennek meg, ahogy az az alábbi ábrán látható.  

![ROC görbék modellekhez](./media/tutorial-part2-credit-risk-train/roc-curves.png)

Ezeknek az értékeknek a vizsgálatával eldöntheti, hogy melyik modell áll legközelebb a keresett eredmények megadásához. A különböző modellek paraméterértékeinek módosításával visszaléphet a kísérletre, és itethet. 

Az eredmények értelmezésének és a modell teljesítményének finomhangolásának tudománya és művészete kívül esik az oktatóanyag hatókörén. További segítségért olvassa el az alábbi cikkeket:
- [A modellteljesítmény kiértékelése az Azure Machine Learning Studio-ban (klasszikus)](evaluate-model-performance.md)
- [Paraméterek kiválasztása az algoritmusok optimalizálásához az Azure Machine Learning Studio-ban (klasszikus)](algorithm-parameters-optimize.md)
- [Modelleredmények értelmezése az Azure Machine Learning Studio-ban (klasszikus)](interpret-model-results.md)

> [!TIP]
> Minden alkalommal, amikor futtatja a kísérletet, az iteráció rekordja a Futtatási előzményekben marad. Ezeket az ismétléseket megtekintheti, és bármelyikhez visszatérhet, ha a vászon alatt a **FUTTATÁSI ELŐZMÉNYEK MEGTEKINTÉSE** gombra kattint. A **Tulajdonságok** ablaktáblán a **Korábbi futtatás** elemre kattintva is visszatérhet a megnyitottat közvetlenül megelőző ismétléshez.
> 
> A kísérlet bármely iterációjának másolatát a vászon alatti **MENTÉS MÁSKÉNT** gombra kattintva készítheti el. 
> A kísérlet **Összegzés** és **leírás** tulajdonságaival rögzítheti, hogy mit próbált meg a kísérlet ismétlései során.
> 
> További információ: [Kísérletezési ismétlések kezelése az Azure Machine Learning Studio -ban (klasszikus)](manage-experiment-iterations.md).  
> 
> 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az alábbi lépéseket hajtotta végre: 
 
> [!div class="checklist"]
> * Kísérlet létrehozása
> * Több modell betanítása
> * Pontszám és értékeli a modellek

Most már készen áll az adatok modellek üzembe helyezésére.

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