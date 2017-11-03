---
title: "Szöveg elemzési modellek létrehozása az Azure Machine Learning Studióban |} Microsoft Docs"
description: "Elemzési modelljei szöveg létrehozása az Azure Machine Learning Studio a szöveg előfeldolgozása, N-g vagy a szolgáltatáskivonatolás modulok használata"
services: machine-learning
documentationcenter: 
author: rastala
manager: jhubbard
editor: 
ms.assetid: 08cd6723-3ae6-4e99-a924-e650942e461b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: roastala
ms.openlocfilehash: 5f45a500c7cc28b6d6ad3fcac780791d43067ff3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-text-analytics-models-in-azure-machine-learning-studio"></a>Szövegelemzési modellek létrehozása az Azure Machine Learning Studio használatával
Azure Machine Learning segítségével hozza létre, és azok szöveg elemzési modelljeit. Ezek a modellek segítségével, például dokumentum besorolását vagy a céggel kapcsolatos véleményeket elemzés előforduló problémák megoldásához.

Az a szöveg elemzési kísérletet üzembe helyezése rendszerint:

1. Tiszta és a szöveg dataset előfeldolgozása
2. Bontsa ki a numerikus szolgáltatás vektorok Előfeldolgozott szövegből
3. Tanítási osztályozási vagy regressziós modell
4. Pontszám és a modell ellenőrzése
5. A modell rendszerbe állítása üzemi környezetben

Ebben az oktatóanyagban elsajátíthatja, ezeket a lépéseket, azt végezze el a céggel kapcsolatos véleményeket elemzési modellek Amazon könyv értékelést adatkészlet (lásd a tanulmány "adatainak, Bollywood, elterjedése-Box és Blenders: tartomány kiigazítása véleményeket besorolási" John Blitzer, be van jelölve Dredze és Fernando Pereira; Társítás számítási Linguistics (ACL), a 2007.) Ez az adatkészlet felülvizsgálati pontszámok (1-2 vagy 4-5) és a szabad formátumú szöveget áll. A cél az, hogy a felülvizsgálati pontszám előrejelzése: alacsony (1 - 2) vagy nagy (4-5).

Ebben az oktatóanyagban a Cortana Intelligence Gallery kísérletek található:

[Könyv értékelést előrejelzése](https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-1)

