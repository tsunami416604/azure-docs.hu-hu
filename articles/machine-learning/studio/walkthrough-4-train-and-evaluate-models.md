---
title: '4. lépés: Betanítása és kiértékelése a prediktív elemzési modellek |} Microsoft Docs'
description: A Develop egy prediktív megoldás útmutató 4. lépés:, tanítási pontozása, és értékelje az Azure Machine Learning Studióban több modellek.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: d905f6b3-9201-4117-b769-5f9ed5ee1cac
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: 3f722fa314b42501e0f6144f71b9d74c0a9127e9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="walkthrough-step-4-train-and-evaluate-the-predictive-analytic-models"></a>Az útmutató 4. lépése: A prediktív elemzési modellek betanítása és kiértékelése
Ez a témakör a negyedik lépése annak a forgatókönyv [az Azure Machine Learning a prediktív elemzési megoldás fejlesztése](walkthrough-develop-predictive-solution.md)

1. [Machine Learning-munkaterület létrehozása](walkthrough-1-create-ml-workspace.md)
2. [Meglévő adatok feltöltése](walkthrough-2-upload-data.md)
3. [Új kísérlet létrehozása](walkthrough-3-create-new-experiment.md)
4. **A modellek betanítása és kiértékelése**
5. [A webszolgáltatás üzembe helyezése](walkthrough-5-publish-web-service.md)
6. [Hozzáférés a webszolgáltatáshoz](walkthrough-6-access-web-service.md)

- - -
A machine learning modellek létrehozása az Azure Machine Learning Studio használatának előnyeit egyike egyetlen kísérlet egyszerre egynél több típus modell próbálja, és hasonlítsa össze az eredményt. Az ilyen típusú kísérletezhet segít a legjobb megoldás keresése a probléma.

A kísérletben, azt kidolgozása a forgatókönyv azt modellek két különböző típusú hozhat létre, és hasonlítsa össze a pontozási eredményeinek döntse el, hogy mely algoritmus azt az elkészült kísérletnek használni szeretne.  

Nincsenek azt kiválaszthatják a különböző modellek. Az elérhető modellek megtekintéséhez bontsa ki a **Machine Learning** csomópont, a modulpalettán, majd bontsa ki a **inicializálása modell** és az alatta csomópontok. Ehhez a kísérlethez alkalmazásában azt beállításakor válassza ki a [két osztályú támogatást vektoros gép] [ two-class-support-vector-machine] (SVM) és a [két osztályú súlyozott döntési fa] [ two-class-boosted-decision-tree] modulok.    

> [!TIP]
> Ha segítséget szeretne kérni dönt a legjobb gépi tanulási algoritmus megfelel az adott problémát próbál megoldani, tekintse meg a [algoritmusok kiválasztása a Microsoft Azure Machine Learning](algorithm-choice.md).
> 
> 

## <a name="train-the-models"></a>A modellek betanítása

Is fel kell venni a [két osztályú súlyozott döntési fa] [ two-class-boosted-decision-tree] modul és [két osztályú támogatást vektoros gép] [ two-class-support-vector-machine] modul a kísérletben.

### <a name="two-class-boosted-decision-tree"></a>Két osztályú súlyozott döntési fája

Először állítsa be a súlyozott döntési fa modell.

1. Keresés a [két osztályú súlyozott döntési fa] [ two-class-boosted-decision-tree] a modulpalettán modul és a vászonra húzva.

2. Keresés a [tanítási modell] [ train-model] modult a vászonra húzva, és csatlakoztassa a kimenetét a [két osztályú súlyozott döntési fa] [ two-class-boosted-decision-tree] modul bal bemeneti portját a [tanítási modell] [ train-model] modul.
   
   A [két osztályú súlyozott döntési fa] [ two-class-boosted-decision-tree] modul inicializálja az általános modell és [Train Model] [ train-model] betanítási adatok használja a modell betanításához. 

