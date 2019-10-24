---
title: 'Visual Interface példa #7: könyv felülvizsgálatának besorolása'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan hozhat létre egy gépi tanulási modellt a Book Reviews különböző kategóriákba való besorolásával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 09/20/2019
ms.openlocfilehash: b61cce54699e86a9a1d2cb526f6dec370baaf26b
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72694661"
---
# <a name="sample-7---text-classification-predict-company-category"></a>7\. minta – szöveges besorolás: a vállalat kategóriájának előrejelzése 

Ez a minta azt mutatja be, hogyan használhatók a Text Analytics-modulok egy szöveges besorolási folyamat létrehozásához Azure Machine Learning vizuális felületen.

A szöveges besorolás célja, hogy egy vagy több előre definiált osztályba vagy kategóriába rendeljen egy szövegrészt. A szöveg lehet dokumentum, hírek, keresési lekérdezés, e-mail, tweet, támogatási jegyek, felhasználói visszajelzés, felhasználói termék felülvizsgálata stb. A szöveges besorolás alkalmazása magában foglalja az újságcikkek és a hírek tartalmának kategorizálása, a weblapok rendszerezése hierarchikus kategóriákba, a levélszemét e-mailek szűrése, a hangulat elemzése, a felhasználók szándékának előrejelzése a keresési lekérdezésektől, az Útválasztás támogatási jegyek és a vásárlói visszajelzések elemzése. 

Ez a folyamat egy **többosztályos logisztikai regressziós** osztályozást vezet be a vállalat kategóriájának előrejelzéséhez a wikipedia által a wikipedia SP 500 adatkészlet alapján.  

A betanítási gépi tanulási modellek szöveges adattal való alapvető lépései a következők:

1. Az adatok lekérése

1. A szöveges adatértékek előzetes feldolgozása

1. Szolgáltatások mérnöki

   A Text (szöveg) funkciót a numerikus funkcióba konvertálhatja a szolgáltatás kibontási modulja, például a szolgáltatások kivonatolása, az n-Gram funkció kinyerése a szöveges adatokból.

1. A modell tanítása

1. Pontszám adatkészlet

1. A modell értékelése

Itt látható a kísérlet utolsó, befejezett gráfja. Biztosítjuk az összes modul indoklását, így a hasonló döntéseket saját maga is megteheti.