[Könyv értékelést - prediktív Kísérletté előrejelzése](https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>1. lépés: Törlése és a szöveg dataset előfeldolgozása
Az első lépések a kísérlet a felülvizsgálati pontszámok elosztjuk a kategorikus alacsony és magas gyűjtőkbe, állítson össze a két osztályú osztályozás problémákat. Használjuk [szerkesztése metaadatok](https://msdn.microsoft.com/library/azure/dn905986.aspx) és [Kategorikus Csoportértékek](https://msdn.microsoft.com/library/azure/dn906014.aspx) modulok.

![Címke létrehozása](./media/text-analytics-module-tutorial/create-label.png)

Ezt követően azt törlése a szöveg használatával [előfeldolgozása szöveg](https://msdn.microsoft.com/library/azure/mt762915.aspx) modul. A rendszer törölné az adatkészletben, a legfontosabb szolgáltatások kereséséhez, és javítja a végső modell a zaj csökkenti. Tudjuk eltávolítani szavak - gyakori szavakat, például "a" vagy "a" - és számok, különleges karaktereket, ismétlődő karaktereket, e-mail címek és URL-címeket. Azt is kisbetű, a szavakat lemmatize szöveg átalakítása, és észleli majd eldobni mondat határok "|||" szimbólum Előfeldolgozott szövegben.

![Szöveg előfeldolgozása](./media/text-analytics-module-tutorial/preprocess-text.png)

Mi történik, ha használni kívánt egyéni szavak listáját? Is át kell neki adni a nem kötelező bemeneti adatként. Is karakterláncrészletek helyett használhatja a egyéni C# szintaxis reguláris kifejezést, és része a beszédfelismerés szavak eltávolításához: parancsokat, műveletek és melléknevek.

A előfeldolgozása befejezése után, azt az adatok felosztása tanítási és készletek tesztelése.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>2. lépés: Numerikus szolgáltatás vektorok kinyerése Előfeldolgozott szöveg
Szöveges adatok modellt létrehozásához általában akkor szabad formátumú szöveg átalakítása numerikus szolgáltatás veszélyének. A jelen példában használjuk [N-Gramot szolgáltatások bontsa ki a szöveg](https://msdn.microsoft.com/library/azure/mt762916.aspx) ilyen formátumú szöveg átalakítására modult. Ez a modul szóköz elválasztott szavakat oszlopot vesz igénybe, és kiszámítja szóból álló dictionary, vagy az N-g, szereplő szavakkal a dataset. Ezt követően álló hány alkalommal minden szó, vagy N-gramot, rekordokban levő jelenik meg, és létrehozza a szolgáltatás vektorok azoktól, amelyeket száma. Ebben az oktatóanyagban hivatott N-gramot mérete, 2, a szolgáltatás vektorok közé tartozik a önálló szavak és két további szavak kombinációi.

![Bontsa ki az N-g](./media/text-analytics-module-tutorial/extract-ngrams.png)

TF érvénybe lépni * N-gramot súlyozást IDF (kifejezés gyakoriság inverz dokumentum gyakoriság) száma. Ezt a módszert ad súly szó gyakran jelennek meg egyetlen rekordot, de ritka között a teljes adatkészletet. Egyéb lehetőségek a következők bináris, TF, és súlyú diagramot.

Ilyen szöveg funkciók általában magas granularitása van. Például ha a corpus 100 000 egyedi szót, igényel a funkció kellene 100 000 dimenziók, vagy több N-g használata esetén. A kinyerni N-Gramot szolgáltatások modul lehetővé teszi az olyan beállítási lehetőségekkel dimenzióinak csökkentése érdekében. Ha szeretné, rövid vagy hosszú, vagy túl ritka vagy jelentős prediktív érték túl gyakori szavak kizárása. Az oktatóanyag azt jelennek meg az 5-nél kevesebb rekordok vagy a több mint 80 %-a rekordok N g zárhatja ki.

Szolgáltatás kiválasztása segítségével is, csak azokat a szolgáltatásokat, amelyek a legtöbb az előrejelzés TARGET korrelált kiválasztása. 1000 szolgáltatásainak használjuk a khi-négyzet szolgáltatás kiválasztása. A kijelölt szavak vagy N-g Szójegyzék a jobb oldali kimeneti kivonat N-g modul kattintva megtekintheti.

Egy másik módjáról kibontása N-Gramot funkciókat használ, mint a Szolgáltatáskivonatolás modul is használhatja. Vegye figyelembe azonban, hogy [Szolgáltatáskivonatolás](https://msdn.microsoft.com/library/azure/dn906018.aspx) nem rendelkezik beépített kijelölés funkciói vagy TF * IDF súlyú.

## <a name="step-3-train-classification-or-regression-model"></a>3. lépés: Besorolási vagy regressziós modell betanításához.
Most már a szöveg átalakítása numerikus szolgáltatás oszlopra. A dataset továbbra is tartalmaz az előző lépésben, karakterlánc típusú oszlopokra, ezért a használjuk Select Columns adatkészlet kizárja őket.

Majd használjuk [két osztályú logisztikai regresszió](https://msdn.microsoft.com/library/azure/dn905994.aspx) előre jelezni a cél: magas vagy alacsony felülvizsgálati pontszámot. Ezen a ponton a szöveg analytics probléma átalakítása rendszeres besorolás hiba. Az Azure Machine Learning elérhető eszközök segítségével javítani a modellen. Például kísérletezik azzal, hogy megtudja, hogyan pontos eredményeket adnak különböző osztályozó, vagy használja hyperparameter hangolása pontosságának javítása érdekében.

![Tanítási és pontszám](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>4. lépés: Pontszám és a modell ellenőrzése
Hogyan ellenőrzi azt a betanított modell? Azt pontozása azt a tesztelési adatkészletnél ellen, és értékelje ki a pontosság. A modell megtanulta, azonban az N-g és a képzési adatkészletből tanúsítványsúly szóhasználatának. Ezért használandó adott szóhasználatának és azokat a súlyok szolgáltatások kivételekor az adatok, szemben a szóhasználatának újra létrehozni. Ezért azt kibontása N-Gramot szolgáltatások modul hozzá lesz a kísérlet pontozási elágazási, csatlakoztassa a kimeneti szóhasználatának képzési ág és a szóhasználatának mód beállítása legyen írásvédett. Azt is tiltsa le az N-g alapján történő szűrés gyakoriság úgy, hogy legalább 1 példányt, és legfeljebb 100 %-ra, és kapcsolja ki a szolgáltatás kiválasztása.

A Tesztadatok a szöveges oszlop átalakítása numerikus szolgáltatás oszlopra, miután azt kizárása a karakterlánc típusú oszlopokra képzési fiókirodai például a korábbi szakaszaiban. Ezután használja Score Model-modul előrejelzéseket készítsen és modell kiértékelése modul pontosságának kiértékeléséhez.

## <a name="step-5-deploy-the-model-to-production"></a>5. lépés: A modell rendszerbe állítása éles
A modell már majdnem kész éles üzembe helyezni. Webszolgáltatás telepítésekor szabad formátumú karaktersorozat bemenetként vesz igénybe, és vissza előrejelzéshez "magas" vagy "alacsony". A megjegyzett N-gramot szóhasználatának átalakítás a szöveg szolgáltatásait, és azokat a funkciókat, ellenben képzett logisztikai regresszió modellt használ. 

A prediktív kísérletté beállításához először elmentjük dataset, az N-gramot szóhasználatának és a betanított logisztikai regresszió modell a tanítási ágat a kísérlet a. Ezt követően elmentjük a kísérletet, egy kísérlet ábrázoló prediktív kísérletté létrehozása a "Mentés másként" használatával. A megosztott adatok modul és a képzési ág nem eltávolítani a kísérletet. Nem majd csatlakozni a korábban mentett N-gramot szóhasználatának és modell kibontása N-Gramot szolgáltatásait és pontszám modell modulok, illetve. A modell kiértékelése modul is eltávolítása.

Azt a beszúrandó oszlopok kiválasztása az adathalmaz modul előfeldolgozása szöveg modul a címke oszlop eltávolítása előtt, és kikapcsolni pontszám modulban "Adatkészlet Append pontszám oszlop" lehetőséget. Ily módon a webszolgáltatás nem kér a címkét az előre jelezni próbál, és nem echo válasz szolgáltatásai a bemeneti biztosítja.

![Prediktív Kísérletté](./media/text-analytics-module-tutorial/predictive-text.png)

Most van egy kísérlet, amely egy webszolgáltatás közzétett és neve a kérés-válasz vagy kötegelt végrehajtási API-k használatával.

## <a name="next-steps"></a>Következő lépések
Tudnivalók a szöveg analytics modulok [az MSDN dokumentációjában tájékozódhat](https://msdn.microsoft.com/library/azure/dn905886.aspx).