3. Csatlakoztassa a bal oldali a bal oldali kimeneti [R-parancsfájl végrehajtása] [ execute-r-script] modul jobb bemeneti portját a [tanítási modell] [ train-model] modul (a döntöttünk [3. lépés](walkthrough-3-create-new-experiment.md) a jelen útmutató a megosztott adatok modul, a képzési bal oldalán származó adatokhoz).
   
   > [!TIP]
   > Két a bemenetek és egy eredményei nem szükséges a [R-parancsfájl végrehajtása] [ execute-r-script] modul ehhez a kísérlethez, így azt is hagyhatja őket választani. 
   > 
   > 

Ez a kísérlet része most már a következőhöz hasonló:  

![A modell betanítása][1]

Most igazolnia kell, amely közli a [tanítási modell] [ train-model] , hogy szeretnénk előre jelezni a hitelkockázat érték modell modul.

1. Válassza ki a [tanítási modell] [ train-model] modul. Az a **tulajdonságok** ablaktáblán kattintson a **Oszlopválasztás**.

2. Az a **csak egy oszlop kiválasztása** párbeszédpanelen írja be a "credit kockázat" alatt a keresési mezőbe **elérhető oszlopok**, válassza ki a "Credit kockázat" alatt, és kattintson a jobbra mutató nyíl gombra (**>**) "Hitelkockázat" áthelyezése **kijelölt oszlopok**. 

    ![Válassza ki a hitelkockázat oszlopot a tanítási modell modulhoz][0]

3. Kattintson a **OK** pipára.

### <a name="two-class-support-vector-machine"></a>Kétosztályos tartóvektor-gép

A következő beállítjuk a SVM modell.  

Első, SVM egy kis leírását. Súlyozott döntési fák működnek jól bármilyen típusú szolgáltatásokat. Azonban a SVM modul lineáris besorolás állít elő, mert a modellnek létrehozott van a legjobb sikerült ha összes numerikus szolgáltatás ugyanolyan skála. Ugyanolyan skála összes numerikus szolgáltatás alakításához "Tanh" átalakítás használjuk (az a [normalizálása az adatok] [ normalize-data] modul). A [0,1] tartományba ez átalakítja a számokat. A SVM modul alakít karakterlánc szolgáltatások kategorikus funkciókat, majd a bináris 0 vagy 1-szolgáltatások, így nem szükséges manuális átalakítás karakterlánc szolgáltatásokat. Emellett a hitelkockázat oszlop (oszlop 21) átalakítás nem szeretnénk - numerikus, de képes előre jelezni, a modell betanítása azt van, ezért ellenőriznünk kell a felügyeli azt érték.  

Állítsa be a SVM modell, tegye a következőket:

1. Keresés a [két osztályú támogatást vektoros gép] [ two-class-support-vector-machine] a modulpalettán modul és a vászonra húzva.

2. Kattintson a jobb gombbal a [tanítási modell] [ train-model] modul, jelölje be **másolási**, és kattintson jobb gombbal a vászonra, és válasszon **Beillesztés**. A másolatát a [tanítási modell] [ train-model] a modulnak a azonos kijelölés az eredetivel.

3. Csatlakoztassa a kimenetét a [két osztályú támogatást vektoros gép] [ two-class-support-vector-machine] modul bal bemeneti portját a második [tanítási modell] [ train-model] modul.

4. Keresés a [normalizálása az adatok] [ normalize-data] modul és a vászonra húzva.

5. Csatlakozás a bal oldali a bal oldali kimeneti [R-parancsfájl végrehajtása] [ execute-r-script] ehhez a modul (figyelje meg, hogy a kimeneti portra, a modulok csatlakoztathatók egynél több modul) a bemeneti modul.

6. A bal oldali kimeneti portjára csatlakozzon a [normalizálása az adatok] [ normalize-data] modul jobb bemeneti portját a második [tanítási modell] [ train-model] modul.

Ez a kísérlet része most hasonlóan kell kinéznie ezt:  

![A második modell betanítása][2]  

Mostantól konfigurálhatja a [normalizálása az adatok] [ normalize-data] modul:

1. Kattintással jelölje ki a [normalizálása az adatok] [ normalize-data] modul. Az a **tulajdonságok** ablaktáblán válassza előbb **Tanh** a a **átalakítási metódus** paraméter.