[a kísérlet ![Graph](./media/how-to-ui-sample-text-classification/nlp-modules-overall.png)](./media/how-to-ui-sample-text-classification/nlp-modules-overall.png#lightbox)

## <a name="data"></a>Adatok

Ebben a kísérletben a **WIKIPEDIA SP 500** adatkészletet használjuk. Az adatkészlet a wikipedia-ből származik (https://www.wikipedia.org/) az egyes S & P 500 vállalat cikkei alapján. Azure Machine Learning Visual Interface-re való feltöltés előtt az adatkészlet a következőképpen lett feldolgozva:

- Szöveges tartalom kinyerése minden egyes vállalatnál
- Wiki formázásának eltávolítása
- Nem alfanumerikus karakterek eltávolítása
- Az összes szöveg konvertálása kisbetűsre
- Ismert vállalati kategóriák lettek hozzáadva

Egyes vállalatoknak nem találhatók cikkek, így a rekordok száma kevesebb, mint 500.

## <a name="pre-process-the-text-data"></a>A szöveges adatértékek előzetes feldolgozása

Az **előfeldolgozási szöveg** modul használatával elődolgozhatja a szöveges adatbevitelt, beleértve a mondatok észlelését, a tokenize mondatokat és így tovább. A [**szöveg előfeldolgozása**](../algorithm-module-reference/preprocess-text.md) című cikkben szereplő összes támogatott beállítás megtalálható. A TeX-adatok előfeldolgozása után az **adatok felosztása** modul használatával véletlenszerűen oszthatja meg a bemeneti adatokat, így a betanítási adatkészlet az eredeti adatok 50%-át, a tesztelési adatkészlet pedig az eredeti adatok 50%-át tartalmazza.

## <a name="feature-engineering"></a>Szolgáltatások mérnöki
Ebben a példában két módszert használunk a szolgáltatások fejlesztéséhez.

### <a name="feature-hashing"></a>Funkciókivonatolás
A szolgáltatás- [**kivonatolási**](../algorithm-module-reference/feature-hashing.md) modul használatával a cikkek egyszerű szövegét egész számmá konvertáljuk, és az egész értékeket a modell bemeneti funkcióinak használták. 

A **szolgáltatás-kivonatolási** modul változó hosszúságú szöveges dokumentumok konvertálására használható a Vowpal Wabbit-függvénytár által biztosított 32 bites murmurhash v3 kivonatolási módszer használatával. A szolgáltatás-kivonatolás használatának célja a dimenzióját csökkentése; Emellett a szolgáltatás-kivonatolás lehetővé teszi, hogy a szolgáltatás súlyozása gyorsabb legyen a besorolás időpontjában, mert a karakterlánc-összehasonlítás helyett kivonatoló érték összehasonlítást használ.

A mintául szolgáló kísérlet során a kivonatolási bitek számát állítsa 14-re, és állítsa be az n-g és a 2 közötti értéket. Ezekkel a beállításokkal a kivonatoló tábla 2 ^ 14 bejegyzést tud tárolni, amelyekben az egyes kivonatolási funkciók egy vagy több n-Gram-funkciót jelképeznek, és annak értéke az n-Gram előfordulási gyakoriságát jelöli a szöveges példányban. Számos probléma esetén az ilyen méretű kivonatoló táblázat több mint megfelelő, de bizonyos esetekben további lemezterületre lehet szükség az ütközések elkerülése érdekében. Értékelje ki a gépi tanulási megoldás teljesítményét különböző számú bitek használatával. 

### <a name="extract-n-gram-feature-from-text"></a>N-Gram funkció kinyerése szövegből

Az n-Gram egy adott szövegrész n kifejezésének folytonos sorozata. Az 1. méretű n-grammos unigram nevezik. a 2. méretű n-gramm egy bigram; a 3. méretű n-gramm trigram. A nagyobb méretű n-grammot esetenként az n érték, például a "négy gramm", az "öt gramm" és így tovább.

Az [**N-Gram funkciót a szöveges**](../algorithm-module-reference/extract-n-gram-features-from-text.md)modulból egy másik megoldásként használták a funkció mérnöki környezetében. Ez a modul először kibontja az n-gramm értéket az n-grammon kívül, azon dokumentumok számát, amelyekben minden n-Gram megjelenik a szövegben (DF). Ebben a példában a TF-IDF mérőszámot használjuk a funkciók értékének kiszámításához. Ezután átalakítja a strukturálatlan szöveges adatmennyiséget az azonos hosszúságú numerikus funkciós vektorokra, ahol mindegyik funkció egy n-Gram TF-IDF-értékét jelöli egy szöveges példányban.

A szöveges adatok numerikus szolgáltatásbeli vektorokra való konvertálása után az **Oszlop kiválasztása** modul használatával távolíthatja el a szöveges adatokat az adatkészletből. 

## <a name="train-the-model"></a>A modell tanítása

Az Ön által választott algoritmus gyakran a használati eset követelményeitől függ. Mivel a kísérlet célja a vállalat kategóriájának előrejelzése, a többosztályos osztályozó modell jó választás. Figyelembe véve, hogy a szolgáltatások száma nagy, és ezek a funkciók ritkák, **többosztályos logisztikai regressziós** modellt alkalmazunk ehhez a kísérlethez.

## <a name="test-evaluate-and-compare"></a>Tesztelés, kiértékelés és összehasonlítás

 Feldaraboljuk az adatkészletet, és különböző adatkészleteket használunk a modell betanításához és teszteléséhez, hogy a modell kiértékelése nagyobb legyen.

A modell betanítása után a **pontszám modellt** használjuk, és **kiértékeljük a modell** moduljait az előre jelzett eredmények létrehozásához és a modellek kiértékeléséhez. A **pontszám modell** modul használata előtt azonban a funkció mérnöki funkciója, ahogyan azt a képzés során elvégezte. 

A **szolgáltatás-kivonatolási** modul esetében könnyen elvégezhető a betanítási folyamaton alapuló szolgáltatási mérnök. Használja közvetlenül a **funkció-kivonatolási** modult a bemeneti szöveges adatok feldolgozásához.

Az **N-grammos funkció szöveges modulból való kinyeréséhez** az **eredményül szolgáló szókincs kimenetét** összekapcsoljuk a adatfolyam szereplő **bemeneti szókincstel** a pontozási adatfolyam, és a **szókincs mód** paraméterét ReadOnly értékre kell állítani.
[n-Gram pontszám ![Graph](./media/how-to-ui-sample-text-classification/n-gram.png)](./media/how-to-ui-sample-text-classification/n-gram.png)

A mérnöki lépés befejezése után a **score Model** segítségével előkészítheti a tesztelési adatkészlet előrejelzéseit a betanított modell használatával. Az eredmény ellenőrzéséhez válassza ki a **pontszám modell** kimeneti portját, majd válassza a **Megjelenítés**lehetőséget.

Ezután átadjuk a pontszámokat a **modell kiértékelése** modulhoz az értékelési mérőszámok létrehozásához. A **modell kiértékelése** két bemeneti porttal rendelkezik, így kiértékelheti és összehasonlíthatja a különböző módszerekkel generált pontozásos adatkészleteket. Ebben a példában összehasonlítjuk a szolgáltatás-kivonatolási módszer és az n-Gram módszer által generált eredmény teljesítményét.
Az eredmény ellenőrzéséhez válassza ki a **kiértékelési modell** kimeneti portját, majd válassza a **Megjelenítés**lehetőséget.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ismerje meg a vizuális felületen elérhető egyéb mintákat:

- [1. példa – regresszió: az autó árának előrejelzése](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [2. minta – regresszió: algoritmusok összehasonlítása az autó árának előrejelzéséhez](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [3. példa – besorolás: hitelkockázat előrejelzése](how-to-ui-sample-classification-predict-credit-risk-basic.md)
- [4. minta – besorolás: a hitelkockázat előrejelzése (a Cost szenzitív)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [5. példa – besorolás: forgalom előrejelzése](how-to-ui-sample-classification-predict-churn.md)
- [6. példa – besorolás: repülési késések előrejelzése](how-to-ui-sample-classification-predict-flight-delay.md)