2. Kattintson a **Oszlopválasztás**, "Oszlop" kiválasztása **Begin With**, jelölje be **Include** az első legördülő menüben válassza ki **oszloptípus** a második legördülő menüből, és válasszon **numerikus** harmadik a legördülő. Azt határozza meg, hogy az összes numerikus oszlopok (és csak numerikus) átalakításából származnak.

3. Kattintson a plusz jelre (+) a sor jobb – létrejön egy legördülő listák megnyílásának sorát. Válassza ki **kizárása** az első legördülő menüben válassza ki **oszlopnevek** meg a második legördülő listában, és írja be a "Credit kockázat" a szövegmezőben. Azt határozza meg, hogy a hitelkockázat oszlop figyelmen kívül hagyja (kell ennek, mivel ebben az oszlopban numerikus, és így lenne kell alakítani, ha azt nem zárja ki).

4. Kattintson a **OK** pipára.  

    ![Az adatok normalizálása modul egy oszlopot válasszon ki][5]

A [normalizálása az adatok] [ normalize-data] modul most állítja be az összes numerikus oszlopoknál kivételével a hitelkockázat oszlop Tanh átalakítás elvégzéséhez.  

## <a name="score-and-evaluate-the-models"></a>Pontszám és a modell kiértékelése

A tesztelési adatokat lett elválasztott kimenő használjuk a [Split Data] [ split] modul a betanított modell pontozása céljából. Ezután összehasonlíthatja, azt az eredmények megtekintéséhez a jobb eredmények jön létre, amely két modell.  

### <a name="add-the-score-model-modules"></a>A Score Model modulok hozzáadása

1. Keresés a [Score Model] [ score-model] modul és a vászonra húzva.

2. Csatlakozás a [tanítási modell] [ train-model] modul, amely kapcsolódik a [két osztályú súlyozott döntési fa] [ two-class-boosted-decision-tree] modul bal bemeneti portját a [Score Model] [ score-model] modul.

3. Csatlakozás a jobb oldali [R-parancsfájl végrehajtása] [ execute-r-script] modul (a tesztelési adatokat), a jobb oldali bemeneti portját a [Score Model] [ score-model] modul.

    ![Csatlakoztatott score Model-modul][6]
   
   A [Score Model] [ score-model] modul mostantól a jóváírás információkat a tesztelési adatokból venni, a modell használatával való futtatáshoz, és hasonlítsa össze az előrejelzés a modell állít elő, a vizsgálati adatok tényleges jóváírás kockázat oszloppal.

4. Másolja és illessze be a [Score Model] [ score-model] modul egy másolat létrehozásához.

5. Csatlakozzon a SVM modell kimeneti (Ez azt jelenti, hogy a kimeneti portjára a [tanítási modell] [ train-model] modul, amely kapcsolódik a [két osztályú támogatást vektoros gép] [ two-class-support-vector-machine] modul) a bemeneti porthoz második [Score Model] [ score-model] modul.

6. SVM modell tudunk a azonos átalakítással vizsgálati adatok ehhez hasonlóan a betanítási adatok számára. Ezért másolja és illessze be a [normalizálása az adatok] [ normalize-data] modul egy másolat létrehozásához, és csatlakoztassa a jobb oldali [R-parancsfájl végrehajtása] [ execute-r-script] modul.

7. Csatlakozás a bal oldali kimenetét a második [normalizálása az adatok] [ normalize-data] modul jobb bemeneti portját a második [Score Model] [ score-model] modul.

    ![Mindkét Score Model modulok csatlakoztatva][7]

### <a name="add-the-evaluate-model-module"></a>A modell kiértékelése modul hozzáadása

A két pontozási eredményeinek értékeli, és hasonlítsa össze azokat, használjuk egy [modell kiértékelése] [ evaluate-model] modul.  

1. Keresés a [modell kiértékelése] [ evaluate-model] modul és a vászonra húzva.

2. A kimeneti portjára csatlakozzon a [Score Model] [ score-model] modul bal oldali bemeneti portját a súlyozott döntési fa modellt társított a [modell kiértékelése] [ evaluate-model] modul.

3. Csatlakozás a másik [Score Model] [ score-model] jobb modul bemeneti port.  

    ![Modell kiértékelése modul csatlakoztatva][8]

### <a name="run-the-experiment-and-check-the-results"></a>A kísérlet futtatásához és az eredményeket

A kísérlet futtatásához kattintson a **futtatása** a vászon alatti gombra. Néhány percig is eltarthat. Minden modul forgó mutató jeleníti meg, hogy fut-e, és ezután egy zöld pipa jeleníti meg, amikor befejeződött a modul. Ha a modul be van jelölve, a kísérlet futása befejeződött.

A kísérlet most hasonlóan kell kinéznie ezt:  

![Mindkét modell kiértékelése][3]

Az eredmények ellenőrzéséhez kattintson a kimeneti portjára a [modell kiértékelése] [ evaluate-model] modul, és válassza ki **Visualize**.  

A [modell kiértékelése] [ evaluate-model] modul görbék és metrikákat, amelyek lehetővé teszik a pontozott két modell összehasonlítására két hoz létre. Az eredmények fogadó operátor jellemző (ROC) görbék, pontosság/visszaírási görbék, vagy növekedési tekintheti meg. Megjelenített további adatok zavart mátrix, a görbe (AUC), és más metrikákkal területre összesített értékeket tartalmaz. A küszöbérték megváltoztatása a csúszka jobbra vagy balra mozgatásával, és tekintse meg a hatása a metrikák készletét.  

Az a diagram jobb oldalán, kattintson a **dataset program pontozza a mennyiségeket** vagy **dataset összehasonlítani a program pontozza a mennyiségeket** az alábbi kapcsolódó metrikák megjelenítendő és a kapcsolódó görbe kiemeléséhez. A bal oldali bemeneti portját a "Program pontozza a mennyiségeket dataset" megfelel a görbék a jelmagyarázatban, a [modell kiértékelése] [ evaluate-model] modul – ebben az esetben ez az a súlyozott döntési fa modell. "Program pontozza a mennyiségeket összehasonlítandó dataset" felel meg a jobb oldali bemeneti portját - abban az esetben, ha a SVM modell. Ezek a címkék kiválasztásakor az adott modell görbe ki van jelölve, és a kapcsolódó metrikák jelennek meg, az alábbi ábrán látható módon.  

![A modellek ROC görbévé][4]

Ezeket az értékeket megvizsgálásával eldöntheti, melyik modellben megtalálhatja a keresett eredmények legközelebb. Lépjen vissza, és a különböző modellek paraméterértékek módosításával kísérletbe többször. 

A tudományos és az eredmények értelmezéséhez, és a modell teljesítményének hangolása argentin Ez az útmutató hatókörén kívül esik. Segítségre van szüksége akkor előfordulhat, hogy olvassa el a következő cikkeket:
- [Hogyan modell teljesítmény az Azure Machine Learning kiértékelése](evaluate-model-performance.md)
- [Válassza ki a paraméterek az Azure Machine Learning a algoritmusok optimalizálása](algorithm-parameters-optimize.md)
- [Az Azure Machine Learning modell eredmények értelmezését](interpret-model-results.md)

> [!TIP]
> Minden alkalommal futtassa a kísérletet, hogy iterációs rekordja van jelszóelőzményei között a futtatása. Ezek az ismétlés megtekintheti, és kattintva visszatérhet valamelyiket, **futtatása ELŐZMÉNYEINEK megtekintése** a vászon alatt. Is **előzetes futtatása** a a **tulajdonságok** ablaktábla a közvetlenül megelőző a egy iteráció visszatérés megnyitva.
> 
> Hogy ismétlések kísérletbe másolatát kattintva **SAVE AS** a vászon alatt. 
> Használja a kísérlet **összegzés** és **leírás** nyilvántarthatja a mi próbálta a kísérletismétlések a tulajdonságokat.
> 
> További részletekért lásd: [kísérletismétlések kezelése a Azure Machine Learning Studio](manage-experiment-iterations.md).  
> 
> 

- - -
**Következő: [a webszolgáltatás üzembe helyezése](walkthrough-5-publish-web-service.md)**

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
